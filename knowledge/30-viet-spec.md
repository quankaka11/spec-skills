*Cho vai THỦ (người gõ spec), khung 11:00–12:00 ngày 12/09; mở song song RTM [HD §2.4] và log hội thoại AI Khách hàng.*

# 30 — Quy tắc & kỹ thuật viết spec cho AI Executor đọc

> **CẤU TRÚC SPEC = `knowledge/32-cau-truc-spec-btc.md` (10 mục BTC).** File 30 này là *kỹ thuật viết bên trong* cấu trúc đó: cú pháp luật, catch-all, checklist, tiết kiệm từ, cổng chất lượng. Đọc 32 trước, 30 sau.

Quy ước file: `[..]` = chỗ điền · ⚠ = lấy từ dòng ⚠ trong RTM · HD = hackathon_descriptions.md · "mục n" = mục n của cấu trúc BTC (knowledge/32 §1).

## 1. TEMPLATE SPEC (10 mục BTC — chi tiết cấu trúc ở knowledge/32 §1)

Copy nguyên khối; điền hết ô mọi bảng [HD §4.8-7]. Thứ tự cắt khi quá hạn mức: knowledge/32 §6, và trước cả danh sách đó là **BR không thuộc mục tiêu nào** (05 §2).

**Ngân sách tính bằng token, không bằng từ** (00 §A: ≤6.000 token, đích 5.400). Tổng mười mục dưới đây ≈ **5.580 token**, tức đã sát đích — nên mỗi mục vượt ngân sách của nó phải lấy chỗ từ mục khác, không được cộng thêm. Quy đổi nhanh khi viết: **1 từ tiếng Việt ≈ 2,5 token**, nên ~540 token ≈ 215 từ. Ngân sách theo *từ* của bản trước (3.000 từ ≈ 7.500 token) **vượt hạn mức 09/09** — đừng dùng lại.

Hai hệ quả phải nhớ khi phân bổ: dấu `|` của bảng là token thật (~6 token mỗi hàng 5 cột chỉ riêng dấu), và sơ đồ Mermaid tốn ~450–550 token cho cả bốn cái. Với đề nhiều luật (≥40 dòng RTM), chọn **dạng danh sách** cho mục 6 ngay từ đầu (§5) — viết mục 6 bằng bảng 5 cột đã đo được 2.235 từ ≈ 5.600 token, tức một mục ăn hết hạn mức của cả spec.

### Mục 1. Tổng quan & phạm vi 概要 — ~540 token
4–5 bullet: tên hệ thống, tên màn hình/chức năng, trạng thái tài liệu, chức năng này làm gì.

- **TRONG phạm vi:** [tạo / xem / gia hạn / hủy / chuyển hold thành đơn; loại hàng áp dụng; kênh].
- **NGOÀI phạm vi:** [thanh toán đơn cuối, vận chuyển, đổi trả, khuyến mãi, kế toán, thuế] — nêu tường minh để Executor trả lời "không thuộc tài liệu này" (0.9).

**1.x Nguyên tắc giải nghĩa & luật mặc định** — dùng khung catch-all ở mục 2 file này. VIẾT ĐẦU TIÊN lúc 11:00–11:10; rà lại 11:47–11:50, dòng chưa verify → sửa khớp hoặc gắn `[GIẢ ĐỊNH]`.

**1.y Glossary** (nếu ngắn; dài thì đưa lên đầu mục 6): chỉ thuật ngữ dễ hiểu sai; trạng thái viết `CODE_STYLE`; một khái niệm một tên.

| Thuật ngữ | Định nghĩa một câu (có đơn vị / trạng thái) |
|---|---|
| Hold | [Yêu cầu giữ 1..N HoldLine (SKU + qty) cho một khách trong TTL] |
| TTL | [.. phút, tính từ `created_at`] |

### Mục 2. Item trên màn hình 画面項目仕様 — ~630 token
Chia theo khu vực (header / danh sách sản phẩm / khối cọc / footer nút). **Bắt buộc có cột khác biệt login vs guest.** Text hiển thị ghi nguyên văn trong ngoặc kép.

| No | 項目名 (Item) | コントロール | I/O | 必須 | 備考 (mặc định, giới hạn, format, placeholder, hiển thị/ẩn/disable, login vs guest) |
|---|---|---|---|---|---|
| 1 | [Số lượng] | [number input] | I | ✓ | [mặc định 1; 1..≤ qty tồn; disable khi ATP = 0; guest: tối đa [..]] |
| 2 | [Nút "Giữ hàng"] | [button] | — | — | [disable khi ATP < qty HOẶC khách đã có [..] hold ACTIVE; text "Giữ hàng"] |

Ô không có dữ liệu từ RTM → ghi `[GIẢ ĐỊNH — ..]`, không để trống.

### Mục 3. Event イベント仕様 — ~360 token

