```mermaid
flowchart LR
  U[User (Internet/Phone)] -->|HTTPS| G(Ngrok Cloud)
  G -->|Secure tunnel| N(NGINX on Laptop : :80)
  N -->|Serve static| W[Web /]
  N -->|Proxy /api/*| B[Flask API 127.0.0.1:5000]
```
