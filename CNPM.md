# Sequence Diagrams — Student Flows (HCMUT Tutor/Mentor)

Các sơ đồ giả định kiến trúc cơ bản:
- FE: Ứng dụng Web/Mobile
- BE: Backend hệ thống Tutor/Mentor
- HCMUT_SSO: Dịch vụ đăng nhập tập trung (xác thực/trao đổi token)
- HCMUT_DATACORE: Chia sẻ dữ liệu học vụ (đăng ký, lịch, thông tin lớp)
- HCMUT_LIBRARY: Tài nguyên học liệu của trường

Ghi chú:
- ->> là lời gọi sync; -->> là phản hồi.
- Có dùng alt/else/opt để mô tả luồng thay thế/ngoại lệ.
- Có thể lược bớt một số bước kỹ thuật (cache, pagination) trong bản MVP.

---

## UC: Truy cập tài liệu môn học

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh viên
  participant FE as FE (Web/Mobile)
  participant BE as BE (Tutor/Mentor)
  participant SSO as HCMUT_SSO
  participant DC as HCMUT_DATACORE
  participant LIB as HCMUT_LIBRARY

  SV->>FE: Chọn "Tài liệu môn học" tại môn X
  FE->>BE: GET /courses/{X}/materials (JWT)
  activate BE
  BE->>SSO: Xác thực/Introspect token
  SSO-->>BE: Token hợp lệ

  BE->>DC: GET /enrollments?studentId&courseId=X
  alt Có đăng ký môn X
    DC-->>BE: Enrollment OK
    BE->>LIB: GET /materials?courseId=X (đính kèm token SSO)
    alt LIBRARY hoạt động
      LIB-->>BE: Danh sách tài liệu (meta, quyền)
      BE-->>FE: 200 OK (danh sách)
      deactivate BE
      FE-->>SV: Hiển thị danh sách tài liệu

      SV->>FE: Mở tài liệu D
      FE->>BE: GET /materials/{D}/access
      activate BE
      BE->>LIB: POST /materials/{D}/access?studentId
      alt Trả về pre-signed URL/stream
        LIB-->>BE: 200 {signedUrl|stream, mime}
        BE-->>FE: 302 Redirect URL (hoặc stream)
        FE-->>SV: Hiển thị/tải tài liệu
      else Không đủ quyền
        LIB-->>BE: 403 Forbidden
        BE-->>FE: 403
        FE-->>SV: Thông báo "Không có quyền truy cập"
      else Không tìm thấy
        LIB-->>BE: 404 Not Found
        BE-->>FE: 404
        FE-->>SV: "Tài liệu không tồn tại"
      end
      deactivate BE
    else Lỗi LIBRARY
      LIB-->>BE: 5xx
      BE-->>FE: 502 Bad Gateway
      deactivate BE
      FE-->>SV: "Không thể tải tài liệu — vui lòng thử lại"
    end
  else Chưa đăng ký môn X
    DC-->>BE: Enrollment NOT FOUND
    BE-->>FE: 403 Forbidden
    deactivate BE
    FE-->>SV: "Bạn chưa đăng ký môn này"
  end
```

---

## UC: Xem danh sách môn học (đang học)

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh viên
  participant FE as FE (Web/Mobile)
  participant BE as BE (Tutor/Mentor)
  participant SSO as HCMUT_SSO
  participant DC as HCMUT_DATACORE

  SV->>FE: Nhấn "Môn của tôi"
  FE->>BE: GET /my-courses?term=current (JWT)
  activate BE
  BE->>SSO: Xác thực token
  SSO-->>BE: OK
  BE->>DC: GET /enrollments/{studentId}?term=current
  alt Truy vấn thành công
    DC-->>BE: Danh sách môn + lớp
    BE-->>FE: 200 OK (list + bộ lọc kỳ/nhóm)
    deactivate BE
    FE-->>SV: Hiển thị danh sách

    alt SV chọn một môn
      SV->>FE: Chọn môn X
      FE->>BE: GET /courses/{X}
      BE-->>FE: 200 (chi tiết môn, link nhanh)
      FE-->>SV: Mở trang môn học (có lối tắt tới tài liệu, bài tập…)
    else Không có môn trong kỳ hiện tại
      FE-->>SV: Trạng thái rỗng + gợi ý chọn kỳ khác
    end
  else Lỗi truy vấn DATACORE
    DC-->>BE: 5xx/timeout
    BE-->>FE: 503 "Không thể tải danh sách môn"
    deactivate BE
    FE-->>SV: Hiển thị lỗi, cho phép thử lại
  end
```

