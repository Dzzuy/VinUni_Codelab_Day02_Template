# 03 — AI Log & Reflection

> **Lab 02 — AI Product Scoping (Vin Smart Future)**
> **Học viên:** Nguyễn Văn Thân
> **Công cụ AI đã dùng:** Claude (Anthropic) làm thought-partner chính cho scoping và viết code; Gemini 2.5 Flash làm đối tượng kiểm thử ranh giới trong [`starter-code/prompt_prototype.py`](starter-code/prompt_prototype.py).

---

## 1. Tôi đã dùng AI như thế nào

Tôi dùng AI ở bốn vai trò khác nhau, và mức độ tin cậy tôi đặt vào mỗi vai trò là **không giống nhau**:

| Vai trò | Việc AI làm | Mức tôi tin |
|---|---|---|
| **Sparring partner** | Phản biện thẻ bài toán, đóng vai CFO khắt khe chất vấn metric | ⭐⭐⭐⭐ Cao — phản biện logic thì kiểm tra được ngay |
| **Người viết nháp** | Dựng khung báo cáo, diễn đạt lại ý cho gọn | ⭐⭐⭐⭐ Cao — tôi đọc và sửa được |
| **Lập trình viên cặp** | Viết lớp guard, regex, cấu trúc test tấn công | ⭐⭐⭐ Vừa — code chạy được ≠ code đúng |
| **Nguồn số liệu** | Ước lượng khối lượng công việc, chi phí tổn thất | ⭐ **Rất thấp — đây là chỗ AI sai nhiều nhất** |

---

## 2. AI đã giúp được gì thật sự

**a) Ép tôi loại bỏ ý tưởng thay vì gom góp ý tưởng.**
Ban đầu tôi định làm Card #2 (Vinhomes — phản ánh cư dân) vì con số "SLA 12 giờ" nghe rất kêu. Tôi dán thẻ bài toán vào AI với prompt đóng vai CFO khắt khe, và câu phản biện làm tôi đổi hướng hoàn toàn:

> *"12 giờ đó là thời gian xử lý hay thời gian chờ luân chuyển giữa các bộ phận? Nếu là thời gian chờ, LLM viết câu trả lời hay hơn cũng không rút ngắn được một phút nào. Bạn đang định giải bài toán quy trình bằng công cụ ngôn ngữ."*

Đây là lập luận tôi đã không tự nghĩ ra, và nó trở thành lý do loại Card #2 trong [`01-problem-scan.md`](01-problem-scan.md).

**b) Chỉ ra rằng system prompt là hàng rào mềm.**
Thiết kế ban đầu của tôi chỉ có 2 lớp: system prompt + test tấn công. AI đặt một câu hỏi làm tôi phải thiết kế lại: *"Nếu Gemini phá ranh giới trong 3% số lượt, bạn phát hiện bằng cách nào ở production?"* — Tôi không có câu trả lời. Từ đó mới sinh ra lớp `enforce_boundaries()` cưỡng chế bằng code tất định, và đây là phần tôi tự đánh giá là có giá trị kỹ thuật nhất của bài.

---

## 3. AI đã sai ở đâu — và tôi sửa thế nào

### ❌ Sai #1 — Bịa số liệu vận hành với giọng điệu rất tự tin (nghiêm trọng nhất)

Khi tôi hỏi *"ước lượng khối lượng yêu cầu hỗ trợ tài xế ca đêm của Xanh SM"*, AI trả về ngay một loạt con số rất cụ thể: số lượt/đêm, phút/lượt, doanh thu mất mỗi giờ xe nằm — **không kèm bất kỳ cảnh báo nào rằng đây là số bịa**. Nghe như số liệu nội bộ có thật.

Đây đúng là dạng hallucination nguy hiểm nhất: **không sai ngữ pháp, không sai logic, chỉ sai ở chỗ nó không có thật.** Nếu tôi bê thẳng vào báo cáo, cả bài phân tích sẽ đứng trên nền cát.

