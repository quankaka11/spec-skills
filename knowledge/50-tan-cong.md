*Dùng cho vai CÔNG 13:00–15:00 (3 spec đối thủ, 15 test) và red team nội bộ 11:45.*

# 50 — Sổ tay tấn công

Quy ước: `<TTL>`, `<QTY_MAX>`, `<HOLD_MAX>`, `<GH_MAX>` (số lần gia hạn), `<%CỌC>`, `<TZ>` = số lấy từ AI Khách hàng buổi sáng; điền số thật trước khi nộp. Dùng thời điểm tuyệt đối (10:00:00) thay vì "sau TTL".

## 1. Bảng 15 loại lỗ hổng [HD §5.1]

| # | Tên | Dấu hiệu trong spec (Ctrl+F / thiếu mục) | Probe §5 | Tỷ lệ TRÚNG | Rủi ro VÔ HIỆU |
|---|---|---|---|---|---|
| 1 | Khoảng trống hoàn toàn (silent gap) | Heading thiếu cả một nhóm N1–N14 [HD §3.5]; Ctrl+F "cọc", "hoàn", "gia hạn", "guest" = 0 hit | P20 / P21 | Cao | Thấp |
| 2 | Ngoại lệ vắng mặt | Chỉ có luồng tạo → thanh toán → xong; không có mục "Ngoại lệ"/"Lỗi" | P2 / P3 | Cao | Thấp |
| 3 | Định lượng mơ hồ | Hit danh sách đen [HD §4.6]: "nhanh", "một khoảng thời gian", "hợp lý", "nhiều" | P4 | Cao | Thấp |
| 4 | Biên & off-by-one | Có số nhưng thiếu "≤/<", "bao gồm/không bao gồm", "[đầu, cuối)" | P1 | Cao | Thấp |
| 5 | Mâu thuẫn nội tại | Cùng tham số có 2 giá trị ở 2 mục; bảng khác văn bản | "Khách VIP tạo hold 10:00. Hold hết hạn đúng lúc nào?" (khi §A nói 60 phút, §B nói 120) | Cao | Thấp |
| 6 | Trạng thái không xác định | Không có bảng trạng thái; bảng có ô trống; thiếu trạng thái "hết hạn chưa dọn" | P36 / P42 | Cao | TB |
| 7 | Đồng thời / race condition | Ctrl+F "đồng thời", "cùng lúc", "first-come", "khóa" = 0 hit | P13 | Cao | Thấp |
| 8 | Thiếu ưu tiên xung đột | Nhiều luật cùng chủ đề; không có "ưu tiên", "ghi đè", "trừ khi" | P35 | TB | Thấp |
| 9 | Actor bị bỏ sót | Chỉ có "khách hàng"; không nhắc guest, VIP, CSKH, admin, cron | P24 | Cao | Thấp |
| 10 | Quyền hạn không rõ | Không có bảng actor × hành động | P26 | Cao | TB |
| 11 | Đơn vị / múi giờ / lịch | Không có <TZ>, "UTC", "ngày lễ"; dùng "giờ làm việc" không định nghĩa | P28 | Cao | TB |
| 12 | Rollback / bù trừ thiếu | Không có "hoàn nguyên", "rollback", "nhả tồn" | P33 | Cao | TB |
| 13 | Phụ thuộc ngoài không định nghĩa | Nhắc ERP/cổng thanh toán/kho nhưng không nêu nguồn sự thật, timeout, retry | P16 | TB | TB |
| 14 | Từ ngữ đa nghĩa | Dùng lẫn "giữ hàng"/"đặt trước"/"đặt cọc"/"khóa hàng"/"giỏ hàng" | "Khách 'đặt trước' 1 sản phẩm tồn = 0 sắp về. Hold được tạo hay từ chối?" | TB | TB |
| 15 | Luật phản trực giác bị im lặng | Đối chiếu danh sách ⚠ buổi sáng; spec không nói → bắn thẳng [HD §2.3] | P19 | Rất cao | Thấp |

