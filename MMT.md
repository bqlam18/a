# Kế hoạch chia việc — Assignment 1 (Client–Server, P2P, Socket TCP/IP)

Mục tiêu chung
- Hoàn thành 3 phần: (1) HTTP Cookies (2) Chat lai (Client–Server + P2P) (3) Tích hợp.
- Đạt 7 điểm demo + 3 điểm báo cáo (PEP8/PEP257).

Cấu hình cổng (có thể đổi nếu cần)
- Proxy: 8080
- Backend(s): 9000, 9001 (tuỳ số instance)
- WeApRous (tracker): 8000
- P2P peers: 7001, 7002, …

Phân vai (5 người; nếu 4 người gộp WP4+WP5)
- WP1 (Proxy & LB): [Tên SV]
- WP2 (Backend + Cookie Session): [Tên SV]
- WP3 (WeApRous Tracker + REST API): [Tên SV]
- WP4 (P2P Peer TCP + Channels): [Tên SV]
- WP5 (Tích hợp, Kiểm thử, Báo cáo): [Tên SV]

Nhánh Git đề xuất
- feature/proxy, feature/backend-cookie, feature/weaprous-tracker, feature/p2p-peer, feature/integration, docs/report

Quy ước commit
- feat: tính năng mới | fix: sửa lỗi | test: test | docs: tài liệu | refactor: tái cấu trúc

---

## WP1 — Proxy & Load Balancing
Mục tiêu
- Khởi động được proxy trên 8080, parse `config/proxy.conf`, định tuyến theo host/URL, hỗ trợ round-robin đến nhiều backend.

Đầu vào
- start_proxy.py, daemon/proxy.py, config/proxy.conf

Đầu ra
- Proxy chạy ổn định, log “route resolved” và “forwarded to …”.
- Ví dụ host `app2.local` có 2 `proxy_pass` và `dist_policy round-robin`.

DoD
- Proxy lắng nghe 0.0.0.0:8080.
- 10 request liên tiếp tới `app2.local` phân phối luân phiên giữa 2 backend.
- Ghi lại log minh chứng (ảnh màn hình/đoạn log).

Test nhanh
- `curl -i -H 'Host: app2.local' http://IP:8080/`
- Lặp 10 lần, quan sát backend nào trả về.

---

## WP2 — Backend + Cookie Session (2.1)
Mục tiêu
- Xử lý `POST /login` (admin/password) → Set-Cookie; `GET /` kiểm cookie → trả index/401.

Đầu vào
- start_backend.py, daemon/backend.py, daemon/httpadapter.py, daemon/response.py, www/, static/

Đầu ra
- Index page truy cập được khi có cookie; 401 khi không có cookie.
- Header: `Cache-Control: no-store` để tránh cache.

DoD
- `POST /login` trả `Set-Cookie: auth=...; HttpOnly; SameSite=Lax`.
- `GET /`:
  - Không cookie → 401.
  - Có cookie hợp lệ → 200 và trả `/www/index.html`.
- Chạy qua Proxy 8080 → Backend 9000 OK.

Test nhanh
- Đăng nhập:  
  `curl -i -X POST http://IP:9000/login -H 'Content-Type: application/json' --data '{"username":"admin","password":"password"}'`
- Dùng cookie trả về để `GET /`.

---

## WP3 — WeApRous Tracker + REST (2.2 — pha khởi tạo)
Mục tiêu
- Xây REST endpoints: `/login` (nếu cần cookie), `/submit-info`, `/get-list` (lọc peer alive theo timeout).

Đầu vào
- start_sampleapp.py, daemon/weaprous.py (decorator @route), dictionary, httpadapter

Đầu ra
- Chạy trên 8000; nhận và trả JSON hợp lệ; lưu peer list in-memory.

DoD
- `POST /login` (tuỳ chọn) trả `Set-Cookie`.
- `POST /submit-info` nhận `{peer_id, ip, port, channels, ts}` → 200.
- `GET /get-list` trả danh sách peers có `now - ts <= 60s`.
- Log rõ ràng mỗi lần submit/get.

Test nhanh
- Lấy cookie (nếu yêu cầu) → submit-info 1–2 peers → get-list thấy đủ entries.

---

## WP4 — P2P Peer TCP + Channels (2.2 — pha P2P)
Mục tiêu
- Ứng dụng peer dùng socket TCP, hỗ trợ broadcast và direct; format message kiểu JSON Lines (`\n`).

