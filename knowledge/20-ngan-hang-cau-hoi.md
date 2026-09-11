*Dùng cho vai THỦ khi hỏi AI Khách hàng (hỏi liên tục theo hàng đợi, 9:30–11:20 ngày thi); buổi chiều dùng lại §2 và §4 để soi spec mình và spec đối thủ.*
*Viết lại 11/09 sau thi thử: bỏ trần 5 câu, bỏ kiểu câu gộp-một-bảng — hai thứ đó giờ đều làm câu bị từ chối.*

# 20. Hỏi AI Khách hàng & ngân hàng nguyên liệu

## 1. Mười quy tắc hỏi khi một lượt = một ý, cấm chỉ thị, không memory

Tham số (00 §A): **không giới hạn số câu · 4.000 token cho cả hỏi + trả lời · mỗi lượt đúng MỘT Ý · câu chứa chỉ thị bị từ chối · nhịp chờ giữa hai lượt (thi thử: 45 giây) · KHÔNG memory**.

Hai bộ quy tắc cũ đều đã chết, và chết theo hai hướng ngược nhau:
- Bộ "gộp 5–8 câu một lượt" (kit 08/09) — **bị từ chối vì nhiều câu hỏi trong một lượt**.
- Bộ "một câu hỏi nhưng ép về một bảng, cap dòng, cap từ, không giải thích" (kit 09/09) — **bị từ chối vì câu hỏi chứa chỉ thị**, xếp loại injection.

Hệ quả gốc, phải nhớ trước mọi quy tắc: **không ra lệnh được cho AI Khách hàng nữa, nên hình dạng câu hỏi là công cụ điều khiển duy nhất còn lại.**

| # | Quy tắc | Vì sao / Thay vì → Hỏi |
|---|---|---|
| 1 | **Một lượt = một ẩn số.** Đúng một dấu `?`, không "và…", "còn…", "ngoài ra…", không dấu `;` nối hai ô chưa biết | Gộp 3 ý → bị từ chối, mất một nhịp chờ mà không thu được gì. "Ngoài phạm vi là gì, phải ngăn điều gì, guest làm được gì?" → tách thành 3 lượt |
| 2 | **Không một chữ mệnh lệnh nào về cách trả lời.** Danh sách đen: "trả lời đúng một bảng", "theo định dạng", "tối đa N dòng", "mỗi dòng ≤N từ", "không giải thích", "chọn 1", "liệt kê", "ghi rõ", "hãy…", "vui lòng…", `〜してください`, `お願いします`. Danh sách này chỉ bắt cụm **áp lên câu trả lời** — "gia hạn tối đa mấy lần?" là nội dung nghiệp vụ, hoàn toàn hợp lệ | Đây là thứ duy nhất bị gán nhãn *injection* trong thi thử. Câu hỏi phải là **câu nghi vấn thuần**, không có vế sai khiến nào |
| 3 | **Nhị phân là vua: "A hay B?"** Nêu đủ hai vế trong câu; câu trả lời tự ngắn (20–60 token) mà không cần một chỉ thị nào | "Thời hạn giữ tính từ đâu?" (mở, ~200 token, dễ lạc đề) → "…thời hạn giữ bắt đầu tính từ lúc tạo lượt giữ hay từ lúc cọc thành công?" (~40 token, chốt được luật ngay) |
| 4 | **Câu mở chỉ dành cho ô không có mặc định ngành.** Ba loại xứng đáng: **message lỗi nguyên văn**, **danh sách NGOÀI phạm vi**, **thứ tự ưu tiên khi nhiều lỗi/luật cùng đúng**. Chấp nhận 150–400 token cho mỗi lượt loại này | Mọi ô còn lại đều có mặc định ngành (§4) — hỏi mở ở đó là trả 300 token để nghe lại điều mình đã biết |
| 5 | **Mỗi lượt tự chứa.** Nhắc tên hệ thống + tên màn hình/tính năng đúng như brief gọi, trong chính lượt đó | Không memory: "Còn trường hợp thẻ đóng thì sao?" → AI không biết đang nói về hoàn cọc |
| 6 | **Xếp hạng theo giá trị, gửi theo thứ tự, chấp nhận mất phần đuôi.** Giá trị = (đảo lại thì đổi tiền / trạng thái cuối / ai thắng) × (Executor mù sẽ đoán khác) | Trần thật không phải 4.000 token mà là **thời lượng pha hỏi ÷ nhịp chờ**. Kế hoạch không có đường cắt = kế hoạch chưa lập xong |
| 7 | **Không hỏi điều suy được** từ brief hoặc từ mặc định ngành (§4). Mỗi lượt phí là một ô rủi ro Cao không kịp hỏi | "Có audit log không?" → bỏ, tự điền |
| 8 | **Không dẫn dắt.** Câu nhị phân phải nêu **đủ cả hai vế** và không gợi ý vế nào đúng | "Chắc guest không giữ hàng được nhỉ?" → "…khách chưa đăng nhập có tạo được lượt giữ hàng không?" |
| 9 | **Không dùng ảnh.** Thi thử xác nhận ảnh **có** tính token; một ảnh ≈ 1.000–1.600 token = 8–10 lượt nhị phân | 3 lượt ảnh bỏ không dùng, không tiếc |
| 10 | **Cổng 8 kiểm tra trước khi gửi** (bảng ngay dưới) | Một câu bị từ chối không mất token nhưng mất một nhịp — trong pha 16–90 phút thì đó là dữ kiện thật bị mất |

### Cổng 8 kiểm tra (chạy trên từng lượt, còn ✗ thì không gửi)

| # | Kiểm tra | Cách chấm nhanh |
|---|---|---|
| 1 | Đúng **một** dấu `?` | đếm |
| 2 | Đúng **một ý** | thử tách câu thành hai câu hỏi độc lập — tách được ⇒ ✗ |
| 3 | **Tự chứa** | có tên hệ thống/tính năng + tên màn hình; không "như trên", "việc đó", "câu trước" |
| 4 | **Không mệnh lệnh** | grep danh sách đen quy tắc 2. **Chỉ tính khi cụm đó áp lên *câu trả lời***: "gia hạn **tối đa** mấy lần?" là nội dung nghiệp vụ (hợp lệ); "**tối đa** 5 dòng" là chỉ thị (✗). Phân biệt bằng đơn vị đi kèm: dòng / từ / mục / ký tự / bảng ⇒ chỉ thị |
| 5 | **Không viện dẫn tài liệu** | không "theo tài liệu", "trong spec", "tài liệu thiết kế", `資料`, `仕様書`, `設計書` |
| 6 | **Không nhắc bộ máy chấm** | không "chấm", "đáp án", "điểm", "prompt", "model", `採点`, `正解` |
| 7 | **Không dẫn dắt** | câu nhị phân nêu đủ hai vế, không có "chắc là", "đúng không nhỉ" |
| 8 | **Câu trả lời tệ nhất vẫn dùng được** | AI trả lời đúng một từ ⇒ vẫn chốt được một luật? Không ⇒ đổi sang dạng nhị phân |

### Khi bị từ chối

Câu bị từ chối **không trừ token và không reset nhịp chờ** (00 §A). Quy trình: đọc nhãn từ chối → xác định nó là "chứa chỉ thị" hay "nhiều câu hỏi" → sửa đúng lỗi đó → gửi lại ngay. Ghi vào log như một dòng riêng (§6) với `Trạng thái: bị từ chối`, **không cộng token**, nhưng có cộng vào cột "nhịp đã tiêu" — vì đó mới là thứ đang cạn.

### Muốn biết gì → hỏi dạng nào

| Ô cần biết | Dạng | Ví dụ khung |
|---|---|---|
| Mốc thời gian, thứ tự, nguồn chân lý | **nhị phân** | "…tính từ X hay từ Y?" · "…số nào được dùng, số của A hay của B?" |
| Biên off-by-one | **nhị phân** | "…đúng thời điểm bằng thời hạn thì còn hiệu lực hay đã hết?" |
| Tham số có đơn vị | **một con số** | "…thời hạn giữ hàng là bao nhiêu phút?" (một tham số một lượt, không gộp bảng) |
| Quyền / tồn tại của một hành vi | **có–không** | "…khách chưa đăng nhập có tạo được lượt giữ không?" |
| Message hiển thị | **mở, chấp nhận đắt** | "…khi vượt tồn khả dụng thì màn hình hiện message gì?" |
| Danh sách phạm vi | **mở, một lượt duy nhất** | "…những nghiệp vụ nào nằm ngoài phạm vi của ba màn hình này?" |

**Ngân sách:** ước `token ≈ max(số từ × 2,5 ; số ký tự / 2,2)` (00 §A1). Một lượt nhị phân ≈ 100–170 token cả hỏi lẫn trả lời; một lượt mở ≈ 250–450. Hàng đợi 17 lượt hỗn hợp ≈ 2.500–3.200 token — với ngân sách hỏi `Q` cỡ 4.000 thì **token gần như không bao giờ là thứ chặn**; nhịp chờ mới là. Đo lại `Q` từ brief mỗi đề.

**Quy tắc phụ:** mỗi câu trả lời khác mặc định ngành → ghi ⚠ ngay vào RTM (§5) và log (§6). Số dòng ⚠ thu được với 15–20 lượt thường là 12–25 dòng. Phần còn lại của spec vẫn là giả định có xếp hạng, không phải lỗ hổng quy trình.

## 2. Ngân hàng câu hỏi (158 câu, 20 nhóm N0–N19) — kho ý, không phải kho câu gửi đi

