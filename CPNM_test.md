# UC — Truy Cập Tài Liệu Môn Học (Access Course Materials)

Tiền điều kiện
- Sinh viên đã đăng nhập qua SSO.
- Hệ thống đã kết nối với HCMUT_LIBRARY.

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh viên (Mentee)
  participant SYS as Hệ thống (Cổng Tutor/Mentor)
  participant LIB as HCMUT_LIBRARY

  Note over SV,SYS: SV đã đăng nhập; hệ thống đã kết nối với HCMUT_LIBRARY

  %% Trigger
  SV->>SYS: Chọn mục Tài liệu môn học của môn X

  %% Tra cứu tài liệu
  SYS->>LIB: Tìm tài liệu liên quan đến môn X

  alt Có tài liệu
    LIB-->>SYS: Danh sách tài liệu (tiêu đề, loại, liên kết)
    SYS-->>SV: Hiển thị danh sách tài liệu

    SV->>SYS: Chọn tài liệu D để xem hoặc tải
    SYS->>LIB: Yêu cầu quyền truy cập hoặc URL của tài liệu D

    alt Được cấp quyền
      LIB-->>SYS: URL hoặc stream của tài liệu D
      SYS-->>SV: Mở hoặc tải tài liệu D
    else Không đủ quyền hoặc không tồn tại
      LIB-->>SYS: 403 hoặc 404
      SYS-->>SV: Thông báo lỗi tương ứng
    end

  else Không tìm thấy hoặc lỗi kết nối
    LIB-->>SYS: Danh sách rỗng hoặc lỗi 5xx
    SYS-->>SV: Thông báo không tìm thấy tài liệu hoặc lỗi kết nối
  end
```
