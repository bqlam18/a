# BÁO CÁO BÀI TẬP LỚN – WEAPROUS (Reverse Proxy + Tracker + Peers)

Sinh viên: [Điền tên của bạn]  
MSSV: [Điền MSSV]  
Môn: Mạng máy tính (CO3094)  
Ngày nộp: [Điền ngày]

---

## 1. TÓM TẮT
Đồ án hiện thực một hệ thống web tối giản gồm:
- Reverse Proxy tự cài đặt (port 8080), định tuyến theo Host header và hỗ trợ load balancing (round-robin).
- Tracker (port 9000) quản lý vòng đời Peer (register, heartbeat, TTL expire, stats).
- Sample Backend (port 8000) với các API minh hoạ: /login, /logout, /user, /echo, và phục vụ static.
- Giao diện web (qua proxy) tích hợp xem danh sách Peers và (đã mở rộng) form đăng ký/heartbeat/update/xoá peer trực tiếp từ UI.

Hệ thống chạy hoàn toàn trên máy cá nhân (127.0.0.1). TTL và heartbeat hoạt động đúng, CORS đầy đủ, có script/README hướng dẫn chạy nhanh.

---

## 2. YÊU CẦU – MÔI TRƯỜNG – THƯ VIỆN
- Python ≥ 3.11
- Hệ điều hành đã kiểm thử: Windows 11 (PowerShell), thử thêm trên Ubuntu 22.04.
- Thư viện: requests (cho sanity_check), các module chuẩn Python khác.
- Cổng sử dụng mặc định:
  - Proxy: 8080
  - Backend: 8000 (có thể thêm 8001 cho load balancing)
  - Tracker: 9000

> Lưu ý: Mặc định sử dụng 127.0.0.1 để đảm bảo portable. Không hardcode IP LAN.

---

## 3. KIẾN TRÚC HỆ THỐNG

```
[ Browser ]  <--HTTP-->  [ Reverse Proxy :8080 ]  --(forward)-->  [ Backend :8000 (8001) ]
                                       \ 
                                        \--(optional)--> [ tracker.local → Tracker :9000 ]
```

- Reverse Proxy đọc proxy.conf, định tuyến theo Host (có hỗ trợ nhiều backend cho 1 host với chính sách round-robin).
- Tracker là dịch vụ REST quản lý Peer. Các dịch vụ/ứng dụng (hoặc UI) giao tiếp với Tracker để đăng ký và gửi heartbeat.
- UI (trang index) gọi trực tiếp Tracker (CORS đã bật) hoặc qua proxy nếu muốn.

---

## 4. THÀNH PHẦN CHÍNH
1) Reverse Proxy
- Đọc cấu hình host → danh sách upstream qua `proxy_pass`.
- Chính sách `dist_policy round-robin`.
- Chuẩn hoá Host header và hỗ trợ khóa “host:port” và “host” (khuyến nghị).

2) Tracker (tracker_routes.py)
- Endpoint:
  - GET /tracker/health
  - POST /tracker/register
  - POST /tracker/heartbeat
  - POST /tracker/update
  - POST /tracker/peer/remove
  - GET /tracker/peers
  - GET /tracker/stats
- TTL cleaner thread mỗi 5 giây; CORS bật trên mọi response và hỗ trợ OPTIONS.

3) Sample Backend
- Route: /login (GET/POST), /logout, /, /user (GET), /echo (POST), static /static/css|js|images.
- Đăng nhập demo: admin/password, set cookie và redirect.

4) Giao diện Web (index.html + app.js)
- Khu vực “Peers từ Tracker”: nhập Tracker URL và lấy danh sách Peers.
- ĐÃ BỔ SUNG form đăng ký/heartbeat/update/xoá peer trực tiếp từ UI.

---

## 5. CẤU HÌNH PROXY (LOCAL)
Ví dụ proxy.conf (local demo):

```conf
host "localhost" {
    proxy_pass http://127.0.0.1:8000;
}

host "app1.local" {
    proxy_pass http://127.0.0.1:9001;
}

host "app2.local" {
    proxy_pass http://127.0.0.1:9002;
    proxy_pass http://127.0.0.1:9003;
    dist_policy round-robin
}
```

> Trên máy cá nhân chỉ cần block `host "localhost"` là đủ để demo backend qua proxy.

---

