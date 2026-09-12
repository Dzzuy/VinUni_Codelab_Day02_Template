# 01 Problem Scan - Vin Smart Future

## Hướng chọn đề tài

Nhóm chọn hướng cuối là: **Xanh SM - AI hỗ trợ điều phối viên xử lý sự cố xe điện sắp hết pin**.

Lý do chọn:

- Xanh SM là dịch vụ gọi xe thuần điện, nên pin và trạm sạc là vấn đề rất gần với vận hành hằng ngày.
- VinFast có hệ thống trạm sạc rộng, nhưng trong tình huống gấp điều phối viên vẫn phải kiểm tra vị trí, mức pin, trạm phù hợp rồi soạn hướng dẫn.
- Bài toán này cụ thể, có metric rõ, và khớp với phần code prototype: `[DRAFT_ONLY]`, pin dưới 5%, và `dispatch_mobile_charger`.

Nguồn tham khảo:

- Xanh SM giới thiệu dịch vụ xe điện: https://xanhsm.taxi/gioi-thieu/
- VinFast dịch vụ pin và trạm sạc: https://vinfastauto.com/vn_vi/dich-vu-pin-oto-dien
- Google Cloud về Human-in-the-loop: https://docs.cloud.google.com/architecture/choose-design-pattern-agentic-ai-system
- Google Cloud về prompt design: https://cloud.google.com/vertex-ai/generative-ai/docs/learn/prompts/prompt-design-strategies

Ghi chú: Các số trong bài là **baseline giả định cho lab**, chưa phải số nội bộ chính thức.

---

## Phase 1 - SCAN

| # | Công ty thành viên | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên xử lý thủ công sự cố xe gần hết pin: hỏi tài xế, xem vị trí xe, tra trạm sạc, rồi soạn hướng dẫn. |
| 2 | Xanh SM | Lặp lại | Sau khi hỗ trợ tài xế, nhân viên phải nhập lại biển số, mã tài xế, nhóm lỗi, mức pin, vị trí, hành động và kết quả vào CRM. |
| 3 | VinFast | Lặp lại | Nhân viên đối chiếu hóa đơn phiên sạc giữa log trạm sạc, dữ liệu xe và dữ liệu thanh toán theo chu kỳ. |
| 4 | Vinhomes | AI có thể tốt hơn | Phản ánh cư dân trên app cần được phân loại và chuyển đúng bộ phận nhanh hơn, ví dụ thang máy, nước, tiếng ồn, bãi xe. |
| 5 | Vinmec | Tốn thời gian | Bác sĩ mất nhiều thời gian viết tóm tắt xuất viện từ ghi chú điều trị, kết quả xét nghiệm và lịch sử thuốc. |
| 6 | Vinpearl | Pain từ người khác | Quản lý khách sạn phải đọc nhiều review từ nhiều nền tảng để tìm phàn nàn lặp lại như phòng bẩn, check-in chậm, nhân viên chưa tốt. |

---

## Phase 2 - QUICK-ASSESS

Nhóm chọn 3 bài toán tiềm năng nhất:

1. Xanh SM xử lý sự cố pin thực địa
2. Vinhomes phân loại phản ánh cư dân
3. Vinmec hỗ trợ viết tóm tắt xuất viện

---

## Quick Problem Card #1 - Xanh SM: Sự cố pin thực địa

```text
QUICK PROBLEM CARD #1

Bài toán:
Điều phối viên cần tìm phương án sạc an toàn và soạn hướng dẫn cho tài xế
khi xe điện sắp hết pin hoặc không đủ pin để tiếp tục nhận chuyến.

Công ty thành viên:
[x] Xanh SM

Ai đang đau?
Điều phối viên, tài xế và khách hàng đang chờ xe.

Workflow thủ công hiện tại:
1. Tài xế gọi hoặc nhắn tin báo xe sắp hết pin.
2. Điều phối viên xác minh biển số, loại xe, vị trí và mức pin.
3. Điều phối viên tra dashboard để tìm trạm sạc gần nhất.
4. Điều phối viên kiểm tra khoảng cách, loại cổng và trạng thái trạm.
5. Điều phối viên soạn hướng dẫn hoặc gọi hỗ trợ sạc/cứu hộ.

Bước tốn thời gian/lỗi nhất:
Bước 3 và 4, khoảng 9-10 phút/lượt theo baseline giả định.

AI có thể hỗ trợ ở bước nào?
AI đọc dữ liệu sự cố, tóm tắt tình huống, kiểm tra ranh giới an toàn,
rồi tạo bản nháp cho điều phối viên.

Metric có số:
Giảm thời gian xử lý từ 15 phút xuống dưới 3 phút/lượt.
98% gợi ý đúng loại xe/trạm trong test set.
100% output có `[DRAFT_ONLY]`.

Quick Architecture:
[ ] No AI  [x] Rule  [x] LLM  [ ] Agent
```

