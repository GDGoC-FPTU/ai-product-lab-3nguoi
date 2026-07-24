# Problem Scan — Vin Smart Future

> **Bài cá nhân:** Phase 1 (SCAN) và Phase 2 (QUICK-ASSESS).

## 🏛️ Bối cảnh: Tôi đang tìm bài toán gì?

Trong vai trò **AI Product Engineer tại Vin Smart Future**, tôi quét các quy trình vận hành của những công ty thành viên Vingroup để tìm bottleneck có actor rõ ràng, workflow đo được và ranh giới triển khai an toàn.

> **Quy ước số liệu:** Các số ghi **“ước tính”** là giả thuyết baseline để scoping, không phải số liệu nội bộ do Vingroup công bố. Trước khi làm prototype, cần kiểm chứng bằng log và đo thời gian trên tối thiểu 100 trường hợp thực tế. Các ngưỡng trong phần metric là **mục tiêu pilot**, không phải kết quả đã đạt được.

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Sử dụng 4 thấu kính để quét các bottleneck vận hành. Mỗi dòng chọn **một lens chính** phản ánh lý do mạnh nhất để đưa bài toán vào danh sách.

|   # | Công ty thành viên        | Lens chính           | Mô tả ngắn bài toán                                                                                                                                                                                         |
| --: | ------------------------- | -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|   1 | **Vinhomes**              | **Stakeholder Pain** | Nhân viên CSKH/Ban Quản lý phải đọc nội dung tự do từ nhiều kênh, xác định mức khẩn cấp rồi chuyển phản ánh của cư dân đến đúng bộ phận; cư dân phải chờ nếu ticket thiếu dữ kiện hoặc bị chuyển sai tuyến. |
|   2 | **Vinmec**                | **Tốn thời gian**    | Nhân viên bảo hiểm/thu ngân phải tiền kiểm thư bảo lãnh, giấy tờ định danh, thẻ bảo hiểm, kết quả khám và phạm vi quyền lợi trước khi gửi hồ sơ xác nhận viện phí.                                          |
|   3 | **Vinpearl / VinWonders** | **AI-upgrade**       | FAQ và câu trả lời mẫu khó xử lý các yêu cầu có ngữ cảnh như phòng liền kề, tầng cao, trang trí phòng hoặc thay đổi dịch vụ; nhân viên phải tra booking, tồn chỗ và hỏi nhiều bộ phận.                      |
|   4 | **VinFast**               | **Lặp lại**          | Chuyên viên đối soát phải so khớp bảng kê/hóa đơn của đối tác sạc với log phiên sạc, biểu giá và mã giao dịch, sau đó tách các trường hợp chênh lệch để kiểm tra.                                           |
|   5 | **Xanh SM (GSM)**         | **Stakeholder Pain** | Tài xế và khách mất thời gian gọi qua lại khi mô tả điểm đón không rõ hoặc ghim GPS rơi vào sai cổng; điều phối viên phải đọc ghi chú rồi xác định lại điểm đón thủ công.                                   |
|   6 | **Vinmec**                | **Tốn thời gian**    | Bác sĩ phải tổng hợp chẩn đoán, thuốc, kết quả xét nghiệm và hướng dẫn theo dõi từ nhiều phần của bệnh án để soạn tóm tắt xuất viện dễ hiểu cho bệnh nhân.                                                  |
|   7 | **Vinpearl**              | **Lặp lại**          | Nhân viên trải nghiệm khách hàng phải đọc và gắn nhãn thủ công review từ nhiều kênh để phát hiện các phàn nàn khẩn cấp về vệ sinh, an toàn hoặc chất lượng dịch vụ.                                         |
|   8 | **VinFast**               | **AI-upgrade**       | Mô tả lỗi xe bằng ngôn ngữ đời thường của khách hàng khó ánh xạ trực tiếp sang nhóm lỗi kỹ thuật, khiến cố vấn dịch vụ phải hỏi lại và tra tài liệu trước khi chuyển kỹ thuật viên.                         |

## Lựa chọn top 3

Ba bài toán được đưa vào QUICK-ASSESS là **#1 Vinhomes**, **#2 Vinmec** và **#3 Vinpearl** vì có actor rõ, quy trình lặp lại với đầu vào xác định được, metric có thể đo trong pilot và đại diện cho ba lựa chọn kiến trúc khác nhau: **LLM, Rule và Agent**.

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

