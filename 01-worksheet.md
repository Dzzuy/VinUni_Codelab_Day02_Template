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

> [!TIP]
> **🤖 AI Prompts — Partner brainstorm:**
> Hãy sử dụng prompt sau để brainstorm các bài toán thực tế nếu bạn chưa có ý tưởng:
> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng [Chọn một: VinFast / Xanh SM / Vinhomes / Vinmec]. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Xanh SM** | Tốn thời gian | Phẩm định và tự động xử lý khiếu nại tính sai giá tiền cuốc xe / nhầm tuyến đường từ hành khách và tài xế. |
| 2 | **VinFast** | Lặp lại | Chẩn đoán sớm và phân loại mã lỗi ECU/Telematics từ dữ liệu cảm biến xe điện gửi về Trung tâm Bảo hành. |
| 3 | **Vinhomes** | Pain từ người khác | Thẩm định và phê duyệt đơn đăng ký thi công/sửa chữa nội thất của cư dân trên App Vinhomes Resident. |
| 4 | **Vinmec** | AI-upgrade | Đối chiếu tương tác thuốc (Drug-Drug Interaction) và kiểm tra tiền sử dị ứng tự động từ đơn thuốc khám lâm sàng. |
| 5 | **Vinpearl** | Tốn thời gian | Xử lý và phân loại tự động yêu cầu hoàn/đổi ngày phòng khách sạn & vé VinWonders do thời tiết bất khả kháng (mưa bão). |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Thẩm định khiếu nại tính sai cước/nhầm đường từ    │
│ hành khách Xanh SM và tính toán mức hoàn tiền tự động.      │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau? Khách hàng (chờ hoàn tiền), CSKH/Kế toán (quá tải)│
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Nhận ticket khiếu nại giá cước từ khách hàng           │
│   → 2. Tra cứu lịch trình GPS thực tế và bản đồ cước phí     │
│   → 3. Trích xuất thời gian kẹt xe / thời gian tài xế chờ    │
│   → 4. Tính toán thủ công số tiền chênh lệch cần hoàn        │
│   → 5. Soạn email/SMS giải trình và gửi lệnh hoàn tiền      │
│                                                             │
│ Bước nào tốn nhất? Bước 2, 3, 4 (⏱ 12-15 phút/vé)           │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3, 4, 5       │
│ (AI đọc log GPS + cước phí -> tính chênh lệch -> draft mail)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian xử lý vé từ 15 phút ──> dưới 2 phút.         │
│ Tỉ lệ tính toán hoàn tiền chính xác đạt 99%.                │
│                                                             │
│ Quick Architecture: [x] LLM Feature (kết hợp Rule-based API) │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #___                                     │
│                                                             │
│ Bài toán: Phân loại & tóm tắt mã lỗi kỹ thuật từ dữ liệu    │
│ Telemetry thô gửi từ xe điện VinFast về xưởng bảo hành.     │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau? Kỹ thuật viên bảo hành (mất thời gian tra log) │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Xe báo lỗi hoặc khách đưa xe vào Trung tâm Bảo hành    │
│   → 2. Kỹ thuật viên cắm thiết bị đọc file log ECU/Telemetry│
│   → 3. Tra cứu thủ công mã lỗi trong tài liệu kỹ thuật      │
│   → 4. Đánh giá nguyên nhân (Pin, Động cơ, hay phần mềm)    │
│   → 5. Lập phiếu sửa chữa & đề xuất linh kiện thay thế      │
│                                                             │
│ Bước nào tốn nhất? Bước 2, 3 (⏱ 20 phút/xe)                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3, 4          │
│ (AI phân tích file log -> tóm tắt lỗi -> đề xuất hướng sửa) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian chẩn đoán ban đầu từ 25 phút ──> dưới 3 phút.│
│ Giảm 30% thời gian xe phải nằm chờ tại xưởng bảo hành.      │
│                                                             │
│ Quick Architecture: [x] LLM                                 │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Phê duyệt đơn đăng ký thi công nội thất & danh    │
│ sách thợ vào căn hộ trên App Vinhomes Resident.             │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Cư dân (chờ lâu 3-5 ngày), Ban Quản Lý (quá tải)│
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Cư dân gửi bản vẽ & danh sách thợ thi công trên App    │
│   → 2. Ban Quản Lý (BQL) mở file PDF/ảnh bản vẽ kiểm tra     │
│   → 3. Đối chiếu quy định an toàn PCCC, tải trọng, tiếng ồn  │
│   → 4. Phản hồi yêu cầu sửa đổi/bổ sung giấy tờ còn thiếu   │
│   → 5. Cấp giấy phép thi công & thẻ ra vào cho thợ         │
│                                                             │
│ Bước nào tốn nhất? Bước 2, 3 (⏱ 45 phút/hồ sơ)             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3, 4          │
│ (AI đọc bản vẽ/hồ sơ -> đối chiếu quy chế BQL -> draft duyệt)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian phê duyệt từ 3 ngày ──> dưới 4 giờ.          │
│ 95% hồ sơ hợp lệ được tự động cấp phép trong ngày.           │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Document AI + Vision)  │
└─────────────────────────────────────────────────────────────┘
```

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm, 85 min)

Nhóm quyết định lựa chọn bài toán **"Card #1 — Xanh SM Thẩm định & Xử lý khiếu nại tính sai tiền cuốc xe"** để thực hiện Deep-Dive.

## 3.1. Current-State Workflow Mapping (25 min)

Quy trình thẩm định và hoàn tiền cước phí thủ công hiện tại của chuyên viên CSKH & Kế toán Xanh SM:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận ticket  │     │ Tra cứu GPS  │     │ Trích xuất   │     │ Tính chênh   │
│ khiếu nại    │ ──→ │ & bản đồ     │ ──→ │ thời gian    │ ──→ │ lệch cước    │
│              │     │ cước phí     │     │ kẹt xe/chờ   │     │ thủ công     │
│ Ai: CSKH     │     │ Ai: CSKH     │     │ Ai: CSKH     │     │ Ai: Kế toán  │
│ ⏱ 2 phút     │     │ ⏱ 4 phút 🔴  │     │ ⏱ 4 phút 🔴  │     │ ⏱ 3 phút 🔴  │
│ In: Ticket ID│     │ In: Trip ID  │     │ In: GPS raw  │     │ In: Formula  │
│ Out: Raw log │     │ Out: Route   │     │ Out: Delay   │     │ Out: Refund  │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Soạn email / │
                                                               │ SMS & duyệt  │
                                                               │ hoàn tiền    │
                                                               │ Ai: CSKH/KT  │
                                                               │ ⏱ 2 phút     │
                                                               └──────────────┘
🔴 = Bottlenecks (Bước 2, 3, 4)
⏱ Tổng thời gian xử lý thủ công: 15 phút/vé khiếu nại.
```

