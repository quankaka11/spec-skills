*Đọc 9:00–9:30 ngày thi, NGAY khi nhận brief, TRƯỚC lượt hỏi đầu tiên. Owner: Interrogator + Spec Writer cùng làm, 20 phút.*

# 05 — Hiểu bài toán trước khi hỏi (problem framing)

## 0. Lỗi mà file này chặn

Bộ kit các phiên trước đi thẳng từ brief sang **hỏi tham số** (TTL bao nhiêu phút, cọc bao nhiêu %). Kết quả đo được ở diễn tập 08/09: spec `battle/spec.nop.md` đạt mọi cổng hình thức (36/36 ô bảng trạng thái, 0 hit danh sách đen, 2.991/3.000 từ) nhưng:

- **BR-03 + BR-18** đặt hạn mức chống gom hàng lên `email guest tự khai` → bot đổi email là lách được. Luật *tồn tại* nhưng **không cưỡng chế được**, và brief nói mục tiêu là "chống bot gom hàng".
- **BR-01 khóa tồn ngay khi nhận yêu cầu, trước khi có tiền** + **BR-10 hủy hoàn 100% không phí** + **guest được tạo hold** = khóa tồn miễn phí, không rủi ro. Đây đúng là kịch bản *denial of inventory* [Arkose/DataDome/HUMAN]. Spec **tự phá mục tiêu của chính nó**.
- **0.15 "hoàn tất ≤ 24 giờ về phương thức gốc"** — hoàn tiền thẻ mất nhiều ngày, và **hoàn về thẻ đã đóng/hết hạn có thể thất bại** [Stripe refunds; Helcim; Universe]. Luật **bất khả thi**, và spec không có luật nào cho ca hoàn tiền thất bại.

Ba lỗi này **không** bị bất kỳ cổng nào của kit bắt, vì mọi cổng đều kiểm *hình thức* (mơ hồ, ô trống, truy vết, số từ). Chúng chỉ bị bắt khi có **mô hình bài toán** để đối chiếu. ISO/IEC/IEEE 29148 xếp `feasible` (khả thi) và `affordable` (chi phí chấp nhận được) ngang hàng với `unambiguous` và `verifiable` — kit trước chỉ kiểm nửa sau.

**Luật cứng:** không gõ câu hỏi nào cho AI Khách hàng trước khi 6 khối §1 đã có chữ. Mô hình sai còn cứu được; mô hình **không tồn tại** thì mọi lượt hỏi đều là hỏi tham số cho một bài toán chưa hiểu.

## 1. Sáu khối mô hình bài toán — điền trong 20 phút từ brief

Điền được từ brief thì ghi luôn; không có thì ghi `?` **và nó thành câu hỏi P0 cho lượt 1**. Không suy diễn rồi ghi như dữ kiện.

### M1. Mục tiêu & thước đo [BABOK Business Objectives Model; PMI Goal Model]

| Ô | Nội dung | Lấy ở đâu |
|---|---|---|
| Mục tiêu 1..n | Động từ + đối tượng + hướng (tăng/giảm/chặn) | Câu "Bối cảnh"/"để…" của brief |
| Thước đo mỗi mục tiêu | Số nào tăng/giảm thì gọi là thành công | Suy ra, đánh `[SUY RA]` |
| Ai chịu thiệt nếu hỏng | Khách / shop / kho / CSKH | Brief + M5 |
| Điều PHẢI KHÔNG xảy ra | Câu "chống…", "tránh…", "không cho…" | Brief |

Câu hỏi chốt khối: **mỗi mục tiêu ở đây sẽ được luật nào trong spec bảo vệ?** Mục tiêu không có luật nào bảo vệ = mục tiêu trang trí; luật không phục vụ mục tiêu nào = luật đội tự nghĩ ra (§2).

### M2. Dòng tiền [Volere 13b Interfacing; PMI Financial Analysis]

