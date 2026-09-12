# 01 — Problem Scan & Quick Cards

> **Lab 02 — AI Product Scoping (Vin Smart Future)**
> **Học viên:** Nguyễn Văn Thân
> **Vai trò giả định:** AI Product Engineer — Vin Smart Future
> **Phạm vi khảo sát:** Xanh SM (GSM), VinFast, Vinhomes, Vinmec, Vinpearl

---

## 🏛️ Bối cảnh cá nhân

Tôi được phân công khảo sát khối vận hành hậu cần (back-office operations) của các công ty thành viên Vingroup để tìm các quy trình thủ công đang rò rỉ hiệu suất. Điểm tôi tập trung quan sát là **những tác vụ mà nhân viên phải đọc — hiểu — rồi viết lại bằng tiếng Việt**, vì đây là nhóm tác vụ mà rule-based code truyền thống không xử lý được, và cũng là nơi LLM có lợi thế rõ rệt nhất.

> ⚠️ **Ghi chú về số liệu:** Các con số trong tài liệu này là **ước lượng làm việc (working assumptions)** được xây dựng từ quan sát quy trình và brainstorm với AI, **chưa được đối chiếu với số liệu nội bộ chính thức của Vingroup**. Trước khi ra quyết định đầu tư thật, toàn bộ cần được xác minh lại với Khối Vận hành. Chi tiết cách các con số này được tạo ra và giới hạn của chúng được ghi trong [`03-ai-log.md`](03-ai-log.md).

---

# 🔍 Phase 1 — SCAN: Quét cơ hội qua 4 Lenses

| # | Subsidiary | Lens | Mô tả ngắn bài toán | Ước lượng tổn thất |
|---|------------|------|---------------------|--------------------|
| 1 | **Xanh SM** | Tốn thời gian | Chuyên viên trực **ca đêm (22:00–06:00)** phải gom yêu cầu hỗ trợ tài xế từ **3 kênh rời rạc** (chat trong app tài xế, ghi âm hotline, nhóm Zalo khu vực), tự phân loại, tra cứu telemetry xe rồi soạn tay phản hồi tiếng Việt. | ~14 phút/lượt × ~180 lượt/đêm |
| 2 | **Xanh SM** | Lặp lại | Nhập tay lại **7 trường** thông tin sự cố (biển số, mã tài xế, nhóm lỗi, %pin, vị trí, hành động, kết quả) vào CRM sau khi đã trả lời tài xế — dữ liệu đã có sẵn trong đoạn chat nhưng không tự đổ vào hệ thống. | ~2 phút/lượt, lỗi nhập liệu ~8% |
| 3 | **VinFast** | Lặp lại | Đối soát hóa đơn phiên sạc giữa log trạm sạc đối tác và hệ thống thanh toán nội bộ; lệch phiên phải tra thủ công từng giao dịch. | ~6 giờ/tuần/nhân sự |
| 4 | **Vinhomes** | AI-upgrade | Phân loại và định tuyến phản ánh cư dân trên App Resident; phản hồi hiện rập khuôn, không bám nội dung khiếu nại cụ thể. | SLA phản hồi ~12 giờ |
| 5 | **Vinmec** | Pain từ người khác | Bác sĩ mất 20–30 phút soạn tóm tắt hồ sơ xuất viện cho mỗi bệnh nhân; bác sĩ phản ánh đây là gánh nặng hành chính lớn nhất cuối ca trực. | 20–30 phút/bệnh nhân |
| 6 | **Vinpearl / VinWonders** | AI-upgrade | Chatbot CSKH chỉ trả lời được câu hỏi mẫu về giá vé, không xử lý được câu hỏi ghép nhiều điều kiện (đoàn gia đình + combo + ngày lễ) nên đẩy hết sang tổng đài người. | ~35% hội thoại phải chuyển người |

**Nhận xét sau khi quét:** Bài toán #1 và #2 thực chất là **hai nửa của cùng một quy trình** (xử lý yêu cầu hỗ trợ tài xế → ghi log). Chúng có chung actor, chung dữ liệu đầu vào, và nếu giải thì giải được cùng lúc. Đây là lý do tôi ưu tiên cụm này khi chọn Deep-Dive.

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

