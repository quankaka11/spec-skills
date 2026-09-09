---
name: attack
description: Soi một spec đối thủ trong 10 phút — cổng khả thi F, 20 loại lỗ hổng, 12 kịch bản suy biến, phạm vi tự đặt — rồi sinh 5 test đa dạng loại (+2 dự phòng) kèm hồ sơ finding, đã chấm rủi ro VÔ HIỆU và dry-run qua agent executor mù. Dùng 13:00–14:30 ngày thi khi người dùng nói "soi spec đối thủ", "bắn spec", "tạo 5 test", "attack", "red team đội X"; cũng dùng để tự bắn spec mình sau /spec-review.
argument-hint: "<đường-dẫn-spec-đối-thủ> <tên-đội> [thư-mục-trận]"
allowed-tools: Read, Write, Edit, Grep, Glob, Agent, Bash(date *), Bash(mkdir *)
---
# /attack — 5 test cho một spec đối thủ

Mục tiêu: 5 test nộp đi, mỗi test một loại lỗ hổng khác nhau, nhắm vào chỗ specs thật phản trực giác mà spec đối thủ im lặng, rủi ro VÔ HIỆU ≤1, đã kiểm bằng executor mù.

## Input
`$ARGUMENTS` = `<spec-đối-thủ> <tên-đội> [thư-mục-trận]`. Thư mục trận mặc định `battle/` — lấy `rtm.md` (dòng ⚠ = băng đạn; mục NGOÀI phạm vi = rào chống VÔ HIỆU) và `log-khach-hang.md` (đáp án chuẩn nguyên văn + timestamp).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md`: §1 (**20 loại**, gồm #16–#20 khả thi / tự đánh bại mục tiêu / hạn mức không cưỡng chế / thiếu luật lỗi phụ thuộc ngoài / phạm vi tự đặt), §2 (quy tắc viết tình huống + mẫu câu), §3 (phân bổ 5 test), §4 (quy trình 10 phút + cổng F rút gọn + knowledge boundary sweep), §5 (bảng probe **P1–P52**), §6 (rubric phạm vi 0–3), §7 (hồ sơ finding).
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md` §1 (6 lệnh grep cổng F — chạy trên spec đối thủ), §2 (bảng thực tế phụ thuộc ngoài — dùng để chứng minh luật đối thủ bất khả thi).
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §M6 (12 kịch bản suy biến — checklist đối chiếu spec đối thủ), §M5 (6 kẻ lạm dụng).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 (regex danh sách đen **22 nhóm**), §4 (chuyển hit → tình huống).
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md` §5 (ô trống/overlap → tình huống).
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §6 (edge case ⚠ để đối chiếu nhanh).
- `<thư-mục-trận>/mo-hinh-bai-toan.md` — M1 mục tiêu (để soi #17 trên spec đối thủ), M4 láng giềng, M6 12 ca.

## Bước (mốc phút theo knowledge/50 §4)
1. **0–2 Heading**: đọc mục lục spec đối thủ, tick 14 nhóm N1–N14 có/không (knowledge/20 §2 tên nhóm). Nhóm vắng = ứng viên hạng A (#1).
1b. **2–3 Cổng F rút gọn** — chạy 4 lệnh grep của knowledge/32 §1 trên spec đối thủ:
   - `hoàn tất|tiền về|đối soát xong` + số giờ/ngày ⇒ **#16** (luật bất khả thi). Chứng cứ bảng knowledge/32 §2: hoàn tiền thẻ không hoàn tất trong giờ, và hoàn về thẻ đóng có thể thất bại ⇒ specs thật không viết như họ ⇒ probe P43/P44.
   - hạn mức neo vào `email|số điện thoại|tự khai` ⇒ **#18** ⇒ probe P47.
   - nhắc `cổng|ERP|job|thông báo|callback` mà **không có nhánh lỗi nào** ⇒ **#19** ⇒ probe P48–P51. Đây là loại hở nhiều nhất: hầu hết spec nhắc cổng thanh toán nhưng chỉ viết nhánh thành công.
   - `guest|hoàn 100|không phí|miễn phí` + đọc §7 luồng chính xem tài nguyên bị khóa trước hay sau rào ⇒ **#17** ⇒ probe P45/P46.
   Mỗi hit ở bước này là ứng viên **hạng A** và có đáp án chuẩn nếu buổi sáng đã hỏi nhóm N0.
2. **3–4 Lint**: Grep `-i -n` các nhóm 1, 3, 4, 6, 8, 11 **và 17–21** của knowledge/40 §2 trên spec đối thủ; mỗi hit trong core flow = ứng viên #3/#4/#16–#19 (chuyển theo knowledge/40 §4).
3. **4–5,5 Bảng**: tìm bảng trạng thái / decision table. Không có → #6/#8. Có → liệt kê ô trống, đếm tổ hợp (knowledge/31 §5), sự kiện vắng (admin hủy, cọc thất bại, kho lệch, SKU ngừng bán).
4. **5,5–7 Sáu mục hiếm**: Grep "đồng thời|cùng lúc|first-come", "múi giờ|timezone|UTC|ngày lễ", "guest|chưa đăng nhập", "rollback|hoàn nguyên|kiểm kê|lệch", "ưu tiên|ghi đè|trừ khi", "safety|tồn đệm|an toàn|oversell". 0 hit = ứng viên hạng A.
4b. **7–8 Đối chiếu 12 ca suy biến + phạm vi**: mỗi ca knowledge/05 §M6 → spec đối thủ có luật bao trùm không (ứng viên #19). Rồi đọc danh sách NGOÀI phạm vi của họ, so với brief: nghiệp vụ nào brief nhắc tường minh mà họ đẩy ra ngoài ⇒ **#20**, probe P52 — **phải trích được câu brief** mới nộp.
5. **Knowledge boundary sweep**: lập bảng — mỗi dòng ⚠ trong `rtm.md` | spec đối thủ có nói không? (trích mục / KHÔNG / nói KHÁC specs thật) | ứng viên?. Im lặng hoặc nói khác ⇒ ứng viên hạng A (#15) — đáp án chuẩn đã có sẵn trong log. Dòng đối thủ đã viết đúng ⇒ vào bảng "⚠ đã bị chắn" (không bắn).
6. **Soạn 8–10 ứng viên** theo mẫu câu knowledge/50 §2 (thời điểm tuyệt đối, loại khách, số lượng, trạng thái hiện tại; kết thúc bằng một câu hỏi ép con số/trạng thái/ai thắng/hoàn tiền; ≤60 từ; không mã BR, không thuật ngữ riêng của đội mình). Gắn loại # và probe P. Đa dạng theo knowledge/50 §3.
7. **Chấm phạm vi** từng ứng viên theo rubric knowledge/50 §6 (0–3). Điểm 3 loại ngay. Điểm 2 chỉ giữ nếu log có câu trả lời AI Khách hàng về đúng nghiệp vụ đó (trích + timestamp) → hạ xuống 1. Chưa hỏi NGOÀI phạm vi buổi sáng → mọi điểm +1.
8. **Dry-run executor mù**: với từng ứng viên còn lại gọi Agent `subagent_type: executor`, prompt CHỈ gồm:
   ```
   Đường dẫn spec: <đường dẫn tuyệt đối spec đối thủ>
   Tình huống: <văn bản test>
   ```
   Không kèm RTM, log, đáp án chuẩn. Gọi song song. Tiêu chí duy nhất để GIỮ: TRẢ LỜI của executor khác đáp án chuẩn trong RTM về ý nghĩa (trạng thái cuối / con số / ai thắng / tiền). Phân hạng: ĐỘ PHỦ = ĐỦ mà vẫn khác ⇒ spec đối thủ viết TRÁI specs thật — hạng A+ (TRÚNG gần chắc chắn); ĐỘ PHỦ KHÔNG/MỘT PHẦN hoặc ĐA NGHĨA ⇒ hạng A. Executor trả lời trùng đáp án chuẩn (dù spec im lặng) ⇒ TRƯỢT dự kiến → loại (knowledge/50 §2-11).
9. **Chọn 5 + 2 dự phòng** theo phân bổ knowledge/50 §3: 5 loại # khác nhau, phủ ≥4 nhóm N, tối đa 2 test cùng chủ đề. Ưu tiên: #15 → **#16/#18/#19** → #2/#12 → #4/#11 → #7/#8/#17. Slot #9/#10 (actor, quyền) xuống dự phòng — hầu hết đội có bảng actor, còn cổng F thì hầu như không đội nào chạy. Dự phòng thay slot yếu nhất.
9b. **Kiểm chéo với spec mình**: mỗi loại # vừa bắn được đối thủ, đánh dấu để `/spec-review` khối G kiểm lại chính spec mình cùng loại đó. Lỗ hổng tìm thấy ở đối thủ thường cũng có ở ta — cả hai đội đọc cùng một brief.
10. **Hồ sơ finding** cho mỗi test theo knowledge/50 §7: ID `T-<đội><n>`, loại #/probe, tình huống nộp nguyên văn, chỗ spec im lặng/mâu thuẫn (trích số mục hoặc "không mục nào đề cập" + 3 từ khóa đã Ctrl+F), đáp án chuẩn kỳ vọng (trích log nguyên văn + timestamp + ID RTM), dự đoán Executor (từ dry-run), điểm phạm vi, ô "Kết quả máy" và "Đánh giá" để trống.

## Output `tests/<tên-đội>.md` (trong thư mục trận; mkdir nếu cần)
1. Khối "5 TEST NỘP" — chỉ nguyên văn 5 tình huống, đánh số, copy được.
2. 5 hồ sơ finding + 2 dự phòng (đánh dấu DP).
3. Bảng knowledge boundary sweep (bước 5) gồm mục "⚠ đã bị chắn".
3b. **Bảng cổng F trên spec đối thủ** (bước 1b + 4b): hit nào ở F1/F2/F5/F8, ca suy biến nào hở, nghiệp vụ nào brief nhắc mà họ đẩy ra ngoài — kể cả ứng viên không được chọn, để tái dùng khi soi spec khác.
4. Dòng kiểm: 5 loại # khác nhau ✓ | mọi điểm phạm vi ≤1 ✓ | mọi test ≤60 từ ✓ | mọi test có đáp án chuẩn trong log ✓ | **≥1 test thuộc #16–#20** ✓.
5. Danh sách loại # cần kiểm chéo lại trên spec mình (bước 9b).

## Không được
- Hai test cùng loại #; test nào điểm phạm vi ≥2 không có trích log; test về thuế/kế toán/bảo hiểm/vận chuyển quốc tế/đổi trả sau giao.
- Nộp test loại #20 mà không trích được câu brief nhắc nghiệp vụ đó.
- Nộp test loại #16 mà không nêu được trong hồ sơ finding *vì sao* luật của đối thủ bất khả thi (dẫn bảng knowledge/32 §2) — thiếu phần này thì không kháng nghị được nếu bị chấm TRƯỢT.
- Câu hỏi mở, câu hỏi kép, câu hỏi dùng thuật ngữ hoặc mã của đội mình.
- Đưa RTM/log/đáp án vào prompt gọi executor.
- Nộp test không có đáp án chuẩn nguyên văn trong log (không kháng nghị được).
