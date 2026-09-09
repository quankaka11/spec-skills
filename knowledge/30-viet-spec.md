*Cho vai THỦ (người gõ spec), khung 11:00–12:00 ngày 12/09; mở song song RTM [HD §2.4] và log hội thoại AI Khách hàng.*

# 30 — Quy tắc & template viết spec cho AI Executor đọc

Quy ước file: `[..]` = chỗ điền · ⚠ = lấy từ dòng ⚠ trong RTM · HD = hackathon_descriptions.md.

**Điều kiện vào:** `mo-hinh-bai-toan.md` (knowledge/05) phải tồn tại trước khi gõ chữ đầu tiên. Spec viết mà không có mô hình bài toán thì đạt mọi cổng hình thức và vẫn sai bài toán — bằng chứng đo được ở knowledge/05 §0 và 32 §7.

**Hai luật ngân sách mới (từ bài học 08/09):**
- **Đích là ≤ 2.700 từ trên bản nộp, không phải ≤ 3.000.** Bản 08/09 về đích 2.991/3.000 → còn 9 từ đệm, không đủ để vá một luật sau khi elicit trả về số thật. 300 từ đệm ≈ 4–6 luật vá.
- **Bản nộp không chứa câu nói về chính nó.** "36/36 ô đã điền", "Phân hoạch 2×2×2 = 8 tổ hợp; phủ 4+2+2 = 8", "ba hàng rời ở cột 1 hoặc 2 → đủ, không chồng lấn" là chứng minh gửi cho người rà, không phải luật cho Executor. Chúng thuộc `spec.md` nội bộ và `review.md`; bỏ khỏi `spec.nop.md` (~60 từ ở bản 08/09).

## 1. TEMPLATE SPEC (11 mục, ngân sách theo [HD §4.7])

Copy nguyên khối; điền hết ô mọi bảng [HD §4.8-7].

### §0. Nguyên tắc giải nghĩa & luật mặc định — 200 từ
→ Dùng khung ở mục 2. VIẾT ĐẦU TIÊN lúc 11:00–11:10 từ khung mục 2, cột verify điền từ RTM/log; rà lại 11:47–11:50, dòng chưa verify → sửa khớp hoặc xóa.

### §1. Phạm vi — 150 từ
NGOÀI phạm vi nêu tường minh để Executor trả lời "không thuộc tài liệu này" (0.9).

- **TRONG phạm vi:** [tạo / xem / gia hạn / hủy / chuyển hold thành đơn; loại hàng áp dụng; kênh].
- **NGOÀI phạm vi:** [thanh toán đơn cuối, vận chuyển, đổi trả, khuyến mãi, kế toán, thuế].

### §2. Glossary — 200 từ
Chỉ thuật ngữ dễ hiểu sai; trạng thái viết `CODE_STYLE`; một khái niệm một tên.

| Thuật ngữ | Định nghĩa một câu (có đơn vị / trạng thái) |
|---|---|
| Hold | [Yêu cầu giữ 1..N HoldLine (SKU + qty) cho một khách trong TTL] |
| TTL | [.. phút, tính từ `created_at`] |
| Cọc (deposit) | [.. % giá / số tiền cố định; hoàn khi ..] |

### §3. Actor & quyền — 200 từ
✓ / ✗ / ✓* (có điều kiện, ghi BR). Ô không ✓ = ✗ theo 0.4. Bắt buộc cột Guest, System/Job [HD §5.9].

| Hành động | Guest | Khách đăng nhập | Sale/CS | Admin | System/Job |
|---|---|---|---|---|---|
| Tạo hold | [..] | [..] | [..] | [..] | ✗ |
| Gia hạn | [..] | [✓* BR-..] | [..] | [..] | ✗ |
| Hủy | [..] | [..] | [..] | [..] | [✓ khi hết TTL, BR-..] |
| Chuyển thành đơn | [..] | [..] | [..] | [..] | ✗ |

