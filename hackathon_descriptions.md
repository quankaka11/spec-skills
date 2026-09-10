# HBLAB AI HACKATHON #02 — SPEC BATTLE
## Tài liệu nền: Mô tả cuộc thi · Miền tri thức · Thư viện tài liệu để xây LLM Wiki / PageIndex

> **Nguồn:** tổng hợp từ website chính thức HBLAB AI Hackathon, trang thể lệ `spec-battle.html`, Google Doc thông báo của BTC, hợp nhất từ 3 bản phân tích độc lập (đã lược bỏ phần suy diễn sai — xem Phụ lục B).
> **Cập nhật:** 04/09/2026; **tham số cập nhật sau họp BTC 09/09** (§1.5, §6.3).
> **NGUỒN SỰ THẬT CHO THAM SỐ: `knowledge/00-luat-choi.md` §A.** File này là tài liệu nền — nó giải thích *vì sao* các kỹ thuật được chọn. Khi một con số ở đây khác `knowledge/00` §A thì **`knowledge/00` thắng**; các số cũ được giữ lại ở chỗ chúng còn giá trị lịch sử (số đo diễn tập, ngân sách 3.000 từ) và được chú thích tại chỗ.
> **Tóm tắt tham số chốt 09/09:** nộp **markdown ≤ 6.000 token** (sơ đồ mermaid, không ảnh) · hỏi AI Khách hàng **5 câu, mỗi lượt 1 câu, 5.000 token, KHÔNG memory**, ảnh ≤3 lần · điểm **CÔNG trúng +2 / THỦ đỡ +1 / CÔNG vô hiệu −1** · spec đối thủ markdown tải về được · **kháng nghị chỉ cho ca CÔNG bị VÔ HIỆU**, nộp bằng text gửi AI · không sửa test sau khi nộp · được xem lại hội thoại · câu hỏi về vận hành hệ thống thi là thông tin bảo mật.

---

## §0. CÁCH DÙNG TÀI LIỆU NÀY

Tài liệu được thiết kế làm **hạt giống cho một miền tri thức (knowledge domain)**, không phải để đọc một lượt rồi bỏ.

| Phần | Vai trò trong pipeline của bạn |
|---|---|
| §1 Mô tả cuộc thi | Ground truth về luật chơi. Nạp vào system prompt của mọi agent. |
| §2 Phân tích chiến lược | Định hướng hành vi cho agent (tại sao ưu tiên A hơn B). |
| §3 Miền nghiệp vụ "Đặt giữ hàng" | Ontology + question bank. Nguồn cho RAG domain. |
| §4 Nghề viết spec | Rule set cho agent `spec-writer`. |
| §5 Đối kháng công/thủ | Rule set cho agent `red-teamer` + `defender`. |
| §6 Vận hành | Playbook con người, không phải cho AI. |
| **§7 Thư viện tài liệu** | **Danh mục để đi tìm PDF → dựng LLM Wiki / PageIndex.** |
| §8 Blueprint skills & agents | Kiến trúc đề xuất cho bộ agent, map ngược về §7. |

**Thứ tự thực thi đề xuất:** đọc §1–§2 → dùng §7 để thu PDF → index bằng PageIndex → dựng agent theo §8 → dùng §3–§5 làm bộ test/eval cho agent.

---
---

# PHẦN I — MÔ TẢ CUỘC THI

## 1.1. Thông tin cốt lõi

| Mục | Nội dung |
|---|---|
| Tên | AI Hackathon 2026 — Kỳ #02, chủ đề **Spec Battle** |
| Slogan | REAL WORK, REAL IMPACT |
| Đề bài | Tính năng **"Đặt giữ hàng"** trên nền tảng **E-commerce** |
| Đối tượng | Toàn bộ thành viên HBLAB, không giới hạn vị trí/phòng ban |
| Đội hình | 02–03 thành viên. Khuyến khích đa phòng ban. Khuyến khích có Manager đồng hành |
| Ràng buộc | Mỗi cá nhân chỉ 01 đội/kỳ. Chốt danh sách là không đổi thành viên |
| Yêu cầu kỹ thuật | **Không cần biết code** |
| Deadline đăng ký | **12h00 Thứ 4, 09/09** |
| Ngày thi | **Thứ 7, 12/09** |
| Địa điểm | Offline tại HBLAB Việt Nam, Đà Nẵng, Nhật Bản — kết nối online giữa các đầu cầu |
| Giải thưởng | Nhất 5.000.000đ · Nhì 3.000.000đ · Ba 2.000.000đ |
| BGK | Công bố trong ngày thi |
| Quy mô | 20–30 đội (theo website), phân đối thủ **ngẫu nhiên** |
| Contact | HienLT (SC), UyenBHT (SC) |

## 1.2. Timeline

```
04/09    ── BTC công bố chủ đề & đề bài
04–09/09 ── Đăng ký, BTC chốt danh sách
09/09    ── Họp BTC & các đội: phổ biến thể lệ, quy trình, giải đáp   ← BẮT BUỘC DỰ
12/09    ── NGÀY THI CHÍNH THỨC
```

Buổi **09/09 đã diễn ra** và chốt các con số từng bỏ trống (hạn mức token, công thức điểm, cơ chế kháng nghị, định dạng nộp). Xem §1.5 và §6.3; bảng tham số hiện hành ở `knowledge/00-luat-choi.md` §A.

## 1.3. Agenda ngày thi 12/09

| Giờ | Nội dung | Chi tiết |
|---|---|---|
| 9:00–9:30 | Khai mạc + **bắt đầu vai THỦ** | Check-in, phổ biến thể lệ, **công bố brief** |
| 9:30–12:00 | Viết & nộp spec | **Markdown, tối đa 6.000 token** (chốt 09/09; trước đó website ghi 3.000 từ). Sau 12:00 khóa, không sửa được |
| 12:00–13:00 | Nghỉ trưa | BTC lo ăn trưa |
| 13:00–15:00 | **Bắt đầu vai CÔNG** | Nhận spec của **3 đội còn lại**, nộp **5 test / mỗi spec** (= 15 test) |
| 15:00–16:00 | Máy chạy **60 test** | Bảng điểm cập nhật trực tiếp trên màn hình lớn |
| 16:00–17:00 | Kháng nghị | **Tối đa 3 ca/đội.** Trọng tài xử có/không, vài phút mỗi ca |
| 17:00 | Công bố bảng điểm cuối & trao giải | |

**Suy luận về cấu trúc giải:** 4 đội × 3 đối thủ × 5 test = **60 test** → các đội được chia thành **pool 4 đội**. Với quy mô 20–30 đội, rất có thể nhiều pool chạy song song. Hệ quả:

- Bạn chỉ đối đầu trực tiếp 3 đội trong pool → mỗi test đều đắt.
- Nhưng bảng điểm có thể so toàn giải → cần điểm phòng thủ **tuyệt đối**, không chỉ hơn 3 đội cùng pool.
- **Phải hỏi BTC:** xếp giải theo pool hay theo toàn giải? (§6.3 câu 6)

## 1.4. Cơ chế thi đấu

Mỗi đội chơi **cả hai vai** trong cùng một ngày: buổi sáng là THỦ (viết spec), buổi chiều là CÔNG (bắn spec đối thủ).

### Ba tác nhân AI

| Tác nhân | Biết gì | Vai trò |
|---|---|---|
| **AI Khách hàng** | Nắm **specs thật** của đề bài | Chỉ trả lời điều đội **chủ động hỏi**. Ở pha chấm: quyết định tình huống có nằm trong phạm vi specs thật hay không, và đưa **đáp án chuẩn** |
| **AI Executor** | **CHỈ đọc bản spec bị bắn** | Trả lời tình huống chỉ theo nội dung tìm thấy trong spec. **Không được xem** brief, specs thật, hay hội thoại của đội |
| **AI So khớp** | Nhận 2 đầu ra | So sánh **ý nghĩa** giữa đáp án chuẩn và câu trả lời của Executor, sinh trạng thái + lưu **lý do đối chiếu làm bằng chứng** |

### Luồng một test

```
                 Tình huống do đội CÔNG tạo
                    ↙                  ↘
   ┌────────────────────────┐   ┌────────────────────────┐
   │ NHÁNH NGUỒN SỰ THẬT    │   │ NHÁNH SPEC BỊ BẮN      │
   │ Tình huống + specs thật│   │ Tình huống + spec      │
   │          ↓             │   │          ↓             │
   │  AI Khách hàng đọc     │   │  AI Executor chỉ đọc   │
   │  specs thật            │   │  spec được cấp         │
   │          ↓             │   │          ↓             │
   │  ĐÁP ÁN CHUẨN          │   │  CÂU TRẢ LỜI THEO SPEC │
   └────────────────────────┘   └────────────────────────┘
                    ↘                  ↙
                   AI So khớp đối chiếu ý nghĩa
                              ↓
                  TRÚNG / TRƯỢT / VÔ HIỆU
```

### Ba trạng thái kết quả

| Trạng thái | Nghĩa | Ai được lợi |
|---|---|---|
| **TRÚNG** | Executor trả lời **lệch** specs thật vì spec có lỗ hổng | Đội CÔNG ghi điểm, đội THỦ mất điểm |
| **TRƯỢT** | Executor trả lời **khớp** specs thật — spec đã bảo vệ được tình huống | Đội THỦ được bảo toàn, đội CÔNG mất 1 lượt test |
| **VÔ HIỆU** | Tình huống **nằm ngoài phạm vi** specs thật | Đội CÔNG đốt 1 test vô ích |

### Ví dụ chuẩn của BTC (đề giả định: app nghỉ phép)

- **TRÚNG** — *"Nhân viên xin nghỉ qua một ngày lễ, tính ngày phép thế nào?"* → Spec không mô tả xử lý ngày lễ → Executor tính cả ngày lễ → Intent thật: ngày lễ không tính. ⇒ Lỗ hổng.
- **TRƯỢT** — *"Đơn bị từ chối có sửa & nộp lại được không?"* → Spec mô tả rõ → Executor: đưa về nháp để sửa và nộp lại → khớp intent. ⇒ Spec đủ vững.
- **VÔ HIỆU** — *"Nghỉ không lương dài ngày thì bảo hiểm xử lý thế nào?"* → AI Khách hàng: nghiệp vụ ngoài phạm vi ứng dụng. ⇒ Test bị loại.

### Bốn yếu tố quyết định kết quả (BTC nêu; công thức điểm chưa công bố)

1. **Thông tin khai thác được** — lượng thông tin về specs thật thu được trong hạn mức cho phép.
2. **Độ vững của spec** — diễn đạt rõ hành vi, ngoại lệ, ranh giới để giảm khoảng trống suy diễn.
3. **Khả năng khai thác spec** — mức độ tình huống làm lộ điểm mập mờ của đối thủ.
4. **Kỷ luật phạm vi** — tránh đốt test vào tình huống ngoài bài toán.

## 1.5. Các hạn mức

**Đã chốt sau họp 09/09** (nguồn sự thật: `knowledge/00-luat-choi.md` §A):
- Spec: **markdown, ≤ 6.000 token** (BTC có thể chỉnh sau), sơ đồ chỉ dạng mermaid, **bản nộp không có ảnh**
- Hỏi AI Khách hàng: **5 câu · mỗi lượt đúng 1 câu · 5.000 token cho cả hỏi + trả lời · AI KHÔNG có memory** · gửi ảnh ≤ 3 lần
- Điểm: **CÔNG trúng +2 · THỦ đỡ được (TRƯỢT) +1 · CÔNG bị VÔ HIỆU −1**
- Vai CÔNG: **5 test / spec đối thủ**, 3 spec → **15 test**; spec đối thủ **tải về được** ở dạng markdown; **không sửa test sau khi nộp**
- Kháng nghị: **chỉ cho ca test CÔNG bị chấm VÔ HIỆU**, nộp bằng **text gửi AI**, BTC review; ≤3 ca/đội
- Được **xem lại hội thoại** với AI Khách hàng
- Câu hỏi về **vận hành hệ thống thi** (model của 3 tác nhân, prompt Executor, cách chấm bên trong): **thông tin bảo mật, BTC không trả lời**
- Thời gian: 2h30 cho vai THỦ, 2h cho vai CÔNG

**Còn hở** (7 ô, chi tiết ở `knowledge/00` §A2): ảnh có tính vào 5.000 token không · AI Khách hàng còn mở sau 12:00 không · giao diện có hiển thị token không · xếp giải pool hay toàn giải · độ dài tối đa mỗi test · VÔ HIỆU của đối thủ có cho THỦ +1 không · được xem lý do đối chiếu trước khi kháng nghị không.

