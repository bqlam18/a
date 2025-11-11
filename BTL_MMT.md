# BÁO CÁO BÀI TẬP LỚN – HỆ THỐNG HTTP + PROXY + TRACKER + P2P CHAT (WeApRous)


## 1. TÓM TẮT

Bài tập lớn hiện thực một hệ thống máy chủ HTTP tự xây dựng kết hợp ứng dụng Web và giao tiếp peer‑to‑peer (P2P) trên nền socket thuần Python (không dùng framework web sẵn có). Hệ thống gồm 3 thành phần chính:

1) Reverse Proxy (daemon/proxy.py): nhận và định tuyến HTTP theo Host, hỗ trợ cân bằng tải (round‑robin/least‑connection/random).  
2) Backend Server (daemon/backend.py + apps/sampleApp.py): xử lý route REST (/login, /logout, /user, /echo), quản lý cookie session, phục vụ tệp tĩnh.  
3) Tracker (apps/tracker.py): “service discovery + liveness” cho peer; có các API register/heartbeat/update/remove/peers/stats và mở rộng heartbeat_batch.  

Phần mở rộng P2P:
- Peer HTTP API (p2p/p2p_peer.py + start_peer.py): cung cấp /p2p/msg, /p2p/inbox, /p2p/inbox/clear, /p2p/whoami để các peer gửi/nhận tin trực tiếp.  
- Giao diện Web (static/index.html + static/js/app.js) có panel “Chat P2P” cho phép người dùng chọn Local Peer ID, Target Peer, gửi tin và tải inbox.

Kết quả:
- Đăng nhập qua proxy, duy trì session bằng cookie.  
- Tracker quản lý TTL và loại peer hết hạn.  
- UI có thể đăng ký/heartbeat/update/xóa peer và gửi/nhận tin P2P giữa nhiều peer (một máy hoặc nhiều máy).  

---

## 2. GIỚI THIỆU TỔNG QUAN

### 2.1 Mục tiêu
- Nắm vững mô hình client–server và peer‑to‑peer.  
- Thực hành socket TCP/IP, giao thức HTTP/1.1, cookie/session.  
- Tự thiết kế reverse proxy, routing theo Host, cân bằng tải.  
- Xây dựng tracker cho discovery/liveness (TTL + heartbeat).  
- Tích hợp WebApp gọi REST + P2P chat.

### 2.2 Công nghệ sử dụng
- Python 3.11+, mô‑đun chuẩn: socket, threading, time, json, http.server.  
- RESTful JSON, MIME, CORS.  
- Không dùng framework web (Flask, FastAPI…) – chỉ socket/HTTP thuần.

---

## 3. THIẾT KẾ HỆ THỐNG

### 3.1 Kiến trúc tổng thể

```
[ Browser ]
    | HTTP
    v
[ Reverse Proxy :8080 ] ----> [ Backend :8000 (8001...) - Request/Response/HttpAdapter ]
                        \---> (optional hosts khác)
                        \---> [ Static files / images / css / js ]

[ Tracker :9000 ]  <--- REST: register/heartbeat/update/remove/peers/stats (+ heartbeat_batch)

[ Peer pX (HTTP API :751x, TCP P2P :701x) ]  <--- /p2p/msg, /p2p/inbox, whoami
[ Peer pY (HTTP API :751y, TCP P2P :701y) ]
```

Luồng chính: Browser → Proxy → Backend (login, set‑cookie) → index.html (JS) → gọi REST tracker và (khi gửi tin) gọi trực tiếp HTTP API của peer đích. Tracker cung cấp danh sách peers (discovery/liveness).

### 3.2 Server Process

Vòng đời xử lý request (áp dụng cho cả proxy/backend):
- socket.accept() → spawn thread → HttpAdapter.handle_client()  
- HttpAdapter dựng Request (parse request line, headers, cookies, body), gắn hook route  
- Thực thi handler nếu có → Response.build_response() → sendall() → close

Routing policy (proxy):
- round‑robin: xoay vòng tuyến tới các upstream.  
- least‑connection: chọn backend có số kết nối hoạt động ít nhất.  
- random: chọn ngẫu nhiên trong các backend “healthy”.

### 3.3 Communication Flow

