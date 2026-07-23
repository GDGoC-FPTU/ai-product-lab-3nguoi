# Phase 3 — DEEP DIVE & Phase 5 — EVALUATE

## 1. Bài toán được chọn

**Bài toán:** Xử lý sự cố pin thực địa của tài xế Xanh SM.

## 2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) thuộc Trung tâm Điều vận Xanh SM và tài xế xe điện Xanh SM đang gặp sự cố pin giữa đường. |
| **2. Current Workflow** | Khi tài xế báo sự cố pin, điều phối viên phải tra cứu vị trí GPS xe, tra cứu trạm sạc VinFast còn trụ trống, soạn tin nhắn chỉ dẫn, và gọi xe cứu hộ nếu cần. Quy trình hiện tại hoàn toàn thủ công và mất thời gian. |
| **3. Bottleneck** | Bước tra cứu trạm sạc phù hợp và soạn tin nhắn hướng dẫn tài xế là gánh nặng lớn nhất. Đây là các bước cần xử lý ngôn ngữ và dữ liệu định tuyến nhiều nhất. |
| **4. Business Impact** | Mỗi ngày có khoảng 80 sự cố pin thực địa tại Hà Nội. Mỗi lượt xử lý mất khoảng 15 phút, cộng lại gây lãng phí khoảng 20 giờ nhân công/ngày cho bộ phận điều vận. Điều này làm tăng thời gian chờ của tài xế và làm giảm doanh thu do xe không thể nhận cuốc kịp thời. |
| **5. Success Metric** | 1. Giảm thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút. 2. Đạt tỷ lệ đề xuất trạm sạc và chỉ đường đúng ở mức 98%. |
| **6. Operational Boundary** | AI được phép tự động truy xuất vị trí xe và trạm sạc, tạo bản nháp tin nhắn hướng dẫn, và đề xuất phương án cứu hộ khi pin quá thấp. **TUYỆT ĐỐI KHÔNG** được tự động gửi tin mà không có người duyệt; không được đề xuất trạm sạc quá xa khi pin dưới 5%; không được vượt quá quyền hạn của điều phối viên. |

## 3. Current-State Workflow Mapping

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │ ──→ │ Tra cứu định │ ──→ │ Tra cứu trạm │ ──→ │ Soạn văn bản │
│ gọi sự cố    │     │ vị GPS xe    │     │ sạc VinFast  │     │ hướng dẫn    │
│              │     │              │     │ còn trụ trống│     │ gửi tài xế   │
│ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │
│ ⏱ 2 phút     │     │ ⏱ 2 phút     │     │ ⏱ 5 phút 🔴  │     │ ⏱ 5 phút 🔴  │
│ In: Điện thoại│     │ In: Biển số  │     │ In: Vị trí GPS│     │ In: Raw data │
│ Out: Log sự cố│     │ Out: Toạ độ  │     │ Out: Địa chỉ │     │ Out: SMS     │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Gọi xe cứu   │
                                                               │ hộ (nếu cần) │
                                                               │ Ai: Dispatch │
                                                               │ ⏱ 1 phút     │
                                                               └──────────────┘
🔴 = Bottlenecks
⏱ Tổng thời gian xử lý thủ công: 15 phút/lượt.
```

## 4. Future-State Flow & AI Fit

**AI Fit:** Chọn **LLM Feature**.

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │ ──→ │ 🔵 Auto-pull │ ──→ │ 🔵 AI draft  │ ──→ │ 🟢 Dispatch  │
│ gọi sự cố    │     │ vị trí xe &   │     │ tin nhắn &   │     │ duyệt & gửi │
│              │     │ trạm sạc gần │     │ chỉ đường    │     │ tới tài xế   │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ↩️ Fallback:
                                                               Nếu AI không tự tin hoặc trả về lỗi,
                                                               điều phối viên viết tay như cũ.
```

## 5. Evaluate

### AI Readiness Checklist
1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test.
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback).
3. [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ.

### Quyết định cuối cùng
[ ] **GO**
[x] **NOT YET**
[ ] **NO-GO**

**Justification:** Bài toán này có cấu trúc rõ, bottleneck dễ đo, và AI có thể giúp giảm thời gian xử lý đáng kể nếu được giới hạn đúng phạm vi. Tuy nhiên, để chuyển sang giai đoạn production, nhóm cần thêm dữ liệu thực tế về vị trí trạm sạc, trạng thái pin, và mức độ phù hợp của từng loại xe điện. Hiện tại, nếu triển khai ngay mà không có dữ liệu đủ mạnh, rủi ro sai sót có thể ảnh hưởng đến trải nghiệm tài xế. Vì vậy, quyết định phù hợp nhất là **NOT YET**.
