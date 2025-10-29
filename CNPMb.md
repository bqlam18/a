# Activity Diagrams — Student Use Cases (Swimlanes)

Mỗi sơ đồ gồm 3 lane:
- Sinh viên
- Hệ thống
- Dịch vụ tích hợp liên quan: HCMUT_LIBRARY hoặc HCMUT_DATACORE

Lưu ý: Không dùng dấu ngoặc kép trong nhãn để tránh lỗi khi render Mermaid trên GitHub.

---

## 1) Truy cập tài liệu môn học

```mermaid
flowchart LR
  subgraph SV["Sinh viên"]
    direction TB
    sv_start1((Bắt đầu))
    sv_open_mat[Chọn mục Tài liệu môn học của môn X]
    sv_pick_file[Chọn tài liệu D để xem hoặc tải]
    sv_done1(((Kết thúc)))
  end

  subgraph SYS["Hệ thống"]
    direction TB
    sys_show_list[Hiển thị danh sách tài liệu]
    sys_req_list[Yêu cầu danh sách tài liệu môn X]
    sys_req_access[Yêu cầu URL hoặc quyền truy cập tài liệu D]
    sys_open_file[Mở hoặc tải tài liệu D]
    sys_err_access[Hiển thị lỗi không đủ quyền hoặc không tồn tại]
    sys_err_list[Hiển thị thông báo không tìm thấy tài liệu hoặc lỗi kết nối]
  end

  subgraph LIB["HCMUT_LIBRARY"]
    direction TB
    lib_list[Trả danh sách tài liệu liên quan]
    lib_none[Lỗi hoặc danh sách rỗng]
    lib_grant[Trả URL hoặc stream của tài liệu D]
    lib_deny[403 hoặc 404]
    lib_end1(((Kết thúc)))
  end

  %% Luồng chính
  sv_start1 --> sv_open_mat --> sys_req_list --> lib_list --> sys_show_list --> sv_pick_file --> sys_req_access
  sys_req_access --> lib_grant --> sys_open_file --> sv_done1

  %% Nhánh lỗi danh sách
  sys_req_list --> lib_none --> sys_err_list --> sv_done1

  %% Nhánh lỗi truy cập tài liệu
  sys_req_access --> lib_deny --> sys_err_access --> sv_done1
```

---

## 2) Xem danh sách môn học đang học

```mermaid
flowchart LR
  subgraph SV2["Sinh viên"]
    direction TB
    sv_start2((Bắt đầu))
    sv_open_my[Chọn Môn của tôi]
    sv_pick_course[Chọn môn X]
    sv_done2(((Kết thúc)))
  end

  subgraph SYS2["Hệ thống"]
    direction TB
    sys_req_enroll[Yêu cầu danh sách môn lớp kỳ hiện tại theo MSSV]
    sys_show_courses[Hiển thị danh sách và bộ lọc kỳ hoặc nhóm]
    sys_empty[Rỗng không có môn trong kỳ hiện tại]
    sys_error_enroll[Hiển thị lỗi và cho phép thử lại]
    sys_open_course[Mở trang môn X]
  end

  subgraph DC2["HCMUT_DATACORE"]
    direction TB
    dc_enroll_ok[Trả danh sách môn lớp]
    dc_enroll_err[Lỗi hoặc timeout]
    dc_end2(((Kết thúc)))
  end

  sv_start2 --> sv_open_my --> sys_req_enroll
  sys_req_enroll --> dc_enroll_ok --> sys_show_courses --> sv_pick_course --> sys_open_course --> sv_done2

  sys_req_enroll --> dc_enroll_err --> sys_error_enroll --> sv_done2
  sys_show_courses -->|Không chọn hoặc không có môn| sys_empty --> sv_done2
```

---

## 3) Xem thời khóa biểu