Client gửi HTTP → Proxy phân tích Host → chọn backend theo policy → chuyển tiếp gói HTTP → Backend xử lý route (hoặc tĩnh) → trả Response → Proxy chuyển lại cho client.  
Với P2P chat: UI tra danh sách peers từ Tracker → chọn ip:api_port của peer đích → gọi POST /p2p/msg tới peer đích.

---

## 4. HIỆN THỰC/PHÂN TÍCH CÁC MODULES

### 4.1 HTTP Server với Cookie Session

- Xác thực: POST /login (username=admin, password=password).  
- Thành công: Set‑Cookie: auth=true; chuyển về index.html.  
- Thất bại: 401 Unauthorized (Unauthorized.html).  
- Bảo vệ nội dung: /index.html yêu cầu cookie hợp lệ.

#### 4.1.1 Request (daemon/request.py)

- Vai trò: bóc tách gói HTTP thô → method, path, version, headers (CaseInsensitive), cookies, body; gắn hook route nếu có.  
- Hàm chính:
  - extract_request_line(): tách method/path/version.  
  - prepare_headers(): chuẩn hóa key header về chữ thường.  
  - prepare(): tổng hợp parse + gắn hook + parse cookie/body.  
  - prepare_body()/prepare_content_length(): phục vụ khi server đóng vai client (proxy).  

Tương tác:
- HttpAdapter tạo Request, gán hook, rồi Response sử dụng để dựng phản hồi.

#### 4.1.2 Response (daemon/response.py)

- Thuộc tính: status_code, headers, cookies, _content, reason, request.  
- Hàm:
  - get_mime_type(): xác định MIME.  
  - build_content(): đọc file tĩnh theo đường dẫn, trả 200/404/403/500.  
  - build_response_header(): lắp header chuẩn (Date, CORS, Content‑Length…).  
  - build_notfound(), build_response(): luồng tổng gộp logic login/logout/redirect/tĩnh.

Điểm nhấn:
- CORS mở mặc định: Access‑Control‑Allow‑Origin: *; Allow‑Methods: GET, POST, OPTIONS.  
- Có nhánh OPTIONS để browser preflight không lỗi.

#### 4.1.3 Backend (daemon/backend.py)

- Tạo socket TCP, listen backlog, spawn một thread cho mỗi client.  
- Mỗi thread gọi HttpAdapter.handle_client(); log kết nối.  
- Hàm create_backend(ip, port, routes) → entry point khởi chạy.

#### 4.1.4 Reverse Proxy (daemon/proxy.py)

- Biến PROXY_PASS: ánh xạ hostname → danh sách upstream.  
- resolve_routing_policy(): round‑robin/least‑connection/random.  
- forward_request(): mở socket tới backend, gửi request, nhận response trả về client.  
- get_healthy_backends(): kiểm tra kết nối TCP ngắn hạn.  
- create_proxy(ip, port, routes): khởi động proxy.

#### 4.1.5 HttpAdapter (daemon/httpadapter.py)

- Cầu nối socket ↔ logic: đọc bytes, Request.prepare(), chạy hook, Response.build_response(), sendall().  
- Hỗ trợ trích cookies, tạo header ủy quyền khi proxy có user:pass.

---

### 4.2 Tracker (apps/tracker.py)

- Trạng thái:  
  - peers: peer_id → info {ip, port, roles, channels, meta, load, ttl, last_heartbeat,...}  
  - index_role, index_channel để truy vấn nhanh.  
- TTL cleaner: thread 5s quét và loại peers quá hạn.  
- CORS mở; có route OPTIONS cho preflight.

Endpoints:
- GET /tracker/health  
- POST /tracker/register  
- POST /tracker/heartbeat  
- POST /tracker/update  
- POST /tracker/peer/remove  
- GET /tracker/peers  
- GET /tracker/stats  
- NEW: POST /tracker/heartbeat_batch  
  - Body: {"peers":["id1","id2"], "ttl":120, "load":{"cpu":0.35}}  
  - Trả: {"updated":[...], "not_found":[...]}

Mục đích:
- Service discovery + liveness (TTL).  
- Cho phép UI thao tác đăng ký/heartbeat/update/xóa peer, xem danh sách/stats.

