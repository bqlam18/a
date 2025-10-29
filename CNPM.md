# Sequence Diagrams — Student Use Cases (Black-box System)

Ghi chú
- Subject “Hệ thống (Cổng Tutor/Mentor)” là ứng dụng bạn xây; không phải actor.
- Tiền điều kiện cho các UC: Sinh viên đã đăng nhập qua HCMUT_SSO.
- Không dùng `activate`/`deactivate` để tương thích Mermaid trên GitHub.

## UC: Truy cập tài liệu môn học

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh viên
  participant SYS as Hệ thống (Cổng Tutor/Mentor)
  participant DC as HCMUT_DATACORE
  participant LIB as HCMUT_LIBRARY

  Note over SV,SYS: Tiền điều kiện: SV đã đăng nhập qua SSO

  SV->>SYS: Mở "Tài liệu môn học" của môn X
  SYS->>DC: Kiểm tra enrollment(studentId, courseId=X)

  alt SV có đăng ký môn X
    DC-->>SYS: Enrollment OK
    SYS->>LIB: Lấy danh sách tài liệu(courseId=X)
    alt Thành công
      LIB-->>SYS: Danh sách tài liệu
      SYS-->>SV: Hiển thị danh sách tài liệu

      SV->>SYS: Mở tài liệu D
      SYS->>LIB: Yêu cầu quyền truy cập tài liệu D
      alt Được cấp quyền/URL
        LIB-->>SYS: 200 {signedUrl|stream}
        SYS-->>SV: Mở/tải tài liệu
      else Không đủ quyền hoặc không tồn tại
        LIB-->>SYS: 403/404
        SYS-->>SV: Thông báo lỗi tương ứng
      end
    else Lỗi thư viện
      LIB-->>SYS: 5xx/timeout
      SYS-->>SV: "Không thể tải tài liệu, vui lòng thử lại"
    end
  else Không có enrollment
    DC-->>SYS: Not Found
    SYS-->>SV: "Bạn chưa đăng ký môn này"
  end
```

## UC: Xem danh sách môn học (đang học)

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh viên
  participant SYS as Hệ thống (Cổng Tutor/Mentor)
  participant DC as HCMUT_DATACORE

  Note over SV,SYS: Tiền điều kiện: SV đã đăng nhập

  SV->>SYS: Nhấn "Môn của tôi"
  SYS->>DC: Lấy enrollments(studentId, term=current)

  alt Thành công
    DC-->>SYS: Danh sách môn + lớp
    SYS-->>SV: Hiển thị danh sách + bộ lọc (kỳ/nhóm)
    alt SV chọn môn X
      SV->>SYS: Chọn môn X
      SYS-->>SV: Mở trang môn X (đi tới các chức năng môn)
    else Không có môn trong kỳ
      SYS-->>SV: Trạng thái rỗng + gợi ý chọn kỳ khác
    end
  else Lỗi/timeout
    DC-->>SYS: 5xx/timeout
    SYS-->>SV: "Không thể tải danh sách môn" (cho phép thử lại)
  end
```

## UC: Xem thời khóa biểu

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh viên
  participant SYS as Hệ thống (Cổng Tutor/Mentor)
  participant DC as HCMUT_DATACORE

  Note over SV,SYS: Tiền điều kiện: SV đã đăng nhập

  SV->>SYS: Chọn "Thời khóa biểu"
  SYS->>DC: Lấy timetable(studentId, range=week, term=current)

  alt Thành công
    DC-->>SYS: Danh sách sự kiện (môn, lớp, phòng, cơ sở, GV)
    SYS-->>SV: Hiển thị lịch tuần/tháng + bộ lọc (cơ sở/phòng/môn)
    alt Không có sự kiện trong khoảng chọn
      SYS-->>SV: Trạng thái rỗng
    end
  else Lỗi/không truy xuất được lịch
    DC-->>SYS: 5xx/timeout
    SYS-->>SV: Thông báo lỗi, cho phép thử lại
  end
```

## UC: Đăng ký môn học kỳ sau

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh viên
  participant SYS as Hệ thống (Cổng Tutor/Mentor)
  participant DC as Hệ thống đăng ký (HCMUT_DATACORE/Đào tạo)

  Note over SV,SYS: Tiền điều kiện: Đang/đến thời gian cho phép đăng ký

  SV->>SYS: Mở "Đăng ký môn kỳ sau"
  SYS->>DC: Lấy danh mục lớp mở (term=next)
  DC-->>SYS: Danh sách môn/lớp + chỗ còn
  SYS-->>SV: Hiển thị danh sách + bộ lọc (khung giờ, cơ sở, GV)

  SV->>SYS: Tìm kiếm/Lọc
  SYS-->>SV: Kết quả phù hợp

  SV->>SYS: Chọn lớp L để đăng ký
  SYS->>DC: Validate({classId, studentId}: tiên quyết, song hành, xung đột, chỗ trống)
  DC-->>SYS: {valid, reasons?, seatStatus}

  alt Hợp lệ
    SYS-->>SV: Hiển thị hộp thoại xác nhận
    SV->>SYS: Xác nhận đăng ký
    SYS->>DC: Tạo đăng ký (atomic seat decrement)
    alt Còn chỗ
      DC-->>SYS: 201 Created
      SYS-->>SV: "Đăng ký thành công" + cập nhật lịch dự kiến
    else Hết chỗ
      DC-->>SYS: 409 Full
      SYS-->>SV: "Hết chỗ" (không ghi nhận)
    end
  else Không hợp lệ
    SYS-->>SV: Hiển thị lý do (tiên quyết/xung đột/không đủ chỗ)
  end

  opt AF — Thêm vào "Yêu thích"
    SV->>SYS: Thêm lớp L vào danh sách yêu thích
    SYS-->>SV: "Đã lưu"
  end

  opt AF — Xem "Đề xuất lịch"
    SV->>SYS: Nhấn "Đề xuất lịch"
    SYS->>DC: Lấy các cấu hình lịch không xung đột (term=next)
    DC-->>SYS: Danh sách lịch gợi ý
    SYS-->>SV: Hiển thị để SV chọn kịch bản
  end
```
