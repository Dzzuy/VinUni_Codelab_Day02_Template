# 02 - Deep-Dive Report: Xanh SM Battery Incident Copilot

## 1. Current-State Workflow

**Tình huống:** Tài xế báo xe sắp hết pin ngoài đường.

`Tài xế gọi tổng đài (2 phút) -> Handoff: dispatcher ghi biển số, pin, vị trí (2 phút) -> Handoff: tra GPS trên bản đồ nội bộ (2 phút) -> Bottleneck: tìm trạm còn chỗ và đúng loại cổng (5 phút) -> Bottleneck: soạn hướng dẫn hoặc gọi cứu hộ (4 phút) -> dispatcher duyệt và gửi.`

**Tổng thời gian baseline:** 15 phút/lượt. Đây là ước tính cần xác nhận bằng log thực tế.

## 2. Problem Statement - 6 Fields

| Field | Nội dung |
|---|---|
| **Actor / Operator** | Điều phối viên trung tâm vận hành Xanh SM; tài xế cung cấp dữ liệu sự cố. |
| **Current Workflow** | Dispatcher nhận cuộc gọi, ghi biển số/pin/vị trí, tra bản đồ và dashboard trạm sạc, sau đó soạn hướng dẫn hoặc gọi cứu hộ. |
| **Bottleneck** | Tra trạm còn chỗ, đúng loại cổng và trong tầm pin; sau đó viết tin nhắn rõ ràng cho tài xế. Hai bước chiếm khoảng 9 phút. |
| **Business Impact** | Giả định 80 sự cố/ngày và 15 phút/lượt tương đương 20 giờ công/ngày. Tài xế chờ lâu làm giảm thời gian nhận chuyến và tăng nguy cơ xe dừng giữa đường. Cần đối chiếu bằng log. |
| **Success Metric** | Giảm thời gian xử lý trung bình từ 15 xuống dưới 3 phút; 98% draft chọn đúng trạm tương thích; 100% tin nhắn gửi ra có duyệt của dispatcher. |
| **Operational Boundary** | AI chỉ đọc dữ liệu đã cung cấp, đề xuất và soạn draft. AI không được tự gửi tin, tự điều xe cứu hộ, bịa vị trí/trạm, hoặc đề xuất trạm xa hơn 5 km khi pin dưới 5%. Thiếu dữ liệu hoặc confidence thấp thì chuyển người xử lý thủ công. |

## 3. AI Fit & Future-State Flow

### AI-Fit Matrix

- **Rule / State Machine:** Xử lý ngưỡng pin, khoảng cách, loại cổng, schema và quyền gửi.
- **LLM Feature:** Trích xuất thông tin từ cuộc gọi và viết draft tiếng Việt tự nhiên.
- **Agentic Loop:** Không chọn. Agent tự gọi nhiều API hoặc tự điều phối sẽ tăng rủi ro trong tình huống an toàn.

### Future-State Flow

`Nhận báo cáo -> lấy dữ liệu có cấu trúc -> Rule kiểm tra pin/khoảng cách/tương thích -> LLM tạo JSON và draft -> Human-in-the-loop kiểm tra/sửa -> dispatcher bấm gửi.`

- **AI Step:** Trích xuất dữ liệu và soạn draft theo JSON schema.
- **Human Step:** Dispatcher kiểm tra biển số, pin, vị trí, trạm và nội dung trước khi gửi.
- **Critical battery:** Nếu pin dưới 5%, action bắt buộc là `dispatch_mobile_charger`; không đề xuất trạm xa hơn 5 km.
- **Fallback:** JSON sai schema, thiếu GPS, thiếu trạng thái trạm hoặc confidence thấp thì không gửi; hiển thị cảnh báo và quay về quy trình thủ công.

## 4. Evaluate

| Tiêu chí | Đánh giá |
|---|---|
| Dữ liệu mẫu/log sạch | **Chưa đủ.** Cần log sự cố và trạng thái trạm đã ẩn dữ liệu nhạy cảm. |
| Rủi ro có kiểm soát | **Có.** AI chỉ tạo draft; Rule chặn ngưỡng an toàn và dispatcher duyệt trước khi gửi. |
| Stakeholder sẵn sàng | **Chưa xác nhận.** Cần thử nghiệm với một ca trực và lấy feedback dispatcher. |

### Quyết định: NOT YET

Chưa nên triển khai rộng vì các số liệu 80 sự cố/ngày, 15 phút/lượt và độ chính xác 98% chưa có baseline chính thức. Tuy vậy, bài toán phù hợp để làm prototype hẹp: Rule + LLM Feature đủ đáp ứng nhu cầu, không cần Agent tự trị. Bước tiếp theo là thu thập dữ liệu đã ẩn danh, đo baseline trong một ca trực và kiểm thử 50-100 case. Sau đó mới quyết định GO.