---

### 4.3 Ứng dụng P2P Chat

#### 4.3.1 Peer TCP + HTTP API (p2p/p2p_peer.py)

- TCP P2P (JSON Lines):  
  - Listener thread và recv per‑connection; hỗ trợ broadcast kênh (“chat”), direct message (“direct”), gói “hello”.  
- HTTP API cho browser:  
  - POST /p2p/msg: nhận tin (“from”, “to”, “content”) → lưu inbox.  
  - GET /p2p/inbox: trả inbox hiện thời.  
  - POST /p2p/inbox/clear: xóa inbox.  
  - GET /p2p/whoami: trả về peer_id thực trên cổng đang nghe.  
  - Bật CORS.  
- Inbox lưu trong bộ nhớ tiến trình; có thể mở rộng lưu DB.

#### 4.3.2 Trình khởi động peer (start_peer.py)

- Khởi chạy đồng thời:
  - TCP P2P listener (port 701x).  
  - HTTP API cho UI (port 751x).  
- Tùy chọn --register: tự đăng ký vào tracker với roles=["p2p"], channels=["chat"], meta={"api_port":..., "p2p_port":...} và chạy heartbeat định kỳ.

#### 4.3.3 WebApp (static/index.html + static/js/app.js)

- Khu “Peers từ Tracker”: nhập Tracker URL, lấy danh sách /tracker/peers.  
- Khu “Đăng ký/Quản lý Peer”: form register + heartbeat + update TTL + remove.  
- Khu “Chat P2P”:  
  - Local Peer ID, chọn Target Peer (lọc channel=chat/role=p2p).  
  - Gửi tin: UI gọi POST /p2p/msg tới api_port của peer đích.  
  - Tải Inbox/Xóa Inbox: UI gọi /p2p/inbox(/clear) tới api_port của peer local.  
  - Ưu tiên meta.api_port nếu có; fallback p.port.

#### 4.3.4 Cách triển khai thực tế

- Khởi động backend (sample app) và tracker:
```bash
python start_sampleapp.py --server-port 8000
python start_tracker.py    --server-port 9000
```

- Khởi động reverse proxy:
```bash
python start_proxy.py --server-port 8080
```

- Mỗi peer P2P chạy riêng (có HTTP API + auto register):
```bash
# Peer 1
python start_peer.py --peer-id p1 --p2p-port 7010 --api-port 7510 --register --tracker http://127.0.0.1:9000 --ttl 180
# Peer 2
python start_peer.py --peer-id p2 --p2p-port 7011 --api-port 7511 --register --tracker http://127.0.0.1:9000 --ttl 180
```

