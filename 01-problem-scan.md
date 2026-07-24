# 01-problem-scan.md — Bài cá nhân (Phase 1 SCAN + Phase 2 QUICK-ASSESS)

**Họ và tên:** Trần Anh Thư
**MSSV:** 2A202601611

---

## 🔍 Phase 1 — SCAN

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Vinhomes | Lặp lại | Phân loại tự động các phản ánh của cư dân (mất nước, hỏng đèn, ồn ào...) gửi qua App Vinhomes Resident và điều hướng đến đúng ban quản lý của từng tòa nhà. |
| 2 | VinFast | AI có thể tốt hơn | Khách hàng mô tả triệu chứng xe bằng tiếng Việt tự nhiên (VD: "xe đi qua gờ giảm tốc kêu cụp cụp ở bánh trước"); hệ thống tự động gợi ý mã lỗi kỹ thuật ban đầu để kỹ thuật viên xử lý nhanh hơn. |
| 3 | Vinmec | Tốn thời gian | Trích xuất thông tin lâm sàng từ bệnh án điện tử, kết quả xét nghiệm và ghi chú của bác sĩ để soạn thảo bản tóm tắt xuất viện (Discharge Summary) bằng ngôn ngữ dễ hiểu cho bệnh nhân. |
| 4 | Vinpearl | Pain từ người khác | Quét qua review trên Booking.com, Agoda, Google Maps để lọc ra các phàn nàn khẩn cấp (phòng bẩn, thái độ nhân viên...) và gửi cảnh báo ngay cho Manager phụ trách. |
| 5 | Xanh SM | Tốn thời gian | Phân tích ghi âm cuộc gọi hủy chuyến và ghi chú của tài xế để tự động phân loại 10 lý do phổ biến nhất gây rò rỉ cuốc xe, hỗ trợ đội vận hành tối ưu điều vận. |

---

## 🃏 Phase 2 — QUICK-ASSESS

**Quick Problem Card #1**
```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                               │
│ Bài toán: Phân loại & điều hướng phản ánh cư dân tự động     │
│ Công ty thành viên: [x] Vinhomes                             │
│                                                               │
│ Ai đang đau (Actor)? Nhân viên tổng đài / lễ tân Ban quản lý │
│ tòa nhà — người tiếp nhận và phân loại thủ công phản ánh.    │
│                                                               │
│ Workflow thủ công hiện tại (4 bước):                         │
│   1. Cư dân gửi phản ánh trên App ──>                        │
│   2. Nhân viên tổng đài đọc, phân loại thủ công ──>          │
│   3. Chuyển phản ánh đến đúng ban chuyên trách (điện, nước,  │
│      an ninh...) ──> 4. Ban chuyên trách xử lý và phản hồi   │
│                                                               │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 — đọc & phân loại    │
│ thủ công (⏱ ~5 phút/lượt, dễ phân loại sai vào ban không     │
│ đúng chuyên trách gây chậm trễ xử lý).                       │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 — tự động đọc   │
│ nội dung và phân loại + định tuyến ngay đến đúng ban.        │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                        │
│   "Giảm thời gian phân loại + định tuyến từ 5 phút xuống     │
│   dưới 30 giây, độ chính xác phân loại đạt trên 90%."        │
│                                                               │
│ Quick Architecture: [x] LLM  [ ] No AI  [ ] Rule  [ ] Agent  │
└─────────────────────────────────────────────────────────────┘
```

**Quick Problem Card #2**
```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                               │
│ Bài toán: Trợ lý chẩn đoán lỗi xe từ mô tả tiếng Việt        │
│ Công ty thành viên: [x] VinFast                              │
│                                                               │
│ Ai đang đau (Actor)? Tư vấn viên tổng đài chăm sóc khách     │
│ hàng (CSKH) và kỹ thuật viên xưởng dịch vụ.                  │
│                                                               │
│ Workflow thủ công hiện tại (4 bước):                         │
│   1. Khách hàng mô tả sự cố bằng lời tự do ──>                │
│   2. Tư vấn viên ghi nhận, tự suy đoán mã lỗi/loại sự cố ──> │
│   3. Chuyển thông tin cho kỹ thuật viên phù hợp ──>          │
│   4. Kỹ thuật viên kiểm tra lại thực tế trên xe              │
│                                                               │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 — tư vấn viên không  │
│ có chuyên môn kỹ thuật sâu nên suy đoán sai mã lỗi ban đầu   │
│ (⏱ ~7 phút/lượt, dễ điều sai kỹ thuật viên chuyên trách).    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 — phân tích mô  │
│ tả tiếng Việt tự nhiên và gợi ý nhóm mã lỗi kỹ thuật khả dĩ.  │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                        │
│   "Giảm thời gian phân loại sơ bộ từ 7 phút xuống dưới 1     │
│   phút, tỷ lệ gợi ý đúng nhóm lỗi đạt trên 80%."              │
│                                                               │
│ Quick Architecture: [x] LLM  [ ] No AI  [ ] Rule  [ ] Agent  │
└─────────────────────────────────────────────────────────────┘
```

**Quick Problem Card #3**
```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                               │
│ Bài toán: Soạn thảo tóm tắt hồ sơ xuất viện tự động          │
│ Công ty thành viên: [x] Vinmec                               │
│                                                               │
│ Ai đang đau (Actor)? Bác sĩ / điều dưỡng phụ trách xuất viện │
│ cho bệnh nhân.                                                │
│                                                               │
│ Workflow thủ công hiện tại (4 bước):                         │
│   1. Tổng hợp bệnh án điện tử, kết quả xét nghiệm ──>        │
│   2. Bác sĩ đọc và tự tay soạn bản tóm tắt xuất viện ──>     │
│   3. Rà soát lại thuật ngữ cho dễ hiểu với bệnh nhân ──>     │
│   4. In và bàn giao cho bệnh nhân khi xuất viện              │
│                                                               │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 — soạn thảo thủ công │
│ từ nhiều nguồn dữ liệu rời rạc (⏱ ~15 phút/lượt, dễ bỏ sót   │
│ thông tin quan trọng do khối lượng bệnh nhân lớn).           │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 — tự động draft │
│ bản tóm tắt xuất viện bằng ngôn ngữ dễ hiểu từ dữ liệu có sẵn,│
│ bác sĩ chỉ cần rà soát và ký duyệt (Human-in-the-loop).      │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                        │
│   "Giảm thời gian soạn thảo từ 15 phút xuống dưới 5 phút     │
│   cho mỗi hồ sơ, 100% bản draft vẫn qua bác sĩ duyệt trước   │
│   khi bàn giao."                                              │
│                                                               │
│ Quick Architecture: [x] LLM  [ ] No AI  [ ] Rule  [ ] Agent  │
└─────────────────────────────────────────────────────────────┘
```
