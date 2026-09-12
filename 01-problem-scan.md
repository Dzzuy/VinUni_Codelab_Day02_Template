# 01 - Problem Scan & Quick Assess

## Bối cảnh

Nhóm đóng vai AI Product Engineer tại Vin Smart Future. Các ý tưởng dưới đây được quét qua bốn lens: lặp lại, tốn thời gian, AI có thể tốt hơn và pain từ stakeholder.

## Phase 1 - Scan

| # | Công ty | Lens | Bài toán / bottleneck |
|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên xử lý thủ công báo cáo xe sắp hết pin: tra GPS, tìm trạm sạc phù hợp và soạn hướng dẫn cho tài xế. |
| 2 | VinFast | Lặp lại | Nhân viên đối soát hóa đơn sạc từ nhiều trạm đối tác với dữ liệu giao dịch nội bộ mỗi tuần. |
| 3 | Vinhomes | AI-upgrade | Phân loại phản ánh cư dân theo tòa nhà, loại sự cố và mức độ khẩn cấp trước khi chuyển ban quản lý. |
| 4 | Vinmec | Tốn thời gian | Bác sĩ soạn bản nháp tóm tắt xuất viện từ bệnh án, kết quả xét nghiệm và ghi chú điều trị. |
| 5 | Vinpearl | Pain từ stakeholder | Quản lý đọc và gom các review tiêu cực để phát hiện nhanh vấn đề phòng, vệ sinh hoặc thái độ phục vụ. |

## Phase 2 - Quick Problem Cards

### Card #1 - Xanh SM xử lý sự cố pin

- **Bài toán:** Tài xế báo pin dưới mức an toàn, điều phối viên phải tìm phương án sạc/cứu hộ và gửi hướng dẫn.
- **Actor:** Tài xế và điều phối viên trung tâm vận hành.
- **Workflow:** Nhận cuộc gọi -> tra GPS và pin -> tìm trạm còn chỗ/phù hợp -> soạn hướng dẫn -> điều phối viên duyệt và gửi.
- **Bottleneck:** Tra cứu trạm và soạn tin, khoảng 10 phút/lượt trong tổng 15 phút.
- **AI hỗ trợ:** Trích xuất thông tin từ cuộc gọi và tạo bản nháp hướng dẫn.
- **Metric:** Giảm thời gian xử lý từ 15 phút xuống dưới 3 phút; 98% nháp chọn đúng loại trạm.
- **Architecture:** Rule kiểm tra an toàn + LLM Feature tạo draft; không dùng Agent tự trị.

### Card #2 - Vinhomes phân loại phản ánh cư dân

- **Bài toán:** Phản ánh tự do trên ứng dụng cần được phân loại và chuyển đúng ban quản lý.
- **Actor:** Nhân viên CSKH và ban quản lý tòa nhà.
- **Workflow:** Đọc phản ánh -> gán nhóm vấn đề -> tìm tòa/ban phụ trách -> nhập ticket -> theo dõi SLA.
- **Bottleneck:** Đọc và gán nhãn thủ công, khoảng 6 phút/ticket.
- **AI hỗ trợ:** Phân loại chủ đề, tòa nhà và mức độ khẩn cấp; nhân viên duyệt trước khi tạo ticket.
- **Metric:** 90% ticket được phân loại dưới 30 giây; giảm lỗi route từ 12% xuống dưới 3%.
- **Architecture:** Rule cho tuyến cố định + LLM Feature cho nội dung tự do.

### Card #3 - Vinmec soạn tóm tắt xuất viện

- **Bài toán:** Bác sĩ mất thời gian gom thông tin từ hồ sơ để soạn bản nháp dễ hiểu cho bệnh nhân.
- **Actor:** Bác sĩ điều trị và điều dưỡng phụ trách hồ sơ.
- **Workflow:** Mở bệnh án -> đọc xét nghiệm/ghi chú -> viết chẩn đoán và thuốc -> kiểm tra -> ký duyệt.
- **Bottleneck:** Đọc nhiều nguồn và viết bản nháp, khoảng 20-30 phút/bệnh nhân.
- **AI hỗ trợ:** Trích xuất và tóm tắt thông tin có trích dẫn nguồn trong hồ sơ.
- **Metric:** Giảm thời gian soạn nháp từ 25 xuống 10 phút; 100% bản cuối do bác sĩ duyệt.
- **Architecture:** LLM Feature có trích dẫn nguồn và Human-in-the-loop.

## Lựa chọn

Nhóm chọn Card #1 để deep-dive vì đây là tình huống vận hành có bottleneck rõ, metric đo được và có thể giới hạn AI ở vai trò tạo draft. Hai card còn lại cần thêm dữ liệu nghiệp vụ và kiểm soát rủi ro riêng trước khi triển khai.

> Các con số trong tài liệu là baseline giả định để thiết kế prototype, cần xác minh bằng log vận hành trước khi ra quyết định đầu tư.