Đầu vào
- File mới `p2p_peer.py` hoặc thư mục `p2p/`.

Đầu ra
- Khởi chạy nhiều peer (vd: A:7001, B:7002).
- A kết nối B; A broadcast → B nhận; A direct “ping” → B nhận.

DoD
- Thread listener (accept), 1 thread/connection (recv loop).
- Xử lý ghép mảnh gói (buffer + split theo `\n`), timeout, đóng kết nối sạch.
- In log mỗi message nhận được (channel/from/to/msg).

Test nhanh
- Khởi 2 tiến trình Python khác nhau; gọi `connect_to()`; `broadcast()`; `direct()`.

---

## WP5 — Tích hợp, Kiểm thử, Báo cáo
Mục tiêu
- Ghép Proxy ↔ Backend ↔ WeApRous; viết kịch bản demo đạt rubric; hoàn thiện báo cáo PEP8/PEP257.

Đầu vào
- Kết quả WP1–WP4

Đầu ra
- Kịch bản demo rõ ràng; script curl; ảnh chụp màn hình; báo cáo PDF/Markdown.

DoD
- Demo 3 phần:
  1) Cookies: 401 khi chưa login; login→Set‑Cookie; truy cập `/` thành công; clear cookie→401.
  2) Client–Server: submit-info ≥2 peers; get-list trả đúng.
  3) P2P: broadcast & direct hoạt động giữa ≥2 peers.
- Báo cáo: kiến trúc, API/protocol, sơ đồ luồng, kịch bản test, log minh chứng, lỗi & khắc phục, hướng mở.

Test nhanh
- Script `tests/curl_examples.sh` (WP5 chuẩn bị).
- Ảnh chụp: màn hình tương tự Hình 2–3 trong đề.

---

## MỐC THỜI GIAN (gợi ý 5 ngày làm)
- D1: WP1 + khung WP2/WP3 (khởi động được).
- D2: Hoàn tất WP2 (cookie); test incognito.
- D3: Hoàn tất WP3 (tracker); test submit/get-list.
- D4: Hoàn tất WP4 (P2P); test 2 peers.
- D5: WP5 tích hợp + quay demo + hoàn tất báo cáo.

---

## Phụ lục A — Mẫu “lời giao việc” ngắn gọn (copy/paste vào nhóm)

- [SV A] Proxy & LB (WP1)
  - Mục tiêu: proxy 8080 định tuyến host và round-robin 2 backend.
  - DoD: 10 request vào `app2.local` luân phiên 2 backend; có log “forwarded to …”.
  - Test: curl -H 'Host: app2.local' http://IP:8080/

- [SV B] Backend + Cookie (WP2)
  - Mục tiêu: POST /login (admin/password) → Set‑Cookie; GET / kiểm cookie.
  - DoD: Không cookie → 401; Có cookie → 200 index.html; header `Cache-Control: no-store`.

- [SV C] WeApRous Tracker (WP3)
  - Mục tiêu: /submit-info, /get-list (lọc peers alive).
  - DoD: Nộp JSON hợp lệ; get-list trả đủ peers mới đăng ký trong 60s.

- [SV D] P2P Peer (WP4)
  - Mục tiêu: TCP peer; broadcast/direct; JSON Lines.
  - DoD: 2 peers trò chuyện được; log rõ message nhận.

- [SV E] Tích hợp & Báo cáo (WP5)
  - Mục tiêu: ráp tất cả; viết test script; soạn báo cáo theo PEP8/PEP257.
  - DoD: Demo trọn rubric + nộp `assignment_STUDENTID.zip`.

---

## Phụ lục B — Definition of Done (checklist)
- Code theo PEP8, có docstring PEP257.
- Có log tối thiểu (time, method, path/status).
- Xử lý lỗi căn bản (400/401/404/500).
- Hướng dẫn chạy (README) + lệnh test.
- Ảnh chụp màn hình lưu trong `docs/images/`.
- Không dùng web framework ngoài sample.

---

## Phụ lục C — Rủi ro & Cách tránh
- Content-Length sai → treo: đọc đủ byte theo header, set socket timeout.
- Race condition LB/session → dùng lock cho biến chia sẻ.
- Cache trình duyệt → dùng Incognito + `Cache-Control: no-store`.
- Session backend khác (LB) → dùng token “stateless” (mỗi backend xác thực được).
- P2P: phân khung theo `\n`, giới hạn kích thước message, xử lý disconnect.