Vẽ từng chặng tiền đi. Mỗi chặng ghi đủ 5 ô — thiếu ô nào là một câu hỏi:

| Chặng | Ai trả → ai nhận | Lúc nào | Qua đâu | Thất bại thì sao | Ai chịu phí |
|---|---|---|---|---|---|
| Thu cọc | khách → shop | ? | cổng nào | ? | ? |
| Trừ cọc vào đơn | (bút toán nội bộ) | ? | — | ? | — |
| Hoàn cọc | shop → khách | ? | ? | **? ← ô hay bị bỏ trống nhất** | ? |

Ba câu bắt buộc trả lời trước khi viết bất kỳ luật tiền nào:
1. **Khoản nào không thể đảo?** (đã capture, đã settle, đã đối soát) — luật không được hứa đảo cái không đảo được.
2. **Hoàn tiền là "quyết định" hay "hoàn tất"?** Hệ thống quyết định hoàn *tức thì*; tiền *về tay khách* phụ thuộc bên ngoài. Spec phải tách hai mốc (33 §3).
3. **Ai trả phí cổng thanh toán khi hold bị bỏ?** Cọc 50.000 ₫ hoàn 100% mà phí cổng ~1–3% không hoàn → mỗi hold bỏ là một khoản lỗ. Đây là `affordable` của ISO 29148 [ISO/IEC/IEEE 29148].

### M3. Dòng tồn & nguồn chân lý

| Câu | Vì sao phải trả lời |
|---|---|
| Cột nào của tồn đổi, ở bước nào, bao nhiêu | Không có = §4 spec bịa công thức |
| **Ai là nguồn chân lý** cho mỗi con số | ERP giữ `OnHand`, e-com giữ `Reserved` [Oracle; Shopify] → hai hệ có thể lệch |
| Đồng bộ **tức thì hay định kỳ**; chu kỳ bao lâu | ATP thật có cửa sổ dừng đồng bộ (Oracle `ATP 24x7 Synchronization Downtime` ≥ 10 phút) [Oracle] |
| Khi tồn thật tụt dưới tổng đang giữ thì hệ thật làm gì | Shopify để `Available` **âm**, KHÔNG tự hủy gì [Shopify]. Nếu spec chọn "tự hủy hold đã cọc" thì đó là cam kết vận hành nặng — phải có luật thông báo + CSKH + thứ tự hủy |
| Giữ tồn có phải **khóa cứng** không | IBM: `reserveAvailableInventory` **không khóa kho**, yêu cầu vẫn có thể thất bại sau khi kiểm thấy còn hàng [IBM]. Shopify bắt dùng compare-and-swap, trả `CHANGE_FROM_QUANTITY_STALE` khi lệch [Shopify] → **phải có luật cho ca "kiểm thấy còn hàng nhưng tạo giữ thất bại"** |
| Có ưu tiên giữa các hold không | Oracle Allocated ATP cho kênh ưu tiên cao **lấy phần đã phân bổ** của kênh thấp hơn [Oracle] |

### M4. Biên hệ thống & phụ thuộc ngoài [BABOK Scope Modelling / Context Diagram; Volere 13b]

Sáu láng giềng cố định của miền giữ hàng. Mỗi dòng thiếu cột nào = một câu hỏi và một lỗ hổng loại #19 (50 §1):

| Láng giềng | Chiều | Kích hoạt bởi | Trễ / chu kỳ | Ngữ nghĩa giao hàng | Nguồn chân lý của |
|---|---|---|---|---|---|
| Kênh khách (web/app/API) | vào | khách | tức thì | at-most-once | ý định của khách |
| Cổng thanh toán | ra + **callback vào** | hệ thống, rồi cổng | **bất đồng bộ, phút → ngày** | **at-least-once, KHÔNG bảo đảm thứ tự** [Stripe] | kết quả giao dịch |
| ERP / WMS | vào (đồng bộ tồn) | job | **định kỳ, có cửa sổ dừng** [Oracle] | eventual | `OnHand` |
| Bộ hẹn giờ (job hết hạn) | nội bộ | cron | **định kỳ, không đúng mốc** [IBM purge agent] | at-least-once | không gì |
| Kênh thông báo | ra | sự kiện | best-effort | có thể mất | không gì |
| Bảng điều khiển CSKH | vào | người | tức thì | — | lý do can thiệp |

