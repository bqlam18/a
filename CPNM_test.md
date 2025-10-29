# UC — Truy Cập Tài Liệu Môn Học (Access Course Materials)

Tiền điều kiện
- Sinh viên đã đăng nhập (qua SSO).
- Hệ thống đã kết nối được với HCMUT_LIBRARY.

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh viên (Mentee)
  participant SYS as Hệ thống (Cổng Tutor/Mentor)
  participant LIB as HCMUT_LIBRARY

  Note over SV,SYS: SV đã đăng nhập; hệ thống có kết nối đến HCMUT_LIBRARY

  %% Trigger
  SV->>SYS: Nhấp "Tài liệu môn học" trong trang môn X

  %% Steps 1-3
  SYS->>LIB: Tra cứu tài liệu liên quan đến môn X
  alt Tìm thấy tài liệu
    LIB-->>SYS: Danh sách tài liệu (tiêu đề, loại, liên kết)
    %% Step 4
    SYS-->>SV: Hiển thị danh sách tài liệu (liên kết trực tiếp)
    %% Step 5
    SV->>SYS: Chọn tài liệu D để xem/tải
    SYS->>LIB: Yêu cầu quyền/URL truy cập tài liệu D
    LIB-->>SYS: URL/stream tài liệu D
    SYS-->>SV: Mở/tải tài liệu D
  else Không tìm thấy hoặc lỗi kết nối
    LIB-->>SYS: 404/5xx hoặc danh sách rỗng
    SYS-->>SV: Thông báo "Không tìm thấy tài liệu liên quan từ thư viện hoặc lỗi kết nối."
  end
```
