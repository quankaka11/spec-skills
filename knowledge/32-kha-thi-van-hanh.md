*Dùng 11:35–11:45 (trước khi rà §0) và trong `/spec-review` khối G. Owner: Red Teamer. Vào được cả buổi chiều: đây là 5 loại đạn ít đội nào chắn.*

# 32 — Cổng khả thi & vận hành (feasibility / operability gate)

## 0. Chỗ đứng của file này

ISO/IEC/IEEE 29148 xếp chín đặc tính cho một requirement: `necessary` · `appropriate` · `unambiguous` · `complete` · `singular` · **`feasible`** · `verifiable` · `correct` · `conforming`; bộ yêu cầu còn phải `affordable` và `bounded` [ISO/IEC/IEEE 29148:2018].

Kit trước kiểm được: `unambiguous` (40), `singular` + `verifiable` (30 §4), `complete` (bảng đủ ô, 31), `traceable` (RTM). **Không kiểm**: `feasible`, `affordable`, và tính `correct` theo nghĩa "phục vụ đúng mục tiêu nghiệp vụ". File này là hai cổng còn thiếu.

Vì sao nó ăn điểm trong cuộc thi, không chỉ đẹp lý thuyết:
- Luật bất khả thi ⇒ specs thật **không thể** viết như vậy ⇒ Executor đọc spec ta rồi trả lời khác specs thật ⇒ **TRÚNG**. Ví dụ: ta viết "hoàn tất ≤ 24 giờ", specs thật viết "3–7 ngày làm việc" → mọi tình huống hoàn tiền đều lệch.
- Luật không cưỡng chế được ⇒ đối thủ chỉ cần hỏi "bot dùng email mới thì sao" là spec ta hoặc im lặng hoặc trả lời phi lý.
- Kịch bản suy biến bị bỏ (M6 của 05) ⇒ mỗi ca là một test hạng A cho đối thủ, rủi ro VÔ HIỆU thấp vì nó nằm trong core flow.

## 1. Cổng F — tám kiểm tra chạy trên TỪNG luật

Chấm ✓/✗ cho mỗi BR. ✗ ở F1, F2, F5 hoặc F7 = **mức Cao, chặn nộp**. ✗ ở F3, F4, F6, F8 = Trung, sửa nếu còn thời gian.

| # | Tên | Câu hỏi kiểm | Dấu hiệu ✗ (grep / đọc) | Cách xử lý |
|---|---|---|---|---|
| **F1** | Khả thi kỹ thuật | Có hệ thống nào trên đời làm được điều này trong khung thời gian luật nêu? | Luật hứa một mốc **hoàn tất** cho việc do bên ngoài thực hiện: `hoàn tất ≤`, `tiền về`, `đối soát`, `giao dịch xong` kèm số giờ | Tách "quyết định" / "hoàn tất" (§3.1) |
| **F2** | Cưỡng chế được | Luật neo vào dữ liệu nào; **khách có tự đổi được dữ liệu đó không?** | Hạn mức / định danh neo vào `email`, `số điện thoại` chưa xác thực, `tên`, `địa chỉ`, `thiết bị` không định danh | Neo vào tài khoản đã xác thực / SĐT đã OTP / phương tiện thanh toán; nếu không biết specs thật neo vào gì → **hỏi**, đừng đoán (§3.3) |
| **F3** | Vận hành được | Ai làm, bằng công cụ nào, khi nào biết là phải làm? | Luật giao việc cho người mà không có màn hình/thông báo/lý do: `CSKH hủy`, `Admin xử lý`, `đối soát` không có luật thông báo tương ứng ở §9 | Thêm 1 dòng §9 (ai được báo) + 1 trường audit; hoặc bỏ quyền đó |
| **F4** | Chi phí chấp nhận được | Mỗi lần luật này chạy, ai mất bao nhiêu tiền / bao nhiêu phút người? | Hoàn 100% + phí cổng không hoàn; thao tác thủ công không có trần số lượng; thông báo không có trần tần suất | Ghi rõ ai chịu phí; hoặc đặt trần; hoặc đưa ra NGOÀI phạm vi tường minh (§4) |
| **F5** | Phụ thuộc ngoài thực tế | Luật giả định gì về cổng thanh toán / ERP / job / thông báo? Giả định đó có đúng với hệ thật? | Ngầm giả định: đồng bộ tức thì, đúng thứ tự, giao đúng một lần, không bao giờ lỗi | Đối chiếu bảng §2, viết lại theo ngữ nghĩa thật |
| **F6** | Đảo được / bù được | Nếu luật chạy sai, có đường về không? | Luật tiêu thụ cái không đảo được (đã capture, đã settle, đã giao) rồi mới kiểm điều kiện | Đổi thứ tự: kiểm trước, tiêu thụ sau; hoặc định nghĩa bút toán bù |
| **F7** | Phục vụ mục tiêu | Luật này thuộc cột 2 hay cột 3 của bảng Mục tiêu↔Luật (05 §2)? | Ở cột 3 (làm hỏng mục tiêu), hoặc không ở cột nào | Cột 3 ⇒ hỏi AI Khách hàng ngay, đây gần chắc là chỗ specs thật có luật riêng. Không ở cột nào ⇒ ứng viên cắt đầu tiên nếu vượt 10.000 token |
| **F8** | Chịu được lạm dụng | Sáu kẻ ở 05 §M5 — kẻ nào có lợi khi luật này chạy như viết? | Luật cấp quyền / khóa tài nguyên **trước** khi có ràng buộc (tiền, xác thực, hạn mức) | Đảo thứ tự rào; hoặc giới hạn tài nguyên bị khóa; hoặc thêm cooldown |

