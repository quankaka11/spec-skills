*Dùng cho người rà spec (reviewer) của đội: Ctrl+F spec MÌNH lúc 11:45 trước khi nộp, và Ctrl+F spec ĐỐI THỦ lúc 13:00 để tìm chỗ bắn.*

# 40 — Danh sách đen từ mơ hồ & kiểm tra cấu trúc (ambiguity lint)

Executor chỉ đọc spec [HD §2.1 ①]; câu cho phép ≥2 cách đọc → Executor chọn cách phổ biến; specs thật khác cách phổ biến → TRÚNG [HD §2.2]. Lint này tìm câu cho phép ≥2 cách đọc.

## 1. Taxonomy mơ hồ (Berry–Kamsties–Krieger)

| # | Loại | Dấu hiệu nhận diện | Ví dụ mơ hồ trong miền giữ hàng | Cách viết lại |
|---|---|---|---|---|
| 1 | Từ vựng (lexical: homonymy/polysemy) — một từ hai nghĩa; quá-trình vs kết-quả; đơn-vị vs loại | Danh từ nghiệp vụ không có trong bảng thuật ngữ; "đơn", "hủy", "kho", "sản phẩm", "hết hạn" dùng cho >1 thứ | "Khi đơn bị hủy, hàng trả về kho." | "Khi phiếu giữ (hold) chuyển sang CANCELLED hoặc EXPIRED, hệ thống cộng số lượng đã giữ vào tồn kho khả dụng (available stock) của SKU đó." |
| 2 | Cú pháp — gắn kết (attachment) | Cụm bổ nghĩa ("một lần", "trong 15 phút", "của khách VIP") đứng cuối câu, có thể gắn vào 2 danh/động từ | "Khách có thể gia hạn phiếu giữ sắp hết hạn một lần." | "Mỗi hold được gia hạn tối đa 1 lần trong vòng đời; chỉ khi thời gian còn lại ≤ 15 phút." |
| 3 | Cú pháp — phối hợp (coordination) "và"/"hoặc" cùng câu | Câu có ≥2 liên từ "và"/"hoặc"/"và/hoặc"; một tính từ đứng trước 2 danh từ ("khách mới và VIP") | "Hold bị hủy nếu khách không thanh toán và hết hạn hoặc tồn kho về 0." | Tách danh sách: "Hold chuyển EXPIRED khi (1) TTL hết; chuyển CANCELLED khi (2) khách bấm Hủy. Tồn kho về 0 KHÔNG ảnh hưởng hold ACTIVE." |
| 4 | Cú pháp — tỉnh lược (ellipsis) / so sánh không mốc | "hơn", "lâu hơn", "ưu tiên hơn", "khác với" mà thiếu vế sau | "Khách VIP được giữ lâu hơn khách thường." | "TTL: VIP = 240 phút; Thường = 120 phút; tính từ thời điểm hold ACTIVE." |
| 5 | Ngữ nghĩa — phạm vi lượng từ "tất cả/mỗi/mọi/một/các" | Số nhiều + "một"; "tất cả X có một Y" (Y chung hay riêng?) | "Tất cả khách hàng có một hạn mức giữ hàng." | "Mỗi tài khoản có hạn mức riêng: tối đa 3 hold ACTIVE đồng thời, tính trên toàn tài khoản, không theo SKU." |
| 6 | Ngữ nghĩa — phạm vi phủ định "không … vì/khi/và" | "không" đứng trước động từ rồi theo sau là "vì", "khi", "và" | "Hệ thống không hủy hold vì tồn kho thay đổi." | "Thay đổi tồn kho KHÔNG phải nguyên nhân hủy hold. Hold ACTIVE chỉ đổi trạng thái theo BR-07." |
| 7 | Ngữ nghĩa — "chỉ/duy nhất/cũng" đặt sai chỗ | "chỉ" đứng trước động từ thay vì trước từ bị giới hạn | "Khách chỉ được gia hạn hold 1 lần." (chỉ khách? chỉ gia hạn? chỉ 1 lần?) | "Mỗi hold được gia hạn tối đa 1 lần." / "Chỉ tài khoản VIP được gia hạn." |
| 8 | Ngữ nghĩa — "ngược lại/nếu không/trường hợp còn lại" treo (otherwise) | Chuỗi ≥2 "Nếu … thì …" rồi "Ngược lại" | "Nếu còn hàng thì tạo hold. Nếu là VIP thì TTL 240. Ngược lại từ chối." | Bảng quyết định (decision table) đủ 4 tổ hợp còn-hàng × VIP; mỗi ô một kết quả. |
| 9 | Tham chiếu — đại từ trôi (anaphora) "nó/này/đó/chúng" | Đại từ có ≥2 danh từ ứng viên phía trước; "Điều này" mở đầu câu | "Khi đơn hàng được tạo từ hold, nó bị xóa." | "Khi đơn hàng được tạo từ hold, hold chuyển sang CONVERTED; đơn hàng giữ nguyên." |
| 10 | Ngữ dụng — trực chỉ (deixis) thời gian/nơi chốn | "hiện tại", "bây giờ", "trước đó", "sau đó", "tiếp theo", "đến 17:00", "đến hết ngày" | "Hold có hiệu lực đến hết ngày." | "Hold hiệu lực trong [T0, T0 + 120 phút); T0 = thời điểm tạo, giờ máy chủ UTC+7; giây 7199 còn, giây 7200 đã hết." |
| 11 | Mờ (vagueness) & bất định (uncertainty) | Tính từ/trạng từ không đo được; "có thể", "thường", "v.v." | "Hệ thống thông báo kịp thời trước khi hold hết hạn; khách có thể được hoàn tiền." | "Hệ thống gửi push đúng 1 lần tại T_hết hạn − 15 phút. Hold không thu tiền nên KHÔNG có hoàn tiền." |
| 12 | Lỗi ngôn ngữ (language error) số ít/nhiều, mạo từ | "các X có Y của chúng", "một X" dùng như "mỗi X" | "Các hold có thời hạn của chúng." | "Mỗi hold có đúng một TTL riêng, gán tại lúc tạo, không đổi khi gia hạn." |

