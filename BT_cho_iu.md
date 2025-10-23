# ỨNG DỤNG CÔNG NGHỆ SỐ TRONG QUẢN LÝ SẢN XUẤT TẠI DOANH NGHIỆP VIỆT NAM – NGHIÊN CỨU TRƯỜNG HỢP VINAMILK

Tác giả: [Điền tên bạn]  
Lớp/Học phần: [Điền mã học phần]  
Ngày nộp: [Điền ngày]

---

## 0) Tóm tắt
Đề tài phân tích cách Vinamilk ứng dụng công nghệ số trong quản lý sản xuất (ERP–MES–SCADA/IoT, robot/AGV, WMS/TMS, LIMS/QMS, CMMS/EAM, EMS, phân tích dữ liệu) nhằm nâng cao hiệu quả vận hành, chất lượng, khả năng truy xuất và bền vững. Phương pháp nghiên cứu tình huống đơn (single-case study) với tam giác hóa dữ liệu (tài liệu công khai, báo cáo thường niên/bền vững, bài báo, whitepaper nhà cung cấp công nghệ, phỏng vấn chuyên gia nếu có) và phân tích theo khung trưởng thành Công nghiệp 4.0 (acatech I4.0 Maturity Index) kết hợp SCOR và KPI OEE. Kết quả kỳ vọng: mô tả kiến trúc công nghệ–quy trình–dữ liệu tại Vinamilk, lượng hóa tác động vận hành (OEE, lead time, yield, tồn kho, năng lượng), rút ra bài học triển khai cho doanh nghiệp sản xuất Việt Nam. [R1–R6]

Từ khóa: Industry 4.0, ERP, MES, SCADA/IoT, OEE, SCOR, acatech, Vinamilk.

---

## 1) Giới thiệu
- Khái niệm:
  - Quản lý công nghệ là việc hoạch định, lựa chọn, triển khai, vận hành và đánh giá các nguồn lực công nghệ nhằm đạt mục tiêu chiến lược của doanh nghiệp.
  - Chuyển đổi số sản xuất (Smart Manufacturing/Industry 4.0) là tích hợp liên thông hệ thống thông tin–tự động hóa–dữ liệu, giúp ra quyết định theo thời gian thực và tối ưu hóa toàn chuỗi giá trị.
- Lý do chọn đề tài:
  - Xu hướng Công nghiệp 4.0 tại Việt Nam tăng tốc theo Chương trình Chuyển đổi số quốc gia (Quyết định 749/QĐ-TTg). [R2–R3]
  - Ngành sữa đòi hỏi kiểm soát chất lượng–an toàn thực phẩm nghiêm ngặt, truy xuất nguồn gốc, tối ưu chuỗi lạnh; Vinamilk là doanh nghiệp dẫn dắt, có nhiều ứng dụng số hóa/robot hóa tiêu biểu (cần kiểm chứng từng minh chứng). [R1, R7–R9]
  - Nguồn dữ liệu công khai khá phong phú: website Vinamilk, báo cáo thường niên/bền vững, báo chí kinh tế–công nghiệp, case study nhà cung cấp (SAP, Tetra Pak, Siemens, ABB…), tài liệu chuẩn/khung (acatech, ISA‑95, SCOR, OEE). [R1, R4–R6]
- Mục tiêu:
  1) Mô tả kiến trúc và hành trình chuyển đổi số quản lý sản xuất tại Vinamilk.
  2) Đo lường tác động lên hiệu quả vận hành qua bộ KPI chuẩn (OEE, yield, lead time, tồn kho, tiêu thụ năng lượng).
  3) Đề xuất khuyến nghị/khung triển khai cho doanh nghiệp sản xuất Việt Nam.
- Câu hỏi nghiên cứu:
  - Vinamilk đã ứng dụng những công nghệ số nào trong quản lý sản xuất và tích hợp chúng ra sao với quy trình?
  - Tác động định lượng/định tính của các ứng dụng này lên vận hành, chất lượng, chi phí, bền vững?
  - Yếu tố thành công, rủi ro, và bài học khả chuyển cho doanh nghiệp khác?