Ba hệ quả bắt buộc vào spec:
- Callback **at-least-once + sai thứ tự** ⇒ phải có luật khử trùng theo **ID sự kiện của cổng**, không theo timestamp [Stripe].
- Job **định kỳ** ⇒ hiệu lực hold tính theo `expires_at`, **không** theo lúc job quét; và phải nói hệ thống làm gì trong khoảng trễ.
- Thông báo **best-effort** ⇒ không được để hiệu lực của hold phụ thuộc việc gửi được thông báo.

### M5. Actor & động cơ lạm dụng (abuse model) [HTSM Operations: disfavored/extreme use]

Không phải "actor nào có quyền gì" (đó là §3 spec) mà **"ai được lợi khi lạm dụng luật này"**. Sáu kẻ cố định:

| Kẻ | Được lợi gì | Lách bằng cách nào | Luật phải chặn |
|---|---|---|---|
| Bot gom hàng | khóa tồn để đầu cơ / phá đối thủ | tạo hàng loạt hold, không trả tiền | rào **trước** khi khóa tồn |
| Kẻ đổi danh tính | vượt hạn mức | email/SĐT/tài khoản mới | hạn mức phải neo vào cái **khách không tự đặt được** |
| Kẻ hủy chuỗi | giữ chỗ vô hạn phí 0 | hold → hủy → hold lại | cooldown + tính lịch sử hủy |
| Kẻ săn giá | khóa giá rồi chờ giá lên | hold khi giá thấp | thời hạn + trần số lượng |
| Người trong (CSKH) | ưu ái/che lỗi | hủy hộ, gia hạn hộ | lý do bắt buộc + audit + không tự phục vụ |
| Khách vô ý | không phải kẻ xấu | bấm 2 lần, mất mạng giữa luồng | khử trùng, không phạt |

**Luật cứng của khối này:** mỗi hạn mức trong spec phải trả lời được "neo vào cái gì, và khách có tự đổi được cái đó không?". Neo vào dữ liệu khách tự khai (email, SĐT chưa xác thực, tên) = hạn mức trang trí, và là lỗ hổng loại #18 (50 §1).

### M6. Kịch bản suy biến — ConOps off-nominal [NASA SE Handbook]

NASA: thiếu ConOps thì spec **qua được kiểm tra kỹ thuật nhưng thất bại khi vận hành thật**, vì bỏ sót kịch bản sự cố và chế độ suy biến [NASA]. Mười hai kịch bản dưới đây **phải** có câu trả lời trong spec hoặc bị đẩy ra NGOÀI phạm vi tường minh. Đánh dấu ✓/✗ khi rà spec lúc 11:47:

| # | Kịch bản suy biến | Nếu spec im lặng |
|---|---|---|
| 1 | Cổng thanh toán không phản hồi khi đang tạo hold | tồn bị khóa hay không? |
| 2 | Cổng báo thành công **sau khi** hold đã đóng | tiền về đâu? |
| 3 | Cổng báo **hai lần** cùng một giao dịch | thu hai lần? |
| 4 | Cổng báo các sự kiện **sai thứ tự** (thành công đến sau thất bại) | trạng thái cuối là gì? |
| 5 | **Hoàn tiền thất bại** (thẻ đóng/hết hạn, ví bị khóa) | khách mất tiền, không có luật nào cứu |
| 6 | Job hết hạn **không chạy** một khoảng dài | tồn bị khóa vô hạn |
| 7 | Job chạy **hai lần** cùng một hold | hoàn tiền hai lần |
| 8 | ERP đồng bộ về số **thấp hơn** tổng đang giữ | hủy hold nào, thông báo ai |
| 9 | ERP **không đồng bộ được** trong cửa sổ dừng | cho tạo hold theo số cũ hay từ chối |
| 10 | Kiểm tra thấy còn hàng nhưng **ghi giữ thất bại** (stale/CAS) [IBM; Shopify] | khách thấy gì |
| 11 | Thông báo **không gửi được** | hold còn hiệu lực chứ? có bù giờ? |
| 12 | Hệ thống **downtime** làm hold quá hạn oan | bù giờ hay không |