**Thứ tự chạy nhanh (3 phút cho 22 BR):** grep trước, đọc sau.

```
F1  grep -nE "hoàn tất|tiền về|đối soát|xong trong|trong vòng .*(giờ|ngày)"
F2  grep -nE "theo email|theo số điện thoại|tự khai|mỗi khách|mỗi tài khoản"
F3  grep -nE "CSKH|Admin|nhân viên|thủ công|đối soát"      → mỗi hit phải có dòng §9 + audit
F5  grep -nE "cổng|ERP|WMS|job|đồng bộ|callback|thông báo"  → mỗi hit đối chiếu §2
F7  đọc bảng Mục tiêu↔Luật (05 §2)
F8  grep -nE "guest|chưa đăng nhập|hoàn 100|không phí|miễn phí"
```

## 2. Bảng thực tế của phụ thuộc ngoài — được hứa gì, KHÔNG được hứa gì

Cột "KHÔNG được hứa" là danh sách câu chặn nộp.

| Phụ thuộc | Sự thật vận hành | ĐƯỢC hứa trong spec | **KHÔNG được hứa** |
|---|---|---|---|
| **Cổng thanh toán — callback** | Không bảo đảm thứ tự sự kiện; khử trùng theo **ID sự kiện**, không theo timestamp; retry tới nhiều ngày [Stripe webhooks] | "hệ thống PHẢI khử trùng theo mã giao dịch của cổng"; "PHẢI chấp nhận sự kiện đến sai thứ tự và lấy trạng thái sau cùng theo mã giao dịch" | "cổng gửi đúng một lần"; "sự kiện đến theo thứ tự phát sinh"; suy trạng thái từ thời điểm nhận |
| **Cổng thanh toán — hoàn tiền** | Hoàn về phương thức gốc là quy tắc của tổ chức thẻ; hoàn về thẻ đã đóng/hết hạn **có thể thất bại** và bị trả về; ngân hàng có thể chuyển sang thẻ thay thế hoặc trả bằng đường khác [Stripe refunds; Helcim; Universe] | "hệ thống PHẢI **khởi tạo** hoàn tiền ≤ N phút"; "NẾU hoàn về phương thức gốc thất bại, THÌ …" | "khách **nhận được** tiền ≤ 24 giờ"; "hoàn tiền luôn thành công"; im lặng về ca thất bại |
| **ERP / WMS đồng bộ tồn** | Định kỳ; có cửa sổ dừng đồng bộ khi đổi bản kế hoạch, tối thiểu ~10 phút (Oracle `MSC: ATP 24x7 Synchronization Downtime`) [Oracle GOP] | "`OnHand` lấy theo ERP, cập nhật mỗi N phút; trong khoảng chưa đồng bộ hệ thống PHẢI dùng số lần cuối và …" | "tồn ERP và e-com luôn khớp"; "đồng bộ tức thì" |
| **Giữ tồn** | **Không phải khóa cứng**: IBM `reserveAvailableInventory` không lock kho, yêu cầu vẫn có thể thất bại vì giao dịch song song đã tiêu thụ [IBM]; Shopify bắt compare-and-swap, lỗi `CHANGE_FROM_QUANTITY_STALE` [Shopify] | "NẾU ghi giữ thất bại do số tồn đã đổi, THÌ hệ thống PHẢI trả `OUT_OF_STOCK`, KHÔNG giữ một phần, KHÔNG tự thử lại" | "kiểm `ATP ≥ q` xong thì hold **luôn** được tạo" |
| **Tồn tụt dưới mức đang giữ** | Shopify: `Available` xuống **âm**, hệ thống **không** tự hủy gì [Shopify]. Oracle: ghi nhận thiếu hụt, cảnh báo overcommitment, hoặc cho ưu tiên cao "lấy" của ưu tiên thấp [Oracle] | chọn **một** đường và viết đủ: giữ nguyên & báo, hoặc hủy theo thứ tự tường minh + thông báo + hoàn tiền | "không bao giờ xảy ra"; hủy hold đã cọc mà không có luật thông báo và hoàn tiền |
| **Job hết hạn** | Purge agent chạy **định kỳ**, không đúng mốc [IBM] | "hiệu lực tính theo `expires_at`; job chạy mỗi N giây; sai lệch ≤ N giây; trong khoảng trễ hệ thống PHẢI coi `q` là chưa nhả" | "hold chuyển trạng thái đúng giây `expires_at`"; nhả tồn "ngay lập tức" |
| **Kênh thông báo** | Best-effort, có thể mất | "gửi đúng 1 lần tại mốc T"; "gửi thất bại KHÔNG ảnh hưởng hiệu lực hold" | hiệu lực hold phụ thuộc gửi được thông báo; "khách luôn nhận được nhắc" |

