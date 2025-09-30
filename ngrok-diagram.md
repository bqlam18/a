````markdown
```mermaid
graph LR
    subgraph Internet
        User[Người dùng]
        NgrokCloud[Ngrok Cloud Service]
    end
    subgraph "Máy tính của bạn (Local)"
        WebApp[Web Server cục bộ <br> (localhost:8000)]
        NgrokAgent[Ngrok Agent]
    end

    User -- "1. Gửi request đến<br>https://...ngrok-free.app" --> NgrokCloud
    NgrokCloud -- "2. Chuyển tiếp request<br>qua đường hầm an toàn" --> NgrokAgent
    NgrokAgent -- "3. Gửi request đến<br>server cục bộ" --> WebApp
    WebApp -- "4. Trả về response" --> NgrokAgent
    NgrokAgent -- "5. Chuyển tiếp response<br>ra ngoài" --> NgrokCloud
    NgrokCloud -- "6. Gửi response<br>cho người dùng" --> User
```
````