*Dùng cho vai THỦ khi hỏi AI Khách hàng (5 câu, 9:30–11:20 ngày thi); buổi chiều dùng lại §2 và §4 để soi spec mình và spec đối thủ.*

# 20. Năm câu hỏi cho AI Khách hàng & ngân hàng nguyên liệu

## 1. Mười quy tắc hỏi khi chỉ có 5 câu, 1 câu/lượt, không memory

Tham số: **5 câu hỏi · mỗi lượt đúng 1 câu · 5.000 token cho cả hỏi + trả lời · AI Khách hàng KHÔNG có memory** (00 §A). Bộ quy tắc cũ ("gộp 5–8 câu một lượt") không còn dùng được: một lượt bây giờ là một câu hỏi.

| # | Quy tắc | Vì sao / Thay vì → Hỏi |
|---|---|---|
| 1 | **Một câu hỏi, nhưng một bảng trả lời.** Giới hạn là số *câu hỏi*, không phải số *dữ kiện*. Mỗi câu phải kết thúc bằng đúng một dấu `?` và xin về một bảng hoặc một danh sách đánh số | "TTL bao nhiêu phút?" (1 dữ kiện) → "Trả lời đúng một bảng `Tham số \| Giá trị \| Đơn vị \| Mốc`: các tham số sau có giá trị bao nhiêu: …?" (12 dữ kiện) |
| 2 | **Ép format và cap độ dài ngay trong câu đó.** Không có memory ⇒ không sửa được format ở lượt sau mà không mất một câu trong 5. Luôn có: dạng trả lời · số dòng tối đa · số từ tối đa mỗi ô · "không giải thích" | thiếu cap → một câu trả lời văn xuôi 900 token ăn 18% ngân sách và vẫn thiếu ô |
| 3 | **Mỗi câu tự chứa.** Cấm mọi tham chiếu tới lượt trước ("như đã nói", "câu trên", "bổ sung cho ý 3"). Nhắc lại tên tính năng và mọi thuật ngữ cần thiết trong chính câu đó | "Còn trường hợp thẻ đóng thì sao?" → AI không biết đang nói về hoàn cọc → mất một câu |
| 4 | **Xếp nửa giá trị cao lên trước trong cùng một câu.** Câu hai nửa (NGOÀI phạm vi + điều PHẢI ngăn) có thể bị trả lời hụt nửa sau; đặt nửa quan trọng hơn ở trước để phần bị hụt là phần rẻ hơn | — |
| 5 | **Không hỏi điều suy được.** 5 câu chỉ để lấy thứ mà (a) đảo lại thì đổi kết quả quan sát được — tiền / trạng thái cuối / ai thắng, và (b) Executor sẽ đoán khác nếu spec im lặng. Mọi tham số trùng mặc định ngành: tự điền theo §4, đừng hỏi | "Có audit log không?" (mặc định ngành: có, và không ai bắn vào đó) → bỏ |
| 6 | **Không "vì sao", không chào hỏi, không xin phép, không nhắc lại brief.** Token cho câu trả lời đắt hơn token cho câu hỏi, nhưng câu hỏi dài vẫn ăn vào cùng 5.000 | "Chúng tôi đang viết spec cho…, xin hỏi vì sao TTL là 2 giờ?" → bỏ cả câu |
| 7 | **Không dẫn dắt.** Đưa lựa chọn cân bằng, không cài đáp án kỳ vọng — câu dẫn dắt làm mất giá trị bằng chứng của câu trả lời khi kháng nghị | "Chắc guest không được giữ hàng nhỉ?" → "Khách chưa đăng nhập tạo lượt giữ được không; nếu được thì có rào gì?" |
| 8 | **Câu cuối là câu restate, và chỉ soạn sau khi có bản nháp spec.** Nó phải nhắm vào giả định đã thật sự vào spec (bảng xếp hạng rủi ro của `/spec-write`), không nhắm vào kế hoạch soạn hôm trước. Phát biểu có con số + trạng thái, xin Đúng/Sai, ý "Sai" xin giá trị đúng ≤8 từ | 10 phát biểu ≈ 380 token hỏi + 150 token trả lời = **10 dữ kiện đã kiểm với ~530 token** — mật độ tốt nhất trong cả 5 câu |
| 9 | **Mặc định KHÔNG dùng ảnh.** Được 3 lần, nhưng một ảnh chụp bảng ≈ 1.000–1.600 token, còn chính bảng đó viết bằng text ≈ 400–600 token. Ảnh chỉ thắng khi BTC xác nhận **ảnh không tính vào 5.000 token** (00 §A2-1) — lúc đó gửi ảnh bảng 25 giả định và xin về "dòng nào sai" là cách restate rẻ nhất trong ngày | Hỏi BTC trước 9:30. Nếu ảnh có tính token: 3 lần đó bỏ không dùng, không tiếc |
| 10 | **Cổng 5 kiểm tra trước khi gửi từng câu.** Không có memory nghĩa là không có lần thử thứ hai: (1) đúng một dấu `?`; (2) không tham chiếu lượt trước; (3) có ép format + cap dòng/từ; (4) câu trả lời tệ nhất có thể vẫn dùng được (không phải một chữ "Có"); (5) mọi thuật ngữ trong câu đều là từ của brief, không phải từ riêng của đội | Câu bị hụt = mất 20% tri thức chắc chắn của cả ngày |

