*Dùng cho vai CÔNG 13:00–15:00 (3 spec đối thủ, 15 test) và red team nội bộ 11:45.*

# 50 — Sổ tay tấn công

Quy ước: `<TTL>`, `<QTY_MAX>`, `<HOLD_MAX>`, `<GH_MAX>` (số lần gia hạn), `<%CỌC>`, `<TZ>` = số lấy từ AI Khách hàng buổi sáng; điền số thật trước khi nộp. Dùng thời điểm tuyệt đối (10:00:00) thay vì "sau TTL".

## 1. Bảng 24 loại lỗ hổng [HD §5.1] — #16–#18 từ cấu trúc BTC 11/09, #19–#24 từ cổng khả thi (knowledge/33)

> Loại 16–18 sinh ra từ cấu trúc spec BTC 10 mục (knowledge/32). Đội nào viết spec theo lối luật nghiệp vụ thuần (không có mục 2/3/4) sẽ hở toàn bộ ba loại này.

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
| 16 | **Message lỗi không nguyên văn** (mục 4 BTC) | Mục 4 vắng, hoặc chỉ ghi "hiển thị thông báo lỗi" / "báo lỗi phù hợp" — không có chuỗi trong ngoặc kép | P43 | **Rất cao** | Thấp |
| 17 | **Guest vs login không phân biệt** (mục 2, 8 BTC) | Mục 2 không có cột guest; mục 8 không có dòng Guest; Ctrl+F "guest / chưa đăng nhập" = 0 hit | P44 | **Rất cao** | Thấp |
| 18 | **Trạng thái UI không xác định** (mục 2, 3 BTC) | Không nói nút disable/ẩn khi nào; không nói sau thao tác màn hình hiển thị gì; bảng Case thiếu cột "hiển thị / trạng thái nút" | P45 | Cao | TB |
| 19 | **Luật bất khả thi** — hứa hộ bên ngoài | Grep nhóm 40 §2-17: `hoàn tất|tiền về|đối soát xong` + số giờ/ngày. Specs thật **không thể** viết vậy ⇒ mọi ca hoàn tiền lệch | P49 / P50 | Rất cao | Thấp |
| 20 | **Luật tự đánh bại mục tiêu** | Brief có mục tiêu chống-gì-đó, spec có luật mở cửa cho đúng việc đó (khóa tài nguyên trước khi thu tiền/xác thực; hủy phí 0 không trần) | P51 / P52 | Cao | Thấp |
| 21 | **Hạn mức không cưỡng chế được** | Grep nhóm 40 §2-18: hạn mức neo vào email/SĐT/tên khách tự khai | P53 | Rất cao | Thấp |
| 22 | **Thiếu luật cho thất bại của phụ thuộc ngoài** | Spec nhắc cổng/ERP/job/thông báo nhưng không có nhánh lỗi nào (nhóm 40 §2-19). Đối chiếu 12 ca 05 §M6 | P54–P57 | Rất cao | Thấp |
| 23 | **Phạm vi NGOÀI tự đặt rộng hơn brief** | Đọc danh sách NGOÀI phạm vi của đối thủ, so với brief: nghiệp vụ nào brief nhắc mà họ đẩy ra ngoài | P58 | Cao | **Thấp** — vì brief là căn cứ chung, không phải suy diễn của ta |
| 24 | **Lệch đồng thuận chéo** (mới 09/09) | Cả 3 spec tải về đều bàn một nghiệp vụ, **một** spec im lặng hoặc nói khác hai spec kia (§4b) | theo chủ đề | Cao | **Thấp** — hai spec kia là bằng chứng nghiệp vụ nằm trong phạm vi |

**Vì sao #24 rẻ nhất.** Nó không cần biết specs thật: chỉ cần ba file markdown tải về và một bảng đối chiếu. Hai đội khác viết luật cho nghiệp vụ X là bằng chứng gián tiếp rằng X thuộc phạm vi (họ đã hỏi, hoặc brief có nói), nên rủi ro VÔ HIỆU thấp; còn đội im lặng thì Executor phải đoán. Cẩn trọng duy nhất: **cả 3 im lặng không phải bằng chứng ngoài phạm vi** — đó là điểm mù chung, muốn bắn thì phải có brief hoặc log đỡ.

