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