**Ngân sách:** ước `token ≈ max(số từ × 2,5 ; số ký tự / 2,2)` (00 §A1). Sau mỗi câu, ghi token thật (nếu giao diện hiển thị) hoặc token ước vào log; cộng dồn. Vượt kế hoạch ở câu 2–3 thì cắt phần liệt kê của câu 4, **không cắt câu 5**.

**Quy tắc phụ:** mỗi câu trả lời khác mặc định ngành → ghi ⚠ ngay vào RTM (§5) và log (§6). Với 4 câu dữ liệu, số dòng ⚠ thu được thường chỉ 8–20 dòng — nhỏ hơn kit cũ giả định (111 dòng ở diễn tập 08/09). Phần còn lại của spec là giả định có xếp hạng, không phải lỗ hổng quy trình.

## 2. Ngân hàng câu hỏi (158 câu, 20 nhóm N0–N19) — hai công dụng mới

Chỉ 5 câu được gửi đi thật, nên ngân hàng này **không còn là danh sách để hỏi**. Nó có hai việc:

1. **Nguyên liệu để gọt 5 câu ở §3.** Mỗi câu trong 5 câu gộp 8–15 ID của ngân hàng thành một bảng. Bảng ánh xạ ID → câu nào: cuối §3.
2. **Danh sách phải-tự-điền.** Mọi ID không được câu nào trong 5 câu phủ tới là một ô spec vẫn phải có luật. Điền bằng **mặc định ngành** (§4 + 10 §6), gắn `[GIẢ ĐỊNH]`, xếp hạng rủi ro, và ứng viên rủi ro cao nhất đi vào câu 5. Không bỏ trống, không sáng tạo giá trị mới (30 §1b).

> **N15–N19 (49 câu, thêm 11/09)** phục vụ bốn vùng hoàn toàn mới của cấu trúc spec BTC 10 mục (knowledge/33 §1): mục 2 item màn hình, mục 3 event, mục 4 validation & message lỗi, mục 9 API, mục 7 ràng buộc/bất thường/chưa chốt. Với hạn mức 5 câu, **phần lớn N15–N19 phải tự điền** — xem bảng phủ cuối §3 và khối §3b.

Định dạng dòng: **ID** [Ưu tiên·Kiểu] câu hỏi. Kiểu: **S** = số có đơn vị · **B** = bảng · **ĐS** = Đúng/Sai · **DS** = danh sách đóng (chọn 1 hoặc liệt kê tên). Mã lỗ hổng từng câu chắn: xem 50 §1/§3.

### N0. Mục tiêu, ràng buộc, quyền sở hữu thất bại

Nhóm này **không hỏi tham số** — nó hỏi bài toán. Sinh ra từ `knowledge/05-hieu-bai-toan.md` §1 (khối M1, M2, M3, M5) và §4. Mật độ ⚠ cao nhất trong toàn ngân hàng vì đây là chỗ specs thật hay có luật mà brief không kể. **Bắt buộc có mặt trong 5 câu**: N0-01 (vào C1), N0-02 (vào C3), N0-05 và N0-07 (vào C3/C4).

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

## 3. Năm câu hỏi — bản soạn sẵn, copy-paste

Ngân sách: **5.000 token** cho cả 5 câu (hỏi + trả lời). Kế hoạch dưới ước ~3.500 token, chừa ~1.500 cho câu trả lời dài hơn dự kiến. Gửi C1–C4 xong trước **10:20**; C5 gửi lúc **11:05**, sau khi có bản nháp spec.

