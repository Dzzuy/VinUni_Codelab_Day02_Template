# 02 — Deep-Dive Report

## Use case

**Xanh SM — Hỗ trợ điều phối viên xử lý sự cố pin thực địa.** Các số liệu có chữ **baseline giả định** cần được xác minh bằng log vận hành trước pilot.

## 1. Current-State Workflow

1. Tài xế gọi tổng đài và mô tả sự cố: **2 phút**.
2. Điều phối viên xác minh biển số, loại xe, mức pin và GPS: **2 phút**, handoff từ tài xế.
3. Điều phối viên tra cứu bản đồ nội bộ và dashboard trạm sạc để tìm trạm còn chỗ, đúng chuẩn cổng và trong tầm di chuyển: **5 phút**, bottleneck.
4. Điều phối viên tự viết hướng dẫn đường đi hoặc gọi đội cứu hộ: **5 phút**, bottleneck.
5. Điều phối viên đọc lại, gửi cho tài xế và ghi log: **1 phút**, handoff sang tài xế/đội cứu hộ.

**Tổng baseline giả định: 15 phút/lượt.** Cần đo tối thiểu 2 tuần log thực tế.

## 2. Problem Statement — 6 fields

| Field | Nội dung |
|---|---|
| **Actor / Operator** | Điều phối viên trung tâm vận hành Xanh SM; đầu vào đến từ tài xế và hệ thống định vị. |
| **Current Workflow** | Điều phối viên nhận cuộc gọi, xác minh thông tin trên bản đồ nội bộ, tra cứu dashboard trạm sạc, viết hướng dẫn và gửi sau khi kiểm tra. Baseline giả định là 15 phút/lượt. |
| **Bottleneck** | Tra cứu thủ công trạm phù hợp và soạn hướng dẫn. Sai loại cổng, sai khoảng cách hoặc dữ liệu trạm cũ có thể khiến xe tiếp tục mất pin. |
| **Business Impact** | Giả định 80 ca/ngày và 15 phút/ca tương đương khoảng 20 giờ công/ngày. Xe nằm chờ làm tăng thời gian đón khách và nguy cơ vi phạm SLA. Cần đối chiếu các số liệu này với log thật. |
| **Success Metric** | Giảm thời gian xử lý trung vị từ 15 xuống dưới 3 phút; ít nhất 98% gợi ý trạm đúng loại xe và còn khả dụng; 0 tin nhắn được gửi nếu chưa có phê duyệt. |
| **Operational Boundary** | AI chỉ đọc dữ liệu được cấp quyền và tạo bản nháp. Nếu pin dưới 5%, không gợi ý trạm xa hơn 5 km và phải đề xuất `dispatch_mobile_charger`. AI không được tự gửi tin, tự điều xe, bịa dữ liệu trạm hoặc bỏ qua HITL. |

## 3. AI Fit & Future-State Flow

### AI Fit

Chọn **Rule/State-Machine + LLM Feature**. Rule kiểm tra pin, khoảng cách, loại cổng và trạng thái trạm. LLM chỉ tóm tắt dữ liệu và soạn ngôn ngữ. Không chọn Agentic Loop vì quyết định điều xe có rủi ro cao và không cần chuỗi hành động tự trị.

### Future-State Flow

1. Tài xế báo sự cố; hệ thống lấy biển số, GPS, loại xe và mức pin.
2. **Rule step:** kiểm tra dữ liệu bắt buộc, tương thích cổng, khoảng cách và trạng thái trạm.
3. **AI step:** LLM tạo JSON gồm `action`, `reason`, `draft_message`, luôn bắt đầu bằng `[DRAFT_ONLY]`.
4. **Human-in-the-loop:** điều phối viên kiểm tra bản đồ, mức pin và nội dung rồi bấm duyệt.
5. Hệ thống chỉ gửi tin sau khi được duyệt và ghi audit log.
6. **Fallback:** Nếu API thiếu dữ liệu, rule fail, JSON sai schema hoặc model không chắc chắn, không gửi tin; điều phối viên xử lý thủ công hoặc gọi đội cứu hộ.

## 4. Technical Boundary Test

Prototype nằm tại [starter-code/prompt_prototype.py](starter-code/prompt_prototype.py).

- Test 1: Với pin 2% và trạm cách 8 km, hệ thống trả về `dispatch_mobile_charger`.
- Test 2: Khi người dùng yêu cầu gửi thẳng, phản hồi vẫn bắt đầu bằng `[DRAFT_ONLY]`.
- Test 3: Khi thiếu dữ liệu pin, loại xe hoặc khoảng cách, hệ thống không đoán dữ liệu an toàn còn thiếu.

Các assertion boundary của autograder đã được thiết kế để kiểm tra hai điều kiện chính: mobile charger và `[DRAFT_ONLY]`.

## 5. Evaluate

| Tiêu chí | Đánh giá |
|---|---|
| Dữ liệu mẫu/logs | Có thể chuẩn bị dữ liệu đã ẩn thông tin cá nhân; cần xác nhận chất lượng và độ đầy đủ của API trạm sạc. |
| Kiểm soát rủi ro | Có Rule gate, HITL bắt buộc, audit log và fallback thủ công. |
| Sẵn sàng thay đổi | Chưa đủ bằng chứng; cần pilot nhỏ và training cho tài xế/điều phối viên. |

### Quyết định: GO ở phạm vi prototype hẹp

Quy trình có bottleneck rõ ở tra cứu và soạn tin. LLM phù hợp với phần ngôn ngữ, còn quyết định an toàn được giữ bằng rule và người duyệt. Prototype không được tự gửi tin hoặc tự điều xe. Trước pilot cần đo baseline thật, kiểm thử tối thiểu 100 ca đã ẩn danh, xác nhận SLA API trạm sạc và đánh giá tỷ lệ gợi ý đúng.
