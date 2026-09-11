*Dùng cho người cầm vai THỦ khi hỏi AI Khách hàng 9:30–11:00 ngày thi; buổi chiều dùng lại §2 (đối chiếu mã lỗ hổng ở 50 §1/§3) để soi spec mình và spec đối thủ.*

# 20. Ngân hàng câu hỏi & kỹ thuật hỏi AI Khách hàng

## 1. Mười quy tắc hỏi AI Khách hàng có hạn mức token

| # | Quy tắc | Thay vì hỏi → Hỏi |
|---|---|---|
| 1 | Gộp 5–8 câu cùng chủ đề vào 1 lượt, đánh số, yêu cầu trả lời theo số | "TTL bao lâu?" (1 lượt, 1 ý) → "Trả lời theo số: 1. TTL (phút); 2. mốc bắt đầu; 3. số lần gia hạn; …" |
| 2 | Câu đầu lượt luôn ép format + cap độ dài: "Trả lời dạng bảng, mỗi ô ≤10 từ, không giải thích" | "Hết hạn thì sao?" → "Bảng: sự kiện hết hạn → trạng thái mới / tồn kho / cọc / ai được báo" |
| 3 | Câu đóng (closed question) là mặc định: chỉ chấp nhận số, Đúng/Sai, hoặc 1 phương án trong danh sách cho sẵn. Câu mở chỉ dùng để lấy **danh sách đóng** (liệt kê trạng thái, liệt kê NGOÀI phạm vi) | "Guest dùng thế nào?" → "Guest tạo hold được không? Đúng/Sai" |
| 4 | Con số phải kèm **đơn vị + mốc bắt đầu + bao gồm/không bao gồm mốc** | "TTL bao lâu?" → "TTL = ? phút; tính từ sự kiện nào; đúng phút thứ N hold còn hiệu lực?" |
| 5 | Hỏi NGOÀI phạm vi trước khi hỏi trong; xin danh sách tên, không mô tả | "Tính năng gồm gì?" → "Liệt kê ≤10 nghiệp vụ NGOÀI phạm vi, mỗi dòng ≤8 từ" |
| 6 | Không hỏi "vì sao", "mục đích", không chào hỏi, không nhắc lại ngữ cảnh — token cho câu trả lời đắt hơn câu hỏi | "Vì sao TTL 2 giờ?" → bỏ câu này |
| 7 | Không dẫn dắt (leading): không cài đáp án kỳ vọng vào câu hỏi; đưa lựa chọn cân bằng | "Chắc guest không được giữ hàng nhỉ?" → "Guest tạo hold: Được / Không được?" |
| 8 | Xác nhận bằng phát biểu lại (restate): cuối buổi gói 8–10 phát biểu rút từ câu trả lời trước, xin Đúng/Sai; ý "Sai" hỏi lại ngay trong cùng lượt | "Tôi hiểu đúng chưa?" → "Đúng/Sai từng ý: 1. TTL 120 phút liên tục; 2. cọc 10%; …" |
| 9 | Câu trả lời có từ mơ hồ ("sớm", "hợp lý", "tùy") hoặc thuật ngữ mới → bắt định nghĩa 1 dòng hoặc con số ngay lượt sau | AI nói "hoàn cọc sớm" → "'Sớm' = tối đa ? ngày làm việc?" |
| 10 | Đặt P0 trước, P2 sau; đánh dấu câu bỏ được khi hết token; kết mỗi lượt bằng "Còn quy tắc nào về <chủ đề> chưa hỏi? Liệt kê tên" | Hỏi tuần tự theo cảm tính → theo 7 lượt ở mục 3 (thứ tự L1→L2→L3→L7→L4→L5→L6), giữ 8% token cho lượt restate |

Quy tắc phụ: mỗi câu trả lời khác lẽ thường e-commerce → ghi ⚠ ngay vào RTM (mục 5) và log (mục 6), không chờ hết lượt [HD §2.3].

## 2. Ngân hàng câu hỏi (152 câu, 19 nhóm)

> N15–N19 phục vụ **cấu trúc spec BTC 10 mục** (knowledge/32): mục 2 item màn hình, mục 3 event, mục 4 validation & message, mục 9 API, mục 7 ràng buộc/bất thường/chưa chốt. Không hỏi bốn vùng này = spec trống bốn mục.