| No | Event | Trigger | Xử lý | Ghi chú |
|---|---|---|---|---|
| 1 | Mở màn hình | [load] | [lấy ATP theo SKU từ [..]; lấy hold ACTIVE của khách] | [guest: không lấy hold] |
| 2 | Bấm "Giữ hàng" | [click] | [gọi API tạo hold (mục 9); chạy validation mục 4] | [chặn double-click: idempotency 0.10] |
| 3 | Đổi [dropdown] | [change] | [tính lại [..]] | [..] |

### Mục 4. Validation & message lỗi バリデーション仕様 — ~540 token
**Message ghi đúng từng chữ** — Executor sẽ bị hỏi "hiện message gì". Có cả rule nghiệp vụ (vượt tồn, hết hạn, trạng thái không hợp lệ), không chỉ rule format.

| No | FE/BE | Item | Nội dung check | Message |
|---|---|---|---|---|
| 1 | Cả hai | Số lượng | [1 ≤ qty ≤ ATP] | "[Số lượng vượt tồn khả dụng (còn {n}).]" |
| 2 | BE | Hold | [trạng thái phải ACTIVE] | "[Yêu cầu giữ hàng đã hết hạn.]" |
| 3 | FE | [SĐT] | [format [..]] | "[..]" |

### Mục 5. Design / Wireframe デザイン — ~90 token
Có link Figma/ảnh thì dán. Không có → vẽ **Mermaid `block-beta`** (hoặc `flowchart TD` nếu cần an toàn render) theo mẫu knowledge/32 §7.1, kèm 1–2 câu chữ mô tả thứ tự khu vực từ trên xuống. Ba trạng thái (mặc định, lỗi, thành công) ghi bằng bảng ngắn, không vẽ ba sơ đồ. **Không bỏ trống mục.**

### Mục 6. Flow nghiệp vụ & quy tắc xử lý ビジネスロジック詳細 — ~1980 token
**Mục lớn nhất, nơi quyết định TRÚNG/TRƯỢT.** Chia thành từng LOGIC, mỗi logic một mục con. Danh sách logic tối thiểu cho miền giữ hàng:

`6.1 Hiển thị tồn khả dụng` · `6.2 Tạo hold` · `6.3 Chuyển trạng thái hold (state machine)` · `6.4 Gia hạn` · `6.5 Hủy` · `6.6 Hết hạn (job)` · `6.7 Chuyển thành đơn` · `6.8 Tranh chấp đồng thời` · `6.9 Gửi thông báo/mail`

Mỗi logic viết đủ **ba thứ** (knowledge/32 §2):

**(1) Bảng step**

| Step | Ai | Làm gì | Hệ thống phản hồi |
|---|---|---|---|
| 1 | [Khách đăng nhập] | [gửi yêu cầu tạo hold] | [kiểm ATP, mục 4 validation] |
| 2 | [Hệ thống] | [tạo hold ACTIVE, Reserved += qty] | [hiện "[..]", gửi mail BR-..] |

