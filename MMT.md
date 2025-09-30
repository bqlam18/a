```mermaid
graph TD
    subgraph "Nhà Cung Cấp Dịch Vụ (ISP)"
        Internet("🌐 Internet (Fiber)")
    end

    subgraph "Tầng Trệt (Ground Floor) - Trung tâm mạng"
        ONT["🏢 Modem/ONT của ISP"]
        Router["📡 <b>Router Chính</b><br>ASUS RT-AX86U (AiMesh Controller)"]
        Switch["🔌 <b>Switch</b><br>TP-Link TL-SG108"]
        NAS["🗄️ <b>Máy chủ NAS</b><br>(Lưu trữ đám mây riêng)"]
        WiredDevices["💻 TV / PC / Console"]
        UPS["🔋 <b>Bộ lưu điện UPS</b><br>APC 1500VA"]
    end

    subgraph "Các Tầng Trên (Upper Floors) - Mở rộng sóng"
        MeshNode1["🛰️ <b>Mesh Node 1 (Tầng 1)</b><br>ASUS ZenWiFi XT8"]
        MeshNode2["🛰️ <b>Mesh Node 2 (Tầng 2)</b><br>ASUS ZenWiFi XT8"]
        WiFi_UpperFloors("📶 Wi-Fi phủ sóng Tầng 1, 2, 3")
    end

    %% --- Kết Nối Chính ---
    Internet -->|Cáp Quang| ONT
    ONT -->|Cáp Ethernet (WAN 2.5G)| Router
    
    %% --- Bảo Vệ Nguồn Điện ---
    Router & Switch & NAS --> UPS

    %% --- Mạng Có Dây (LAN) Tại Tầng Trệt ---
    Router -->|LAN Gigabit| Switch
    Switch -->|LAN Gigabit<br><i>(Link Aggregation tùy chọn)</i>| NAS
    Switch -->|LAN Gigabit| WiredDevices

    %% --- Mạng Không Dây (Wi-Fi) & MESH ---
    Router -- Wi-Fi --> id1("📱 Thiết bị Wi-Fi Tầng Trệt")
    Router -.->|<b>Backhaul không dây 5GHz-2</b><br><i>(hoặc Ethernet có dây)</i>| MeshNode1
    MeshNode1 -.->|<b>Backhaul không dây 5GHz-2</b>| MeshNode2
    
    MeshNode1 -- Wi-Fi --> WiFi_UpperFloors
    MeshNode2 -- Wi-Fi --> WiFi_UpperFloors

    %% --- Chú thích & Kiểu dáng ---
    classDef default fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef mesh fill:#e6f3ff,stroke:#0066cc;
    classDef core fill:#fff5e6,stroke:#ff9900;
    classDef devices fill:#e6ffed,stroke:#009933;

    class Router,Switch,NAS core;
    class MeshNode1,MeshNode2 mesh;
    class WiredDevices,id1,WiFi_UpperFloors devices;
```