## 2. Bảng đối chiếu Mục tiêu ↔ Luật (Why vs How) [WWWWWHKE]

Lập lúc 11:40, trước khi rà §0. Mỗi mục tiêu M1 một dòng, mỗi luật một dòng:

| Mục tiêu | Luật phục vụ | Luật nào **làm hỏng** mục tiêu này | Xử lý |
|---|---|---|---|
| (ví dụ) chống bot gom hàng | BR-03 hạn mức | BR-18 guest + BR-01 khóa tồn trước khi thu tiền + BR-10 hủy phí 0 | hoặc thêm rào trước khóa tồn, hoặc **hỏi AI Khách hàng ngay** — mâu thuẫn này gần chắc là chỗ specs thật có luật riêng |

Ba kết luận đọc ra từ bảng:
1. Luật **không** thuộc cột 2 hay cột 3 của mục tiêu nào ⇒ đội tự nghĩ ra ⇒ ứng viên `[GIẢ ĐỊNH]` rủi ro cao.
2. Ô cột 3 có chữ ⇒ **specs thật gần chắc chắn có quy định mà ta chưa biết** (không ai thiết kế tính năng tự phá mục tiêu của nó) ⇒ đẩy lên câu hỏi P0.
3. Mục tiêu có cột 2 trống ⇒ spec đang bỏ trắng đúng phần brief nhấn mạnh ⇒ đối thủ bắn vào đây là TRÚNG.

## 3. Đầu ra: `battle/mo-hinh-bai-toan.md`

```markdown
# Mô hình bài toán — <tên tính năng>
> Nguồn: brief. Ô `?` = chưa biết → câu hỏi P0. Ô `[SUY RA]` = đội suy ra, chưa verify.

## M1 Mục tiêu & thước đo
| # | Mục tiêu | Thước đo | Ai chịu thiệt | PHẢI KHÔNG xảy ra |
## M2 Dòng tiền
| Chặng | Ai→ai | Lúc nào | Qua đâu | Thất bại → | Ai chịu phí |
+ 3 câu: khoản không đảo được · quyết định vs hoàn tất · ai trả phí cổng
## M3 Dòng tồn & nguồn chân lý
| Số | Ai là nguồn chân lý | Đồng bộ tức thì/định kỳ | Khi lệch thì ai thắng |
## M4 Biên hệ thống (6 láng giềng)
| Láng giềng | Chiều | Kích hoạt | Trễ | Ngữ nghĩa giao hàng | Nguồn chân lý của |
## M5 Actor & động cơ lạm dụng
| Kẻ | Được lợi | Lách bằng | Luật phải chặn | Đã có luật? |
## M6 Kịch bản suy biến (12 ca) — ✓ có luật / ✗ hở / ⛔ ngoài phạm vi
## Bảng Mục tiêu ↔ Luật (điền lúc 11:30)
## Câu hỏi P0 sinh ra từ mô hình (nguyên liệu để gọt 5 câu — 20 §3)
```

## 4. Mô hình này lái 5 câu hỏi thế nào

Chỉ có **5 câu hỏi cho cả ngày** (00 §A), nên mô hình không còn dùng để *sinh thêm* câu hỏi — nó dùng để **chọn** ô nào xứng đáng chiếm chỗ trong 5 câu và ô nào phải tự điền bằng mặc định ngành.

