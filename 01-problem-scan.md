# 📄 01 — Problem Scan & Quick Cards (Bài cá nhân)

**Họ và tên:** Bùi Hữu Nghĩa  
**MSSV:** 2A202601880  

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Dùng **4 Lenses** quét qua vận hành của các công ty thành viên Vingroup.

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Vinmec** | Tốn thời gian | Bác sĩ mất 20-30 phút/bệnh nhân để viết tóm tắt hồ sơ xuất viện (Discharge Summary) thủ công từ bệnh án điện tử, kết quả xét nghiệm, và ghi chú lâm sàng. Gây quá tải cho bác sĩ vào cuối ngày. |
| 2 | **Vinhomes** | Lặp lại | Nhân viên Ban quản lý phải phân loại thủ công hàng trăm phản ánh/khiếu nại của cư dân gửi qua App Vinhomes Resident mỗi ngày (mất nước, hỏng đèn, ồn ào, vi phạm nội quy...) để điều hướng đến đúng bộ phận xử lý. |
| 3 | **VinFast** | AI có thể tốt hơn | Khách hàng mô tả lỗi xe bằng tiếng Việt tự nhiên (VD: "xe đi qua gờ giảm tốc kêu cụp cụp ở bánh trước"), nhân viên CSKH phải tra cứu thủ công mã lỗi kỹ thuật để chuyển cho bộ phận sửa chữa, dễ sai và mất thời gian. |
| 4 | **Xanh SM** | Pain từ người khác | Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác vào giờ cao điểm, dẫn đến khách hủy chuyến (tỉ lệ hủy ~18%), giảm thu nhập tài xế và uy tín dịch vụ. |
| 5 | **Vinpearl** | Tốn thời gian | Nhân viên quản lý khách sạn Vinpearl mất 2-3 tiếng/ngày quét review từ Booking.com, Agoda, Google Maps để lọc ra phàn nàn khẩn cấp ("phòng bẩn", "côn trùng", "thái độ nhân viên") gửi Manager xử lý ngay. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn top 3 từ danh sách SCAN: **#1 (Vinmec Discharge Summary), #2 (Vinhomes Phân loại khiếu nại), #3 (VinFast Chẩn đoán lỗi xe).**

## Quick Problem Card #1 — Vinmec: Soạn thảo tóm tắt hồ sơ xuất viện

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Bác sĩ Vinmec mất quá nhiều thời gian    │
│ soạn thảo thủ công tóm tắt hồ sơ xuất viện từ bệnh án      │
│ điện tử, ghi chú lâm sàng và kết quả xét nghiệm.           │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau (Actor)? Bác sĩ điều trị (quá tải cuối ngày),  │
│ Y tá trực (phải hỗ trợ soạn thảo), Bệnh nhân (chờ đợi     │
│ giấy xuất viện lâu).                                        │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Bác sĩ mở bệnh án điện tử (EMR) tra cứu tiền sử      │
│   → 2. Đọc toàn bộ kết quả xét nghiệm và chẩn đoán hình   │
│        ảnh (X-quang, CT, MRI...)                            │
│   → 3. Tổng hợp thủ công các ghi chú lâm sàng qua các     │
│        ngày nằm viện                                        │
│   → 4. Soạn thảo bản tóm tắt xuất viện bằng ngôn ngữ dễ   │
│        hiểu cho bệnh nhân (chẩn đoán, thuốc, lịch tái khám)│
│   → 5. Ký duyệt và in ấn giao cho bệnh nhân/người nhà     │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3-4 (⏱ 20 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4             │
│ (Tự động trích xuất → Tổng hợp → Draft bản tóm tắt)        │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ "Giảm thời gian soạn discharge summary từ 25 phút xuống    │
│ dưới 5 phút/bệnh nhân, đạt tỉ lệ chính xác nội dung ≥97%"│
│                                                             │
│ Quick Architecture: [x] LLM Feature (AI draft + Bác sĩ     │
│ duyệt, HITL bắt buộc vì liên quan y tế)                    │
└─────────────────────────────────────────────────────────────┘
```

## Quick Problem Card #2 — Vinhomes: Phân loại & Điều hướng phản ánh cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Nhân viên CSKH Vinhomes phải phân loại   │
│ thủ công hàng trăm phản ánh/khiếu nại cư dân mỗi ngày      │
│ từ App Vinhomes Resident để điều hướng đến đúng bộ phận.     │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH Ban quản lý (quá tải), │
│ Cư dân (chờ phản hồi lâu, trung bình 12 tiếng).            │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Nhân viên CSKH đọc từng phản ánh trên App/email       │
│   → 2. Phân loại thủ công vào danh mục (kỹ thuật/an ninh/  │
│        vệ sinh/tiện ích/hành chính...)                      │
│   → 3. Xác định mức độ ưu tiên (Khẩn cấp/Bình thường)     │
│   → 4. Chuyển ticket đến đúng bộ phận xử lý kèm ghi chú   │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (⏱ 8 phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3             │
│ (Tự động phân loại danh mục + đánh giá mức độ ưu tiên)     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ "Phân loại tự động 90% phản ánh với độ chính xác ≥92%,     │
│ giảm thời gian phản hồi đầu tiên từ 12h xuống dưới 2h"    │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Phân loại NLP +       │
│ nhân viên duyệt các case mức ưu tiên cao)                   │
└─────────────────────────────────────────────────────────────┘
```

## Quick Problem Card #3 — VinFast: Chẩn đoán lỗi xe từ mô tả tiếng Việt

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Khách hàng VinFast mô tả lỗi xe bằng    │
│ tiếng Việt tự nhiên, nhân viên CSKH phải tra cứu thủ công  │
│ để phân loại mã lỗi kỹ thuật ban đầu trước khi chuyển      │
│ cho xưởng sửa chữa.                                        │
│ Công ty thành viên: [x] VinFast                             │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH VinFast (phải hiểu     │
│ kỹ thuật ô tô), Kỹ sư xưởng (nhận mô tả sai, mất thêm    │
│ thời gian chẩn đoán lại), Khách hàng (chờ lâu).            │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách gọi hotline/chat mô tả triệu chứng bằng ngôn   │
│      ngữ tự nhiên (VD: "xe kêu cụp cụp qua gờ giảm tốc") │
│   → 2. Nhân viên CSKH tra cứu bảng mã lỗi kỹ thuật        │
│        (>200 mã lỗi) để tìm mã phù hợp                    │
│   → 3. Ghi chú và tạo phiếu sửa chữa gửi xưởng           │
│   → 4. Xưởng nhận phiếu, chẩn đoán lại và xác nhận lỗi   │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 (⏱ 10 phút/lượt,  │
│ tỉ lệ phân loại sai mã lỗi ~25%)                          │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2               │
│ (Tự động phân tích NLP → Map sang mã lỗi kỹ thuật)        │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ "Giảm tỉ lệ phân loại sai mã lỗi từ 25% xuống dưới 8%,   │
│ giảm thời gian tạo phiếu sửa chữa từ 15 phút → 3 phút"   │
│                                                             │
│ Quick Architecture: [x] LLM Feature (NLP phân loại +       │
│ nhân viên CSKH confirm + Kỹ sư xưởng duyệt cuối cùng)     │
└─────────────────────────────────────────────────────────────┘
```