Bỏ trần 5 câu rồi thì ngân hàng này **lại là danh sách để hỏi** — nhưng qua hai lần biến đổi bắt buộc:

1. **Mỗi dòng ở đây là một Ý, không phải một câu gửi được.** Rất nhiều dòng viết theo lối cũ: có mệnh lệnh ("Liệt kê ≤5…", "Chọn 1", "Cho bảng") hoặc ghép 2–3 ẩn số vào một dòng ("TTL bao nhiêu; mốc bắt đầu là gì?"). Gửi nguyên như vậy ⇒ **bị từ chối**. Luật chuyển đổi ở ngay dưới.
2. **Xếp hạng rồi mới gửi.** Số lượt bị nhịp chờ chặn (00 §A1), nên thứ tự quan trọng hơn danh sách. Hàng đợi soạn sẵn: §3.
3. **Phần dưới đường cắt vẫn phải thành luật** trong spec, bằng **mặc định ngành** (§4 + 10 §6), gắn `[GIẢ ĐỊNH]` và xếp hạng rủi ro. Không bỏ trống, không sáng tạo giá trị mới (30 §1b).

### Luật chuyển một dòng ngân hàng thành một lượt gửi được

| Dòng ngân hàng viết kiểu | Chuyển thành | Ví dụ |
|---|---|---|
| Có mệnh lệnh (`Liệt kê`, `Chọn 1`, `Cho bảng`, cap dòng/từ) | Bỏ sạch vế mệnh lệnh, chuyển thành câu nghi vấn | N0-02 "…tính theo đơn vị nào: A / B / C / D? **Chọn 1.**" → "…hạn mức số lượt giữ hàng của một khách được tính theo tài khoản đã đăng nhập hay theo số điện thoại đã xác thực?" |
| Ghép nhiều ẩn số (`ĐS×4`, `S+ĐS`, dấu `;`) | **Tách thành nhiều lượt**, xếp hạng riêng từng lượt | N2-07 "Gia hạn được không? Tối đa ? lần; mỗi lần +? phút" → 2–3 lượt |
| Kiểu **B** (xin cả bảng) | Chọn **1–3 ô đắt nhất** của bảng, mỗi ô một lượt; phần còn lại tự điền | N9-04 bảng quyền 7 hành động → chỉ hỏi "CSKH có hủy thay khách được không?" |
| Kiểu **ĐS** / **S** | Gửi gần như nguyên trạng, chỉ thêm tên tính năng cho tự chứa | N2-01 → "…thời hạn giữ hàng của <TÊN TÍNH NĂNG> là bao nhiêu phút?" |

> **N15–N19 (49 câu, thêm 11/09)** phục vụ bốn vùng của cấu trúc spec BTC 10 mục (knowledge/33 §1): mục 2 item màn hình, mục 3 event, mục 4 validation & message lỗi, mục 9 API, mục 7 ràng buộc/chưa chốt. **N17 (message lỗi nguyên văn) giờ hỏi được và phải hỏi** — xem §3b.

Định dạng dòng: **ID** [Ưu tiên·Kiểu] câu hỏi. Kiểu: **S** = số có đơn vị · **B** = bảng · **ĐS** = Đúng/Sai · **DS** = danh sách đóng. Mã lỗ hổng từng câu chắn: xem 50 §1/§3.

### N0. Mục tiêu, ràng buộc, quyền sở hữu thất bại

Nhóm này **không hỏi tham số** — nó hỏi bài toán. Sinh ra từ `knowledge/05-hieu-bai-toan.md` §1 (khối M1, M2, M3, M5) và §4. Mật độ ⚠ cao nhất trong toàn ngân hàng vì đây là chỗ specs thật hay có luật mà brief không kể. **Bắt buộc nằm trên đường cắt của hàng đợi**: N0-01, N0-02, N0-03, N0-04 (§3.2 lượt 25, 2, 1, 6).

- **N0-01** [P0·DS] Liệt kê ≤5 điều tính năng này PHẢI ngăn không cho xảy ra, mỗi dòng ≤8 từ.
- **N0-02** [P0·DS] Hạn mức chống gom hàng tính theo đơn vị nào: tài khoản đã đăng nhập / SĐT đã xác thực OTP / thiết bị / phương tiện thanh toán? Chọn 1.
- **N0-03** [P0·DS] Tồn bị khóa tại thời điểm nào: khi khách bấm giữ, hay khi cọc thành công? Chọn 1.
- **N0-04** [P0·S] Khách tự hủy: mất bao nhiêu % cọc, phí hủy bao nhiêu?
- **N0-05** [P0·B] Bảng: sự kiện kết thúc hold | % cọc hoàn | mốc khởi tạo hoàn (phút) | mốc tiền về tay khách (ngày làm việc).
- **N0-06** [P0·ĐS] Khách chưa đăng nhập tạo hold được không? Nếu được, có rào thêm nào (OTP / captcha / giới hạn thiết bị)?
- **N0-07** [P0·DS] Hoàn về phương thức gốc thất bại (thẻ đóng, ví khóa): hoàn bằng gì thay thế? Chọn 1 hoặc nêu tên.
- **N0-08** [P0·DS] Tồn e-commerce và ERP lệch nhau: số nào thắng? Đồng bộ mỗi ? phút.
- **N0-09** [P1·DS] Cổng báo trùng cùng một giao dịch: ghi nhận 1 hay 2 lần? Sự kiện đến sai thứ tự: lấy kết quả nào?
- **N0-10** [P1·DS] Ghi giữ tồn thất bại dù vừa kiểm thấy còn hàng: khách nhận kết quả gì? Chọn 1.
- **N0-11** [P1·DS] Ai chịu phí cổng thanh toán của khoản cọc đã hoàn: shop / khách / không có phí?
- **N0-12** [P1·S] Trần số hold đang mở trên toàn hệ thống cho một SKU (chống khóa hết tồn) = ?
- **N0-13** [P1·ĐS] Gửi thông báo thất bại có làm đổi trạng thái hold, `expires_at`, hay nghĩa vụ hoàn tiền không?
- **N0-14** [P2·DS] Thao tác của CSKH/Admin trên hold có màn hình riêng không, và ai duyệt?

### N1. Định nghĩa & phạm vi

- **N1-01** [P0·DS] "Đặt giữ hàng" thuộc loại nào: cart hold / đặt cọc giữ hàng / giữ nhận tại cửa hàng / khác (nêu tên)?
- **N1-02** [P0·DS] Liệt kê tối đa 10 nghiệp vụ NGOÀI phạm vi tính năng, mỗi dòng ≤8 từ.
- **N1-03** [P0·DS] Liệt kê các nghiệp vụ TRONG phạm vi tính năng, mỗi dòng ≤8 từ.
- **N1-04** [P0·DS] Áp dụng cho mọi SKU hay chỉ nhóm nào? Nêu tên nhóm + tiêu chí.
- **N1-05** [P1·ĐS×5] Hàng flash sale / khuyến mãi / combo / pre-order / digital: từng loại được giữ không?
- **N1-06** [P1·ĐS×5] Kênh được tạo hold: web / app / POS / hotline / marketplace — Có/Không từng kênh.
- **N1-07** [P1·ĐS×4] "Đặt trước", "khóa hàng", "giữ chỗ", "reservation" có đồng nghĩa với "hold" không?
- **N1-08** [P0·S] Một hold gồm 1 SKU hay nhiều SKU? Tối đa bao nhiêu dòng SKU?
- **N1-09** [P1·DS] Hold gắn với giỏ hàng (cart) hay là đối tượng độc lập?
- **N1-10** [P1·B] Hệ thống ngoài tham gia (ERP, WMS, cổng thanh toán): tên + vai trò 1 dòng.

### N2. Thời hạn giữ (TTL)

- **N2-01** [P0·S] TTL mặc định = ? phút.
- **N2-02** [P0·DS] Mốc bắt đầu TTL: tạo request / cọc thành công / admin xác nhận? Chọn 1.
- **N2-03** [P0·ĐS+S] TTL tính giờ đồng hồ liên tục hay chỉ giờ làm việc? Giờ làm việc = từ ? đến ?.
- **N2-04** [P0·ĐS×3] Chủ nhật / ngày lễ / ngoài giờ mở cửa: TTL có chạy không? Đúng/Sai từng ý.
- **N2-05** [P0·DS] Múi giờ chuẩn của hệ thống? Khách ở múi giờ khác tính theo đâu?
- **N2-06** [P0·ĐS] Đúng giây t = TTL, hold còn hiệu lực hay đã hết?
- **N2-07** [P0·S] Gia hạn được không? Tối đa ? lần; mỗi lần +? phút.
- **N2-08** [P0·DS] Gia hạn cộng dồn vào hạn cũ hay reset từ lúc gia hạn?
- **N2-09** [P0·ĐS×4] Ai được gia hạn: khách / CSKH / admin / hệ thống tự động — Có/Không.
- **N2-10** [P1·S] Cửa sổ được gia hạn: trước hết hạn ? phút; sau hết hạn còn được không?
- **N2-11** [P1·B] TTL khác theo nhóm khách / nhóm hàng / kênh? Nếu có, cho bảng giá trị.
- **N2-12** [P0·S] Hết hạn: chuyển trạng thái tức thì hay do job định kỳ? Job chạy mỗi ? phút.
- **N2-13** [P1·S] Grace period sau hết hạn = ? phút. Trong grace: chốt đơn Được/Không; gia hạn Được/Không.
- **N2-14** [P1·ĐS+S] Hệ thống downtime khiến hold quá hạn: có bù giờ không? Bù ? phút.