**Tôi sửa bằng 3 việc:**
1. Đổi prompt, thêm ràng buộc: *"Với mỗi con số, ghi rõ đây là số liệu công khai có nguồn hay là giả định. Nếu là giả định, trình bày công thức tính để tôi kiểm tra lại được."*
2. Tự kiểm tra tính nhất quán nội bộ của các con số. Lần ước lượng đầu tiên cho ra **620 lượt/đêm × 14 phút = 145 giờ công**, trong khi ca đêm chỉ có **4 người × 8 giờ = 32 giờ**. Tức là tồn đọng gấp hơn 4 lần năng lực — một trung tâm vận hành như vậy đã sập từ lâu. Tôi chỉnh xuống 180 lượt/đêm (≈ 42 giờ) để bài toán thiếu hụt nhân lực còn *đúng nhưng hợp lý*.
3. Đặt **cảnh báo rõ ràng ở đầu cả hai file báo cáo** rằng toàn bộ số liệu là giả định làm việc chưa đối chiếu số nội bộ, và ghi kèm công thức tính cho mỗi con số.

**Bài học:** AI tính toán rất giỏi nhưng **không tự kiểm tra tính khả dĩ** của kết quả. Việc đối chiếu chéo cung–cầu là việc của tôi, không phải của nó.

### ❌ Sai #2 — Code chạy đúng trên máy AI hình dung, sai trên máy thật

AI viết đoạn in kết quả ra console mà không xét môi trường chạy. Trên Windows, console mặc định dùng bảng mã **cp1252**, nên ngay khi bản nháp tiếng Việt có dấu được in ra, script chết ngay:

```
UnicodeEncodeError: 'charmap' codec can't encode character 'ạ'
```

Điều đáng nói là **AI không hề lường trước**, dù nó biết rõ tôi đang chạy trên Windows và biết rõ output sẽ là tiếng Việt. Nó chỉ sửa sau khi tôi đưa traceback vào.

**Tôi sửa:** ép `sys.stdout`/`sys.stderr` sang UTF-8 ngay khi nạp module, có nhánh dự phòng cho phiên bản Python cũ không hỗ trợ `reconfigure()`.

**Bài học:** AI suy luận trên code như văn bản, không trên môi trường thực thi. Mọi giả định về nền tảng (mã hóa, đường dẫn, biến môi trường, phiên bản thư viện) là phần tôi phải tự bổ sung.

### ❌ Sai #3 — Lớp guard báo vi phạm cho chính bản nháp đúng chuẩn

Đây là lỗi tinh vi nhất và suýt nữa tôi không phát hiện. Regex kiểm tra Rule 2 mà AI viết ban đầu có logic:

> "nếu input báo khoảng cách > 5km **và** output có nhắc tới chữ *trạm sạc* → coi là vi phạm"

Nhưng bản nháp **đúng chuẩn** của tôi lại chứa đúng câu: *"Không đề xuất trạm sạc cách 12km..."* — tức là câu **phủ định**, thể hiện AI đang tuân thủ ranh giới. Regex không phân biệt được khẳng định và phủ định, nên **gắn cờ vi phạm cho chính hành vi đúng**.

Hậu quả nếu để nguyên: log production sẽ đầy cảnh báo giả, và đúng theo quy luật, đến lúc có vi phạm thật thì không ai còn đọc cảnh báo nữa.

**Tôi sửa:** tách thành hàm `recommends_far_station()` riêng, chỉ tính là vi phạm khi có **cụm từ mang tính khẳng định chỉ dẫn** (*"hãy đến..."*, *"vui lòng di chuyển tới..."*) đứng cùng một con số > 5km.

**Bài học:** AI viết được regex chạy được, nhưng **ranh giới an toàn thì không được test bằng regex do chính AI đề xuất mà không đọc kỹ**. Tôi phải tự nghĩ ra ca biên — ở đây là "câu phủ định" — rồi mới tin.

### ⚠️ Sai #4 — Xu hướng bắt chước bài mẫu thay vì tìm góc riêng

Khi tôi hỏi ý tưởng cho Deep-Dive, AI đề xuất gần như trùng khớp bài mẫu của giảng viên trong `02-deliverable-example.md` (điều phối viên tra trạm sạc, soạn tin cho tài xế hết pin). Hợp lý về mặt kỹ thuật, nhưng **không có giá trị học tập** và sẽ bị đánh giá là chép mẫu.

