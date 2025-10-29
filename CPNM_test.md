# UC — Truy Cập Tài Liệu Môn Học (Access Course Materials)

Tiền điều kiện
- Sinh viên đã đăng nhập qua SSO.
- Hệ thống đã kết nối với HCMUT_LIBRARY.  

```mermaid
sequenceDiagram
  autonumber
  actor SV as Sinh vien
  participant SYS as He thong
  participant LIB as HCMUT_LIBRARY

  SV->>SYS: Mo muc Tai lieu mon hoc cua mon X
  SYS->>LIB: Tim tai lieu lien quan den mon X
  LIB-->>SYS: Danh sach tai lieu
  SYS-->>SV: Hien thi danh sach
  SV->>SYS: Chon tai lieu D
  SYS->>LIB: Yeu cau URL tai lieu D
  LIB-->>SYS: URL tai lieu D
  SYS-->>SV: Mo tai lieu D
```