### §4. Mô hình tồn kho & công thức — 200 từ
- `ATP = OnHand − Reserved − Safety` ⚠
- Tạo hold: `Reserved += qty`; EXPIRED/CANCELLED: `Reserved −= qty`; CONVERTED: `Reserved −= qty; OnHand −= qty`.
- Điều kiện tạo: `ATP ≥ qty` tại thời điểm server nhận (0.8).
- Phạm vi: [theo SKU/variant; theo kho/chi nhánh hay toàn hệ thống].
- Giá: [khóa tại `created_at` / tính lại khi chuyển đơn] ⚠
- Giới hạn: [qty tối đa/HoldLine; số hold ACTIVE/khách; SKU không được hold].

### §5. State machine — 350 từ
Mỗi ô = `→ TRẠNG_THÁI (BR-xx)`, `Từ chối 0.5` hoặc `KHL` (sự kiện không thể xảy ra ở trạng thái đó). Trạng thái cuối (terminal) không có chuyển tiếp ra.

Trạng thái: `[PENDING_DEPOSIT], ACTIVE, [EXTENDED], CONVERTED, EXPIRED, CANCELLED_CUSTOMER, CANCELLED_STAFF`.

| Trạng thái \ Sự kiện | Đạt expires_at | Cọc thành công (callback) | Cọc thất bại / timeout | Khách hủy | Sale/Admin hủy | Chuyển đơn | Yêu cầu gia hạn | Kho bị điều chỉnh < Reserved |
|---|---|---|---|---|---|---|---|---|
| PENDING_DEPOSIT | [..] | → ACTIVE (BR-..) | → CANCELLED_STAFF hoặc giữ PENDING_DEPOSIT thêm [..] phút (BR-..) | [..] | [..] | Từ chối 0.5 | Từ chối 0.5 | [..] |
| ACTIVE | → EXPIRED (BR-..) | KHL | KHL | → CANCELLED_CUSTOMER (BR-..) | → CANCELLED_STAFF (BR-..) | → CONVERTED (BR-..) | [→ EXTENDED / giữ ACTIVE, expires_at mới] (BR-..) | [..] ⚠ |
| EXTENDED | [..] | KHL | KHL | [..] | [..] | [..] | [Từ chối, BR-..] | [..] |
| CONVERTED / EXPIRED / CANCELLED_* (terminal nếu N7-07 = Không; nếu AI cho khôi phục, thêm cột "Khôi phục" → ACTIVE (BR-..) với cửa sổ [..] phút) | Không tác động | KHL | KHL | Từ chối 0.5 | Từ chối 0.5 | Từ chối 0.5 | Từ chối 0.5 | Không tác động |

### §6. Luật nghiệp vụ — 900 từ
Thứ tự: tạo → sửa → gia hạn → hết hạn → hủy → chuyển đơn → đồng thời/trùng. Dòng ⚠ RTM viết trước.

| Mã | Luật (mẫu EARS, mục 3) | Kho / Tiền / Thông báo / Log | KHÔNG ĐƯỢC | RTM |
|---|---|---|---|---|
| BR-01 | KHI [..] VÀ [..], hệ thống PHẢI [..] | [±Reserved; thu/hoàn; kênh→người nhận; trường log] | [..] | A-.. |

Luật ≥ 2 điều kiện → decision table, cột: `# | Loại khách | Loại hàng | Có cọc | TTL (phút) | Gia hạn tối đa | Kết quả | BR`; điền hết tổ hợp, ô không hợp lệ ghi "Từ chối 0.5" (ví dụ đã chứng minh đủ/rời: knowledge/31 §2.5 DT-1; hit policy: 31 §1).

### §7. Luồng chính + luồng phụ — 300 từ
Bước đánh số trỏ BR: `1. Khách gửi yêu cầu tạo hold. 2. Hệ thống kiểm tra §3, BR-01, ATP. 2a. NẾU ATP < qty → BR-..`.

### §8. Ngoại lệ & xử lý lỗi — 300 từ
Tối thiểu 7 EX: hai khách tranh SKU cuối; kho điều chỉnh khi đang hold; cọc thất bại; request trùng; tài khoản bị khóa; SKU ngừng bán; hết hạn đúng lúc chuyển đơn.

Bảng cột: `Mã EX-xx | Tình huống | Điều kiện phát hiện | Hành vi hệ thống | Trạng thái sau | Kho / Tiền | BR`.