### N3. Số lượng & giới hạn

- **N3-01** [P0·S] Số lượng tối đa mỗi SKU trong 1 hold = ?
- **N3-02** [P0·S] Số hold ACTIVE đồng thời tối đa trên 1 khách = ?
- **N3-03** [P1·S] Giới hạn theo tổng giá trị (VND) / theo ngày / theo SKU = ?
- **N3-04** [P0·DS] Khách muốn 10, tồn còn 6: từ chối toàn bộ hay giữ 6?
- **N3-05** [P1·DS] qty 0 hoặc âm: từ chối / tự sửa thành 1 / chấp nhận — chọn 1.
- **N3-06** [P1·S] Khách hủy rồi tạo lại cùng SKU: cooldown ? phút; tối đa ? lần/ngày.
- **N3-07** [P1·DS] Cùng khách tạo hold thứ 2 cho cùng SKU: gộp / từ chối / tạo mới?

### N4. Tồn kho & đồng thời

- **N4-01** [P0·DS] Hold trừ vào Available / On-hand / không trừ? Chọn 1.
- **N4-02** [P0·ĐS] Tồn hiển thị cho khách khác đã trừ hàng đang giữ chưa?
- **N4-03** [P0·S] Cho oversell không? Ngưỡng = ? đơn vị hoặc ?%.
- **N4-04** [P0·DS] Hai khách bấm giữ đơn vị cuối cùng cùng lúc: tiêu chí thắng (timestamp server / cọc trước / hạng khách)?
- **N4-05** [P0·DS] Người thua: báo hết hàng / vào waitlist / tạo hold PENDING — chọn 1; request ở trạng thái nào.
- **N4-06** [P1·S] Safety stock không được đem giữ = ? đơn vị hoặc ?%.
- **N4-07** [P0·DS] Hold gắn với 1 kho/cửa hàng cụ thể hay tồn toàn hệ thống?
- **N4-08** [P1·ĐS] Kho A hết, kho B còn: tự chuyển kho không? Ai quyết?
- **N4-09** [P0·DS] Hàng đang hold bị mất/hỏng/kiểm kê lệch: hold nào bị hủy trước (mới nhất / cũ nhất / chưa cọc)?
- **N4-10** [P0·DS] ERP/WMS báo tồn khác hệ thống: nguồn nào là chân lý?
- **N4-11** [P1·DS] Tồn tăng (nhập kho, hủy hold) khi có người chờ: ai được cấp trước?
- **N4-12** [P0·DS] Lỗi giữa lúc trừ tồn và tạo hold. Sau lỗi: Available = trước lỗi? Đúng/Sai; hold tồn tại? Đúng/Sai.
- **N4-13** [P1·S] Khách bấm 2 lần trong 1 giây: 1 hay 2 hold? Cửa sổ khử trùng (idempotency) = ? giây.

### N5. Đặt cọc & thanh toán

- **N5-01** [P0·S] Có yêu cầu cọc không? Cố định (VND) hay % giá trị? Con số?
- **N5-02** [P1·S] Cọc tối thiểu / tối đa = ? VND.
- **N5-03** [P1·DS] Phương thức thanh toán cọc: liệt kê. COD / trả sau có không?
- **N5-04** [P0·B] Cọc hoàn hay mất trong 4 trường hợp: khách hủy / hệ thống hủy / hết hạn / hết hàng do lỗi kho?
- **N5-05** [P0·S] Hoàn cọc: ?%; trong ? ngày làm việc; về phương thức nào?
- **N5-06** [P0·ĐS] Cọc có trừ vào tiền hàng khi thành đơn không?
- **N5-07** [P0·S] Thanh toán cọc thất bại / timeout: hold ở trạng thái nào; giữ tồn thêm ? phút?
- **N5-08** [P0·DS] Thanh toán thành công NHƯNG hold đã hết hạn trước đó: tự hoàn / khôi phục hold / tạo đơn?
- **N5-09** [P1·ĐS] Thanh toán một phần (ít hơn cọc yêu cầu): chấp nhận hay từ chối?
- **N5-10** [P1·S] Phí hủy = ? VND hoặc ?%; trừ từ cọc hay thu riêng?
- **N5-11** [P1·DS] Callback cổng thanh toán đến sau khi hold đã hủy. Tiền: hoàn 100% / giữ làm cọc hold mới / giữ chờ CSKH — chọn 1; trong ? ngày.

### N6. Giá & khuyến mãi

- **N6-01** [P0·DS] Giá khóa tại thời điểm tạo hold hay tính lại khi chốt đơn?
- **N6-02** [P0·DS] Giá giảm trong lúc hold: khách hưởng giá nào?
- **N6-03** [P1·DS] Giá tăng trong lúc hold: khách trả giá cũ hay mới?
- **N6-04** [P1·ĐS] Flash sale kết thúc trong lúc hold: giá sale còn không?
- **N6-05** [P1·ĐS] Voucher gắn vào hold hết hạn giữa kỳ: còn áp dụng không?

### N7. Hủy & hết hạn

- **N7-01** [P0·ĐS×4] Ai được hủy hold: khách / CSKH / admin / hệ thống — Có/Không từng actor.
- **N7-02** [P0·ĐS+S] Khách hủy được sau khi đã cọc không? Trước mốc nào?
- **N7-03** [P1·S] Hủy có tính điểm phạt / uy tín không? Ngưỡng bị hạn chế = ? lần / ? ngày.
- **N7-04** [P0·DS] Hết hạn: tồn về Available ngay hay vào waitlist trước?
- **N7-05** [P1·S+DS] Có waitlist không? Kích cỡ tối đa? Thứ tự ưu tiên?
- **N7-06** [P1·S] Người trong waitlist được giữ chỗ ? phút để xác nhận.
- **N7-07** [P0·ĐS+S] Hold hết hạn/hủy có khôi phục (reactivate) được không? Ai làm? Trong ? phút.
- **N7-08** [P1·ĐS] Hủy một phần (giảm số lượng) có được không?
- **N7-09** [P0·DS] Hệ thống tự hủy hold trong những trường hợp nào ngoài hết hạn? Liệt kê.
- **N7-10** [P0·ĐS] EXPIRED và CANCELLED là hai trạng thái riêng hay gộp một?

### N8. Chuyển thành đơn hàng

- **N8-01** [P0·DS] Điều kiện đủ để hold thành order: liệt kê.
- **N8-02** [P1·DS] Khách phải xác nhận thêm bước gì (OTP / email / không)?
- **N8-03** [P1·DS] Order là bản ghi mới hay hold đổi trạng thái giữ nguyên ID?
- **N8-04** [P1·DS] Khách đổi SKU / số lượng khi chốt: hold cũ hủy / sửa / tạo mới?
- **N8-05** [P0·ĐS×2] Chốt đơn khi hold còn 1 giây hoặc vừa hết hạn 1 giây: được không?
- **N8-06** [P1·DS] Chốt một phần số lượng đã giữ. Phần dư: giải phóng ngay / giữ đến expires_at / tạo hold mới — chọn 1.

### N9. Actor, quyền & trạng thái khách hàng

- **N9-01** [P0·ĐS] Guest (chưa đăng nhập) được tạo hold không?
- **N9-02** [P1·ĐS×2] Guest được xem / hủy hold qua link email không?
- **N9-03** [P0·DS] Liệt kê toàn bộ actor (khách, CSKH, admin, kho, sale, hệ thống, đối tác).
- **N9-04** [P0·B] Bảng quyền: actor × hành động (tạo, xem, gia hạn, hủy, chuyển khách, ghi đè TTL, duyệt).
- **N9-05** [P1·B] Khách mới / thân thiết / VIP: TTL, hạn mức, cọc khác nhau? Cho bảng.
- **N9-06** [P1·S] Khách blacklist / hủy nhiều. Chặn: cấm tạo hold / giảm HOLD_MAX / bắt cọc — chọn; ngưỡng ? lần/? ngày.
- **N9-07** [P1·ĐS] CSKH tạo hold hộ khách được không? Ghi nhận actor nào?
- **N9-08** [P0·S+ĐS] Hold giá trị lớn cần duyệt không? Ngưỡng ? VND; ai duyệt; chờ duyệt có giữ tồn không?
- **N9-09** [P1·ĐS×2] Admin ghi đè (override) TTL / hạn mức được không? Có log không?
- **N9-10** [P1·DS] Tài khoản bị khóa / xóa khi đang có hold. Hold: giữ đến hết hạn / hủy ngay / chuyển CSKH — chọn 1; cọc hoàn ?%.
- **N9-11** [P0·DS] Tham số dùng chung do admin đổi (TTL, mức cọc, hạn mức, giờ làm việc): áp cho hold ĐANG MỞ hay chỉ hold tạo sau khi đổi? Nếu áp cho hold đang mở thì `expires_at` tính lại từ mốc nào? Cọc đã thu có điều chỉnh không?

### N10. Thông báo

- **N10-01** [P0·ĐS×5] Mốc gửi thông báo: tạo / sắp hết hạn / hết hạn / hủy / thành đơn — Có/Không từng mốc.
- **N10-02** [P0·S] "Sắp hết hạn" = trước ? phút; nhắc ? lần.
- **N10-03** [P1·B] Kênh thông báo theo mốc: email / SMS / push / in-app. Cho bảng.
- **N10-04** [P1·DS] Ai nhận ngoài khách: sale / kho / quản lý?

