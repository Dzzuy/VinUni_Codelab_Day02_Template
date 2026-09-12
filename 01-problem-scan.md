# 01 — Problem Scan & Quick Cards

## Bối cảnh

Nhóm chọn bài toán vận hành trong hệ sinh thái Vin Smart Future. Các số liệu có chữ **baseline giả định** chỉ dùng để thiết kế prototype và cần được xác minh bằng log thực tế trước pilot.

## Scan: 5 cơ hội vận hành

| # | Subsidiary | Lens | Mô tả bài toán |
|---|---|---|---|
| 1 | Xanh SM | Lặp lại | Điều phối viên xử lý thủ công các báo cáo xe hết pin hoặc gặp sự cố sạc giữa đường. |
| 2 | Vinhomes | Tốn thời gian | Nhân viên phân loại và soạn phản hồi cho khiếu nại cư dân trên ứng dụng. |
| 3 | VinFast | Lặp lại | Nhân viên đối chiếu hóa đơn sạc với dữ liệu trạm và xe theo chu kỳ. |
| 4 | Vinmec | Tốn thời gian | Bác sĩ soạn tóm tắt hồ sơ xuất viện từ nhiều ghi chú lâm sàng. |
| 5 | Vinpearl | AI-upgrade | Nhân viên CSKH trả lời thủ công câu hỏi về phòng, vé và dịch vụ vui chơi. |

## Quick Problem Card #1 — Xanh SM: Sự cố pin thực địa

- **Bài toán:** Điều phối viên cần tìm phương án sạc an toàn và soạn hướng dẫn cho tài xế khi xe hết pin giữa đường.
- **Actor:** Tài xế, điều phối viên và đội cứu hộ.
- **Workflow:** Tài xế báo sự cố -> xác minh biển số/vị trí -> tra cứu trạm tương thích -> soạn hướng dẫn -> điều phối viên duyệt và gửi.
- **Bottleneck:** Tra cứu trạm và soạn tin, khoảng 10 phút/lượt theo baseline giả định.
- **AI hỗ trợ:** Tóm tắt dữ liệu sự cố và tạo bản nháp hướng dẫn.
- **Metric:** Giảm thời gian xử lý từ 15 phút xuống dưới 3 phút/lượt; đạt ít nhất 98% địa điểm và loại cổng sạc đúng.
- **Architecture:** LLM Feature kết hợp Rule/State Machine; không dùng Agent tự trị.

## Quick Problem Card #2 — Vinhomes: Phân loại khiếu nại cư dân

- **Bài toán:** Tự động phân loại khiếu nại và tạo bản nháp phản hồi cho nhân viên CSKH.
- **Actor:** Cư dân, nhân viên CSKH và bộ phận vận hành tòa nhà.
- **Workflow:** Cư dân gửi ticket -> CSKH đọc và phân loại -> chuyển bộ phận phụ trách -> soạn phản hồi -> trưởng nhóm duyệt case nhạy cảm.
- **Bottleneck:** Đọc, phân loại và chuyển ticket, khoảng 8 phút/ticket theo baseline giả định.
- **AI hỗ trợ:** Phân loại chủ đề, trích xuất mức độ ưu tiên và tạo bản nháp có dẫn nguồn chính sách.
- **Metric:** 85% ticket được phân loại dưới 10 giây; thời gian soạn phản hồi dưới 2 phút; 100% case pháp lý/tài chính qua người duyệt.
- **Architecture:** LLM Feature kết hợp Rule router.

## Quick Problem Card #3 — Vinmec: Tóm tắt hồ sơ xuất viện

- **Bài toán:** Tạo bản nháp tóm tắt xuất viện từ hồ sơ đã được xác thực.
- **Actor:** Bác sĩ điều trị và nhân viên hồ sơ bệnh án.
- **Workflow:** Hoàn tất ghi chú -> gom kết quả xét nghiệm/thuốc -> bác sĩ đọc lại -> viết tóm tắt -> ký duyệt và lưu hồ sơ.
- **Bottleneck:** Gom thông tin và viết bản nháp, khoảng 20 phút/bệnh nhân theo baseline giả định.
- **AI hỗ trợ:** Trích xuất thông tin có cấu trúc và tạo bản nháp; không tự chẩn đoán hoặc ký hồ sơ.
- **Metric:** Giảm thời gian soạn nháp xuống dưới 5 phút; 100% bản nháp được bác sĩ duyệt.
- **Architecture:** LLM Feature có trích xuất cấu trúc và HITL bắt buộc.

## Lựa chọn

Nhóm chọn **Card #1 — Xanh SM: Sự cố pin thực địa** cho Deep-Dive vì đây là quy trình thời gian thực, có bottleneck rõ và có thể kiểm soát rủi ro bằng Rule gate, HITL và fallback thủ công.

- Vinhomes cần Rule router và nguồn chính sách có version trước khi pilot vì có rủi ro về phí và tranh chấp.
- Đối chiếu hóa đơn VinFast chủ yếu là dữ liệu có cấu trúc, nên Rule/SQL phù hợp hơn LLM ở giai đoạn đầu.
- Tóm tắt hồ sơ Vinmec có rủi ro dữ liệu y tế cao, chỉ nên tạo bản nháp trong môi trường được kiểm soát và bắt buộc bác sĩ ký duyệt.
