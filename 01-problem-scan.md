# 🔍 Phase 1 — SCAN & Phase 2 — QUICK-ASSESS

**Họ và tên:** *Điền tên của bạn*  
**Công ty / Đơn vị:** Vin Smart Future (Vingroup)  

---

## 🔍 Phase 1 — SCAN: 5 Bài Toán Vận Hành Thực Tế (Vingroup)

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Tốn thời gian | Phẩm định và tự động xử lý khiếu nại tính sai giá tiền cuốc xe / nhầm tuyến đường từ hành khách và tài xế. |
| 2 | **VinFast** | Lặp lại | Chẩn đoán sớm và phân loại mã lỗi ECU/Telematics từ dữ liệu cảm biến xe điện gửi về Trung tâm Bảo hành. |
| 3 | **Vinhomes** | Pain từ người khác | Thẩm định và phê duyệt đơn đăng ký thi công/sửa chữa nội thất của cư dân trên App Vinhomes Resident. |
| 4 | **Vinmec** | AI-upgrade | Đối chiếu tương tác thuốc (Drug-Drug Interaction) và kiểm tra tiền sử dị ứng tự động từ đơn thuốc khám lâm sàng. |
| 5 | **Vinpearl** | Tốn thời gian | Xử lý và phân loại tự động yêu cầu hoàn/đổi ngày phòng khách sạn & vé VinWonders do thời tiết bất khả kháng (mưa bão). |

---

## 🃏 Phase 2 — QUICK-ASSESS: Top 3 Quick Problem Cards

```text
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
│ QUICK PROBLEM CARD #2                                       │
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
│ Quick Architecture: [x] LLM Feature (RAG + Log Analyzer)    │
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