### N11. Hiển thị & UX

- **N11-01** [P1·DS] Trường bắt buộc hiển thị trên hold: liệt kê.
- **N11-02** [P1·DS] Đồng hồ đếm ngược tính theo server hay client?
- **N11-03** [P0·DS] SKU bị giữ hết hiển thị cho khách khác là "hết hàng" / "đang được giữ" / số còn lại?

### N12. Ngoại lệ & lỗi hệ thống

- **N12-01** [P0·DS] SKU bị ngừng bán / xóa / ẩn khi đang hold: trạng thái mới = ?; cọc: hoàn 100% / ?% / mất.
- **N12-02** [P1·DS] Kho / cửa hàng đóng tạm thời: hold tạm dừng / hủy / chuyển kho?
- **N12-03** [P1·DS] Job hết hạn không chạy: hold quá hạn xử lý thế nào khi khách chốt đơn?
- **N12-04** [P1·ĐS] Mất kết nối cổng thanh toán: có cho tạo hold tạm không cọc không?
- **N12-05** [P1·DS] Đồng bộ tồn từ ERP trễ ? phút: trong lúc đó tin nguồn nào?

### N13. Audit & phi chức năng

- **N13-01** [P2·DS] Log bắt buộc: trường nào; ai xem; lưu ? tháng.

### N14. Ưu tiên khi xung đột

- **N14-01** [P0·DS] Thứ tự ưu tiên tổng quát khi 2 luật xung đột: liệt kê từ cao xuống thấp.
- **N14-02** [P0·DS] VIP hết hạn hold vs khách thường đang chờ waitlist: ai được hàng?
- **N14-03** [P1·DS] Luật kênh POS vs luật online khác nhau: bên nào thắng?
- **N14-04** [P1·ĐS] Cấu hình riêng theo SKU / nhóm khách có ghi đè mặc định toàn hệ thống không?

### N15. Item trên màn hình (mục 2 BTC)

- **N15-01** [P0·DS] Màn hình/chức năng cần đặc tả tên là gì? Liệt kê các khu vực trên màn hình, mỗi dòng ≤6 từ.
- **N15-02** [P0·B] Bảng: mỗi item hiển thị | nhập hay chỉ xem | bắt buộc hay không.
- **N15-03** [P0·B] Item nào có giá trị mặc định? Bảng "Item | Mặc định".
- **N15-04** [P0·B] Giới hạn độ dài / khoảng giá trị từng ô nhập: bảng "Item | Min | Max | Đơn vị".
- **N15-05** [P0·B] Item nào bị **ẩn / disable** và trong điều kiện nào? Bảng "Item | Ẩn hay disable | Điều kiện".
- **N15-06** [P0·B] **Khác biệt giữa khách đăng nhập và guest** trên màn hình: bảng "Item | Login | Guest".
- **N15-07** [P1·DS] Text hiển thị nguyên văn của các nút chính (giữ hàng, hủy, gia hạn)?
- **N15-08** [P1·DS] Format hiển thị số lượng, tiền, thời gian còn lại (ví dụ mẫu 1 dòng)?
- **N15-09** [P1·ĐS] Có hiển thị số tồn còn lại cho khách không? Nếu có, hiển thị số thật hay khoảng?
- **N15-10** [P2·DS] Placeholder của các ô nhập là gì?

### N16. Event màn hình (mục 3 BTC)

- **N16-01** [P0·B] Khi **mở màn hình**, hệ thống lấy những dữ liệu gì, từ đâu? Bảng "Dữ liệu | Nguồn".
- **N16-02** [P0·B] Mỗi nút bấm gọi xử lý gì? Bảng "Nút | Xử lý | Kết quả".
- **N16-03** [P0·DS] Bấm nút giữ hàng **hai lần liên tiếp** (double-click): tạo mấy hold?
- **N16-04** [P1·B] Đổi dropdown / đổi số lượng thì cập nhật lại gì trên màn hình?
- **N16-05** [P1·ĐS] Màn hình có tự làm mới (auto refresh) tồn kho / đồng hồ đếm ngược không? Chu kỳ?
- **N16-06** [P1·DS] Sau khi tạo hold thành công, màn hình chuyển đi đâu hay ở lại?
- **N16-07** [P2·ĐS] Có xác nhận (popup) trước khi hủy hold không? Text nguyên văn?

### N17. Validation & message lỗi (mục 4 BTC)

- **N17-01** [P0·B] Bảng mọi rule kiểm tra: "Item | Nội dung check | Message hiển thị (nguyên văn)".
- **N17-02** [P0·B] Rule nào chạy ở **FE**, rule nào ở **BE**, rule nào cả hai? Bảng "Rule | FE/BE/Cả hai".
- **N17-03** [P0·DS] Message nguyên văn khi **vượt tồn khả dụng**?
- **N17-04** [P0·DS] Message nguyên văn khi **hold đã hết hạn**?
- **N17-05** [P0·DS] Message nguyên văn khi **trạng thái không hợp lệ** (gia hạn hold đã hủy…)?
- **N17-06** [P1·DS] Message nguyên văn khi **thanh toán cọc thất bại**?
- **N17-07** [P1·ĐS] Nhiều lỗi cùng lúc: hiện tất cả hay chỉ lỗi đầu tiên? Theo thứ tự nào?
- **N17-08** [P1·DS] Lỗi hệ thống chung (API chết): message chung cho user là gì?
- **N17-09** [P2·DS] Message hiển thị ở đâu: cạnh item, đầu trang, hay popup?

### N18. Luồng dữ liệu & API (mục 9 BTC)

- **N18-01** [P0·B] Các hệ thống tham gia và quan hệ: bảng "Hệ thống | Vai trò | Nhận/gửi gì".
- **N18-02** [P0·B] Mỗi lần trao đổi dữ liệu: bảng "Khi nào | Từ → Tới | Dữ liệu | Đồng bộ hay bất đồng bộ".
- **N18-03** [P0·DS] Khi gọi hệ thống ngoài **thất bại**: retry mấy lần, cách nhau bao lâu, rồi làm gì?
- **N18-04** [P0·DS] Tồn kho lấy **real-time từ ERP/WMS** hay từ cache? Nếu cache, trễ bao lâu?
- **N18-05** [P1·ĐS] Có gửi dữ liệu hold sang hệ thống khác không? Field nào chỉ gửi trong điều kiện nào?
- **N18-06** [P1·DS] Khi mail/thông báo gửi thất bại **sau khi đã lưu hold**: xử lý thế nào?
- **N18-07** [P1·DS] Có alert cho vận hành khi tích hợp lỗi không? Ngưỡng nào?
- **N18-08** [P2·B] Field nào do server tự tính (không nhận từ client)?

### N19. Ràng buộc & ca bất thường liên logic (mục 7 BTC)

- **N19-01** [P0·DS] **Dữ liệu đổi giữa lúc hiển thị và lúc submit** (tồn/giá đổi sau khi khách mở màn hình): xử lý thế nào?
- **N19-02** [P0·DS] **Gửi trùng** (submit 2 lần, mở link xác nhận 2 lần): kết quả là gì?
- **N19-03** [P0·DS] Ràng buộc kỹ thuật/nghiệp vụ đã biết (giới hạn hệ thống, quy định công ty): liệt kê tên.
- **N19-04** [P1·DS] Điều gì trong tính năng này **đang chờ xác nhận / chưa chốt**? Liệt kê tên.
- **N19-05** [P1·DS] Mục tiêu tốc độ (thời gian phản hồi) và giới hạn số request có quy định không? Số cụ thể?
- **N19-06** [P2·DS] Yêu cầu bảo mật/thông tin cá nhân riêng cho tính năng này?

## 3. Hàng đợi lượt hỏi — cách dựng, và bản soạn sẵn 28 lượt

### 3.1 Dựng hàng đợi trong 8 phút (chạy ngay sau `/frame`)

1. **Tính trần lượt thật**: `(số phút của pha hỏi ÷ nhịp chờ) − 2 lượt dự phòng cho câu bị từ chối`. Thi thử: 16 phút ÷ 45 giây ≈ 21, trừ dự phòng và trừ thời gian viết spec ⇒ đường cắt thật là **13**. Ngày thi 9:30–11:20 với nhịp 45 giây ⇒ ~100 lượt về lý thuyết, nhưng token 4.000 chặn ở **~20 lượt** — lúc đó token mới là thứ chặn. **Tính cả hai, lấy số nhỏ hơn.**
2. **Đổ ô `?` từ `/frame`** (nhóm 1 và nhóm 2) vào một danh sách phẳng. Mỗi ô một dòng, chưa viết câu.
3. **Chấm giá trị từng ô**: `đảo lại thì đổi tiền / trạng thái cuối / ai thắng` (có = 2 điểm) × `Executor mù sẽ đoán khác mặc định ngành` (có = 2 điểm) + `brief chỉ mặt đích danh` (+1) + `là mâu thuẫn nội tại của brief` (+2). Mâu thuẫn nội tại luôn lên đầu: nó là dấu hiệu specs thật có luật thứ ba.
4. **Chọn dạng** theo bảng "muốn biết gì → hỏi dạng nào" (§1). Nhắm **≥60% hàng đợi là nhị phân** — đó là cách duy nhất giữ token thấp mà không dùng chỉ thị.
5. **Viết nguyên văn từng lượt**, chạy **cổng 8 kiểm tra** (§1) trên cả hàng đợi một lần, trước khi bấm gửi lượt đầu tiên.
6. **Kẻ đường cắt** ở trần lượt của bước 1. Mọi lượt dưới đường cắt → thành dòng `G-xx` trong RTM **ngay lúc lập kế hoạch**, kèm giá trị mặc định ngành dự kiến (§4). Không đợi tới lúc hết giờ mới nghĩ.
7. **Không chờ câu trả lời trước để soạn câu sau** — AI không có memory, các lượt độc lập hoàn toàn. Người bấm gửi chỉ bấm và dán; người viết spec viết song song từ phút đầu.