---
---

# PHẦN II — PHÂN TÍCH CHIẾN LƯỢC

## 2.1. Năm nhận định cấu trúc

**① Executor mù bối cảnh ⇒ spec phải TỰ CHỨA 100%.**
Mọi thứ "ai chả biết", "theo thông lệ e-commerce", "như bình thường" đều là lỗ hổng. Executor không có common sense về nghiệp vụ của bạn — nó chỉ có những gì bạn viết trong 6.000 token. Đây là khác biệt lớn nhất so với viết spec thật cho dev người thật.

**② AI Khách hàng chỉ trả lời khi được hỏi ⇒ elicitation là điểm nghẽn số 1.**
Không hỏi = không biết = spec trống = bị bắn. Chất lượng câu hỏi trong 2h30 buổi sáng quyết định 60–70% điểm phòng thủ. Đây là lý do **question bank chuẩn bị trước** là tài sản giá trị nhất của đội.

> **Cập nhật 09/09:** với hạn mức **5 câu**, question bank đổi vai — nó không còn là *danh sách để hỏi* mà là (a) nguyên liệu để gọt 5 câu mật độ cao và (b) **nguồn giá trị mặc định ngành để tự điền** phần không hỏi được. Xem `knowledge/20` §2.

**③ Token dùng chung cho hỏi + trả lời ⇒ tối ưu mật độ thông tin trên mỗi token.**
Câu hỏi mở ("kể tôi nghe về tính năng này") tốn token đầu ra khổng lồ nhưng nội dung loãng. Câu hỏi đóng dạng bảng ("liệt kê đúng các trạng thái của một hold và điều kiện chuyển") có mật độ cao gấp nhiều lần. Xem §5.6.

> **Cập nhật 09/09 — nhận định này đúng nhưng chưa đủ.** Hạn mức thật là **5 câu hỏi, mỗi lượt 1 câu, và AI không có memory**. Ràng buộc chặt hơn token: không hỏi tiếp được, không sửa format ở lượt sau mà không mất 20% tri thức của cả ngày. Kỹ thuật đúng là **một câu hỏi xin về một bảng**, tự chứa, có cap dòng/từ — và phần bài toán không hỏi được (~70%) phải **tự điền bằng mặc định ngành**, không bỏ trống. Xem `knowledge/20` §1, §3 và `knowledge/30` §1b.

**④ VÔ HIỆU là hình phạt kép ⇒ trinh sát phạm vi trước khi bắn.**
Test vô hiệu đốt 1 trong 5 lượt mà không lấy được gì. Ưu tiên bắn vào **vùng chắc chắn trong phạm vi** (core flow + ngoại lệ hiển nhiên của "đặt giữ hàng") thay vì vùng biên xa (bảo hiểm, kế toán, thuế).

**⑤ Ranh giới tri thức (knowledge boundary) là khái niệm trung tâm.**
Trong an toàn thông tin có *trust boundary* — nơi dữ liệu đi từ vùng tin cậy sang vùng không tin cậy. Ở Spec Battle có thứ tương đương và quan trọng hơn: **ranh giới giữa những gì bạn biết và những gì spec của bạn nói ra**. Mọi tri thức bạn có trong đầu nhưng không viết vào spec đều nằm sai phía ranh giới, và đó chính là bề mặt tấn công. Kỹ thuật vận dụng: sau khi viết xong, đọc lại spec **như một người chưa từng nghe brief**. Xem §5.4.

## 2.2. Bất đối xứng cần khai thác

| Sự thật | Hệ quả chiến thuật |
|---|---|
| Hạn mức spec rất chặt cho một tính năng nhiều ngoại lệ (6.000 token ≈ 2.100–2.200 từ tiếng Việt — chặt hơn mức 3.000 từ mà tài liệu này viết ban đầu) | Không thể liệt kê mọi case → phải viết **luật bao quát (catch-all rules)** thay vì liệt kê. Xem §5.5 |
| Máy so khớp theo **ý nghĩa**, không theo từ khóa | Spec cần đúng *ý*, không cần trùng *chữ* với specs thật. Nhưng mơ hồ ngữ nghĩa vẫn bị bắt |
| Bạn bắn 3 spec, nhưng chỉ có 1 spec để bảo vệ | Rủi ro phòng thủ tập trung hơn → **ưu tiên phòng thủ hơn công** nếu phải chọn khi cạn thời gian |
| Đối thủ cũng hỏi AI Khách hàng cùng specs thật | Sau buổi sáng, bạn **đã biết** phần lớn specs thật → dùng chính tri thức đó soi chỗ đối thủ *chưa hỏi tới*. Đây là chìa khóa vai CÔNG |
| Executor phải trả lời *gì đó* dù spec trống | Spec trống → Executor **đoán theo mặc định phổ biến nhất** (prior). Nếu specs thật khác mặc định phổ biến → TRÚNG gần như chắc chắn. Đây là mỏ vàng: **tìm chỗ specs thật đi ngược trực giác** |
| So khớp lưu **lý do đối chiếu** làm bằng chứng | Có nguồn dữ liệu để kháng nghị → phải xin xem trước khi kháng nghị (§6.3 câu 12) |

## 2.3. Quy tắc vàng cho vai CÔNG

> **Bắn vào chỗ specs thật có quy định "phản trực giác" mà spec đối thủ im lặng.**

Nếu spec im lặng, Executor sẽ đoán theo lẽ thường:
- lẽ thường **=** specs thật → TRƯỢT (mất công)
- lẽ thường **≠** specs thật → **TRÚNG**

Vì vậy buổi sáng, khi hỏi AI Khách hàng, hãy **đánh dấu riêng (⚠) mọi câu trả lời gây bất ngờ**. Danh sách ⚠ đó chính là băng đạn cho buổi chiều — và đồng thời là checklist bắt buộc phải có trong spec của mình.

## 2.4. Quy tắc vàng cho vai THỦ

> **Mọi câu trả lời ⚠ của AI Khách hàng phải truy vết được tới một luật có mã trong spec.**

Đây là **RTM (Requirements Traceability Matrix)** áp dụng ngược: thay vì truy vết requirement → test case, ta truy vết *đáp án của khách hàng → luật trong spec*. Bảng này là công cụ tự kiểm mạnh nhất của vai THỦ và là bằng chứng số 1 khi kháng nghị.

| ID | Câu trả lời AI Khách hàng (rút gọn) | ⚠ Phản trực giác? | Luật trong spec | Trạng thái |
|---|---|---|---|---|
| A-01 | TTL 120 phút, tính giờ đồng hồ liên tục | ⚠ | BR-04 | ✅ đã viết |
| A-02 | Guest KHÔNG được đặt giữ hàng | ⚠ | BR-11 | ✅ đã viết |
| A-03 | Giá không khóa, tính lại khi chốt đơn | ⚠⚠ | — | ❌ **THIẾU** |

Ô trống ở cột "Luật trong spec" = lỗ hổng chắc chắn bị bắn.

---
---

# PHẦN III — MIỀN NGHIỆP VỤ: "ĐẶT GIỮ HÀNG" TRÊN E-COMMERCE

Đây là phần chuẩn bị có ROI cao nhất: brief chưa công bố nhưng **domain đã biết**. Dựng sẵn bản đồ nghiệp vụ để buổi sáng 12/09 chỉ cần *xác nhận* thay vì *khám phá*.

## 3.1. Giải nghĩa nghiệp vụ

"Đặt giữ hàng" (*reserve / hold / pre-order hold*) là hành vi **tạm khóa một lượng tồn kho cho một khách hàng cụ thể trong một khoảng thời gian**, trước khi đơn hàng được thanh toán/xác nhận hoàn tất.

Ba biến thể thường gặp — **câu hỏi đầu tiên buổi sáng phải là: đề bài thuộc biến thể nào?**

| Biến thể | Mô tả | Đặc trưng |
|---|---|---|
| **Cart hold** | Giữ hàng khi bỏ vào giỏ / vào checkout | TTL ngắn (5–30 phút), tự động, không tiền |
| **Deposit reserve** | Khách đặt cọc để giữ hàng | TTL dài (giờ/ngày), có tiền, có refund policy |
| **Store pickup hold** | Giữ hàng tại cửa hàng để khách đến lấy | Gắn với địa điểm, giờ mở cửa, người nhận |

Biến thể quyết định phần lớn nội dung spec: nếu là *deposit reserve* thì cụm luật về tiền (§N5) chiếm nhiều từ nhất; nếu là *cart hold* thì cụm luật về đồng thời/tồn kho (§N4) chiếm nhiều nhất.

## 3.2. Ontology — thực thể & khái niệm cần định nghĩa

```
Product ──< SKU/Variant ──< Inventory (per Warehouse/Store)
                                  │
                                  ├── On-hand         (tồn thực tế)
                                  ├── Reserved        (đang bị giữ)
                                  ├── Available (ATP) (= On-hand − Reserved − Safety stock)
                                  └── Safety stock    (đệm không bán)

Customer ──< Hold/Reservation ──< HoldLine (SKU, qty)
                     │
                     ├── TTL / expires_at
                     ├── Status
                     ├── Deposit (nếu có)
                     └── Fulfillment target (warehouse/store)
```

**Glossary tối thiểu (~30 thuật ngữ) cần tự dựng:** SKU, variant, on-hand, reserved, ATP/available-to-promise, safety stock, allocation (soft vs hard), oversell, backorder, waitlist, TTL, grace period, hold, reservation, pre-order, deposit, refund, fulfillment, pick/pack/ship, warehouse vs store, multi-source inventory, cycle count, stock sync, idempotency, race condition, first-come-first-served, cooldown, blacklist, audit log, SLA.

> Executor không biết "hold", "reserve", "giữ hàng", "khóa hàng" có phải cùng một thứ. Glossary không phải trang trí — nó là **hàng rào chống đa nghĩa**.

## 3.3. State machine của một Hold — khung dựng sẵn

Trạng thái ứng viên (cần xác nhận với AI Khách hàng đúng tập nào tồn tại):

```
                 ┌──────────┐
   create ──────▶│ PENDING  │ (chờ xác nhận / thanh toán cọc)
                 └────┬─────┘
            confirm   │        timeout ──▶ EXPIRED
                      ▼
                 ┌──────────┐  extend ──▶ (ACTIVE, TTL mới)
                 │  ACTIVE  │  cancel ──▶ CANCELLED
                 └────┬─────┘  timeout ─▶ EXPIRED
            convert   │
                      ▼
                 ┌──────────┐
                 │FULFILLED │ (đã thành đơn / đã lấy hàng)
                 └──────────┘
```

**Với mỗi mũi tên phải trả lời được 6 câu:**
① ai được kích hoạt · ② điều kiện tiền đề · ③ tồn kho biến động thế nào · ④ tiền cọc xử lý thế nào · ⑤ thông báo gì cho ai · ⑥ có ghi audit log không.

Nếu spec có **bảng chuyển trạng thái đầy đủ** (mọi ô `state × event` đều có giá trị, kể cả "không hợp lệ — từ chối"), bạn đã bịt được cả loại lỗ hổng #6 và #8 trong §5.1.

## 3.4. Customer analysis — dùng làm giàn giáo elicitation, không phải deliverable

Spec Battle không yêu cầu nộp persona hay journey map. Nhưng ba công cụ này có giá trị **phát hiện khoảng trống** rất cao, vì specs thật gần như chắc chắn có luật khác nhau theo loại khách và theo bước hành trình.

**① Danh sách actor** — mỗi actor bị bỏ sót là một lỗ hổng loại #9:
guest (chưa đăng nhập) · member mới · member thân thiết/VIP · khách bị blacklist · CSKH · sale/telesale · nhân viên cửa hàng (POS) · quản lý kho · admin · **hệ thống/cron job** · cổng thanh toán · **ERP/WMS bên ngoài**.

**② Journey của một hold** — mỗi bước chuyển là một chỗ để hỏi "nếu thất bại ở đây thì sao?":
```
xem sản phẩm → bấm giữ hàng → (thanh toán cọc) → nhận xác nhận
    → chờ (nhận nhắc sắp hết hạn) → đến lấy / chốt đơn → hoàn tất
                                  ↘ hủy / hết hạn / hết hàng ↗
```

**③ Assumption log** — bảng: *giả định · mức ảnh hưởng nếu sai · đã xác nhận với AI Khách hàng chưa · bằng chứng*. Mọi dòng chưa xác nhận, ảnh hưởng cao là ưu tiên P0 cho question bank.