| Câu | Nội dung | Ước token (hỏi + trả lời) | Phủ ID §2 | Nếu mất câu này |
|---|---|---|---|---|
| C1 | Phạm vi NGOÀI + điều PHẢI ngăn | 120 + 350 | N1-02, N0-01 (N1-03 suy từ brief) | Mọi test buổi chiều +1 điểm phạm vi (50 §6-5); §1 spec phải tự đặt |
| C2 | Bảng chuyển trạng thái đầy đủ | 300 + 550 | N7-04/09/10, N5-04/07, N8-01/03, N4-09, N12-01 | §5 và nửa §6 thành giả định; đây là câu **không được bỏ** |
| C3 | Bảng tham số | 400 + 550 | N2-01…08, N3-01/02, N5-01/05, N0-02/12, N4-06, N2-12 | Mọi con số thành mặc định ngành; biên TTL/qty thành đạn cho đối thủ |
| C4 | Tám kịch bản suy biến & thất bại phụ thuộc ngoài | 430 + 280 | N0-07/09/10, N12-03, N4-04/12, N9-01 | Loại lỗ hổng #16–#19 trên spec mình không kiểm được |
| C5 | Restate Đúng/Sai từ bảng xếp hạng rủi ro | 380 + 150 | tùy bản nháp | Mọi dòng `[GIẢ ĐỊNH]` vẫn là suy luận một chiều |

**Thứ tự gửi không phụ thuộc nhau** (AI không có memory) — C1, C2, C3, C4 gửi liên tiếp không cần chờ câu trước. Chỉ C5 phụ thuộc bản nháp spec.

### C1 — Phạm vi & điều cấm (9:30)

```
Trả lời đúng hai danh sách đánh số, mỗi danh sách tối đa 8 dòng, mỗi dòng ≤8 từ, không giải thích: tính năng "<TÊN TÍNH NĂNG THEO BRIEF>" có những nghiệp vụ nào NGOÀI phạm vi, và có những điều nào tính năng PHẢI ngăn không cho xảy ra?
```

Nửa "NGOÀI phạm vi" đặt trước vì hai lý do: nó không suy được từ đâu khác, và **chưa hỏi nó thì mọi test buổi chiều bị +1 điểm phạm vi** (50 §6-5). Nửa "PHẢI ngăn" nuôi bảng Mục tiêu↔Luật (05 §2) — nhóm có mật độ ⚠ cao nhất trong ngân hàng.

Danh sách **TRONG phạm vi** (N1-03) cố tình không hỏi: nó suy được từ brief, và §1 spec viết được bằng "những gì brief mô tả + mọi nghiệp vụ có luật trong tài liệu này". Hệ quả phải biết khi bắn: bằng chứng phạm vi cho test sẽ là **câu brief (mức 2)** thay vì danh sách TRONG (mức 1) — 50 §2-9.

### C2 — Bảng chuyển trạng thái (9:40)

```
Trả lời đúng một bảng, mỗi ô ≤6 từ, không giải thích, cột: Trạng thái hiện tại | Sự kiện | Trạng thái mới | Tồn kho | Tiền cọc | Ai được thông báo — một lượt giữ hàng chuyển trạng thái thế nào khi xảy ra từng sự kiện: đạt hết hạn, khách hủy, nhân viên hoặc admin hủy, thanh toán cọc thành công, thanh toán cọc thất bại hoặc timeout, chuyển thành đơn hàng, yêu cầu gia hạn, tồn kho bị điều chỉnh xuống dưới số đang giữ, sản phẩm ngừng bán?
```

Câu đắt nhất và lãi nhất: một câu trả về tên tập trạng thái, mọi chuyển tiếp, hướng tồn, hướng tiền và người nhận thông báo — tức §5 spec cộng phần lớn §6. Không cắt câu này để nhường token cho câu khác.

### C3 — Bảng tham số (9:55)

