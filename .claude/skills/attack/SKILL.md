---
name: attack
description: Soi một spec đối thủ trong 10 phút — mục "Điểm chưa chốt" của đối thủ, bảng 14 ô đề luôn chốt, cấu trúc 10 mục BTC, cổng khả thi F, 27 loại lỗ hổng, đồng thuận chéo 3 spec, 12 kịch bản suy biến, phạm vi tự đặt — rồi sinh tối đa 5 test đa dạng loại kèm hồ sơ finding, gói bằng chứng phạm vi 3 mức để kháng nghị, điểm kỳ vọng theo công thức +2/−1, và dry-run qua agent executor mù. Dùng 13:00–14:30 ngày thi khi người dùng nói "soi spec đối thủ", "bắn spec", "tạo 5 test", "attack", "red team đội X"; cũng dùng để tự bắn spec mình sau /spec-review.
argument-hint: "cheo <spec1> <spec2> <spec3> [thư-mục] | hoi <đường-dẫn-spec-đối-thủ> [thư-mục-trận] | <đường-dẫn-spec-đối-thủ> <tên-đội> [thư-mục-trận]"
allowed-tools: Read, Write, Edit, Grep, Glob, Agent, Bash(date *), Bash(mkdir *)
---
# /attack — tối đa 5 test cho một spec đối thủ

Mục tiêu: mỗi test một loại lỗ hổng khác nhau, nhắm vào chỗ specs thật phản trực giác mà spec đối thủ im lặng, **mỗi test có điểm kỳ vọng dương và một gói bằng chứng phạm vi thu sẵn**, đã kiểm bằng executor mù.

**Bài học thi thử 11/09 — 2/2 viên TRƯỢT (knowledge/50 §0). Ba luật cứng sinh ra từ đó, đọc trước khi làm gì khác:**
- **Cấm cược vào đáp án chuẩn.** Không nộp test mà lý do TRÚNG bắt đầu bằng "specs thật gần như chắc chắn…". Suy luận hợp lý là đúng thứ đề cố bẻ và cũng là đúng thứ Executor mù sẽ đoán ⇒ hai bên trùng ⇒ TRƯỢT. Mỗi test phải thuộc **cơ chế A** (ta có lời khách nguyên văn và đối thủ nói khác) hoặc **cơ chế B** (ô bảng knowledge/50 §8, đối thủ im lặng/tự khai chưa chốt, không gian đáp án `W ≥ 4`). Không thuộc A cũng không B ⇒ **bỏ slot**.
- **Im lặng của AI Khách hàng không phải bằng chứng phủ định.** Khách trả lời một ẩn số mỗi lượt, không memory. Không được bắn vào một con số đối thủ tự đặt chỉ vì khách không nhắc tới nó — đối thủ có thể đã hỏi ô ta không hỏi.
- **TRƯỢT không phải 0** — nó cho đội thủ +1. Ngưỡng nộp thật: `P(TRÚNG) > (1 + 2·P(VÔ HIỆU))/3` ≈ **0,37** (knowledge/50 §6 luật 9). Test nhị phân (`W = 2`) có `P ≈ 0,5` vẫn đáng nộp, test cược có `P = 0,25` thì không.

**Ba tham số 09/09 đổi cách chấm (00 §A, §D1):**
- **TRÚNG +2 · TRƯỢT 0 nhưng đội thủ +1 · VÔ HIỆU −1 · bỏ slot 0** ⇒ nộp khi `EV_rel = 3·P(TRÚNG) − 1 − 2·P(VÔ HIỆU) > 0` (knowledge/50 §6 luật 9); **nộp 4 test tốt hơn nộp 5 test trong đó 1 test đoán bừa**.
- **Test không sửa được sau khi nộp** ⇒ không có "dự phòng thay slot" sau 15:00; 2 test dự phòng chỉ dùng để đổi *trước* khi nộp.
- **Chỉ ca VÔ HIỆU được kháng nghị** ⇒ gói bằng chứng phạm vi (50 §2-9, ba mức) phải xong lúc nộp, không phải lúc 16:00.