## 2. Quy tắc viết tình huống test

1. Ép kết quả cụ thể: câu hỏi kết thúc bằng một trong bốn dạng — con số / trạng thái cuối / ai thắng / có hoàn tiền không [HD §5.2 ②].
2. Một tình huống = một câu hỏi. Hai câu hỏi → cách AI So khớp xử lý câu hỏi kép chưa được BTC nêu (hỏi câu 10, 00 §H) — mặc định coi là rủi ro TRƯỢT. Kiểm nhanh: đếm dấu ? và dấu , trước câu hỏi — > 1 câu hỏi = viết lại.
3. Đủ 4 dữ kiện: thời điểm tuyệt đối, loại khách, số lượng, trạng thái hiện tại của hold.
4. Không hỏi mở một mình ("xử lý thế nào?"), không hỏi "vì sao", không hỏi "có nên".
5. Không khẳng định số liệu spec đối thủ ("TTL là 120 phút"); đưa mốc tuyệt đối để Executor tự tra. Áp riêng cho **giá trị dẫn xuất** mà spec tự tính ra — `expires_at`, `deposit_due`, `ATP`, mức cọc, số tiền hoàn: chỉ nêu dữ kiện gốc (`created_at`, `on_hand`, giá niêm yết) và để Executor tính. Nêu sẵn giá trị dẫn xuất mà lệch luật của spec sẽ khiến Executor báo ĐA NGHĨA giả và làm hỏng cả tình huống.
6. Hiểu được chỉ với brief + lẽ thường: không dùng mã BR, tên mục, thuật ngữ riêng đội mình.
7. Nằm trong core flow hold (tạo, giữ tồn, cọc, gia hạn, hết hạn, hủy, chuyển đơn, quyền actor). Chấm rubric §6 trước khi nộp.
8. ≤ 60 từ (giới hạn tự đặt để Executor không bỏ dữ kiện; thay bằng độ dài BTC công bố 09/09 nếu nhỏ hơn — 00 §A).
9. Chỉ nộp khi có đáp án chuẩn trong log buổi sáng; không có = không kháng nghị được.
10. Không dùng từ mơ hồ trong câu hỏi ("một lúc sau", "khá nhiều").
11. Tự đóng vai Executor: đoán mặc định khi spec im lặng vẫn trùng đáp án chuẩn → TRƯỢT cao, đổi test [HD §2.3].

Mẫu câu chuẩn:
> "[Loại khách] tạo hold [qty] đơn vị sản phẩm [X] lúc [hh:mm:ss, ngày, TZ]; hold hiện ở trạng thái [S]. Lúc [hh:mm:ss] xảy ra [sự kiện]. [Trạng thái cuối của hold là gì / tồn khả dụng bằng bao nhiêu / tiền cọc hoàn bao nhiêu / ai được hàng]?"

## 3. Phân bổ 5 test / spec [HD §5.3]

| Test | Loại | Nguồn đạn |
|---|---|---|
| 1 | #15 hoặc #1 — khoảng trống lớn nhất, ưu tiên ⚠ | Danh sách ⚠ × sweep §4 |
| 2 | #2 / #12 — ngoại lệ, lỗi hệ thống, rollback | Probe N12, N5; P39 / P41 / P42 |
| 3 | #4 / #11 — biên, đơn vị, múi giờ | Probe BVA, Time |
| 4 | #7 / #8 — đồng thời, ưu tiên xung đột | Probe N4, N14 |
| 5 | #9 / #10 — actor, quyền | Probe N9 |

Luật phân tán: 5 test phủ ≥ 4 nhóm N; tối đa 2 test cùng chủ đề. Slot đối thủ quá tốt → thay bằng #15 thứ hai ở chủ đề khác.

## 4. Quy trình soi 10 phút / spec [HD §5.4]

