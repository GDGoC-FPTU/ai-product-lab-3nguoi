# Báo cáo Deep-Dive & Evaluation — Vinhomes Resident Request Triage

> **Bài toán:** Phân loại, ưu tiên và chuyển tuyến yêu cầu cư dân Vinhomes bằng LLM Feature có Human-in-the-loop

## Thông tin nhóm

| Thông tin        | Nội dung                                   |
| ---------------- | ------------------------------------------ |
| **Tên nhóm**     | **[3NGUOI]**                               |
| **Thành viên 1** | **Nguyễn Đức Tín** — MSSV: **2A202601185** |
| **Thành viên 2** | **Trần Anh Thư** — MSSV: **2A202601611**   |
| **Thành viên 3** | **Bùi Hữu Nghĩa** — MSSV: **2A202601880**  |

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

- Kênh app là giả thuyết pilot cần xác minh; nguồn công khai xác nhận
  điện thoại, email, bản cứng và tiếp nhận trực tiếp.

````

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
## 3.3. AI Fit

| Phương án | Điểm phù hợp | Hạn chế / rủi ro | Kết luận |
|---|---|---|---|
| **Rule / State Machine** | Dễ audit; tốt cho kiểm tra trường bắt buộc, từ khóa khẩn cấp, validation schema và ánh xạ nhãn sang BPXL | Dễ bỏ sót tiếng Việt tự do, cách diễn đạt mới và ticket đa ý định; bộ luật tăng nhanh theo ngoại lệ | **Dùng làm guardrail và router**, không dùng một mình cho hiểu nội dung |
| **LLM Feature** | Phù hợp cho trích xuất thực thể, hiểu ngữ cảnh, phân loại và soạn nháp có cấu trúc; không cần quyền tự chủ | Có thể hallucinate, bị prompt injection hoặc trả sai schema; cần validator, confidence gate và HITL | **Lựa chọn chính** cho phạm vi pilot |
| **Agentic Loop** | Có ích nếu cần tự chọn nhiều công cụ, tra cứu lặp lại và thực thi nhiều bước | Workflow hiện tại cố định, chưa có nhu cầu vòng lặp tự chủ; quyền thực thi làm tăng chi phí, latency và blast radius | **Không chọn** |

**AI-Fit Matrix:** [ ] Rule / State-Machine &nbsp; [x] **LLM Feature** &nbsp; [ ] Agentic Loop

Giải pháp thực tế là kiến trúc lai: **Rule → LLM Feature → Validator → Human → Rule router**. LLM hỗ trợ hiểu ngôn ngữ; rule giữ các điều kiện xác định và quyền thực thi.

---

## 3.4. Future-State Flow

**Ký hiệu:** 🔵 AI Step · 🟢 Human Step (HITL) · ↩️ Fallback