## 6. HƯỚNG DẪN CHẠY
Mở 3 terminal (hoặc dùng script chạy tất cả).

1) Tracker (9000)
```bash
python start_tracker.py --server-port 9000
# Kiểm tra:
curl http://127.0.0.1:9000/tracker/health
```

2) Backend (8000)
```bash
python start_sampleapp.py --server-port 8000
# Kiểm tra:
# Mở http://127.0.0.1:8000/login và đăng nhập admin / password
```

3) Proxy (8080)
```bash
python start_proxy.py --server-port 8080
# Kiểm tra:
# Mở http://localhost:8080/login → sau login, / hiển thị trang chính, static 200
```

---

## 7. KỊCH BẢN KIỂM THỬ (DEMO)

### 7.1. UI qua Proxy
- Truy cập http://localhost:8080/login → đăng nhập → về trang index.
- Nút “Tải” (GET /user): hiển thị JSON user.
- Nút “Gửi” (POST /echo): nhập JSON → phản hồi JSON.

### 7.2. Tracker – Register & Heartbeat
- Trong UI, ô “Tracker URL”: http://127.0.0.1:9000
- Bấm “Lấy /tracker/peers”: ban đầu count=0.
- Đăng ký peer trực tiếp từ UI (Form Đăng ký/Quản lý Peer):
  - Template Backend → bấm “Đăng ký” → OK.
  - Bấm “Heartbeat” để gia hạn.
- Hoặc dùng lệnh PowerShell:
```powershell
Invoke-RestMethod -Uri http://127.0.0.1:9000/tracker/register -Method Post `
  -Body '{"peer_id":"backend1","ip":"127.0.0.1","port":8000,"roles":["backend"],"channels":["http"],"ttl":60}' `
  -ContentType 'application/json'
Invoke-RestMethod -Uri http://127.0.0.1:9000/tracker/peers | ConvertTo-Json -Depth 5
```

### 7.3. TTL Expire
- Đăng ký p2 TTL=15 (qua UI hoặc lệnh).
- Không gửi heartbeat cho p2.
- Sau ~20 giây bấm “Lấy /tracker/peers” → p2 biến mất. Console tracker in “[Tracker] expired peers: ['p2']”.

### 7.4. Load Balancing (tuỳ chọn)
- Chạy backend thứ 2: `python start_sampleapp.py --server-port 8001`
- Thêm vào proxy.conf:
```conf
host "localhost" {
    proxy_pass http://127.0.0.1:8000;
    proxy_pass http://127.0.0.1:8001;
    dist_policy round-robin
}
```
- Refresh nhiều lần → log hai backend luân phiên nhận request.

---

## 8. KẾT QUẢ DỰ KIẾN
- /login qua proxy: 200 (form), POST /login → 302 về “/”, cookie set.
- / (index) tải được assets: styles.css, app.js, welcome.png = 200.
- /user, /echo qua proxy: 200 (sau đăng nhập).
- /tracker/register trả về đối tượng peer với registered_at, last_heartbeat, ttl.
- /tracker/peers hiển thị count và danh sách peers hiện còn sống.
- TTL expire hoạt động (peer không heartbeat sẽ bị xóa sau ttl + ≤5s).
- UI có thể đăng ký/heartbeat/update/xoá peer thành công.

> Chèn ảnh chụp màn hình minh chứng:  
> - Kết quả đăng ký backend1, p1.  
> - Peers count trước/sau.  
> - TTL expire (p2 biến mất).  
> - UI form đăng ký peer và kết quả.

---

## 9. BẢO MẬT – ỔN ĐỊNH – HẠN CHẾ
- CORS cho Tracker: Access-Control-Allow-Origin: *; Methods: GET, POST, OPTIONS.
- Chưa có xác thực cho /tracker/register → phù hợp demo nội bộ; nếu mở LAN, có thể thêm “X-Tracker-Key” bắt buộc.
- TTL/Heartbeat: đảm bảo đơn vị giây nhất quán (time.time()).
- Proxy: nên in log rõ ràng khi không tìm thấy mapping host.
- Hạn chế: Reverse proxy chưa hỗ trợ HTTPS và một số header nâng cao.

---