| Ô mô hình | Đi vào đâu | Nếu không hỏi được |
|---|---|---|
| M1 mục tiêu `?` hoặc "PHẢI KHÔNG xảy ra" trống | **C1** (nửa sau) | Bảng Mục tiêu↔Luật không lập được ⇒ không phát hiện luật tự phá mục tiêu. Ô này gần như luôn phải hỏi |
| M5 hạn mức neo vào dữ liệu tự khai | **C3** (dòng "đơn vị neo hạn mức") | Dùng luật an toàn hai chiều 33 §3.3, **không chọn hộ** |
| M2 ô "thất bại →" trống | **C3** (% hoàn + ngày tiền về) và **C4** ca (3) | Tách hai mốc theo 33 §3.1, để `[..]` cho số chưa biết |
| M3 nguồn chân lý tồn | **C4** ca (5), (6) | Mặc định ngành: hệ nội bộ là nguồn chân lý, ERP đồng bộ định kỳ (33 §2) |
| M4 cột "nguồn chân lý"/"trễ" trống | **C4** ca (1), (2), (4) | Điền theo bảng thực tế 33 §2, đánh `[NGÀNH]` |
| M6 ca ✗ | **C4** (8 trong 12 ca) | Bốn ca còn lại phủ bằng luật §0.11–0.14 (30 §2) |
| Bảng §2 có ô cột 3 (luật phá mục tiêu) | **C5** — ưu tiên cao nhất | Chọn phương án phục vụ mục tiêu brief, ghi `[GIẢ ĐỊNH-MT]` |

Đảo lại: **ô mô hình còn `?` sau C1–C4 = một dòng `G-xx` trong RTM và một dòng `[GIẢ ĐỊNH]` trong spec** (20 §5), và phải nằm trong bảng xếp hạng rủi ro giả định của `/spec-write`. Mười dòng rủi ro cao nhất là nội dung câu C5.

## 5. Ví dụ đã điền — brief "Đặt cọc giữ hàng" 08/09 (dùng làm mẫu diễn tập)

**M1** — Mục tiêu: (1) tăng tỷ lệ chốt đơn *[thước đo: % hold → đơn]*; (2) **chống bot gom hàng** *[thước đo: % tồn bị khóa bởi hold không dẫn tới đơn]*. PHẢI KHÔNG xảy ra: bot gom hết hàng giới hạn; khách mất cọc oan (brief nói hoàn cọc ở cả 2 nhánh kết thúc).

**M2** — Thu cọc: khách → shop, qua cổng, bất đồng bộ. Hoàn cọc: `?` mốc, `?` phương thức thay thế khi hoàn thất bại, **phí cổng: `?`** → 3 câu P0.

**M5** — Hạn mức đang neo vào `email guest tự khai` ⇒ **trang trí**. Câu P0: "Hạn mức chống gom hàng tính theo gì: tài khoản đã đăng nhập / SĐT đã xác thực OTP / thiết bị / thẻ thanh toán?" — và "Guest **chưa đăng nhập** có được tạo hold không?" phải hỏi lại dù brief nói "mọi khách hàng truy cập nền tảng", vì brief cũng nói chống bot: hai câu của brief xung đột nhau, và đó là dấu hiệu điển hình specs thật có luật thứ ba mà brief không kể.

**§2 bảng** — ô cột 3 có chữ ngay từ đầu: `BR-01 khóa tồn trước khi thu tiền` + `hủy hoàn 100% phí 0` + `guest` phá mục tiêu 2. ⇒ P0: "Tồn bị khóa tại thời điểm nào: khi bấm giữ hay khi cọc thành công?" và "Khách tự hủy có bị mất phần cọc hay phí nào không?"

**Bài học ghi lại:** cả ba câu P0 quan trọng nhất của đề này **không** sinh ra từ ngân hàng câu hỏi tham số — chúng sinh ra từ M1/M5 và bảng §2. Đó là lý do khối này chạy trước.