**Quy tắc thứ tự cuối cùng:** cái gì *không suy được từ đâu khác* đi trước cái gì *có mặc định ngành an toàn*. Một ô có mặc định ngành tốt mà rớt xuống dưới đường cắt chỉ mất một chút xác suất; một ô như message nguyên văn rớt xuống thì spec chắc chắn hở.

### 3.2 Hai mươi tám lượt soạn sẵn — đề "đặt giữ hàng" (gọt lại theo brief thật)

Thay `<TÊN TÍNH NĂNG>` bằng đúng tên brief gọi, và thay tên màn hình cho khớp. Cột "Không hỏi ⇒" là giá trị tự điền nếu lượt đó rớt dưới đường cắt.

| # | Ý | Dạng | Vì sao xếp ở đây | ~token | Phủ ID | Không hỏi ⇒ |
|---|---|---|---|---|---|---|
| 1 | Mốc khóa tồn | nhị phân | F7 — quyết định cả mô hình chống gom hàng; không suy được | 140 | N0-03 | khóa khi tạo `[NGÀNH]` |
| 2 | Đơn vị neo hạn mức | nhị phân | F2 — neo sai thì mọi hạn mức là trang trí | 150 | N0-02 | tài khoản đăng nhập |
| 3 | Thời hạn giữ (phút) | số | mọi luật thời gian treo vào đây | 110 | N2-01 | 120 phút |
| 4 | Mốc bắt đầu thời hạn | nhị phân | off-by-one + tranh chấp tiền | 130 | N2-02 | lúc tạo |
| 5 | Mức cọc | số | dòng tiền gốc | 110 | N5-01 | 10% |
| 6 | Khách tự hủy: hoàn đủ hay bị trừ | nhị phân | F7 — hủy miễn phí là lỗ giữ chỗ vô hạn | 140 | N0-04 | hoàn 100% |
| 7 | Hết hạn: cọc hoàn hay mất | nhị phân | ca kết thúc phổ biến nhất | 140 | N5-04 | hoàn 100% |
| 8 | Message khi vượt tồn khả dụng | **mở** | mục 4 BTC — **không có mặc định ngành** | 300 | N17-03 | tự viết (30 §1b-2: ca tệ nhất) |
| 9 | Message khi thao tác trên lượt giữ đã hết hạn | **mở** | mục 4 BTC — như trên | 300 | N17-04 | tự viết |
| 10 | Nhiều lỗi cùng lúc thì hiện lỗi nào | mở ngắn | mục 4 BTC; đối thủ bắn rẻ nhất | 220 | N17-07 | lỗi đầu tiên theo thứ tự trên màn hình |
| 11 | Khách chưa đăng nhập có tạo được không | có–không | #23 + mâu thuẫn brief điển hình | 120 | N9-01 | không được |
| 12 | Đúng mốc bằng thời hạn: còn hay hết | nhị phân | biên off-by-one, đạn rẻ nhất | 130 | N2-06 | đã hết (nửa mở) |
| 13 | Thiếu tồn: từ chối toàn bộ hay giữ phần còn | nhị phân | đổi trạng thái cuối | 140 | N3-04 | từ chối toàn bộ |
| 14 | Hai khách giành đơn vị cuối: ai thắng | nhị phân | ai thắng = tiêu chí chấm | 150 | N4-04 | timestamp server sớm hơn |
| 15 | Giá khóa lúc tạo hay tính lại lúc chốt | nhị phân | đổi mọi con số tiền | 140 | N6-01 | khóa lúc tạo |
| 16 | Tiền hoàn về tay khách sau mấy ngày làm việc | số | F1 — tách mốc quyết định khỏi mốc hoàn tất | 120 | N5-05 | 5–7 ngày làm việc |
| 17 | Hoàn về phương thức gốc thất bại thì hoàn bằng gì | mở ngắn | #16; không có mặc định an toàn | 200 | N0-07 | luật an toàn hai chiều 32 §3.3 |
| 18 | Thanh toán thành công sau khi đã hết hạn | nhị phân | ca tiền–trạng thái chỏi nhau | 160 | N5-08 | tự hoàn, không tạo đơn |
| 19 | Cổng báo thành công hai lần: ghi nhận mấy lần | nhị phân | F5 idempotency | 150 | N0-09 | khử trùng, ghi 1 lần |
| 20 | Gia hạn được mấy lần | số | — | 110 | N2-07 | 1 lần |
| 21 | Gia hạn cộng dồn hay tính lại | nhị phân | — | 130 | N2-08 | tính lại từ lúc gia hạn |
| 22 | Tồn hiển thị đã trừ phần đang giữ chưa | có–không | mục 2 BTC + đạn hiển thị | 120 | N4-02 | đã trừ |
| 23 | Tồn lệch với ERP: số nào được dùng | nhị phân | F5 phụ thuộc ngoài | 150 | N4-10 | hệ nội bộ |
| 24 | Một khách mở tối đa mấy lượt cùng lúc | số | — | 110 | N3-02 | 3 |
| 25 | Điều tính năng phải ngăn | **mở** | nuôi bảng Mục tiêu↔Luật (05 §2) | 320 | N0-01 | suy từ M1 của brief |
| 26 | Nghiệp vụ ngoài phạm vi | **mở** | mục 1 BTC + rào chống VÔ HIỆU. **Hạ hạng nếu brief không liệt kê "sai phạm vi" trong lý do VÔ HIỆU** | 320 | N1-02 | suy từ brief, ghi `[SUY RA]` |
| 27 | CSKH có hủy/gia hạn thay khách không | có–không | mục 8 BTC actor | 120 | N9-07 | có, có log |
| 28 | Job dọn hết hạn chạy mỗi bao nhiêu phút | số | grace period ngầm | 110 | N2-12 | 1 phút |

