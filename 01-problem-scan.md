# Phase 1 — SCAN & Phase 2 — QUICK ASSESS

## 1. Bảng quét cơ hội (SCAN)

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Lặp lại | Tài xế báo sự cố hết pin giữa đường và điều phối viên phải xử lý thủ công từng cuộc gọi. |
| 2 | **Xanh SM** | Tốn thời gian | Điều phối viên mất nhiều thời gian tra cứu trạm sạc phù hợp và soạn tin nhắn hướng dẫn tài xế. |
| 3 | **Vinhomes** | AI-upgrade | Nhân viên CSKH phải trả lời hàng trăm phản hồi cư dân theo template rập khuôn, mất nhiều thời gian. |
| 4 | **Vinmec** | Pain from stakeholder | Bác sĩ mất quá nhiều thời gian viết tóm tắt hồ sơ xuất viện cho từng bệnh nhân. |
| 5 | **VinFast** | Tốn thời gian | Nhân viên đối soát hóa đơn sạc điện và dữ liệu trạm đối tác hằng tuần bằng cách thủ công. |

## 2. Quick Problem Cards

### Quick Problem Card #1 — Xanh SM: Xử lý sự cố pin thực địa

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Tài xế Xanh SM báo hết pin giữa đường cần được   │
│ điều phối hướng dẫn đi trạm sạc gần nhất hoặc gọi cứu hộ.   │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau (Actor)? Tài xế đang chờ đợi và điều phối viên │
│ phải xử lý quá tải trong giờ cao điểm.                      │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                      │
│   1. Tài xế gọi tổng đài điều vận báo sự cố pin             │
│   → 2. Điều phối viên tra cứu vị trí GPS xe trên hệ thống   │
│   → 3. Tra cứu trạm sạc VinFast còn trụ trống gần nhất     │
│   → 4. Soạn tin nhắn chỉ dẫn gửi cho tài xế                 │
│   → 5. Nếu pin rất thấp, gọi xe cứu hộ pin di động          │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3-4 (⏱ 10-12 phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4               │
│ (Auto-pull trạm sạc, draft tin nhắn, đề xuất hành động)     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút.   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

### Quick Problem Card #2 — Vinhomes: Phản hồi cư dân chậm và rập khuôn

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Nhân viên CSKH Vinhomes phải trả lời các khiếu nại │
│ và câu hỏi của cư dân bằng cách soạn thủ công từng tin nhắn. │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên chăm sóc cư dân và người dân │
│ đang chờ phản hồi.                                         │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                      │
│   1. Cư dân gửi phản hồi qua app hoặc hotline               │
│   → 2. Nhân viên đọc nội dung và phân loại chủ đề            │
│   → 3. Soạn phản hồi theo template hoặc thủ công            │
│   → 4. Gửi phản hồi và theo dõi SLA                          │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (⏱ 8-10 phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3               │
│ (Phân loại, gợi ý câu trả lời, draft phản hồi)              │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian phản hồi từ 8 giờ xuống dưới 15 phút cho    │
│ các trường hợp phổ biến.                                    │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

### Quick Problem Card #3 — Vinmec: Tóm tắt hồ sơ xuất viện mất thời gian

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Bác sĩ và điều dưỡng phải tóm tắt hồ sơ bệnh án   │
│ xuất viện thủ công trước khi bàn giao cho bệnh nhân.       │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau (Actor)? Bác sĩ, điều dưỡng, và bệnh nhân.      │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                      │
│   1. Bệnh nhân hoàn tất quá trình điều trị                  │
│   → 2. Nhân viên y tế đọc toàn bộ hồ sơ bệnh án            │
│   → 3. Viết tóm tắt, hướng dẫn tái khám và thuốc            │
│   → 4. Gửi tệp hồ sơ cho bệnh nhân/đối tác hậu cần          │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (⏱ 20-30 phút/bệnh nhân) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3               │
│ (Tóm tắt hồ sơ, tạo bản nháp dưới dạng structured note)    │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian tóm tắt hồ sơ từ 25 phút xuống dưới 5 phút. │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```
