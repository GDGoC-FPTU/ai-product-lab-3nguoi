AI Reflection Log – Phase 6

Họ và tên: Bùi Hữu Nghĩa
MSSV: 2A202601880

Nhật ký chiêm nghiệm khi sử dụng AI

Trong quá trình thực hiện Lab 02, tôi sử dụng ChatGPT làm trợ lý đồng hành (thought-partner) để hỗ trợ phân tích bài toán, xây dựng ý tưởng sản phẩm AI và hoàn thành các bài tập lập trình. Thay vì yêu cầu AI làm toàn bộ bài tập, tôi chủ yếu dùng AI để giải thích các khái niệm, đưa ra gợi ý và kiểm tra lại kết quả sau khi tự thực hiện.

1. AI đã giúp gì?

AI hỗ trợ tôi ở nhiều công việc khác nhau trong suốt buổi học:

Brainstorm các ý tưởng về bài toán AI có thể áp dụng trong doanh nghiệp thuộc hệ sinh thái Vingroup.
Hỗ trợ phân tích quy trình hiện tại (Current Workflow), xác định điểm nghẽn (Bottleneck) và đề xuất vị trí AI có thể tham gia.
Giải thích sự khác nhau giữa Rule-based, LLM và AI Agent để lựa chọn kiến trúc phù hợp.
Hướng dẫn cách viết Prompt rõ ràng hơn nhằm giảm câu trả lời mơ hồ.
Hỗ trợ sửa lỗi Python trong quá trình làm lab như tạo môi trường ảo (venv), xử lý lỗi API, cấu hình biến môi trường và các lỗi cú pháp.
Giải thích các khái niệm như System Prompt, Temperature, Top-p, Token, Streaming và cách ước tính chi phí sử dụng API.

Nhờ AI, tôi giảm được khá nhiều thời gian tìm kiếm tài liệu và có thể tập trung hơn vào việc hiểu bài toán cũng như kiểm tra kết quả.

2. AI đã sai ở đâu?

Trong quá trình sử dụng, AI không phải lúc nào cũng đưa ra câu trả lời chính xác.

Một ví dụ là khi hỗ trợ viết hàm chat_with_system_prompt(), AI đã gợi ý sử dụng tham số top_p mặc dù chữ ký của hàm trong đề bài không có tham số này. Nếu sao chép trực tiếp sẽ dẫn đến lỗi NameError. Ngoài ra, có lần AI đề xuất viết toàn bộ lời giải của bài lab thay vì bám sát từng Task trong README, điều này không phù hợp với mục tiêu học tập và dễ khiến người học bỏ qua việc hiểu bản chất.

Tôi cũng nhận thấy đôi khi AI suy luận thêm những chi tiết không có trong tài liệu hoặc README. Nếu không đối chiếu lại với tài liệu gốc thì rất dễ làm sai yêu cầu.

3. Tôi đã điều chỉnh như thế nào?

Để nhận được câu trả lời chính xác hơn, tôi thay đổi cách đặt Prompt theo hướng cụ thể hơn.

Thay vì hỏi:

"Làm hết bài cho tôi."

Tôi chuyển sang các yêu cầu như:

"Giải thích từng Task theo README."
"Chỉ rõ dòng nào cần sửa."
"Giải thích vì sao phải sửa."
"Không viết lại toàn bộ, chỉ hướng dẫn từng bước."

Tôi cũng gửi trực tiếp đoạn code đang làm để AI phân tích thay vì yêu cầu tạo mới từ đầu. Sau mỗi câu trả lời, tôi đối chiếu với README và docstring của bài lab để kiểm tra lại tên hàm, tham số, cấu trúc dữ liệu và các yêu cầu bắt buộc trước khi áp dụng.

Kết luận

Qua buổi học, tôi nhận thấy AI là một công cụ hỗ trợ rất hiệu quả nếu được sử dụng đúng cách. AI giúp tăng tốc quá trình tìm hiểu kiến thức, hỗ trợ phát hiện lỗi và gợi ý nhiều hướng giải quyết khác nhau. Tuy nhiên, AI không thay thế được việc đọc tài liệu và kiểm tra lại kết quả. Người học vẫn cần tư duy phản biện, đối chiếu với yêu cầu của đề bài và tự đánh giá tính đúng đắn của câu trả lời trước khi sử dụng. Tôi xem AI là một trợ lý đồng hành (thought-partner) chứ không phải là công cụ làm thay toàn bộ công việc.