## Quick Problem Card #1 — Phân loại và chuyển tuyến yêu cầu cư dân

| Trường                            | Nội dung                                                                                                                                                                                           |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bài toán (1 câu)**              | Nhân viên CSKH/Ban Quản lý Vinhomes phải đọc, phân loại mức ưu tiên và chuyển thủ công yêu cầu tự do của cư dân, làm tăng thời gian xử lý ban đầu và nguy cơ chuyển sai bộ phận.                   |
| **Công ty thành viên**            | [ ] VinFast &nbsp; [ ] Xanh SM &nbsp; [x] **Vinhomes** &nbsp; [ ] Vinmec &nbsp; [ ] Khác                                                                                                           |
| **Ai đang đau (Actor/Operator)?** | **Actor chính:** nhân viên CSKH/Ban Quản lý tiếp nhận và điều phối ticket. **Stakeholder bị ảnh hưởng:** cư dân chờ phản hồi và đội kỹ thuật/an ninh/vệ sinh nhận ticket thiếu hoặc sai thông tin. |

### Workflow thủ công hiện tại

```text
[1. Nhận yêu cầu từ app/điện thoại/email — 0,5 phút]
                         ↓
[2. Đọc mô tả, ảnh và hỏi bổ sung dữ kiện — 3 phút]
                         ↓
[3. Xác định tòa/căn, loại sự cố, mức ưu tiên — 2 phút]
                         ↓
[4. Tra đơn vị phụ trách/SLA và tạo ticket — 2 phút]
                         ↓
[5. Gửi xác nhận cho cư dân, chuyển bộ phận — 0,5 phút]

Tổng thời gian thao tác ước tính: 8 phút/yêu cầu
(không tính thời gian ticket nằm trong hàng đợi).
```

**Bước tốn thời gian/gây lỗi nhất:** Bước **2–4**, khoảng **7 phút/yêu cầu**. Nội dung thiếu địa điểm, dùng từ đời thường hoặc chứa nhiều vấn đề trong một ticket dễ làm nhân viên chọn sai danh mục, mức ưu tiên hay đơn vị xử lý.

**AI có thể nhảy vào hỗ trợ ở bước nào?**

- **Bước 2–3:** LLM trích xuất tòa/căn, loại sự cố, mức độ khẩn, dữ kiện còn thiếu và tạo bản tóm tắt có cấu trúc từ nội dung tự do.
- **Bước 4:** rule ánh xạ danh mục sang bộ phận/SLA; LLM chỉ đề xuất, không tự quyết định đối với khiếu nại pháp lý, phí dịch vụ hoặc tình huống khẩn cấp.
- Ticket khẩn cấp, có độ tin cậy thấp hoặc nhiều ý định phải chuyển nhân viên duyệt.

**Đo thành công bằng gì (Metric có số)?**

1. Giảm thời gian xử lý ban đầu trung vị từ **8 phút xuống ≤ 2 phút/yêu cầu**.
2. Đạt **≥ 90%** tỷ lệ chuyển đúng bộ phận trên tập kiểm thử đã gán nhãn.
3. Đạt **≥ 99% recall** với nhóm tình huống khẩn cấp; **100%** ticket khẩn cấp hoặc confidence thấp phải được con người duyệt.

**Quick Architecture:** [ ] No AI &nbsp; [ ] Rule &nbsp; [x] **LLM** &nbsp; [ ] Agent

**Lý do chọn:** Đầu vào chủ yếu là văn bản tự do cần hiểu ngữ cảnh. Đây chỉ là một LLM Feature tạo đề xuất có cấu trúc; chưa cần Agent tự vận hành hoặc tự đóng ticket.

---

## Quick Problem Card #2 — Tiền kiểm hồ sơ bảo lãnh viện phí

| Trường                            | Nội dung                                                                                                                                                                                      |
| --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bài toán (1 câu)**              | Nhân viên bảo hiểm/thu ngân Vinmec phải kiểm tra thủ công tính đầy đủ và nhất quán của hồ sơ bảo lãnh viện phí trước khi phối hợp với công ty bảo hiểm xác nhận phạm vi chi trả.              |
| **Công ty thành viên**            | [ ] VinFast &nbsp; [ ] Xanh SM &nbsp; [ ] Vinhomes &nbsp; [x] **Vinmec** &nbsp; [ ] Khác                                                                                                      |
| **Ai đang đau (Actor/Operator)?** | **Actor chính:** nhân viên quầy bảo hiểm/thu ngân. **Stakeholder bị ảnh hưởng:** bệnh nhân đang chờ, bác sĩ/bộ phận xét nghiệm cung cấp hồ sơ và công ty bảo hiểm tiếp nhận yêu cầu xác nhận. |