## 🃏 Card #1 — Xanh SM: Xử lý yêu cầu hỗ trợ tài xế ca đêm

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                        │
│                                                              │
│ Bài toán (1 câu): Chuyên viên trực ca đêm phải đọc thủ công  │
│ yêu cầu hỗ trợ tài xế từ 3 kênh rời rạc, tự phân loại, tra   │
│ telemetry xe rồi soạn tay phản hồi tiếng Việt cho từng ca.   │
│                                                              │
│ Công ty thành viên: [x] Xanh SM (GSM)                        │
│                                                              │
│ Ai đang đau (Actor)?                                         │
│   - Chuyên viên Trung tâm Hỗ trợ Tài xế (4 người/ca đêm)     │
│   - Tài xế đang đứng chờ bên đường lúc 1 giờ sáng            │
│                                                              │
│ Workflow thủ công hiện tại (5 bước):                         │
│   1. Gom tin từ app chat + hotline + Zalo   (⏱ 2 phút)       │
│   → 2. Phân loại vào 1 trong 6 nhóm sự cố   (⏱ 2 phút)       │
│   → 3. Tra telemetry xe trên 2 dashboard 🔴 (⏱ 4 phút)       │
│   → 4. Soạn tay phản hồi + phương án 🔴     (⏱ 4 phút)       │
│   → 5. Gửi qua app + nhập 7 trường CRM      (⏱ 2 phút)       │
│                                                              │
│ Bước nào tốn thời gian/lỗi nhất?                             │
│   Bước 3 + Bước 4 (⏱ 8 phút/lượt — chiếm 57% tổng thời gian) │
│                                                              │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                        │
│   Bước 2 (phân loại + trích slot), Bước 3 (tự gọi API        │
│   telemetry theo biển số đã trích), Bước 4 (soạn bản nháp).  │
│   Bước 5 điền sẵn CRM từ chính draft đã duyệt.               │
│                                                              │
│ Đo thành công bằng gì (Metric có số)?                        │
│   - Trung vị thời gian xử lý: 14 phút ──> dưới 4 phút        │
│   - SLA phản hồi đầu ≤ 5 phút: 61% ──> ≥ 90%                 │
│   - Độ chính xác phân loại 6 nhóm: ≥ 92%                     │
│                                                              │
│ Quick Architecture: [x] LLM Feature                          │
│   (quy trình cố định, có HITL — chưa cần Agentic Loop)       │
└─────────────────────────────────────────────────────────────┘
```

---

## 🃏 Card #2 — Vinhomes: Định tuyến & soạn phản hồi phản ánh cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                        │
│                                                              │
│ Bài toán (1 câu): Phản ánh cư dân trên App Vinhomes Resident │
│ được định tuyến thủ công tới bộ phận xử lý và trả lời bằng   │
│ mẫu câu rập khuôn, không bám nội dung khiếu nại cụ thể.      │
│                                                              │
│ Công ty thành viên: [x] Vinhomes                             │
│                                                              │
│ Ai đang đau (Actor)?                                         │
│   Nhân viên CSKH Ban Quản lý tòa nhà; cư dân chờ phản hồi.   │
│                                                              │
│ Workflow thủ công hiện tại (4 bước):                         │
│   1. Đọc phản ánh trên App     → 2. Gán bộ phận phụ trách    │
│   → 3. Chờ bộ phận xử lý 🔴    → 4. Soạn phản hồi cư dân     │
│                                                              │
│ Bước nào tốn thời gian/lỗi nhất?                             │
│   Bước 3 — chờ luân chuyển giữa các bộ phận (⏱ ~12 giờ)      │
│                                                              │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 và Bước 4.      │
│                                                              │
│ Đo thành công bằng gì (Metric có số)?                        │
│   Giảm SLA phản hồi đầu tiên từ 12 giờ ──> dưới 2 giờ.       │
│                                                              │
│ Quick Architecture: [x] Rule  [x] LLM Feature                │
│   (Rule router cho phân loại an toàn + LLM soạn phản hồi)    │
└─────────────────────────────────────────────────────────────┘
```

---

## 🃏 Card #3 — Vinmec: Trợ lý soạn tóm tắt hồ sơ xuất viện

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                        │
│                                                              │
│ Bài toán (1 câu): Bác sĩ mất 20–30 phút soạn tay bản tóm tắt │
│ hồ sơ xuất viện cho mỗi bệnh nhân vào cuối ca trực.          │
│                                                              │
│ Công ty thành viên: [x] Vinmec                               │
│                                                              │
│ Ai đang đau (Actor)? Bác sĩ điều trị và điều dưỡng trưởng.   │
│                                                              │
│ Workflow thủ công hiện tại (4 bước):                         │
│   1. Mở lại toàn bộ bệnh án điện tử                          │
│   → 2. Lọc thông tin cần đưa vào tóm tắt 🔴                  │
│   → 3. Soạn tay bản tóm tắt 🔴                               │
│   → 4. Ký duyệt và bàn giao cho bệnh nhân                    │
│                                                              │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2+3 (⏱ 20–30 phút)     │
│                                                              │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 và Bước 3.      │
│                                                              │
│ Đo thành công bằng gì (Metric có số)?                        │
│   Giảm thời gian soạn từ 25 phút ──> dưới 8 phút.            │
│                                                              │
│ Quick Architecture: [x] LLM Feature (bắt buộc HITL bác sĩ ký)│
└─────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết định chọn bài toán Deep-Dive

**Chọn Card #1 — Xanh SM: Xử lý yêu cầu hỗ trợ tài xế ca đêm.**

### Lý do chọn
1. **Bottleneck nằm đúng vùng LLM mạnh:** 8/14 phút của quy trình là *đọc tin nhắn viết tắt, sai chính tả của tài xế* và *viết lại thành hướng dẫn tiếng Việt rõ ràng*. Rule-based code không làm được phần này.
2. **Ranh giới an toàn đo được và test được:** Rủi ro cụ thể (chỉ tài xế pin 2% đi 8km) có thể diễn đạt thành assertion chạy được trong code — xem [`starter-code/prompt_prototype.py`](starter-code/prompt_prototype.py).
3. **Giải một lần được hai bài:** Bản nháp đã duyệt chứa sẵn dữ liệu để auto-fill CRM, nên Card #1 kéo theo lời giải cho bài toán #2 trong bảng SCAN.

### Lý do loại các thẻ khác
* **Card #2 (Vinhomes):** Bottleneck thật là **thời gian chờ luân chuyển giữa các bộ phận (12 giờ)** — đây là bài toán *tái thiết kế quy trình và phân quyền*, không phải bài toán ngôn ngữ. Cắm LLM vào chỉ làm đẹp câu trả lời chứ không rút ngắn được 12 giờ chờ. Đây là ứng viên **NO-GO cho AI ở giai đoạn này**.
* **Card #3 (Vinmec):** Giá trị rất cao nhưng rủi ro sai sót thuộc nhóm **an toàn người bệnh**, yêu cầu quy trình kiểm định y khoa, phê duyệt hội đồng đạo đức và dữ liệu bệnh án nhạy cảm. Vượt xa phạm vi một lab 4 tiếng.

➡️ Phân tích chi tiết Card #1 được trình bày tại [`02-deep-dive-report.md`](02-deep-dive-report.md).