### §9. Thông báo — 100 từ
| Mốc | Kênh | Người nhận | Nội dung tối thiểu | BR |
|---|---|---|---|---|
| Tạo hold thành công | [email / push / in-app] | [Khách] | hold_id, SKU, expires_at | BR-.. |
| Trước hết hạn [..] phút | [..] | [Khách] | [..] | BR-.. |
| Hết hạn / hủy | [..] | [Khách; Sale phụ trách] | [lý do] | BR-.. |

### §10. Phi chức năng & audit — 100 từ
- Audit log: mọi chuyển trạng thái ghi `hold_id, from, to, actor, timestamp, reason` (0.7).
- Job hết hạn chạy mỗi [..] phút; sai lệch ≤ [..] giây.
- Đồng thời: khóa theo SKU, xử lý FCFS (0.8).
- Idempotency: khóa = [..], hiệu lực [..] phút (0.10).

## 2. KHUNG §0 CATCH-ALL [HD §5.5]

⚠ Viết §0 bản 1 lúc 11:00, rà lại 11:47 sau khi verify. Catch-all lệch specs thật = tự tạo mâu thuẫn (specs thật: "hết hạn → tự hủy, hoàn cọc" nhưng 0.5 nói "từ chối, giữ nguyên") [HD §5.5].

| # | Dòng luật | Verify? (lượt / giờ) | Câu hỏi verify |
|---|---|---|---|
| 0.1 | Tài liệu này là nguồn duy nhất. Không suy diễn từ thông lệ ngành hay hệ thống khác. | Không cần | — |
| 0.2 | Mọi mốc thời gian theo [TZ], đơn vị phút, giờ đồng hồ liên tục (gồm ngày lễ, ngoài giờ làm việc) trừ khi luật cụ thể nói khác. | [..] | "TTL tính giờ liên tục hay giờ làm việc? Ngày lễ?" |
| 0.3 | Khoảng thời gian là [bắt đầu, kết thúc): gồm mốc đầu, không gồm mốc cuối. | [..] | "Đúng phút thứ [TTL], hold còn hiệu lực không?" |
| 0.4 | Hành động không được cấp quyền tường minh tại §3 → actor KHÔNG ĐƯỢC thực hiện. | [..] | "Liệt kê actor và hành động được phép." |
| 0.5 | Tình huống không khớp luật nào → hệ thống PHẢI từ chối, giữ nguyên trạng thái, không đổi tồn kho, không thu/hoàn tiền, ghi log lý do. | [..] | "Yêu cầu không thuộc trường hợp nào đã mô tả, hệ thống làm gì?" |
| 0.6 | Hai luật cùng áp dụng: [an toàn tồn kho > nghĩa vụ tài chính đã phát sinh > cam kết với khách > tối ưu vận hành]. | [..] | "Kho bị điều chỉnh dưới Reserved khi hold có cọc đang ACTIVE: giữ hold hay giữ kho?" |
| 0.7 | Mọi thay đổi trạng thái hold PHẢI cập nhật tồn kho theo §4 và ghi audit theo §10, kể cả khi luật cụ thể không nhắc. | Không cần | — |
| 0.8 | Xung đột đồng thời trên cùng tồn kho: xử lý theo thời điểm server tiếp nhận (FCFS). | [..] | "Hai khách giữ SKU cuối cùng giây, ai được?" |
| 0.9 | Nghiệp vụ tại §1 NGOÀI PHẠM VI không thuộc tài liệu này. | [..] | "Nghiệp vụ nào KHÔNG thuộc tính năng này?" |
| 0.10 | Request trùng cùng khóa idempotency → trả kết quả lần đầu, không tạo hold mới. | [..] | "Bấm giữ hàng 2 lần liên tiếp cùng SKU: 1 hay 2 hold?" |
| 0.11 | Khử trùng sự kiện từ cổng thanh toán theo **mã giao dịch của cổng**, không theo thời điểm nhận; sự kiện đến sai thứ tự → lấy kết quả sau cùng theo mã giao dịch. | [..] | "Cổng báo trùng: ghi 1 hay 2 lần? Sai thứ tự thì lấy kết quả nào?" (N0-09) |
| 0.12 | Mốc hệ thống **quyết định** (thu, hoàn, đổi trạng thái) khác mốc bên ngoài **hoàn tất**. Mọi nghĩa vụ tiền có hai mốc và một trường trạng thái riêng. | [..] | "Hoàn cọc: khởi tạo sau ? phút, tiền về sau tối đa ? ngày làm việc?" (N0-05) |
| 0.13 | Hạn mức và định danh neo vào [đơn vị định danh]; yêu cầu không kèm đơn vị định danh hợp lệ PHẢI bị từ chối. | [..] | "Hạn mức tính theo tài khoản / SĐT đã OTP / thiết bị / thẻ? Chọn 1." (N0-02) |
| 0.14 | Thất bại của kênh thông báo KHÔNG làm đổi trạng thái hold, `expires_at`, hay nghĩa vụ hoàn tiền. | Không cần | — |