## Input
`$ARGUMENTS` = `cheo <spec1> <spec2> <spec3> [thư-mục]` (chế độ đồng thuận chéo, **chạy trước tiên lúc 13:00**) · `hoi <spec-đối-thủ> [thư-mục-trận]` (**sinh lượt hỏi nạp đạn — chạy ngay sau `cheo` nếu AI Khách hàng còn mở**) · hoặc `<spec-đối-thủ> <tên-đội> [thư-mục-trận]`. Thư mục trận mặc định `battle/` — lấy `rtm.md` (dòng `A-xx` ⚠ = băng đạn chắc nhất; dòng `G-xx` = đạn suy luận; mục NGOÀI phạm vi = rào chống VÔ HIỆU), `log-khach-hang.md` (mọi câu trả lời nguyên văn + timestamp; bỏ qua khối `[BỊ TỪ CHỐI]`), `brief.md` (bằng chứng phạm vi mức 2), `review.md` mục `RỦI RO ĐÃ BIẾT` (khi tự bắn spec mình).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/33-cau-truc-spec-btc.md` §1 (10 mục BTC — dùng để tick mục vắng), §2 (5 gạch bắt buộc của bảng Case ở mục 6), §7 (Mermaid — sơ đồ không thay bảng).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md`: **§0 (bài học 11/09 — đọc trước tiên)**, **§9 (nạp đạn bằng lượt hỏi AI Khách hàng — `Δ`, scope-probe, đếm-probe)**, §1 (**27 loại**, gồm #16–#20 nội dung, #21 đồng thuận chéo, #22–#24 cấu trúc 10 mục, **#25 ô đối thủ tự khai chưa chốt · #26 đơn vị/bội số/trần · #27 thao tác hoàn tác**), §2 (quy tắc viết tình huống + §2-9 ba mức bằng chứng phạm vi + §2-10 bỏ slot + **§2-13 cổng hỏi hành vi · §2-14 cấm cược · §2-15 im lặng ≠ phủ định · §2-16 xếp theo `W`**), §3 (phân bổ test), §4 (quy trình 10 phút + cổng F rút gọn bằng grep), §4b (bảng đồng thuận chéo 3 spec), §5 (probe **P1–P61**, gồm **P59–P61 cơ chế B**), §6 (rubric phạm vi + **luật 9 điểm kỳ vọng tương đối + bảng gán `P(TRÚNG)`**), §7 (hồ sơ finding + kháng nghị chỉ ca VÔ HIỆU), **§8 (bảng 14 ô đề luôn chốt — nguồn đạn chính)**.
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md` §1 (6 lệnh grep cổng F — chạy trên spec đối thủ), §2 (bảng thực tế phụ thuộc ngoài — dùng để chứng minh luật đối thủ bất khả thi).
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §M6 (12 kịch bản suy biến — checklist đối chiếu spec đối thủ), §M5 (6 kẻ lạm dụng).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 (regex danh sách đen **22 nhóm**), §3 (**S31–S39** kiểm cấu trúc 10 mục), §4 (chuyển hit → tình huống).
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md` §5 (ô trống/overlap → tình huống).
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §6 (edge case ⚠ để đối chiếu nhanh).
- `<thư-mục-trận>/mo-hinh-bai-toan.md` — M1 mục tiêu (để soi #17 trên spec đối thủ), M4 láng giềng, M6 12 ca.

## Chế độ `cheo` (13:00–13:15, chạy MỘT LẦN trước khi soi từng spec)
1. Tải/đọc cả ba spec đối thủ (markdown — 00 §A). Lập danh sách ~25 nghiệp vụ từ 14 nhóm N1–N14 (knowledge/20 §2) + 12 ca suy biến (05 §M6).
2. Điền bảng knowledge/50 §4b bằng grep, mỗi dòng một lệnh `grep -n -i -E "<3 từ khóa>"` trên cả 3 file + `spec.nop.md` của đội mình.
3. Xuất `<thư-mục>/dong-thuan-cheo.md`: bảng đầy đủ + ba danh sách ứng viên xếp theo độ mạnh — (a) **hai spec chỏi nhau** về cùng nghiệp vụ (một tình huống bắn được cả hai); (b) **một spec im lặng, ≥2 spec có luật** (#21, có sẵn bằng chứng phạm vi mức 3); (c) **cả ba im lặng** (điểm mù chung — chỉ bắn khi có bằng chứng mức 1 hoặc 2).
4. In thêm cột "Spec mình": dòng nào ta im lặng mà ≥2 đội có luật = chỗ ta gần chắc bị bắn. Không sửa được nữa (spec khóa 12:00) — ghi vào bài học.

## Chế độ `hoi` — sinh lượt hỏi AI Khách hàng để nạp đạn (chạy ngay sau khi nhận spec đối thủ, TRƯỚC khi soạn test)

Chỉ chạy được khi AI Khách hàng còn mở ở pha CÔNG (00 §A2-4 — kiểm đầu ngày). Đóng rồi thì bỏ qua chế độ này và đọc knowledge/50 §9.3.

Ba thẻ kết quả của hệ chấm (50 §9) nói: TRÚNG cần **đối thủ im lặng ở X** *và* **specs thật lệch mặc định ngành ở X**; VÔ HIỆU do **chính AI Khách hàng phán phạm vi**. Cả hai vế đều hỏi được, và một lượt hỏi rẻ hơn một viên đạn hỏng (−1 điểm, cộng +1 cho đối thủ).

1. Chạy bước 0 và 0b của mục "Bước" để có danh sách ô đối thủ im lặng / tự khai chưa chốt.
2. **Điền cột "mặc định ngành" trước** cho từng ô, tra `<thư-mục-trận>/mac-dinh-nganh.md`. **Không gửi lượt nào khi cột này còn trống** — không có nó thì câu trả lời về cũng không đọc được là `Δ = 0` hay `Δ ≠ 0`.
3. Sinh lượt theo ba dạng của 50 §9.1, **scope-probe trước Δ-probe** cho cùng một ứng viên:
   - **Scope-probe** cho mọi ứng viên điểm phạm vi ≥ 1 hoặc nằm ở rìa ba màn hình. Đáp "ngoài phạm vi" ⇒ **xoá ứng viên ngay** và ghi vào danh sách rào.
   - **Δ-probe** hỏi thẳng giá trị của ô, dạng số hoặc nhị phân.
   - **Đếm-probe** cho ô Đ7 và mọi danh sách đóng (`全部で何種類`).
4. Chạy **cổng 8 kiểm tra** (20 §1) trên cả loạt một lần. Câu bị từ chối không mất token, không mất nhịp — sửa rồi gửi lại.
5. Xếp hàng đợi theo `giá trị kỳ vọng của lượt` = (ứng viên đó đang ở `P` bao nhiêu) × (câu trả lời nâng được lên bao nhiêu). Ô cơ chế B `P` = 0,55 mà một lượt nâng lên 0,85 thì lãi hơn ô đã là cơ chế A.
6. Ghi `<thư-mục-trận>/nap-dan.md`: bảng 50 §9.2 (ứng viên · mặc định ngành · lượt hỏi nguyên văn · đáp = mặc định ⇒ BỎ · đáp ≠ mặc định ⇒ BẮN) + hàng đợi đã xếp + ô trống chờ điền câu trả lời.
7. Nạp câu trả lời xong: mỗi ô đánh **`Δ = 0` ⇒ loại ứng viên** (Executor sẽ đoán trúng — đây đúng là ca TRƯỢT ở thẻ giữa), **`Δ ≠ 0` ⇒ nâng lên cơ chế A, `P(TRÚNG)` = 0,85**, **deflection ⇒ loại** (không có đáp án chuẩn thì không có ca TRÚNG). Rồi chạy chế độ thường để soạn test.

## Bước (mốc phút theo knowledge/50 §4)
0. **0–1 Mục "Điểm chưa chốt" của đối thủ — làm TRƯỚC MỌI BƯỚC KHÁC.** Grep spec đối thủ: `chưa chốt|chưa rõ|TBD|確認中|課題|未定|要確認|Open issue|Câu hỏi`. Mỗi dòng tìm được là một chỗ **đối thủ tự khai Executor của họ sẽ phải bịa** — loại #25, `P(TRÚNG)` khởi điểm **0,70**, và rủi ro VÔ HIỆU thấp nhất trong mọi nguồn đạn vì chính đối thủ đã nhận nghiệp vụ đó thuộc phạm vi bằng cách viết nó ra. Với mỗi dòng: đối chiếu bảng knowledge/50 §8 xem nó rơi vào ô Đ nào, đếm `W`, dựng tình huống bằng probe P59. **Nếu bước này ra ≥2 ứng viên `W ≥ 4` thì đó là các slot đầu tiên, không cần chờ bước 5.**
0b. **1–2,5 Quét bảng 14 ô "đề luôn chốt"** (knowledge/50 §8) bằng grep từ khoá miền trên spec đối thủ. Ô nào **0 hit** mà nằm trong phạm vi brief ⇒ ứng viên cơ chế B; ô nào đối thủ viết rõ ⇒ bỏ, **trừ khi** ta có lời khách nguyên văn nói khác (khi đó thành cơ chế A, `P = 0,85`). Xếp ứng viên theo `W` giảm dần. Đây là nguồn đạn **duy nhất còn sống** khi đối thủ đã chắn hết dòng ⚠ của ta — tình huống 11/14 dòng bị chắn của 11/09 là bình thường, không phải bất thường, vì hai đội đọc cùng brief và hỏi cùng một AI Khách hàng.
0c. **AI Khách hàng còn mở?** Còn ⇒ dừng ở đây, chạy **chế độ `hoi`** trước (mỗi lượt hỏi biến một ứng viên `P` = 0,55 thành `P` = 0,85, hoặc loại sớm một ứng viên sẽ TRƯỢT). Đóng rồi ⇒ lọc `rtm.md` lấy các dòng đánh `Δ ≠ 0` (20 §5) làm băng đạn xếp sẵn, rồi đi tiếp.
1. **2,5–3,5 Mục lục**: tick **10 mục BTC** (knowledge/33 §1) có/không, rồi tick 14 nhóm nghiệp vụ N1–N14 (knowledge/20 §2 tên nhóm). Mục hoặc nhóm vắng = ứng viên hạng A (#1). Mục 4 vắng ⇒ #22; mục 2 hoặc 8 vắng cột/dòng Guest ⇒ #23; mục 2/3 không nói trạng thái nút ⇒ #24.
1b. **1,5–3 Cổng F rút gọn** — chạy 4 lệnh grep của knowledge/32 §1 trên spec đối thủ:
   - `hoàn tất|tiền về|đối soát xong` + số giờ/ngày ⇒ **#16** (luật bất khả thi). Chứng cứ bảng knowledge/32 §2: hoàn tiền thẻ không hoàn tất trong giờ, và hoàn về thẻ đóng có thể thất bại ⇒ specs thật không viết như họ ⇒ probe P43/P44.
   - hạn mức neo vào `email|số điện thoại|tự khai` ⇒ **#18** ⇒ probe P47.
   - nhắc `cổng|ERP|job|thông báo|callback` mà **không có nhánh lỗi nào** ⇒ **#19** ⇒ probe P48–P51. Đây là loại hở nhiều nhất: hầu hết spec nhắc cổng thanh toán nhưng chỉ viết nhánh thành công.
   - `guest|hoàn 100|không phí|miễn phí` + đọc bảng step của mục 6 xem tài nguyên bị khóa trước hay sau rào ⇒ **#17** ⇒ probe P45/P46.
   Mỗi hit ở bước này là ứng viên **hạng A** và có đáp án chuẩn nếu buổi sáng đã hỏi nhóm N0.
1c. **3–4 Mục 4 (message) + mục 2/8 (guest, trạng thái UI)**: Grep mục validation — có chuỗi message trong ngoặc kép không? Chỉ ghi "hiển thị thông báo lỗi / báo lỗi phù hợp" = **#22 hạng A**, bắn P53/P53b ("hệ thống hiển thị message gì, nguyên văn?"). Grep `guest|chưa đăng nhập`: 0 hit = **#23 hạng A**, bắn P54/P54b. Không nói nút disable/ẩn khi nào, không nói sau thao tác hiển thị gì = **#24**, bắn P55/P55b. Ba loại này không cần biết specs thật để phát hiện, và rủi ro VÔ HIỆU thấp vì đều nằm trong core flow.
2. **4–5 Lint**: Grep `-i -n` các nhóm 1, 3, 4, 6, 8, 11 **và 17–21** của knowledge/40 §2 trên spec đối thủ; mỗi hit trong core flow = ứng viên #3/#4/#16–#19 (chuyển theo knowledge/40 §4).
3. **5–6,5 Mục 6 (bảng Case + bảng trạng thái)**: với từng logic, đếm case bình thường / biên / lỗi — thiếu loại nào = ứng viên; kiểm **5 gạch** (số · toán tử `>`/`≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng) — gạch thiếu nào là một tình huống bắn sẵn. Bảng trạng thái / decision table: không có → #6/#8; **chỉ có sơ đồ Mermaid mà không có bảng → #6 hạng A** (sơ đồ chỉ vẽ chuyển hợp lệ, thiếu hẳn ô `Từ chối 0.5`/`KHL` — knowledge/33 §7.2). Có bảng → liệt kê ô trống, đếm tổ hợp (knowledge/31 §5), sự kiện vắng (admin hủy, cọc thất bại, kho lệch, SKU ngừng bán).
4. **6,5–7,5 Mục 7 + sáu mục hiếm**: mục 7.2 có đủ 4 ca BTC nêu đích danh không (dữ liệu đổi giữa hiển thị và submit → P56 · gửi trùng → P34 · mở link hai lần → P57 · mail fail sau khi đã lưu → P58)? Vắng ca nào = tình huống bắn sẵn. Rồi sáu mục hiếm**: Grep "đồng thời|cùng lúc|first-come", "múi giờ|timezone|UTC|ngày lễ", "guest|chưa đăng nhập", "rollback|hoàn nguyên|kiểm kê|lệch", "ưu tiên|ghi đè|trừ khi", "safety|tồn đệm|an toàn|oversell". 0 hit = ứng viên hạng A.
4b. **7,5–8 Đối chiếu 12 ca suy biến + phạm vi**: mỗi ca knowledge/05 §M6 → spec đối thủ có luật bao trùm không (ứng viên #19). Rồi đọc danh sách NGOÀI phạm vi của họ, so với brief: nghiệp vụ nào brief nhắc tường minh mà họ đẩy ra ngoài ⇒ **#20**, probe P52 — **phải trích được câu brief** mới nộp.
5. **Knowledge boundary sweep**: lập bảng — mỗi dòng `A-xx` ⚠ trong `rtm.md` | spec đối thủ có nói không? (trích mục / KHÔNG / nói KHÁC specs thật) | ứng viên?. Im lặng hoặc nói khác ⇒ ứng viên hạng A (#15), **bằng chứng phạm vi mức 1 có sẵn**. Dòng đối thủ đã viết đúng ⇒ vào bảng "⚠ đã bị chắn" (không bắn).
5b. **Nạp ứng viên từ `dong-thuan-cheo.md`**: mọi dòng thuộc danh sách (a) và (b) liên quan tới spec này ⇒ ứng viên #21. Danh sách ⚠ ở bước 5 thường chỉ 12–25 dòng ngay cả khi buổi sáng kịp 15–20 lượt hỏi, nên bước này thường vẫn là nguồn ứng viên lớn nhất (50 §3).
6. **Soạn 8–10 ứng viên** theo mẫu câu knowledge/50 §2 (thời điểm tuyệt đối, loại khách, số lượng, trạng thái hiện tại; kết thúc bằng một câu hỏi ép con số/trạng thái/ai thắng/hoàn tiền; ≤60 từ; không mã BR, không thuật ngữ riêng của đội mình). Gắn loại # và probe P. Đa dạng theo knowledge/50 §3.
7. **Chấm phạm vi + gói bằng chứng** từng ứng viên theo rubric knowledge/50 §6. Điểm 3 loại ngay. Điểm 2 hạ xuống 1 khi có bằng chứng **mức 1** (lời AI Khách hàng nguyên văn) hoặc **mức 2** (câu brief tường minh); hạ xuống 1,5 khi chỉ có **mức 3** (≥2 spec có luật — trích số mục cả hai). Không có mức nào ⇒ loại. Buổi sáng chưa gửi lượt hỏi nào về NGOÀI phạm vi → mọi điểm +1.
   Với mỗi ứng viên còn lại, **copy sẵn trích dẫn nguyên văn của bằng chứng vào hồ sơ finding ngay tại bước này** — đây là toàn bộ hồ sơ kháng nghị nếu bị VÔ HIỆU, và 16:00 không còn thời gian đi tìm.
8. **Dry-run executor mù**: với từng ứng viên còn lại gọi Agent `subagent_type: executor`, prompt CHỈ gồm:
   ```
   Đường dẫn spec: <đường dẫn tuyệt đối spec đối thủ>
   Tình huống: <văn bản test>
   ```
   Không kèm RTM, log, đáp án chuẩn. Gọi song song. Tiêu chí duy nhất để GIỮ: TRẢ LỜI của executor khác đáp án chuẩn trong RTM về ý nghĩa (trạng thái cuối / con số / ai thắng / tiền). Phân hạng: ĐỘ PHỦ = ĐỦ mà vẫn khác ⇒ spec đối thủ viết TRÁI specs thật — hạng A+ (TRÚNG gần chắc chắn); ĐỘ PHỦ KHÔNG/MỘT PHẦN hoặc ĐA NGHĨA ⇒ hạng A. Executor trả lời trùng đáp án chuẩn (dù spec im lặng) ⇒ TRƯỢT dự kiến → loại (knowledge/50 §2-11).
8b. **Chấm điểm kỳ vọng** cho từng ứng viên còn lại theo knowledge/50 §6 **luật 9**. `P(TRÚNG)` **chỉ được lấy từ bảng gán của §6**, cấm gán theo cảm giác — ghi rõ ứng viên thuộc cơ chế nào:

   | Ứng viên | P(TRÚNG) |
   |---|---|
   |A: có lời khách nguyên văn, đối thủ nói **khác** nó|0,85|
   |A yếu: có lời khách nguyên văn, đối thủ im lặng, dry-run ra kết quả khác lời khách|0,70|
   |B: ô §8, đối thủ **tự khai chưa chốt** (#25), `W ≥ 4`|0,70|
   |B: ô §8, đối thủ im lặng, `W ≥ 4`|0,55|
   |B: `W = 3`|0,40|
   |`W = 2` nhị phân · cược §2-14 · dry-run trùng lời khách|0,25 / 0,25 / 0,10 ⇒ **loại**|

   `P(VÔ HIỆU)` từ điểm phạm vi (0 ⇒ 0,05; 1 ⇒ 0,15; 1,5 ⇒ 0,30). `EV_rel = 3·P(TRÚNG) − 1 − 2·P(VÔ HIỆU)`. Ứng viên `EV_rel ≤ 0` loại, ghi lý do. Ngưỡng thực tế: `P(TRÚNG) ≥ 0,40` mới qua.
9. **Chọn tối đa 5 + 2 dự phòng** theo phân bổ knowledge/50 §3: các loại # khác nhau, phủ ≥4 nhóm N, tối đa 2 test cùng chủ đề. Ưu tiên: #15 → **#16/#18/#19/#21** → **#22/#23** (message nguyên văn, guest — spec viết theo lối luật nghiệp vụ thuần hở cả hai) → #2/#12 → #4/#11 → #7/#8/#17. Slot #9/#10 (actor, quyền) xuống dự phòng.
   **Nếu số ứng viên `EV > 0` ít hơn 5 thì nộp ít hơn 5.** Bỏ slot là 0 điểm, nộp một test đoán bừa là −1 và không cứu được bằng kháng nghị (kháng nghị chỉ tranh *phạm vi*, không tranh việc test kém). Nói rõ trong output: "nộp k/5, k' slot bỏ trống vì EV ≤ 0".
9b. **Kiểm chéo với spec mình**: mỗi loại # vừa bắn được đối thủ, đánh dấu để `/spec-review` khối G kiểm lại chính spec mình cùng loại đó. Lỗ hổng tìm thấy ở đối thủ thường cũng có ở ta — cả hai đội đọc cùng một brief.
10. **Hồ sơ finding** cho mỗi test theo knowledge/50 §7: ID `T-<đội><n>`, loại #/probe, tình huống nộp nguyên văn, chỗ spec im lặng/mâu thuẫn (trích số mục hoặc "không mục nào đề cập" + 3 từ khóa đã Ctrl+F), đáp án chuẩn kỳ vọng (trích log nguyên văn + timestamp + ID RTM), dự đoán Executor (từ dry-run), điểm phạm vi, ô "Kết quả máy" và "Đánh giá" để trống.

## Output `tests/<tên-đội>.md` (trong thư mục trận; mkdir nếu cần)
1. Khối "TEST NỘP (k ≤ 5)" — chỉ nguyên văn các tình huống được chọn, đánh số, copy được. Nếu k < 5, ghi ngay dưới khối: "bỏ <5−k> slot vì EV ≤ 0" kèm ứng viên bị loại và lý do.
2. Hồ sơ finding cho từng test đã chọn + 2 dự phòng (đánh dấu DP) — **mỗi hồ sơ có gói bằng chứng phạm vi đã trích nguyên văn**.
3. Bảng knowledge boundary sweep (bước 5) gồm mục "⚠ đã bị chắn".
3b. Bảng "10 mục BTC của spec đối thủ: có / thiếu / rỗng" (bước 1).
3c. **Bảng cổng F trên spec đối thủ** (bước 1b + 4b): hit nào ở F1/F2/F5/F8, ca suy biến nào hở, nghiệp vụ nào brief nhắc mà họ đẩy ra ngoài — kể cả ứng viên không được chọn, để tái dùng khi soi spec khác.
4. Dòng kiểm: **mỗi test ghi rõ cơ chế A hay B ✓** | **mọi `P(TRÚNG)` lấy từ bảng §6, không gán tay ✓** | **≥1 test từ mục "Điểm chưa chốt" của đối thủ nếu mục đó tồn tại ✓** | **mọi test dạng hỏi hành vi hệ thống, không hỏi giá trị ✓** | các loại # khác nhau ✓ | mọi điểm phạm vi ≤1,5 ✓ | mọi test ≤60 từ ✓ | **mọi test có gói bằng chứng phạm vi mức 1/2/3 đã trích nguyên văn** ✓ | mọi test có `EV > 0` ✓ | **≥1 test thuộc #22/#23 nếu spec đối thủ thiếu mục 4 hoặc cột Guest** ✓ | **≥1 test thuộc #16–#21** ✓ | số test nộp = k (nêu rõ nếu k < 5).
5. Danh sách loại # cần kiểm chéo lại trên spec mình (bước 9b).

## Không được
- **Nộp test mà cơ sở TRÚNG là suy luận "specs thật chắc phải thế"** (knowledge/50 §2-14). Đây là lỗi làm trượt cả 2 viên ở thi thử 11/09. Mỗi hồ sơ finding phải ghi rõ **cơ chế A hay B**; không ghi được ⇒ không nộp.
- **Bắn vào một con số đối thủ tự đặt chỉ vì AI Khách hàng không nhắc tới nó** (§2-15). Chỉ bắn khi có lời khách nguyên văn nêu **con số khác**.
- Hỏi 「いくら / bao nhiêu tiền」 thay vì hỏi hành vi hệ thống (§2-13) — hệ thống trả câu về, mất một nhịp.
- Hai test cùng loại #; test điểm phạm vi ≥2 không có bằng chứng mức 1/2/3; test về thuế/kế toán/bảo hiểm/vận chuyển quốc tế/đổi trả sau giao.
- **Nộp cho đủ 5 slot khi ứng viên còn lại có `EV ≤ 0`** — mỗi test bừa là −1 điểm chắc chắn.
- Nộp test mà bằng chứng phạm vi chưa được trích nguyên văn vào hồ sơ; test đã nộp không sửa được và kháng nghị chỉ mở cho ca VÔ HIỆU.
- Coi "cả 3 spec đều im lặng" là bằng chứng ngoài phạm vi (đó là điểm mù chung), hoặc coi nó là bằng chứng trong phạm vi.
- Nộp test loại #20 mà không trích được câu brief nhắc nghiệp vụ đó.
- Nộp test loại #16 mà không nêu được trong hồ sơ finding *vì sao* luật của đối thủ bất khả thi (dẫn bảng knowledge/32 §2) — thiếu phần này thì không kháng nghị được nếu bị chấm TRƯỢT.
- Câu hỏi mở, câu hỏi kép, câu hỏi dùng thuật ngữ hoặc mã của đội mình.
- Đưa RTM/log/đáp án vào prompt gọi executor.
- Nộp test không có bất kỳ mức bằng chứng phạm vi nào (không kháng nghị được).
