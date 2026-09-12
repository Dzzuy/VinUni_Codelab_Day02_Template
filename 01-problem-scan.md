# 01 Problem Scan - Vin Smart Future

## Hướng chọn đề tài

Nhóm mình chọn hướng: **Xanh SM hỗ trợ điều phối viên xử lý sự cố xe điện sắp hết pin**.

Lý do chọn hướng này:

- Xanh SM là dịch vụ gọi xe thuần điện ở Việt Nam, nên pin, trạm sạc và điều phối xe là vấn đề rất gần với vận hành hằng ngày.
- VinFast có hệ thống trạm sạc rộng ở Việt Nam, nhưng trong tình huống gấp thì điều phối viên vẫn phải quyết định nhanh.
- Đề tài này khớp với file code mẫu: nếu pin dưới 5%, AI không được gợi ý trạm sạc quá xa và nên đề xuất xe sạc pin di động.

Nguồn đã xem:

- Xanh SM giới thiệu dịch vụ xe điện: https://xanhsm.taxi/gioi-thieu/
- VinFast tìm showroom và trạm sạc: https://vinfastauto.com/vn_vi/tim-kiem-showroom-tram-sac
- VinFast dịch vụ pin và trạm sạc: https://vinfastauto.com/vn_vi/dich-vu-pin-oto-dien
- Google AI gợi ý dùng prompt có role, context, constraint và output format rõ ràng: https://cloud.google.com/vertex-ai/generative-ai/docs/learn/prompts/prompt-design-strategies

---

## Phase 1 - SCAN

| # | Công ty thành viên | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên phải xử lý thủ công sự cố xe gần hết pin: hỏi tài xế, xem vị trí xe, tìm trạm sạc gần nhất, rồi soạn hướng dẫn. |
| 2 | Xanh SM | Pain từ người khác | Tài xế có thể không biết trạm sạc nào tốt nhất khi pin quá thấp, nhất là giờ cao điểm hoặc đang có khách chờ. |
| 3 | VinFast | Lặp lại | Nhân viên phải đối chiếu nhiều phiên sạc, log trạm sạc và dữ liệu thanh toán để tìm lỗi tính phí hoặc thiếu dữ liệu. |
| 4 | Vinhomes | AI có thể tốt hơn | Phản ánh của cư dân trên app có thể bị phân loại chậm, làm các vấn đề gấp như mất nước, hỏng thang máy bị xử lý trễ. |
| 5 | Vinmec | Tốn thời gian | Bác sĩ mất nhiều thời gian viết tóm tắt xuất viện vì phải đọc ghi chú điều trị, kết quả xét nghiệm và lịch sử thuốc. |
| 6 | Vinpearl | Pain từ người khác | Quản lý khách sạn phải đọc nhiều review từ nhiều nền tảng để tìm các phàn nàn lặp lại như phòng bẩn, check-in chậm, nhân viên thái độ kém. |

---

## Phase 2 - QUICK-ASSESS

Nhóm chọn 3 bài toán tiềm năng nhất:

1. Xanh SM hỗ trợ điều phối viên xử lý xe sắp hết pin
2. Vinhomes phân loại và điều hướng phản ánh cư dân
3. Vinmec hỗ trợ viết tóm tắt xuất viện

---

## Quick Problem Card #1

```text
QUICK PROBLEM CARD #1

Bài toán:
Điều phối viên Xanh SM mất quá nhiều thời gian khi xử lý sự cố xe điện sắp hết pin.

Công ty thành viên:
[x] Xanh SM

Ai đang đau?
Người chính: Điều phối viên.
Người bị ảnh hưởng: Tài xế và khách hàng.

Workflow thủ công hiện tại:
1. Tài xế gọi hoặc nhắn tin báo xe sắp hết pin.
2. Điều phối viên kiểm tra vị trí xe và mức pin hiện tại.
3. Điều phối viên tìm các trạm sạc VinFast gần đó.
4. Điều phối viên kiểm tra trạm nào đủ gần và phù hợp.
5. Điều phối viên viết hướng dẫn cho tài xế hoặc gọi hỗ trợ cứu hộ.

Bước tốn thời gian nhất:
Bước 3 và 4, khoảng 8-12 phút mỗi ca.

AI có thể hỗ trợ ở đâu?
AI có thể đọc tin nhắn của tài xế, mức pin, vị trí xe và danh sách trạm sạc.
Sau đó AI tạo bản nháp gợi ý an toàn cho điều phối viên.

Metric thành công:
Giảm thời gian xử lý từ khoảng 15 phút xuống dưới 3 phút mỗi ca.

Quick Architecture:
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

Vì sao chọn bài này:

- Bài toán này gấp và có ảnh hưởng trực tiếp đến vận hành.
- AI không cần tự quyết định cuối cùng. AI chỉ viết bản nháp, điều phối viên vẫn duyệt.

Vì sao không chỉ dùng rule-based:

- Rule có thể kiểm tra mức pin và khoảng cách, nhưng tin nhắn của tài xế thường không gọn gàng.
- LLM hữu ích vì có thể hiểu tiếng Việt tự nhiên và viết hướng dẫn dễ đọc cho tài xế.

---

## Quick Problem Card #2

```text
QUICK PROBLEM CARD #2