**Vì sao #19–#23 là đạn tốt nhất năm nay.** Chúng nhắm vào *nội dung* chứ không vào *cách viết*, nên (a) đội nào cũng hở — checklist phổ biến chỉ dạy chống mơ hồ; (b) rủi ro VÔ HIỆU thấp vì đều nằm trong core flow tiền/tồn; (c) đáp án chuẩn dễ có trong log nếu buổi sáng đã hỏi nhóm N0 (20 §2). Đo trên `battle/spec.nop.md` — một spec đạt mọi cổng hình thức: 0 hit mức Cao ở loại #3/#4/#5/#6, nhưng **7 điểm bắn** ở #19–#23 (knowledge/33 §7).

## 2. Quy tắc viết tình huống test

1. Ép kết quả cụ thể: câu hỏi kết thúc bằng một trong bốn dạng — con số / trạng thái cuối / ai thắng / có hoàn tiền không [HD §5.2 ②].
2. Một tình huống = một câu hỏi. Hai câu hỏi → cách AI So khớp xử lý câu hỏi kép chưa được BTC nêu (hỏi câu 10, 00 §H) — mặc định coi là rủi ro TRƯỢT. Kiểm nhanh: đếm dấu ? và dấu , trước câu hỏi — > 1 câu hỏi = viết lại.
3. Đủ 4 dữ kiện: thời điểm tuyệt đối, loại khách, số lượng, trạng thái hiện tại của hold.
4. Không hỏi mở một mình ("xử lý thế nào?"), không hỏi "vì sao", không hỏi "có nên".
5. Không khẳng định số liệu spec đối thủ ("TTL là 120 phút"); đưa mốc tuyệt đối để Executor tự tra. **Cũng không nêu sẵn giá trị dẫn xuất** (`expires_at`, `deposit_due`, `ATP`, mức cọc, số tiền hoàn): chỉ nêu dữ kiện gốc và để Executor tự tính — một giá trị dẫn xuất lệch luật của họ sẽ khiến Executor báo ĐA NGHĨA giả và làm hỏng cả tình huống.
6. Hiểu được chỉ với brief + lẽ thường: không dùng mã BR, tên mục, thuật ngữ riêng đội mình.
7. Nằm trong core flow hold (tạo, giữ tồn, cọc, gia hạn, hết hạn, hủy, chuyển đơn, quyền actor). Chấm rubric §6 trước khi nộp.
8. ≤ 60 từ (giới hạn tự đặt để Executor không bỏ dữ kiện; thay bằng độ dài BTC công bố 09/09 nếu nhỏ hơn — 00 §A).
9. **Gói bằng chứng phạm vi — thu ngay khi viết test, không để tới 16:00.** Với 5 câu hỏi cả ngày (00 §A), "có đáp án chuẩn trong log" là điều kiện hiếm, nên bằng chứng xếp theo ba mức, mạnh trước:
   - **Mức 1** — lời AI Khách hàng nguyên văn + timestamp về đúng nghiệp vụ đó. Mạnh nhất, nhưng chỉ phủ 4–5 chủ đề.
   - **Mức 2** — câu brief tường minh. Brief là căn cứ dùng chung, đội bạn không bác được bằng suy diễn.
   - **Mức 3** — đồng thuận chéo (§4b): ≥2/3 spec có luật cho nghiệp vụ đó.
   Không có mức nào ⇒ chấm rubric §6 và nộp chỉ khi kỳ vọng dương (§6 luật 7). Gói phải xong **lúc 14:30**, vì kháng nghị chỉ có 1 giờ và không thu bằng chứng kịp.
