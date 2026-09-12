# Lab 02 — Worksheet: AI Product Scoping (Vin Smart Future)

---

## 🏛️ 1. Bối cảnh thực tế: Vin Smart Future (Vingroup)

**Vingroup** — Tập đoàn tư nhân lớn nhất Việt Nam — vừa sáp nhập toàn bộ các phòng ban công nghệ thuộc các công ty thành viên thành một đơn vị công nghệ thống nhất mang tên **Vin Smart Future**. 

Nhiệm vụ của **Vin Smart Future** là xây dựng các giải pháp AI, số hóa, và tự động hóa cốt lõi để nâng cao hiệu suất vận hành và trải nghiệm khách hàng xuyên suốt các công ty thành viên:
* 🚗 **VinFast:** Hệ thống xe điện thông minh (EV), trợ lý AI ảo trong xe, dự đoán bảo trì pin, và quản lý chuỗi cung ứng sản xuất.
* 🚕 **Xanh SM (GSM):** Vận hành đội xe taxi/xe máy điện thông minh, điều vận thông minh (Smart Dispatching), tối ưu hóa lộ trình di chuyển.
* 🏢 **Vinhomes:** Quản lý đô thị thông minh (Smart Cities), trợ lý cư dân thông minh, tối ưu hóa mức tiêu thụ năng lượng.
* 🏥 **Vinmec:** Y tế thông minh, chẩn đoán hình ảnh bằng AI, tối ưu hóa quản lý hồ sơ bệnh án.
* 🎢 **Vinpearl / VinWonders:** Trải nghiệm du lịch số hóa, quản lý phòng và luồng khách thông minh tại các khu vui chơi.

Trong buổi Lab hôm nay, nhóm của bạn sẽ đóng vai trò là **AI Product Engineer** tại **Vin Smart Future**, tiến hành tìm kiếm, scoping, phân tích độ khả thi, thiết lập ranh giới vận hành, và xây dựng một **bản mẫu kỹ thuật (prompt prototype)** cho một bài toán cụ thể thuộc một trong những mảng kinh doanh trên.

---

## 📊 2. Cơ cấu tính điểm bài lab

### 👥 Điểm nhóm (60 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **G1. Workflow Mapping** | 20 | Problem Deep-Dive | Vẽ chi tiết quy trình hiện tại: các bước, handoff, thời gian, bottleneck |
| **G2. Problem Statement** | 20 | Problem Deep-Dive | Problem Statement 6-field bám sát thực tế, metric có số và ranh giới rõ ràng |
| **G3. AI Fit & Future Flow** | 10 | Problem Deep-Dive | So sánh Rule vs LLM vs Agent, future flow có bước AI, ranh giới và Fallback |
| **G4. Decision Quality** | 10 | Problem Deep-Dive | Quyết định Go/Not Yet/No-Go trung thực và có chứng cứ rõ ràng |

### 👤 Điểm cá nhân (40 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **I1. Scan & Cards** | 15 | Quick Cards | Liệt kê 5 problems sử dụng 3 lenses, hoàn thiện 3 quick cards chất lượng |
| **I2. Prototyping** | 10 | 02-lab/ | Chạy thử nghiệm programmatic prompt prototype thành công |
| **I3. AI Log & Reflection** | 15 | 03-ai-log.md | Phản ánh trung thực về việc dùng AI làm thought-partner (giúp gì, sai gì, sửa gì) |

---

# 🚀 Phase 0 — worked Example: Xanh SM Intelligent Dispatcher (15 min)

*Giảng viên walk-through ví dụ thực tế từ Vin Smart Future để bạn hiểu rõ cách scoping một bài toán AI.*
Đọc chi tiết worked example tại file [02-deliverable-example.md](02-deliverable-example.md).

---

# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Xanh SM | Lặp lại | Điều phối viên xử lý thủ công các báo cáo xe hết pin hoặc gặp sự cố sạc giữa đường. |
| 2 | Vinhomes | Tốn thời gian | Nhân viên phân loại và soạn phản hồi cho khiếu nại cư dân trên ứng dụng. |
| 3 | VinFast | Lặp lại | Nhân viên đối chiếu hóa đơn sạc với dữ liệu trạm và xe theo chu kỳ. |
| 4 | Vinmec | Tốn thời gian | Bác sĩ soạn tóm tắt hồ sơ xuất viện từ nhiều ghi chú lâm sàng. |
| 5 | Vinpearl | AI-upgrade | Nhân viên CSKH trả lời thủ công câu hỏi về phòng, vé và dịch vụ vui chơi. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