### Workflow thủ công hiện tại

```text
[1. Nhận GOP, hộ chiếu/CCCD, thẻ bảo hiểm — 3 phút]
                         ↓
[2. Đọc tài liệu, nhập các trường cần thiết — 8 phút]
                         ↓
[3. Đối chiếu hồ sơ, kết quả khám, điều khoản — 15 phút]
                         ↓
[4. Liệt kê mục thiếu và lập yêu cầu xác nhận — 7 phút]
                         ↓
[5. Nhân viên kiểm tra, gửi bảo hiểm, giải thích — 2 phút]

Thời gian thao tác baseline giả định: khoảng 35 phút/hồ sơ.
Tham chiếu độc lập: Vinmec công bố riêng bước xác nhận với
công ty bảo hiểm mất 15–45 phút, tùy đối tác.
```

**Bước tốn thời gian/gây lỗi nhất:** Bước **2–4**, khoảng **30 phút/hồ sơ** ở baseline giả định. Lỗi nhập mã hợp đồng, bỏ sót giấy tờ hoặc đọc nhầm phạm vi quyền lợi có thể làm hồ sơ bị trả lại và kéo dài thời gian chờ.

**AI có thể nhảy vào hỗ trợ ở bước nào?**

- **Bước 1–2:** Document AI/OCR trích xuất trường dữ liệu, kèm vị trí nguồn và confidence. Đây là lớp số hóa đầu vào, không phải lớp ra quyết định.
- **Bước 3–4:** rule kiểm tra trường bắt buộc, định dạng, tính nhất quán và tạo checklist phần còn thiếu.
- Hệ thống **không** tự kết luận quyền lợi, chấp nhận/từ chối bảo lãnh, thay đổi viện phí hoặc gửi hồ sơ khi chưa có nhân viên duyệt.

**Đo thành công bằng gì (Metric có số)?**

1. Giảm thời gian tiền kiểm trung vị từ **35 phút xuống ≤ 10 phút/hồ sơ**.
2. Đạt **≥ 98% field-level exact match** trên các trường bắt buộc (giá trị trích xuất khớp hoàn toàn nhãn chuẩn sau bước chuẩn hóa).
3. **100%** trường thiếu, mâu thuẫn hoặc confidence thấp được chuyển người kiểm tra; **0** quyết định chấp nhận/từ chối bảo lãnh được tự động hóa.

**Quick Architecture:** [ ] No AI &nbsp; [x] **Rule** &nbsp; [ ] LLM &nbsp; [ ] Agent

**Lý do chọn:** Kiến trúc lai gồm **Document AI/OCR ở đầu vào + Rule là lõi xử lý**. Sau OCR, dữ liệu và tiêu chí đầy đủ có cấu trúc, cần kết quả xác định và có thể audit. Rule phù hợp hơn LLM/Agent cho quyết định tài chính-y tế; AI chỉ hỗ trợ số hóa đầu vào.

---

## Quick Problem Card #3 — Điều phối yêu cầu đặc biệt của khách

| Trường                            | Nội dung                                                                                                                                                                           |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bài toán (1 câu)**              | Nhân viên CSKH/đặt phòng Vinpearl phải tra cứu booking, tồn chỗ và chính sách rồi phối hợp nhiều bộ phận để xử lý các yêu cầu đặc biệt theo ngữ cảnh của khách.                    |
| **Công ty thành viên**            | [ ] VinFast &nbsp; [ ] Xanh SM &nbsp; [ ] Vinhomes &nbsp; [ ] Vinmec &nbsp; [x] **Khác: Vinpearl**                                                                                 |
| **Ai đang đau (Actor/Operator)?** | **Actor chính:** nhân viên Contact Center/đặt phòng/Guest Service. **Stakeholder bị ảnh hưởng:** khách đang chờ câu trả lời và Front Office/Housekeeping/F&B nhận yêu cầu từ CSKH. |

### Workflow thủ công hiện tại