**(2)(3) Bảng Case · Điều kiện · Kết quả mong đợi** — mỗi case đủ **5 gạch bắt buộc**: số · toán tử `>` hay `≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng.

| Case | Điều kiện | Kết quả mong đợi (hiển thị · trạng thái nút · lưu gì · mail · message · kho · tiền) | Mã | RTM |
|---|---|---|---|---|
| Bình thường | [ATP ≥ qty ≥ 1] | [tạo hold ACTIVE; Reserved +qty; nút → "Xem hold"; mail X; message "[..]"] | BR-01 | A-.. |
| Biên | [ATP = qty] | [..] | BR-02 | A-.. |
| Biên | [đúng mốc `expires_at` (Asia/Ho_Chi_Minh), khoảng `[đầu, cuối)`] | [..] | BR-03 | A-.. |
| Lỗi | [ATP < qty] | [từ chối; giữ nguyên kho; message "[..]"; nút giữ nguyên disable] | BR-04 | A-.. |
| Ưu tiên | [hai case cùng đúng] | [áp dụng [..] trước, theo 0.6] | BR-05 | A-.. |

Logic `6.3` dùng bảng state × event (điền 100% ô — `→ ĐÍCH (BR-xx)` / `Từ chối 0.5` / `KHL`); mẫu bảng và mã hiệu ứng K/T/N/L: knowledge/31 §3. Kèm **Mermaid `stateDiagram-v2`** (knowledge/32 §7.2) cho trực quan — nhưng sơ đồ KHÔNG thay bảng: ô "Từ chối 0.5" và "KHL" chỉ có trong bảng, mà đó mới là chỗ Executor đoán sai. Hết chỗ thì bỏ sơ đồ, giữ bảng. Logic có ≥ 2 điều kiện → decision table hit policy U, đếm tổ hợp = tích, ô không hợp lệ ghi "Từ chối 0.5" (knowledge/31 §1–§2).

Cuối mục 6: **sequence diagram** cho flow end-to-end (mô tả bằng chữ dạng `Khách → FE → BE → [hệ thống kho]` nếu không vẽ được).

### Mục 7. Ràng buộc, ca bất thường liên logic, điều chưa chốt 制約・イレギュラー・確認中 — ~630 token
Viết SAU khi từng logic ở mục 6 đã có case riêng. Ba–bốn bảng ngắn:

**7.1 Ràng buộc** — kỹ thuật/nghiệp vụ + cách giảm nhẹ.

**7.2 Ca bất thường cắt ngang nhiều logic** (BTC nêu đích danh): dữ liệu đổi giữa lúc hiển thị và lúc submit · gửi trùng · mở link hai lần · mail fail sau khi đã lưu. Cộng thêm miền giữ hàng: hai khách tranh SKU cuối · kho điều chỉnh khi đang hold · cọc thất bại · tài khoản bị khóa · SKU ngừng bán · hết hạn đúng lúc chuyển đơn.

| Mã EX-xx | Tình huống | Điều kiện phát hiện | Hành vi hệ thống | Trạng thái sau | Kho / Tiền | Message |
|---|---|---|---|---|---|---|

**7.3 Lỗi hệ thống chung** — log gì, alert ai, message chung cho user.

**7.4 Điều đang chờ xác nhận** — **một bảng riêng, không rải trong bài**:

| # | Điều chưa chốt | Giả định hiện tại đang dùng | Ảnh hưởng mục |
|---|---|---|---|

### Mục 8. Xác thực & phân quyền 認証・認可 — ~270 token
Bảng 1 — actor: ✓ / ✗ / ✓* (có điều kiện, ghi mã). Ô không ✓ = ✗ theo 0.4. **Bắt buộc có Guest và System/Job.** Phải phủ cả thao tác chỉ-đọc (xem hold của mình / mọi hold, xem audit log).

| Hành động | Guest | Member | Staff/CS | Admin | System/Job |
|---|---|---|---|---|---|
| Tạo hold | [..] | [..] | [..] | [..] | ✗ |
| Xem hold của mình | [..] | [..] | [..] | [..] | — |

Bảng 2 — xác thực: mỗi actor xác thực bằng gì; giữa các hệ thống, đường truyền nào xác thực bằng gì.

### Mục 9. Luồng dữ liệu & API データ連携・API仕様 — ~270 token
Sơ đồ hệ thống bằng **Mermaid `flowchart LR`** (knowledge/32 §7.4) + bảng timing. Chi tiết khi nào gọi / gửi gì / thất bại làm gì nằm ở bảng, không nhồi vào sơ đồ:

| Khi nào | Từ → Tới | Gửi/nhận dữ liệu | Field điều kiện | Thất bại thì làm gì (retry, message, log, alert, gửi tay) |
|---|---|---|---|---|

Mỗi API: trigger · payload · response thành công / thất bại làm gì.

### Mục 10. Data model, performance, security 技術仕様 — ~270 token
Bảng field (tên, ý nghĩa, bắt buộc, giới hạn, khi nào null, field server tự tính) · mục tiêu tốc độ + giới hạn API (**ghi rõ số nào là giả định**) · bảo mật (mã hóa đường truyền, quản lý key & thông tin cá nhân, log & alert) · tài liệu tham chiếu.

Audit: mọi chuyển trạng thái ghi `hold_id, from, to, actor, timestamp, reason` (0.7). Job hết hạn chạy mỗi [..] phút; sai lệch ≤ [..] giây. Idempotency: khóa = [..], hiệu lực [..] phút (0.10).

## 1b. Luật giả định = mặc định ngành, không sáng tạo

Với 5 câu hỏi (00 §A), **~70% ngân hàng câu hỏi không được hỏi** (bảng cuối 20 §3). Phần spec không có nguồn từ AI Khách hàng vẫn phải có luật. Quy tắc chọn giá trị:

1. **Điền bằng mặc định phổ biến của ngành**, lấy từ 31 phát biểu ở 20 §4 và catalogue ⚠ ở 10 §6. Gắn `[GIẢ ĐỊNH]` ở bản nội bộ.
2. **Không sáng tạo giá trị mới.** Lý do là số học, không phải khẩu hiệu: Executor mù khi gặp spec im lặng sẽ đoán theo mặc định ngành. Nếu specs thật = mặc định ngành thì viết mặc định ra → TRƯỢT (giữ điểm), im lặng → cũng TRƯỢT. Nếu specs thật ≠ mặc định ngành thì viết mặc định → TRÚNG, im lặng → cũng TRÚNG. **Viết mặc định ngành ra không làm xấu đi ca nào**, mà lại loại được đa nghĩa ở các ca ghép (hai luật cùng áp dụng, biên, thứ tự ưu tiên) — nơi im lặng khiến hai reader ra hai kết quả. Ngược lại, viết một giá trị *tự nghĩ ra* (khác cả mặc định ngành lẫn specs thật) là ca duy nhất tệ hơn im lặng: nó biến một TRƯỢT tiềm năng thành TRÚNG chắc chắn.
3. **Ba trường hợp không được tự điền, phải đi vào câu C5 hoặc dùng luật an toàn hai chiều (33 §3.3):** (a) luật làm hỏng mục tiêu brief (nhãn `⚡`, cổng F7); (b) hạn mức không cưỡng chế được (cổng F2); (c) giả định mà đảo lại thì đổi hướng tiền. Ba loại này là chỗ specs thật gần chắc có luật riêng, và đoán sai thì mất điểm ở đúng core flow.
4. **Xếp hạng mọi `[GIẢ ĐỊNH]`** theo ba tiêu chí ở `/spec-write` bước 14; 10 dòng đầu là nội dung câu C5.
5. **Nhãn `[GIẢ ĐỊNH]` trong bản nộp — theo yêu cầu BTC, không theo thói quen cũ.** Cấu trúc 10 mục yêu cầu **mục 7.4 gom "điều chưa chốt"** (knowledge/32), nên bản nộp *có* khai giả định — nhưng khai **tập trung ở bảng 7.4**, mỗi dòng kèm giá trị đang dùng. Trong thân luật ở mục 6 thì **bỏ nhãn**: với Executor, một luật mang chữ "giả định" là một luật yếu, mời nó tự suy diễn. Tức luật viết dứt khoát, còn việc "giá trị này chưa chốt" nằm ở 7.4.

Hệ quả cho cổng chất lượng: "mọi luật truy vết về RTM" không còn nghĩa là "mọi luật có `← A-xx`". Nó có nghĩa **mọi luật có một dòng RTM**, loại `A-xx` (có câu trả lời) hoặc `G-xx` (giả định có xếp hạng) — 20 §5.

## 2. KHUNG §0 CATCH-ALL [HD §5.5]

⚠ Khối này đặt tại **mục 1.x** của spec (knowledge/32 §3). Viết bản 1 lúc 11:00, rà lại 11:47 sau khi verify. Catch-all lệch specs thật = tự tạo mâu thuẫn (specs thật: "hết hạn → tự hủy, hoàn cọc" nhưng 0.5 nói "từ chối, giữ nguyên") [HD §5.5].

| # | Dòng luật | Verify? (lượt / giờ) | Câu hỏi verify |
|---|---|---|---|
| 0.1 | Tài liệu này là nguồn duy nhất. Không suy diễn từ thông lệ ngành hay hệ thống khác. | Không cần | — |
| 0.2 | Mọi mốc thời gian theo [TZ], đơn vị phút, giờ đồng hồ liên tục (gồm ngày lễ, ngoài giờ làm việc) trừ khi luật cụ thể nói khác. | [..] | "TTL tính giờ liên tục hay giờ làm việc? Ngày lễ?" |
| 0.3 | Khoảng thời gian là [bắt đầu, kết thúc): gồm mốc đầu, không gồm mốc cuối. | [..] | "Đúng phút thứ [TTL], hold còn hiệu lực không?" |
| 0.4 | Hành động không được cấp quyền tường minh tại mục 8 → actor KHÔNG ĐƯỢC thực hiện. | [..] | "Liệt kê actor và hành động được phép." |
| 0.5 | Tình huống không khớp luật nào → hệ thống PHẢI từ chối, giữ nguyên trạng thái, không đổi tồn kho, không thu/hoàn tiền, ghi log lý do. | [..] | "Yêu cầu không thuộc trường hợp nào đã mô tả, hệ thống làm gì?" |
| 0.6 | Hai luật cùng áp dụng: [an toàn tồn kho > nghĩa vụ tài chính đã phát sinh > cam kết với khách > tối ưu vận hành]. | [..] | "Kho bị điều chỉnh dưới Reserved khi hold có cọc đang ACTIVE: giữ hold hay giữ kho?" |
| 0.7 | Mọi thay đổi trạng thái hold PHẢI cập nhật tồn kho theo logic 6.1 và ghi audit theo mục 10, kể cả khi luật cụ thể không nhắc. | Không cần | — |
| 0.8 | Xung đột đồng thời trên cùng tồn kho: xử lý theo thời điểm server tiếp nhận (FCFS). | [..] | "Hai khách giữ SKU cuối cùng giây, ai được?" |
| 0.9 | Nghiệp vụ tại mục 1 NGOÀI PHẠM VI không thuộc tài liệu này. | [..] | "Nghiệp vụ nào KHÔNG thuộc tính năng này?" |
| 0.10 | Request trùng cùng khóa idempotency → trả kết quả lần đầu, không tạo hold mới. | [..] | "Bấm giữ hàng 2 lần liên tiếp cùng SKU: 1 hay 2 hold?" |
| 0.11 | Khử trùng sự kiện từ cổng thanh toán theo **mã giao dịch của cổng**, không theo thời điểm nhận; sự kiện đến sai thứ tự → lấy kết quả sau cùng theo mã giao dịch. | [..] | "Cổng báo trùng: ghi 1 hay 2 lần? Sai thứ tự thì lấy kết quả nào?" (N0-09) |
| 0.12 | Mốc hệ thống **quyết định** (thu, hoàn, đổi trạng thái) khác mốc bên ngoài **hoàn tất**. Mọi nghĩa vụ tiền có hai mốc và một trường trạng thái riêng. | [..] | "Hoàn cọc: khởi tạo sau ? phút, tiền về sau tối đa ? ngày làm việc?" (N0-05) |
| 0.13 | Hạn mức và định danh neo vào [đơn vị định danh]; yêu cầu không kèm đơn vị định danh hợp lệ PHẢI bị từ chối. | [..] | "Hạn mức tính theo tài khoản / SĐT đã OTP / thiết bị / thẻ? Chọn 1." (N0-02) |
| 0.14 | Thất bại của kênh thông báo KHÔNG làm đổi trạng thái hold, `expires_at`, hay nghĩa vụ hoàn tiền. | Không cần | — |
| 0.15 | *(chỉ khi spec có sơ đồ mermaid)* Sơ đồ chỉ minh họa; khi sơ đồ và bảng hoặc luật có mã chỏi nhau, bảng và luật có mã thắng. | Không cần | — |

**0.11–0.14 là bốn dòng lãi nhất của cả spec:** ~90 từ phủ **8/12 kịch bản suy biến bắt buộc** (05 §M6) — tỷ lệ chắn/token tốt nhất trong kit. Không cắt bốn dòng này khi thiếu chỗ (33 §3.2). **0.15 bắt buộc có nếu spec dùng sơ đồ Mermaid** (knowledge/32 §7): thiếu nó thì mỗi sơ đồ là một mâu thuẫn nội tại (lỗ hổng #5) và Executor có hai cách đọc hợp lệ.

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

## 4. CHECKLIST 24 QUY TẮC VIẾT REQUIREMENT TỐT — 1–17 kiểm *cách viết*, 18–24 kiểm *nội dung*

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
| 13 | **Chỉ áp dụng cho mục 6–10:** WHAT không HOW trong luật nghiệp vụ. **Mục 2–5 BẮT BUỘC mô tả UI** (control, placeholder, disable, text nguyên văn) — BTC yêu cầu. Điều chưa chốt KHÔNG bỏ nữa: đưa vào bảng mục 7.4 kèm giả định hiện tại | Luật (mục 6): "Sale bấm nút Hủy" → "KHI Sale gửi yêu cầu hủy hold ACTIVE, hệ thống PHẢI ...". Item (mục 2): giữ nguyên "nút Hủy, disable khi ..." |
| 14 | Sự kiện không mong muốn có luật riêng: lỗi thanh toán, kho âm, request trùng, hết hạn đúng lúc chuyển đơn | Chỉ luồng chính → thêm EX-01..EX-07 |
| 15 | Kết quả xác định đủ 7 chiều: trạng thái cuối + kho + tiền + thông báo + **hiển thị gì** + **trạng thái nút** + **message** (knowledge/32 §2) | "Hold bị hủy" → "→ CANCELLED_STAFF; Reserved −qty; hoàn 100% cọc ≤ 24 giờ; email → khách" |
| 16 | Không ô trống trong bảng; điều kiện phải kiểm chứng được bằng dữ liệu trong spec | "gia hạn nếu cần" → "gia hạn ≤ 1 lần, +60 phút, chỉ khi còn ≥ 10 phút trước `expires_at`" |
| 17 | Giả định ngầm viết thành luật; khẳng định trước, không phủ định kép | "(ai cũng hiểu 1 hold nhiều SKU)" → "BR-12: một hold chứa 1..N HoldLine; mỗi SKU ≤ 1 HoldLine" |

### Bảy quy tắc chất-lượng-nội-dung (18–24) — ISO/IEC/IEEE 29148 `feasible` + `affordable`

Quy tắc 1–17 kiểm *cách viết*. Bảy quy tắc dưới kiểm *điều được viết có đứng được trong thực tế và có phục vụ mục tiêu không*. Chi tiết cách chấm: knowledge/33 §1 (cổng F). Chạy lúc 11:00, **trước** khi chọn phát biểu cho câu C5 — hit ở quy tắc 18, 19, 20 là ứng viên hàng đầu của C5.

| # | Quy tắc | Trước → Sau |
|---|---|---|
| 18 | **Không hứa hộ bên ngoài.** Mốc "hoàn tất" của việc do cổng/ngân hàng/ERP thực hiện phải tách khỏi mốc "quyết định" của hệ thống | "hoàn cọc hoàn tất ≤ 24 giờ" → "khởi tạo hoàn ≤ 5 phút, `refund_status = ĐANG_HOÀN`; cổng xác nhận → `ĐÃ_HOÀN`; quá [N] ngày làm việc chưa xác nhận → `HOÀN_THẤT_BẠI` + [phương án 2]" |
| 19 | **Hạn mức phải cưỡng chế được.** Neo vào dữ liệu khách tự đặt được = hạn mức trang trí | "hạn mức theo email khai lúc cọc" → "hạn mức theo [đơn vị định danh]; yêu cầu không có đơn vị định danh hợp lệ bị từ chối `IDENTITY_REQUIRED`" |
| 20 | **Mỗi luật phục vụ một mục tiêu.** Luật ở cột 3 bảng Mục tiêu↔Luật (05 §2) = luật tự phá mục tiêu → hỏi lại, không tự chốt | "guest tạo hold + khóa tồn trước khi thu tiền + hủy hoàn 100% phí 0" trong khi mục tiêu là chống bot → hỏi N0-02, N0-03, N0-04 trước khi viết |
| 21 | **Mỗi phụ thuộc ngoài có một luật cho chế độ lỗi của nó.** Sáu láng giềng (05 §M4) → sáu luật | im lặng về cổng timeout → "NẾU cổng không xác nhận trong hạn trả cọc, THÌ → CANCELLED (HETHONG), K+, T0" |
| 22 | **Không coi việc giữ tài nguyên là chắc chắn thành công.** Kiểm điều kiện xong vẫn có thể ghi thất bại [IBM; Shopify] | "`ATP ≥ q` khi nhận yêu cầu → tạo hold" → "+ NẾU ghi giữ thất bại do tồn đã đổi giữa lúc kiểm và lúc ghi, THÌ `OUT_OF_STOCK`, K0 T0, không giữ một phần, không tự thử lại" |
| 23 | **Việc giao cho người phải quan sát được và có trần.** Mỗi `CSKH`/`Admin`/`thủ công` cần một dòng ai được báo + trường audit + trần số lượng hoặc điều kiện | "hệ thống hủy hold đã cọc khi kho lệch" → "+ báo CSKH phụ trách, ghi lý do vào audit, và nêu trần / thứ tự hủy tường minh" |
| 24 | **Nói ai chịu chi phí.** Hoàn 100%, thao tác thủ công, thông báo, tồn bị khóa đều có giá | "hoàn 100%, không thu phí hủy" → "+ phí cổng của khoản hoàn do [ai] chịu" hoặc đưa vào NGOÀI phạm vi tường minh |

## 5. KỸ THUẬT TIẾT KIỆM TOKEN [HD §4.7]

**Chọn dạng mục 6 TRƯỚC khi gõ chữ đầu tiên — theo tổng số dòng RTM (`A-xx` + `G-xx`):**

| Số dòng RTM | Dạng mục 6 | Lý do |
|---|---|---|
| < 40 | bảng 5 cột (Mã · Luật · K/T/N/L · KHÔNG ĐƯỢC · RTM) | đủ ngân sách, dễ soi ô trống |
| ≥ 40 | **danh sách** `**BR-xx** — luật. Cấm: … ← A-yy` | mỗi hàng bảng 5 cột tốn ~6 token chỉ riêng dấu `\|`; 50 BR = ~300 token dấu bảng |

Đếm bằng token đổi vài kết luận so với bản đếm từ:

- **Dấu bảng đắt hơn tưởng.** `|` là một token. Bảng chỉ đáng dùng khi có ≥3 cột nội dung thật; bảng 2 cột luôn nên viết thành danh sách `**Khóa** — giá trị`.
- **Ký hiệu và mã rẻ.** `→`, `≤`, `[a,b)`, `BR-07`, `ATP` mỗi cái 1–3 token, thay được cả cụm tiếng Việt 5–8 từ (12–20 token). Khai báo một lần ở mục 1.x rồi dùng khắp spec là kỹ thuật lãi nhất.
- **Tiếng Việt đắt hơn mã.** Cùng một ý, viết bằng công thức `Reserved += qty` (~6 token) thay vì "hệ thống phải tăng số lượng đang giữ thêm đúng số lượng khách yêu cầu" (~35 token).
- **Số và đơn vị viết liền:** `120 phút` (3 token) thay "một trăm hai mươi phút".

**Với đề ≥ 40 dòng RTM, dồn tác động phụ thành luật chung ở 0.7 (mục 1.x)** — "mọi chuyển trạng thái cập nhật tồn theo logic 6.1, ghi audit theo mục 10, gửi thông báo theo mục 6, kể cả khi luật cụ thể không nhắc" — thay vì lặp cột K/T/N/L trong từng BR. Tiết kiệm ~1.700 token trên 50 BR mà **không mất** thành phần (4) của §3.2, vì 0.7 là căn cứ hợp lệ cho Executor.

Số đo thực tế (diễn tập 08/09, đề "đặt cọc giữ hàng", 111 dòng ⚠): mục 6 viết đúng template bảng 5 cột tốn **2.235 từ ≈ 5.600 token** — tức một mục ăn hết hạn mức 6.000 của cả spec. Chọn dạng danh sách + §0.7 từ đầu là điều kiện cần để vào được 5.400 token, không phải tối ưu về sau.


- Tập luật cùng cấu trúc → một bảng; luật tổng quát + ngoại lệ ("Mọi terminal → Từ chối 0.5, trừ BR-xx") thay liệt kê.
- "áp dụng BR-07" thay lặp nội dung; bảng step chỉ trỏ mã, không chép lại luật.
- Công thức (`ATP = OnHand − Reserved − Safety`) thay câu văn.
- Ký hiệu `[a, b)`, `→ STATE (BR-xx)`, ✓/✗/✓*, ±qty, KHL khai báo một lần ở mục 1.x hoặc đầu bảng — Executor không đoán ký hiệu lạ.
- Không "vì sao", không mở bài. (Mô tả màn hình KHÔNG cắt — mục 2, 5 bắt buộc có.)
- Sơ đồ Mermaid tốn ~180–220 từ cho cả 4 cái (knowledge/32 §7.5). Quá từ thì cắt theo thứ tự wireframe → sơ đồ hệ thống → sequence → state machine, mỗi lần thay bằng 1–2 câu chữ. **Không cắt bảng đi kèm** — bảng chở luật, sơ đồ chỉ chở trực quan.

## 6. THỨ TỰ VIẾT DƯỚI ÁP LỰC 10:20–11:52 [HD §4.9]

Viết theo thứ tự **ăn điểm**, không theo thứ tự số mục. Lịch này dựa trên tham số 09/09: C1–C4 gửi xong trước 10:20 (20 §3), nên có **45 phút viết trước khi restate** — và câu C5 vì thế nhắm đúng giả định *đã thật sự vào spec*, thay vì nhắm vào kế hoạch soạn hôm trước.

| Phút | Việc | Đầu ra |
|---|---|---|
| 10:20–10:30 | Chốt RTM (dòng `A-xx` đã hỏi + sinh dòng `G-xx` giả định, 20 §5) + mục 1 (phạm vi TRONG/NGOÀI + catch-all 1.x, **đủ 0.1–0.15**) | RTM đủ cột "Mã BR"; mục 1 xong |
| 10:30–10:55 | **Mục 6** — liệt kê danh sách logic, dòng ⚠ RTM vào logic tương ứng; mỗi logic: bảng step + bảng Case (đủ 5 gạch: số, toán tử, múi giờ, default khi config trống, ưu tiên). Chọn dạng bảng hay danh sách theo §5 **trước khi gõ** | ≥ 15 BR trong các bảng Case |
| 10:55–11:00 | **Mục 6.3** state machine: bảng state × event, 100% ô | Bảng đầy |
| 11:00–11:05 | **Mục 4** validation & message (message nguyên văn) + **mục 8** actor & quyền (có Guest, System/Job) | 2 bảng đầy |
| 11:05–11:15 | **Mục 2** item màn hình + **mục 3** event + **mục 7** (7.2 bất thường liên logic, 7.4 chưa chốt) | Đủ bảng |
| 11:15–11:20 | **Mục 9, 10, 5** gọn | Đủ 10 mục |
| 11:20–11:30 | **Xếp hạng rủi ro giả định** (`/spec-write` bước 14) → 8–10 phát biểu Đúng/Sai cho **câu C5**; gửi C5 | Danh sách phát biểu đã gửi |
| 11:30–11:40 | Vá spec theo mọi ý C5 trả lời "Sai"; dòng `G-xx` liên quan chuyển thành `A-xx` | Spec khớp câu trả lời cuối |
| 11:40–11:46 | **Cổng F** (33 §1) + **bảng Mục tiêu↔Luật** (05 §2): sửa ✗ F1/F5 bằng viết lại; ✗ F2/F7 thì dùng luật an toàn hai chiều (33 §3.3) | 0 ✗ ở F1/F5; mọi mục tiêu có luật |
| 11:46–11:49 | Rà mục 1.x catch-all: đối chiếu từng dòng với log; red team: eval set **hai reader**, Ctrl+F danh sách đen knowledge/40 §2 (22 nhóm) | Lỗ hổng đã vá |
| 11:49–11:52 | Đếm **token** bản nộp (`LC_ALL=C.UTF-8 wc -w -m`, công thức 00 §A1), đích ≤5.400; cắt theo knowledge/32 §6; nộp; lưu bản copy | Đã nộp trước 11:52 |

Cạn giờ → cắt theo thứ tự: **BR không thuộc mục tiêu nào** (05 §2) → nhóm lint 22 (câu nói về chính spec) → **10 → 9 → 5 → 3 → 2**. KHÔNG cắt **1, 6, 7, 4, 8**, và **không cắt 0.11–0.14**.

Phân công: A mục 6 (logic chính) ; B mục 2, 3, 4, 8, 9 ; C RTM, mục 1 + 7, xếp hạng giả định cho C5, cổng F, red team.

## 7. CỔNG CHẤT LƯỢNG TRƯỚC NỘP [HD §5.9]

Đánh ✓ từng dòng; một dòng chưa ✓ = sửa trước khi nộp. **Dòng 1–18 kiểm hình thức và cấu trúc 10 mục; dòng 19–23 kiểm nội dung** (knowledge/05, knowledge/33) — spec đạt hết 1–18 vẫn có thể sai bài toán.

| # | Kiểm tra |
|---|---|
| 1 | Đủ **10 mục** BTC, không mục nào trống (mục 5 ít nhất có mô tả bố cục bằng chữ)? |
| 2 | Mọi dòng ⚠ trong RTM đã map tới một mã BR/EX trong mục 6 hoặc 7? |
| 3 | Mọi bảng Case ở mục 6 có **đủ 3 loại case: bình thường, biên, lỗi**? |
| 4 | Mỗi bảng Case đủ **5 gạch** (số · toán tử `>`/`≥` · múi giờ · default khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng)? |
| 5 | Cột "Kết quả mong đợi" đủ 7 chiều (trạng thái cuối, kho, tiền, thông báo, hiển thị, trạng thái nút, message)? |
| 6 | Mục 4: message lỗi ghi **nguyên văn từng chữ**, có cột FE/BE? |
| 7 | Mục 2: có cột khác biệt **login vs guest**; có điều kiện hiển thị/ẩn/disable? |
| 8 | Mục 8: bảng quyền có **Guest và System/Job**, phủ cả thao tác chỉ-đọc? |
| 9 | Mục 6.3: mọi ô bảng state × event đã điền, kể cả "Từ chối 0.5" / "KHL"? |
| 10 | Mục 7.2 có ≥ 4 ca bất thường BTC nêu đích danh (dữ liệu đổi giữa hiển thị và submit, gửi trùng, mở link hai lần, mail fail sau khi đã lưu)? |
| 11 | Mục 7.4 gom **một bảng riêng** điều chưa chốt kèm giả định — không rải `[GIẢ ĐỊNH]` rải rác không khai báo? |
| 12 | Mục 1.x có catch-all: 0.6 ưu tiên xung đột, 0.2 timezone, 0.3 `[đầu, cuối)`, 0.5 tình huống không khớp luật nào — đã verify? |
| 13 | Đã Ctrl+F danh sách đen knowledge/40 §2 (**22 nhóm** — 1–16 mơ hồ + 17–22 nội dung) + §3 **S1–S39** (S22–S30 = cấu trúc 10 mục, S31–S39 = nội dung); ký hiệu lạ đã khai báo? |
| 14 | Không tham chiếu tài liệu ngoài; không đại từ trôi? |
| 15 | Mã BR/EX không trùng; mọi "áp dụng BR-xx" trỏ tới mã tồn tại? |
| 16 | Eval set đã chạy bằng **hai reader mù độc lập**: không tình huống nào hai reader ra kết quả khác nhau? (Reader tự khai "không mơ hồ" **không tính** — đó là vòng tự xác nhận.) |
| 17 | Mọi sơ đồ Mermaid có kèm 1–2 câu chữ tóm tắt (phòng khi render lỗi), nhãn có dấu đã bọc ngoặc kép? |
| 18 | Đếm **token** trên bản nộp ≤ 6.000, đích 5.400 (`LC_ALL=C.UTF-8 wc -w -m`; công thức 00 §A1) và đã lưu bản copy cho kháng nghị? |
| 19 | **Cổng F** (33 §1) đã chấm cho mọi BR/EX: không còn ✗ ở F1, F2, F5, F7? |
| 20 | **Bảng Mục tiêu↔Luật** (05 §2) đã lập: không mục tiêu brief nào có cột 2 trống; mọi ô cột 3 đã hỏi hoặc đã xử lý? |
| 21 | **12 kịch bản suy biến** (05 §M6) đều có luật bao trùm, hoặc nằm trong NGOÀI phạm vi tường minh? |
| 22 | Mọi nghĩa vụ tiền đã **tách hai mốc** (hệ thống khởi tạo / bên ngoài hoàn tất) và có luật cho ca **hoàn tiền thất bại**? |
| 23 | Bản nộp **không chứa câu nói về chính nó** (nhóm lint 22: "đã điền đủ 36/36 ô", "phân hoạch không chồng lấn") — chuyển sang `review.md`? |
