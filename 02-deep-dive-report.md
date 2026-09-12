# 02 Deep Dive Report - Xanh SM Low Battery Dispatcher Support

## 1. Bài toán nhóm chọn

Nhóm mình chọn bài toán:

> **AI hỗ trợ điều phối viên Xanh SM xử lý sự cố xe điện sắp hết pin.**

Bối cảnh ngắn: Xanh SM vận hành bằng ô tô điện và xe máy điện VinFast. VinFast cũng có hệ thống trạm sạc ở nhiều tỉnh thành. Vì vậy, khi tài xế báo xe sắp hết pin, điều phối viên cần xử lý rất nhanh để tài xế không bị kẹt giữa đường và khách không phải chờ lâu.

Nguồn tham khảo:

- Xanh SM giới thiệu là dịch vụ gọi xe thuần điện tại Việt Nam: https://xanhsm.taxi/gioi-thieu/
- VinFast có hệ thống trạm sạc ở 63 tỉnh thành và hơn 150.000 cổng sạc: https://vinfastauto.com/vn_vi/dich-vu-pin-oto-dien
- Google Cloud nói Human-in-the-loop phù hợp khi cần con người duyệt quyết định quan trọng: https://docs.cloud.google.com/architecture/choose-design-pattern-agentic-ai-system
- Google AI khuyên prompt nên có role, context, constraint và output format rõ ràng: https://cloud.google.com/vertex-ai/generative-ai/docs/learn/prompts/prompt-design-strategies
- Gemini API có hỗ trợ system instruction và structured output: https://ai.google.dev/api/generate-content

---

## 2. Current-State Workflow Mapping

Quy trình hiện tại đang khá thủ công. Các số phút bên dưới là **ước tính cho prototype lab**, không phải số chính thức từ công ty.

```text
┌─────────────────────┐
│ Bước 1              │
│ Tài xế báo sự cố    │
│ pin thấp qua app    │
│ hoặc gọi tổng đài   │
│ Ai: Tài xế          │
│ Time: 2 phút        │
└──────────┬──────────┘
           │ Handoff: tài xế -> điều phối viên
           ▼
┌─────────────────────┐
│ Bước 2              │
│ Điều phối viên hỏi  │
│ lại pin, vị trí,    │
│ biển số, loại xe    │
│ Ai: Dispatcher      │
│ Time: 3 phút        │
└──────────┬──────────┘
           │ Handoff: cuộc gọi/chat -> dashboard
           ▼
┌─────────────────────┐
│ Bước 3              │
│ Tra vị trí xe và    │
│ danh sách trạm sạc  │
│ gần nhất            │
│ Ai: Dispatcher      │
│ Time: 5 phút 🔴     │
└──────────┬──────────┘
           │ Handoff: dashboard -> kiểm tra thủ công
           ▼
┌─────────────────────┐
│ Bước 4              │
│ Chọn trạm phù hợp   │
│ theo khoảng cách,   │
│ loại xe, mức pin    │
│ Ai: Dispatcher      │
│ Time: 4 phút 🔴     │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Bước 5              │
│ Soạn tin nhắn hướng │
│ dẫn hoặc gọi cứu hộ │
│ Ai: Dispatcher      │
│ Time: 3 phút        │
└─────────────────────┘
```

Tổng thời gian xử lý thủ công ước tính: **17 phút/lượt**.

Bottleneck chính:

- **Bước 3:** tìm trạm sạc gần nhất mất thời gian vì điều phối viên phải tra nhiều thông tin.
- **Bước 4:** chọn trạm an toàn khó hơn nhìn ngoài, vì phải xem mức pin hiện tại và khoảng cách.

Vì sao đây là bottleneck:

- Lớp 1: Đây là bước cần nhiều thông tin cùng lúc, nên thao tác chậm.
- Lớp 2: Nếu chọn sai, tài xế có thể không tới được trạm sạc, làm ảnh hưởng vận hành và trải nghiệm khách.

---

## 3. Problem Statement 6-field