- Phạm vi: Quản lý sản xuất (kế hoạch–lập lịch, thực thi, chất lượng, bảo trì, kho–vận chuyển nội bộ, năng lượng) tại một số nhà máy tiêu biểu; không đi sâu tài chính/marketing.
- Đóng góp:
  - Học thuật: kết hợp acatech I4.0 Maturity Index + SCOR + OEE để phân tích một case Việt Nam.
  - Thực tiễn: bộ KPI, lộ trình và checklist triển khai từ thí điểm đến mở rộng.

---

## 2) Tổng quan lý thuyết và khung phân tích
- Chu trình công nghệ và đường cong S (S‑Curve): mô tả vòng đời công nghệ từ hình thành đến bão hòa và tái thiết; hàm ý đổi mới liên tục để tránh suy giảm hiệu suất.
- Lý thuyết/các khung liên quan:
  - RBV (Resource‑Based View): công nghệ số và dữ liệu như năng lực lõi khó bắt chước, tạo lợi thế bền vững khi được “bọc” bởi quy trình và kỹ năng tổ chức.
  - TOE (Technology–Organization–Environment): phân tích yếu tố ảnh hưởng chấp nhận/triển khai (sẵn sàng công nghệ, cấu trúc–văn hóa tổ chức, áp lực cạnh tranh/quy định).
  - acatech Industrie 4.0 Maturity Index: 6 mức trưởng thành từ Computerization → Connectivity → Visibility → Transparency → Predictability → Adaptability, đánh giá trên các trụ cột (Processes, Resources, Information Systems, Organizational Structure, Culture, Compliance). [R4]
  - SCOR (Plan–Source–Make–Deliver–Return) để “gắn” công nghệ vào quy trình chuỗi cung ứng–sản xuất chuẩn hóa. [R5]
  - ISA‑95 (IEC 62264): tiêu chuẩn tích hợp ERP–MES–SCADA/PLC giữa IT/OT. [R6]
- Ngăn xếp công nghệ sản xuất (tham chiếu ISA‑95):
  - Tầng doanh nghiệp: ERP, PLM, BI.
  - Tầng điều hành: MES/MOM, LIMS/QMS, WMS/TMS, CMMS/EAM, EMS.
  - Tầng hiện trường: SCADA/DCS, PLC, cảm biến/IoT, robot/AGV/AMR.
  - Nền dữ liệu: Data Lake/Warehouse, Integration (API/ESB/MQ), AI/ML.
- KPI chuẩn:
  - OEE = Availability × Performance × Quality; FPY; yield; thời gian chu kỳ/đổi khuôn; phế phẩm; DOH (Days of Inventory on Hand); OTIF; kWh/tấn; độ chính xác dự báo; thời gian truy xuất lô.

---

## 3) Phương pháp nghiên cứu
- Thiết kế: Nghiên cứu tình huống đơn (Yin), phân tích dọc theo giai đoạn triển khai (as‑is → to‑be).
- Dữ liệu:
  - Thứ cấp: báo cáo thường niên/bền vững Vinamilk; tin IR; bài báo chính thống (Báo Công Thương, Vietstock, VnEconomy…); case study/whitepaper từ nhà cung cấp (SAP, Tetra Pak, Siemens/ABB/Schneider…); chính sách (749/QĐ‑TTg). [R1–R3, R7–R12]
  - Sơ cấp (nếu có): phỏng vấn bán cấu trúc (quản đốc nhà máy, IT/OT, QA, bảo trì), quan sát hiện trường.
- Quy trình:
  1) Rà soát tài liệu, lập timeline dự án/công nghệ.
  2) Bản đồ quy trình as‑is/to‑be theo SCOR/ISA‑95.
  3) Lập ma trận hệ thống–dữ liệu–KPI; xác định luồng tích hợp.
  4) Đánh giá trưởng thành theo acatech (định tính, chấm điểm thang 1–6).
  5) Phân tích KPI trước/sau (nếu có số liệu), hoặc so sánh chuẩn ngành.
- Phân tích:
  - Định tính: mã hóa chủ đề (integration, data quality, change management, ROI).
  - Định lượng: tính OEE, FPY, lead time, DOH, kWh/tấn; thống kê mô tả và chênh lệch.
- Đạo đức nghiên cứu: xin phép trích dẫn; ẩn danh phát ngôn; chỉ dùng số liệu công khai hoặc được phép; nêu rõ giả định/giới hạn.

---

