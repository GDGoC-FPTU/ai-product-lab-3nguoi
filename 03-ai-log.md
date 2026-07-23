# AI Log & Reflection

## 1. AI đã giúp gì?

Trong buổi lab, tôi đã sử dụng AI như một trợ lý tư duy để brainstorm các bài toán vận hành có thể tối ưu bằng AI trong hệ sinh thái Vingroup. AI hỗ trợ tôi sắp xếp các pain point theo bốn lens, chọn bài toán nào có khả năng triển khai tốt nhất, và cụ thể hóa workflow hiện tại thành một mô hình rõ ràng hơn.

Bên cạnh đó, tôi cũng dùng AI để stress-test prompt và định hình ranh giới an toàn của hệ thống. Điều này giúp tôi hình dung rõ hơn rằng AI không nên làm mọi thứ trong quy trình mà chỉ nên hỗ trợ ở các bước có tính cấu trúc và cần review từ con người.

## 2. AI sai gì?

Một điểm sai mà AI có thể mắc phải là đề xuất hành động quá tự tin trong tình huống nguy hiểm, ví dụ như gợi ý trạm sạc quá xa khi pin đang dưới mức an toàn. Nếu không có prompt ràng buộc chặt chẽ, model có thể bỏ qua điều kiện an toàn và đưa ra câu trả lời “không đáng tin cậy”.

Đây là nơi cần có hệ thống kiểm soát bằng prompt và human-in-the-loop.

## 3. Tôi đã sửa đổi như thế nào?

Tôi bổ sung các ràng buộc rõ ràng vào system prompt:
- luôn bắt đầu bằng `[DRAFT_ONLY]` khi tạo draft,
- nếu pin < 5%, không đề xuất trạm sạc xa hơn 5km,
- phải chuyển sang `dispatch_mobile_charger` trong trường hợp nguy hiểm,
- không được tự động gửi tin mà không có con người phê duyệt.

Việc này giúp model phản hồi theo hướng an toàn hơn và tập trung vào chức năng hỗ trợ, không phải tự động hóa độc lập.