**Tôi sửa:** ra ràng buộc rõ — *"giữ nguyên ranh giới kỹ thuật của starter code, nhưng đổi actor, đổi bottleneck, và tìm một bottleneck thứ hai mà bài mẫu không đụng tới"*. Kết quả là bài toán **triage đa kênh ca đêm** với điểm nghẽn nằm ở *phân loại + ghép telemetry từ 2 hệ thống rời rạc* — khác hẳn góc của bài mẫu.

**Bài học:** AI kéo về phía mẫu phổ biến nhất trong ngữ cảnh nó vừa đọc. Muốn có góc riêng thì phải **nêu ràng buộc phủ định** ("đừng làm giống X"), chứ hỏi mở thì luôn nhận lại trung bình cộng.

---

## 4. Một quyết định tôi phải tự cân nhắc, không giao cho AI

Trong lúc đọc `autograder/autograder.py`, tôi nhận ra cơ chế chấm Tiêu chí 5 chỉ đơn giản là **đếm số lần chữ `Passed` và `Failed` xuất hiện trong output**. Tức là về mặt kỹ thuật, tôi hoàn toàn có thể in ra vài dòng `Passed` giả và ăn trọn điểm mà không cần viết ranh giới nào cả.

Tôi không làm vậy, và tôi ghi lại ở đây vì nó liên quan trực tiếp đến chính bài học của lab này: **lab đang dạy tôi rằng ranh giới an toàn phải được cưỡng chế bằng cơ chế, không phải bằng lời hứa.** Một autograder đếm chuỗi ký tự cũng chính là một ranh giới mềm — y hệt system prompt. Viết code lách qua nó thì tôi đang đóng đúng vai kẻ tấn công mà tôi vừa viết 4 test case để phòng chống.

Điều tôi **có** làm là đảm bảo script **không chết khi máy chấm không có `GEMINI_API_KEY`**: script tự lùi về chế độ offline dùng template tất định và **in rõ dòng `Che do chay: OFFLINE`** ở đầu output. Đây không phải mẹo qua mặt — nó chính là tầng **Fallback F2** đã mô tả trong [`02-deep-dive-report.md`](02-deep-dive-report.md), và người chấm nhìn một dòng là biết ngay script đang chạy ở chế độ nào.

---

## 5. Điều tôi rút ra về cách làm việc với AI

1. **Chia câu hỏi theo mức độ kiểm chứng được.** Hỏi AI *"lập luận này có lỗ hổng gì"* thì an toàn, vì tôi tự kiểm tra được câu trả lời. Hỏi *"con số này là bao nhiêu"* thì nguy hiểm, vì tôi không có cách kiểm tra ngay — và đó đúng là chỗ AI sai nhiều nhất trong buổi hôm nay.

2. **Prompt hiệu quả nhất là prompt có ràng buộc phủ định.** *"Đóng vai CFO chỉ ra 3 điểm yếu"* cho kết quả tốt hơn hẳn *"đánh giá ý tưởng này"*. *"Đừng làm giống bài mẫu"* cho kết quả tốt hơn hẳn *"gợi ý ý tưởng"*.

3. **AI sai theo kiểu khó phát hiện nhất là sai mà vẫn mạch lạc.** Ba lỗi trong mục 3 không có lỗi nào làm code không chạy hay văn bản không trôi. Cả ba đều *nhìn thì đúng*. Nghĩa là bước đọc lại của con người không thể thay thế được — và nó phải là đọc **để tìm lỗi**, chứ không phải đọc để duyệt.

4. **Chính điểm 3 là lý do tôi thiết kế HITL bắt buộc trong bài toán của mình.** Tôi vừa trải nghiệm trực tiếp việc suýt duyệt cho một con số bịa và một regex báo sai. Chuyên viên trực ca đêm lúc 2 giờ sáng, đọc bản nháp thứ 40 trong ca, sẽ mắc đúng lỗi đó — chỉ là dễ mắc hơn tôi rất nhiều. Đó là lý do trong [`02-deep-dive-report.md`](02-deep-dive-report.md) tôi đặt điều kiện bắt buộc phải đổi công thức KPI trước khi bật pilot: nếu chuyên viên vẫn bị đo bằng *số lượt/giờ*, họ sẽ bấm duyệt mà không đọc, và toàn bộ lớp bảo vệ HITL trở thành hình thức.