## 4) Bối cảnh Vinamilk và chuỗi giá trị
- Tổng quan: Vinamilk là một trong các doanh nghiệp sữa lớn tại Việt Nam, có mạng lưới nhà máy, trang trại bò sữa, kho trung tâm và hệ thống phân phối rộng khắp. [R1]
- Chuỗi giá trị đặc thù:
  - Nguồn cung: sữa tươi từ trang trại/hộ nông dân; nguyên vật liệu phụ trợ (đường, bao bì, hương liệu…).
  - Sản xuất: tiêu chuẩn an toàn–chất lượng nghiêm ngặt (ISO 22000/FSSC 22000, HACCP…); nhiều công đoạn liên tục và ràng buộc nhiệt–vệ sinh.
  - Phân phối: chuỗi lạnh, hạn sử dụng, FEFO; kênh đa dạng (GT/MT/online).
- Thách thức vận hành: đồng bộ kế hoạch–lập lịch đa nhà máy, tối ưu chuỗi lạnh, kiểm soát chất lượng–truy xuất lô, tối ưu năng lượng và năng suất.

Ghi chú: Các thông tin định danh cụ thể (tên nhà máy, công nghệ tại từng nhà máy, mốc đầu tư) cần đối chiếu tài liệu IR và thông cáo báo chí của Vinamilk. [R1, R7–R9]

---

## 5) Kiến trúc công nghệ và quy trình số hóa (trường hợp Vinamilk)
Lưu ý: Liệt kê dưới đây mô tả kiến trúc đích (target) phổ biến cho ngành sữa và những gì “có khả năng” Vinamilk áp dụng; mọi nêu đích danh nền tảng/nhà cung cấp cần kiểm chứng từ nguồn chính thức. [Cần kiểm chứng]

- ERP
  - Chức năng: MRP/MPS, BOM/routing, mua sắm, kho, bán hàng, tài chính; quản trị master data (sản phẩm, công thức, định mức).
  - Tích hợp: đẩy lệnh sản xuất xuống MES; nhận báo cáo tiêu hao/ sản lượng; đồng bộ tồn kho với WMS; kế toán nguyên vật liệu/phế phẩm.
  - Ví dụ nền tảng phổ biến trong ngành: SAP S/4HANA, Oracle ERP, Microsoft Dynamics 365. [R10]
- MES/MOM
  - Lập lịch chi tiết (finite scheduling), dispatch, thu thập dữ liệu máy/line (SFC), quản lý chất lượng in‑line (SPC), genealogy/traceability, electronic batch record.
  - Tích hợp: lên xuống dữ liệu với ERP; kết nối SCADA/PLC qua OPC UA/MQTT; đồng bộ LIMS cho release lô.
  - Ví dụ: Siemens Opcenter, Rockwell FactoryTalk, AVEVA MES. [R11–R12]
- SCADA/PLC/IoT
  - Giám sát–điều khiển công đoạn (tiệt trùng UHT, đồng hóa, chiết rót, đóng gói), thu thập tín hiệu cảm biến (nhiệt, áp, lưu lượng, độ dẫn…), cảnh báo.
  - Giao thức: OPC UA, Modbus, MQTT; phân vùng mạng OT.
  - Ví dụ: Siemens/ABB/Schneider/Rockwell. [R11–R13]
- WMS/AGV/AMR và kho tự động
  - Quản lý vị trí kệ (AS/RS), FEFO, palletizing/depalletizing, cấp phát nội bộ bằng AGV/AMR; tích hợp nhãn/QR/GS1.
  - Ví dụ: Swisslog, SSI Schäfer, Dematic, Geek+. [R14–R16]
- LIMS/QMS
  - Quản trị chỉ tiêu kiểm nghiệm nguyên liệu–bán thành phẩm–thành phẩm; COA; CAPA; liên kết SPC/MES.
  - Ví dụ: Thermo Fisher SampleManager, LabWare. [R17]
- CMMS/EAM
  - Bảo trì định kỳ–dự đoán (CBM/PdM), MTBF/MTTR, tồn kho phụ tùng; tích hợp dữ liệu rung/điện từ IoT/SCADA.
  - Ví dụ: IBM Maximo, Infor EAM. [R18]