**Cộng dồn:** 13 lượt đầu ≈ **2.100 token**; 20 lượt ≈ **3.100**; cả 28 ≈ **4.200 — vượt 4.000**. Nghĩa là ở ngày thi, **token chặn quanh lượt 26–27**, còn nhịp chờ chặn sớm hơn nếu pha hỏi ngắn. Ba lượt mở (#8, #9, #25/#26) một mình đã ăn ~1.100 token: giữ chúng ở trên đường cắt là quyết định có ý thức, không phải sơ suất.

### 3.3 Nguyên văn 28 lượt (mỗi dòng một lượt, copy từng dòng)

```
1.  Trong <TÊN TÍNH NĂNG>, tồn kho bị khóa tại thời điểm khách bấm giữ hàng hay tại thời điểm cọc thành công?
2.  Trong <TÊN TÍNH NĂNG>, hạn mức số lượt giữ của một khách được tính theo tài khoản đã đăng nhập hay theo số điện thoại đã xác thực?
3.  Trong <TÊN TÍNH NĂNG>, thời hạn giữ hàng mặc định là bao nhiêu phút?
4.  Trong <TÊN TÍNH NĂNG>, thời hạn giữ hàng bắt đầu tính từ lúc tạo lượt giữ hay từ lúc cọc thành công?
5.  Trong <TÊN TÍNH NĂNG>, tiền cọc khách phải trả khi tạo lượt giữ là bao nhiêu?
6.  Trong <TÊN TÍNH NĂNG>, khi khách tự hủy lượt giữ thì tiền cọc được hoàn đủ hay bị trừ một phần?
7.  Trong <TÊN TÍNH NĂNG>, khi lượt giữ hết hạn mà khách chưa chốt đơn thì tiền cọc được hoàn hay khách mất cọc?
8.  Trong <TÊN TÍNH NĂNG>, khi khách yêu cầu giữ nhiều hơn số tồn khả dụng thì màn hình hiển thị message gì?
9.  Trong <TÊN TÍNH NĂNG>, khi khách thao tác trên một lượt giữ đã hết hạn thì màn hình hiển thị message gì?
10. Trong <TÊN TÍNH NĂNG>, khi nhiều lỗi cùng xảy ra trên một lần bấm thì màn hình hiển thị lỗi nào trước?
11. Trong <TÊN TÍNH NĂNG>, khách chưa đăng nhập có tạo được lượt giữ hàng không?
12. Trong <TÊN TÍNH NĂNG>, tại đúng thời điểm bằng thời hạn giữ thì lượt giữ còn hiệu lực hay đã hết hiệu lực?
13. Trong <TÊN TÍNH NĂNG>, khi tồn còn ít hơn số khách muốn giữ thì hệ thống từ chối toàn bộ hay giữ đúng phần còn lại?
14. Trong <TÊN TÍNH NĂNG>, khi hai khách cùng giành đơn vị cuối cùng thì người gửi yêu cầu trước thắng hay người cọc thành công trước thắng?
15. Trong <TÊN TÍNH NĂNG>, giá khách phải trả được khóa tại thời điểm tạo lượt giữ hay tính lại tại thời điểm chốt đơn?
16. Trong <TÊN TÍNH NĂNG>, tiền hoàn cọc về tới tay khách sau tối đa bao nhiêu ngày làm việc?
17. Trong <TÊN TÍNH NĂNG>, khi hoàn cọc về phương thức thanh toán gốc thất bại vì thẻ đã đóng thì tiền được hoàn bằng cách nào?
18. Trong <TÊN TÍNH NĂNG>, khi thanh toán cọc báo thành công sau lúc lượt giữ đã hết hạn thì hệ thống hoàn tiền hay tạo đơn hàng?
19. Trong <TÊN TÍNH NĂNG>, khi cổng thanh toán báo thành công hai lần cho cùng một giao dịch thì hệ thống ghi nhận một lần hay hai lần?
20. Trong <TÊN TÍNH NĂNG>, một lượt giữ được gia hạn tối đa mấy lần?
21. Trong <TÊN TÍNH NĂNG>, thời hạn sau khi gia hạn được cộng dồn vào hạn cũ hay tính lại từ thời điểm gia hạn?
22. Trong <TÊN TÍNH NĂNG>, số tồn hiển thị cho khách khác đã trừ phần hàng đang được giữ chưa?
23. Trong <TÊN TÍNH NĂNG>, khi số tồn của hệ thống bán hàng lệch với số tồn của ERP thì hệ thống dùng số nào?
24. Trong <TÊN TÍNH NĂNG>, một khách được mở tối đa mấy lượt giữ cùng lúc?
25. Trong <TÊN TÍNH NĂNG>, những điều nào tuyệt đối không được xảy ra?
26. Những nghiệp vụ nào nằm ngoài phạm vi của <TÊN TÍNH NĂNG>?
27. Trong <TÊN TÍNH NĂNG>, nhân viên chăm sóc khách hàng có hủy hoặc gia hạn lượt giữ thay khách được không?
28. Trong <TÊN TÍNH NĂNG>, job dọn các lượt giữ hết hạn chạy mỗi bao nhiêu phút?
```

Cả 28 dòng đã qua cổng 8 kiểm tra: một dấu `?`, một ý, tự chứa, không một chữ mệnh lệnh, không viện dẫn tài liệu, không nhắc bộ máy chấm, nhị phân nêu đủ hai vế (18/28 dòng), và câu trả lời một từ vẫn chốt được luật.

### 3.4 Lượt xác nhận (thay cho "câu restate" của bản cũ)

Không còn một câu restate gộp 10 phát biểu — **gộp như vậy bị từ chối**. Thay bằng các **lượt xác nhận rời**, mỗi lượt một phát biểu, chạy sau khi `/spec-write` có bảng xếp hạng rủi ro giả định:

```
Trong <TÊN TÍNH NĂNG>, <phát biểu có con số hoặc trạng thái cụ thể> — điều này đúng hay không đúng?
```

- Giá: ~60–90 token/lượt, nhưng **vẫn tốn đúng một nhịp chờ như mọi lượt khác**. Đó là lý do gốc khiến chiến thuật đổi: một giả định rủi ro Cao đáng hỏi thì **hỏi thẳng dạng nhị phân ngay từ đầu hàng đợi**, đừng để dành tới cuối. Lượt xác nhận chỉ dành cho giả định *sinh ra trong lúc viết spec* mà kế hoạch ban đầu không nhìn thấy.
- Dạng nhị phân "A hay B" **luôn tốt hơn** dạng xác nhận "điều này đúng không?": cùng giá, nhưng không dẫn dắt, nên câu trả lời dùng được làm bằng chứng kháng nghị (50 §7).
- Xếp lượt xác nhận theo rủi ro giảm dần và chấp nhận mất phần đuôi khi hết giờ.

### 3.5 Ánh xạ nhóm §2 → hàng đợi, và phần vẫn phải tự điền

| Nhóm §2 | Hàng đợi §3.2 phủ | Vẫn phải tự điền bằng mặc định ngành (§4, 10 §6) |
|---|---|---|
| N0 | 01, 02, 03, 04, 07, 09 | 05, 06, 08, 10, 11, 12, 13, 14 |
| N1 | 02 | 01, 03, 04, 05, 06, 07, 08, 09, 10 |
| N2 | 01, 02, 06, 07, 08, 12 | 03, 04, 05, 09, 10, 11, 13, 14 |
| N3 | 02, 04 | 01, 03, 05, 06, 07 |
| N4 | 02, 04, 10 | 01, 03, 05, 06, 07, 08, 09, 11, 12, 13 |
| N5 | 01, 04, 05, 08 | 02, 03, 06, 07, 09, 10, 11 |
| N6 | 01 | 02, 03, 04, 05 |
| N7 | — | toàn nhóm (hết hạn → Available ngay; EXPIRED và CANCELLED tách riêng) |
| N8 | — | toàn nhóm |
| N9 | 01, 07 | 02…06, 08…11 |
| N10–N14 | — | toàn nhóm |
| N15 (item màn hình) | 22 | phần còn lại: mặc định UI + brief |
| N16 (event) | — | toàn nhóm; double-click phủ gián tiếp qua lượt 19 |
| N17 (validation & message) | 03, 04, 07 | 01, 02, 05, 06, 08, 09 — **mỗi message còn thiếu là một lượt đáng gửi nếu còn giờ** |
| N18 (API) | — | toàn nhóm; retry/thất bại phủ gián tiếp qua lượt 17, 19, 23 |
| N19 (ràng buộc, bất thường) | — | 01 phủ qua lượt 15, 02 qua lượt 19; còn lại tự điền |

Tỷ lệ vẫn là **~65% ngân hàng không được hỏi**, ngay cả khi bỏ trần số câu — vì trần đã chuyển sang thời gian. Mỗi ô ở cột phải là một luật vẫn phải viết, bằng mặc định ngành, gắn `[GIẢ ĐỊNH]`.

## 3b. Bốn vùng mới của cấu trúc BTC 10 mục — hở nào đã đóng, hở nào còn

Cấu trúc 10 mục (knowledge/33 §1) thêm bốn vùng mà kế hoạch C1–C5 cũ không phủ. Bỏ trần 5 câu thì **hở lớn nhất đã đóng lại**:

| Vùng | Trước (5 câu) | Bây giờ (hàng đợi) |
|---|---|---|
| **Mục 4 — message lỗi nguyên văn** | Hở **Cao**; chuỗi nguyên văn không suy được từ mặc định ngành | **Hỏi được và phải hỏi**: mỗi message một lượt mở (~300 token). Lượt 8, 9, 10 của §3.2 nằm trên đường cắt. Còn giờ thì hỏi tiếp các message của N17-05/06/08 |
| **Guest vs login** (N15-06) | Hở TB–Cao | Lượt 11; nếu brief có mâu thuẫn "mọi khách truy cập" vs "chống bot" thì đẩy lên top 5 |
| Mục 2 item, mục 3 event | Tự điền được | Vẫn tự điền, trừ ô hiển thị tồn (lượt 22). Sai thì chỉ sai hiển thị |
| Mục 9 API | Tự điền được | Vẫn tự điền; ca thất bại phụ thuộc ngoài đã nằm ở lượt 17, 19, 23 |

**Quyết định duy nhất còn phải cân trước giờ mở AI:** ba lượt mở (#8, #9, #25 hoặc #26) ăn ~1.100 token và 3 nhịp. Giữ cả ba khi brief có message hiển thị trong phạm vi; bỏ #26 trước tiên nếu brief **không** liệt kê "ngoài phạm vi" trong các lý do VÔ HIỆU (00 §A2-5).

## 4. Bảng mặc định ngành — **VÍ DỤ cho miền "đặt giữ hàng"**

> ⚠ **Bảng này chỉ đúng cho miền đặt giữ hàng.** Đề ngày thi gần như chắc chắn ở miền khác (giỏ hàng & khuyến mãi, đặt lịch, hoàn tiền, ví/điểm, duyệt nhiều cấp…). Dùng nó như **ví dụ về hình dạng** một bảng mặc định, rồi dựng bảng cho miền thật theo **§4b**. Tra một ô của miền A trong bảng của miền B luôn trả về "không thấy" — và "không thấy" phải đi theo nhánh 30 §1b-2b, **không** được im lặng điền bừa.
>
> Đây là lỗ hổng đã làm hỏng bản nộp thi thử 11/09: kit bảo "lấy mặc định từ 20 §4", đề lại ở miền khác, bảng tra rỗng 0/31, nên sáu giá trị được nghĩ ra tại chỗ và cả sáu đều sai.

Bảng này có **hai công dụng, cả hai đều quan trọng hơn trước**:

1. **Nguồn giá trị mặc định để tự điền.** Cột "Phát biểu" là mặc định ngành. Ô nào không hỏi được thì spec viết đúng theo phát biểu đó và gắn `[GIẢ ĐỊNH]` — vì đó cũng chính là điều Executor mù sẽ đoán khi spec im lặng, nên viết ra không làm tăng rủi ro mà loại được đa nghĩa (lý lẽ đầy đủ: 30 §1b).
2. **Kho nội dung cho các lượt xác nhận** (§3.4) và cho các lượt nhị phân sinh thêm khi còn giờ. Chọn theo cột 3: ưu tiên dòng có **tiền hoặc tồn** dính vào. Chuyển một phát biểu thành lượt nhị phân luôn tốt hơn hỏi xác nhận: "Giá khóa tại thời điểm tạo hold" → "…giá được khóa lúc tạo lượt giữ hay tính lại lúc chốt đơn?"

Nếu AI trả lời **Sai** ⇒ đó là ⚠ phản trực giác: viết ngay thành BR có mã trong spec (vai THỦ) và ghi vào danh sách đạn (vai CÔNG).

| # | Phát biểu | Nếu "Sai" ⇒ BR về |
|---|---|---|
| 1 | Guest (chưa đăng nhập) không được tạo hold. | Quyền guest, cách định danh |
| 2 | TTL tính giờ đồng hồ liên tục, chạy cả Chủ nhật và ngày lễ. | Lịch/giờ làm việc, cách dừng đồng hồ |
| 3 | TTL bắt đầu từ lúc tạo hold, không phải lúc cọc thành công. | Mốc bắt đầu chính xác |
| 4 | Hold trừ ngay vào tồn Available khi tạo. | Thời điểm và loại tồn bị trừ |
| 5 | Khách khác nhìn thấy tồn đã trừ phần đang giữ. | Hiển thị tồn |
| 6 | Không cho oversell. | Ngưỡng oversell |
| 7 | Thiếu tồn thì từ chối toàn bộ, không giữ một phần. | Giữ một phần |
| 8 | Giá được khóa tại thời điểm tạo hold. | Thời điểm định giá |
| 9 | Giá giảm trong lúc hold, khách vẫn trả giá đã khóa. | Ưu tiên giá thấp hơn |
| 10 | Hết hạn thì tồn về Available ngay, không qua waitlist. | Waitlist, thứ tự cấp |
| 11 | Không có waitlist. | Kích cỡ, TTL của chỗ chờ |
| 12 | Khách được hủy hold bất kỳ lúc nào trước khi thành đơn. | Mốc khóa hủy |
| 13 | Khách hủy trước hạn được hoàn 100% cọc. | Tỷ lệ hoàn, phí hủy |
| 14 | Hết hạn mà không chốt đơn thì mất cọc. | Xử lý cọc khi hết hạn |
| 15 | Gia hạn được tối đa 1 lần. | Số lần gia hạn |
| 16 | Gia hạn reset TTL từ thời điểm gia hạn, không cộng dồn. | Cách tính TTL sau gia hạn |
| 17 | CSKH có thể gia hạn và hủy hold thay khách. | Bảng quyền CSKH |
| 18 | Đơn vị cuối cùng thuộc về người có timestamp server sớm hơn. | Tiêu chí thắng đồng thời |
| 19 | Hold gắn với một kho cụ thể, không tự chuyển kho. | Đa kho |
| 20 | Cọc được trừ vào tiền hàng khi thành đơn. | Cấn trừ cọc |
| 21 | Thanh toán thành công sau khi hold hết hạn ⇒ tự hoàn tiền, không tạo đơn. | Xử lý thanh toán trễ |
| 22 | Order là bản ghi mới; hold chuyển sang trạng thái đã chuyển đổi. | Quan hệ hold–order |
| 23 | Mỗi khách có giới hạn số hold ACTIVE đồng thời. | Hạn mức/khách |
| 24 | Hết hạn có hiệu lực đúng mốc TTL, không chờ job định kỳ. | Grace period, chu kỳ job |
| 25 | Thông báo "sắp hết hạn" gửi đúng 1 lần. | Lịch nhắc |
| 26 | Đúng mốc t = TTL, hold đã hết hiệu lực (khoảng nửa mở). | Biên bao gồm/không |
| 27 | Hàng flash sale không được giữ. | Phạm vi sản phẩm |
| 28 | Tồn kho hệ thống nội bộ là nguồn chân lý, không phải ERP. | Phụ thuộc ngoài |
| 29 | Hủy hold không mất phí. | Phí hủy |
| 30 | Hold đã hết hạn/hủy không khôi phục được. | Reactivate |
| 31 | Admin đổi tham số dùng chung chỉ áp cho hold tạo sau khi đổi; hold đang mở giữ tham số cũ. | Hồi tố tham số lên hold đang mở, mốc tính lại |

## 4b. Dựng bảng mặc định cho **miền của đề này** — 6 phút, chạy trong `/frame`

Output: `battle/mac-dinh-nganh.md`. Đây là **toán hạng** của quy tắc "không sáng tạo giá trị" (30 §1b). Không có file này thì quy tắc đó vô nghĩa.

**Bước 1 — gọi tên miền** từ brief bằng 2–4 chữ ("giỏ hàng & khuyến mãi", "đặt lịch khám", "hoàn tiền đơn"). Kể tên 2–3 sản phẩm có thật trong miền đó mà mình biết hành vi (đó là nguồn mặc định; không kể được tên nào ⇒ ghi thẳng "miền này tôi không có mặc định" và mọi ô của nó vào nhánh 30 §1b-2b).

**Bước 2 — mỗi ô một dòng, theo tám trục.** Tám trục này là chỗ specs thật hay có luật riêng, và cũng là chỗ người viết spec hay tự nghĩ ra nhất:

| # | Trục | Hỏi bản thân | Ví dụ ô |
|---|---|---|---|
| 1 | **Ngưỡng** | con số nào chia hai hành vi? | mốc miễn phí, mức tối thiểu, hạn mức |
| 2 | **Bậc** | ngưỡng đó **phẳng hay chia hạng**? | theo hạng thành viên / theo vùng / theo loại hàng |
| 3 | **Trần & khoảng** | mỗi ô nhập cho nhập từ đâu tới đâu? | số lượng, số tiền, số dòng |
| 4 | **Đơn vị & bội số** | dùng theo bội số bao nhiêu, làm tròn hướng nào? | điểm, tiền lẻ, phút |
| 5 | **Định dạng** | độ dài, tập ký tự, có phân biệt hoa thường? | mã, mã số, SĐT |
| 6 | **Mốc thời gian** | luật xét tại **thời điểm nào**, và có mấy mốc? | lúc áp / lúc chốt / lúc giao |
| 7 | **Thứ tự** | nhiều điều kiện cùng hỏng thì theo thứ tự nào? | thứ tự kiểm, thứ tự dòng trong bảng tiền |
| 8 | **Kết hợp** | hai thứ cùng loại dùng chung được không, mấy cái? | phiếu, ưu đãi, phương thức |
| 9 | **Số phần tử của danh sách đóng** | danh sách này có **đúng mấy** phần tử? | mấy loại phiếu · mấy trạng thái đơn · mấy điều kiện kiểm · mấy dòng trong bảng tiền |

Trục 9 là trục hay bị bỏ nhất vì nó trông như đã biết. Đội thi thử 11/09 viết "có 2 loại phiếu" trong khi thật ra có 3, và viết một bảng điều kiện kiểm thiếu hẳn một dòng mà họ không biết là tồn tại. Một danh sách đóng **thiếu phần tử** không hiện ra ở bất cứ cổng kiểm nào — chỉ có hỏi thẳng "có mấy…" mới lộ.

**Bước 3 — mỗi dòng ghi ba cột:** `Ô | Mặc định ngành (hoặc "KHÔNG BIẾT") | Nguồn (tên sản phẩm có thật / suy từ đâu)`. Cột giữa ghi `KHÔNG BIẾT` là **kết quả hợp lệ và quan trọng nhất** — nó tự động đẩy ô đó lên đầu hàng đợi hỏi.

**Bước 4 — hiệu chỉnh thiên lệch.** Người viết spec đoán **rộng và đẹp**; nghiệp vụ thật **hẹp và tuỳ tiện**. Soát lại từng dòng vừa viết theo bảng dưới, dòng nào rơi vào cột trái thì sửa về cột phải hoặc hạ xuống `KHÔNG BIẾT`:

| Thiên lệch của người viết | Thực tế thường gặp |
|---|---|
| khoảng **rộng** cho an toàn (`1〜99`) | khoảng **hẹp**, thường là dropdown ngắn (`1〜10`) |
| độ dài **linh hoạt** (`≤20 ký tự`) | độ dài **cố định** (`đúng 12 ký tự`) |
| kiểm ở **nhiều mốc** cho chắc | kiểm ở **một mốc** duy nhất, và mốc đó là dữ kiện nghiệp vụ |
| đơn vị **mịn** (`1 điểm`) | đơn vị **thô** theo bội số (`100 điểm`) |
| ngưỡng **phẳng**, một con số | ngưỡng **chia bậc** theo hạng/vùng |
| một **công thức** suy ra được (`trần = tổng − phí ship`) | một **tỷ lệ hoặc con số áp đặt** (`50% của小計`) |
| danh sách **gọn** (2 loại) | danh sách **dài hơn ta nghĩ** (3 loại) |

Nếu một ô có hai phương án và ta đang chọn phương án "kỹ sư" ở cột trái ⇒ ô đó là ứng viên hạng đầu của hàng đợi hỏi, **không** phải ô tự điền.

## 4c. Quét hằng số — bắt buộc, chạy trước khi xếp hàng đợi

**Mọi con số sẽ xuất hiện trong bản nộp đều phải có một dòng RTM trước khi được gõ ra.** Phần lớn hằng số không nằm ở mục 6 (nơi mọi người soi) mà nằm rải ở **mục 2 và mục 4** — nơi không ai soi, và là nơi 6/19 lỗi của bản thi thử 11/09 nằm.

Cách chạy (3 phút, ngay sau §4b):

1. Duyệt khung 10 mục (33 §1) và liệt kê **mọi ô sẽ cần một con số**, không cần biết giá trị:
   - **mục 2** — mỗi ô nhập: trần/sàn, độ dài, tập ký tự, bước nhảy, giá trị mặc định, điều kiện ẩn/disable; mỗi cột tiền: chưa thuế hay đã thuế.
   - **mục 4** — số lượng điều kiện, thứ tự, mã của từng điều kiện.
   - **mục 6** — mọi ngưỡng, tỷ lệ, bội số, hướng làm tròn, mốc thời gian, thứ tự dòng của mỗi bảng tiền.
   - **mọi mục** — **số phần tử của từng danh sách đóng** (trục 9 §4b): mấy loại, mấy trạng thái, mấy điều kiện, mấy dòng. Đây là hằng số ẩn: nó không xuất hiện dưới dạng chữ số trong spec, nên không cổng nào bắt được — chỉ có hỏi mới biết.
   - **mục 7** — mọi hạn (ngày giữ, hạn huỷ).
2. Mỗi ô thành một dòng: `Ô | tra §4b thấy gì | KHÔNG BIẾT? | hạng ưu tiên hỏi`.
3. Dòng `KHÔNG BIẾT` **lên đầu hàng đợi**, trước mọi câu hỏi quan hệ.

**Xếp hàng đợi theo XÁC SUẤT Ô TỒN TẠI, không theo dạng câu.** Đây là bản sửa sau drill "lich-hen" (12 lượt đo thật) — cách phát biểu cũ "hằng số luôn đi trước quan hệ" **sai**: đo được hai lượt mà câu quan hệ trả về nhiều dữ kiện hơn câu hằng số cùng ô.

Thứ quyết định là **specs thật có ô đó hay không**:

| Hạng | Loại ô | Vì sao | Đo được ở drill |
|---|---|---|---|
| 1 | **Hằng số** (ngưỡng, trần, độ dài, bội số, mốc thời gian, số phần tử danh sách) | spec nào cũng phải ghi ra con số ⇒ gần như luôn có câu trả lời | 6/6 lượt có dữ kiện |
| 2 | **Quan hệ giữa một khái niệm và chính nó** ("huỷ lúc nào được", "mốc xét ở đâu") | vẫn là một ô spec có | 3/3 có dữ kiện, một lượt còn lộ thêm 2 hằng số |
| 3 | **Giao giữa HAI khái niệm** ("phí huỷ có áp cho loại hẹn X không", "điểm có trừ vào phí Y không") | đây là chỗ spec thật hay **không phủ** ⇒ trả về deflection | 1/2 trả về `Không có quy định riêng.` |

Lỗi thật 11/09 nằm đúng hạng 3: hỏi "điểm có trừ vào phí ship không" (giao điểm × phí ship) nhận deflection, rồi đội tự chế trần. Ô cần biết là hạng 1 — *trần bao nhiêu, bội số bao nhiêu* — và hạng 1 thì luôn có câu trả lời.

**Vẫn giữ: con số sai thì cả cụm luật quanh nó sai.** Biết `判定額 ≥ しきい値 ⇒ miễn ship` mà `しきい値` sai là hỏng cả cụm. Bản thi thử hỏi 5 câu hạng 2–3, **0 câu hạng 1**, và 11/19 lỗi là hằng số.

**Nhận deflection ⇒ đổi hạng, đừng đổi cách nói.** Một câu hạng 3 trả về "không có quy định riêng" nghĩa là ô đó không tồn tại trong specs thật — hỏi lại cùng ô bằng câu khác cũng vô ích. Việc phải làm là tìm **ô hạng 1 nằm gần nó**: `điểm có trừ vào phí ship không` (hạng 3, deflection) → `trần dùng điểm là bao nhiêu` + `bội số bao nhiêu` (hạng 1, cả hai đều có đáp án).

**Cảnh giác câu trả lời SUY DIỄN.** Đo được ở drill: hỏi một ô hạng 3, khách trả lời `Được — trong bảng tiền có cả dòng này lẫn dòng kia`. Đó là khách **suy ra từ một chỗ khác**, không phải luật tường minh. Ghi vào RTM thì đánh dấu là suy diễn, **không** dùng làm bằng chứng kháng nghị, và nếu ô đó có tiền dính vào thì hỏi lại bằng một ô hạng 1.

## 5. Mẫu RTM ngược — có cả dòng giả định

Truy vết *đáp án AI Khách hàng → luật có mã trong spec*, **và** *giả định → luật*. Kể cả khi kịp 15–20 lượt, phần lớn spec vẫn không có nguồn từ AI Khách hàng; RTM vì thế có hai loại dòng và không loại nào được để trống cột "Mã BR".

| ID | Nguồn | Nội dung rút gọn (≤12 từ) | ⚠? | Rủi ro | Mã BR trong spec | Trạng thái | Timestamp |
|---|---|---|---|---|---|---|---|
| A-01 | C3 | TTL 120 phút, liên tục, gồm lễ | ⚠ | — | BR-04 | ✅ | 09:56 |
| A-02 | C4-8 | Guest KHÔNG được tạo lượt giữ | ⚠ | — | BR-11 | ✅ | 10:12 |
| A-03 | C2 | Hết hạn → EXPIRED, hoàn 100% cọc | ⚠⚠ | — | BR-07 | ✅ | 09:43 |
| G-01 | mặc định ngành (§4-8) | Giá khóa tại thời điểm tạo | — | Cao | BR-15 | ✅ | 10:35 |
| G-02 | mặc định ngành (10 §6) | Không có waitlist | — | TB | §1 NGOÀI | ✅ | 10:38 |

Quy tắc:

1. Dòng `A-xx` = có câu trả lời nguyên văn trong log (bằng chứng kháng nghị dùng được). Dòng `G-xx` = giả định, **không** phải bằng chứng, không được trích khi kháng nghị.
2. Ô "Mã BR" trống = lỗ hổng chắc bị bắn. Không nộp spec khi còn ô trống, kể cả ở dòng `G-xx`.
3. ⚠⚠ = khác mặc định ngành VÀ có con số/trạng thái cụ thể → viết BR trước tiên, thêm 1 ví dụ số vào spec.
4. Cột **Rủi ro** chỉ điền cho dòng `G-xx`, theo ba tiêu chí của `/spec-write` bước 14: đảo lại thì đổi kết quả quan sát được · Executor mù có đoán trùng không · có nằm trong core flow tiền/tồn không. Mười dòng `G-xx` rủi ro cao nhất là nguyên liệu của các lượt hỏi còn lại (§3.4) — chuyển sang dạng nhị phân trước khi gửi.
5. Trạng thái: ✅ đã viết · ✍ đang viết · ❌ thiếu · ⛔ ngoài phạm vi (không viết, dùng cho §1 spec).
6. Câu trả lời dạng liệt kê hoặc bảng nhiều hàng → **mỗi ý một dòng RTM**, không gộp (ý bị gộp sẽ không thành BR và bị catch-all 0.4/0.5 xử sai).
7. Một lượt xác nhận trả về "không đúng" ⇒ dòng `G-xx` tương ứng **chuyển thành `A-xx`** với nội dung mới, đánh ⚠⚠, và mở một việc sửa spec.
8. Sau 11:20, đếm: số dòng ⚠ có BR / tổng dòng ⚠ phải = 100%; số dòng `G-xx` rủi ro Cao **rớt dưới đường cắt** phải được liệt kê trong `review.md` như rủi ro đã biết.

## 6. Mẫu log hội thoại có timestamp (bằng chứng kháng nghị)

Tên file: `log-khach-hang.md`. **Một khối cho mỗi lượt**, kể cả lượt bị từ chối; dán nguyên văn, không tóm tắt. Hội thoại được xem lại (00 §A), nhưng log tự giữ vẫn cần: nó là thứ `/attack` và `/appeal` grep được.

```
## L04 — Mốc bắt đầu thời hạn
Thời gian gửi: 2026-09-12 09:33:15
Trạng thái: chấp nhận
Token trước lượt này: 420 / 4.000
Câu hỏi (nguyên văn):
<dán>
Câu trả lời (nguyên văn):
<dán>
Token sau lượt này: 560 / 4.000  (thật / ước — ghi rõ)
Trích rút → RTM: A-04 (thời hạn tính từ lúc cọc thành công) ⚠
```

Lượt bị từ chối ghi ngắn, **không cộng token**, nhưng vẫn chiếm một dòng để đếm nhịp đã tiêu:

```
## L09 — Message hết hạn  [BỊ TỪ CHỐI]
Thời gian gửi: 2026-09-12 09:38:00
Trạng thái: bị từ chối — câu hỏi chứa chỉ thị ("mỗi dòng ≤8 từ")
Token: không trừ · Nhịp: tiêu 1
Bản đã sửa gửi lại lúc: 09:38:40 → xem khối L09b
```

Quy tắc dùng khi kháng nghị (00 §A: chỉ ca CÔNG bị VÔ HIỆU):

1. Mỗi ca kháng nghị = trích đúng khối log (mã câu, timestamp, câu trả lời nguyên văn) + 2 câu lập luận về **phạm vi**.
2. Chỉ câu trả lời nguyên văn của AI Khách hàng mới là bằng chứng. Bản rút gọn trong RTM không dùng. Dòng `G-xx` (giả định) không dùng.
3. Chụp màn hình từng câu trả lời ngay khi nhận, đặt tên `C<n>_<hhmm>.png` — hội thoại được xem lại, nhưng ảnh chụp là bản không phụ thuộc hệ thống của BTC còn hoạt động lúc 16:00.