Taxonomy theo Berry–Kamsties–Krieger §3.3, §5.

## 2. Danh sách đen phẳng (Ctrl+F / grep)

Cách dùng: mỗi dòng đã ở dạng regex không khoảng trắng — dán thẳng vào ô tìm kiếm (bật Regex), hoặc Ctrl+F từng cụm. Mỗi hit = 1 dòng trong báo cáo lint (mục 4).

1. **Định lượng mờ** [HD §4.6][Berry §5.1]: `(nhanh chóng|kịp thời|ngay lập tức|nhanh|ngay|ngay khi|sớm|sớm nhất|một khoảng thời gian|đủ lâu|hợp lý|định kỳ|thường xuyên|tạm thời|trong thời gian ngắn|lâu|immediately|quickly|timely|periodically|reasonable|shortly|soon)`
2. **Số lượng mờ** [HD §4.6][Berry §5.2.3]: `(nhiều|một số|vài|phần lớn|đa số|ít|tối đa cho phép|đủ|không quá nhiều|một lượng|hàng loạt|many|few|some|several|most|sufficient|enough|a number of)`
3. **Tùy chọn / modal yếu** [HD §4.6][Berry §5.1]: `(có thể|có lẽ|nên|thường|tùy trường hợp|tùy tình huống|được khuyến nghị|mong muốn|dự kiến|hỗ trợ|may|might|should|could|usually|typically|optionally|possibly|probably|support)`
4. **Bỏ ngỏ / bất định** [HD §4.6][NASA App.C][Berry §5.1]: `(v.v.|vv|…|và các trường hợp tương tự|như trên|tương tự|bao gồm nhưng không giới hạn|chẳng hạn|ví dụ như|và những thứ khác|etc|and so on|including but not limited to|such as|e.g.|TBD|TBR)`
5. **Chủ ngữ ẩn / bị động** [HD §4.6][NASA C.2]: `(được cập nhật|được hủy|sẽ bị hủy|được xử lý|được kiểm tra|được thông báo|được ghi nhận|được giải phóng|hệ thống xử lý|tự động|is updated|is cancelled|will be processed|is handled|is released|automatically)`
6. **Trực giác ngầm** [HD §4.6][HD §2.1 ①]: `(như thông lệ|theo chuẩn ngành|hiển nhiên|như bình thường|theo quy định chung|như thường lệ|mặc định|như các hệ thống khác|ai cũng biết|tương tự Shopee|as usual|standard practice|obviously|by default|as expected|industry standard)`
7. **Đại từ trôi** [HD §4.6][Berry §5.4.2–5.4.3]: `(nó|chúng|điều này|điều đó|cái đó|việc này|trạng thái này|trường hợp này|trường hợp đó|như vậy|ở đây|it|this|that|these|those|they|such|the former|the latter)`
8. **Điều kiện treo** [HD §4.6][NASA App.C]: `(nếu cần thiết|khi cần|khi phù hợp|trong trường hợp đặc biệt|tùy điều kiện|khi có yêu cầu|nếu được|khi thích hợp|trong một số trường hợp|ngược lại|nếu không|if necessary|when required|if required|as appropriate|where applicable|otherwise)`
9. **Toán tử mờ** [HD §4.6][Berry §5.1, §5.4.7]: `(và/hoặc|hoặc|và|trước/sau đó|đồng thời|cũng như|kèm theo|tối thiểu|tối đa|and/or|or|and|before/after|minimum|maximum|include)` — "hoặc": cả hai hay chỉ một? "và": đồng thời hay tuần tự?
10. **So sánh không mốc** [HD §4.6][Berry §5.5.2]: `(tốt hơn|nhanh hơn|lâu hơn|ưu tiên cao hơn|ít hơn|nhiều hơn|khác với|tương đương|cao|thấp|lớn|nhỏ|better|faster|higher priority|longer|different from|high|low|large|small)`
11. **Thiếu bao gồm/loại trừ** [HD §4.6][Berry §5.1, §5.7]: `(trong vòng|đến|tới|cho đến|đến hết|từ … đến …|trước|sau|tối đa|tối thiểu|lên đến|không quá|within|until|by|up to|before|after|between|at most|at least)`
12. **Từ NASA không kiểm chứng được** [NASA App.C C.4]: `(linh hoạt|dễ|dễ dùng|thân thiện|an toàn|đầy đủ|phù hợp|tối ưu|tối thiểu hóa|tối đa hóa|mạnh mẽ|hiệu quả|chấp nhận được|thiết yếu|trạng từ "-ly"|flexible|easy|sufficient|safe|adequate|accommodate|user-friendly|usable|appropriate|fast|robust|maximize|minimize|efficient|acceptable|handle|process|support)`
13. **Trực chỉ thời gian** [Berry §5.7]: `(hiện tại|bây giờ|trước đó|sau đó|tiếp theo|lúc đó|khi đó|hôm nay|cuối ngày)`
14. **Lượng từ / giới hạn** [Berry §5.2, §5.3]: `(chỉ|duy nhất|cũng|tất cả|mọi|mỗi|các [^.]{0,30} một|một số)`
15. **Phủ định có phạm vi** [Berry §5.4.5]: `không [^.]{0,40}(vì|khi|và|trừ)`
16. **Biện luận (cắt)** [30 §5]: `(vì|bởi vì|nhằm|để|mục đích|giúp|lý do)`