- EMS/Năng lượng
  - Đo lường kWh, hơi, khí nén, nước theo máy/line; tối ưu phụ tải/đỉnh; báo cáo kWh/tấn sản phẩm.
  - Ví dụ: Schneider EcoStruxure, Siemens Energy Manager. [R13]
- Nền dữ liệu & Phân tích
  - Data lake/warehouse; tích hợp qua API/ESB; BI dashboard (OEE, downtime Pareto, FPY, DOH, kWh/tấn); AI/ML cho dự báo nhu cầu, tối ưu lập lịch, phát hiện bất thường.

Quy trình số hóa theo SCOR:
1) Plan: dự báo nhu cầu → MPS/MRP trên ERP → kế hoạch tổng thể → finite scheduling trên MES.  
2) Source: đặt hàng NVL → tiếp nhận → LIMS kiểm nghiệm → nhập kho WMS/FEFO.  
3) Make: MES điều độ lệnh → SCADA/PLC vận hành; SPC in‑line; genealogy & eBR; báo cáo sản lượng/tiêu hao lên ERP.  
4) Deliver (nội bộ & ra thị trường): WMS điều phối kho; AGV/AMR cấp phát; tích hợp TMS cho phân phối.  
5) Return/Quality: truy xuất lô nhanh; CAPA; quản trị khiếu nại; tuân thủ tiêu chuẩn ATTP.  
6) Maintenance & Energy: CMMS/EAM quản trị bảo trì; EMS theo dõi năng lượng; phân tích dữ liệu để PdM/tiết kiệm năng lượng.

Sơ đồ tích hợp (mô tả văn bản):
- ERP <-> MES (đơn hàng–lệnh–báo cáo)  
- MES <-> SCADA/PLC/IoT (tag dữ liệu máy)  
- MES <-> LIMS (release chất lượng)  
- ERP <-> WMS (tồn kho/FEFO)  
- MES/SCADA/EMS/CMMS -> Data Lake/BI (dashboard KPI)  
- IAM/OT security bao quanh (phân vùng IT/OT, firewall, monitoring)

---

## 6) Phân tích tác động và đo lường

6.1 Phương pháp đo lường
- Thiết lập baseline KPI (trước triển khai) và đo sau triển khai từng giai đoạn.
- Nguồn dữ liệu: dashboard MES/BI, báo cáo năng lượng, báo cáo chất lượng, kho WMS, ERP.
- KPI chính và công thức (tóm tắt):
  - OEE = Availability × Performance × Quality (Phụ lục A).
  - FPY, yield, tỷ lệ phế phẩm; lead time sản xuất; DOH; OTIF; kWh/tấn; thời gian truy xuất lô.

6.2 Kết quả minh họa (giả định – cần kiểm chứng bằng số liệu công khai hoặc nội bộ)
Bảng 1. Tác động vận hành ở một dây chuyền tiêu biểu sau 6–12 tháng triển khai MES+SCADA+WMS+LIMS.  
Lưu ý: số liệu dưới đây là ví dụ minh họa để trình bày phương pháp, KHÔNG phải số liệu chính thức của Vinamilk. [Cần kiểm chứng]

- OEE: 68% → 78% (+10 điểm phần trăm)  
- Availability: 85% → 90% (giảm downtime không kế hoạch nhờ PdM và giảm thời gian đổi khuôn)  
- Performance: 80% → 88% (tối ưu lập lịch/giảm micro‑stoppages)  
- Quality: 99.0% → 99.3% (SPC in‑line, kiểm soát tham số quy trình)  
- FPY: 95% → 97%  
- Lead time sản xuất (đơn hàng→thành phẩm): giảm 15–25%  
- DOH: giảm 10–15% nhờ đồng bộ kế hoạch–sản xuất–kho  
- kWh/tấn: giảm 5–8% nhờ EMS và tối ưu phụ tải/đỉnh  
- Thời gian truy xuất lô: từ vài giờ → vài phút (genealogy eBR)  
- NCR/cảnh báo chất lượng: giảm 10–20%

6.3 Diễn giải tác động
- Năng suất & ổn định: dữ liệu thời gian thực giúp xác định Pareto downtime, lập lịch sát năng lực, giảm micro‑stoppages.
- Chất lượng & tuân thủ: SPC in‑line và eBR giảm sai lệch, nâng xác suất đạt chuẩn ngay lần đầu (FPY), rút ngắn truy xuất lô khi cần.
- Vốn lưu động & logistics: WMS/FEFO và đồng bộ ERP–MES giúp tối ưu tồn kho, tăng OTIF.
- Năng lượng & bền vững: đo lường chi tiết theo máy/line giúp phát hiện bất thường tiêu thụ, tối ưu quy trình CIP/tiệt trùng và phụ tải.

