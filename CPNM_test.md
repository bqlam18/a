# Activity Diagrams — Student Use Cases (HCMUT Tutor/Mentor)

Bao gồm 4 use case phía Sinh viên:
- Truy cập tài liệu môn học
- Xem danh sách môn học (đang học)
- Xem thời khóa biểu
- Đăng ký môn học kỳ sau

---

## UC — Truy cập tài liệu môn học

```mermaid
flowchart TD
  A(("Bắt đầu"))
  B[SV mở mục Tài liệu môn học của môn X]
  C[Hệ thống yêu cầu HCMUT_LIBRARY tìm tài liệu liên quan đến môn X]
  D{Nhận được danh sách tài liệu?}
  E[Hiển thị danh sách tài liệu cho SV]
  F[SV chọn tài liệu D]
  G[Hệ thống yêu cầu HCMUT_LIBRARY cấp quyền hoặc URL tài liệu D]
  H{Được cấp quyền hoặc URL?}
  I[Hiển thị hoặc tải tài liệu D]
  J[Thông báo không đủ quyền hoặc không tồn tại]
  K[Thông báo không tìm thấy tài liệu hoặc lỗi kết nối]
  Z(("Kết thúc"))

  A --> B --> C --> D
  D -->|Có| E --> F --> G --> H
  D -->|Không| K --> Z
  H -->|Có| I --> Z
  H -->|Không| J --> Z
```

---

## UC — Xem danh sách môn học (đang học)

```mermaid
flowchart TD
  A(("Bắt đầu"))
  B[SV mở trang Môn của tôi]
  C[Hệ thống yêu cầu HCMUT_DATACORE trả danh sách môn lớp SV đang học trong kỳ hiện tại]
  D{Lấy được dữ liệu?}
  E{Có môn trong kỳ hiện tại?}
  F[Hiển thị danh sách và bộ lọc kỳ hoặc nhóm]
  G[SV chọn môn X]
  H[Mở trang môn X]
  I[Trạng thái rỗng và gợi ý chọn kỳ khác]
  J[Thông báo lỗi và cho phép thử lại]
  Z(("Kết thúc"))

  A --> B --> C --> D
  D -->|Có| E
  D -->|Lỗi hoặc timeout| J --> Z
  E -->|Có| F --> G --> H --> Z
  E -->|Không| I --> Z
```

---

## UC — Xem thời khóa biểu

```mermaid
flowchart TD
  A(("Bắt đầu"))
  B[SV chọn Thời khóa biểu]
  C[Hệ thống yêu cầu HCMUT_DATACORE trả lịch học tuần hoặc tháng cho kỳ hiện tại]
  D{Lấy được lịch?}
  E[Hiển thị lịch tuần tháng và bộ lọc cơ sở phòng môn]
  F{Có sự kiện trong khoảng thời gian đã chọn?}
  G[Hiển thị trạng thái rỗng]
  H[Thông báo lỗi và cho phép thử lại]
  Z(("Kết thúc"))

  A --> B --> C --> D
  D -->|Có| E --> F
  D -->|Lỗi hoặc timeout| H --> Z
  F -->|Có| Z
  F -->|Không| G --> Z
```

---

## UC — Đăng ký môn học kỳ sau

```mermaid
flowchart TD
  A(("Bắt đầu"))
  B[SV mở Đăng ký môn học kỳ sau]
  C[Hệ thống yêu cầu HCMUT_DATACORE trả danh mục lớp mở của kỳ kế tiếp]
  D[Hiển thị danh sách và bộ lọc khung giờ cơ sở giảng viên]
  E[SV tìm kiếm hoặc lọc]
  F[SV chọn lớp L muốn đăng ký]
  G[Hệ thống gửi yêu cầu kiểm tra điều kiện tới HCMUT_DATACORE gồm tiên quyết song hành xung đột và chỗ trống]
  H{Kết quả hợp lệ?}
  I[Hiển thị hộp thoại xác nhận]
  J[SV xác nhận đăng ký]
  K[Hệ thống tạo đăng ký tại HCMUT_DATACORE và trừ chỗ nguyên tử]
  L{Còn chỗ?}
  M[Thông báo Đăng ký thành công và cập nhật lịch dự kiến]
  N[Thông báo Hết chỗ không ghi nhận]
  O[Hiển thị lý do không hợp lệ như tiên quyết xung đột hoặc thiếu chỗ]
  P[SV thêm lớp vào danh sách Yêu thích tùy chọn]
  Q[Hệ thống lưu vào danh sách Yêu thích]
  R[SV xem Đề xuất lịch tùy chọn]
  S[Hệ thống lấy và hiển thị các lịch gợi ý không xung đột]
  Z(("Kết thúc"))

  A --> B --> C --> D --> E --> F --> G --> H
  H -->|Hợp lệ| I --> J --> K --> L
  H -->|Không hợp lệ| O --> Z
  L -->|Còn chỗ| M --> Z
  L -->|Hết chỗ| N --> Z

  %% Luồng tùy chọn quay lại danh sách để thao tác tiếp
  D -.-> P --> Q -.-> D
  D -.-> R --> S -.-> D
```