Ngoại lệ: "có thể" = "ĐƯỢC PHÉP" không đánh lỗi nếu đầu spec đã khai báo quy ước từ khóa PHẢI / KHÔNG ĐƯỢC / ĐƯỢC PHÉP [Berry §4.3].

### Sáu nhóm lint NỘI DUNG (17–22)

Nhóm 1–16 tìm câu **cho phép ≥2 cách đọc**. Sáu nhóm dưới tìm câu **chỉ có một cách đọc, và cách đó không đứng được trong thực tế** — loại lỗi mà lint cũ mù hoàn toàn (bằng chứng: `battle/spec.nop.md` ra 0 hit mức Cao ở nhóm 1–16 nhưng 7 ✗ ở cổng F, knowledge/32 §7). Nguồn: ISO/IEC/IEEE 29148 `feasible`/`affordable`; SPECS/DESIGNS WATCHLIST (McCann) mục *Oversimplification*, *Fudge*; HTSM *Operations: disfavored use*.

Mỗi hit ở nhóm 17–20 **phải** được chấm cổng F (knowledge/32 §1) trước khi kết luận — hit ở đây là *nghi vấn*, cổng F là *phán quyết*.

17. **Hứa hộ bên ngoài** (→ F1, F5): `(hoàn tất|tiền về|đã về tài khoản|đối soát xong|giao dịch xong|xong trong|hoàn thành trong|hoàn tiền trong|chuyển tiền trong) ?[^.]{0,30}(giờ|ngày|phút)` — mọi mốc **hoàn tất** cho việc do cổng / ngân hàng / ERP / đơn vị vận chuyển thực hiện. Mức **Cao**.
18. **Hạn mức trang trí** (→ F2, F8): `(theo email|theo số điện thoại|theo tên|theo địa chỉ|khai lúc|tự khai|đã khai|cung cấp lúc)` xuất hiện trong cùng câu với `(hạn mức|giới hạn|tối đa|cooldown|định danh|mỗi khách)`. Neo hạn mức vào dữ liệu khách tự đặt được. Mức **Cao**.
19. **Giả định phụ thuộc ngoài hoàn hảo** (→ F5): `(cổng|gateway|ERP|WMS|callback|webhook|job|cron|đồng bộ|thông báo|email|SMS)` — với **mỗi** hit, kiểm spec có luật cho chế độ lỗi của láng giềng đó chưa (knowledge/05 §M4). Có nhắc mà không có luật lỗi = mức **Cao**; nhắc kèm luật lỗi = ✓.
20. **Việc giao cho người không quan sát được** (→ F3): `(CSKH|chăm sóc khách hàng|nhân viên|Admin|quản trị|thủ công|bằng tay|đối soát|duyệt)` — mỗi hit phải có (a) một dòng ở §9 nói ai được báo, (b) một trường audit, (c) một trần hoặc điều kiện. Thiếu ≥1 = mức Trung.
21. **Chi phí không ai nhận** (→ F4, `affordable`): `(hoàn 100|hoàn toàn bộ|hoàn đủ|không thu phí|không phí|miễn phí|không giới hạn số lần|không giới hạn)` — kiểm spec có nói ai chịu phí cổng / phí xử lý, hoặc có trần. Mức Trung.
22. **Câu nói về chính spec** (cắt khỏi bản nộp): `(ô đã điền|đã điền|tổ hợp|phân hoạch|không chồng lấn|đủ, không|phủ \d|hit policy .* vì|chứng minh|/36|/\d+ ô)` — chứng minh độ phủ là thứ gửi cho người rà, không phải luật cho Executor. Mức Thấp về rủi ro TRÚNG, nhưng **luôn cắt** vì nó tiêu token trong ngân sách 6.000. Bản 08/09 tốn ~60 từ ≈ 150 token ở nhóm này.