Bốn dòng 0.11–0.14 là bốn luật rẻ nhất phủ 8/12 kịch bản suy biến bắt buộc (knowledge/05 §M6), tổng ~90 từ. Xem mẫu câu dán được ở knowledge/32 §3.2.

## 3. CÚ PHÁP LUẬT

### 3.1 Năm mẫu EARS tiếng Việt [HD §4.1]

| Mẫu | Khuôn câu cố định | Ví dụ miền giữ hàng |
|---|---|---|
| Ubiquitous (luôn đúng) | `Hệ thống PHẢI <hành động> <ràng buộc đo được>.` | Hệ thống PHẢI gắn `expires_at = created_at + TTL` cho mọi hold khi tạo. |
| Event-driven | `KHI <sự kiện> [VÀ <điều kiện>], hệ thống PHẢI <hành động>.` | KHI khách đăng nhập gửi yêu cầu tạo hold VÀ `ATP ≥ qty`, hệ thống PHẢI tạo hold ACTIVE và tăng Reserved thêm qty. |
| State-driven | `TRONG KHI <trạng thái>, hệ thống PHẢI <hành động>.` | TRONG KHI hold ở EXPIRED, hệ thống PHẢI từ chối mọi yêu cầu gia hạn với mã lỗi HOLD_EXPIRED. |
| Unwanted behaviour | `NẾU <điều kiện không mong muốn>, THÌ hệ thống PHẢI <hành động>.` | NẾU thanh toán cọc thất bại, THÌ hệ thống PHẢI không tạo hold, giữ nguyên Reserved, trả lỗi DEPOSIT_FAILED. |
| Optional feature | `Ở NƠI <cấu hình/biến thể bật>, hệ thống PHẢI <hành động>.` | Ở NƠI cửa hàng bật nhận tại quầy (pickup), hệ thống PHẢI bắt buộc chọn điểm nhận khi tạo hold. |

Kết hợp được: `TRONG KHI ACTIVE, KHI đạt expires_at, hệ thống PHẢI ...`. Không "và/hoặc", "trước/sau đó" (40 §2).

### 3.2 Sáu thành phần bắt buộc của một luật [HD §4.4]

(1) điều kiện kích hoạt có mốc, đơn vị · (2) chủ thể · (3) hành động chính · (4) tác động phụ kho / tiền / thông báo / log · (5) điều KHÔNG ĐƯỢC · (6) mã BR-xx.

### 3.3 Mẫu BR-xx chuẩn

`BR-nn` — [TRONG KHI `<state>`,] KHI `<sự kiện>` [VÀ `<điều kiện>`], hệ thống PHẢI (a) `<hành động chính>`, (b) kho: `<±Reserved/OnHand>`, (c) tiền: `<thu/hoàn X% trong ≤ N giờ / không>`, (d) thông báo: `<kênh → người nhận>`, (e) log: `<trường>`. Hệ thống KHÔNG ĐƯỢC `<hành vi cấm>`. *Fit:* `<giá trị/trạng thái quan sát được>` [Volere]. *RTM:* A-nn.

Ví dụ đủ 6 thành phần [HD §4.4]:

> `BR-04` — **KHI** một hold ở trạng thái `ACTIVE` đạt mốc `expires_at` (tính theo Asia/Ho_Chi_Minh, giờ đồng hồ liên tục kể cả ngày lễ), **THÌ** hệ thống PHẢI (a) chuyển hold sang `EXPIRED`, (b) giảm `Reserved` và tăng `Available` đúng số lượng của mọi HoldLine, (c) hoàn tiền cọc về phương thức gốc trong 24 giờ, (d) gửi email cho khách và in-app notification cho sale phụ trách, (e) ghi audit log gồm `hold_id`, thời điểm, actor = `system`. Hệ thống KHÔNG ĐƯỢC tự gia hạn trong trường hợp này.

## 4. CHECKLIST 17 QUY TẮC VIẾT REQUIREMENT TỐT

Hợp nhất [NASA App.C], [Volere], [HD §4.8]. Chạy trên từng BR lúc 11:50.

| # | Quy tắc | Trước → Sau |
|---|---|---|
| 1 | PHẢI = yêu cầu; KHÔNG ĐƯỢC = cấm; không dùng nên/có thể/thường | "Hold nên bị hủy khi hết hạn" → "KHI đạt `expires_at`, hệ thống PHẢI chuyển hold sang EXPIRED" |
| 2 | Câu chủ động, chủ ngữ tường minh | "Tồn kho được cập nhật" → "Hệ thống PHẢI giảm Reserved của SKU đúng qty" |
| 3 | Một luật một ý; một chủ ngữ một vị ngữ; tác động phụ tách (a)(b)(c) | "Khách hủy thì hoàn cọc và báo sale và log" → (a) → CANCELLED_CUSTOMER, (b) hoàn 100% cọc, (c) in-app → sale, (d) log |
| 4 | Mọi luật có fit criterion đo được; không tìm được tiêu chí = luật mơ hồ | "Hoàn cọc sớm" → "Hoàn cọc về phương thức gốc ≤ 24 giờ kể từ khi chuyển EXPIRED" |
| 5 | Mọi số có đơn vị + mốc bắt đầu + bao gồm/loại trừ | "TTL 2 giờ" → "TTL = 120 phút từ `created_at`; hiệu lực trong [created_at, created_at + 120 phút)" |
| 6 | Dùng ≤ < ≥ > thay "đến", "trong vòng", "tối đa" | "tối đa 5 hold" → "Số hold ACTIVE của một khách ≤ 5 tại mọi thời điểm" |
| 7 | Không đại từ trôi: nó, này, đó | "Khi này xảy ra, nó bị hủy" → "KHI thanh toán cọc thất bại, hold PENDING_DEPOSIT bị xóa" |
| 8 | Cấm từ không kiểm chứng: nhanh, hợp lý, kịp thời, khi cần, v.v., và/hoặc (đủ: 40 §2) | "Xử lý hết hạn kịp thời" → "Job hết hạn chạy mỗi 1 phút; sai lệch ≤ 60 giây" |
| 9 | Một khái niệm một tên, khai báo ở §2 | "giữ hàng / hold / đặt trước" lẫn lộn → chỉ "hold" |
| 10 | Nêu điều KHÔNG ĐƯỢC làm cạnh hành động chính | "Hết hạn → EXPIRED" → "+ KHÔNG ĐƯỢC tự gia hạn; KHÔNG ĐƯỢC giữ Reserved" |
| 11 | Không tham chiếu ngoài spec | "theo chính sách hoàn tiền hiện hành" → viết công thức hoàn tiền vào BR |
| 12 | Mã duy nhất; mọi tham chiếu chéo tồn tại | "xem luật gia hạn" → "áp dụng BR-07" |
| 13 | WHAT không HOW; không mô tả thao tác UI; không TBD — chưa hỏi được thì bỏ luật, để 0.5 chặn | "Sale bấm nút Hủy" → "KHI Sale gửi yêu cầu hủy hold ACTIVE, hệ thống PHẢI ..." |
| 14 | Sự kiện không mong muốn có luật riêng: lỗi thanh toán, kho âm, request trùng, hết hạn đúng lúc chuyển đơn | Chỉ luồng chính → thêm EX-01..EX-07 |
| 15 | Kết quả xác định đủ 4 chiều: trạng thái cuối + kho + tiền + thông báo | "Hold bị hủy" → "→ CANCELLED_STAFF; Reserved −qty; hoàn 100% cọc ≤ 24 giờ; email → khách" |
| 16 | Không ô trống trong bảng; điều kiện phải kiểm chứng được bằng dữ liệu trong spec | "gia hạn nếu cần" → "gia hạn ≤ 1 lần, +60 phút, chỉ khi còn ≥ 10 phút trước `expires_at`" |
| 17 | Giả định ngầm viết thành luật; khẳng định trước, không phủ định kép | "(ai cũng hiểu 1 hold nhiều SKU)" → "BR-12: một hold chứa 1..N HoldLine; mỗi SKU ≤ 1 HoldLine" |

