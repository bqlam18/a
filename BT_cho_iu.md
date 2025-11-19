@startuml
(*) --> [Bank Manager đăng nhập thành công] Đăng nhập hệ thống
--> [Chọn tab "Add Customer"] Chọn tab Add Customer
--> [Nhập thông tin khách hàng] Nhập First Name, Last Name, Post Code
--> [Nhấn "Add Customer"] Nhấn nút Add Customer

if (Dữ liệu hợp lệ?) then (Yes)
  --> [Hiển thị thông báo thành công] Hiển thị thông báo "Customer added successfully"
  --> [Cập nhật danh sách khách hàng] Khách hàng mới xuất hiện ở tab "Customers"
else (No)
  --> [Hiển thị thông báo lỗi] Hiện thông báo lỗi/bôi đỏ trường bị thiếu
endif

--> (*)
@enduml