Định dạng dòng: **ID** [Ưu tiên·Kiểu] câu hỏi. Kiểu: **S** = số có đơn vị · **B** = bảng · **ĐS** = Đúng/Sai · **DS** = danh sách đóng (chọn 1 hoặc liệt kê tên). Mã lỗ hổng từng câu chắn: xem 50 §1/§3.

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

## 3. Bảy lượt hỏi đã batch sẵn (copy-paste)

Copy câu hỏi theo ID từ §2 vào sau preamble; giữ số thứ tự.

**Bảy lượt** (L7 mới, phục vụ mục 2/3/4/9 của cấu trúc BTC). Ngân sách theo % TOKEN_MAX (00 §A): L1 13% · L2 15% · L3 13% · L4 13% · L5 10% · L6 13% · **L7 15%** · Restate 8%.

Nếu TOKEN_MAX nhỏ, cắt theo thứ tự: L6 câu 9 → L1 câu 7 → L4 câu 9 → L6 câu 7 → L2 câu 5 → L7 câu 6. **Không cắt:** L1 câu 3, L2 câu 1–2, L3, **L7 câu 1–4** (message lỗi và khác biệt guest là hai vùng Executor đoán sai nhiều nhất).

Thứ tự chạy đề xuất: L1 → L2 → L3 → **L7** → L4 → L5 → L6 → Restate. Đưa L7 lên sớm vì mục 2/4 cần nhiều dữ kiện nguyên văn, khó bịa lúc viết.

### Lượt 1 — Phạm vi TRONG/NGOÀI

```
Trả lời dạng danh sách đánh số, mỗi dòng ≤10 từ, không giải thích.
1. N1-01  2. N1-03  3. N1-02  4. N1-04 (+N1-05)  5. N1-06  6. N9-03  7. N1-10  8. N1-07
```

### Lượt 2 — State machine

```
Trả lời dạng bảng, không giải thích.
1. Liệt kê đúng tên các trạng thái của một hold.
2. Bảng chuyển trạng thái, cột: Trạng thái hiện tại | Sự kiện | Trạng thái mới | Tồn kho thay đổi | Tiền cọc | Ai được thông báo. Phủ đủ: hết hạn, khách hủy, CSKH/admin hủy, thanh toán thất bại, thanh toán thành công, thành đơn, hết hàng do lỗi kho, SKU ngừng bán.
3. Cặp (trạng thái × sự kiện) KHÔNG hợp lệ: ghi "từ chối".
4. N7-07  5. N8-03  6. N7-10
```

### Lượt 3 — Toàn bộ con số

```
Trả lời dạng bảng "Tham số | Giá trị | Đơn vị | Mốc/điều kiện", không giải thích. Không có giới hạn thì ghi "không giới hạn".
1. N2-01, N2-02, N2-03, N2-05, N2-04
2. N2-07, N2-08, N2-10
3. N3-01, N1-08, N3-02, N3-03
4. N5-01, N5-02, N5-05, N5-10
5. N2-12, N2-13
6. N10-02
7. N4-06, N4-03, N3-06, N9-08
8. N2-06
```

### Lượt 4 — Tồn kho, đồng thời, ưu tiên

```
Trả lời theo số, mỗi câu ≤2 dòng, chọn đúng 1 phương án khi có lựa chọn.
1. N4-01  2. N4-02, N11-03  3. N4-04, N4-05  4. N3-04  5. N4-07, N4-08
6. N4-09  7. N4-10  8. N4-12, N4-13  9. N14-01, N14-02, N14-03, N14-04
```

### Lượt 5 — Đúng/Sai xác nhận giả thuyết

```
Trả lời Đúng/Sai theo số, không giải thích. Nếu "tùy", ghi "Tùy: <điều kiện ≤6 từ>".
[dán 30 phát biểu ở mục 4, giữ đúng số thứ tự]
```

### Lượt 6 — Ngoại lệ, actor, thông báo

```
Trả lời theo số, mỗi câu ≤2 dòng, không giải thích.
1. N5-08  2. N5-07  3. N5-11  4. N12-01  5. N12-02, N2-14  6. N9-01, N9-02
7. N9-04  8. N9-06, N9-10  9. N10-01, N10-03, N10-04, N10-02
10. Còn quy tắc nào về tính năng chưa được hỏi? Liệt kê tên, không mô tả.
```

### Lượt 7 — Màn hình, event, validation, tích hợp (cấu trúc BTC mục 2, 3, 4, 9)