6.4 Đánh giá trưởng thành số (acatech – minh họa định tính)
- Processes: Visibility → Transparency (dashboard theo ca/line, phân tích nguyên nhân gốc)  
- Resources: Connectivity → Visibility (máy/line kết nối OPC UA, dữ liệu cảm biến hợp nhất)  
- Information Systems: Connectivity → Transparency (ERP–MES–SCADA–LIMS–WMS tích hợp)  
- Organization: Computerization → Visibility (vai trò Data/OT, cơ chế ra quyết định dựa dữ liệu)  
- Culture: Computerization → Connectivity (đào tạo OEE/Kaizen số)  
- Compliance: Visibility (truy xuất lô, eBR hỗ trợ audit)

[Cần bảng chấm điểm chi tiết khi có dữ liệu phỏng vấn/quan sát]

---

## 7) Thảo luận: yếu tố thành công, rủi ro, khả chuyển

7.1 Yếu tố thành công
- Cam kết lãnh đạo và điều phối IT/OT; kiến trúc tích hợp theo chuẩn ISA‑95.
- Quản trị dữ liệu: chuẩn hóa master data, từ điển dữ liệu, trách nhiệm chất lượng dữ liệu.
- Lộ trình theo giai đoạn: thí điểm (quick wins) → chuẩn hóa → mở rộng; song hành quản lý thay đổi và đào tạo.
- Lựa chọn nền tảng mở (API/OPC UA), tránh khóa chặt nhà cung cấp.

7.2 Rủi ro/Thách thức
- Tích hợp hệ thống legacy; chất lượng/độ sẵn sàng dữ liệu; bảo mật OT (phân vùng, patching, giám sát).
- ROI kéo dài nếu mục tiêu kinh doanh không được “gắn KPI” rõ ràng hoặc phạm vi dàn trải.
- Thiếu năng lực vận hành phân tích dữ liệu và năng lực DevOps/OTSec tại chỗ.

7.3 Khả chuyển cho DN Việt Nam
- Bắt đầu từ bài toán rõ giá trị: OEE/downtime, truy xuất lô, kho FEFO, năng lượng.
- Chuẩn hóa dữ liệu–quy trình trước khi “đổ” AI/ML.
- Thiết lập CoE số nhỏ gọn (process + data + OT) và cơ chế “value backlog”.

---

## 8) Khuyến nghị và lộ trình

Ngắn hạn (0–6 tháng)
- Đánh giá nhanh trưởng thành số theo acatech; lập baseline KPI.  
- PoC tại 1–2 dây chuyền: MES‑OEE, EMS, WMS; tích hợp dữ liệu tối thiểu (ISA‑95).  
- Thiết lập quản trị dữ liệu (data owner, tiêu chuẩn master data, từ điển KPI).  
- Bảo mật OT nền tảng: phân vùng IT/OT, quản lý truy cập, sao lưu/khôi phục.

Trung hạn (6–18 tháng)
- Mở rộng MES (scheduling, SPC, genealogy) + LIMS + CMMS; triển khai WMS/AGV tại kho trọng điểm.  
- Dựng data lake + BI hợp nhất; dashboard thời gian thực OEE/FPY/DOH/kWh/tấn.  
- Chuẩn hóa interface API/ESB; quy trình change control IT/OT; đào tạo vai trò Data/OT.

Dài hạn (18–36 tháng)
- AI/ML: tối ưu lập lịch đa ràng buộc; PdM trên rung/âm thanh/dòng điện; phát hiện bất thường quy trình.  
- Digital twin quy trình/line trọng yếu; tối ưu năng lượng theo thời gian thực.  
- Mở rộng quản trị giá trị (value management): backlog các sáng kiến số với KPI/ROI.

---