Bài toán:
Phản ánh của cư dân Vinhomes không phải lúc nào cũng được chuyển đến đúng bộ phận một cách nhanh.

Công ty thành viên:
[x] Vinhomes

Ai đang đau?
Người chính: Nhân viên ban quản lý tòa nhà.
Người bị ảnh hưởng: Cư dân và đội bảo trì.

Workflow thủ công hiện tại:
1. Cư dân gửi phản ánh trên app.
2. Nhân viên đọc nội dung thủ công.
3. Nhân viên phân loại vấn đề, ví dụ thang máy, nước, tiếng ồn, bãi xe, an ninh.
4. Nhân viên chuyển đến đội phụ trách.
5. Nhân viên viết phản hồi cho cư dân.

Bước tốn thời gian nhất:
Bước 2 và 3, khoảng 5-10 phút mỗi ticket.

AI có thể hỗ trợ ở đâu?
AI có thể phân loại phản ánh, nhận diện mức độ khẩn cấp và draft câu trả lời ngắn.

Metric thành công:
Điều hướng đúng 85% phản ánh thông thường đến đúng đội trong dưới 30 giây.

Quick Architecture:
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

Vì sao chọn bài này:

- Có nhiều tin nhắn lặp lại, nên có khả năng tiết kiệm thời gian.
- Phân loại văn bản và viết câu trả lời là việc LLM làm khá tốt.

Vì sao không chọn làm đề tài cuối:

- Một số phản ánh có thể liên quan đến phí, tranh chấp hoặc an toàn.
- Cần ranh giới chính sách rõ hơn trước khi cho AI gợi ý câu trả lời.

---

## Quick Problem Card #3

```text
QUICK PROBLEM CARD #3

Bài toán:
Bác sĩ Vinmec mất quá nhiều thời gian để viết tóm tắt xuất viện sau điều trị.

Công ty thành viên:
[x] Vinmec

Ai đang đau?
Người chính: Bác sĩ.
Người bị ảnh hưởng: Điều dưỡng, bệnh nhân và bộ phận hành chính bệnh viện.

Workflow thủ công hiện tại:
1. Bác sĩ đọc chẩn đoán và ghi chú điều trị.
2. Bác sĩ kiểm tra kết quả xét nghiệm và lịch sử dùng thuốc.
3. Bác sĩ viết tóm tắt xuất viện thủ công.
4. Bác sĩ giải thích bước tiếp theo cho bệnh nhân.
5. Bộ phận hành chính lưu tài liệu vào hệ thống.

Bước tốn thời gian nhất:
Bước 1 đến 3, khoảng 20-30 phút mỗi bệnh nhân.

AI có thể hỗ trợ ở đâu?
AI có thể tạo bản nháp tóm tắt xuất viện từ ghi chú và kết quả có cấu trúc.
Bác sĩ bắt buộc phải đọc lại và duyệt trước khi sử dụng.

Metric thành công:
Giảm thời gian viết bản nháp đầu tiên từ 25 phút xuống dưới 8 phút.

Quick Architecture:
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

Vì sao chọn bài này:

- Có khả năng tiết kiệm thời gian rõ.
- Đầu vào và đầu ra đều nhiều văn bản, nên phù hợp với LLM.

Vì sao không chọn làm đề tài cuối:

- Nội dung y tế có rủi ro cao.
- Nếu tóm tắt sai có thể ảnh hưởng đến an toàn bệnh nhân, nên cần kiểm tra chặt chẽ và bác sĩ phê duyệt.

---

## Lựa chọn cuối cùng

Đề tài nhóm chọn là:

> **Xanh SM hỗ trợ điều phối viên xử lý xe điện sắp hết pin**

Nhóm chọn đề tài này vì nó cụ thể, gần với vận hành, và có thể test nhanh trong lab 30 phút. Rủi ro cũng có thể kiểm soát vì AI chỉ viết bản nháp. Điều phối viên vẫn là người duyệt hành động cuối cùng.

Metric chính:

> Giảm thời gian xử lý từ khoảng 15 phút xuống dưới 3 phút cho mỗi sự cố xe sắp hết pin.

Ranh giới chính:

> AI luôn phải xuất kết quả có tag `[DRAFT_ONLY]`. Nếu pin dưới 5%, AI không được gợi ý trạm sạc xa hơn 5km. AI nên trả về hành động `dispatch_mobile_charger`.