| Phút | Việc | Đầu ra |
|---|---|---|
| 0–2 | Đọc heading; tick 14 nhóm N1–N14 [HD §3.5] có/không | Nhóm KHÔNG có = hạng A |
| 2–4 | Ctrl+F danh sách đen [HD §4.6] + "có thể", "nên", "v.v.", "phù hợp", "hệ thống xử lý" | Mỗi hit = 1 điểm #3, ghi số mục |
| 4–6 | Tìm bảng trạng thái / decision table. Không có → #6/#8. Có → tìm ô trống, sự kiện thiếu (admin hủy, hết hàng, thanh toán lỗi) | Danh sách ô trống |
| 6–8 | Ctrl+F 5 mục hiếm: "đồng thời", "múi giờ", "guest", "rollback", "ưu tiên" | 0 hit = hạng A |
| 8–10 | Chốt 5 test theo §3; viết theo mẫu §2; chấm rubric §6; mở hồ sơ §7 | 5 test điểm ≤1 |

Knowledge boundary sweep [HD §2.1 ⑤]: Ctrl+F từng dòng danh sách ⚠ + bảng tham số đội mình (TTL, qty, hold max, %cọc, gia hạn, giá khóa, guest, hoàn cọc) trong spec đối thủ; không thấy hoặc khác specs thật → test hạng A, đáp án chuẩn có sẵn. 2 người: một sweep, một soi cấu trúc, song song từ phút 0.

## 5. Bảng PROBE

Nguồn: EP [ISTQB 4.2.1]; BVA 3-value [4.2.2]; decision table [4.2.3]; state table [4.2.4]; error guessing [4.4.1]; SFDIPOT [HTSM]; cheat sheet [Hendrickson]. N theo [HD §3.5].

