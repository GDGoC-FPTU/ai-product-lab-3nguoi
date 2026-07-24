# Báo cáo Deep-Dive & Evaluation — Vinhomes Resident Request Triage

> **Bài toán:** Phân loại, ưu tiên và chuyển tuyến yêu cầu cư dân Vinhomes bằng LLM Feature có Human-in-the-loop

## Thông tin nhóm

| Thông tin        | Nội dung                                        |
| ---------------- | ----------------------------------------------- |
| **Tên nhóm**     | **[3NGUOI]**                                    |
| **Thành viên 1** | **Nguyễn Đức Tín** — MSSV: **02A2026011851185** |
| **Thành viên 2** | **Trần Anh Thư** — MSSV: **2A202601611**        |
| **Thành viên 3** | **Bùi Hữu Nghĩa** — MSSV: **2A202601880**       |

---

# Quyết định lựa chọn của nhóm

Nhóm chọn **Chủ đề — Phân loại, ưu tiên và chuyển tuyến yêu cầu cư dân Vinhomes** để thực hiện Deep-Dive.

## Vì sao chọn Vinhomes

- Quy trình công khai của Vinhomes xác nhận có **bộ phận tiếp nhận (BPTN)**, **bộ phận xử lý (BPXL)**, nhiều kênh tiếp nhận và bước chuyển thông tin tới đúng bộ phận chuyên trách. Đây là workflow có actor, handoff và đầu ra rõ.
- Đầu vào là ngôn ngữ tự do, có thể thiếu địa điểm, chứa nhiều ý định hoặc diễn đạt tình huống khẩn cấp theo nhiều cách. LLM phù hợp để trích xuất và chuẩn hóa nội dung hơn một danh sách keyword cố định.
- Phạm vi giải pháp chỉ dừng ở **đề xuất có cấu trúc và soạn nháp**. Con người luôn duyệt trước hành động ra ngoài nên có thể kiểm soát rủi ro trong pilot.

## Quy ước bằng chứng và số liệu

- **Thông tin công khai:** Quy định Vinhomes cập nhật ngày 05/05/2025 nêu kênh điện thoại, email, bản cứng và tiếp nhận trực tiếp; với kênh điện thoại, CSKH chuyển thông tin tới đúng bộ phận trong vòng 04 giờ làm việc; email hợp lệ được xác nhận trong 08 giờ và xử lý/chuyển tiếp trong 24 giờ làm việc.
- **Giả thuyết scoping cần kiểm chứng:** app cư dân, CRM/ticket system, SOP nội bộ, thời gian thao tác **8 phút/yêu cầu**, tỷ lệ lỗi và sản lượng ticket. Đây không phải số liệu nội bộ do Vinhomes công bố.
- **Mục tiêu pilot:** các ngưỡng hiệu quả/chất lượng trong báo cáo là tiêu chí nghiệm thu đề xuất, chưa phải kết quả đã đạt.
- **Không trộn hai loại thời gian:** SLA công khai là thời hạn phản hồi/chuyển tiếp; baseline 8 phút là **touch time** giả định của nhân viên. Thời gian ticket nằm trong hàng đợi hoặc chờ cư dân/bộ phận khác không được cộng vào touch time.