## 3. Ba mẫu viết lại (dán được)

### 3.1 Tách "quyết định" khỏi "hoàn tất" — chữa F1 + F5

> ✗ `Mọi khoản hoàn cọc bằng 100% số tiền đã thu, về phương thức thanh toán gốc, hoàn tất ≤ 24 giờ.`
>
> ✓ `Hoàn cọc = 100% số tiền đã thu. Hệ thống PHẢI khởi tạo yêu cầu hoàn ≤ 5 phút kể từ khi hold vào trạng thái cuối, về phương thức thanh toán gốc, và đặt trường refund_status = ĐANG_HOÀN. KHI cổng xác nhận thành công → refund_status = ĐÃ_HOÀN. NẾU cổng báo thất bại hoặc không xác nhận trước [N] ngày làm việc, THÌ hệ thống PHẢI đặt refund_status = HOÀN_THẤT_BẠI, báo khách và CSKH, và hoàn bằng [phương án 2]. KHÔNG ĐƯỢC coi hold là đã hoàn tiền khi cổng chưa xác nhận.`

Ba lợi ích: (a) `refund_status` là trạng thái quan sát được ⇒ `verifiable`; (b) có luật cho ca thất bại ⇒ bịt kịch bản M6-5; (c) mốc "khởi tạo" là mốc ta kiểm soát ⇒ `feasible`. Số `[N]` và `[phương án 2]` **phải hỏi**, không đoán.

### 3.2 Luật cho thất bại của phụ thuộc ngoài — chữa F5, bịt M6

Mỗi láng giềng ở 05 §M4 cần đúng một luật dạng:

> `NẾU <láng giềng> <chế độ lỗi> THÌ hệ thống PHẢI (a) <trạng thái hold>, (b) tồn: <K?>, (c) tiền: <T?>, (d) báo: <ai>, và KHÔNG ĐƯỢC <hành vi hấp dẫn nhưng sai>.`

Bốn luật rẻ nhất, phủ 8/12 ca M6, tổng ~90 từ:

1. `NẾU cổng không xác nhận trong hạn trả cọc, THÌ → CANCELLED (HETHONG), K+, T0.` *(M6-1, 6)*
2. `Hệ thống PHẢI khử trùng theo mã giao dịch của cổng; hai lần báo cùng mã ⇒ ghi nhận một lần. Sự kiện đến sai thứ tự ⇒ lấy kết quả sau cùng theo mã giao dịch, không theo thời điểm nhận.` *(M6-3, 4, 7)*
3. `NẾU ghi giữ tồn thất bại do số tồn đã đổi giữa lúc kiểm và lúc ghi, THÌ trả OUT_OF_STOCK, K0 T0, KHÔNG giữ một phần, KHÔNG tự thử lại.` *(M6-10)*
4. `Gửi thông báo thất bại KHÔNG ĐƯỢC làm đổi trạng thái hold, expires_at, hay nghĩa vụ hoàn tiền.` *(M6-11)*