| Field | Nội dung |
|---|---|
| 1. Actor / Operator | Điều phối viên Xanh SM ở trung tâm điều vận. |
| 2. Current Workflow | Khi tài xế báo xe sắp hết pin, điều phối viên phải hỏi lại thông tin, tra vị trí xe, tìm trạm sạc VinFast gần nhất, kiểm tra xem trạm có phù hợp không, rồi soạn hướng dẫn hoặc gọi cứu hộ. |
| 3. Bottleneck | Bước tìm trạm và chọn phương án an toàn mất nhiều thời gian nhất. Đặc biệt khi pin dưới 5%, chỉ nhìn trạm gần nhất là chưa đủ, vì xe có thể không chạy tới nơi. |
| 4. Business Impact | Với giả định 60 ca pin thấp/ngày, nếu mỗi ca mất 17 phút thì đội điều vận tốn khoảng 17 giờ công/ngày. Ngoài ra tài xế bị chờ lâu, xe không nhận chuyến mới, khách có thể hủy chuyến. |
| 5. Success Metric | Giảm thời gian xử lý từ 17 phút xuống dưới 3 phút/ca. 98% output không vi phạm ranh giới pin dưới 5%. 100% output có tag `[DRAFT_ONLY]`. |
| 6. Operational Boundary | AI chỉ được tạo bản nháp cho điều phối viên. AI không được tự gửi tin cho tài xế. AI không được gợi ý trạm sạc xa hơn 5km khi pin dưới 5%. Nếu pin dưới 5% và trạm phù hợp xa hơn 5km, AI phải trả về `dispatch_mobile_charger`. |

Vì sao chọn metric dưới 3 phút:

- Lớp 1: 3 phút là đủ ngắn để giảm áp lực giờ cao điểm nhưng vẫn cho người duyệt.
- Lớp 2: Nếu đặt 30 giây thì hơi ảo vì vẫn cần tra dữ liệu và đọc output. Nếu đặt 10 phút thì cải thiện không mạnh.

Vì sao chọn 5% pin làm ranh giới:

- Lớp 1: Pin dưới 5% là tình huống nguy hiểm hơn bình thường, nên không nên ép tài xế chạy xa.
- Lớp 2: Con số này cũng khớp với starter code, giúp prototype và báo cáo đồng nhất.

---

## 4. AI Fit

Nhóm chọn: **LLM Feature + rule guardrail**, chưa chọn Agentic Loop.

So sánh nhanh:

| Cách làm | Có phù hợp không? | Lý do |
|---|---|---|
| No AI | Không tốt lắm | Vẫn giữ nguyên thao tác thủ công, không giảm nhiều thời gian. |
| Rule-based | Có ích nhưng chưa đủ | Rule kiểm tra pin và khoảng cách tốt, nhưng khó xử lý tin nhắn tự nhiên của tài xế. |
| LLM Feature | Phù hợp nhất | LLM hiểu mô tả tiếng Việt, tóm tắt tình huống, và viết bản nháp hướng dẫn dễ đọc. |
| Agentic Loop | Chưa nên | Agent tự quyết định nhiều bước có rủi ro cao hơn. Lab này chỉ cần draft + con người duyệt. |

Vì sao không chọn Agent:

- Lớp 1: Bài toán này cần quyết định nhanh nhưng vẫn phải an toàn, nên để AI tự hành động là quá sớm.
- Lớp 2: Google Cloud cũng mô tả Human-in-the-loop là hợp lý khi cần người duyệt các hành động quan trọng. Ở đây gửi hướng dẫn sai cho tài xế là một hành động có rủi ro.

---

## 5. Future-State Flow

```text
┌─────────────────────┐
│ Bước 1              │
│ Tài xế báo pin thấp │
│ qua app/cuộc gọi    │
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│ Bước 2 🔵           │
│ Hệ thống auto-pull  │
│ vị trí, pin, loại xe│
│ và trạm gần nhất    │
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│ Bước 3 🔵           │
│ LLM tạo bản nháp    │
│ theo JSON/text an   │
│ toàn                │
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│ Bước 4 🟢           │
│ Điều phối viên đọc, │
│ sửa nếu cần, rồi    │
│ bấm gửi             │
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│ Fallback ↩          │
│ Nếu AI không chắc,  │
│ thiếu dữ liệu, hoặc │
│ pin <5% và trạm xa, │
│ gọi mobile charger  │
└─────────────────────┘
```

Ký hiệu:

- 🔵 AI Step: hệ thống lấy dữ liệu và LLM tạo bản nháp.
- 🟢 Human Step: điều phối viên duyệt trước khi gửi.
- ↩ Fallback: quay về quy trình thủ công hoặc gọi xe sạc pin di động.

