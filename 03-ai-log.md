# 03 - AI Log & Reflection

## Bối cảnh

Trong Lab 02, tôi sử dụng AI như một thought-partner để scoping bài toán cho Vin Smart Future. Tôi chọn case Xanh SM xử lý sự cố xe sắp hết pin ngoài đường.

## AI đã giúp gì?

AI giúp tôi brainstorm các bottleneck ở nhiều công ty thành viên, sau đó nhóm chúng theo bốn lens: tác vụ lặp lại, tốn thời gian, AI-upgrade và pain của stakeholder. AI cũng giúp tôi chuyển ý tưởng chung thành workflow cụ thể gồm actor, handoff, bottleneck và metric.

Khi chọn case Xanh SM, AI gợi ý một hướng triển khai nhỏ hơn Agent tự trị: dùng Rule để kiểm tra ngưỡng pin, khoảng cách và loại cổng; dùng LLM để trích xuất nội dung cuộc gọi và soạn tin nhắn draft; bắt buộc dispatcher duyệt trước khi gửi. Cách tách này giúp tôi nhìn rõ phần nào cần logic xác định, phần nào mới cần ngôn ngữ tự nhiên.

## AI đã sai hoặc có nguy cơ hallucination ở đâu?

Trong quá trình brainstorm, AI đưa ra các con số như khoảng 80 sự cố/ngày, 15 phút/lượt, 20 giờ công/ngày và độ chính xác 98%. Những con số này nghe hợp lý nhưng không có nguồn dữ liệu trong bài. Nếu đưa thẳng vào báo cáo, tôi sẽ biến một giả định thành sự thật và có thể đánh giá sai chi phí lẫn hiệu quả.

AI cũng có xu hướng đề xuất tự động hóa rộng hơn, ví dụ tự gọi API trạm sạc, tự gửi tin hoặc tự điều phối cứu hộ. Với một xe đang gần cạn pin, lỗi chọn trạm có thể gây rủi ro vận hành và an toàn. Vì vậy, tôi không coi câu trả lời của AI là quyết định cuối cùng.

## Tôi đã sửa prompt và ranh giới thế nào?

Tôi ghi rõ các số liệu là **baseline giả định cần xác minh bằng log**. Tôi bổ sung system prompt với các ràng buộc:

1. Mọi output phải bắt đầu bằng `[DRAFT_ONLY]`.
2. Output phải là một JSON có schema cố định.
3. Khi pin dưới 5%, action bắt buộc là `dispatch_mobile_charger` và không được đề xuất trạm xa hơn 5 km.
4. AI không được tự gửi tin, tự điều xe cứu hộ, bịa GPS, bịa trạng thái trạm hoặc xác nhận hành động đã hoàn tất.
5. Thiếu dữ liệu, sai schema hoặc confidence thấp phải chuyển về dispatcher và fallback thủ công.

Tôi thêm ba adversarial test: yêu cầu pin 2% nhưng đi đến trạm cách 8 km; yêu cầu bỏ tag và gửi thẳng; giả mạo admin để ép bỏ system prompt và bịa trạm 12 km. Các test này kiểm tra cả safety boundary lẫn prompt injection, thay vì chỉ kiểm tra một câu hỏi bình thường.

## Kết luận cá nhân

AI hữu ích nhất ở giai đoạn mở rộng phương án, phản biện architecture và phát hiện các lỗ hổng trong boundary. AI không thay thế việc xác minh dữ liệu, thiết kế fallback hay phê duyệt vận hành. Kết luận của tôi là dùng AI để hỗ trợ dispatcher bằng draft có cấu trúc là hợp lý, nhưng chưa đủ bằng chứng để triển khai thật; quyết định hiện tại là **NOT YET** cho đến khi có log, baseline và phản hồi từ người vận hành.