## 9) Kết luận
Nghiên cứu cung cấp khung toàn diện để phân tích ứng dụng công nghệ số trong quản lý sản xuất của Vinamilk, kết hợp acatech–SCOR–ISA‑95 và bộ KPI OEE/FPY/lead time/DOH/kWh. Kết quả minh họa cho thấy tiềm năng cải thiện đáng kể hiệu quả vận hành, chất lượng, truy xuất và bền vững nếu triển khai tích hợp theo lộ trình và quản trị dữ liệu–thay đổi bài bản. Các khuyến nghị có thể khả chuyển cho nhiều doanh nghiệp sản xuất Việt Nam ở các mức trưởng thành khác nhau.

---

## 10) Tài liệu tham khảo
[R1] Vinamilk – Trang chủ/IR/Newsroom: [vinamilk.com.vn](https://www.vinamilk.com.vn)  
[R2] Cổng TT Chính phủ – Chương trình Chuyển đổi số quốc gia (Quyết định 749/QĐ‑TTg): [chinhphu.vn](https://chinhphu.vn)  
[R3] Bộ TT&TT – Tài liệu chuyển đổi số: [mic.gov.vn](https://mic.gov.vn)  
[R4] acatech – Industrie 4.0 Maturity Index: [en.acatech.de/publication/industrie-4-0-maturity-index](https://en.acatech.de/publication/industrie-4-0-maturity-index/)  
[R5] ASCM/APICS – SCOR Digital Standard: [ascm.org/learning-development/scor-digital-standard](https://www.ascm.org/learning-development/scor-digital-standard/)  
[R6] ISA – ISA‑95/IEC 62264: [isa.org/standards-and-publications/isa-standards/isa-95](https://www.isa.org/standards-and-publications/isa-standards/isa-95)  
[R7] Vietstock/CafeF – Hồ sơ mã VNM, công bố thông tin: [finance.vietstock.vn/VNM.htm](https://finance.vietstock.vn/VNM.htm), [cafef.vn](https://cafef.vn)  
[R8] Báo Công Thương – Tin doanh nghiệp/ngành: [congthuong.vn](https://congthuong.vn)  
[R9] VnEconomy/VIR – Bối cảnh thị trường ngành sữa: [vneconomy.vn](https://vneconomy.vn), [vir.com.vn](https://vir.com.vn)  
[R10] SAP – Customer Stories (F&B): [sap.com/about/customer-stories](https://www.sap.com/about/customer-stories.html)  
[R11] Siemens – Opcenter/MES case resources: [plm.automation.siemens.com](https://www.plm.automation.siemens.com/global/en/resources/)  
[R12] Rockwell Automation – Case Studies: [rockwellautomation.com/.../case-studies](https://www.rockwellautomation.com/en-us/company/news/case-studies.html)  
[R13] Schneider Electric – EcoStruxure F&B: [se.com/.../food-and-beverage](https://www.se.com/ww/en/work/solutions/for-business/food-and-beverage/)  
[R14] Swisslog – Warehousing automation: [swisslog.com](https://www.swisslog.com/)  
[R15] SSI Schäfer – Case Studies: [ssi-schaefer.com](https://www.ssi-schaefer.com/)  
[R16] Dematic – Customer stories: [dematic.com](https://www.dematic.com/)  
[R17] Thermo Fisher – LIMS: [thermofisher.com/.../information-management-systems.html](https://www.thermofisher.com/labsolutions/information-management-systems.html)  
[R18] IBM Maximo – EAM/CMMS: [ibm.com/products/maximo](https://www.ibm.com/products/maximo)  
[R19] Vorne – What is OEE?: [vorne.com/what-is-oee](https://www.vorne.com/what-is-oee/)

Ghi chú: Khi trích dẫn thông tin cụ thể về Vinamilk (tên hệ thống, năm triển khai, số liệu KPI), bắt buộc dẫn nguồn nguyên bản (báo cáo/IR/thông cáo/tài liệu nhà cung cấp).

---

## 11) Phụ lục

### Phụ lục A. Bộ KPI và công thức
- OEE = Availability × Performance × Quality
  - Availability = Run Time / Planned Production Time
  - Performance = (Ideal Cycle Time × Total Count) / Run Time
  - Quality = Good Count / Total Count
- FPY = (Sản lượng đạt ngay lần đầu) / (Tổng sản lượng)
- DOH = (Tồn kho bình quân / Giá vốn hàng bán bình quân ngày)
- OTIF = (Đơn giao đúng hạn và đủ) / (Tổng đơn)
- kWh/tấn = (Tổng năng lượng tiêu thụ) / (Tổng sản lượng quy đổi tấn)

### Phụ lục B. Bảng câu hỏi phỏng vấn bán cấu trúc (gợi ý)
- Chiến lược số hóa sản xuất của đơn vị là gì? Ưu tiên giá trị nào (OEE, chất lượng, năng lượng, an toàn)?
- Kiến trúc hệ thống hiện tại (ERP–MES–SCADA–WMS–LIMS–CMMS–EMS)? Cổng tích hợp?
- Quy trình quản trị dữ liệu/KPI? Ai chịu trách nhiệm master data, chất lượng dữ liệu?
- Các dự án đã/đang triển khai? Kết quả định lượng? Khó khăn lớn nhất?
- An ninh OT được tổ chức ra sao? Phân vùng mạng, IAM, backup, monitoring?
- Nhu cầu năng lực/đào tạo nào để vận hành mô hình mới?

### Phụ lục C. Checklist đánh giá trưởng thành số (acatech – rút gọn)
- Processes: mức đo lường theo thời gian thực? có phân tích nguyên nhân gốc?  
- Resources: thiết bị/máy kết nối mức nào? tỷ lệ line thu thập dữ liệu tự động?  
- Information Systems: tích hợp ERP–MES–SCADA–LIMS–WMS mức nào (batch/order/real‑time)?  
- Organization: quy trình ra quyết định dựa dữ liệu? vai trò Data/OT?  
- Culture: đào tạo kỹ năng số? cơ chế cải tiến liên tục?  
- Compliance: truy xuất lô, eBR, đáp ứng audit/tiêu chuẩn FSSC 22000?

Đánh giá thang 1–6 theo acatech; ghi minh chứng/tài liệu đính kèm cho từng mục.

### Phụ lục D. Ma trận rủi ro an ninh OT (ví dụ)
- Rủi ro: xâm nhập mạng OT → Tác động: ngừng line → Kiểm soát: phân vùng mạng, firewall, MFA, patching, monitoring.  
- Rủi ro: dữ liệu sai lệch → Tác động: quyết định sai → Kiểm soát: chuẩn hóa master data, validation, data lineage, kiểm soát thay đổi.  
- Rủi ro: khóa nhà cung cấp → Tác động: chi phí, chậm mở rộng → Kiểm soát: tiêu chuẩn mở (OPC UA, API), điều khoản hợp đồng.

### Phụ lục E. Mẫu “Data & KPI Inventory” (trích yếu)
- Thiết bị/line → Tag/biến đo → Nguồn (SCADA/PLC) → Tần suất → Hệ thống đích (MES/BI) → KPI sử dụng → Chủ dữ liệu.

---

## 12) Kế hoạch thực hiện (gợi ý)
- Tuần 1–2: rà soát tài liệu, chốt khung phân tích, xác định KPI–dữ liệu cần.
- Tuần 3–4: thu thập dữ liệu thứ cấp, liên hệ phỏng vấn (nếu có), lập bản đồ quy trình kiến trúc.
- Tuần 5–6: phân tích KPI, đánh giá trưởng thành số hóa, viết kết quả.
- Tuần 7: thảo luận–khuyến nghị, hoàn thiện phụ lục.
- Tuần 8: biên tập, chuẩn hóa trích dẫn (APA/IEEE), kiểm chứng số liệu.

---

## 13) Giới hạn nghiên cứu
- Thiếu số liệu công khai chi tiết có thể buộc dùng giả định minh họa; cần thay bằng số liệu thực khi có.
- Không khảo sát trực tiếp toàn bộ nhà máy/kho (nếu không được phép), vì vậy mức chấm acatech chỉ mang tính định tính và phụ thuộc tài liệu/phỏng vấn.

---

## 14) Gợi ý cách cập nhật bản thảo này
- Điền thông tin lớp/tác giả/ngày nộp.
- Thay thế các đoạn [Cần kiểm chứng] bằng dẫn liệu cụ thể từ [R1] và nguồn IR/nhà cung cấp.
- Thêm biểu đồ/table KPI thực tế (nếu thu thập được).
- Chuẩn hóa mục Tài liệu tham khảo theo chuẩn trích dẫn yêu cầu (ví dụ APA).