| # | Probe | Nguồn | N | Ví dụ tình huống viết sẵn |
|---|---|---|---|---|
| P1 | TTL đúng mốc | BVA 3-value | N2 | "Hold tạo 10:00:00. Khách thanh toán đúng 10:00:00 + <TTL>. Hold còn hiệu lực không?" |
| P2 | TTL mốc +1 giây | BVA 3-value | N2 | "Hold tạo 10:00:00, thanh toán tại mốc <TTL> + 1 giây. Đơn có được tạo không?" |
| P3 | Mốc −1 giây, xử lý trễ | BVA; Time/Pacing [HTSM] | N2 | "Khách bấm thanh toán 1 giây trước hết hạn; cổng xử lý mất 40 giây. Đơn có được tạo không?" |
| P4 | Mốc bắt đầu TTL | Error guessing | N2 | "Bấm 'Giữ' 10:00:00, cọc thành công 10:07:00. Hold hết hạn lúc mấy giờ?" |
| P5 | Gia hạn lần <GH_MAX>+1 | BVA | N2 | "Hold đã gia hạn <GH_MAX> lần. Khách yêu cầu gia hạn tiếp 5 phút trước hết hạn. Chấp nhận hay từ chối?" |
| P6 | Gia hạn cộng dồn hay reset | Error guessing | N2 | "Hold tạo 10:00, gia hạn lúc 11:50. Hạn mới tính từ 11:50 hay từ mốc hết hạn cũ? Nêu giờ." |
| P7 | qty = 0 | Data: zero [Hendrickson] | N3 | "Khách nhập số lượng 0, bấm 'Giữ'. Tạo hold, từ chối, hay tự sửa thành 1?" |
| P8 | qty âm | Data: negative | N3 | "Khách gửi qty = −1 qua API. Tồn khả dụng thay đổi bao nhiêu?" |
| P9 | qty = <QTY_MAX>+1 | BVA 3-value | N3 | "Tồn 100. Khách thường giữ <QTY_MAX>+1 đơn vị. Hold tạo với số lượng nào?" |
| P10 | qty > tồn | Data: huge | N3 | "Tồn 3. Khách giữ 5. Kết quả: hold 3, hold 0, hay từ chối?" |
| P11 | Hold thứ <HOLD_MAX>+1 | BVA; Count [Hendrickson] | N3 | "Khách có <HOLD_MAX> hold ACTIVE, tạo thêm 1 hold sản phẩm khác. Chấp nhận hay từ chối?" |
| P12 | Cùng SKU 2 hold | Data: duplicates | N3 | "A đã hold 2 đơn vị SKU X, tạo thêm hold 1 đơn vị SKU X. Hai hold riêng, gộp thành 3, hay từ chối?" |
| P13 | Race đơn vị cuối | Multi-User [Hendrickson]; Concurrency [HTSM] | N4 | "Tồn 1. A và B gửi giữ cùng mili-giây, server nhận A trước 5 ms. Ai được hold?" |
| P13b | Race: phản hồi cho người thua | Multi-User; Concurrency | N4 | "Tồn 1. A và B gửi giữ cùng mili-giây, server nhận A trước 5 ms. B nhận trạng thái nào: hết hàng / waitlist / PENDING?" |
| P14 | Double-click | Flood [Hendrickson] | N4 | "Khách bấm 'Giữ' 2 lần trong 300 ms cùng sản phẩm qty 1. Số hold tạo ra và tồn bị trừ?" |
| P15 | Hold vs mua thẳng | Multi-User | N4 | "Tồn 1, A đang hold. B mua thẳng không hold. B thanh toán thành công không?" |
| P16 | Nguồn sự thật tồn | Interfaces [HTSM] | N4 | "Hệ thống báo tồn 5, ERP báo 0. Khách giữ 1. Chấp nhận hay từ chối?" |
| P17 | Cọc thất bại sau giữ | Error handling [HTSM] | N5 | "Hold tạo, tồn đã trừ; cọc bị ngân hàng từ chối. Hold ở trạng thái nào?" |
| P18 | Cổng thanh toán timeout | Interfaces: timeout | N5 | "Tồn 10. Khách giữ 2; cổng thanh toán không phản hồi 60 giây khi cọc. Tồn khả dụng bằng bao nhiêu?" |
| P19 | Giá đổi trong TTL | Error guessing (prior ⚠) | N6 | "Hold khi giá 100.000đ; lúc thanh toán 120.000đ. Khách trả bao nhiêu?" |
| P20 | Hủy sau cọc | Silent gap | N7 | "Khách tự hủy hold đã cọc 30 phút sau khi tạo. Hoàn bao nhiêu % cọc?" |
| P21 | Hết hạn: cọc | Silent gap (⚠) | N7 | "Hold hết hạn, khách không thanh toán. Cọc: hoàn 100%, một phần, hay mất?" |
| P22 | Hết hạn: nhả tồn khi nào | Operations: cron [HTSM] | N7 | "Hold hết hạn 12:00:00. Lúc 12:00:01 khách khác tra tồn. Tồn khả dụng đã gồm hàng vừa nhả chưa?" |
| P23 | Thanh toán hold đã hủy | State: invalid transition | N8 | "Hold HỦY 5 phút trước; khách bấm thanh toán qua link cũ. Đơn tạo hay từ chối?" |
| P24 | Guest | EP loại khách | N9 | "Người chưa đăng nhập bấm 'Giữ' qty 1. Tạo hay từ chối; nếu tạo, TTL?" |
| P25 | VIP | EP loại khách | N9 | "Khách VIP tạo hold 10:00. Hold hết hạn lúc mấy giờ?" |
| P25b | Khách thường | EP loại khách | N9 | "Khách thường tạo hold 10:00. Hold hết hạn lúc mấy giờ?" |
| P26 | CSKH gia hạn thay khách | Authorization [HTSM] | N9 | "CSKH gia hạn 60 phút cho hold khách A khi khách không yêu cầu. Chấp nhận hay từ chối?" |
| P27 | Admin hủy hold đã cọc | Authorization | N9 | "Admin hủy hold ĐÃ CỌC của khách. Hoàn cọc bao nhiêu %?" |
| P28 | Múi giờ | Time zones [Hendrickson; HTSM] | N2 | "Khách Tokyo tạo hold 23:30 JST thứ 7. Hết hạn lúc mấy giờ theo giờ Việt Nam?" |
| P29 | Qua 00:00 / cuối tháng | Date boundary | N2 | "Hold tạo 23:50 ngày 30/09. Hết hạn lúc mấy giờ, ngày nào?" |
| P30 | Ngày lễ / ngoài giờ | Business holidays [HTSM Time] | N2 | "Hold tạo 17:30 ngày 01/09; cửa hàng nghỉ 02/09. Hold hết hạn lúc nào?" |
| P31 | DST | DST changeover [Hendrickson] | N2 | "Khách ở châu Âu tạo hold 01:30 đêm đổi giờ mùa hè. TTL tính theo đồng hồ nào, hết hạn lúc nào?" |
| P32 | Đồng hồ client lệch | Time difference between machines | N2 | "Điện thoại khách chậm 10 phút so với server. Hold hết hạn theo mốc nào; giờ cụ thể?" |
| P33 | Lỗi giữa trừ tồn và ghi hold | Data integrity [HTSM]; #12 | N12 | "Đã trừ tồn 2, ghi hold lỗi DB. Tồn khả dụng cuối cùng so với trước?" |
| P34 | Retry / idempotency | Data: duplicates | N12 | "Client không nhận phản hồi, gửi lại đúng yêu cầu giữ sau 2 giây. Có 1 hay 2 hold?" |
| P35 | Hai luật cùng lúc | Decision table gap | N14 | "Đúng 12:00 hold hết hạn và khách gửi gia hạn hợp lệ cùng lúc. Kết quả cuối?" |
| P36 | Hết hàng vs hold đã cọc | Priority conflict | N14 | "Kho báo hư hỏng, tồn thật = 0 khi A có hold ĐÃ CỌC 1 đơn vị. Hold A còn ACTIVE hay bị hủy?" |
| P37 | Chốt một phần | Selection Some/None/All [Hendrickson] | N8 | "Hold 5 đơn vị SKU X tạo 10:00, chốt đơn 3 đơn vị lúc 10:20. Hai đơn vị còn lại được giải phóng lúc mấy giờ?" |
| P38 | Hold nhiều SKU, 1 dòng hết hàng | Dependencies; Count [Hendrickson] | N4 | "Hold gồm SKU X (qty 1) và SKU Y (qty 1); kho báo Y hỏng, tồn Y = 0. Hold ở trạng thái nào?" |
| P39 | Tài khoản bị khóa khi hold ACTIVE | Interruptions [Hendrickson] | N9 | "Tài khoản khách bị khóa lúc 10:30 khi hold ACTIVE tới 12:00. Lúc 11:00 hold ở trạng thái nào?" |
| P40 | Hủy rồi tạo lại ngay | Sequences [Hendrickson] | N3 | "Khách hủy hold 10:05, tạo lại hold cùng SKU lúc 10:06. Hold mới được tạo hay bị từ chối?" |
| P41 | Hủy khi cổng đang xử lý cọc | Interruptions: Cancel [Hendrickson] | N5 | "Khách bấm Hủy lúc cổng thanh toán đang xử lý cọc (chưa callback). Cọc về đâu: không thu / hoàn 100% / giữ?" |
| P42 | SKU ngừng bán khi hold ACTIVE | State: invalid transition | N12 | "Admin ngừng bán SKU X lúc 10:30 khi hold ACTIVE tới 12:00. Hold ở trạng thái nào?" |