## 3.5. Question Bank — 14 nhóm câu hỏi nghiệp vụ

Danh sách này dùng cho **ba mục đích cùng lúc**: (a) hỏi AI Khách hàng buổi sáng, (b) checklist tự soi spec của mình, (c) checklist soi spec đối thủ buổi chiều.

### N1. Định nghĩa & phạm vi
- "Đặt giữ hàng" trong hệ thống này chính xác là gì? Cart hold, đặt cọc, hay giữ tại cửa hàng?
- Áp dụng cho **mọi sản phẩm** hay chỉ nhóm nào (hàng giá trị cao, hàng hot, hàng pre-order)?
- Có áp dụng cho hàng khuyến mãi / flash sale / hàng combo / hàng digital không?
- Kênh nào được dùng: web, app, POS tại cửa hàng, hotline, marketplace?
- **Liệt kê những nghiệp vụ NGOÀI phạm vi tính năng này.** ← câu hỏi quan trọng nhất để chống VÔ HIỆU buổi chiều

### N2. Thời hạn giữ (TTL) — vùng bị bắn nhiều nhất
- TTL bao lâu? Tính theo **giờ đồng hồ** hay **giờ làm việc**?
- Múi giờ nào? Khách ở múi giờ khác thì tính theo đâu?
- TTL có bao gồm **ngày lễ / Chủ nhật / ngoài giờ mở cửa** không?
- Có gia hạn được không? Bao nhiêu lần? Ai duyệt? TTL mới cộng dồn hay reset?
- Mốc bắt đầu TTL: lúc tạo request, lúc thanh toán cọc, hay lúc admin xác nhận?
- Hết hạn thì hủy **tự động ngay** hay chờ job? Job chạy mỗi bao lâu (grace period)?

### N3. Số lượng & giới hạn
- Tối đa bao nhiêu đơn vị/SKU cho 1 hold?
- Tối đa bao nhiêu hold **đồng thời** trên 1 khách?
- Giới hạn theo khách, theo SKU, theo ngày, hay theo tổng giá trị?
- Có **giữ một phần** không (khách muốn 10, tồn còn 6 — báo lỗi hay giữ 6)?
- Khách hủy rồi tạo lại ngay — có cooldown không?

### N4. Tồn kho & tranh chấp đồng thời
- Hold trừ vào tồn nào: giảm `Available` hay giảm cả `On-hand`?
- Tồn hiển thị cho khách khác có phản ánh hàng đang bị giữ không?
- Có cho phép **oversell** không? Ngưỡng bao nhiêu?
- Hai khách bấm giữ cùng lúc cho đơn vị cuối cùng — ai thắng, người sau nhận thông báo gì?
- Hàng bị mất/hỏng/kiểm kê lệch trong lúc đang hold — ưu tiên ai?
- Có **safety stock** không được đem giữ không?
- Đa kho / đa cửa hàng: hold gắn với một địa điểm cụ thể hay toàn hệ thống? Có chuyển kho không?

### N5. Đặt cọc & thanh toán
- Có yêu cầu cọc không? Cố định hay % giá trị? Tối thiểu/tối đa?
- Phương thức thanh toán nào? Có COD / trả sau không?
- Cọc **có hoàn** khi: khách hủy · hệ thống hủy · hết hạn · hết hàng do lỗi kho?
- Cọc có trừ vào tiền hàng khi chuyển thành đơn không?
- Thanh toán cọc thất bại / timeout cổng thanh toán → hold còn hiệu lực bao lâu?
- **Thanh toán thành công nhưng hold đã hết hạn trong lúc đó → xử lý thế nào?** ← case kinh điển, tỷ lệ TRÚNG rất cao

### N6. Giá & khuyến mãi
- Giá được **khóa** tại thời điểm giữ hàng, hay tính lại khi chốt đơn?
- Nếu giá giảm trong lúc hold — khách được giá nào?
- Voucher gắn vào hold có bị hết hạn giữa kỳ hold không?
- Flash sale kết thúc trong lúc hold — giá sale còn giữ không?
- Thuế/phí vận chuyển tính ở bước nào?

### N7. Hủy & hết hạn
- Ai được hủy: khách, CSKH, admin, hệ thống?
- Khách có hủy được sau khi đã đặt cọc không? Trước mốc nào?
- Hủy có phí không? Có ảnh hưởng uy tín/điểm khách hàng không?
- Hết hạn thì tồn được **giải phóng ngay** hay vào **waitlist**?
- Có waitlist không? Ưu tiên ai khi hàng được giải phóng?

### N8. Chuyển đổi thành đơn hàng
- Điều kiện để hold trở thành order?
- Khách phải làm thêm bước gì? Có xác nhận OTP/email không?
- Order được tạo mới hay hold "biến thành" order (giữ nguyên ID)?
- Nếu khách đổi SKU/số lượng khi chốt đơn — hold cũ xử lý ra sao?

### N9. Actor, quyền & trạng thái khách hàng
- **Khách chưa đăng nhập (guest)** có được đặt giữ hàng không?
- Khách mới vs khách thân thiết có TTL/hạn mức khác nhau không?
- Khách blacklist / lịch sử hủy nhiều — bị hạn chế gì?
- CSKH/admin được can thiệp gì: tạo hộ, gia hạn, hủy, chuyển hold sang khách khác?
- Có cần approval cho hold giá trị lớn không?

### N10. Thông báo & giao tiếp
- Thông báo ở những mốc nào: tạo, sắp hết hạn, hết hạn, hủy, chuyển đơn?
- "Sắp hết hạn" là trước bao lâu? Nhắc bao nhiêu lần?
- Kênh nào: email, SMS, push, in-app? Ai nhận (khách, sale, quản lý kho)?
- Nội dung bắt buộc phải có gì?

### N11. Hiển thị & UX
- Khách xem hold ở đâu? Thông tin nào bắt buộc hiển thị?
- Có đếm ngược không? Đồng hồ tính theo server hay client?
- Sản phẩm đang bị giữ hiển thị thế nào cho khách khác ("còn 3" / "hết hàng" / "đang được giữ")?

### N12. Ngoại lệ & lỗi hệ thống
- Sản phẩm bị **ngừng bán / xóa / ẩn** trong lúc đang bị hold?
- Kho đóng cửa / cửa hàng tạm ngưng hoạt động?
- Hệ thống downtime khiến TTL chạy quá — có bù giờ không?
- Dữ liệu tồn kho sync lỗi từ ERP — **nguồn nào là chân lý**?
- Request trùng lặp (khách bấm 2 lần) — có idempotency không?

### N13. Báo cáo, audit & phi chức năng
- Cần log những gì? Ai xem được?
- Báo cáo nào: tỷ lệ chuyển đổi hold→order, tỷ lệ hết hạn, hàng bị giữ tồn lâu?
- SLA/hiệu năng: bao lâu phải phản hồi khi bấm giữ hàng?
- Yêu cầu về dữ liệu cá nhân / lưu trữ?

### N14. Ma trận ưu tiên khi xung đột
- Khi 2 luật xung đột (VD: khách VIP hết hạn hold vs khách thường trong waitlist) — luật nào thắng?
- Thứ tự ưu tiên tổng quát của hệ thống là gì?

> Chuyển 14 nhóm này thành **~100–120 câu hỏi cụ thể**, xếp P0/P1/P2. Đó là artifact chuẩn bị số 1.

---
---

# PHẦN IV — NGHỀ VIẾT SPEC (VAI THỦ)

## 4.1. Nền tảng lý thuyết cần nắm

| Chủ đề | Tại sao cần cho cuộc thi này | Nguồn (chi tiết ở §7) |
|---|---|---|
| **Requirement vs Specification** | Brief là requirement mơ hồ; sản phẩm dự thi là specification. Lẫn hai thứ = spec kể chuyện chứ không quy định hành vi | BABOK ch.3; Wiegers ch.1–2 |
| **Functional vs Non-functional** | Spec thiếu NFR (thời gian, đồng thời, hiệu năng) là vùng dễ bị bắn | ISO/IEC/IEEE 29148 |
| **Đặc tính của requirement tốt** | Bộ tiêu chí tự soi: đầy đủ · nhất quán · không mơ hồ · khả thi · **kiểm chứng được** · truy vết được | ISO/IEC/IEEE 29148 §5.2; INCOSE Guide |
| **Cú pháp requirement (EARS)** | Khuôn câu cố định giúp Executor parse chính xác | Mavin et al., *EARS* |
| **Elicitation techniques** | Interview, questionnaire, hypothesis testing — chọn đúng kỹ thuật cho AI Khách hàng | BABOK ch.4 |
| **Use case & acceptance criteria** | Khung để không bỏ sót actor và luồng phụ | Cockburn |
| **Business rule** | Tách luật ra khỏi luồng, đánh mã BR-xx để tra cứu & kháng nghị | Ross, *Business Rule Concepts* |
| **Decision table / DMN** | Bắt buộc phủ hết tổ hợp điều kiện | OMG DMN; Copeland |
| **Glossary / Ubiquitous Language** | Hàng rào chống đa nghĩa | Evans, DDD ch.2 |
| **RTM (traceability)** | Truy vết đáp án khách hàng → luật trong spec (§2.4) | BABOK; BA Times |

## 4.2. Taxonomy tài liệu BA — và cái nào thực sự dùng ở Spec Battle

Trong dự án thật, một bộ tài liệu BA đầy đủ gồm nhiều loại. Ở cuộc thi này bạn chỉ nộp **một** tài liệu markdown ≤6.000 token, nên phải biết **nén những loại nào vào đâu**:

| Tài liệu | Nội dung gốc | Dùng ở Spec Battle |
|---|---|---|
| **BRD** (Business Requirements Doc) | Mục tiêu kinh doanh, phạm vi, deliverables, lợi ích | ❌ Bỏ. Không có điểm cho "vì sao làm" |
| **FRD** (Functional Requirements Doc) | Use case, logic nghiệp vụ, hành vi hệ thống | ✅ **Lõi.** Chiếm ~70% spec |
| **NFRD** (Non-Functional Requirements Doc) | Bảo mật, hiệu năng, chịu lỗi, mở rộng | ⚠️ Nén còn ~100 từ, chỉ giữ NFR có thể bị bắn (timeout, đồng thời, SLA) |
| **SRS** (Software Requirements Spec, IEEE 830 / ISO 29148) | Hợp nhất FRD + NFRD + định nghĩa hệ thống | ✅ **Đây là hình dạng của bài dự thi.** Dùng cấu trúc SRS rút gọn |
| **RTM** (Requirements Traceability Matrix) | Ma trận yêu cầu ↔ test case | ✅ Dùng **nội bộ**, không nộp. Xem §2.4 |
| **Glossary** | Định nghĩa thuật ngữ | ✅ Bắt buộc, ~200 từ |
| **Sơ đồ kiến trúc / DFD / ERD** | Cấu trúc hệ thống | ⚠️ Chỉ khi BTC cho phép hình. Ưu tiên **bảng** vì chắc chắn Executor đọc được |

**Kết luận:** bài dự thi = **SRS rút gọn, nặng business rule, không có phần biện luận kinh doanh.**

## 4.3. Bốn định dạng đặc tả phải thuần thục

| Định dạng | Dùng cho | Vì sao chống bắn tốt |
|---|---|---|
| **Decision table** | Luật nhiều điều kiện (TTL × loại khách × loại hàng) | Bắt buộc phủ hết tổ hợp; ô trống lộ ra ngay khi viết |
| **State transition table** | Lifecycle của hold | Mỗi `(state × event)` có 1 ô → không còn chuyển trạng thái không xác định |
| **Gherkin / Given-When-Then** | Hành vi cụ thể, acceptance criteria | Executor parse rất tốt vì có điều kiện + kết quả tách bạch |
| **Rule statement có mã (BR-xx)** | Luật nghiệp vụ độc lập | Dễ tham chiếu chéo, dễ viết catch-all, **dễ trích khi kháng nghị** |

## 4.4. Mẫu requirement kiểm thử được

**Không tốt:** "Hệ thống phản hồi nhanh và an toàn."
**Không tốt:** "Hold sẽ được hủy khi hết hạn."

**Tốt (có đủ 6 thành phần):**
> `BR-04` — **KHI** một hold ở trạng thái `ACTIVE` đạt mốc `expires_at` (tính theo Asia/Ho_Chi_Minh, giờ đồng hồ liên tục kể cả ngày lễ), **THÌ** hệ thống PHẢI (a) chuyển hold sang `EXPIRED`, (b) giảm `Reserved` và tăng `Available` đúng số lượng của mọi HoldLine, (c) hoàn tiền cọc về phương thức gốc trong 24 giờ, (d) gửi email cho khách và in-app notification cho sale phụ trách, (e) ghi audit log gồm `hold_id`, thời điểm, actor = `system`. Hệ thống KHÔNG ĐƯỢC tự gia hạn trong trường hợp này.