```
Trả lời dạng bảng theo số, mỗi ô ≤12 từ, không giải thích. Message lỗi ghi NGUYÊN VĂN.
1. N15-01, N15-02
2. N15-05, N15-06
3. N17-01 (bảng: Item | Nội dung check | Message nguyên văn)
4. N17-03, N17-04, N17-05, N17-08
5. N17-02 (rule nào FE, nào BE, nào cả hai)
6. N16-01, N16-02
7. N16-03, N19-02
8. N15-03, N15-04, N15-07
9. N18-01, N18-02, N18-04
10. N18-03, N18-06
11. N19-01
12. N19-03, N19-04
```

### Lượt đệm — Restate (làm sau 10:45)

```
Đúng/Sai từng ý, không giải thích. Ý nào Sai, ghi giá trị đúng ≤8 từ.
[8–10 phát biểu viết lại từ các câu trả lời ⚠ trong RTM, kèm con số]
```

## 4. Ba mươi phát biểu Đúng/Sai (mặc định phổ biến e-commerce)

Nếu AI trả lời **Sai** ⇒ đó là ⚠ phản trực giác: viết ngay thành BR có mã trong spec (vai THỦ) và ghi vào danh sách đạn (vai CÔNG) [HD §2.3].

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

## 5. Mẫu RTM ngược

Truy vết *đáp án AI Khách hàng → luật có mã trong spec* [HD §2.4]. Điền ngay trong lúc hỏi; Red Teamer rà lúc 11:30.

| ID | Câu hỏi | Câu trả lời AI rút gọn (≤12 từ) | ⚠? | Mã BR trong spec | Trạng thái | Timestamp |
|---|---|---|---|---|---|---|
| A-01 | N2-01 | TTL 120 phút, liên tục, gồm lễ | ⚠ | BR-04 | ✅ | 09:41 |
| A-02 | N9-01 | Guest KHÔNG được tạo hold | ⚠ | BR-11 | ✅ | 09:42 |
| A-03 | N6-01 | Giá không khóa, tính lại khi chốt | ⚠⚠ | — | ❌ THIẾU | 10:05 |

Quy tắc:

1. Ô "Mã BR" trống = lỗ hổng chắc bị bắn; không nộp spec khi còn ô trống ở dòng ⚠ [HD §2.4].
2. ⚠⚠ = khác mặc định phổ biến VÀ có con số/trạng thái cụ thể → viết BR trước tiên, thêm 1 ví dụ số vào spec.
3. Trạng thái: ✅ đã viết · ✍ đang viết · ❌ thiếu · ⛔ ngoài phạm vi (không viết, dùng cho §1 spec).
4. Câu trả lời mâu thuẫn với câu trả lời trước → gắn cả 2 ID, đưa vào lượt restate; spec lấy câu sau và ghi mã BR "xác nhận lại lúc hh:mm" trong log [BABOK 4.3.4 .2].
5. Sau 11:00, đếm: số dòng ⚠ có BR / tổng dòng ⚠ phải = 100%.

## 6. Mẫu log hội thoại có timestamp (bằng chứng kháng nghị)

Tên file: `log_ai_khach_hang_<đội>_<ngày>.md`. Mỗi lượt một khối; dán nguyên văn câu hỏi và câu trả lời, không tóm tắt.

```
## Lượt 3 — Con số
Thời gian gửi: 2026-09-12 09:58:20
Token trước lượt: <đã dùng> / <TOKEN_MAX> (TOKEN_MAX = ô "Token AI Khách hàng" ở knowledge/00-luat-choi.md §A, điền sau họp 09/09)
Câu hỏi (nguyên văn):
<dán>
Câu trả lời (nguyên văn):
<dán>
Token sau lượt: <đã dùng>
Trích rút → RTM: A-07 (TTL 120), A-08 (gia hạn 1 lần, +30 phút) ⚠, A-09 (cọc 10%)
Mâu thuẫn với lượt trước: không / có → ID
```

Quy tắc dùng khi kháng nghị [HD §6.4 16:00–17:00]:

1. Mỗi ca kháng nghị = trích số mục trong spec + trích đúng khối log (lượt, timestamp, câu trả lời nguyên văn) + 2 câu lập luận.
2. Chỉ câu trả lời nguyên văn mới là bằng chứng; bản rút gọn trong RTM không dùng.
3. Chụp màn hình lượt có câu trả lời ⚠ ngay khi nhận, đặt tên `L<lượt>_<hhmm>.png`.
