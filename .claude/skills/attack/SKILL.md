---
name: attack
description: Soi một spec đối thủ trong 10 phút — cấu trúc 10 mục BTC, cổng khả thi F, 24 loại lỗ hổng, đồng thuận chéo 3 spec, 12 kịch bản suy biến, phạm vi tự đặt — rồi sinh tối đa 5 test đa dạng loại kèm hồ sơ finding, gói bằng chứng phạm vi 3 mức để kháng nghị, điểm kỳ vọng theo công thức +2/−1, và dry-run qua agent executor mù. Dùng 13:00–14:30 ngày thi khi người dùng nói "soi spec đối thủ", "bắn spec", "tạo 5 test", "attack", "red team đội X"; cũng dùng để tự bắn spec mình sau /spec-review.
argument-hint: "cheo <spec1> <spec2> <spec3> [thư-mục] | <đường-dẫn-spec-đối-thủ> <tên-đội> [thư-mục-trận]"
allowed-tools: Read, Write, Edit, Grep, Glob, Agent, Bash(date *), Bash(mkdir *)
---
# /attack — tối đa 5 test cho một spec đối thủ

Mục tiêu: mỗi test một loại lỗ hổng khác nhau, nhắm vào chỗ specs thật phản trực giác mà spec đối thủ im lặng, **mỗi test có điểm kỳ vọng dương và một gói bằng chứng phạm vi thu sẵn**, đã kiểm bằng executor mù.

**Ba tham số 09/09 đổi cách chấm (00 §A, §D1):**
- **TRÚNG +2 · VÔ HIỆU −1 · bỏ slot 0** ⇒ nộp khi `EV = 2·P(TRÚNG) − P(VÔ HIỆU) > 0`; **nộp 4 test tốt hơn nộp 5 test trong đó 1 test đoán bừa**.
- **Test không sửa được sau khi nộp** ⇒ không có "dự phòng thay slot" sau 15:00; 2 test dự phòng chỉ dùng để đổi *trước* khi nộp.
- **Chỉ ca VÔ HIỆU được kháng nghị** ⇒ gói bằng chứng phạm vi (50 §2-9, ba mức) phải xong lúc nộp, không phải lúc 16:00.