```text
[Nhận ticket đã ẩn danh]
          │
          ▼
[Rule: kiểm tra trường bắt buộc + từ khóa khẩn cấp]
          │
          ▼
🔵 [LLM: trả JSON phân loại, confidence và draft_reply]
          │
          ▼
[Schema / risk validator]
          │
          ├── Invalid / emergency / confidence < 0,80
          │           │
          │           ▼
          │    🟢 [Human triage chuyên trách]
          │
          └── Hợp lệ, không khẩn cấp
                      │
                      ▼
               🟢 [Nhân viên review / sửa / approve]
                      │
                      ▼
                [Rule route tới BPXL]
                      │
                      ▼
          [Ghi nhãn sửa và feedback để đánh giá]
````

### Trách nhiệm theo lớp

1. **Input/rule gate:** bỏ hoặc che PII trước khi gửi model; phát hiện trường bắt buộc và từ khóa khẩn cấp. Cờ khẩn cấp do rule phát hiện không được LLM hạ cấp.
2. **🔵 LLM Feature:** chỉ tạo JSON theo schema, gồm tóm tắt, vị trí, dữ kiện thiếu, danh mục, ưu tiên, đội đề xuất, confidence và `draft_reply` bắt đầu bằng `[DRAFT_ONLY]`.
3. **Validator:** kiểm tra schema, enum, prefix nháp, confidence, cờ HITL và hành động bị cấm. Output không hợp lệ không được đi tiếp.
4. **🟢 Human review:** ticket khẩn cấp/low-confidence vào human triage; ticket hợp lệ khác vẫn phải được nhân viên review và approve. Không có “straight-through processing” trong pilot.
5. **Rule router:** chỉ sau approval mới ánh xạ danh mục sang BPXL. Rule không tự bịa SLA; mapping phải lấy từ cấu hình đã được nghiệp vụ duyệt.
6. **Feedback:** lưu nhãn ban đầu và phần nhân viên sửa ở dạng đã ẩn danh để tính route accuracy, emergency recall và drift. Không ghi API key hoặc PII vào log.

### Fallback và failure handling

- Khi timeout, API error hoặc JSON sai schema: **retry tối đa 01 lần**.
- Nếu lần retry vẫn lỗi: **↩️ chuyển nguyên yêu cầu vào hàng đợi thủ công hiện tại**, kèm mã lỗi kỹ thuật; không dùng output dở dang.
- Nếu thiếu vị trí/trường bắt buộc: tạo nháp hỏi bổ sung và chờ nhân viên duyệt, không tự đoán.
- Nếu `priority = emergency`, confidence `< 0,80`, nội dung pháp lý/phí, nhiều ý định hoặc nghi có prompt injection: chuyển human triage.
- Không gửi phản hồi, đóng ticket, thay đổi phí, cam kết SLA hoặc gọi hệ thống bên ngoài trước khi có nhân viên approve.

---

# Phase 5 — EVALUATE

## 5.1. AI Readiness Checklist

| Tiêu chí                                   | Trạng thái                              | Bằng chứng / khoảng trống                                                                                                                                                   |
| ------------------------------------------ | --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Có dữ liệu mẫu/log sạch để test?           | [ ] **Chưa**                            | Repo chưa có ticket thật đã ẩn danh và gán nhãn; fixture tự tạo chỉ kiểm tra cấu trúc, không chứng minh chất lượng ngoài thực tế.                                           |
| Rủi ro khi AI sai nằm trong tầm kiểm soát? | [x] **Có, trong phạm vi pilot đề xuất** | Structured output, rule gate, schema/risk validator, HITL bắt buộc, không cấp quyền auto-action và manual fallback giới hạn blast radius. Vẫn phải stress-test trước pilot. |
| Stakeholder sẵn sàng đổi workflow?         | [ ] **Chưa xác minh**                   | Chưa có xác nhận của CSKH/Ban Quản lý về taxonomy, SOP/SLA mapping, luồng phê duyệt và trách nhiệm khi escalation.                                                          |

## 5.2. Quyết định cuối cùng

- [ ] **GO**
- [x] **NOT YET — Cần dữ liệu, baseline và xác nhận stakeholder**
- [ ] **NO-GO**

### Justification

LLM có fit về mặt tác vụ vì cần hiểu văn bản tự do và output có thể bị giới hạn ở dạng đề xuất. Tuy nhiên, hiện chưa đủ bằng chứng để khẳng định hiệu quả hoặc an toàn: baseline 8 phút chưa được đo trên ticket thật, chưa có tập dữ liệu gán nhãn, route taxonomy chưa được stakeholder duyệt và prototype chưa đại diện cho hệ thống vận hành. Vì vậy, quyết định trung thực là **NOT YET**, không phải GO.

Đây cũng chưa phải NO-GO: rủi ro có thể giảm đáng kể bằng kiến trúc rule + validator + HITL, và chi phí inference trong scenario nhỏ hơn nhiều so với chi phí lao động giả định. Quyết định cuối phải dựa vào pilot đối chứng với keyword-rule baseline, không dựa vào demo prompt đơn lẻ.

## 5.3. Điều kiện chuyển từ NOT YET sang GO

Chỉ chuyển sang GO khi đáp ứng đồng thời:

1. Đo touch time trên tối thiểu **100 ticket thật đã ẩn danh** và báo cáo median, P75/P95 cùng thời gian chờ tách riêng.
2. Xây tập đánh giá tối thiểu **200 ticket thường + 100 tình huống khẩn cấp/adversarial**, có nhãn chuẩn được nghiệp vụ review.
3. Đạt route accuracy **≥ 90%** và emergency recall **≥ 99%** trên holdout set.
4. Có **0 boundary violation** và **0 auto-action** trong toàn bộ acceptance test.
5. CSKH/Ban Quản lý phê duyệt taxonomy, ánh xạ đội xử lý/SLA, ngưỡng confidence và quy trình HITL/escalation.
6. LLM cao hơn keyword-rule baseline ít nhất **5 điểm phần trăm route accuracy** trên cùng holdout set. Nếu không đạt, chọn Rule vì đơn giản và dễ audit hơn.

## 5.4. Ước lượng giá trị và chi phí

### Công thức tổng quát

Với `N` là số ticket/tháng:

- Giờ thao tác hiện tại: `N × 8 / 60`.
- Giờ thao tác mục tiêu: `N × 2 / 60`.

- Năng lực được giải phóng: `N × 6 / 60`.
- Giá trị năng lực giả định: `(N × 6 / 60) × chi phí lao động/giờ`.

### Scenario để ra quyết định sơ bộ

> Đây là **scenario giả định**, không phải volume, năng suất hay chi phí nội bộ của Vinhomes.

| Hạng mục              |                 Giả định / phép tính |                  Kết quả |
| --------------------- | -----------------------------------: | -----------------------: |
| Volume                |          `100 ticket/ngày × 22 ngày` |   **2.200 ticket/tháng** |
| Giờ hiện tại          |                     `2.200 × 8 / 60` |      **293,3 giờ/tháng** |
| Giờ mục tiêu          |                     `2.200 × 2 / 60` |       **73,3 giờ/tháng** |
| Năng lực giải phóng   |                     `2.200 × 6 / 60` |        **220 giờ/tháng** |
| Giá trị năng lực      |              `220 × 100.000 VND/giờ` | **22.000.000 VND/tháng** |
| Chi phí pilot một lần |     `10 person-days × 1.500.000 VND` |       **15.000.000 VND** |
| Monitoring định kỳ    | `1 person-day/tháng × 1.500.000 VND` |  **1.500.000 VND/tháng** |

**Chi phí model:** Với 2.200 ticket/tháng, giả định 700 input token và 250 output token/ticket:

```text
Input  = 2.200 × 700 = 1.540.000 token
Output = 2.200 × 250 =   550.000 token