Chạy nhóm 17–22 trên bản **nộp**, không phải bản nội bộ (nhóm 22 đúng ra chỉ tồn tại ở bản nội bộ).

## 3. Kiểm tra cấu trúc (ngoài từ ngữ)

Mọi ví dụ trong file dùng quy ước [a,b) của knowledge/30-viet-spec.md §2 dòng 0.3; nếu spec khai báo quy ước khác, lint theo quy ước đã khai báo.

Mức: **Cao** = Executor gần chắc trả lời khác specs thật; **Trung** = lệch khi tình huống bắn đúng biên; **Thấp** = mất điểm thẩm mỹ/kháng nghị, hiếm bị TRÚNG.

| # | Kiểm tra | Cách phát hiện | Mức | Ví dụ sửa |
|---|---|---|---|---|
| S1 | Số không có đơn vị | Regex `\d+` không kèm phút/giờ/ngày/đơn vị/SKU/%/VND ngay sau | Cao | "TTL = 120" → "TTL = 120 phút (giờ đồng hồ liên tục, không trừ giờ đóng cửa)" |
| S2 | Mốc thời gian không có timezone / nguồn giờ | Mọi "hh:mm", "cuối ngày", "nửa đêm" thiếu UTC+7 / "giờ máy chủ" | Cao | "hết hạn 23:59" → "hết hạn 23:59:59 giờ máy chủ (UTC+7) của ngày tạo" |
| S3 | Khoảng không có [ ) hay ≤/< | Mọi "trong vòng", "đến", "tối đa", "từ…đến" thiếu ký hiệu | Cao | "trong vòng 2 giờ" → "[T0, T0 + 120 phút): phút thứ 120 đã hết hiệu lực" [Berry §5.1 E38] |
| S4 | Luật không có chủ thể (actor) | Câu bị động; "hệ thống" mà không nói module/tác nhân nào (khách, nhân viên, job hẹn giờ) | Trung | "Hold được hủy" → "Job hẹn giờ (chạy mỗi 60 s) chuyển hold sang EXPIRED" [NASA C.2] |
| S5 | "và/hoặc" hoặc ≥2 liên từ logic trong một câu | Ctrl+F "và/hoặc"; đếm "và"+"hoặc" ≥2 trong một câu điều kiện | Cao | Tách thành danh sách đánh số (1)(2)(3) + nói rõ "bất kỳ một" hay "tất cả" [Berry §5.4.7 E244] |
| S6 | "chỉ" đặt sai chỗ | "chỉ" đứng trước động từ ("chỉ được", "chỉ cho phép") | Trung | xem §1 dòng 7 |
| S7 | Phủ định kép / phạm vi phủ định | "không … vì", "không … khi", "không … và", "không phải không" | Trung | xem §1 dòng 6 |
| S8 | Số nhiều không rõ từng-hay-tất-cả | "các X có một Y", "tất cả X … Y" | Cao | "mỗi X có Y riêng" hoặc "tất cả X dùng chung một Y" [Berry §5.2.1 E40] |
| S9 | Thuật ngữ dùng lẫn (2 từ → 1 thứ; 1 từ → 2 thứ) | Lập bảng: mỗi danh từ nghiệp vụ xuất hiện dưới bao nhiêu tên (hold / phiếu giữ / đặt giữ / reservation / đơn giữ) | Cao | Bảng thuật ngữ đầu spec; grep thay toàn bộ về 1 tên [Berry §4.1][NASA C.4 Consistency] |
| S10 | Tham chiếu chết | Mọi "BR-xx", "mục x", "bảng y" được nhắc phải tồn tại | Trung | Grep `BR-\d+` → đối chiếu với danh sách BR đã định nghĩa |
| S12 | Tham chiếu ra tài liệu ngoài | "theo chính sách công ty", "xem tài liệu X", "theo brief", "như đã trao đổi", URL | Cao | Executor không thấy tài liệu ngoài → chép nội dung vào spec [HD §2.1 ①] |
| S13 | Bảng có ô trống / "—" / "N/A" không giải thích | Quét bảng trạng thái, bảng quyết định | Cao | Điền giá trị hoặc ghi "KHÔNG ÁP DỤNG vì …" |
| S14 | Luật không nói điều KHÔNG được làm (inverse requirement) | Mỗi hành động chính (tạo, gia hạn, hủy, chuyển đơn) không có câu "KHÔNG ĐƯỢC …" | Cao | "Guest KHÔNG ĐƯỢC tạo hold; hold KHÔNG khóa giá" [Berry §4.2 E35] |
| S15 | Luật không có tác động phụ (side effect) | Mỗi chuyển trạng thái thiếu 1 trong 4: tồn kho / tiền / thông báo / log-lịch sử | Cao | Thêm cột "Tác động: tồn kho +n; tiền: không; thông báo: push 1 lần; log: ghi sự kiện" |
| S16 | Điều kiện không kiểm chứng được (fit criterion) | Câu không thể viết thành test Input → Output | Cao | "Thông báo kịp thời" → "Input: T_hết hạn − 15 phút. Output: 1 push" [Berry §4.2 E32] |
| S17 | Máy trạng thái thiếu chuyển | Ma trận trạng thái × sự kiện có ô chưa định nghĩa | Cao | Điền mọi ô: "bỏ qua (no-op)" hoặc "lỗi E-xx" |
| S18 | "Ngược lại/nếu không" sau ≥2 điều kiện | Ctrl+F "ngược lại", "nếu không", "trường hợp còn lại" | Trung | xem §1 dòng 8 |
| S19 | Xung đột nội bộ | Hai luật cho cùng điều kiện ra kết quả khác nhau; số liệu khác nhau ở 2 chỗ (TTL 120 ở mục 3, 90 ở bảng) | Cao | Một nguồn sự thật cho mỗi tham số; chỗ khác chỉ trỏ tới BR [NASA C.4 Consistency] |
| S20 | Thiếu luật bao quát (catch-all) | Cuối mỗi mục không có "Mọi trường hợp không nêu ở trên → …" | Cao | Thêm câu chốt phạm vi + hành vi mặc định [HD §2.2] |
| S21 | Thuật ngữ dùng trước khi định nghĩa | Từ viết hoa/trạng thái xuất hiện trước bảng thuật ngữ | Trung | Đưa định nghĩa lên trước lần dùng đầu |
| S22 | **Mục tiêu của brief không có luật nào phục vụ** | Bảng Mục tiêu↔Luật (05 §2): mục tiêu có cột 2 trống | Cao | Viết luật cho mục tiêu đó, hoặc hỏi AI Khách hàng nếu chưa biết specs thật quy định gì |
| S23 | **Luật làm hỏng mục tiêu của brief** | Bảng Mục tiêu↔Luật: ô cột 3 có chữ | Cao | Không tự chốt — hỏi (32 §6). Không ai thiết kế tính năng tự phá mục tiêu của nó ⇒ specs thật gần chắc có luật thứ ba |
| S24 | **Kịch bản suy biến không có luật** | 12 ca ở 05 §M6 — ca nào không tìm được luật bao trùm | Cao khi ca có tiền dính vào; Trung còn lại | Bốn luật §0 mẫu ở 32 §3.2 phủ 8/12 ca, ~90 từ |
| S25 | **Nghĩa vụ tiền chỉ có một mốc** | Câu về thu/hoàn/trừ tiền không tách "khởi tạo" khỏi "hoàn tất"; không có trường trạng thái hoàn tiền | Cao | Mẫu 32 §3.1 |
| S26 | **Không có luật cho ca hoàn tiền thất bại** | Grep `hoàn` → không có nhánh `NẾU … thất bại` | Cao | Thêm nhánh + phương án 2; số ngày phải hỏi, không đoán |
| S27 | **Coi việc giữ tài nguyên là chắc thành công** | Điều kiện dạng `ATP ≥ q` / `còn hàng` → tạo hold, không có nhánh ghi thất bại | Cao | 32 §3.2 luật 3; nguồn: giữ tồn không phải khóa cứng [IBM], compare-and-swap [Shopify] |
| S28 | **Rào bảo vệ đặt sau khi đã khóa tài nguyên** | Đọc §7 luồng chính: thứ tự các bước — tài nguyên (tồn, giá, slot) bị khóa trước khi kiểm định danh / thu tiền / hạn mức | Cao khi brief có mục tiêu chống lạm dụng | Đảo thứ tự, hoặc hỏi mốc khóa tài nguyên (N0-03) |
| S29 | **Phạm vi NGOÀI tự đặt rộng hơn brief** | Từng dòng NGOÀI phạm vi: brief có nhắc nghiệp vụ đó không? | Cao | Nghiệp vụ brief nhắc tường minh không được tự đẩy ra ngoài — 0.10 sẽ trả lời "không thuộc tài liệu này" cho việc brief có nói. Chốt danh sách NGOÀI bằng N1-02, không tự đặt |
| S30 | **Bản nộp chứa câu nói về chính nó** | Nhóm lint 22 | Thấp (nhưng luôn cắt) | Chuyển sang `spec.md` nội bộ / `review.md` |

