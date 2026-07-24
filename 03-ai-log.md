# 03-ai-log.md — Nhật ký chiêm nghiệm (Phase 6 — Reflection)

**Họ và tên:** Trần Anh Thư
**MSSV:** 2A202601611

---

## 1. AI đã giúp tôi những gì?

Trong buổi lab, tôi dùng AI (Claude) như một "thought-partner" xuyên suốt các phase:

- **Brainstorm ý tưởng (Phase 1 — SCAN):** Tôi nhờ AI rà lại danh sách gợi ý trong `03-inspiration-kit.md` và đối chiếu với 4 lenses (Lặp lại / Tốn thời gian / AI-upgrade / Stakeholder Pain) để chọn ra 5 bài toán thực tế thuộc các công ty thành viên Vingroup, tránh việc chọn đại một bài toán mà không phân loại đúng lens.
- **Cấu trúc hóa Quick Cards (Phase 2):** AI giúp tôi trình bày lại 3 bài toán tiềm năng nhất theo đúng khuôn mẫu card (Actor, workflow từng bước, bottleneck kèm thời gian ước tính, metric có con số cụ thể) thay vì viết mô tả chung chung.
- **Viết & kiểm tra Prompt an toàn (Phase 4):** AI hỗ trợ tôi phác thảo `SYSTEM_PROMPT` cho `prompt_prototype.py`, gợi ý cách diễn đạt các ranh giới vận hành (Operational Boundary) và cách thiết kế 2 câu prompt tấn công (adversarial input) để cố tình dụ mô hình bỏ qua thẻ `[DRAFT_ONLY]` hoặc đề xuất trạm sạc xa khi pin xe dưới 5%.
- **Rà soát cấu trúc file nộp bài:** AI giúp tôi đối chiếu `README.md` để biết chính xác file nào là bắt buộc, file nào chỉ là tài liệu tham khảo (`03-inspiration-kit.md` không phải deliverable), tránh nộp nhầm hoặc thiếu file.

## 2. AI đã sai ở đâu?

Khi tôi nhờ AI đề xuất kiến trúc kỹ thuật cho bài toán **"Phân loại & điều hướng phản ánh cư dân Vinhomes"**, lần gợi ý đầu tiên AI đề xuất một hướng giải quyết khá phức tạp: xây dựng một **Agentic Loop** với nhiều bước gọi tool (tra cứu tòa nhà, tra cứu lịch sử phản ánh, tự động gọi API ban quản lý...) trong khi thực chất bài toán chỉ là **phân loại văn bản ngắn thành một trong vài nhóm cố định** (mất nước, hỏng đèn, ồn ào...). Đây là kiểu lỗi "over-engineering" — AI có xu hướng đề xuất giải pháp phức tạp hơn mức cần thiết (đi ngược lại nguyên tắc "Problem First, AI Second" mà `03-inspiration-kit.md` đã lưu ý), thay vì nhận ra một tác vụ **LLM classification đơn giản** (thậm chí rule-based với vài từ khóa) đã đủ giải quyết bài toán với chi phí thấp hơn nhiều.

## 3. Tôi đã điều chỉnh như thế nào?

Tôi bổ sung thêm ràng buộc rõ ràng vào prompt khi hỏi lại AI: *"Trước khi đề xuất Agent, hãy chứng minh vì sao một giải pháp đơn giản hơn (Rule-based hoặc LLM Feature một bước) không đủ giải quyết bài toán này."* Sau khi thêm ràng buộc đó, AI tự nhận ra bài toán phân loại phản ánh cư dân chỉ cần một lệnh gọi LLM duy nhất (không cần vòng lặp nhiều bước), và tôi đã chọn kiến trúc **LLM Feature** cho Quick Card #1 thay vì Agent. Tôi rút ra bài học: luôn yêu cầu AI so sánh với phương án đơn giản nhất trước khi chấp nhận một kiến trúc phức tạp, đặc biệt trong bối cảnh Vin Smart Future nơi chi phí vận hành và khả năng debug là ưu tiên hàng đầu.