Vì sao chọn bài này:

- Bài toán có workflow rõ và ảnh hưởng trực tiếp đến vận hành.
- Rủi ro kiểm soát được vì AI chỉ draft, người vẫn duyệt.

Vì sao không chỉ dùng rule-based:

- Rule kiểm tra pin, khoảng cách, loại cổng rất tốt.
- Nhưng tin nhắn tài xế có thể viết tắt, sai chính tả, không dấu. LLM phù hợp hơn ở phần hiểu tiếng Việt và viết lại hướng dẫn.

---

## Quick Problem Card #2 - Vinhomes: Phân loại phản ánh cư dân

```text
QUICK PROBLEM CARD #2

Bài toán:
Phản ánh cư dân trên app không phải lúc nào cũng được phân loại và chuyển
đến đúng bộ phận nhanh.

Công ty thành viên:
[x] Vinhomes

Ai đang đau?
Cư dân, nhân viên CSKH và đội vận hành tòa nhà.

Workflow thủ công hiện tại:
1. Cư dân gửi phản ánh trên app.
2. Nhân viên đọc nội dung.
3. Nhân viên phân loại vấn đề.
4. Nhân viên chuyển cho bộ phận phụ trách.
5. Nhân viên soạn phản hồi cho cư dân.

Bước tốn thời gian/lỗi nhất:
Đọc, phân loại và chuyển ticket, khoảng 5-10 phút/ticket.

AI có thể hỗ trợ ở bước nào?
AI phân loại chủ đề, nhận diện mức độ khẩn cấp và draft câu trả lời.

Metric có số:
85% ticket thông thường được phân loại dưới 30 giây.
100% case pháp lý/tài chính phải qua người duyệt.

Quick Architecture:
[ ] No AI  [x] Rule  [x] LLM  [ ] Agent
```

Vì sao chưa chọn làm final:

- Một số ticket liên quan đến phí, tranh chấp hoặc an toàn nên cần policy rõ hơn.
- Nếu quy trình giữa các bộ phận vẫn chậm, LLM chỉ làm đẹp câu trả lời chứ chưa chắc giảm SLA thật.

---

## Quick Problem Card #3 - Vinmec: Tóm tắt hồ sơ xuất viện

```text
QUICK PROBLEM CARD #3

Bài toán:
Bác sĩ mất nhiều thời gian viết tóm tắt xuất viện từ nhiều ghi chú lâm sàng.

Công ty thành viên:
[x] Vinmec

Ai đang đau?
Bác sĩ, điều dưỡng, bệnh nhân và bộ phận hồ sơ bệnh án.

Workflow thủ công hiện tại:
1. Bác sĩ đọc chẩn đoán và ghi chú điều trị.
2. Bác sĩ kiểm tra kết quả xét nghiệm và thuốc.
3. Bác sĩ viết bản tóm tắt xuất viện.
4. Bác sĩ ký duyệt và giải thích cho bệnh nhân.

Bước tốn thời gian/lỗi nhất:
Gom thông tin và viết bản nháp, khoảng 20-30 phút/bệnh nhân.

AI có thể hỗ trợ ở bước nào?
AI tạo bản nháp từ dữ liệu đã xác thực. Bác sĩ bắt buộc phải đọc và ký duyệt.

Metric có số:
Giảm thời gian soạn nháp từ 25 phút xuống dưới 8 phút.
100% bản nháp được bác sĩ duyệt.

Quick Architecture:
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
```

Vì sao chưa chọn làm final:

- Giá trị cao nhưng rủi ro y tế cũng cao.
- Cần dữ liệu bệnh án thật, quy trình bảo mật và kiểm định y khoa, vượt phạm vi lab ngắn.

---

## Lựa chọn cuối cùng

Đề tài final:

> **Xanh SM - AI hỗ trợ điều phối viên xử lý sự cố xe điện sắp hết pin.**

Nhóm chọn đề tài này vì nó đủ cụ thể để scope trong lab, có thể test bằng code, và có boundary rõ. Cách làm tốt nhất là **Rule guardrail + LLM Feature + Human-in-the-loop**.

Metric chính:

> Giảm thời gian xử lý từ 15 phút xuống dưới 3 phút/lượt, trong khi vẫn giữ 100% tin nhắn ở trạng thái `[DRAFT_ONLY]` trước khi điều phối viên duyệt.