```text
[1. Nhận email/cuộc gọi và mã booking — 2 phút]
                         ↓
[2. Hiểu yêu cầu, hỏi bổ sung điều kiện — 4 phút]
                         ↓
[3. Tra booking, tồn chỗ, giá và chính sách — 8 phút]
                         ↓
[4. Chọn bộ phận, tạo yêu cầu, soạn phản hồi — 10 phút]
                         ↓
[5. Kiểm tra, gửi khách và theo dõi trạng thái — 6 phút]

Tổng thời gian thao tác ước tính: 30 phút/yêu cầu phức tạp
(không tính thời gian chờ phản hồi giữa các bộ phận).
```

**Bước tốn thời gian/gây lỗi nhất:** Bước **3–4**, khoảng **18 phút/yêu cầu**. Thông tin thay đổi theo thời điểm và nằm ở nhiều nguồn nên nhân viên có thể dùng nhầm chính sách, bỏ sót điều kiện hoặc chuyển sai bộ phận.

**AI có thể nhảy vào hỗ trợ ở bước nào?**

- **Bước 2:** LLM trích xuất ý định, ngày lưu trú, số khách, ràng buộc và dữ kiện còn thiếu; nếu thiếu trường bắt buộc, Agent tạo câu hỏi bổ sung thay vì tự đoán.
- **Bước 3–4:** Agent phạm vi hẹp tự chọn công cụ **chỉ đọc** phù hợp để tra booking, tồn chỗ hoặc kho chính sách. Khi nguồn mâu thuẫn hay đã quá thời hạn cache, Agent ưu tiên nguồn chính thức mới nhất, được phép tra lại **tối đa 1 lần**, rồi đề xuất tuyến xử lý, tạo ticket nháp và soạn phản hồi có nguồn/thời điểm tra cứu.
- Nhân viên phải duyệt trước khi gửi. Agent không được tự sửa/hủy booking, cam kết nâng hạng, duyệt hoàn tiền, thu tiền hoặc gửi yêu cầu ra hệ thống thật.

**Đo thành công bằng gì (Metric có số)?**

1. Giảm thời gian chuẩn bị phương án trung vị từ **30 phút xuống ≤ 8 phút/yêu cầu phức tạp**.
2. Đạt **≥ 90%** tỷ lệ chuyển đúng bộ phận ngay lần đầu.
3. Đạt **≥ 98%** độ chính xác của thông tin booking, tồn chỗ, giá và chính sách khi đối chiếu với nguồn tại đúng thời điểm tra cứu.
4. **100%** phương án phải hiển thị nguồn và thời điểm tra cứu; **0** hành động sửa booking/thanh toán trái phép trong kiểm thử nghiệm thu.

**Quick Architecture:** [ ] No AI &nbsp; [ ] Rule &nbsp; [ ] LLM &nbsp; [x] **Agent**

**Lý do chọn:** Bài toán cần vòng lặp **phát hiện thiếu dữ kiện → hỏi bổ sung → tự chọn công cụ → đối chiếu nguồn → tra lại khi có xung đột**, thay vì một chuỗi cố định. Agent chỉ được đề xuất trong phạm vi read-only và draft. Nếu chưa có API nội bộ ổn định hoặc quy trình luôn cố định, phải hạ scope thành workflow + LLM tra cứu chính sách, để nhân viên tự tra tồn chỗ.

---

## Nguồn dùng để kiểm tra tính thực tế

1. Worksheet và [Inspiration Kit](03-inspiration-kit.md) của bài lab.
2. [Quy trình tiếp nhận, phân loại và chuyển tiếp khiếu nại/yêu cầu của Vinhomes](https://market.vinhomes.vn/quy-dinh-xu-li-khieu-nai-yeu-cau-cua-khach-hang).
3. [Quy trình thanh toán bảo hiểm tại Vinmec](https://www.vinmec.com/eng/blog/insurance-payment-procedures-at-vinmec) — nêu các giấy tờ cần thiết và thời gian xác nhận bảo lãnh **15–45 phút**, tùy công ty bảo hiểm.
4. [FAQ Vinpearl](https://vinpearl.com/en/faqs) — mô tả CSKH phải kiểm tra với bộ phận liên quan, yêu cầu đặc biệt phụ thuộc tình trạng sẵn có và SLA phản hồi hiện hành.