## 6. Rubric kiểm phạm vi (chống VÔ HIỆU)

| Điểm | Tiêu chí | Ví dụ |
|---|---|---|
| 0 | Core flow hold (như §2 rule 7) VÀ trong danh sách TRONG phạm vi hoặc AI Khách hàng đã trả lời trực tiếp | TTL, race, guest, hoàn cọc |
| 1 | Core flow nhưng AI Khách hàng chưa trả lời trực tiếp; chắc chắn thuộc tính năng theo brief | Retry idempotency, hủy 2 lần |
| 2 | Miền kề có chạm hold: khuyến mãi, giá, kiểm kê, catalog, ERP, kênh thông báo, DST | Voucher hết hạn trong TTL, ERP lệch tồn |
| 3 | Miền xa hoặc trong danh sách NGOÀI phạm vi: thuế, kế toán, hóa đơn, bảo hiểm, vận chuyển, đổi trả sau giao, chống gian lận, bảo vệ dữ liệu, KPI | "Xuất hóa đơn cọc thế nào?" |

Luật:
1. Chỉ nộp test điểm ≤ 1.
2. Điểm 2 kèm trích lời AI Khách hàng (timestamp) chứng minh trong phạm vi → hạ xuống 1; không có → loại.
3. Điểm 3 không nộp với bất kỳ lý do.
4. Câu hỏi chạm 2 miền → lấy điểm cao nhất.
5. Buổi sáng chưa hỏi "liệt kê nghiệp vụ NGOÀI phạm vi" [HD §5.6] → mọi test +1 điểm.
6. Người chấm phạm vi ≠ người viết test; ghi điểm vào hồ sơ §7.
7. Câu trả lời `"Không có quy định riêng."` **KHÔNG** phải bằng chứng trong phạm vi — nó chỉ nói specs thật không quy định, không nói nghiệp vụ thuộc tính năng. Chỉ hai thứ hạ được điểm phạm vi: (a) mục nằm trong danh sách TRONG phạm vi (N1-03), hoặc (b) một câu trả lời nghiệp vụ cụ thể (con số / trạng thái cuối / hướng tiền / ai thắng) về đúng nghiệp vụ đó. Dòng "chưa rõ" trong RTM giữ nguyên điểm phạm vi gốc — đừng coi im lặng của specs thật là giấy thông hành.