---

## 3.2. Problem Statement (6-field) & Metrics (15 min)
Điền đầy đủ 6 trường thông tin của bài toán:

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Chuyên viên Chăm sóc Khách hàng (CSKH) & Chuyên viên Kế toán vận hành Xanh SM. |
| **2. Current Workflow** | Khi hành khách hoặc tài xế khiếu nại cuốc xe bị tính sai cước (do đi chệch đường, kẹt xe kéo dài, hoặc ứng dụng bị lỗi GPS), CSKH mở App điều vận tra cứu tọa độ GPS thủ công, đối chiếu với bảng giá cước loại xe (VF5/VF8/VF9), dùng Excel tính tiền chênh lệch, rồi soạn email giải trình và lệnh hoàn tiền gửi Kế toán phê duyệt. 5 bước thủ công, mất 15 phút/vé. |
| **3. Bottleneck** | Bước 2, 3 & 4 (mất 11 phút): Tra cứu thủ công log GPS dài, xác định đoạn đường tài xế chạy sai tuyến và áp bảng cước phí động (Dynamic Pricing) theo khung giờ để tính chính xác số tiền cần hoàn. |
| **4. Business Impact** | Mỗi ngày nhận ~150 khiếu nại cước phí tại Hà Nội & TP.HCM. Gây lãng phí ~35 giờ làm việc/ngày của nhân sự CSKH/Kế toán. Thời gian chờ hoàn tiền kéo dài 2-3 ngày khiến chỉ số hài lòng khách hàng (CSAT) giảm 18% và tăng nguy cơ rời bỏ dịch vụ. |
| **5. Success Metric** | 1. Giảm tổng thời gian xử lý khiếu nại cước phí từ 15 phút xuống dưới 2 phút/vé (Efficiency).<br>2. Tỉ lệ tính toán tiền hoàn tiền chính xác và soạn nội dung giải thích đạt 99% (Quality). |
| **6. Operational Boundary** | AI được phép: Truy xuất API lịch trình GPS, API công thức cước Xanh SM, tự động tính toán tiền chênh lệch và tự động soạn thảo (draft) email/SMS phản hồi.<br>**CẤM / HITL:** AI TUYỆT ĐỐI không được tự động phát lệnh hoàn tiền thực tế qua cổng thanh toán/ví điện tử mà không có nhân viên CSKH click duyệt (Bắt buộc HITL); không được phê duyệt số tiền hoàn vượt quá 500,000 VNĐ/cuốc mà không chuyển tiếp cho Trưởng nhóm CSKH review. |