10. **Bỏ một slot còn hơn nộp một test âm kỳ vọng.** Nộp 13 test tốt hơn 15 test trong đó 2 test đoán bừa: bỏ slot = 0 điểm, test VÔ HIỆU = −1 (00 §D1).
11. Không dùng từ mơ hồ trong câu hỏi ("một lúc sau", "khá nhiều").
12. Tự đóng vai Executor: đoán mặc định khi spec im lặng vẫn trùng đáp án chuẩn → TRƯỢT cao, đổi test [HD §2.3].

Mẫu câu chuẩn:
> "[Loại khách] tạo hold [qty] đơn vị sản phẩm [X] lúc [hh:mm:ss, ngày, TZ]; hold hiện ở trạng thái [S]. Lúc [hh:mm:ss] xảy ra [sự kiện]. [Trạng thái cuối của hold là gì / tồn khả dụng bằng bao nhiêu / tiền cọc hoàn bao nhiêu / ai được hàng]?"

## 3. Phân bổ 5 test / spec [HD §5.3]

| Test | Loại | Nguồn đạn |
|---|---|---|
| 1 | #15 / #1 / **#24** — khoảng trống lớn nhất, ưu tiên ⚠ | Danh sách ⚠ × sweep §4 × **bảng đồng thuận chéo §4b** |
| 2 | **#19 / #21 / #22 — khả thi, cưỡng chế, thất bại phụ thuộc ngoài** | Cổng F trên spec đối thủ (33 §1); P49–P57 |
| 3 | **#16 / #17 — message lỗi nguyên văn, guest vs login** (cấu trúc BTC 11/09) | Mục 4 và mục 2/8 của spec đối thủ; P43 / P44 |
| 4 | #2 / #12 — ngoại lệ, lỗi hệ thống, rollback | Probe N12, N5; P39 / P41 / P42 |
| 5 | #4 / #11 / #7 / #8 / **#20** — biên, múi giờ, đồng thời, tự đánh bại mục tiêu | Probe BVA, Time, N4, N14; P51 / P52 |

Luật phân tán: 5 test phủ ≥ 4 nhóm N; tối đa 2 test cùng chủ đề. Slot đối thủ quá tốt → thay bằng #15 thứ hai ở chủ đề khác. **Mọi test phải có `EV > 0` (§6 luật 7); ≥1 test thuộc nhóm #19–#24.**

**Với 5 câu hỏi buổi sáng, trọng số nguồn đạn đã đổi** — thứ tự độ chắc giảm dần:
1. **⚠ từ C1–C5** — ít (8–20 dòng) nhưng chắc nhất, có bằng chứng mức 1.
2. **Đồng thuận chéo §4b** — nhiều và rẻ, không tốn câu hỏi nào, có bằng chứng mức 3.
3. **Cổng F trên spec đối thủ** (33 §1) — hầu như không đội nào chạy cổng này, nên mật độ lỗ hổng cao.
4. **Catalogue ⚠ của domain** (10 §6) — rủi ro TRƯỢT cao nhất, vì Executor cũng đoán y hệt catalogue.

Slot #9/#10 (actor, quyền) xuống dự phòng: hầu hết đội đều có bảng actor, còn cổng F thì hầu như không đội nào chạy.

## 4. Quy trình soi 10 phút / spec [HD §5.4]