**Sáu thành phần bắt buộc của một luật:** ① điều kiện kích hoạt (có mốc, có đơn vị) · ② chủ thể hành động · ③ hành động chính · ④ tác động phụ (tồn kho / tiền / thông báo / log) · ⑤ điều KHÔNG được làm · ⑥ mã để tham chiếu.

## 4.5. Spec như một CONTRACT cho máy đọc

Executor là một LLM. Vậy hãy viết spec bằng tư duy thiết kế contract cho LLM:

| Khái niệm | Áp dụng vào spec |
|---|---|
| **Task definition** | §1 nêu rõ: tài liệu này quy định hành vi hệ thống cho tính năng đặt giữ hàng — và **không** quy định gì khác |
| **Input contract** | Danh sách actor và loại request hợp lệ. Request ngoài danh sách → xử lý theo catch-all |
| **Output contract** | Với mỗi luật, kết quả phải xác định: trạng thái cuối + biến động tồn kho + biến động tiền + thông báo |
| **Refusal condition** | §0.5: tình huống không khớp luật nào → từ chối, giữ nguyên trạng thái (thay vì để Executor tự sáng tác) |
| **Precedence rule** | §0.6: thứ tự ưu tiên khi hai luật cùng áp dụng |
| **Eval set** | Tự dựng ~20 tình huống khó rồi **tự đóng vai Executor** đọc spec trả lời. Chỗ nào trả lời được nhiều hơn một cách = lỗ hổng |

Bước "eval set" là kỹ thuật phòng thủ bị bỏ qua nhiều nhất. Nó chính là *đội CÔNG mô phỏng* chạy trước khi khóa spec.

## 4.6. Thư viện "từ ngữ mơ hồ" — danh sách đen tuyệt đối tránh

Học thuộc để **không viết vào spec của mình** và **Ctrl+F ngay trong spec đối thủ**.

| Loại | Từ/cụm | Cách viết lại |
|---|---|---|
| Định lượng mờ | nhanh chóng, kịp thời, một khoảng thời gian, đủ lâu, hợp lý | Con số + đơn vị + mốc bắt đầu |
| Số lượng mờ | nhiều, một số, phần lớn, tối đa cho phép | Số cụ thể hoặc công thức |
| Tùy chọn | có thể, nên, thường, tùy trường hợp | PHẢI / KHÔNG ĐƯỢC / ĐƯỢC PHÉP |
| Bỏ ngỏ | v.v., và các trường hợp tương tự, …, như trên | Liệt kê đủ, hoặc luật bao quát tường minh |
| Chủ ngữ ẩn | hệ thống xử lý, được cập nhật, sẽ bị hủy | Ai/cái gì làm, khi nào, tác động lên gì |
| Trực giác ngầm | như thông lệ, theo chuẩn ngành, hiển nhiên | Viết ra thành luật |
| Đại từ trôi | nó, điều này, cái đó, trạng thái này | Nhắc lại danh từ đầy đủ |
| Điều kiện treo | nếu cần thiết, khi phù hợp, trong trường hợp đặc biệt | Điều kiện kiểm chứng được |
| Toán tử mờ | và/hoặc, trước/sau đó | Chọn dứt khoát AND hay OR |
| So sánh không mốc | tốt hơn, nhanh hơn, ưu tiên cao hơn | Nêu mốc so sánh |
| Thiếu bao gồm/loại trừ | "trong vòng 2 giờ", "đến 17:00" | Nêu rõ `[đầu, cuối)` hoặc `≤ / <` |

## 4.7. Ngân sách spec — phân bổ đề xuất

> **Cập nhật 09/09:** hạn mức thật là **6.000 token, đích 5.400** (≈ 2.100–2.200 từ tiếng Việt), không phải 3.000 từ. Bảng dưới là phân bổ *theo từ* của bản gốc, giữ lại vì tỷ lệ giữa các mục vẫn đúng. **Bảng phân bổ theo token đang dùng: `knowledge/30` §1**, trong đó §7 và §10 bị hạ xuống "chỉ viết nếu còn đệm" và tổng đích là 5.350 token cho 9 mục.

| Phần | Từ | Ghi chú |
|---|---|---|
| **§0. Nguyên tắc giải nghĩa & luật mặc định (catch-all)** | 200 | **Phần đắt giá nhất.** Xem §5.5 |
| §1. Phạm vi: TRONG / NGOÀI phạm vi | 150 | Ranh giới rõ giúp Executor không suy diễn bừa |
| §2. Glossary | 200 | Chỉ thuật ngữ có nguy cơ hiểu sai |
| §3. Actor & quyền (bảng) | 200 | Thiếu bảng này = lỗ hổng #9, #10 |
| §4. Mô hình tồn kho & công thức ATP | 200 | |
| §5. State machine + bảng chuyển trạng thái | 350 | Dùng bảng, cực tiết kiệm từ |
| §6. Luật nghiệp vụ BR-01… (bảng/decision table) | 900 | Xương sống của spec |
| §7. Luồng chính + luồng phụ (rút gọn) | 300 | |
| §8. Ngoại lệ & xử lý lỗi | 300 | |
| §9. Thông báo (bảng: mốc × kênh × người nhận) | 100 | |
| §10. Phi chức năng & audit | 100 | |
| **Đệm** | **~0–200** | Giữ lại để bổ sung phút cuối |

**Kỹ thuật tiết kiệm từ:**
- Bảng thay đoạn văn (tiết kiệm ~40% từ cho cùng lượng thông tin)
- Đánh mã và tham chiếu: `áp dụng BR-07` thay vì lặp lại nội dung

> **Đếm bằng token đổi hai kết luận** (`knowledge/30` §5): (a) dấu `|` của bảng là **token thật** — bảng chỉ đáng dùng khi có ≥3 cột nội dung, bảng 2 cột nên viết thành danh sách; (b) **ký hiệu và công thức rẻ hơn nhiều so với tiếng Việt** — `Reserved += qty` (~6 token) thay cho một câu 35 token, khai báo ký hiệu một lần ở §0 rồi dùng khắp spec là kỹ thuật lãi nhất.
- Công thức thay lời: `ATP = OnHand − Reserved − Safety`
- Một luật tổng quát thay 5 ví dụ
- Ký hiệu quy ước khai báo một lần ở §0 (VD: `[a, b)` nghĩa là bao gồm a, không bao gồm b)

## 4.8. Bảy nguyên tắc viết spec cho AI Executor đọc

1. **Cấu trúc hơn văn phong.** Heading rõ, đánh số, một luật một dòng. Không viết văn.
2. **Mỗi luật là một câu điều kiện đầy đủ:** `KHI <điều kiện> THÌ <chủ thể> PHẢI <hành động>, và <tác động phụ>.`
3. **Định lượng mọi thứ.** Mọi con số có đơn vị, mốc tham chiếu và quy ước bao gồm/loại trừ.
4. **Nêu cả điều KHÔNG được làm.** Cấm tường minh chặn Executor suy diễn.
5. **Không tham chiếu ra ngoài.** Không có "theo tài liệu A", "như đã thống nhất" — Executor không có A.
6. **Ưu tiên hóa xung đột.** Có một mục nêu thứ tự ưu tiên khi hai luật đụng nhau.
7. **Không để trống ô nào.** Mọi bảng trạng thái/quyết định phải điền hết, kể cả bằng "không hợp lệ — từ chối theo §0.5".

## 4.9. Quy trình viết spec dưới áp lực 2h30

```
9:00–9:30   Nhận brief. Đọc 2 lượt. Xác định biến thể (cart hold / cọc / pickup).
            → Lọc question bank xuống P0 (~25 câu).
9:30–11:00  ELICITATION (không viết spec). Chạy 6 lượt hỏi theo §6.4.
            → Điền bảng RTM (§2.4). Đánh dấu ⚠.
11:00–11:15 Viết §0 catch-all — SAU KHI đã biết specs thật (bắt buộc theo thứ tự này).
11:15–11:45 Điền template: §6 business rules → §5 state table → §3 actor → còn lại.
11:45–11:55 Red team nội bộ: chạy eval set (§4.5), Ctrl+F danh sách đen (§4.6),
            đối chiếu mọi dòng ⚠ trong RTM.
11:55–12:00 Đếm từ ≤ 3.000. Nộp. Lưu 1 bản copy cho kháng nghị.
```

**Thứ tự quan trọng:** *hỏi xong → mới viết §0*. Đảo ngược sẽ tạo catch-all mâu thuẫn với specs thật (xem cảnh báo §5.5).

---
---

# PHẦN V — ĐỐI KHÁNG: TẤN CÔNG & PHÒNG THỦ

## 5.1. Taxonomy 15 loại lỗ hổng

> **Đã mở rộng thành 21 loại** ở `knowledge/50` §1: #16 luật bất khả thi · #17 luật tự đánh bại mục tiêu · #18 hạn mức không cưỡng chế được · #19 thiếu luật cho thất bại của phụ thuộc ngoài · #20 phạm vi NGOÀI tự đặt rộng hơn brief · **#21 lệch đồng thuận chéo** (chỉ khả thi từ 09/09, khi biết spec đối thủ tải về được ở dạng markdown). spec

Dùng cho **cả hai vai**: THỦ để vá, CÔNG để bắn.

| # | Loại lỗ hổng | Cách nhận diện | Mẫu tình huống bắn |
|---|---|---|---|
| 1 | **Khoảng trống hoàn toàn** (silent gap) | Cả một nhóm nghiệp vụ không được nhắc | "Khách hủy sau khi đã đặt cọc thì tiền cọc xử lý thế nào?" |
| 2 | **Ngoại lệ vắng mặt** | Chỉ mô tả happy path | "Thanh toán thành công nhưng hold vừa hết hạn 3 giây trước — hệ thống làm gì?" |
| 3 | **Định lượng mơ hồ** | "nhanh", "một khoảng thời gian" | "TTL cụ thể là bao nhiêu phút và tính từ mốc nào?" |
| 4 | **Biên & off-by-one** | Có số nhưng không rõ bao gồm mốc | "Đúng giây thứ 3600 thì hold còn hiệu lực không?" |
| 5 | **Mâu thuẫn nội tại** | Hai chỗ nói khác nhau | Tạo tình huống mà 2 luật cho kết quả trái nhau |
| 6 | **Trạng thái không xác định** | State machine thiếu ô | "Đang PENDING mà admin hủy thì sang trạng thái nào?" |
| 7 | **Đồng thời / race condition** | Không nói ai thắng | "Hai khách bấm giữ đơn vị cuối cùng cùng thời điểm — ai được?" |
| 8 | **Thiếu ưu tiên khi xung đột** | Nhiều luật, không thứ tự | Tình huống 2 luật cùng áp dụng |
| 9 | **Actor bị bỏ sót** | Chỉ nói "khách hàng" | "Khách chưa đăng nhập có đặt giữ hàng được không?" |
| 10 | **Quyền hạn không rõ** | Không có bảng quyền | "CSKH có gia hạn hold của khách khác được không?" |
| 11 | **Đơn vị / múi giờ / lịch** | Không nêu timezone, giờ làm việc | "Khách ở Nhật đặt giữ 23:00 JST, TTL tính theo giờ nào?" |
| 12 | **Rollback / bù trừ thiếu** | Không nói hoàn nguyên | "Hệ thống lỗi giữa lúc trừ tồn — tồn kho về đâu?" |
| 13 | **Phụ thuộc ngoài không định nghĩa** | Nhắc ERP/cổng thanh toán nhưng không nêu hợp đồng | "ERP báo tồn khác hệ thống — theo nguồn nào?" |
| 14 | **Từ ngữ đa nghĩa** | Dùng lẫn "giữ hàng"/"đặt trước"/"khóa hàng" | "'Đặt trước' và 'đặt giữ hàng' là cùng một nghiệp vụ?" |
| 15 | **Luật phản trực giác bị im lặng** | Specs thật khác lẽ thường mà spec không nói | Bắn đúng chỗ bạn đã biết là phản trực giác ⇒ **tỷ lệ TRÚNG cao nhất** |

## 5.2. Bốn kỹ thuật thiết kế test sắc