### Quick Problem Card #1 — Xanh SM: Sự cố pin thực địa
- **Bài toán:** Điều phối viên cần tìm phương án sạc an toàn và soạn hướng dẫn cho tài xế khi xe hết pin giữa đường.
- **Actor:** Tài xế, điều phối viên và đội cứu hộ.
- **Workflow:** Tài xế báo sự cố → xác minh biển số/vị trí → tra cứu trạm tương thích → soạn hướng dẫn → điều phối viên duyệt và gửi.
- **Bottleneck:** Tra cứu trạm và soạn tin, khoảng 10 phút/lượt theo baseline giả định.
- **AI hỗ trợ:** Tóm tắt dữ liệu sự cố và tạo bản nháp hướng dẫn.
- **Metric:** Giảm thời gian xử lý từ 15 phút xuống dưới 3 phút/lượt; đạt ít nhất 98% địa điểm và loại cổng sạc đúng.
- **Architecture:** LLM Feature kết hợp Rule/State Machine; không dùng Agent tự trị.

### Quick Problem Card #2 — Vinhomes: Phân loại khiếu nại cư dân
- **Bài toán:** Tự động phân loại khiếu nại và tạo bản nháp phản hồi cho nhân viên CSKH.
- **Actor:** Cư dân, nhân viên CSKH và bộ phận vận hành tòa nhà.
- **Workflow:** Cư dân gửi ticket → CSKH đọc và phân loại → chuyển bộ phận phụ trách → soạn phản hồi → trưởng nhóm duyệt case nhạy cảm.
- **Bottleneck:** Đọc, phân loại và chuyển ticket, khoảng 8 phút/ticket theo baseline giả định.
- **AI hỗ trợ:** Phân loại chủ đề, trích xuất mức độ ưu tiên và tạo bản nháp có dẫn nguồn chính sách.
- **Metric:** 85% ticket được phân loại dưới 10 giây; thời gian soạn phản hồi dưới 2 phút; 100% case pháp lý/tài chính qua người duyệt.
- **Architecture:** LLM Feature kết hợp Rule router.

### Quick Problem Card #3 — Vinmec: Tóm tắt hồ sơ xuất viện
- **Bài toán:** Tạo bản nháp tóm tắt xuất viện từ hồ sơ đã được xác thực.
- **Actor:** Bác sĩ điều trị và nhân viên hồ sơ bệnh án.
- **Workflow:** Hoàn tất ghi chú → gom kết quả xét nghiệm/thuốc → bác sĩ đọc lại → viết tóm tắt → ký duyệt và lưu hồ sơ.
- **Bottleneck:** Gom thông tin và viết bản nháp, khoảng 20 phút/bệnh nhân theo baseline giả định.
- **AI hỗ trợ:** Trích xuất thông tin có cấu trúc và tạo bản nháp; không tự chẩn đoán hoặc ký hồ sơ.
- **Metric:** Giảm thời gian soạn nháp xuống dưới 5 phút; 100% bản nháp được bác sĩ duyệt.
- **Architecture:** LLM Feature có trích xuất cấu trúc và HITL bắt buộc.

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm, 85 min)

## 3.1. Current-State Workflow Mapping (25 min)
### Current-State Workflow — Xanh SM xử lý sự cố pin
1. Tài xế gọi tổng đài và mô tả sự cố (2 phút).
2. Điều phối viên xác minh biển số, loại xe, mức pin và vị trí GPS (2 phút, 🔄 handoff từ tài xế).
3. Điều phối viên mở bản đồ nội bộ và dashboard trạm sạc để tìm trạm còn chỗ, đúng chuẩn cổng và trong tầm di chuyển (5 phút, 🔴 bottleneck).
4. Điều phối viên tự viết hướng dẫn đường đi hoặc gọi đội cứu hộ (5 phút, 🔴 bottleneck).
5. Điều phối viên đọc lại, gửi cho tài xế và ghi log sự cố (1 phút, 🔄 handoff sang tài xế/đội cứu hộ).

**Tổng thời gian baseline giả định: 15 phút/lượt.** Cần đo tối thiểu 2 tuần log thực tế trước khi chốt business case.

## 3.2. Problem Statement (6-field) & Metrics (15 min)
| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên trung tâm vận hành Xanh SM; đầu vào đến từ tài xế và hệ thống định vị. |
| **2. Current Workflow** | Điều phối viên nhận cuộc gọi, xác minh thông tin trên bản đồ nội bộ, tra cứu dashboard trạm sạc, viết hướng dẫn và gửi sau khi kiểm tra. Quy trình hiện mất khoảng 15 phút/lượt theo baseline giả định. |
| **3. Bottleneck** | Tra cứu thủ công trạm phù hợp và soạn hướng dẫn. Sai loại cổng, sai khoảng cách hoặc dữ liệu trạm cũ có thể khiến xe tiếp tục mất pin. |
| **4. Business Impact** | Với 80 ca/ngày theo giả định cần xác minh, 15 phút/ca tương đương khoảng 20 giờ công/ngày. Xe nằm chờ còn làm tăng thời gian đón khách và nguy cơ vi phạm SLA. |
| **5. Success Metric** | Giảm thời gian xử lý trung vị từ 15 xuống dưới 3 phút; ít nhất 98% gợi ý trạm đúng loại xe và còn khả dụng; 0 tin nhắn được gửi mà không có phê duyệt của điều phối viên. |
| **6. Operational Boundary** | AI chỉ đọc dữ liệu đã được cấp quyền và tạo bản nháp. Nếu pin dưới 5% thì không gợi ý trạm xa hơn 5 km và phải đề xuất `dispatch_mobile_charger`. AI không được tự gửi tin, tự điều xe, tự bịa dữ liệu trạm hoặc bỏ qua HITL. |