### 3.3 Neo hạn mức vào cái khách không tự đổi được — chữa F2 + F8

> ✗ `áp hạn mức theo email khai lúc cọc`
>
> ✓ `Hạn mức tính theo [đơn vị định danh]. Đơn vị định danh = <tài khoản đã đăng nhập | SĐT đã xác thực OTP | phương tiện thanh toán>.` — và nếu chưa hỏi được specs thật neo vào gì: **đừng chọn hộ**. Viết luật bao quát: `Yêu cầu tạo hold KHÔNG kèm [đơn vị định danh] hợp lệ PHẢI bị từ chối (IDENTITY_REQUIRED).` Luật này an toàn cả hai chiều: nếu specs thật cho guest thì ta chỉ mất một ca; nếu specs thật chặn guest thì ta chắn được cả nhóm.

## 4. Chi phí ẩn — cổng `affordable`

Sáu khoản mà luật "đẹp" hay quên. Mỗi khoản: spec phải nói ai chịu, hoặc đặt trần, hoặc đẩy ra ngoài phạm vi.

| Chi phí | Sinh ra bởi luật nào | Câu hỏi |
|---|---|---|
| Phí cổng thanh toán trên khoản hoàn | hoàn 100%, không phí hủy | ai chịu phí thu + phí hoàn? |
| Đối soát thủ công | thu qua chuyển khoản / ví / COD | ai đối soát, bao lâu một lần? |
| Giờ CSKH | mọi luật có `CSKH`, `Admin`, "báo CSKH phụ trách" | trần số ca/ngày? có màn hình không? |
| Tồn bị khóa không sinh doanh thu | khóa tồn trước khi thu tiền, TTL dài | trần tổng tồn được khóa đồng thời? |
| Thông báo | nhắc trước hết hạn, báo mọi lần đổi trạng thái | trần số thông báo mỗi hold? |
| Lưu trữ audit | audit mọi ô bảng trạng thái | thời hạn lưu? ai xem? |

## 5. Cổng chất lượng bổ sung (nối vào 30 §7, thành dòng 12–16)

| # | Kiểm | Chặn nộp khi |
|---|---|---|
| 12 | Mọi BR đã chấm cổng F §1 | còn ✗ ở F1, F2, F5 hoặc F7 |
| 13 | Bảng Mục tiêu↔Luật (05 §2) đã lập; không mục tiêu nào có cột 2 trống; mọi ô cột 3 đã hỏi hoặc đã xử lý | có mục tiêu trống cột 2 |
| 14 | 12 kịch bản suy biến (05 §M6) đều ✓ hoặc ⛔ tường minh | còn ca ✗ |
| 15 | Mọi mốc thời gian do bên ngoài thực hiện đã tách "khởi tạo" / "hoàn tất" | còn một mốc "hoàn tất" hứa hộ bên ngoài |
| 16 | Mọi hạn mức neo vào dữ liệu khách không tự đổi được, hoặc có luật bao quát §3.3 | còn hạn mức neo vào dữ liệu tự khai |

**Thang ưu tiên sửa khi hết giờ** (chèn vào 40 §5 ngay sau mục 2): F7 cột 3 → F1 mốc hoàn tất → F2 hạn mức trang trí → M6 ca ✗ có tiền dính vào → F5 còn lại → F3/F4.

## 6. Khi không sửa được thì HỎI — chuyển hit F thành câu hỏi

Hit F1/F2/F7 thường **không** sửa được bằng viết lại: nó có nghĩa là đội đang không biết specs thật quy định gì. Mẫu chuyển:

| Hit | Lượt hỏi (một ý, không chỉ thị — 20 §1) |
|---|---|
| F1 mốc hoàn tất | "…tiền hoàn cọc về tới tay khách sau tối đa bao nhiêu ngày làm việc?" (mốc khởi tạo là **lượt thứ hai**, không gộp) |
| F2 hạn mức trang trí | "…hạn mức chống gom hàng được tính theo tài khoản đã đăng nhập hay theo số điện thoại đã xác thực?" |
| F5 callback | "…khi cổng thanh toán báo thành công hai lần cho cùng một giao dịch thì hệ thống ghi nhận một lần hay hai lần?" (ca sai thứ tự là lượt riêng) |
| F5 ERP | "…khi số tồn của hệ thống bán hàng lệch với số tồn của ERP thì hệ thống dùng số nào?" (chu kỳ đồng bộ là lượt riêng) |
| F7 cột 3 | "…tồn kho bị khóa tại thời điểm khách bấm giữ hàng hay tại thời điểm cọc thành công?" **và** (lượt riêng) "…khi khách tự hủy thì tiền cọc được hoàn đủ hay bị trừ một phần?" |
| F8 | "…khách chưa đăng nhập có tạo được lượt giữ hàng không?" (rào định danh là lượt riêng nếu còn nhịp) |

**Mỗi lượt chỉ được một ý và không được chứa chỉ thị (00 §A)** — nên bảng trên đã viết lại thành câu gửi thẳng được; ô nào có hai ẩn số thì ghi rõ phải tách. Cách dùng:

- Hit **F1, F2, F5** (mốc hoàn tất, neo hạn mức, callback, ERP): đã nằm sẵn trong hàng đợi 28 lượt (20 §3.2 lượt 16, 2, 19, 23) — dùng nguyên văn ở 20 §3.3.
- Hit **F7, F8** phát hiện lúc 11:00: chèn **một lượt nhị phân** lên đầu phần hàng đợi còn lại. Giá một lượt là một nhịp chờ, không còn là "một trong năm câu" — nên ngưỡng để quyết định hỏi thấp hơn nhiều so với luật cũ.
- Hit phát hiện **sau khi hết giờ hỏi**: áp luật an toàn hai chiều §3.3, hoặc chọn phương án phục vụ mục tiêu brief và ghi `[GIẢ ĐỊNH-MT]` (30 §1b-3).

## 7. Ví dụ chấm cổng F — `battle/spec.nop.md` 08/09

Chấm 22 BR + §0, 3 phút. Kết quả: **7 ✗**, trong đó 5 mức Cao.

| Luật | ✗ | Vì sao | Sửa |
|---|---|---|---|
| 0.15 | **F1, F5** | "hoàn tất ≤ 24 giờ về phương thức gốc" — bất khả thi với thẻ; không có luật cho ca hoàn thất bại | §3.1 |
| BR-03 + BR-18 | **F2, F8** | hạn mức neo vào email guest tự khai ⇒ trang trí; brief nói mục tiêu là chống bot | §3.3 + hỏi |
| BR-01 | **F7, F8** | `K−` ngay khi nhận yêu cầu, trước khi có tiền và không có rào định danh ⇒ khóa tồn miễn phí; phá mục tiêu 2 của brief | hỏi mốc khóa tồn; hoặc rào trước khi khóa |
| BR-10 + 0.15 | **F7, F4** | hủy vô hạn, hoàn 100%, phí 0, cooldown chỉ theo SKU ⇒ giữ chỗ miễn phí; shop chịu phí cổng | hỏi phí hủy; ghi ai chịu phí |
| BR-16 | **F5** | giả định ERP là chân lý tức thì; không có luật cho cửa sổ chưa đồng bộ | thêm chu kỳ + hành vi trong khoảng trễ |
| BR-15 | **F3, F4** | tự hủy hold **đã cọc** khi kho lệch, nhưng không có trần, không có màn hình CSKH, không nói ai duyệt | thêm §9 + audit + trần, hoặc đổi sang "giữ nguyên & báo" |
| §4 điều kiện tạo hold | **F5** | "`ATP ≥ q` khi máy chủ nhận yêu cầu" coi giữ tồn là khóa cứng; không có luật cho ca ghi giữ thất bại | §3.2 luật 3 |
| §1 NGOÀI phạm vi | (F7) | tự đẩy "thanh toán phần còn lại" ra ngoài dù brief nhắc tường minh ⇒ 0.10 trả lời "không thuộc tài liệu này" cho một nghiệp vụ brief có nói | hỏi N1-02 rồi mới chốt |

Đối chiếu: khối lint từ ngữ của cùng spec này ra **0 hit mức Cao**, cấu trúc **đủ 36/36 ô**, eval set **15/15 ĐỦ**. Bảy ✗ ở trên là toàn bộ phần mà kit cũ không nhìn thấy.