```
Trả lời đúng một bảng "Tham số | Giá trị | Đơn vị | Mốc hoặc điều kiện", tối đa 14 dòng, không giải thích, tham số nào không có giới hạn thì ghi "không giới hạn": trong tính năng "<TÊN TÍNH NĂNG>", các tham số sau nhận giá trị nào — thời hạn giữ và mốc bắt đầu tính; đồng hồ chạy liên tục hay chỉ trong giờ làm việc, và múi giờ chuẩn; số lần được gia hạn, mỗi lần thêm bao lâu, cộng dồn hay tính lại từ lúc gia hạn; mức cọc; phần trăm cọc được hoàn theo từng lý do kết thúc và số ngày tiền về tay khách; số lượng tối đa một lượt giữ; số lượt giữ đang mở tối đa cho một khách; đơn vị neo hạn mức là tài khoản đăng nhập, số điện thoại đã xác thực, thiết bị hay phương tiện thanh toán; tồn đệm không được đem giữ; chu kỳ chạy của job dọn hết hạn?
```

Ba ô quan trọng hơn phần còn lại: **mốc bắt đầu** thời hạn (biên off-by-one, probe P4), **đơn vị neo hạn mức** (cổng F2 / loại lỗ hổng #18 — spec neo vào email tự khai là hạn mức trang trí), **số ngày tiền về tay khách** (cổng F1 / loại #16 — tách mốc quyết định khỏi mốc hoàn tất).

### C4 — Tám kịch bản suy biến (10:10)

```
Trả lời theo số, mỗi dòng ≤12 từ, ghi rõ trạng thái cuối của lượt giữ, tồn kho và tiền, không giải thích: trong tính năng "<TÊN TÍNH NĂNG>", hệ thống xử lý ra sao khi (1) cổng thanh toán báo thành công hai lần cho cùng một giao dịch; (2) cổng gửi "thất bại" rồi "thành công" của cùng giao dịch nhưng đến sai thứ tự; (3) hoàn cọc về phương thức gốc thất bại vì thẻ đã đóng; (4) job dọn hết hạn ngừng chạy 4 giờ rồi khách chốt đơn một lượt giữ đã quá hạn; (5) hệ thống kiểm thấy còn hàng nhưng khi ghi giữ thì tồn đã bị giao dịch khác lấy; (6) kiểm kê hạ tồn xuống dưới số đang giữ của một lượt giữ đã cọc; (7) hai khách gửi yêu cầu giữ đơn vị cuối cùng cách nhau vài mili-giây; (8) khách chưa đăng nhập tạo lượt giữ?
```

Tám ca này là 8/12 kịch bản suy biến bắt buộc (05 §M6) và là toàn bộ vùng đạn của loại lỗ hổng #16–#19 (50 §1). Hết token thì cắt ca (7) và (8) — hai ca này có mặc định ngành đủ an toàn để tự điền (FCFS theo thời điểm server; guest bị chặn hoặc bị rào định danh).

### C5 — Restate (11:05, sau bản nháp spec)

```
Trả lời Đúng/Sai theo số, ý nào Sai ghi giá trị đúng ≤8 từ, không giải thích: trong tính năng "<TÊN TÍNH NĂNG>", các phát biểu sau đúng hay sai — 1. <phát biểu có con số>; 2. …; … 10. <phát biểu có con số>?
```

Nguồn phát biểu, ưu tiên giảm dần:
1. Mười dòng đầu **bảng xếp hạng rủi ro giả định** của `/spec-write` (giả định đảo lại thì đổi tiền / trạng thái cuối / ai thắng, và ngược mặc định ngành).
2. Mọi BR bị `/spec-review` gắn nhãn `⚡` (làm hỏng mục tiêu brief) hoặc ✗ ở cổng F2/F7.
3. Ô mô hình M1/M2/M5 còn `?` sau C1–C4.
4. Nếu còn chỗ: phát biểu từ §4 chưa được C1–C4 trả lời, chọn theo mức "Nếu Sai ⇒ BR về" có tiền hoặc tồn dính vào.

Mỗi phát biểu **phải có con số hoặc trạng thái cụ thể** — "TTL 120 phút tính từ lúc tạo, chạy cả ngày lễ" chứ không phải "TTL hợp lý". Xếp phát biểu rủi ro cao lên đầu vì câu trả lời có thể bị cắt giữa.

### Ánh xạ ID §2 → câu, và phần phải tự điền

| Nhóm §2 | C1–C5 phủ | Phải tự điền bằng mặc định ngành (§4, 10 §6) |
|---|---|---|
| N0 | 01, 02, 05, 07, 09, 10, 12 | 03, 04, 06 (một phần), 08, 11, 13, 14 |
| N1 | 02 | 01, **03**, 04, 05, 06, 07, 08, 09, 10 |
| N2 | 01, 02, 03, 05, 07, 08, 12 | 04, 06, 09, 10, 11, 13, 14 |
| N3 | 01, 02 | 03, 04, 05, 06, 07 |
| N4 | 04, 06, 09, 12 | 01, 02, 03, 05, 07, 08, 10, 11, 13 |
| N5 | 01, 04, 05, 07 | 02, 03, 06, 08, 09, 10, 11 |
| N6 | — | toàn nhóm (giá khóa hay tính lại: mặc định ngành = khóa tại lúc tạo) |
| N7 | 01, 04, 09, 10 | 02, 03, 05, 06, 07, 08 |
| N8 | 01, 03 | 02, 04, 05, 06 |
| N9 | 01 | 02…11 |
| N10–N14 | — | toàn nhóm |
| N15 (item màn hình) | — | toàn nhóm; mục 2 viết bằng mặc định UI + brief, trừ 06 (guest) nếu đổi được một nửa câu — xem §3b |
| N16 (event) | — | toàn nhóm; 03 (double-click) đã nằm trong C4 ca (1)/(2) qua luật 0.10 |
| N17 (validation & message) | — | toàn nhóm. **Đây là hở lớn nhất của kế hoạch 5 câu** — xem §3b |
| N18 (API) | — | toàn nhóm; 03/06 (thất bại phụ thuộc ngoài) đã được C4 phủ gián tiếp |
| N19 (ràng buộc, bất thường) | — | 01, 02 nằm trong C4 ca (5) và ca (1)/(2); còn lại tự điền |

Đây là bảng phải in ra và dán lên bàn: **~70% ngân hàng câu hỏi không được hỏi**. Mỗi ô ở cột phải là một luật vẫn phải viết, bằng mặc định ngành, có gắn `[GIẢ ĐỊNH]`, và là ứng viên cho C5 nếu rủi ro cao.
## 3b. Hở mới sau tài liệu BTC 11/09 — quyết định trước 9:30

Cấu trúc 10 mục thêm bốn vùng bắt buộc (mục 2, 3, 4, 9) mà kế hoạch C1–C5 ở §3 **không phủ**, vì C1–C5 được soạn khi template còn là 11 mục luật nghiệp vụ. Hai vùng trong đó là chỗ Executor đoán sai nhiều nhất (50 §1 loại #22 message, #23 guest):

| Vùng | Tự điền được không | Rủi ro nếu tự điền |
|---|---|---|
| Mục 2 item, mục 3 event | **Được** — suy từ brief + mặc định UI; sai thì cũng chỉ sai hiển thị | Thấp–TB |
| Mục 9 API | **Được** — C4 đã hỏi ca thất bại phụ thuộc ngoài, phần còn lại là hình thức | Thấp |
| **Mục 4 message lỗi nguyên văn** (N17-01/03/04/05) | Không suy được — chuỗi nguyên văn là dữ kiện, không phải mặc định ngành | **Cao**: đối thủ bắn "hiện message gì" là loại đạn rẻ nhất của họ |
| **Guest vs login** (N15-06) | Một phần — mặc định ngành là chặn guest hoặc rào định danh | TB–Cao |

**Ba phương án, chọn một trước 9:30** (không có phương án nào miễn phí — hạn mức là 5 câu):

1. **Giữ C1–C5 như §3.** Mục 4 điền message do đội tự viết. Theo 30 §1b-2, giá trị tự nghĩ ra là ca *duy nhất* tệ hơn im lặng — nhưng với message thì im lặng cũng mất điểm vì BTC bắt mục 4 phải có message nguyên văn. Chấp nhận hở; dồn phòng thủ vào catch-all và bảng Case.
2. **Đổi nửa sau của C1** ("điều PHẢI ngăn") **thành bảng message** (N17-01 + N17-03/04/05 + N17-02). Mất nguyên liệu bảng Mục tiêu↔Luật (05 §2), phải suy "điều PHẢI ngăn" từ brief.
3. **Cắt ca (7) và (8) của C4** (hai ca có mặc định ngành an toàn: FCFS theo thời điểm server; guest bị chặn) **và nối một nửa câu message** vào C4. Rẻ nhất về mất mát, nhưng C4 thành câu hai chủ đề — rủi ro AI trả lời hụt nửa sau (luật 4 §1).

**Đề xuất: phương án 3.** Nửa câu dán được:

```
… (6) kiểm kê hạ tồn xuống dưới số đang giữ của một lượt giữ đã cọc; và hệ thống hiển thị message lỗi nguyên văn nào trong ba trường hợp: vượt tồn khả dụng, lượt giữ đã hết hạn, thao tác trên lượt giữ đã hủy?
```

Nếu chọn phương án 1, ghi ngay bốn dòng `G-xx` vào RTM cho N17-01/03/04/05 và xếp hạng rủi ro cao để chúng vào danh sách phát biểu C5.

## 4. Ba mươi mốt phát biểu Đúng/Sai (mặc định phổ biến e-commerce)

Bảng này có **hai công dụng, cả hai đều quan trọng hơn trước**:

1. **Nguồn giá trị mặc định để tự điền.** Cột "Phát biểu" là mặc định ngành. Ô nào không hỏi được thì spec viết đúng theo phát biểu đó và gắn `[GIẢ ĐỊNH]` — vì đó cũng chính là điều Executor mù sẽ đoán khi spec im lặng, nên viết ra không làm tăng rủi ro mà loại được đa nghĩa (lý lẽ đầy đủ: 30 §1b).
2. **Kho phát biểu cho câu C5.** Chọn phát biểu để restate theo cột 3: ưu tiên dòng có **tiền hoặc tồn** dính vào.

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

## 5. Mẫu RTM ngược — có cả dòng giả định

Truy vết *đáp án AI Khách hàng → luật có mã trong spec*, **và** *giả định → luật*. Với 5 câu hỏi, phần lớn spec không có nguồn từ AI Khách hàng; RTM vì thế có hai loại dòng và không loại nào được để trống cột "Mã BR".

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
4. Cột **Rủi ro** chỉ điền cho dòng `G-xx`, theo ba tiêu chí của `/spec-write` bước 14: đảo lại thì đổi kết quả quan sát được · Executor mù có đoán trùng không · có nằm trong core flow tiền/tồn không. Mười dòng `G-xx` rủi ro cao nhất là nguyên liệu của C5.
5. Trạng thái: ✅ đã viết · ✍ đang viết · ❌ thiếu · ⛔ ngoài phạm vi (không viết, dùng cho §1 spec).
6. Câu trả lời dạng liệt kê hoặc bảng nhiều hàng → **mỗi ý một dòng RTM**, không gộp (ý bị gộp sẽ không thành BR và bị catch-all 0.4/0.5 xử sai).
7. Câu trả lời C5 nói "Sai" ⇒ dòng `G-xx` tương ứng **chuyển thành `A-xx`** với nội dung mới, đánh ⚠⚠, và mở một việc sửa spec.
8. Sau 11:20, đếm: số dòng ⚠ có BR / tổng dòng ⚠ phải = 100%; số dòng `G-xx` rủi ro Cao chưa qua C5 phải được liệt kê trong `review.md` như rủi ro đã biết.

## 6. Mẫu log hội thoại có timestamp (bằng chứng kháng nghị)

Tên file: `log-khach-hang.md`. **Một khối cho mỗi câu trong 5 câu**; dán nguyên văn, không tóm tắt. Hội thoại được xem lại (00 §A), nhưng log tự giữ vẫn cần: nó là thứ `/attack` và `/appeal` grep được.

```
## C3 — Bảng tham số
Thời gian gửi: 2026-09-12 09:55:20
Token trước câu này: 950 / 5.000
Câu hỏi (nguyên văn):
<dán>
Câu trả lời (nguyên văn):
<dán>
Token sau câu này: 1.900 / 5.000  (thật / ước — ghi rõ)
Trích rút → RTM: A-07 (TTL 120), A-08 (gia hạn 1 lần, +30 phút) ⚠, A-09 (cọc 10%)
```

Quy tắc dùng khi kháng nghị (00 §A: chỉ ca CÔNG bị VÔ HIỆU):

1. Mỗi ca kháng nghị = trích đúng khối log (mã câu, timestamp, câu trả lời nguyên văn) + 2 câu lập luận về **phạm vi**.
2. Chỉ câu trả lời nguyên văn của AI Khách hàng mới là bằng chứng. Bản rút gọn trong RTM không dùng. Dòng `G-xx` (giả định) không dùng.
3. Chụp màn hình từng câu trả lời ngay khi nhận, đặt tên `C<n>_<hhmm>.png` — hội thoại được xem lại, nhưng ảnh chụp là bản không phụ thuộc hệ thống của BTC còn hoạt động lúc 16:00.