---

## 3.3. Future-State Flow & AI Fit (25 min)

* **AI Fit:** Chọn **LLM Feature** (kết hợp Rule-based Calculator API) vì quy trình xử lý cước có logic rõ ràng, cần khả năng đọc dữ liệu GPS/logs và tổng hợp email thân thiện.
* **Quy trình tương lai (Future-State):**

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận ticket  │     │ 🔵 Auto-pull │     │ 🔵 AI draft  │     │ 🟢 CSKH      │
│ khiếu nại    │ ──→ │ GPS & tính   │ ──→ │ email giải   │ ──→ │ click duyệt  │
│              │     │ tiền chênh   │     │ trình & tiền │     │ & phát lệnh  │
│              │     │ lệch bằng API│     │ hoàn         │     │ hoàn tiền    │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ↩️ Fallback:
                                                               Nếu AI trả về độ tin
                                                               tưởng thấp (< 90%) hoặc
                                                               tiền hoàn > 500k,
                                                               chuyển CSKH xử lý
                                                               thủ công như cũ.
```

---

# 💻 Phase 4 — TECHNICAL PROMPT PROTOTYPE (Nhóm, 30 min)

Tiến hành lập trình bản mẫu prompt trực tiếp trên **Gemini 2.5 Flash** bằng Python tại [starter-code/prompt_prototype.py](starter-code/prompt_prototype.py).

### Ranh giới an toàn (Operational Boundary) được cài đặt vào System Prompt:
* **Quy tắc 1 (HITL Tag):** Nội dung phản hồi phản hồi khách hàng bắt buộc phải bắt đầu bằng thẻ `[DRAFT_ONLY]` để ngăn chặn việc hệ thống gửi tự động khi chưa được CSKH duyệt.
* **Quy tắc 2 (Ngưỡng hoàn tiền & Cấp cứu hộ):** 
  - Nếu tiền chênh lệch tính toán lớn hơn **500,000 VNĐ**, AI không được ra quyết định hoàn tiền trực tiếp mà phải xuất kết quả dạng JSON yêu cầu chuyển Trưởng nhóm: `{"action": "escalate_manager", "reason": "Refund amount exceeds threshold of 500,000 VND"}`.
  - Nếu dữ liệu chuyến đi ghi nhận lỗi kỹ thuật nghiêm trọng từ xe điện (ví dụ sụt pin bất ngờ), AI tự động đề xuất hỗ trợ voucher đền bù.

---

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? (Dữ liệu GPS & lịch sử chuyến đi của Xanh SM).
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? (Có, CSKH phải click duyệt trước khi chuyển tiền hoàn).
3. [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? (Bộ phận CSKH & Kế toán sẵn lòng giảm bớt tác vụ tra cứu Excel thủ công).

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
Dự án đạt mức độ **GO** vì bài toán có quy trình nghiệp vụ rõ ràng, giải pháp công nghệ đơn giản mà hiệu quả cao (LLM Feature kết hợp Rule-based Calculation), metric đo lường khả thi và có số liệu cụ thể (giảm từ 15 phút xuống 2 phút). Rủi ro tài chính được kiểm soát tuyệt đối nhờ cơ chế HITL (nhân viên duyệt lệnh hoàn tiền) và ranh giới tự động chuyển Trưởng nhóm với các giao dịch trên 500,000 VNĐ. Dự án giúp tiết kiệm ~35 giờ làm việc/ngày cho tập đoàn.

---

# 📝 Phase 6 — REFLECTION (Cá nhân)
*Ghi nhận phản ánh của cá nhân bạn về việc phối hợp với AI trong buổi học hôm nay vào file `03-ai-log.md`.*
