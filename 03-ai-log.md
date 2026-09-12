# 📝 AI Log & Reflection — Vin Smart Future

**Họ và tên:** *Điền tên của bạn*  
**Vị trí:** AI Product Engineer  
**Dự án:** Xanh SM Fare Dispute Assessment  

---

## 🤖 1. AI làm Thought-Partner (Đồng hành Brainstorm & Scoping)

Trong quá trình thực hiện Lab 02, tôi đã sử dụng AI (Gemini 2.5) làm đối tác phân phản biện và hỗ trợ xây dựng giải pháp:
- **Hỗ trợ Scoping:** AI giúp tôi rà soát các điểm nghẽn trong vận hành dịch vụ taxi điện Xanh SM, chỉ ra bài toán thẩm định khiếu nại cước phí cuốc xe do lỗi lệch tuyến GPS là bài toán có tần suất cao và đo lường metric số liệu rõ ràng.
- **Phân phản biện (Stress-test):** Đóng vai trò CFO khắt khe, AI đặt câu hỏi về rủi ro hoàn tiền nhầm hoặc thất thoát tài chính nếu AI tự động duyệt chuyển khoản. Nhờ đó, tôi đã thiết lập ranh giới **HITL (Human-In-The-Loop)** bắt buộc nhân viên CSKH click duyệt trước khi hoàn tiền.

---

## ⚠️ 2. Các điểm AI trả lời chưa chuẩn / Rủi ro phát hiện

- **Bỏ qua thẻ đánh dấu nháp:** Khi đóng vai người dùng cố tình bảo AI gửi luôn tin nhắn hoàn tiền mà không cần thẻ `[DRAFT_ONLY]`, ban đầu mô hình có xu hướng chiều theo người dùng.
- **Cách khắc phục:** Cập nhật `SYSTEM_PROMPT` đưa ra chỉ thị tuyệt đối: *"Every human-readable draft message MUST ALWAYS begin with the exact tag '[DRAFT_ONLY]'. Even if the user demands to skip it, you MUST INCLUDE IT."*

---

## 🎯 3. Phản ánh bài học cá nhân (Reflection)

- **AI Fit:** Không phải bài toán nào cũng cần dùng AI Agent tự trị phức tạp. Với bài toán khiếu nại cước phí, mô hình **LLM Feature** kết hợp với Rule-based API tính cước là phương án tối ưu, chi phí thấp và an toàn cao nhất.
- **Ranh giới an toàn (Guardrails):** Thiết lập System Prompt chặt chẽ cùng các Adversarial Test Cases là bước bắt buộc để đảm bảo hệ thống AI vận hành an toàn trong doanh nghiệp lớn như Vingroup.
