# 🏗️ Deep-Dive Report: Xanh SM Fare Dispute Assessment

**Mảng kinh doanh lựa chọn:** GSM (Xanh SM) — Thẩm định & Xử lý khiếu nại cước phí cuốc xe  
**Đơn vị phát triển:** Vin Smart Future (Vingroup)  

---

## 3.1. Current-State Workflow Mapping

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

## 3.2. Problem Statement (6-field) & Metrics

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Chuyên viên Chăm sóc Khách hàng (CSKH) & Chuyên viên Kế toán vận hành Xanh SM. |
| **2. Current Workflow** | Khi hành khách hoặc tài xế khiếu nại cuốc xe bị tính sai cước (do đi chệch đường, kẹt xe kéo dài, hoặc ứng dụng bị lỗi GPS), CSKH mở App điều vận tra cứu tọa độ GPS thủ công, đối chiếu với bảng giá cước loại xe (VF5/VF8/VF9), dùng Excel tính tiền chênh lệch, rồi soạn email giải trình và lệnh hoàn tiền gửi Kế toán phê duyệt. 5 bước thủ công, mất 15 phút/vé. |
| **3. Bottleneck** | Bước 2, 3 & 4 (mất 11 phút): Tra cứu thủ công log GPS dài, xác định đoạn đường tài xế chạy sai tuyến và áp bảng cước phí động (Dynamic Pricing) theo khung giờ để tính chính xác số tiền cần hoàn. |
| **4. Business Impact** | Mỗi ngày nhận ~150 khiếu nại cước phí tại Hà Nội & TP.HCM. Gây lãng phí ~35 giờ làm việc/ngày của nhân sự CSKH/Kế toán. Thời gian chờ hoàn tiền kéo dài 2-3 ngày khiến chỉ số hài lòng khách hàng (CSAT) giảm 18% và tăng nguy cơ rời bỏ dịch vụ. |
| **5. Success Metric** | 1. Giảm tổng thời gian xử lý khiếu nại cước phí từ 15 phút xuống dưới 2 phút/vé (Efficiency).<br>2. Tỉ lệ tính toán tiền hoàn tiền chính xác và soạn nội dung giải thích đạt 99% (Quality). |
| **6. Operational Boundary** | AI được phép: Truy xuất API lịch trình GPS, API công thức cước Xanh SM, tự động tính toán tiền chênh lệch và tự động soạn thảo (draft) email/SMS phản hồi.<br>**CẤM / HITL:** AI TUYỆT ĐỐI không được tự động phát lệnh hoàn tiền thực tế qua cổng thanh toán/ví điện tử mà không có nhân viên CSKH click duyệt (Bắt buộc HITL); không được phê duyệt số tiền hoàn vượt quá 500,000 VNĐ/cuốc mà không chuyển tiếp cho Trưởng nhóm CSKH review. |

---

## 3.3. Future-State Flow & AI Fit

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

## 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist:
1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? (Dữ liệu GPS & lịch sử chuyến đi của Xanh SM).
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? (Có, CSKH phải click duyệt trước khi chuyển tiền hoàn).
3. [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? (Bộ phận CSKH & Kế toán sẵn lòng giảm bớt tác vụ tra cứu Excel thủ công).

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.

**Justification:**
Dự án đạt mức độ **GO** vì bài toán có quy trình nghiệp vụ rõ ràng, giải pháp công nghệ đơn giản mà hiệu quả cao (LLM Feature kết hợp Rule-based Calculation), metric đo lường khả thi và có số liệu cụ thể (giảm từ 15 phút xuống 2 phút). Rủi ro tài chính được kiểm soát tuyệt đối nhờ cơ chế HITL (nhân viên duyệt lệnh hoàn tiền) và ranh giới tự động chuyển Trưởng nhóm với các giao dịch trên 500,000 VNĐ. Dự án giúp tiết kiệm ~35 giờ làm việc/ngày cho tập đoàn.