### Bảy quy tắc chất-lượng-nội-dung (18–24) — ISO/IEC/IEEE 29148 `feasible` + `affordable`

Quy tắc 1–17 kiểm *cách viết*. Bảy quy tắc dưới kiểm *điều được viết có đứng được trong thực tế và có phục vụ mục tiêu không*. Chi tiết cách chấm: knowledge/32 §1 (cổng F). Chạy lúc 11:35, **trước** khi rà §0.

| # | Quy tắc | Trước → Sau |
|---|---|---|
| 18 | **Không hứa hộ bên ngoài.** Mốc "hoàn tất" của việc do cổng/ngân hàng/ERP thực hiện phải tách khỏi mốc "quyết định" của hệ thống | "hoàn cọc hoàn tất ≤ 24 giờ" → "khởi tạo hoàn ≤ 5 phút, `refund_status = ĐANG_HOÀN`; cổng xác nhận → `ĐÃ_HOÀN`; quá [N] ngày làm việc chưa xác nhận → `HOÀN_THẤT_BẠI` + [phương án 2]" |
| 19 | **Hạn mức phải cưỡng chế được.** Neo vào dữ liệu khách tự đặt được = hạn mức trang trí | "hạn mức theo email khai lúc cọc" → "hạn mức theo [đơn vị định danh]; yêu cầu không có đơn vị định danh hợp lệ bị từ chối `IDENTITY_REQUIRED`" |
| 20 | **Mỗi luật phục vụ một mục tiêu.** Luật ở cột 3 bảng Mục tiêu↔Luật (05 §2) = luật tự phá mục tiêu → hỏi lại, không tự chốt | "guest tạo hold + khóa tồn trước khi thu tiền + hủy hoàn 100% phí 0" trong khi mục tiêu là chống bot → hỏi N0-02, N0-03, N0-04 trước khi viết |
| 21 | **Mỗi phụ thuộc ngoài có một luật cho chế độ lỗi của nó.** Sáu láng giềng (05 §M4) → sáu luật | im lặng về cổng timeout → "NẾU cổng không xác nhận trong hạn trả cọc, THÌ → CANCELLED (HETHONG), K+, T0" |
| 22 | **Không coi việc giữ tài nguyên là chắc chắn thành công.** Kiểm điều kiện xong vẫn có thể ghi thất bại [IBM; Shopify] | "`ATP ≥ q` khi nhận yêu cầu → tạo hold" → "+ NẾU ghi giữ thất bại do tồn đã đổi giữa lúc kiểm và lúc ghi, THÌ `OUT_OF_STOCK`, K0 T0, không giữ một phần, không tự thử lại" |
| 23 | **Việc giao cho người phải quan sát được và có trần.** Mỗi `CSKH`/`Admin`/`thủ công` cần một dòng §9 (ai được báo) + trường audit + trần số lượng hoặc điều kiện | "hệ thống hủy hold đã cọc khi kho lệch" → "+ báo CSKH phụ trách, ghi lý do vào audit, và nêu trần / thứ tự hủy tường minh" |
| 24 | **Nói ai chịu chi phí.** Hoàn 100%, thao tác thủ công, thông báo, tồn bị khóa đều có giá | "hoàn 100%, không thu phí hủy" → "+ phí cổng của khoản hoàn do [ai] chịu" hoặc đưa vào NGOÀI phạm vi tường minh |