---

## UC: Xem thời khóa biểu

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh viên
  participant FE as FE (Web/Mobile)
  participant BE as BE (Tutor/Mentor)
  participant DC as HCMUT_DATACORE

  SV->>FE: Chọn "Thời khóa biểu"
  FE->>BE: GET /schedule?range=week&term=current
  activate BE
  BE->>DC: GET /timetable?studentId&range=week&term=current
  alt Thành công
    DC-->>BE: Danh sách sự kiện (môn, lớp, phòng, cơ sở, GV)
    BE-->>FE: 200 OK (events)
    deactivate BE
    FE-->>SV: Hiển thị lịch tuần/tháng + bộ lọc (cơ sở/phòng/môn)

    alt Không có sự kiện trong khoảng chọn
      FE-->>SV: Trạng thái rỗng
    end
  else Lỗi kết nối/không lấy được lịch
    DC-->>BE: 5xx/timeout
    BE-->>FE: 502/503 + thông báo lỗi
    deactivate BE
    FE-->>SV: Cho phép thử lại
  end
```

---

## UC: Đăng ký môn học kỳ sau

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh viên
  participant FE as FE (Web/Mobile)
  participant BE as BE (Tutor/Mentor)
  participant DC as Hệ thống đăng ký (DATACORE/Đào tạo)

  SV->>FE: Mở "Đăng ký môn kỳ sau"
  FE->>BE: GET /terms/next/classes?open=true
  activate BE
  BE->>DC: GET /terms/next/classes (danh mục + số chỗ còn)
  DC-->>BE: Danh sách môn/lớp mở
  BE-->>FE: 200 OK (list)
  deactivate BE
  FE-->>SV: Hiển thị + bộ lọc (khung giờ, cơ sở, giảng viên)

  SV->>FE: Tìm kiếm/Lọc
  FE->>BE: GET /classes?filters
  BE-->>FE: 200 Kết quả

  SV->>FE: Chọn lớp L để đăng ký
  FE->>BE: POST /registrations/validate {studentId, classId}
  activate BE
  BE->>DC: Validate {tiên quyết, song hành, xung đột TKB, chỗ trống}
  DC-->>BE: {valid, reasons?, seatStatus}

  alt Hợp lệ
    BE-->>FE: 200 OK (ready to confirm)
    deactivate BE
    FE-->>SV: Hộp thoại xác nhận
    SV->>FE: Xác nhận đăng ký
    FE->>BE: POST /registrations {classId}
    activate BE
    BE->>DC: Tạo đăng ký (atomic seat decrement)
    alt Còn chỗ
      DC-->>BE: 201 Created
      BE-->>FE: 201 Created
      deactivate BE
      FE-->>SV: "Đăng ký thành công" + cập nhật lịch dự kiến
    else Hết chỗ
      DC-->>BE: 409 Full
      BE-->>FE: 409 Full
      deactivate BE
      FE-->>SV: Thông báo "Hết chỗ" (không ghi nhận)
    end
  else Không hợp lệ
    BE-->>FE: 400 Bad Request (lý do: tiên quyết, xung đột…)
    deactivate BE
    FE-->>SV: Hiển thị lý do, không ghi nhận
  end

  opt AF7.1 — Thêm vào "Yêu thích"
    SV->>FE: Thêm lớp L vào danh sách yêu thích
    FE->>BE: POST /wishlist {classId}
    BE-->>FE: 200 OK
    FE-->>SV: "Đã lưu vào yêu thích"
  end

  opt AF7.2 — Xem "Đề xuất lịch" trước khi xác nhận
    SV->>FE: Nhấn "Đề xuất lịch"
    FE->>BE: GET /schedule/suggest?term=next
    BE-->>FE: Danh sách lịch gợi ý (không xung đột)
    FE-->>SV: Chọn kịch bản lịch để đăng ký
  end
```