## 4. Định dạng báo cáo lint

Một file cho spec mình (11:45), một file cho mỗi spec đối thủ (13:00). Sắp theo Mức giảm dần.

| Vị trí (mục/dòng) | Trích | Loại (mục 1–3) | Mức | Viết lại đề xuất | Nếu spec đối thủ: tình huống bắn |
|---|---|---|---|---|---|
| §3.2 / d.41 | "Hold có hiệu lực đến hết ngày." | 10 deixis + S2 + S3 | Cao | "Hết hạn tại T0 + 120 phút UTC+7, khoảng [T0, T0 + 120′): giây 7200 đã hết" | "Khách tạo hold 23:50; 00:05 hôm sau khách bấm thanh toán, hold còn hiệu lực không?" |
| §4 / bảng trạng thái | Ô EXPIRED × "khách bấm gia hạn" trống | S13 + S17 | Cao | "Từ chối, lỗi E-03, không tạo hold mới" | "Hold hết hạn lúc 12:00:00; 12:00:01 khách bấm gia hạn. Gia hạn thành công hay bị từ chối?" |

Chuyển hit → tình huống bắn (vai CÔNG):
1. Chỉ bắn hit trong core flow (tạo / gia hạn / hết hạn / hủy / chuyển đơn / hạn mức / tồn kho) — tránh VÔ HIỆU [HD §2.1 ④].
2. Ưu tiên hit mà đáp án thật (đánh ⚠ buổi sáng) ngược lẽ thường [HD §2.3].
3. Mẫu: bối cảnh cụ thể + số liệu chạm đúng biên + câu hỏi đóng một kết quả.
4. 5 tình huống/spec từ 5 hit khác loại; không bắn 2 lần một câu.