| Phút | Việc | Đầu ra |
|---|---|---|
| 0–1,5 | **Tick 10 mục BTC** (knowledge/32 §1) có/không; rồi tick 19 nhóm nghiệp vụ N1–N19 [HD §3.5] | Mục vắng = hạng A (#1, và #16/#17/#18 nếu là mục 4 / 2 / 3) |
| 1,5–3 | **Cổng F rút gọn — 5 lệnh grep** (33 §1 cuối mục), dán được:<br>`grep -n -iE "hoàn tất\|tiền về\|đối soát xong" B.md` → #19<br>`grep -n -iE "theo email\|theo số điện thoại\|tự khai\|khai lúc" B.md` → #21<br>`grep -n -iE "cổng\|ERP\|WMS\|callback\|job\|cron\|đồng bộ\|thông báo" B.md` → mỗi hit không có nhánh lỗi = #22<br>`grep -n -iE "guest\|chưa đăng nhập\|hoàn 100\|không phí\|miễn phí" B.md` → #20<br>`grep -c "^\|" B.md ; grep -n '```mermaid' B.md` → chỉ có sơ đồ, không có bảng = #6 | Mỗi hit = ứng viên **hạng A**. #22 là loại hở nhiều nhất: hầu hết spec nhắc cổng thanh toán nhưng chỉ viết nhánh thành công |
| 3–4 | **Mục 4** (validation & message): có message **nguyên văn trong ngoặc kép** không? có cột FE/BE không? | Không nguyên văn = #16 hạng A — bắn "hiện message gì" |
| 4–5 | **Mục 2 + 8**: có cột/dòng **Guest** không? có điều kiện ẩn/disable không? | Vắng = #17/#18 hạng A |
| 5–5,5 | Ctrl+F danh sách đen [HD §4.6] nhóm 1, 3, 4, 6, 8, 11 **+ nhóm nội dung 17–22** (40 §2) | Mỗi hit = 1 điểm #3, ghi số mục |
| 5,5–7 | **Mục 6**: với từng logic, đếm case **bình thường / biên / lỗi**; kiểm 5 gạch (số, toán tử `>`/`≥`, múi giờ, default khi config trống, ưu tiên khi nhiều case cùng đúng). Bảng trạng thái: ô trống, sự kiện thiếu | Case thiếu = #2/#4/#6/#8; 5 gạch thiếu = bắn thẳng vào gạch đó |
| 7–8 | **Mục 7**: có 4 ca bất thường BTC nêu đích danh không (dữ liệu đổi giữa hiển thị và submit · gửi trùng · mở link hai lần · mail fail sau khi đã lưu)? | Vắng ca nào = tình huống bắn sẵn |
| 8–8,5 | Ctrl+F 5 mục hiếm: "đồng thời", "múi giờ", "guest", "rollback", "ưu tiên" | 0 hit = hạng A |
| 8,5–9 | **Đối chiếu 12 kịch bản suy biến** (05 §M6) với spec đối thủ; đọc danh sách NGOÀI phạm vi của họ so với brief (#23) | Mỗi ca không có luật = ứng viên #22 |
| 9–10 | Chốt 5 test theo §3; viết theo mẫu §2; chấm rubric §6 + **kỳ vọng EV (§6 luật 7)**; **gói bằng chứng phạm vi (§2-9)**; mở hồ sơ §7 | 5 test `EV > 0`, mỗi test có bằng chứng |

Knowledge boundary sweep [HD §2.1 ⑤]: Ctrl+F từng dòng danh sách ⚠ + bảng tham số đội mình (TTL, qty, hold max, %cọc, gia hạn, giá khóa, guest, hoàn cọc) trong spec đối thủ; không thấy hoặc khác specs thật → test hạng A, đáp án chuẩn có sẵn. 2 người: một sweep, một soi cấu trúc, song song từ phút 0.

## 4b. Bảng đồng thuận chéo 3 spec (bước mới, 13:00–13:15)

Làm **một lần cho cả ba spec**, trước khi soi từng spec. Chỉ làm được vì spec đối thủ tải về được ở dạng markdown.

| Nghiệp vụ | Spec A | Spec B | Spec C | Spec mình | Kết luận |
|---|---|---|---|---|---|
| Hoàn cọc khi hết hạn | §6 BR-09: hoàn 100% | §5: mất cọc | im lặng | hoàn 100% | A và B **chỏi nhau** ⇒ ít nhất một đội sai ⇒ bắn cả hai bằng cùng một tình huống; C im lặng ⇒ #24 |
| Guest tạo lượt giữ | cấm | cấm | im lặng | cấm | C im lặng, hai đội cấm ⇒ #24, phạm vi có bằng chứng mức 3 |
| Callback trùng | im lặng | im lặng | im lặng | có 0.11 | Điểm mù chung ⇒ **không** phải bằng chứng ngoài phạm vi; bắn cả 3 **chỉ khi** có brief/log đỡ |

Cách dùng:

1. Lấy danh sách nghiệp vụ từ 19 nhóm N1–N19 (20 §2) + 12 ca suy biến (05 §M6) — khoảng 25 dòng là đủ. Nhớ lấy cả các dòng thuộc N15–N19 (item màn hình, event, message lỗi, API, ca bất thường): đó là bốn vùng cấu trúc BTC 11/09 mới yêu cầu, nên khả năng có đội bỏ trống rất cao.
2. Điền bằng grep, không đọc tuần tự: mỗi dòng một lệnh `grep -n -i -E "<3 từ khóa>" A.md B.md C.md`.
3. Ba kết luận đáng bắn, theo độ mạnh giảm dần:
   - **Hai spec chỏi nhau về cùng một nghiệp vụ** — ít nhất một đội trái specs thật. Một tình huống bắn được cả hai, và với đội sai thì gần chắc TRÚNG.
   - **Một spec im lặng, ≥2 spec có luật** (#24) — bằng chứng phạm vi mức 3 có sẵn.
   - **Cả ba im lặng** — điểm mù chung, giá trị cao nếu đúng nhưng phải có bằng chứng mức 1 hoặc 2 mới nộp.
4. Cột "Spec mình" là phần lợi kép: dòng nào ta im lặng mà ≥2 đội có luật là chỗ ta gần chắc bị bắn — ghi lại để rút bài học, không sửa được nữa (spec đã khóa 12:00).

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
| **P43** | **Message lỗi nguyên văn** | #16; mục 4 BTC | N17 | "Khách nhập số lượng 5 khi tồn khả dụng còn 2, bấm Giữ hàng. Hệ thống hiển thị message gì, nguyên văn?" |
| **P43b** | **Nhiều lỗi cùng lúc** | #16; mục 4 | N17 | "Khách để trống số điện thoại VÀ nhập số lượng vượt tồn, bấm Giữ hàng. Hiện một hay nhiều message, message nào trước?" |
| **P44** | **Guest vs login** | #17; mục 2, 8 BTC | N15 | "Khách CHƯA đăng nhập bấm Giữ hàng cho SKU còn 3 đơn vị. Hold được tạo hay bị từ chối; màn hình hiện gì?" |
| **P44b** | **Guest xem hold** | #17; mục 8 | N15 | "Guest đã tạo hold lúc 10:00, đóng trình duyệt, mở lại lúc 10:30. Guest có xem được hold của mình không?" |
| **P45** | **Trạng thái nút** | #18; mục 2, 3 BTC | N15 | "Tồn khả dụng của SKU X = 0 khi khách mở màn hình. Nút Giữ hàng ở trạng thái nào: bật, disable hay ẩn?" |
| **P45b** | **Sau thao tác hiển thị gì** | #18; mục 3 | N16 | "Khách tạo hold thành công lúc 10:00. Ngay sau đó màn hình hiển thị gì và nút chuyển thành gì?" |
| **P46** | **Dữ liệu đổi giữa hiển thị và submit** | mục 7.2 BTC | N19 | "Khách mở màn hình lúc 10:00 thấy tồn 3; lúc 10:02 tồn thật còn 1; khách bấm Giữ hàng 2 đơn vị lúc 10:03. Kết quả và message?" |
| **P47** | **Mở link hai lần** | mục 7.2 BTC | N19 | "Khách mở link xác nhận cọc hai lần trong 5 giây. Có mấy hold, thu cọc mấy lần?" |
| **P48** | **Mail fail sau khi đã lưu** | mục 7.2 BTC | N18 | "Hold đã lưu thành công nhưng gửi email xác nhận thất bại. Hold ở trạng thái nào, khách thấy gì?" |

### Probe khả thi & mục tiêu (P49–P58) — dùng cho loại #19–#23

Nguồn: ISO/IEC/IEEE 29148 `feasible`/`affordable`; HTSM *Operations: disfavored use* + *Quality Criteria conflict*; WWWWWHKE (so *Why* với *How*); bảng thực tế phụ thuộc ngoài ở knowledge/33 §2.

| # | Probe | Loại | N | Ví dụ tình huống viết sẵn |
|---|---|---|---|---|
| P49 | Hoàn tiền về phương thức không dùng được | #19 | N5 | "Khách trả cọc bằng thẻ, sau đó thẻ bị ngân hàng đóng. Hold hết hạn. Khách nhận lại tiền bằng cách nào?" |
| P50 | Hai mốc hoàn tiền | #19 | N5 | "Hold hết hạn lúc 12/09 10:00. Đến 13/09 10:00 tiền cọc vẫn chưa vào tài khoản khách. Hệ thống ghi hold ở trạng thái gì và khoản cọc ở trạng thái gì?" |
| P51 | Khóa tài nguyên trước khi thu tiền | #20 | N4 | "Khách bấm 'Đặt cọc giữ hàng' cho đơn vị cuối cùng rồi không thanh toán. Trong lúc chờ, khách khác mua sản phẩm đó được không?" |
| P52 | Giữ chỗ miễn phí lặp lại | #20 | N3 | "Một khách tạo hold rồi tự hủy 20 lần trong một giờ cho các SKU khác nhau. Lần thứ 21 hệ thống tạo hold hay từ chối?" |
| P53 | Vượt hạn mức bằng danh tính mới | #21 | N9 | "Khách chưa đăng nhập tạo hold bằng email A, rồi tạo tiếp bằng email B cho cùng sản phẩm. Hold thứ hai được tạo hay bị từ chối?" |
| P54 | Callback trùng | #22 | N5 | "Cổng thanh toán báo cọc thành công hai lần cho cùng một giao dịch. Số tiền đã thu của khách là bao nhiêu?" |
| P55 | Callback sai thứ tự | #22 | N5 | "Cổng gửi 'thất bại' lúc 10:00:05 rồi gửi 'thành công' của cùng giao dịch lúc 10:00:02 nhưng đến sau. Hold ở trạng thái nào?" |
| P56 | Job hết hạn ngừng chạy | #22 | N12 | "Job dọn hết hạn ngừng chạy từ 10:00 tới 14:00. Lúc 14:00 tồn khả bán của SKU có hold hết hạn 11:00 bằng bao nhiêu?" |
| P57 | Ghi giữ thất bại sau khi kiểm thấy còn hàng | #22 | N4 | "Hệ thống kiểm thấy còn 1 đơn vị nhưng khi ghi giữ thì số tồn đã bị giao dịch khác lấy. Khách nhận kết quả gì?" |
| P58 | Nghiệp vụ brief nhắc bị đẩy ra ngoài phạm vi | #23 | N1 | "Khách đã chốt đơn từ hold và còn phải trả phần tiền còn lại. Khách trả nốt trong thời hạn nào?" *(chỉ bắn khi brief nhắc tường minh phần tiền còn lại)* |

**Cảnh báo phạm vi cho P49–P58:** chấm rubric §6 như mọi test. P49, P50, P54–P57 điểm 0–1 vì nằm trong luồng tiền/tồn của chính tính năng. P58 chỉ nộp khi **trích được câu brief** nhắc nghiệp vụ đó — brief là căn cứ dùng chung, mạnh hơn suy diễn, nhưng vẫn yếu hơn một câu trả lời của AI Khách hàng.
**Cảnh báo phạm vi cho P49–P58:** chấm rubric §6 như mọi test. P49, P50, P54–P57 điểm 0–1 vì nằm trong luồng tiền/tồn của chính tính năng. P58 chỉ nộp khi **trích được câu brief** nhắc nghiệp vụ đó — brief là căn cứ dùng chung, mạnh hơn suy diễn, nhưng vẫn yếu hơn một câu trả lời của AI Khách hàng.

## 6. Rubric kiểm phạm vi (chống VÔ HIỆU)

| Điểm | Tiêu chí | Ví dụ |
|---|---|---|
| 0 | Core flow hold (như §2 rule 7) VÀ trong danh sách TRONG phạm vi hoặc AI Khách hàng đã trả lời trực tiếp | TTL, race, guest, hoàn cọc |
| 1 | Core flow nhưng AI Khách hàng chưa trả lời trực tiếp; chắc chắn thuộc tính năng theo brief | Retry idempotency, hủy 2 lần |
| 2 | Miền kề có chạm hold: khuyến mãi, giá, kiểm kê, catalog, ERP, kênh thông báo, DST | Voucher hết hạn trong TTL, ERP lệch tồn |
| 3 | Miền xa hoặc trong danh sách NGOÀI phạm vi: thuế, kế toán, hóa đơn, bảo hiểm, vận chuyển, đổi trả sau giao, chống gian lận, bảo vệ dữ liệu, KPI | "Xuất hóa đơn cọc thế nào?" |

Luật:
1. Chỉ nộp test điểm ≤ 1.
2. Điểm 2 hạ xuống 1 khi có bằng chứng phạm vi **mức 1 hoặc mức 2** (§2-9): lời AI Khách hàng nguyên văn có timestamp, hoặc câu brief tường minh. Bằng chứng **mức 3** (đồng thuận chéo §4b) hạ điểm 2 xuống 1,5 — nộp được nếu ước `P(TRÚNG)` cao, xem luật 7.
3. Điểm 3 không nộp với bất kỳ lý do.
4. Câu hỏi chạm 2 miền → lấy điểm cao nhất.
5. Buổi sáng chưa hỏi "liệt kê nghiệp vụ NGOÀI phạm vi" [HD §5.6] → mọi test +1 điểm.
6. Người chấm phạm vi ≠ người viết test; ghi điểm vào hồ sơ §7.
7. **Chấm kỳ vọng trước khi chốt slot** (00 §D1). Ước hai xác suất rồi tính `EV = 2·P(TRÚNG) − 1·P(VÔ HIỆU)`:

   | Điểm phạm vi | P(VÔ HIỆU) ước | Nộp khi |
   |---|---|---|
   | 0 | ~0,05 | P(TRÚNG) ≥ 0,05 — tức gần như luôn nộp |
   | 1 | ~0,15 | P(TRÚNG) ≥ 0,10 |
   | 1,5 (điểm 2 + bằng chứng mức 3) | ~0,30 | P(TRÚNG) ≥ 0,20 |
   | 2 không có bằng chứng | ~0,50 | không nộp |

   Ước `P(TRÚNG)` từ dry-run executor mù: executor trả lời khác đáp án chuẩn và ĐỘ PHỦ = ĐỦ ⇒ 0,8; khác và ĐỘ PHỦ = KHÔNG ⇒ 0,6; trùng đáp án chuẩn ⇒ 0,1 (đổi test). **Bỏ một slot là 0 điểm, không phải −1** — test thứ 5 âm kỳ vọng thì đừng nộp.
8. Câu trả lời `"Không có quy định riêng."` **KHÔNG** phải bằng chứng trong phạm vi — nó chỉ nói specs thật không quy định, không nói nghiệp vụ thuộc tính năng. Chỉ hai thứ hạ được điểm phạm vi: (a) mục nằm trong danh sách TRONG phạm vi (N1-03), hoặc (b) một câu trả lời nghiệp vụ cụ thể (con số / trạng thái cuối / hướng tiền / ai thắng) về đúng nghiệp vụ đó. Dòng "chưa rõ" trong RTM giữ nguyên điểm phạm vi gốc — đừng coi im lặng của specs thật là giấy thông hành.

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
| Đáp án chuẩn kỳ vọng — **nguồn** | `log C<n> hh:mm` / `brief câu …` / `mặc định ngành (10 §6)` — ghi rõ nguồn, vì nó quyết định mức bằng chứng |
| **Gói bằng chứng phạm vi** | Mức 1 / 2 / 3 (§2-9) đã trích nguyên văn **ngay lúc này**, không để tới 16:00 — đây là toàn bộ hồ sơ kháng nghị nếu bị VÔ HIỆU |
| Điểm phạm vi & kỳ vọng | 0–3 theo §6, người chấm; kèm `P(TRÚNG)`, `P(VÔ HIỆU)`, `EV` (§6 luật 7) |
| Kết quả máy | TRÚNG / TRƯỢT / VÔ HIỆU + lý do đối chiếu của AI So khớp |
| Đánh giá | Đồng ý / Ứng viên kháng nghị (ưu tiên 1–3) |

### Kháng nghị — chỉ một loại ca

**Tham số 09/09 (00 §A):** kháng nghị **chỉ mở cho ca test CÔNG của mình bị chấm VÔ HIỆU**, nộp bằng **text gửi AI**, ban tổ chức review; ≤3 ca/đội.

Hai loại ca của kit cũ **không còn kháng nghị được**: (a) spec mình bị chấm TRÚNG dù có quy định; (b) AI So khớp hiểu sai ngữ nghĩa làm test mình thành TRƯỢT. Hệ quả trực tiếp: **rủi ro của vai THỦ không có đường lùi**, nên mọi cẩn trọng dồn vào chất lượng spec trước 12:00; còn rủi ro của vai CÔNG cứu được một phần, nên ngưỡng nộp test tính theo kỳ vọng (§6 luật 7).

Điều duy nhất phải chứng minh khi kháng nghị: **tình huống nằm TRONG phạm vi specs thật**. Không tranh đáp án chuẩn, không tranh cách Executor trả lời — chấm lại phạm vi là toàn bộ điều đang xin.

| Ưu tiên | Bằng chứng | Lập luận mẫu (2 câu) |
|---|---|---|
| 1 | **Mức 1** — AI Khách hàng đã trả lời nghiệp vụ đó | "Lúc [hh:mm], khi được hỏi [trích câu hỏi], AI Khách hàng trả lời [trích nguyên văn]. Câu trả lời đó là quy định nghiệp vụ cho đúng tình huống của test này, nên tình huống nằm trong phạm vi specs thật; đề nghị chấm lại phạm vi." |
| 2 | **Mức 2** — brief nhắc tường minh | "Brief do BTC phát ghi nguyên văn [trích câu brief]. Nghiệp vụ này không thể nằm ngoài phạm vi tính năng khi chính đề bài mô tả nó; đề nghị chấm lại phạm vi." |
| 3 | **Mức 3** — đồng thuận chéo | "Spec của đội [X] mục [§] và đội [Y] mục [§] đều có luật cho nghiệp vụ này [trích cả hai]. Hai đội độc lập cùng xếp nghiệp vụ này trong phạm vi tính năng; đề nghị chấm lại phạm vi." |

Giới hạn phải tự biết và nói trước khi trọng tài chỉ ra: mức 2 và mức 3 chỉ chứng minh nghiệp vụ **thuộc tính năng**, không chứng minh specs thật **có luật** cho nó. Vì vậy lập luận chỉ xin chấm lại phạm vi, không xin đổi thành TRÚNG.

**Mẫu text kháng nghị (≤150 từ/ca, gửi AI):**

```
Test: <ID> — <tình huống nguyên văn>
Kết quả máy: VÔ HIỆU
Đề nghị: chấm lại phạm vi.
Căn cứ: <trích nguyên văn 1 nguồn mạnh nhất + vị trí: mã câu + timestamp, hoặc câu brief, hoặc §mục của 2 spec>
Lập luận: <2 câu, chỉ về phạm vi>
Giới hạn: <1 câu tự nêu điểm yếu của bằng chứng, nếu là mức 2 hoặc 3>
```

Câu "Giới hạn" không phải khiêm tốn: người review thấy đội tự nêu đúng giới hạn của bằng chứng sẽ tin phần còn lại hơn, và nó chặn trước lý bác duy nhất mà họ có.

Bằng chứng chuẩn bị trước 15:00 (không kịp thu lúc 16:00): log AI Khách hàng có timestamp + ảnh chụp từng câu trả lời, bản copy spec đã nộp, 15 hồ sơ finding **kèm gói bằng chứng phạm vi 3 mức**, bảng đồng thuận chéo §4b, 3 spec đối thủ đã tải về.
