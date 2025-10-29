# Sequence Diagrams — Student Flows (Fixed for GitHub Mermaid)

Ghi chú:
- Không dùng `activate`/`deactivate` để tránh lỗi.
- Dùng `alt/else/opt` cho luồng thay thế/ngoại lệ.

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
  BE->>SSO: Introspect/Xác thực token
  SSO-->>BE: Token hợp lệ

  BE->>DC: GET /enrollments?studentId&courseId=X
  alt Có đăng ký môn X
    DC-->>BE: Enrollment OK
    BE->>LIB: GET /materials?courseId=X
    alt LIBRARY hoạt động
      LIB-->>BE: Danh sách tài liệu
      BE-->>FE: 200 OK (danh sách)
      FE-->>SV: Hiển thị danh sách tài liệu

      SV->>FE: Mở tài liệu D
      FE->>BE: GET /materials/{D}/access
      BE->>LIB: POST /materials/{D}/access?studentId
      alt Trả về URL/stream
        LIB-->>BE: 200 {signedUrl|stream}
        BE-->>FE: 302 Redirect URL (hoặc stream)
        FE-->>SV: Hiển thị/tải tài liệu
      else Không đủ quyền
        LIB-->>BE: 403 Forbidden
        BE-->>FE: 403
        FE-->>SV: "Không có quyền truy cập"
      else Không tìm thấy
        LIB-->>BE: 404 Not Found
        BE-->>FE: 404
        FE-->>SV: "Tài liệu không tồn tại"
      end
    else Lỗi LIBRARY
      LIB-->>BE: 5xx
      BE-->>FE: 502 Bad Gateway
      FE-->>SV: "Không thể tải tài liệu — vui lòng thử lại"
    end
  else Chưa đăng ký môn X
    DC-->>BE: Enrollment NOT FOUND
    BE-->>FE: 403 Forbidden
    FE-->>SV: "Bạn chưa đăng ký môn này"
  end
```

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
  BE->>SSO: Xác thực token
  SSO-->>BE: OK
  BE->>DC: GET /enrollments/{studentId}?term=current

  alt Truy vấn thành công
    DC-->>BE: Danh sách môn + lớp
    BE-->>FE: 200 OK (list + bộ lọc)
    FE-->>SV: Hiển thị danh sách
    alt SV chọn một môn
      SV->>FE: Chọn môn X
      FE->>BE: GET /courses/{X}
      BE-->>FE: 200 (chi tiết môn)
      FE-->>SV: Mở trang môn học
    else Không có môn trong kỳ hiện tại
      FE-->>SV: Trạng thái rỗng + gợi ý chọn kỳ khác
    end
  else Lỗi DATACORE
    DC-->>BE: 5xx/timeout
    BE-->>FE: 503 "Không thể tải danh sách môn"
    FE-->>SV: Hiển thị lỗi, cho phép thử lại
  end
```

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
  BE->>DC: GET /timetable?studentId&range=week&term=current

  alt Thành công
    DC-->>BE: Danh sách sự kiện (môn, lớp, phòng, cơ sở, GV)
    BE-->>FE: 200 OK (events)
    FE-->>SV: Hiển thị lịch tuần/tháng + bộ lọc
    alt Không có sự kiện trong khoảng chọn
      FE-->>SV: Trạng thái rỗng
    end
  else Lỗi kết nối/không lấy được lịch
    DC-->>BE: 5xx/timeout
    BE-->>FE: 502/503 + thông báo lỗi
    FE-->>SV: Cho phép thử lại
  end
```

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
  BE->>DC: GET /terms/next/classes
  DC-->>BE: Danh sách môn/lớp mở
  BE-->>FE: 200 OK (list)
  FE-->>SV: Hiển thị + bộ lọc (khung giờ, cơ sở, giảng viên)

  SV->>FE: Tìm kiếm/Lọc
  FE->>BE: GET /classes?filters
  BE-->>FE: 200 Kết quả

  SV->>FE: Chọn lớp L để đăng ký
  FE->>BE: POST /registrations/validate {studentId, classId}
  BE->>DC: Validate {tiên quyết, song hành, xung đột, chỗ trống}
  DC-->>BE: {valid, reasons?, seatStatus}

  alt Hợp lệ
    BE-->>FE: 200 OK (ready to confirm)
    FE-->>SV: Hộp thoại xác nhận
    SV->>FE: Xác nhận đăng ký
    FE->>BE: POST /registrations {classId}
    BE->>DC: Tạo đăng ký (giảm chỗ atomically)
    alt Còn chỗ
      DC-->>BE: 201 Created
      BE-->>FE: 201 Created
      FE-->>SV: "Đăng ký thành công" + cập nhật lịch dự kiến
    else Hết chỗ
      DC-->>BE: 409 Full
      BE-->>FE: 409 Full
      FE-->>SV: "Hết chỗ" (không ghi nhận)
    end
  else Không hợp lệ
    BE-->>FE: 400 Bad Request (lý do: tiên quyết/xung đột…)
    FE-->>SV: Hiển thị lý do, không ghi nhận
  end

  opt AF7.1 — Thêm vào "Yêu thích"
    SV->>FE: Thêm lớp L vào danh sách yêu thích
    FE->>BE: POST /wishlist {classId}
    BE-->>FE: 200 OK
    FE-->>SV: "Đã lưu vào yêu thích"
  end

  opt AF7.2 — "Đề xuất lịch" trước khi xác nhận
    SV->>FE: Nhấn "Đề xuất lịch"
    FE->>BE: GET /schedule/suggest?term=next
    BE-->>FE: Danh sách lịch gợi ý (không xung đột)
    FE-->>SV: Chọn kịch bản lịch để đăng ký
  end
```