## 5. Thang ưu tiên sửa khi thiếu thời gian (spec mình, 11:45–12:00)

Sửa từ trên xuống; hết giờ thì dừng.

1. **Ô trống bảng trạng thái / ma trận chuyển (S13, S17)** — đối thủ đọc bảng là thấy lỗ.
2. **Câu ⚠ chưa có BR (RTM thiếu)** [HD §2.4] — TRÚNG gần chắc chắn.
2b. **Luật làm hỏng mục tiêu brief (S23) / mục tiêu không có luật (S22)** — đây là chỗ specs thật gần chắc có quy định riêng; sửa bằng cách **đưa vào câu C5** dưới dạng phát biểu Đúng/Sai (20 §3). Hết câu hỏi rồi thì viết luật bao quát an toàn hai chiều (32 §3.3), hoặc chọn phương án phục vụ mục tiêu brief và ghi `[GIẢ ĐỊNH-MT]`.
2c. **Mốc "hoàn tất" hứa hộ bên ngoài (S25, nhóm 17)** — mọi tình huống hoàn tiền của đối thủ đều bắn trúng dòng này.
2d. **Hạn mức trang trí (S28, nhóm 18)** — một câu hỏi "bot dùng email mới thì sao" là spec hoặc im lặng hoặc trả lời phi lý.
2e. **Kịch bản suy biến có tiền dính vào còn hở (S24, S26, S27)** — bốn luật §0 ở 32 §3.2 phủ 8/12 ca với ~90 từ; đây là tỷ lệ chắn / từ tốt nhất trong toàn bộ file này.
3. **Tham chiếu ra ngoài / trực giác ngầm (S12, nhóm 6)** — Executor đoán theo prior.
4. **Số không đơn vị, khoảng không biên, giờ không timezone (S1–S3)** — "đúng 120 phút", "23:59" là mẫu bắn phổ biến nhất.
5. **Thiếu luật bao quát cuối mục (S20)** — 1 câu chặn cả nhóm tình huống.
6. **Thiếu inverse rule & tác động phụ (S14, S15)**.
7. **"và/hoặc", ≥2 liên từ, "ngược lại" treo (S5, S18)** — tách danh sách.
8. **Thuật ngữ dùng lẫn (S9)** — Find & Replace về 1 tên.
9. **Modal yếu (nhóm 3)** — thay PHẢI / KHÔNG ĐƯỢC / ĐƯỢC PHÉP.
10. **Định lượng mờ, so sánh không mốc (nhóm 1, 2, 10)**.
11. **Đại từ trôi, "chỉ" sai chỗ, phạm vi phủ định (nhóm 7, 14, 15, S6, S7)**.
12. **Mã BR trùng, tham chiếu chết (S10)** — chỉ ảnh hưởng kháng nghị.

11:48–11:52: đếm token bản nộp, đích ≤ 5.400 (30 §1); sau mỗi lần cắt, chạy lại nhóm 4 (cắt câu hay sinh "v.v.", "…").