Nguồn nghiệp vụ: [Quy định xử lý khiếu nại/yêu cầu của khách hàng — Vinhomes](https://market.vinhomes.vn/quy-dinh-xu-li-khieu-nai-yeu-cau-cua-khach-hang).

---

# Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow Mapping

### Sơ đồ quy trình hiện tại

```text
🔄 Cư dân → CSKH/Ban Quản lý
        │
        ▼
[1. Nhận yêu cầu từ app*/điện thoại/email — 0,5 phút]
        │
        ▼
[2. Đọc mô tả/ảnh, xác định dữ kiện thiếu — 3 phút] 🔴
        │
        ▼
[3. Xác định tòa/căn, danh mục, mức ưu tiên — 2 phút] 🔴
        │
        ▼
[4. Tra SOP/SLA, chọn bộ phận và tạo ticket — 2 phút] 🔴
        │
        ▼
[5. Gửi xác nhận và chuyển ticket — 0,5 phút]
        │
        ▼
🔄 CSKH/Ban Quản lý → Bộ phận xử lý

Tổng touch time giả định: 8 phút/yêu cầu.
Bước 2–4: 7 phút, tương đương 87,5% touch time.
* Kênh app là giả thuyết pilot cần xác minh; nguồn công khai xác nhận
  điện thoại, email, bản cứng và tiếp nhận trực tiếp.
```
Tổng touch time giả định: 8 phút/yêu cầu.
Bước 2–4: 7 phút, tương đương 87,5% touch time.
* Kênh app là giả thuyết pilot cần xác minh; nguồn công khai xác nhận
  điện thoại, email, bản cứng và tiếp nhận trực tiếp.
```

### Chi tiết từng bước

| Bước | Actor | Công cụ giả định / kênh | Input | Xử lý và thời gian | Output / handoff |
|---:|---|---|---|---|---|
| **1** | Cư dân; nhân viên CSKH/Ban Quản lý | App cư dân*, điện thoại, email | Mô tả tự do, hình ảnh, thông tin liên hệ | Tiếp nhận yêu cầu và mở đầu việc — **0,5 phút** | Bản ghi yêu cầu thô. **🔄 Handoff:** cư dân → CSKH/Ban Quản lý |
| **2 🔴** | Nhân viên CSKH/Ban Quản lý | Hộp thư/tổng đài/app*, màn hình ticket | Bản ghi thô và ảnh đính kèm | Đọc, tóm tắt, phát hiện dữ kiện thiếu; hỏi lại khi cần — **3 phút** | Mô tả đã hiểu sơ bộ và danh sách thông tin cần bổ sung |
| **3 🔴** | Nhân viên CSKH/Ban Quản lý | CRM/ticket system*, danh mục sự cố* | Nội dung đã đọc và thông tin địa điểm | Xác định dự án/tòa/căn, danh mục và mức ưu tiên — **2 phút** | Nhãn phân loại và ưu tiên dự kiến |
| **4 🔴** | Nhân viên CSKH/Ban Quản lý | SOP/SLA*, CRM/ticket system* | Nhãn, ưu tiên, thông tin vị trí | Tra đơn vị phụ trách, điều kiện SLA và tạo ticket — **2 phút** | Ticket có tuyến xử lý dự kiến |
| **5** | Nhân viên CSKH/Ban Quản lý | CRM/ticket system*, email/app* | Ticket đã tạo | Gửi xác nhận và chuyển ticket — **0,5 phút** | Xác nhận cho cư dân. **🔄 Handoff:** CSKH/Ban Quản lý → BPXL |

\* Công cụ/kênh đánh dấu sao là giả định vận hành để scoping, phải được stakeholder xác nhận.

### Bottleneck và failure modes

Bước **2–4** chiếm **7/8 phút** touch time. Các lỗi có khả năng xảy ra:

- Thiếu dự án, tòa, tầng hoặc căn nhưng nhân viên không phát hiện trước khi chuyển.
- Một ticket chứa nhiều ý định, ví dụ vừa mất nước vừa phản ánh phí, dẫn tới chỉ gắn một nhãn.
- Ngôn ngữ đời thường hoặc viết tắt làm chọn sai danh mục và sai bộ phận.
- Tình huống cháy, khói, kẹt thang máy hoặc đe dọa an toàn bị đánh giá thấp mức ưu tiên.
- Nhân viên dùng nhầm SOP/SLA hoặc tự cam kết thời hạn chưa được nguồn chính thức xác nhận.

**Ngoài phép tính touch time:** thời gian chờ cư dân bổ sung dữ kiện, thời gian ticket nằm trong hàng đợi và thời gian BPXL xử lý. Pilot phải đo riêng các khoảng này để không gán nhầm toàn bộ SLA cho thao tác phân loại.

---
## 3.2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | **Actor chính:** nhân viên CSKH/Ban Quản lý tiếp nhận và điều phối yêu cầu. **Stakeholder:** cư dân chờ phản hồi; đội kỹ thuật, an ninh, vệ sinh, CSKH và tài chính/pháp lý nhận ticket để xử lý. |
| **2. Current Workflow** | Nhân viên nhận yêu cầu qua các kênh, đọc mô tả/ảnh, xác định dữ kiện thiếu, vị trí, danh mục và mức ưu tiên, tra SOP/SLA, tạo ticket, gửi xác nhận rồi chuyển BPXL. Workflow giả định có 5 bước, dùng app/email/tổng đài, CRM/ticket system và SOP; tổng touch time baseline **8 phút/yêu cầu**. |
| **3. Bottleneck** | Bước **2–4**, tổng **7 phút/yêu cầu**: hiểu văn bản tự do, phát hiện trường thiếu, phân loại ưu tiên và chọn tuyến. Lỗi trọng yếu gồm thiếu địa điểm, nhiều ý định, sai danh mục, sai bộ phận hoặc bỏ sót tình huống khẩn cấp. |
| **4. Business Impact** | Chưa có volume Vinhomes đã xác minh nên dùng biến `N` là số ticket/tháng. Giờ hiện tại = `N × 8 / 60`; giờ mục tiêu = `N × 2 / 60`; năng lực giải phóng = `N × 6 / 60`. Chuyển sai tuyến còn làm tăng rework và thời gian chờ, nhưng chưa quy đổi thành tiền nếu chưa có log. |
| **5. Success Metric** | (1) Median handling time từ **8 xuống ≤ 2 phút/ticket**; (2) route accuracy **≥ 90%** trên tập gán nhãn; (3) emergency recall **≥ 99%**; (4) **100%** ticket khẩn cấp hoặc confidence `< 0,80` vào hàng đợi người duyệt; (5) **0** ticket tự gửi, tự đóng hoặc tự cam kết SLA. |
| **6. Operational Boundary** | AI chỉ đọc ticket **đã ẩn danh**, trích xuất trường, đề xuất danh mục/ưu tiên/bộ phận và soạn phản hồi nháp. **CẤM:** tự gửi hoặc đóng ticket; sửa phí; quyết định bồi thường; kết luận trách nhiệm pháp lý; tiết lộ PII; bịa địa điểm, chính sách hay SLA. Mọi phản hồi/hành động ra ngoài bắt buộc có **Human-in-the-loop** phê duyệt. |

### Taxonomy dùng chung cho prototype và pilot

- **Danh mục:** `technical`, `security`, `sanitation`, `noise`, `utility`, `billing`, `legal`, `other`.
- **Mức ưu tiên:** `emergency`, `high`, `normal`.
- **Đội đề xuất:** `engineering`, `security`, `housekeeping`, `customer_service`, `finance_legal`, `human_triage`.
- **Hành động đề xuất:** `draft_route`, `request_missing_info`, `escalate_human`, `reject_out_of_scope`.

Taxonomy này là bản scoping, không phải taxonomy nội bộ Vinhomes. CSKH/Ban Quản lý phải duyệt và ánh xạ nó với SOP/SLA thật trước pilot.

---