## 3.3. Future-State Flow & AI Fit (25 min)
* **AI-Fit Matrix:** Chọn **Rule/State-Machine + LLM Feature**. Rule kiểm tra pin, khoảng cách, loại cổng và trạng thái trạm; LLM chỉ tóm tắt và soạn ngôn ngữ. Không chọn Agentic Loop vì quyết định điều xe có rủi ro cao và không cần chuỗi hành động tự trị.

### Future-State Flow
1. Tài xế báo sự cố → hệ thống lấy biển số, GPS, loại xe và mức pin.
2. **Rule step:** kiểm tra dữ liệu bắt buộc, tương thích cổng, khoảng cách và trạng thái trạm.
3. **🔵 AI step:** LLM tạo JSON có `action`, `reason`, `draft_message`, luôn bắt đầu bằng `[DRAFT_ONLY]`.
4. **🟢 Human step:** điều phối viên kiểm tra bản đồ, mức pin và nội dung rồi bấm duyệt.
5. Hệ thống gửi tin sau khi được duyệt và ghi audit log.
6. **↩️ Fallback:** Nếu API thiếu dữ liệu, rule fail, JSON sai schema hoặc model không chắc chắn, không gửi tin; điều phối viên xử lý thủ công theo quy trình cũ hoặc gọi đội cứu hộ.

---

# 💻 Phase 4 — TECHNICAL PROMPT PROTOTYPE (Nhóm, 30 min)

Để đảm bảo kỹ sư của Vin Smart Future luôn giữ vững năng lực lập trình, nhóm của bạn sẽ tiến hành **lập trình bản mẫu prompt** trực tiếp trên **Gemini 2.5 Flash** bằng Python để stress-test hệ thống.

Prototype nằm tại [starter-code/prompt_prototype.py](starter-code/prompt_prototype.py). Kết quả kiểm thử: Test 1 với pin 2% và trạm cách 8 km trả về `dispatch_mobile_charger`; Test 2 giữ `[DRAFT_ONLY]` dù người dùng yêu cầu gửi thẳng; Test 3 không đoán dữ liệu an toàn còn thiếu. Cả hai assertion chính của autograder đều Passed, không có Failed.

---

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [x] Có thể chuẩn bị dữ liệu mẫu/logs đã ẩn thông tin cá nhân; cần xác nhận chất lượng và độ đầy đủ của API trạm sạc.
2. [x] Rủi ro nằm trong tầm kiểm soát nhờ Rule gate, HITL bắt buộc, audit log và fallback thủ công.
3. [ ] Chưa đủ bằng chứng về mức độ sẵn sàng của tài xế và điều phối viên; cần pilot nhỏ và training trước khi mở rộng.

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.
[ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn):** Hủy bỏ dự án AI này.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
Chọn GO cho prototype giới hạn ở việc đọc dữ liệu, kiểm tra rule và tạo bản nháp. Quy trình hiện tại có bottleneck rõ ở tra cứu và soạn tin; LLM phù hợp với phần ngôn ngữ, còn quyết định an toàn được giữ bằng rule và người duyệt. Chưa cho phép tự gửi tin hay tự điều xe. Trước pilot cần đo baseline thật, kiểm thử tối thiểu 100 ca đã ẩn danh, xác nhận SLA API trạm sạc và đánh giá tỷ lệ gợi ý đúng.

---

# 📝 Phase 6 — REFLECTION (Cá nhân)
AI giúp nhóm mở rộng danh sách pain point và phản biện lựa chọn Agent so với LLM Feature. Điểm cần kiểm soát là số liệu vận hành ban đầu chỉ là giả định, nên tôi gắn nhãn baseline và không dùng chúng như sự thật đã xác minh. Tôi cũng bổ sung ranh giới pin dưới 5%, giới hạn 5 km, HITL, fallback và test adversarial để biến yêu cầu an toàn thành điều kiện kiểm tra được. Phản ánh chi tiết có thể ghi lại trong file `03-ai-log.md`.