- Truy cập qua trình duyệt:
```text
http://localhost:8080/login.html
```
Đăng nhập xong vào index.html → nhập Tracker URL (http://127.0.0.1:9000) → Refresh danh sách → dùng “Chat P2P” để gửi/nhận tin giữa p1 và p2.

Ghi chú:
- Có thể demo toàn bộ trên MỘT máy (mỗi peer là một tiến trình với api‑port khác nhau).  
- Nếu demo đa máy trong LAN, chạy peers với --ip 0.0.0.0 và mở firewall cổng 751x.

---

### 4.4 Code hiện thực

#### Daemon

| File | Vai trò |
|------|---------|
| daemon/request.py | Phân tích gói HTTP, tách method/path/headers/cookies/body; gắn hook route. |
| daemon/response.py | Dựng HTTP response (status line, headers, body), xác định MIME, CORS, cookie. |
| daemon/httpadapter.py | Cầu nối socket ↔ xử lý HTTP; đọc yêu cầu, gọi handler, gửi phản hồi. |
| daemon/backend.py | Máy chủ TCP đa luồng, listen và spawn thread cho từng client, gọi HttpAdapter. |
| daemon/proxy.py | Reverse proxy định tuyến theo Host; policy round‑robin/least‑connection/random; health‑check. |
| daemon/dictionary.py | CaseInsensitiveDict cho header không phân biệt hoa/thường. |
| daemon/utils.py | Hàm tiện ích (log, parse…), dùng chung cho daemon. |
| daemon/weaprous.py | Khung App/router (nếu sử dụng) để register routes. |

#### Ứng dụng (WeApRous App)

| Tệp | Vai trò |
|-----|---------|
| apps/sampleApp.py | Ứng dụng REST mẫu: /login, /logout, /user, /echo; phục vụ tĩnh. |
| apps/tracker.py | Tracker REST: register/heartbeat/update/remove/peers/stats (+ heartbeat_batch), TTL cleaner. |
| p2p/p2p_peer.py | Peer TCP + HTTP API cho browser: /p2p/msg, /p2p/inbox, /p2p/inbox/clear, /p2p/whoami. |
| start_peer.py | Chạy một peer: TCP P2P + HTTP API; tùy chọn --register và heartbeat định kỳ. |

#### WebApp (Giao diện HTML)

| File | Chức năng |
|------|-----------|
| static/index.html | Trang chính: panel user/echo; panel tracker; form register/heartbeat/update/remove peer; panel Chat P2P. |
| static/js/app.js | Logic frontend: gọi REST tracker/backend, gửi/nhận tin P2P, hiển thị JSON. |
| static/css/styles.css | Giao diện CSS. |
| static/images/{favicon.ico,welcome.jpg} | Tài nguyên tĩnh. |
| login.html (phục vụ từ apps/sampleApp) | Form đăng nhập, POST /login. |
| Unauthorized.html (nếu có) | Trang thông báo 401 khi chưa đăng nhập. |

---

## 5. KIỂM THỬ VÀ KẾT QUẢ

### 5.1 Sanity test (REST)
- GET /tracker/health → 200 {ok:true, uptime:...}  
- POST /tracker/register → 201 trả đối tượng peer (registered_at, ttl, last_heartbeat).  
- POST /tracker/heartbeat_batch → {"updated":[...], "not_found":[]}  
- GET /tracker/peers → count và danh sách peers đang sống.  
- TTL expire: tạo peer TTL ngắn (15s), không heartbeat → sau ~20s biến mất.

### 5.2 Đăng nhập và truy cập qua Proxy
- GET /login.html qua http://localhost:8080 → form hiển thị.  
- POST /login (admin/password) → Set‑Cookie: auth=true; 302 về /.  
- /index.html tải CSS/JS/ảnh 200.  
- /user, /echo qua proxy hoạt động bình thường (sau login).

### 5.3 Chat P2P (HTTP API)
- start_peer.py chạy 2 peer p1@7510, p2@7511 (đã register vào tracker).  
- Tại UI:
  - Local Peer ID: p1; Target: p2 → “Gửi tin” → 200.  
  - Đổi Local Peer ID: p2 → “Tải inbox local” → thấy tin nhận.  
  - “Xóa inbox” → count=0.  
- whoami kiểm chứng đúng cổng/peer:
  - GET http://127.0.0.1:7510/p2p/whoami → {"peer_id":"p1"}  
  - GET http://127.0.0.1:7511/p2p/whoami → {"peer_id":"p2"}

Kết quả đạt:
- Discovery/liveness chính xác; TTL cleaner loại peer quá hạn.  
- Proxy định tuyến Host và round‑robin (khi có nhiều backend).  
- UI thao tác đủ: register/heartbeat/update/remove/batch + P2P chat.

---

## 6. ĐÁNH GIÁ

Ưu điểm:
- Tự hiện thực pipeline HTTP đầy đủ bằng socket: dễ hiểu cơ chế lớp giao vận.  
- Proxy có policy đa dạng, health‑check cơ bản.  
- Tracker có đủ vòng đời peer và mở rộng heartbeat_batch.  
- UI thân thiện, có panel Chat P2P chạy trên một máy hoặc nhiều máy.  

Hạn chế:
- Chưa có HTTPS; bảo mật demo mức cơ bản (chưa có auth cho tracker/peer).  
- Inbox lưu RAM, mất khi restart; chưa realtime (WS) trên trình duyệt.  
- NAT traversal chưa xử lý (P2P chủ yếu trong cùng host/LAN).

Hướng phát triển:
- Bổ sung secret header (X‑Tracker‑Key, X‑P2P-Key) hoặc token.  
- Thêm WebSocket để nhận inbox realtime.  
- Lưu inbox vào SQLite/Redis; lọc peers theo role/channel qua querystring.  
- Chính sách load‑balancing dựa trên load từ tracker (least‑load).

---

## 7. HƯỚNG DẪN TRIỂN KHAI NHANH

1) Cài đặt môi trường:
```bash
python -V  # >= 3.11
pip install -r requirements.txt  # nếu có (requests cho sanity_check)
```