## 5. KỸ THUẬT TIẾT KIỆM TỪ [HD §4.7]

**Chọn dạng §6 TRƯỚC khi gõ chữ đầu tiên — theo số dòng ⚠ trong RTM:**

| Số dòng ⚠ | Dạng §6 | Lý do |
|---|---|---|
| < 40 | bảng 5 cột như §1 (Mã · Luật · K/T/N/L · KHÔNG ĐƯỢC · RTM) | đủ ngân sách, dễ soi ô trống |
| ≥ 40 | **danh sách** `**BR-xx** — luật. Cấm: … ← A-yy` | mỗi hàng bảng tốn thêm ~4 "từ" dấu `\|`; 50 BR = ~200 từ chỉ riêng dấu bảng |

**Với đề ≥ 40 dòng ⚠, dồn tác động phụ thành luật chung ở §0.7** — "mọi chuyển trạng thái cập nhật tồn theo §4, ghi audit theo BR-xx, gửi thông báo theo §9, kể cả khi luật cụ thể không nhắc" — thay vì lặp cột K/T/N/L trong từng BR. Tiết kiệm ~700 từ trên 50 BR mà **không mất** thành phần (4) của §3.2, vì §0.7 là căn cứ hợp lệ cho Executor. Chỉ ghi tác động riêng ở BR nào khác luật chung (ví dụ hoàn tiền theo bậc).

Số đo thực tế (diễn tập đề "đặt cọc giữ hàng", 111 dòng ⚠): §6 viết đúng template bảng 5 cột tốn **2.235 từ** (ngân sách 900), toàn spec **5.753 từ** — phải nén 3 vòng và cuối cùng vẫn phải cắt §7, §10 và 3 BR trùng nội dung §1/§2. Chọn dạng danh sách + §0.7 từ đầu thì vào thẳng ~3.000.

- Tập luật cùng cấu trúc → một bảng; luật tổng quát + ngoại lệ ("Mọi terminal → Từ chối 0.5, trừ BR-xx") thay liệt kê.
- "áp dụng BR-07" thay lặp nội dung; §7 chỉ trỏ BR.
- Công thức (`ATP = OnHand − Reserved − Safety`) thay câu văn.
- Ký hiệu `[a, b)`, `→ STATE (BR-xx)`, ✓/✗/✓*, ±qty, KHL khai báo một lần ở §0 hoặc đầu bảng — Executor không đoán ký hiệu lạ.
- Không "vì sao", không mở bài, không mô tả màn hình.

## 6. THỨ TỰ VIẾT DƯỚI ÁP LỰC 11:00–12:00 [HD §4.9]

| Phút | Việc | Đầu ra |
|---|---|---|
| 11:00–11:10 | Chốt RTM (đánh ⚠, gán mã BR dự kiến) + §0 từ khung mục 2 (dòng chưa verify gửi câu hỏi verify ngay) | RTM đủ cột "Luật trong spec"; §0 bản 1 |
| 11:10–11:30 | §6: dòng ⚠ thành BR trước, rồi luật lifecycle còn lại; decision table cho luật ≥ 2 điều kiện | ≥ 15 BR |
| 11:30–11:40 | §5: bảng state × event, mỗi ô trỏ BR hoặc "Từ chối 0.5" | Bảng đầy 100% ô |
| 11:40–11:45 | §3: actor × quyền, có Guest và System/Job | Bảng đầy |
| 11:40–11:44 | §4 → §8 → §9 → §2 → §1 → §7 → §10 | Đủ 11 mục |
| 11:44–11:48 | **Cổng F** (knowledge/32 §1) trên từng BR + bảng Mục tiêu↔Luật (`/frame muc-tieu-luat`) | 0 ✗ ở F1/F2/F5/F7; mọi mục tiêu có luật phục vụ |
| 11:48–11:51 | Rà §0: đối chiếu từng dòng với log hội thoại, sửa khớp hoặc xóa; thêm 0.11–0.14 nếu chưa có | §0 khớp specs thật, 12 ca suy biến ✓ hoặc ⛔ |
| 11:51–11:56 | Red team: eval set 20 tình huống qua **hai** Executor mù độc lập; Ctrl+F danh sách đen knowledge/40 §2 (đủ 22 nhóm); đối chiếu ⚠ RTM | Lỗ hổng đã vá; 0 ca hai reader lệch nhau |
| 11:56–12:00 | Đếm từ trên bản nộp, đích ≤ 2.700; bỏ câu nói về chính spec; nộp; lưu bản copy | Đã nộp, còn đệm |