```mermaid
flowchart LR
  subgraph SV3["Sinh viên"]
    direction TB
    sv_start3((Bắt đầu))
    sv_open_tkb[Chọn Thời khóa biểu]
    sv_done3(((Kết thúc)))
  end

  subgraph SYS3["Hệ thống"]
    direction TB
    sys_req_tkb[Yêu cầu lịch học tuần hoặc tháng kỳ hiện tại]
    sys_show_tkb[Hiển thị lịch tuần tháng và bộ lọc cơ sở phòng môn]
    sys_empty_tkb[Trạng thái rỗng không có sự kiện trong khoảng chọn]
    sys_err_tkb[Thông báo lỗi và cho phép thử lại]
  end

  subgraph DC3["HCMUT_DATACORE"]
    direction TB
    dc_tkb_ok[Trả danh sách sự kiện môn lớp phòng cơ sở giảng viên]
    dc_tkb_err[Lỗi hoặc timeout]
    dc_end3(((Kết thúc)))
  end

  sv_start3 --> sv_open_tkb --> sys_req_tkb
  sys_req_tkb --> dc_tkb_ok --> sys_show_tkb --> sv_done3

  sys_req_tkb --> dc_tkb_err --> sys_err_tkb --> sv_done3
  sys_show_tkb -->|Không có sự kiện| sys_empty_tkb --> sv_done3
```

---

## 4) Đăng ký môn học kỳ sau

```mermaid
flowchart LR
  subgraph SV4["Sinh viên"]
    direction TB
    sv_start4((Bắt đầu))
    sv_open_reg[Chọn Đăng ký môn học kỳ sau]
    sv_search[Lọc hoặc tìm kiếm lớp]
    sv_pick_class[Chọn lớp L muốn đăng ký]
    sv_confirm[Xác nhận đăng ký]
    sv_optional_wish[Thêm lớp vào Yêu thích tùy chọn]
    sv_optional_suggest[Xem Đề xuất lịch tùy chọn]
    sv_done4(((Kết thúc)))
  end

  subgraph SYS4["Hệ thống"]
    direction TB
    sys_req_open[Yêu cầu danh mục lớp mở kỳ kế tiếp]
    sys_show_open[Hiển thị danh sách và bộ lọc khung giờ cơ sở giảng viên]
    sys_validate[Gửi kiểm tra điều kiện tiên quyết song hành xung đột và chỗ trống]
    sys_confirm[Hiển thị hộp thoại xác nhận]
    sys_create[Tạo đăng ký tại hệ thống đăng ký giảm chỗ nguyên tử]
    sys_success[Thông báo đăng ký thành công và cập nhật lịch dự kiến]
    sys_full[Thông báo hết chỗ không ghi nhận]
    sys_invalid[Hiển thị lý do không hợp lệ]
    sys_wish[Lưu vào danh sách Yêu thích]
    sys_suggest[Hiển thị các lịch gợi ý không xung đột]
  end

  subgraph DC4["HCMUT_DATACORE"]
    direction TB
    dc_open_ok[Trả danh mục lớp mở]
    dc_validate_ok[Hợp lệ]
    dc_validate_ng[Không hợp lệ]
    dc_reg_created[Đăng ký thành công còn chỗ]
    dc_reg_full[Hết chỗ]
    dc_end4(((Kết thúc)))
  end

  sv_start4 --> sv_open_reg --> sys_req_open --> dc_open_ok --> sys_show_open --> sv_search --> sv_pick_class --> sys_validate
  sys_validate --> dc_validate_ok --> sys_confirm --> sv_confirm --> sys_create
  sys_create --> dc_reg_created --> sys_success --> sv_done4
  sys_create --> dc_reg_full --> sys_full --> sv_done4

  sys_validate --> dc_validate_ng --> sys_invalid --> sv_done4

  %% Tùy chọn
  sv_optional_wish -.-> sys_wish -.-> sv_done4
  sv_optional_suggest -.-> sys_suggest -.-> sv_done4
```