## 10. KẾT LUẬN
Bài làm đáp ứng các yêu cầu:
- Reverse proxy định tuyến theo Host và hỗ trợ round-robin.
- Tracker quản lý Peer theo cơ chế register/heartbeat/TTL expire.
- UI cung cấp cả quan sát (peers) lẫn thao tác (register/heartbeat/update/remove).
- Tài liệu hướng dẫn chạy từ đầu, kịch bản kiểm thử và kết quả.

Hướng phát triển:
- Thêm HTTPS, health-check upstream, batch heartbeat, và dashboard realtime TTL remaining.
- Bổ sung auth/secret key cho các route nhạy cảm của Tracker.

---

## PHỤ LỤC

### A. Lệnh nhanh (PowerShell)
```powershell
# Register backend1
Invoke-RestMethod -Uri http://127.0.0.1:9000/tracker/register -Method Post `
  -Body '{"peer_id":"backend1","ip":"127.0.0.1","port":8000,"roles":["backend"],"channels":["http"],"ttl":60}' `
  -ContentType 'application/json'

# Heartbeat
Invoke-RestMethod -Uri http://127.0.0.1:9000/tracker/heartbeat -Method Post `
  -Body '{"peer_id":"backend1"}' -ContentType 'application/json'

# Peers
Invoke-RestMethod -Uri http://127.0.0.1:9000/tracker/peers | ConvertTo-Json -Depth 5

# P2 TTL=15 (demo expire)
Invoke-RestMethod -Uri http://127.0.0.1:9000/tracker/register -Method Post `
  -Body '{"peer_id":"p2","ip":"127.0.0.1","port":7011,"roles":["p2p"],"channels":["chat"],"ttl":15}' `
  -ContentType 'application/json'
```

### B. Script giữ sống nhiều peer (PS)
```powershell
$tracker = "http://127.0.0.1:9000"
$peers = @(
  @{ id="backend1"; interval=30 },
  @{ id="p1";       interval=25 }
)
while ($true) {
  $now = Get-Date
  foreach ($p in $peers) {
    if (-not $p.next -or $now -ge $p.next) {
      try {
        Invoke-RestMethod -Uri "$tracker/tracker/heartbeat" -Method Post `
          -Body ("{""peer_id"":""{0}""}" -f $p.id) -ContentType 'application/json' | Out-Host
      } catch { Write-Host "HB error $($p.id): $_" -ForegroundColor Red }
      $p.next = $now.AddSeconds($p.interval)
    }
  }
  Start-Sleep -Seconds 5
}
```

### C. Sanity test (Python)
```python
# sanity_check.py
import requests, json, sys
BACKEND="http://127.0.0.1:8000"; PROXY="http://localhost:8080"; TRACKER="http://127.0.0.1:9000"
def chk(u,m="GET",**kw):
    try: r=requests.request(m,u,timeout=5,**kw); print(r.status_code,u); return r
    except Exception as e: print("ERR",u,e)
# tracker
chk(TRACKER+"/tracker/health")
# backend direct
s=requests.Session(); chk(BACKEND+"/login"); chk(BACKEND+"/login","POST",data={"username":"admin","password":"password"}); chk(BACKEND+"/user")
# proxy
p=requests.Session(); chk(PROXY+"/login"); chk(PROXY+"/login","POST",data={"username":"admin","password":"password"}); chk(PROXY+"/user")
# tracker register + peers
payload={"peer_id":"sanity-backend","ip":"127.0.0.1","port":8000,"roles":["backend"],"channels":["http"],"ttl":60}
chk(TRACKER+"/tracker/register","POST",headers={"Content-Type":"application/json"},data=json.dumps(payload)); chk(TRACKER+"/tracker/peers")
```

### D. Ghi chú CORS trong Tracker
- Mọi response kèm:
  - Access-Control-Allow-Origin: *
  - Access-Control-Allow-Headers: Content-Type
  - Access-Control-Allow-Methods: GET, POST, OPTIONS
- Có route OPTIONS cho các endpoint.

### E. Khác biệt so với cấu hình mẫu dùng IP LAN
- Bài này chuẩn hoá dùng 127.0.0.1; nếu cần IP LAN có thể thêm proxy.conf.lab.
- Proxy hỗ trợ khóa host cả dạng “host:port” và “host” (khuyến nghị).

---

## TÀI LIỆU THAM KHẢO
- RFC 7230–7235 (HTTP/1.1)
- Nginx upstream (concept) – tham khảo chính sách round-robin
- Tài liệu môn học – yêu cầu và cấu trúc bài tập lớn