**① Truy vấn khoảng trống trực tiếp (highest yield).**
Chọn một luật phản trực giác (⚠) bạn biết từ AI Khách hàng, kiểm tra spec đối thủ có nói không. Nếu im lặng → bắn thẳng.

**② Tình huống buộc phải chọn.**
Đặt câu hỏi mà Executor **không thể trả lời chung chung**: hỏi con số, hỏi trạng thái cuối, hỏi ai thắng. Trả lời mơ hồ vẫn dễ bị So khớp đánh lệch.

**③ Ép hai luật đối đầu.**
Xây tình huống rơi vào giao của 2 luật trong spec. Nếu spec không có mục ưu tiên → Executor tự chọn → ~50% lệch.

**④ Thăm dò biên.**
Đúng mốc, mốc ±1, giá trị 0, giá trị max, giá trị âm, tập rỗng, chuỗi rỗng, trùng lặp. Rẻ và tỷ lệ trúng cao vì spec hay quên "bao gồm/không bao gồm".

## 5.3. Phân bổ 5 test / spec — công thức đề xuất

| Test | Loại | Lý do |
|---|---|---|
| 1 | Khoảng trống lớn nhất phát hiện được (#1/#15) | Xác suất trúng cao nhất |
| 2 | Ngoại lệ / lỗi hệ thống (#2, #12) | Đa số spec chỉ viết happy path |
| 3 | Biên hoặc đơn vị/múi giờ (#4, #11) | Rẻ, hay quên |
| 4 | Đồng thời hoặc ưu tiên xung đột (#7, #8) | Hầu như không ai viết |
| 5 | Actor bị bỏ sót hoặc quyền hạn (#9, #10) | Dễ soi nhanh |

**Không** dùng cả 5 test cho cùng một chủ đề — nếu chủ đề đó được spec che kín thì mất trắng.

## 5.4. Quy trình soi spec đối thủ trong 10 phút

```
Phút 0–2  │ Đọc mục lục / heading. Đối chiếu checklist 14 nhóm (§3.5).
          │ → Nhóm nào KHÔNG xuất hiện = ứng viên test hạng A.
Phút 2–4  │ Ctrl+F các từ trong danh sách đen (§4.6).
          │ → Mỗi hit là một điểm mơ hồ định lượng.
Phút 4–6  │ Tìm bảng trạng thái / decision table.
          │ → Không có = lỗ hổng #6/#8. Có = tìm ô trống.
Phút 6–8  │ Kiểm 5 mục hiếm ai viết: đồng thời, timezone,
          │ guest user, rollback, ưu tiên xung đột.
Phút 8–10 │ Chốt 5 test theo công thức §5.3, viết tình huống cụ thể.
          │ Kiểm phạm vi trước khi nộp (chống VÔ HIỆU).
```

**Kỹ thuật "knowledge boundary sweep":** đọc spec đối thủ và tự hỏi *"tri thức nào mình có từ AI Khách hàng mà tài liệu này không chứa?"* Danh sách chênh lệch đó chính là bề mặt tấn công (§2.1 ⑤).

## 5.5. Kỹ thuật CATCH-ALL RULE — vũ khí phòng thủ mạnh nhất

6.000 token không đủ liệt kê mọi ngoại lệ. Giải pháp: viết **luật mặc định bao quát** ngay đầu spec, để Executor có chỗ tra khi gặp tình huống bạn không lường tới.

```
§0. NGUYÊN TẮC GIẢI NGHĨA & LUẬT MẶC ĐỊNH

0.1  Tài liệu này là nguồn duy nhất. Không suy diễn từ thông lệ
     ngành hay hệ thống khác.
0.2  Mọi mốc thời gian tính theo <TZ>, đơn vị phút, theo giờ đồng hồ
     liên tục (bao gồm ngày lễ và ngoài giờ làm việc) trừ khi luật
     cụ thể nói khác.
0.3  Mọi khoảng thời gian là [bắt đầu, kết thúc): bao gồm mốc đầu,
     không bao gồm mốc cuối.
0.4  Nếu một hành động không được cấp quyền tường minh cho một actor
     tại §3, actor đó KHÔNG ĐƯỢC thực hiện hành động đó.
0.5  Nếu một tình huống không khớp bất kỳ luật nào, hệ thống PHẢI từ
     chối yêu cầu, giữ nguyên trạng thái hiện tại, không thay đổi tồn
     kho, không thu/hoàn tiền, và ghi log lý do.
0.6  Khi hai luật cùng áp dụng, thứ tự ưu tiên: an toàn tồn kho >
     nghĩa vụ tài chính đã phát sinh > cam kết với khách hàng >
     tối ưu vận hành.
0.7  Mọi thay đổi trạng thái hold PHẢI cập nhật tồn kho theo §4 và ghi
     audit log theo §10 — kể cả khi luật cụ thể không nhắc lại.
0.8  Khi có xung đột đồng thời trên cùng tồn kho, thứ tự xử lý theo
     thời điểm tiếp nhận yêu cầu tại server (first-come-first-served).
0.9  Các nghiệp vụ nêu tại §1 "NGOÀI PHẠM VI" không thuộc tài liệu này.
0.10 Mọi request trùng lặp có cùng khóa idempotency PHẢI trả về kết quả
     của lần xử lý đầu tiên, không tạo hold mới.
```

> ⚠️ **Cảnh báo:** catch-all chỉ hiệu quả nếu **khớp với specs thật**. Nếu specs thật nói "hết hạn thì tự hủy và hoàn cọc" mà catch-all nói "từ chối, giữ nguyên trạng thái", bạn tự tạo mâu thuẫn nội tại (lỗ hổng #5) — tự bắn vào chân mình.
> ⇒ **Viết §0 SAU KHI đã khai thác xong AI Khách hàng**, và dùng chính buổi sáng để verify từng dòng §0.

## 5.6. Kỹ thuật elicitation với AI Khách hàng (tối ưu token)

**Nguyên tắc:** yêu cầu đầu ra có cấu trúc; hỏi để lấy *danh sách đóng* thay vì *lời giải thích*.

| Thay vì hỏi (tốn token, loãng) | Hỏi thế này (đặc, ít token) |
|---|---|
| "Cho tôi biết về tính năng đặt giữ hàng" | "Liệt kê dạng gạch đầu dòng: các trạng thái của một hold; với mỗi trạng thái nêu event nào làm nó chuyển và sang đâu." |
| "Xử lý hết hạn thế nào?" | "Trả lời dạng bảng: sự kiện hết hạn → (a) trạng thái mới, (b) tồn kho thay đổi ra sao, (c) tiền cọc xử lý thế nào, (d) thông báo cho ai." |
| "Có giới hạn gì không?" | "Nêu đúng các con số: TTL (phút), số hold đồng thời/khách, qty tối đa/hold, số lần gia hạn, % cọc." |
| "Còn gì cần lưu ý?" | "Trong các phát biểu sau, cái nào ĐÚNG? (1) guest được đặt giữ hàng; (2) TTL bao gồm ngày lễ; (3) giá được khóa khi hold; (4) cho phép giữ một phần. Trả lời Đúng/Sai từng ý, không giải thích." |

**Bốn kỹ thuật cụ thể:**
1. **Batch câu hỏi** — gộp 5–8 câu liên quan vào 1 lượt, yêu cầu trả lời theo số thứ tự.
2. **Yêu cầu format ngắn** — "trả lời dạng bảng, không giải thích thêm".
3. **Hypothesis testing (Đúng/Sai)** — đưa giả thuyết đã dựng trước ngày thi, bắt xác nhận. Cực rẻ token, cực đặc thông tin. **Đây là lý do phải chuẩn bị Phần III.**
4. **Truy vấn phạm vi tường minh** — "liệt kê những nghiệp vụ NGOÀI phạm vi tính năng này". Dùng cho §1 của spec và để tránh VÔ HIỆU buổi chiều.

## 5.7. Mẫu ghi nhận finding (dùng cho vai CÔNG và cho kháng nghị)

Mỗi test không chỉ là một câu hỏi — hãy ghi kèm hồ sơ. Nếu bị xử bất lợi, đây là toàn bộ đạn kháng nghị của bạn.

| Trường | Nội dung |
|---|---|
| ID | `T-B3` (test 3, spec đội B) |
| Loại lỗ hổng | #4 Biên & off-by-one |
| Tình huống nộp | Nguyên văn |
| Chỗ spec im lặng / mâu thuẫn | Trích dẫn có số mục, hoặc ghi "không có mục nào đề cập" |
| Đáp án chuẩn kỳ vọng | Trích lời AI Khách hàng buổi sáng + timestamp |
| Kết quả máy trả | TRÚNG / TRƯỢT / VÔ HIỆU + lý do đối chiếu của AI So khớp |
| Đánh giá | Đồng ý / **Ứng viên kháng nghị** |

## 5.8. Kháng nghị (≤3 ca) — chuẩn bị lập luận

> **Cập nhật 09/09 — cơ chế hẹp hơn bản gốc:** kháng nghị **chỉ mở cho ca test CÔNG của mình bị chấm VÔ HIỆU**, nộp bằng **text gửi AI**, ban tổ chức review. Hai loại ca dưới đây **không còn kháng nghị được**: (1) spec mình bị tính TRÚNG dù có quy định; (3) So khớp hiểu sai ngữ nghĩa. Bảng gốc giữ lại để thấy điều gì đã mất.
>
> Hệ quả bất đối xứng, quan trọng khi chia thời gian: **rủi ro của vai THỦ không có đường lùi** (viết sai spec là mất điểm, hết), còn rủi ro của vai CÔNG cứu lại được một phần. Vì thế cẩn trọng dồn vào chất lượng spec trước 12:00, còn việc bắn thì tính theo kỳ vọng `EV = 2·P(TRÚNG) − P(VÔ HIỆU)`.

| Ưu tiên | Loại ca | Lập luận | Còn dùng được? |
|---|---|---|---|
| 1 | Test bị tính TRÚNG nhưng spec **có** quy định | Trích chính xác §/BR trong spec | ❌ không còn |
| 2 | Test mình bắn bị tính VÔ HIỆU sai | Trích lời AI Khách hàng chứng minh nghiệp vụ nằm trong phạm vi | ✅ **loại ca duy nhất** |
| 3 | So khớp hiểu sai ngữ nghĩa | Hai câu trả lời tương đương về ý | ❌ không còn |

Vì chỉ còn 5 câu hỏi buổi sáng, bằng chứng loại 2 rất hiếm. `knowledge/50` §7 mở rộng thành **ba mức bằng chứng phạm vi**: (1) lời AI Khách hàng nguyên văn; (2) **câu brief tường minh** — brief là tài liệu BTC phát cho mọi đội nên không bác được bằng lý "suy diễn của đội bạn"; (3) **đồng thuận chéo** — ≥2 trong 3 spec tải về có luật cho nghiệp vụ đó. Cả ba chỉ chứng minh *phạm vi*, không chứng minh đáp án; text kháng nghị phải tự nêu giới hạn đó.

**Chuẩn bị bắt buộc:** một người trong đội **lưu toàn bộ hội thoại với AI Khách hàng kèm timestamp** (hội thoại được xem lại, nhưng ảnh chụp không phụ thuộc hệ thống BTC còn hoạt động lúc 16:00) + **1 bản copy spec đã nộp** + **gói bằng chứng phạm vi của từng test, thu xong lúc nộp test** — test không sửa được sau khi nộp.

## 5.9. Cổng chất lượng trước khi nộp spec

Tất cả phải là "CÓ":

- [ ] Mọi dòng ⚠ trong bảng RTM (§2.4) đã map tới một BR có mã?
- [ ] Mọi ô trong bảng chuyển trạng thái đã được điền (kể cả "từ chối")?
- [ ] Có bảng actor × quyền, bao gồm **guest** và **system/job**?
- [ ] Có mục ưu tiên khi hai luật xung đột?
- [ ] Có nêu timezone và quy ước bao gồm/loại trừ mốc thời gian?
- [ ] Đã Ctrl+F hết danh sách đen §4.6 và sửa?
- [ ] Không có tham chiếu ra tài liệu ngoài, không có đại từ trôi?
- [ ] Mã BR không trùng, mọi tham chiếu chéo đều tồn tại?
- [ ] Đã chạy eval set ~20 tình huống qua **hai** Executor mù độc lập, không tình huống nào hai reader ra hai kết quả khác nhau?
- [ ] **Token ≤ 6.000 (đích 5.400)** trên bản nộp và đã lưu bản copy? Đo bằng `LC_ALL=C.UTF-8 wc -w -m` rồi `max(từ×2,5; ký tự/2,2)`.
- [ ] Bản nộp là markdown, **không có ảnh**; nếu có sơ đồ mermaid thì §0 có dòng "bảng và luật có mã thắng"?

---
---

# PHẦN VI — VẬN HÀNH

## 6.1. Phân vai trong đội

**Đội 3 người (tối ưu):**

| Vai | Nhiệm vụ sáng (THỦ) | Nhiệm vụ chiều (CÔNG) |
|---|---|---|
| **Interrogator** | Chủ trì hỏi AI Khách hàng theo question bank, quản ngân sách token, điền RTM | Xác minh phạm vi cho test (chống VÔ HIỆU) |
| **Spec Writer** | Viết spec vào template dựng trước, quản ngân sách 6.000 token (đích 5.400) | Soi spec đối thủ #1, #2 |
| **Red Teamer** | Đọc spec đang viết bằng con mắt tấn công, chạy eval set, chỉ lỗ hổng để vá | Soi spec #3 + tổng hợp 15 test + giữ log kháng nghị |

**Đội 2 người:** người A = Interrogator + Spec Writer; người B = Red Teamer + quản log. Buổi chiều chia 1.5 spec/người.

**Nguyên tắc:** một người có thể kiêm nhiều vai, nhưng **mỗi artifact phải có đúng một owner**.

## 6.2. Lịch chuẩn bị D-8 → D-0

| Ngày | Việc |
|---|---|
| **04–05/09** | Đăng ký đội. Đọc toàn bộ tài liệu này. Chốt phân vai. |
| **05–06/09** | Dựng **Glossary** (~30 từ) + **ontology & state machine** (§3.2–3.3). Mỗi người tự nghĩ 30 câu hỏi rồi gộp. |
| **06–07/09** | Hoàn thiện **Question Bank** (~100 câu, xếp P0/P1/P2). Viết **bộ 20 câu Đúng/Sai** để hypothesis-test. |
| **07–08/09** | Dựng **Spec Template** rỗng đúng ngân sách từ (§4.7), viết sẵn **§0 catch-all** dạng khung có chỗ điền. Dựng state table & decision table trống. Dựng **eval set 20 tình huống**. |
| **08/09** | Hoàn thiện **Attack Checklist** (§5.1 rút thành 1 trang A4) + **quy trình soi 10 phút** + **mẫu finding** (§5.7). |
| **09/09** | **Dự họp BTC.** Hỏi hết §6.3. Cập nhật mọi con số trong tài liệu này. |
| **10–11/09** | **DIỄN TẬP 1 lượt hoàn chỉnh.** Dùng ChatGPT/Claude giả lập AI Khách hàng với một tính năng khác (VD: đặt bàn nhà hàng, giữ chỗ lớp học). Chạy đủ theo hạn mức 09/09: 4 câu hỏi (mỗi lần 1 câu, phiên độc lập) → viết spec 45' → câu 5 restate → đổi spec & tự bắn → đo kết quả. **Bước quan trọng nhất; đừng bỏ.** |
| **11/09** | Rút bài học từ diễn tập, sửa template. Thiết bị: laptop, sạc, 2 màn hình nếu được, tool đếm từ, file log có timestamp. |
| **12/09** | Thi. |

## 6.3. Danh sách câu hỏi cần hỏi BTC tại buổi 09/09

1. Hạn mức **token** với AI Khách hàng là bao nhiêu? Tính cả input và output? Có hiển thị số token còn lại real-time?
2. Có giới hạn **số lượt hỏi** riêng, ngoài token?
3. Spec nộp ở **định dạng gì** (Markdown, Google Doc, plain text)? **Bảng và sơ đồ có được không?** Cách đếm hạn mức?
4. Hạn mức **hình ảnh đính kèm**? Executor có "đọc" được hình không?
5. **Công thức điểm**: trọng số vai CÔNG vs vai THỦ? TRÚNG được mấy điểm? VÔ HIỆU có bị trừ không?
6. Xếp giải theo **pool 4 đội** hay **toàn giải**?
7. **Model AI** nào đóng vai Khách hàng / Executor / So khớp? Executor được prompt như thế nào?
8. Đội có được **dùng AI riêng** (ChatGPT/Claude/Copilot) để hỗ trợ viết spec và soi spec không?
9. Được **mang tài liệu chuẩn bị trước** (template, checklist, glossary, question bank) vào phòng thi không? Dạng file hay giấy?
10. Test nộp ở định dạng nào? Giới hạn độ dài mỗi tình huống? Có được nộp test nhiều bước?
11. Spec đối thủ được cấp dưới dạng nào và lúc nào chính xác? Có được tải về?
12. Cơ chế kháng nghị: nộp form hay trình bày miệng? Có được xem **lý do đối chiếu** của AI So khớp trước khi kháng nghị?
13. Có được xem lại hội thoại của mình với AI Khách hàng sau khi khóa spec không?
14. Có được sửa test sau khi nộp, trước 15:00?

**Kết quả sau họp 09/09** (bảng đối chiếu đầy đủ: `knowledge/00` §H):

| Câu | Trả lời |
|---|---|
| 1, 2 | **5 câu hỏi · mỗi lượt 1 câu · 5.000 token gồm hỏi + trả lời · AI KHÔNG có memory** |
| 3 | **Markdown**; bảng được; sơ đồ dạng mermaid; hạn mức tính bằng **token (6.000)**, nên câu "từ trong bảng có tính không" không còn nghĩa — dấu `\|` là token thật |
| 4 | Gửi ảnh cho AI Khách hàng **≤ 3 lần**; **bản nộp không có ảnh** |
| 5 | **CÔNG trúng +2 · THỦ đỡ được +1 · CÔNG bị VÔ HIỆU −1** |
| 7 | ⛔ **Thông tin bảo mật, BTC không trả lời** — model của 3 tác nhân và prompt Executor |
| 11 | Markdown, **tải về được** ⇒ mở ra kỹ thuật đồng thuận chéo 3 spec (`knowledge/50` §4b) |
| 12 | **Text gửi AI, BTC review; chỉ mở cho ca test CÔNG bị chấm VÔ HIỆU** ⇒ ca "spec mình bị TRÚNG oan" và "So khớp hiểu sai ngữ nghĩa" không còn kháng nghị được |
| 13 | **Được** |
| 14 | **Không** ⇒ bằng chứng phạm vi cho từng test phải thu xong lúc nộp |
| 6, 8, 9, 10 (độ dài) | Chưa có — xem `knowledge/00` §A2 |

## 6.4. Checklist ngày thi

**9:00–9:30 — Nhận brief**
- [ ] Đọc brief 2 lượt. Người 1 highlight *danh từ nghiệp vụ*, người 2 highlight *chỗ mơ hồ*.
- [ ] Đối chiếu brief với ontology đã dựng → xác định biến thể (cart hold / cọc / pickup).
- [ ] **Dựng mô hình bài toán** (`knowledge/05`) rồi chia mọi ô chưa biết thành 3 nhóm: vào 4 câu hỏi dữ liệu · hàng đợi câu restate · **tự điền mặc định ngành**.
- [ ] Hỏi BTC miệng: ảnh có tính vào 5.000 token không; AI Khách hàng còn mở sau 12:00 không.

**9:30–10:20 — Khai thác: 4 câu hỏi dữ liệu** *(cập nhật 09/09 — bản gốc viết 6 lượt batch, chế độ đó không còn tồn tại; chi tiết `knowledge/20` §3)*
- [ ] C1: **NGOÀI phạm vi** + điều PHẢI ngăn (rất quan trọng cho buổi chiều).
- [ ] C2: bảng chuyển trạng thái đầy đủ — câu lãi nhất, trả về cả §5 và nửa §6 của spec.
- [ ] C3: bảng tham số, gồm **đơn vị neo hạn mức** và **số ngày tiền về tay khách**.
- [ ] C4: tám kịch bản suy biến & thất bại của phụ thuộc ngoài.
- [ ] Mỗi câu: đúng một dấu `?`, tự chứa (AI không có memory), có cap dòng/từ, qua **cổng 5 kiểm tra** trước khi gửi.
- [ ] **⭐ Đánh dấu ⚠ mọi câu trả lời PHẢN TRỰC GIÁC — đây là băng đạn buổi chiều.**
- [ ] Điền RTM: dòng `A-xx` cho câu trả lời, **dòng `G-xx` cho mọi ô phải tự điền**. Lưu log có timestamp.

**10:20–11:05 — Viết bản nháp spec**
- [ ] Điền mọi ô không hỏi được bằng **mặc định ngành** (`knowledge/20` §4), không tự nghĩ ra giá trị mới.
- [ ] Xếp hạng rủi ro mọi dòng `G-xx` → **10 phát biểu cho câu 5**.

**11:05–11:30 — Câu hỏi cuối cùng (restate) rồi vá**
- [ ] C5: 10 phát biểu Đúng/Sai có con số; mỗi ý "Sai" = một việc sửa BR cụ thể.

**11:30–11:55 — Khóa spec**
- [ ] Viết §0 catch-all (điều chỉnh khung dựng trước cho khớp specs thật).
- [ ] Điền template theo thứ tự: §6 BR → §5 state table → §3 actor → còn lại.
- [ ] Red Teamer chạy eval set + Attack Checklist, đọc **như Executor** (chỉ đọc spec, quên hết bối cảnh).
- [ ] Ctrl+F danh sách đen → sửa hết.
- [ ] Chạy cổng chất lượng §5.9.

**11:55–12:00 — Nộp**
- [ ] Đếm từ ≤ 3.000. Nộp sớm 5 phút. **Lưu 1 bản copy.**

**13:00–14:30 — Soi & bắn**
- [ ] Chia spec: mỗi người 1 spec, quy trình 10 phút/spec (§5.4).
- [ ] Đối chiếu danh sách ⚠ với từng spec → ứng viên hạng A.
- [ ] Viết 5 test/spec theo công thức §5.3. Kiểm phạm vi trước khi chốt.
- [ ] Test phải hỏi được **kết quả cụ thể** (con số / trạng thái / ai thắng), không hỏi mở.
- [ ] Điền mẫu finding §5.7 cho từng test.

**14:30–15:00** — Rà lại 15 test, thay test yếu nhất bằng test biên. Nộp.

**15:00–16:00** — Theo dõi bảng điểm, ghi lại mọi kết quả bất lợi + lý do đối chiếu của AI So khớp.

**16:00–17:00** — Chọn 3 ca kháng nghị theo ưu tiên §5.8; mỗi ca chuẩn bị: trích dẫn có số mục + trích log hội thoại + 2 câu lập luận.

---
---

# PHẦN VII — THƯ VIỆN TRI THỨC (để đi tìm PDF → LLM Wiki / PageIndex)

> **Cách dùng phần này:** mỗi dòng là một tài liệu cần thu. Cột **Ưu tiên** cho biết nên index trước cái nào; cột **Chỉ cần phần** giúp bạn chunk có chọn lọc thay vì nhồi cả sách; cột **Khả năng có PDF** ước lượng độ dễ tìm bản điện tử; cột **Feed vào** map tới agent/skill ở §8.
>
> Nguyên tắc index: **ưu tiên tài liệu có cấu trúc quy tắc (rule-based)** — chuẩn, syllabus, guide có danh sách rule — vì chúng chunk sạch và trả lời tốt; sách kể chuyện (narrative) thì chỉ lấy chương cần.

## 7.1. TẦNG P0 — Bắt buộc có trong wiki (lõi requirements & test design)

| # | Tài liệu | Chỉ cần phần | Dùng cho | PDF |
|---|---|---|---|---|
| 1 | **ISO/IEC/IEEE 29148:2018** — *Systems and software engineering — Life cycle processes — Requirements engineering* | §5.2 (characteristics of good requirements), §8–9 (SRS/SyRS template) | Bộ tiêu chí tự soi spec; cấu trúc SRS | Bản trả phí (ISO/IEEE). Có nhiều bản tóm lược & slide công khai |
| 2 | **INCOSE — Guide for Writing Requirements** | Toàn bộ phần "Rules" (~40+ rule viết requirement) + phụ lục từ ngữ cần tránh | **Nguồn tốt nhất** cho §4.6 và §4.8 | Bản chính cho member; nhiều bản trích/slide công khai |
| 3 | **A. Mavin et al. — "Easy Approach to Requirements Syntax (EARS)"** (RE'09 paper) | Toàn bộ (~10 trang) | Khuôn cú pháp `WHEN/IF/WHILE/WHERE … the system shall …` — cực phù hợp cho Executor parse | **Free PDF** |
| 4 | **NASA — "How to Write a Good Requirement"** + *NASA Systems Engineering Handbook* (SP-2016-6105) App. C–D | Danh sách từ mơ hồ, checklist requirement | Danh sách đen §4.6 mở rộng | **Free PDF** (nasa.gov) |
| 5 | **Karl Wiegers & Joy Beatty — Software Requirements, 3rd ed.** (Microsoft Press, 2013) | Ch.11 (writing requirements), Ch.12 (models), **Ch.15 (ambiguity)**, Ch.17 (review) | Nghề viết & review requirement | Sách thương mại |
| 6 | **ISTQB Foundation Level Syllabus (v4.0)** + *Advanced Test Analyst* | Ch.4 test design techniques: equivalence partitioning, **boundary value analysis**, decision table, state transition testing, use case testing | **Bộ kỹ thuật lõi cho vai CÔNG** (§5.2, §5.3) | **Free PDF** (istqb.org) |
| 7 | **Lee Copeland — A Practitioner's Guide to Software Test Design** | Ch. equivalence class, boundary value, decision table, state-transition, pairwise | Cùng mục đích #6, sâu hơn về ví dụ | Sách thương mại |
| 8 | **IREB CPRE Foundation Level — Syllabus + Handbook** | Toàn bộ syllabus (~50 trang) | Khung requirements engineering gọn, tiếng Anh chuẩn | **Free PDF** (ireb.org) |
| 9 | **Ronald G. Ross — Business Rule Concepts, 4th ed.** | Phần phân loại rule + cách phát biểu rule độc lập | Viết BR-xx có mã, tách luật khỏi luồng (§4.3) | Bản PDF phổ biến trên businessrulesgroup |
| 10 | **OMG — Decision Model and Notation (DMN) Specification** | Ch. về decision table: hit policy, completeness, gap/overlap detection | **Cách chứng minh decision table không có ô trống** — vũ khí phòng thủ | **Free PDF** (omg.org) |

## 7.2. TẦNG P1 — Nên có (elicitation, use case, mơ hồ, ví dụ)

| # | Tài liệu | Chỉ cần phần | Dùng cho | PDF |
|---|---|---|---|---|
| 11 | **BABOK v3 (IIBA)** | Ch.4 Elicitation & Collaboration, Ch.7 Requirements Analysis, Ch.10 Techniques (interview, questionnaire, document analysis, decision analysis) | Bộ kỹ thuật elicitation cho agent `interrogator` | Sách hội viên |
| 12 | **Alistair Cockburn — Writing Effective Use Cases** | Ch. về extensions/exception handling, precondition/guarantee | Không bỏ sót luồng phụ (lỗ hổng #2) | Sách thương mại |
| 13 | **Gause & Weinberg — Exploring Requirements: Quality Before Design** | Ch. về ambiguity poll, "Mary had a little lamb" heuristic | Kỹ thuật phát hiện đa nghĩa (lỗ hổng #14) | Sách thương mại |
| 14 | **Gojko Adzic — Specification by Example** | Ch. key examples, "specification workshops" | Dùng ví dụ làm đặc tả; nguồn cho eval set §4.5 | Sách thương mại |
| 15 | **Gojko Adzic — Fifty Quick Ideas to Improve Your Tests** | Toàn bộ (mỗi ý 2 trang) | Ý tưởng test nhanh cho vai CÔNG | Sách thương mại, rẻ |
| 16 | **Elisabeth Hendrickson — Test Heuristics Cheat Sheet** | 1 trang | Heuristic tìm edge case cực nhanh — in ra mang đi thi | **Free PDF** |
| 17 | **James Bach — Heuristic Test Strategy Model (HTSM)** | Product Elements + Quality Criteria | Khung soi bỏ sót có hệ thống | **Free PDF** (satisfice.com) |
| 18 | **Volere Requirements Specification Template** (Robertson & Robertson) | "Requirement Shell" + Ch. về fit criterion | Mẫu phát biểu requirement chặt, có tiêu chí đo | **Free** (volere.org) |
| 19 | **Suzanne & James Robertson — Mastering the Requirements Process, 4th ed.** | Ch. về fit criteria, ambiguity, trawling for requirements | Nền tảng, đi kèm Volere | Sách thương mại |
| 20 | **Eric Evans — Domain-Driven Design** | **Ch.2 Ubiquitous Language** (chỉ ~20 trang) | Glossary chống lệch nghĩa (§3.2) | Sách thương mại |
| 21 | **Michael Nygard — Release It!, 2nd ed.** | Ch. Stability Antipatterns & Patterns (timeout, circuit breaker, idempotency) | Nguồn ý tưởng test lỗi hệ thống, timeout, race (lỗ hổng #7, #12) | Sách thương mại |
| 22 | **Jeff Patton — User Story Mapping** hoặc **Mike Cohn — User Stories Applied** | Ch. về acceptance criteria & splitting | Journey/actor mapping (§3.4) | Sách thương mại |

## 7.3. TẦNG P0-DOMAIN — Kiến thức e-commerce / inventory (đắt giá nhất cho đề bài này)

Đây là phần **nhiều đội sẽ bỏ qua** và cũng là phần cho ROI cao nhất, vì specs thật chắc chắn nói bằng vốn từ của lĩnh vực này.

| # | Nguồn | Chỉ cần phần | Dùng cho | PDF |
|---|---|---|---|---|
| 23 | **Adobe Commerce (Magento) — Inventory Management (MSI) documentation** | Trang *Reservations*, *Salable Quantity*, *Stock & Sources*, *Backorders* | **Nguồn tốt nhất** về mô hình reservation thật: reservation ledger, salable qty = qty − reservations | Web docs → in PDF |
| 24 | **Shopify — Inventory states / Reserve inventory docs & API** | Khái niệm *available / committed / reserved / on-hand / incoming*, checkout reservation TTL | Vốn từ trạng thái tồn kho | Web docs → in PDF |
| 25 | **Oracle / SAP — Available-to-Promise (ATP) documentation** | Định nghĩa ATP, allocation rules, backorder processing, ATP check | Công thức ATP, soft vs hard allocation | Free PDF (docs.oracle.com, help.sap.com) |
| 26 | **ASCM/APICS Dictionary** (16th ed.) | Các mục: ATP, safety stock, allocation, backorder, cycle count, oversell | Từ điển chuẩn ngành để glossary dùng đúng từ | Bản PDF phổ biến |
| 27 | **David Piasecki — Inventory Management Explained** | Ch. về allocation & inventory accuracy | Nền tảng nghiệp vụ kho | Sách thương mại |
| 28 | **Bài viết kỹ thuật về flash-sale / oversell prevention & distributed locking** (blog engineering: Shopify, Alibaba, Tokopedia, Grab…) | Cơ chế reserve stock, optimistic/pessimistic lock, idempotency key | Nguồn tình huống race condition (lỗ hổng #7) — **rất hay TRÚNG** | Web → PDF |
| 29 | **Tài liệu OMS (Order Management System) khái quát** — IBM Sterling / Manhattan / Kibo docs | Chương về reservation & order lifecycle | Bức tranh tổng thể vòng đời đơn | Web docs → PDF |

> **Lưu ý dùng:** đọc để **lấy vốn từ vựng và danh sách edge case**, KHÔNG phải để chép luật. Specs thật của BTC có thể cố tình khác các nền tảng này — và chính chỗ khác đó là ⚠ phản trực giác đáng giá nhất.

## 7.4. TẦNG P1 — Viết cho LLM đọc & cơ chế sinh ra "TRÚNG"

| # | Nguồn | Chỉ cần phần | Dùng cho |
|---|---|---|---|
| 30 | **Anthropic — Prompt engineering docs** (be clear & direct, use XML tags, structured output, chain of thought) | Toàn bộ, ngắn | Hiểu Executor sẽ diễn giải spec thế nào; cách viết heading/đánh số để LLM parse đúng |
| 31 | **Tài liệu về *underspecification* / *specification gaming* trong LLM** (DeepMind blog "Specification gaming", các paper về ambiguity in instructions) | Khái niệm cốt lõi | **Giải thích cơ chế sinh ra trạng thái TRÚNG:** model lấp khoảng trống bằng prior phổ biến nhất |
| 32 | **Tài liệu về LLM-as-a-judge / semantic equivalence evaluation** | Cách judge so khớp ý nghĩa, các thất bại thường gặp | Hiểu AI So khớp → biết cách viết spec để nó khớp, và biết khi nào kháng nghị loại 3 (§5.8) |
| 33 | **Structured output / JSON schema & function calling docs** | Khái niệm contract | Nền cho §4.5 "spec là contract" |

## 7.5. TẦNG P2 — Tham khảo mở rộng (index sau, nếu còn thời gian)

| # | Nguồn | Dùng cho |
|---|---|---|
| 34 | **IEEE 830-1998** (đã bị 29148 thay thế, nhưng chỉ ~30 trang) | Cấu trúc SRS kinh điển, đọc nhanh |
| 35 | **Karl Wiegers — More About Software Requirements** | Chương về ambiguity & assumptions ngầm |
| 36 | **Adam Shostack — Threat Modeling: Designing for Security** | *Chỉ để lấy tư duy*: khái niệm trust boundary → chuyển hoá thành knowledge boundary (§2.1 ⑤), và tư duy "liệt kê mối đe dọa có hệ thống" → áp cho lỗ hổng spec |
| 37 | **OWASP Application Security Verification Standard (ASVS)** | *Chỉ để lấy hình thức*: cách viết một checklist verifiable requirement có mã, chia cấp độ. Mẫu rất tốt để bắt chước cách đánh mã BR |
| 38 | **Sommerville — Software Engineering, 10th ed.**, Ch.4 Requirements Engineering | Tổng quan RE ngắn gọn cho người mới |
| 39 | **David Harel — Statecharts** / UML State Machine spec | Hình thức hoá state machine nếu muốn chặt chẽ hơn |
| 40 | **von Halle & Goldberg — The Decision Model** | Chuẩn hoá decision table ở mức sâu |

## 7.6. Ghi chú thu thập & index

**Về bản quyền:** ưu tiên các nguồn free/chính thức (đánh dấu *Free PDF*: EARS paper, NASA, ISTQB, IREB, DMN, Volere, HTSM, Hendrickson cheat sheet, Oracle/SAP docs, Anthropic docs). Với sách thương mại, mua bản điện tử hợp pháp hoặc dùng bản trích/summary công khai.

**Gợi ý cấu trúc index cho PageIndex / LLM Wiki:**

```
knowledge/
├── 00-competition/         # §1–§2 của file này: luật chơi, chiến lược (ground truth)
├── 10-requirements-craft/  # #1–#5, #8, #18, #19, #34, #35, #38  → agent spec-writer
├── 11-rule-modeling/       # #9, #10, #40, #39                    → skill decision-table, state-table
├── 12-syntax-ambiguity/    # #2, #3, #4, #13, #37                 → skill ambiguity-linter
├── 20-elicitation/         # #11, #22, #14                        → agent interrogator
├── 30-test-design/         # #6, #7, #15, #16, #17, #21           → agent red-teamer
├── 40-domain-ecommerce/    # #23–#29                              → RAG domain (quan trọng nhất)
├── 50-llm-behavior/        # #30–#33                              → agent executor-simulator
└── 90-appendix/            # #36 (threat modeling mindset), misc
```

**Chunking hint:** với chuẩn và syllabus, chunk theo **từng rule/từng mục có số** (giữ nguyên mã mục làm metadata để agent trích dẫn được — rất cần cho kháng nghị). Với sách, chunk theo mục con và **gắn tag chương** để agent nói được "theo Wiegers ch.15".

**Metadata nên gắn cho mỗi chunk:** `source`, `section_id`, `tier (P0/P1/P2)`, `use_for (spec-writing | attack | domain | llm-behavior)`, `vuln_type (#1..#15 nếu liên quan)`. Trường `vuln_type` cho phép truy vấn kiểu *"cho tôi mọi tri thức liên quan lỗ hổng #7 race condition"* — cực hữu ích cho agent `red-teamer`.

---
---

# PHẦN VIII — BLUEPRINT SKILLS & AGENTS

Đề xuất kiến trúc bộ agent, map ngược về thư viện §7. Mỗi agent nên có: (a) system prompt chứa §1 + phần liên quan của file này, (b) RAG vào nhánh knowledge tương ứng, (c) bộ eval để kiểm chất lượng.

| Agent / Skill | Nhiệm vụ | Input | Output | RAG vào | Eval |
|---|---|---|---|---|---|
| **`interrogator`** | Sinh & xếp ưu tiên câu hỏi cho AI Khách hàng, tối ưu token; gộp batch, ép format bảng | brief + question bank + token còn lại | Danh sách lượt hỏi đã batch, kèm format yêu cầu | `20-elicitation`, `40-domain-ecommerce` | Đo mật độ thông tin/token trên bài diễn tập |
| **`spec-writer`** | Viết/điền spec theo template §4.7, giữ ngân sách từ | RTM đã điền + specs thu được | Spec markdown có mã BR, đếm token | `10-requirements-craft`, `11-rule-modeling` | ≤6.000 token, mọi dòng RTM có BR |
| **`ambiguity-linter`** (skill) | Quét danh sách đen §4.6, đại từ trôi, thiếu đơn vị/timezone, thiếu bao gồm/loại trừ | spec text | Danh sách hit + đề xuất viết lại | `12-syntax-ambiguity` | Chạy trên spec cố tình lỗi, đo recall |
| **`completeness-checker`** (skill) | Kiểm bảng state × event và decision table có ô trống/overlap; kiểm mã BR trùng & tham chiếu chết | spec text | Báo cáo ô thiếu | `11-rule-modeling` (DMN hit policy) | Spec mẫu thiếu ô → phải phát hiện 100% |
| **`executor-simulator`** | **Đóng vai AI Executor**: chỉ đọc spec, trả lời tình huống, không dùng kiến thức ngoài | spec + tình huống | Câu trả lời + trích mục spec đã dùng (hoặc "spec không đề cập") | `50-llm-behavior` (chỉ để hiệu chỉnh hành vi) | Không được dùng tri thức ngoài spec |
| **`red-teamer`** | Sinh 5 test/spec theo công thức §5.3, đa dạng loại lỗ hổng | spec đối thủ + danh sách ⚠ | 5 tình huống + loại lỗ hổng + kỳ vọng | `30-test-design`, `40-domain-ecommerce` | Đo tỷ lệ TRÚNG trên spec mẫu |
| **`scope-referee`** | Chấm điểm rủi ro VÔ HIỆU của một test trước khi nộp | tình huống + danh sách TRONG/NGOÀI phạm vi | Điểm rủi ro + lý do | `00-competition`, `40-domain-ecommerce` | Đo false-negative trên test cố tình ngoài phạm vi |
| **`word-compressor`** (skill) | Nén spec về ≤5.400 token mà không mất luật: chuyển văn → bảng, gộp luật, thay tham chiếu | spec dài | spec ngắn + diff luật | `10-requirements-craft` | Không mất BR nào sau nén |
| **`appeal-builder`** | Dựng lập luận kháng nghị: trích số mục spec + log hội thoại + 2 câu lý lẽ | finding (§5.7) + spec + log | Hồ sơ kháng nghị | `00-competition` | Có trích dẫn cụ thể, không suy diễn |

**Vòng lặp tự huấn luyện (đề xuất chạy 10–11/09):**

```
brief giả lập
   → interrogator  ──hỏi──▶  LLM đóng vai AI Khách hàng (có specs thật do bạn tự viết)
   → spec-writer   ──▶ spec
   → ambiguity-linter + completeness-checker  ──▶ vá spec
   → red-teamer    ──▶ 5 test
   → executor-simulator đọc spec trả lời
   → so khớp với specs thật  ──▶ đếm TRÚNG/TRƯỢT/VÔ HIỆU
   → rút bài học, sửa prompt & template, lặp lại
```

Vòng lặp này chính là bài diễn tập ở §6.2 nhưng tự động hoá — và là cách duy nhất để biết template + question bank của bạn có thực sự tốt trước ngày thi.

---
---

# PHẦN IX — MƯỜI ĐIỀU QUAN TRỌNG NHẤT

1. **Chuẩn bị domain trước là đòn bẩy lớn nhất.** Bạn đã biết tính năng ("đặt giữ hàng") — dựng sẵn ontology và question bank thì buổi sáng chỉ cần *xác nhận*, không phải *khám phá*.
2. **Executor mù bối cảnh.** Mọi common sense phải được viết ra thành luật. Tri thức không nằm trong spec = bề mặt tấn công.
3. **Không hỏi = không biết = bị bắn.** Elicitation quyết định 60–70% điểm phòng thủ.
4. **Hỏi bằng câu Đúng/Sai và yêu cầu format bảng** để tiết kiệm token và tăng mật độ thông tin.
5. **§0 Catch-all rules là phần đắt giá nhất của ngân sách token** — nhưng chỉ viết SAU khi đã khai thác xong, nếu không sẽ tự tạo mâu thuẫn nội tại.
6. **Dùng bảng, không dùng văn.** Decision table + state transition table vừa tiết kiệm từ vừa lộ ô trống để vá.
7. **Bảng RTM ngược (đáp án khách hàng → luật có mã) là công cụ tự kiểm mạnh nhất.** Ô trống trong bảng = lỗ hổng chắc chắn bị bắn.
8. **Đánh dấu ⚠ mọi câu trả lời phản trực giác của AI Khách hàng** — vừa là checklist bắt buộc cho spec của mình, vừa là băng đạn chính xác nhất cho buổi chiều.
9. **Đa dạng hóa 5 test/spec** theo 5 loại lỗ hổng khác nhau; kiểm phạm vi trước khi bắn để tránh VÔ HIỆU.
10. **Lưu log hội thoại + bản spec đã nộp.** Không có bằng chứng thì không kháng nghị được. Và **diễn tập trọn 1 lượt trước 12/09** — không có bài tập nào thay được việc thực sự viết một spec 6.000 token trong 45 phút với 4 câu trả lời trong tay.

---
---

# PHỤ LỤC A — ARTIFACT CẦN CÓ TRƯỚC NGÀY THI

| # | Artifact | Owner | Nguồn trong file này | Trạng thái |
|---|---|---|---|---|
| 1 | Glossary e-commerce ~30 thuật ngữ | | §3.2 | ☐ |
| 2 | Danh sách actor (10+ actor) | | §3.4 ① | ☐ |
| 3 | Ontology + state machine đã điền giả thuyết | | §3.2, §3.3 | ☐ |
| 4 | Question Bank ~100 câu, xếp P0/P1/P2 | | §3.5 | ☐ |
| 5 | Bộ 20 câu Đúng/Sai (hypothesis test) | | §5.6 | ☐ |
| 6 | Spec Template rỗng đúng ngân sách từ | | §4.7 | ☐ |
| 7 | Khung §0 catch-all có chỗ điền | | §5.5 | ☐ |
| 8 | Bảng state × event trống, decision table trống | | §4.3 | ☐ |
| 9 | Eval set 20 tình huống tự kiểm spec | | §4.5 | ☐ |
| 10 | Attack Checklist 1 trang A4 (15 loại lỗ hổng) | | §5.1 | ☐ |
| 11 | Quy trình soi 10 phút in ra giấy | | §5.4 | ☐ |
| 12 | Mẫu finding + file log có timestamp | | §5.7 | ☐ |
| 13 | Danh sách đen từ mơ hồ in ra giấy | | §4.6 | ☐ |
| 14 | Bảng RTM trống | | §2.4 | ☐ |
| 15 | Danh sách câu hỏi BTC (buổi 09/09) | | §6.3 | ☐ |

---

# PHỤ LỤC B — NHẬT KÝ HỢP NHẤT (đã giữ gì, loại gì, vì sao)

Ghi lại để về sau không phải mở lại 3 file gốc (đã xoá).

## Nguồn 1 (`phan_tich_1.md`) — xương sống, giữ ~95%
Bản duy nhất có dữ liệu thật từ website + Google Doc BTC. Giữ nguyên: thông tin cốt lõi, timeline, agenda, cơ chế 3 tác nhân AI, 3 trạng thái kết quả, ví dụ của BTC, 4 yếu tố quyết định, hạn mức, 4 nhận định chiến lược, bảng bất đối xứng, quy tắc vàng vai CÔNG, ontology & state machine "đặt giữ hàng", 14 nhóm question bank, 4 định dạng đặc tả, danh sách đen từ mơ hồ, ngân sách 3.000 từ, taxonomy 15 lỗ hổng, 4 kỹ thuật test, phân bổ 5 test, quy trình soi 10 phút, catch-all §0, kỹ thuật elicitation tối ưu token, kháng nghị, phân vai, lịch D-8, câu hỏi BTC, checklist ngày thi.

## Nguồn 2 (`phan_tich_2.md`) — giữ 5 ý, loại phần generic
**Đã hợp nhất vào:**
- Mẫu requirement kiểm thử được (nêu rõ điều kiện, hành vi, kết quả, chủ thể, cách quan sát) → **§4.4**
- "Spec AI tối thiểu" (task definition, input/output contract, refusal condition, eval set) → chuyển hoá thành **§4.5 "Spec như một contract cho máy đọc"** — đây là đóng góp giá trị nhất của nguồn này
- Mẫu vulnerability/spec finding có bằng chứng & tái hiện → **§5.7**
- Cổng chất lượng cuối cùng (mọi câu trả lời phải là "có") → **§5.9**
- Checklist "source of truth" xác nhận thể lệ → gộp vào **§6.3**
- Nguyên tắc "mỗi artifact một owner" → **§6.1**

**Đã loại:** phần business brief/persona/pitch deck/demo video/threat model bảo mật hệ thống/secure-by-design/artifact repo `.env.example` — cuộc thi này **không** nộp sản phẩm chạy được, không có pitch, không có demo. Lưu ý bản này tự khai không truy cập được website nên mọi mô tả cuộc thi trong đó là suy đoán.

## Nguồn 3 (`phan_tich_3.md`) — giữ 4 ý, loại phần sai bản chất
**Đã hợp nhất vào:**
- Taxonomy tài liệu BA (BRD / FRD / NFRD / SRS / **RTM** / sơ đồ) → **§4.2**, kèm phán quyết cái nào dùng được ở Spec Battle
- **Ý tưởng RTM** → phát triển thành **§2.4 "RTM ngược"** (đáp án khách hàng → luật có mã), một trong những công cụ phòng thủ mạnh nhất
- Quy trình viết spec (thu thập → phân tích → outline → viết → rà soát) → **§4.9**, nén vào khung 2h30
- Phương pháp customer analysis (persona, user story, journey map, assumption log) → **§3.4**, đóng khung lại thành *giàn giáo elicitation* thay vì deliverable
- **Trust boundary** trong threat modeling → chuyển hoá thành **knowledge boundary** (§2.1 ⑤, §5.4) — đóng góp khái niệm hay nhất của nguồn này
- Tư duy "liệt kê mối đe dọa có hệ thống" (STRIDE) → đã có bản tương đương tốt hơn là taxonomy 15 lỗ hổng §5.1

**Đã loại (quan trọng):** toàn bộ phần Red Team / Blue Team pentest và bộ công cụ Metasploit, Nmap, Wireshark, Burp Suite, SQLmap, Nikto, Nessus, Cobalt Strike, ELK, Splunk, Suricata; các kịch bản SQLi/XSS/CSRF/RCE/phishing; checklist OWASP secure coding; kế hoạch đào tạo 8 tuần dạng Gantt.
**Lý do:** nguồn 3 hiểu sai "Spec Battle" là thi xâm nhập hệ thống. Thực tế đây là **đấu đặc tả**: tấn công bằng *tình huống nghiệp vụ* để làm lộ khoảng trống trong văn bản, không phải bằng payload. Không có hệ thống nào để quét, không có code để pentest, thí sinh **không cần biết code**. Đưa các công cụ này vào miền tri thức sẽ **làm nhiễu RAG** và khiến agent sinh ra test sai loại (→ VÔ HIỆU).

**Đã sửa lỗi dữ kiện của nguồn 3:**
- *Mastering the Requirements Process* là của **Robertson & Robertson** (không phải Wiegers)
- *Threat Modeling: Designing for Security* là của **Adam Shostack** (nguồn 3 ghi "Abolish et al.")
- Sommerville viết *Software Engineering* (không phải "Software Requirements")
- Đội hình thực tế là **2–3 người**, không phải 3–5; sự kiện diễn ra **1 ngày**, không phải 1–3 ngày/1 tuần
- Không có vai "HackerSME"/"HackerCoop" trong cuộc thi này

---

*Mọi con số về thể lệ trong tài liệu này lấy từ Google Doc thông báo và trang `spec-battle.html` ngày 04/09/2026. BTC lưu ý số liệu website còn đang tinh chỉnh — **cập nhật lại toàn bộ §1.1, §1.5 và §6.3 sau buổi họp 09/09**.*