Cạn giờ → cắt theo thứ tự: **BR "không thuộc mục tiêu nào" (05 §2)** → §7 → §10 → §9 → §2. Không cắt §0, §3, §5, §6, §8, và không cắt bốn luật 0.11–0.14.

Phân công: A §6, §5; B §3, §4, §8, §9; C mô hình bài toán + RTM + §0 + cổng F + red team.

## 7. CỔNG CHẤT LƯỢNG TRƯỚC NỘP [HD §5.9]

Đánh ✓ từng dòng; một dòng chưa ✓ = sửa trước khi nộp.

| # | Kiểm tra |
|---|---|
| 1 | Mọi dòng ⚠ trong RTM đã map tới một BR có mã? |
| 2 | Mọi ô bảng state × event đã điền, kể cả "Từ chối 0.5"? |
| 3 | Bảng actor × quyền có cột Guest và System/Job? |
| 4 | Có 0.6 thứ tự ưu tiên khi hai luật xung đột, đã verify? |
| 5 | Có timezone và quy ước [đầu, cuối) tại 0.2, 0.3? |
| 6 | Đã Ctrl+F danh sách đen knowledge/40-tu-mo-ho.md §2 (đủ **22 nhóm**: 1–16 mơ hồ, 17–22 nội dung) + §3 **S1–S30**; ký hiệu chưa khai báo? |
| 7 | Không tham chiếu tài liệu ngoài; không đại từ trôi? |
| 8 | Mã BR/EX không trùng; mọi "áp dụng BR-xx" trỏ tới mã tồn tại? |
| 9 | Eval set 20 tình huống: không tình huống nào có 2 cách trả lời? |
| 10 | Mọi BR đủ 6 thành phần (mục 3.2) và fit criterion; mọi dòng §0 đã verify hoặc xóa? |
| 11 | Đếm từ ≤ 3.000 **trên bản nộp** (đã bỏ `← A-xx`, `(verify …)`) và đã lưu bản copy nội bộ cho kháng nghị? Bản nội bộ dài hơn ~200 từ — đo bản nội bộ sẽ tự cắt oan. Đếm bằng `LC_ALL=C.UTF-8 wc -w <file>`, **không** dùng `wc -w` trần: locale `C`/`POSIX` đếm sai ký tự đa byte (`—` `⇒` `→` `✓` `≥` `−` `§`) và báo thừa ~80 từ. |
| 12 | Mọi BR đã chấm **cổng F** (knowledge/32 §1); không còn ✗ ở F1 (khả thi), F2 (cưỡng chế được), F5 (phụ thuộc ngoài), F7 (phục vụ mục tiêu)? |
| 13 | Bảng **Mục tiêu↔Luật** (knowledge/05 §2) đã lập; mọi mục tiêu của brief có ≥1 luật phục vụ; mọi ô "làm hỏng mục tiêu" đã hỏi AI Khách hàng hoặc đã xử lý? |
| 14 | **12 kịch bản suy biến** (knowledge/05 §M6) đều ✓ có luật, hoặc ⛔ nằm trong danh sách NGOÀI phạm vi tường minh? |
| 15 | Mọi nghĩa vụ tiền đã tách mốc "khởi tạo" (ta kiểm soát) khỏi mốc "hoàn tất" (bên ngoài), và có luật cho ca **hoàn tiền thất bại**? |
| 16 | Bản nộp **không chứa câu nói về chính nó** ("36/36 ô đã điền", chứng minh phủ tổ hợp)? Còn ≥ 250 từ đệm dưới 3.000? |