## 7. Hồ sơ finding [HD §5.7] và kháng nghị [HD §5.8]

Một hồ sơ / test, lập trước khi nộp.

| Trường | Nội dung |
|---|---|
| ID | `T-B3` (test 3, spec đội B) |
| Loại lỗ hổng | # theo §1, probe theo §5 |
| Tình huống nộp | Nguyên văn |
| Chỗ spec im lặng / mâu thuẫn | Trích dẫn có số mục; hoặc "không mục nào đề cập" + 3 từ khóa Ctrl+F |
| Đáp án chuẩn kỳ vọng | Trích lời AI Khách hàng + timestamp; ghi cả hệ quả phụ (tồn về đâu, ai được thông báo) đã tách khỏi câu hỏi |
| Dự đoán Executor | Câu Executor sẽ trả lời nếu spec im lặng (prior) |
| Điểm phạm vi | 0–3 theo §6, người chấm |
| Kết quả máy | TRÚNG / TRƯỢT / VÔ HIỆU + lý do đối chiếu của AI So khớp |
| Đánh giá | Đồng ý / Ứng viên kháng nghị (ưu tiên 1–3) |

Ca kháng nghị (tối đa 3 ca/đội):

| Ưu tiên | Ca | Lập luận mẫu (2 câu) |
|---|---|---|
| 1 | Test bắn vào spec mình bị tính TRÚNG nhưng spec có quy định | "BR-nn của spec chúng tôi quy định [trích nguyên văn], bao trùm tình huống này. Executor không áp dụng; đề nghị đối chiếu lại đáp án chuẩn với BR-nn." |
| 2 | Test mình bắn bị tính VÔ HIỆU sai | "Lúc [hh:mm], AI Khách hàng trả lời [trích nguyên văn] về đúng nghiệp vụ này, tức nằm trong specs thật. Đề nghị chấm lại theo đáp án chuẩn đó." |
| 3 | So khớp hiểu sai ngữ nghĩa | "Đáp án chuẩn nói [A], Executor trả lời [B]; cùng kết quả về trạng thái cuối, tồn và tiền, chỉ khác diễn đạt. Đề nghị đổi kết quả theo lý do đối chiếu đã lưu." |

Bằng chứng mang vào phiên 16:00: log AI Khách hàng có timestamp, bản copy spec đã nộp, 15 hồ sơ finding, lý do đối chiếu của AI So khớp (xin BTC trước [HD §2.2]).