Chi phí = 1,54 × 0,30 USD + 0,55 × 2,50 USD
         = 0,462 USD + 1,375 USD
         = 1,837 USD ≈ 1,84 USD/tháng
```

Trang giá chính thức tại thời điểm kiểm tra **24/07/2026** niêm yết Gemini 2.5 Flash standard paid tier ở mức **0,30 USD/1 triệu input token** và **2,50 USD/1 triệu output token**. Nguồn: [Gemini Developer API pricing](https://ai.google.dev/gemini-api/docs/pricing).

Con số 1,84 USD chưa gồm thuế, tỷ giá, hạ tầng, lưu trữ log, quan sát hệ thống, bảo mật, đánh giá định kỳ, token retry hoặc overhead system prompt. Giá model và tỷ giá phải được cập nhật lại vào ngày nộp/pilot.

### Cách diễn giải đúng

- **22 triệu VND/tháng là giá trị năng lực giả định**, không phải tiền tiết kiệm hay lợi nhuận đã hiện thực hóa.
- Không tuyên bố ROI thực tế trước khi xác minh volume, touch time, chi phí lao động, chất lượng output và mức độ nhân sự có thể tái phân bổ.
- Nếu LLM không vượt rule baseline tối thiểu 5 điểm phần trăm, chi phí vận hành và rủi ro bổ sung không được biện minh chỉ bởi inference rẻ.

---