## Input
`$ARGUMENTS` = `cheo <spec1> <spec2> <spec3> [thư-mục]` (chế độ đồng thuận chéo, **chạy trước tiên lúc 13:00**) hoặc `<spec-đối-thủ> <tên-đội> [thư-mục-trận]`. Thư mục trận mặc định `battle/` — lấy `rtm.md` (dòng `A-xx` ⚠ = băng đạn chắc nhất; dòng `G-xx` = đạn suy luận; mục NGOÀI phạm vi = rào chống VÔ HIỆU), `log-khach-hang.md` (mọi câu trả lời nguyên văn + timestamp; bỏ qua khối `[BỊ TỪ CHỐI]`), `brief.md` (bằng chứng phạm vi mức 2), `review.md` mục `RỦI RO ĐÃ BIẾT` (khi tự bắn spec mình).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/33-cau-truc-spec-btc.md` §1 (10 mục BTC — dùng để tick mục vắng), §2 (5 gạch bắt buộc của bảng Case ở mục 6), §7 (Mermaid — sơ đồ không thay bảng).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md`: §1 (**24 loại**, gồm #16–#20 nội dung, **#21 lệch đồng thuận chéo**, **#22–#24 cấu trúc 10 mục**), §2 (quy tắc viết tình huống + **§2-9 ba mức bằng chứng phạm vi** + §2-10 bỏ slot), §3 (phân bổ 5 test + trọng số nguồn đạn mới), §4 (quy trình 10 phút + cổng F rút gọn bằng grep), **§4b (bảng đồng thuận chéo 3 spec)**, §5 (probe **P1–P52**), §6 (rubric phạm vi + **luật 8 điểm kỳ vọng**), §7 (hồ sơ finding + kháng nghị chỉ ca VÔ HIỆU).
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

## Bước (mốc phút theo knowledge/50 §4)
1. **0–1,5 Mục lục**: tick **10 mục BTC** (knowledge/33 §1) có/không, rồi tick 14 nhóm nghiệp vụ N1–N14 (knowledge/20 §2 tên nhóm). Mục hoặc nhóm vắng = ứng viên hạng A (#1). Mục 4 vắng ⇒ #22; mục 2 hoặc 8 vắng cột/dòng Guest ⇒ #23; mục 2/3 không nói trạng thái nút ⇒ #24.
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
7. **Chấm phạm vi + gói bằng chứng** từng ứng viên theo rubric knowledge/50 §6. Điểm 3 loại ngay. Điểm 2 hạ xuống 1 khi có bằng chứng **mức 1** (lời AI Khách hàng nguyên văn) hoặc **mức 2** (câu brief tường minh); hạ xuống 1,5 khi chỉ có **mức 3** (≥2 spec có luật — trích số mục cả hai). Không có mức nào ⇒ loại. Chưa hỏi NGOÀI phạm vi buổi sáng (không có câu trả lời C1) → mọi điểm +1.
   Với mỗi ứng viên còn lại, **copy sẵn trích dẫn nguyên văn của bằng chứng vào hồ sơ finding ngay tại bước này** — đây là toàn bộ hồ sơ kháng nghị nếu bị VÔ HIỆU, và 16:00 không còn thời gian đi tìm.
8. **Dry-run executor mù**: với từng ứng viên còn lại gọi Agent `subagent_type: executor`, prompt CHỈ gồm:
   ```
   Đường dẫn spec: <đường dẫn tuyệt đối spec đối thủ>
   Tình huống: <văn bản test>
   ```
   Không kèm RTM, log, đáp án chuẩn. Gọi song song. Tiêu chí duy nhất để GIỮ: TRẢ LỜI của executor khác đáp án chuẩn trong RTM về ý nghĩa (trạng thái cuối / con số / ai thắng / tiền). Phân hạng: ĐỘ PHỦ = ĐỦ mà vẫn khác ⇒ spec đối thủ viết TRÁI specs thật — hạng A+ (TRÚNG gần chắc chắn); ĐỘ PHỦ KHÔNG/MỘT PHẦN hoặc ĐA NGHĨA ⇒ hạng A. Executor trả lời trùng đáp án chuẩn (dù spec im lặng) ⇒ TRƯỢT dự kiến → loại (knowledge/50 §2-11).
8b. **Chấm điểm kỳ vọng** cho từng ứng viên còn lại theo knowledge/50 §6 luật 8: `P(TRÚNG)` từ kết quả dry-run (khác đáp án chuẩn + ĐỘ PHỦ ĐỦ ⇒ 0,8; khác + ĐỘ PHỦ KHÔNG ⇒ 0,6; trùng ⇒ 0,1), `P(VÔ HIỆU)` từ điểm phạm vi (0 ⇒ 0,05; 1 ⇒ 0,15; 1,5 ⇒ 0,30). `EV = 2·P(TRÚNG) − P(VÔ HIỆU)`. Ứng viên `EV ≤ 0` loại, ghi lý do.
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
4. Dòng kiểm: các loại # khác nhau ✓ | mọi điểm phạm vi ≤1,5 ✓ | mọi test ≤60 từ ✓ | **mọi test có gói bằng chứng phạm vi mức 1/2/3 đã trích nguyên văn** ✓ | mọi test có `EV > 0` ✓ | **≥1 test thuộc #22/#23 nếu spec đối thủ thiếu mục 4 hoặc cột Guest** ✓ | **≥1 test thuộc #16–#21** ✓ | số test nộp = k (nêu rõ nếu k < 5).
5. Danh sách loại # cần kiểm chéo lại trên spec mình (bước 9b).

## Không được
- Hai test cùng loại #; test điểm phạm vi ≥2 không có bằng chứng mức 1/2/3; test về thuế/kế toán/bảo hiểm/vận chuyển quốc tế/đổi trả sau giao.
- **Nộp cho đủ 5 slot khi ứng viên còn lại có `EV ≤ 0`** — mỗi test bừa là −1 điểm chắc chắn.
- Nộp test mà bằng chứng phạm vi chưa được trích nguyên văn vào hồ sơ; test đã nộp không sửa được và kháng nghị chỉ mở cho ca VÔ HIỆU.
- Coi "cả 3 spec đều im lặng" là bằng chứng ngoài phạm vi (đó là điểm mù chung), hoặc coi nó là bằng chứng trong phạm vi.
- Nộp test loại #20 mà không trích được câu brief nhắc nghiệp vụ đó.
- Nộp test loại #16 mà không nêu được trong hồ sơ finding *vì sao* luật của đối thủ bất khả thi (dẫn bảng knowledge/32 §2) — thiếu phần này thì không kháng nghị được nếu bị chấm TRƯỢT.
- Câu hỏi mở, câu hỏi kép, câu hỏi dùng thuật ngữ hoặc mã của đội mình.
- Đưa RTM/log/đáp án vào prompt gọi executor.
- Nộp test không có bất kỳ mức bằng chứng phạm vi nào (không kháng nghị được).