2) Chạy các thành phần:
```bash
# Tracker, Backend, Proxy
python start_tracker.py    --server-port 9000
python start_sampleapp.py  --server-port 8000
python start_proxy.py      --server-port 8080

# Peers (mỗi cửa sổ terminal riêng)
python start_peer.py --peer-id p1 --p2p-port 7010 --api-port 7510 --register --tracker http://127.0.0.1:9000 --ttl 180
python start_peer.py --peer-id p2 --p2p-port 7011 --api-port 7511 --register --tracker http://127.0.0.1:9000 --ttl 180
```

3) Truy cập:
```text
http://localhost:8080/login.html
```
- Đăng nhập (admin/password) → vào index, đặt Tracker URL = http://127.0.0.1:9000.  
- Chat P2P: Local Peer ID = p1 → gửi tới p2; đổi sang p2 → “Tải inbox local”.

4) Kiểm chứng nhanh bằng PowerShell/curl:
```powershell
# Heartbeat batch
$body = @{ peers=@("p1","p2"); ttl=180; load=@{cpu=0.2} } | ConvertTo-Json
Invoke-RestMethod -Uri http://127.0.0.1:9000/tracker/heartbeat_batch -Method Post -Body $body -ContentType 'application/json'

# Gửi tin trực tiếp tới p2 (HTTP API)
Invoke-RestMethod -Uri http://127.0.0.1:7511/p2p/msg -Method Post `
  -Body '{"from":"p1","to":"p2","content":"hello"}' -ContentType 'application/json'

# Đọc inbox p2
Invoke-RestMethod -Uri http://127.0.0.1:7511/p2p/inbox | ConvertTo-Json -Depth 5
```

---

## 8. KẾT LUẬN

Bài làm đáp ứng mục tiêu: hiện thực một hệ thống HTTP đa tầng (Proxy–Backend–App), một dịch vụ Tracker quản lý vòng đời peer, và cơ chế P2P chat dựa trên HTTP API để trình duyệt có thể gửi/nhận tin trực tiếp giữa các peer. Toàn bộ được xây dựng bằng socket thuần Python, minh họa rõ ràng pipeline HTTP, cookie session, routing theo Host, load‑balancing, discovery + TTL/heartbeat và truyền thông P2P.

---

## PHỤ LỤC

### A. proxy.conf (ví dụ local)

```conf
host "localhost" {
    proxy_pass http://127.0.0.1:8000;
}
# Có thể thêm nhiều host và round-robin nếu chạy backend 8001, 8002...
```

### B. Sanity script (tùy chọn)

```python
# sanity_check.py
import requests, json
T="http://127.0.0.1:9000"; B="http://127.0.0.1:8000"; P="http://localhost:8080"
def chk(u,m="GET",**kw):
    try:
        r=requests.request(m,u,timeout=5,**kw); print(r.status_code,u); return r
    except Exception as e:
        print("ERR",u,e)

chk(T+"/tracker/health")
s=requests.Session(); chk(B+"/login"); chk(B+"/login","POST",data={"username":"admin","password":"password"}); chk(B+"/user")
p=requests.Session(); chk(P+"/login"); chk(P+"/login","POST",data={"username":"admin","password":"password"}); chk(P+"/user")

payload={"peer_id":"sanity","ip":"127.0.0.1","port":7510,"roles":["p2p"],"channels":["chat"],"ttl":120,"meta":{"api_port":7510}}
chk(T+"/tracker/register","POST",headers={"Content-Type":"application/json"},data=json.dumps(payload))
chk(T+"/tracker/peers")
```

### C. Ghi chú vận hành
- Với demo một máy: mỗi peer dùng api‑port khác nhau (7510, 7511, …).  
- Với demo nhiều máy trong LAN: chạy peer với --ip 0.0.0.0 và mở firewall cổng api‑port.