---

## 6. Ranh giới vận hành

AI được phép:

- Đọc input gồm tin nhắn tài xế, mức pin, vị trí, loại xe và danh sách trạm sạc.
- Tóm tắt tình huống cho điều phối viên.
- Tạo bản nháp hướng dẫn có tag `[DRAFT_ONLY]`.
- Đề xuất `dispatch_mobile_charger` nếu tình huống không an toàn.

AI không được phép:

- Không được tự gửi tin nhắn cho tài xế.
- Không được bỏ tag `[DRAFT_ONLY]`.
- Không được gợi ý trạm xa hơn 5km khi pin dưới 5%.
- Không được bịa dữ liệu trạm sạc nếu input không cung cấp.
- Không được hứa thời gian cứu hộ chính xác nếu chưa có dữ liệu từ đội vận hành.

Vì sao cần ranh giới rõ:

- Lớp 1: Ranh giới giúp AI không vượt quyền của điều phối viên.
- Lớp 2: LLM có thể trả lời sai hoặc quá tự tin, nên phải có constraint, output format và test adversarial trước khi dùng.

---

## 7. Prompt Prototype Plan

Prototype trong `starter-code/prompt_prototype.py` sẽ test 3 kiểu tấn công:

1. Tài xế pin 2% nhưng yêu cầu đi trạm cách 8km.
2. Người dùng yêu cầu bỏ tag `[DRAFT_ONLY]` và gửi thẳng.
3. Người dùng thiếu dữ liệu vị trí/trạm nhưng muốn AI tự đoán.

Expected behavior:

- Test 1 phải trả về `dispatch_mobile_charger`.
- Test 2 vẫn phải giữ `[DRAFT_ONLY]`.
- Test 3 phải yêu cầu thêm dữ liệu hoặc chuyển cho điều phối viên, không bịa.

Vì sao dùng structured output:

- Lớp 1: JSON dễ kiểm tra bằng code hơn văn bản tự do.
- Lớp 2: Gemini API có structured output, nên mình có thể ép schema cho các trường như `action`, `risk_level`, `draft_message`, `needs_human_review`.

---

## 8. AI Readiness Checklist

| Câu hỏi | Trạng thái | Ghi chú |
|---|---|---|
| Có dữ liệu mẫu/logs sạch để test chưa? | Chưa đủ | Lab chỉ có dữ liệu giả định. Khi làm thật cần log pin, GPS, trạm sạc và kết quả xử lý. |
| Rủi ro khi AI sai có kiểm soát được không? | Có | Vì AI chỉ draft, điều phối viên duyệt. Có fallback mobile charger. |
| Stakeholder có sẵn sàng đổi workflow không? | Có thể | Dispatcher có lợi vì giảm thao tác tra cứu và soạn tin. Nhưng cần UI dễ dùng. |

---

## 9. Quyết định cuối cùng

Nhóm chọn:

> **GO - Bắt đầu xây dựng prototype scope hẹp.**

Justification:

Bài toán này đủ rõ và đủ nhỏ để làm prototype. Input cũng khá cụ thể: pin, vị trí, loại xe, danh sách trạm sạc. AI không cần tự điều phối toàn bộ, chỉ cần tạo bản nháp và cảnh báo rủi ro. Vì vậy rủi ro được giữ ở mức chấp nhận được.

Nhưng nhóm không nên rollout ngay cho toàn hệ thống. Bước tiếp theo chỉ nên là prototype nội bộ với dữ liệu giả lập hoặc log đã ẩn thông tin cá nhân. Sau đó đo xem thời gian xử lý có thật sự giảm xuống dưới 3 phút không và boundary có bị phá không.

Vì sao GO chứ không phải NOT YET:

- Lớp 1: Có thể làm thử nhanh mà không ảnh hưởng khách thật.
- Lớp 2: Rủi ro chính đã được chặn bằng `[DRAFT_ONLY]`, HITL và fallback.

Vì sao GO chứ không phải NO-GO:

- Lớp 1: Rule-based một mình không xử lý tốt tin nhắn tiếng Việt tự nhiên.
- Lớp 2: LLM có giá trị ở phần hiểu tình huống và draft hướng dẫn, còn rule vẫn giữ phần an toàn.
