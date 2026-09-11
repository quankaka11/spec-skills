---
name: attack
description: Soi một spec đối thủ trong 10 phút theo cấu trúc 10 mục BTC và sinh 5 test đa dạng loại lỗ hổng (+2 dự phòng) kèm hồ sơ finding, đã chấm rủi ro VÔ HIỆU và dry-run qua agent executor mù. Dùng 13:00–14:30 ngày thi khi người dùng nói "soi spec đối thủ", "bắn spec", "tạo 5 test", "attack", "red team đội X"; cũng dùng để tự bắn spec mình sau /spec-review.
argument-hint: "<đường-dẫn-spec-đối-thủ> <tên-đội> [thư-mục-trận]"
allowed-tools: Read, Write, Edit, Grep, Glob, Agent, Bash(date *), Bash(mkdir *)
---
# /attack — 5 test cho một spec đối thủ

Mục tiêu: 5 test nộp đi, mỗi test một loại lỗ hổng khác nhau, nhắm vào chỗ specs thật phản trực giác mà spec đối thủ im lặng, rủi ro VÔ HIỆU ≤1, đã kiểm bằng executor mù.

## Input
`$ARGUMENTS` = `<spec-đối-thủ> <tên-đội> [thư-mục-trận]`. Thư mục trận mặc định `battle/` — lấy `rtm.md` (dòng ⚠ = băng đạn; mục NGOÀI phạm vi = rào chống VÔ HIỆU) và `log-khach-hang.md` (đáp án chuẩn nguyên văn + timestamp).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` §1 (10 mục BTC — dùng để tick mục vắng), §2 (5 gạch bắt buộc của bảng Case ở mục 6), §7 (Mermaid — sơ đồ không thay bảng).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md`: §1 (18 loại; #16 message, #17 guest, #18 trạng thái UI là loại mới, tỷ lệ TRÚNG rất cao), §2 (quy tắc viết tình huống + mẫu câu), §3 (phân bổ 5 test), §4 (quy trình 10 phút theo 10 mục + knowledge boundary sweep), §5 (bảng probe P1–P48), §6 (rubric phạm vi 0–3), §7 (hồ sơ finding).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 (regex danh sách đen), §3 (S22–S30 kiểm cấu trúc 10 mục), §4 (chuyển hit → tình huống).
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md` §5 (ô trống/overlap → tình huống).
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §6 (edge case ⚠ để đối chiếu nhanh).

## Bước (mốc phút theo knowledge/50 §4)
1. **0–2 Mục lục**: tick **10 mục BTC** (knowledge/32 §1) có/không, rồi tick 14 nhóm nghiệp vụ N1–N14 (knowledge/20 §2 tên nhóm). Mục hoặc nhóm vắng = ứng viên hạng A (#1). Mục 4 vắng ⇒ #16; mục 2 hoặc 8 vắng cột Guest ⇒ #17; mục 2/3 không nói trạng thái nút ⇒ #18.
2. **2–3 Mục 4 (message)**: Grep mục validation — có chuỗi message trong ngoặc kép không? Chỉ ghi "hiển thị thông báo lỗi / báo lỗi phù hợp" = **#16 hạng A**, bắn P43/P43b ("hệ thống hiển thị message gì, nguyên văn?"). Thiếu cột FE/BE = ứng viên phụ.
3. **3–4 Mục 2 + 8 (guest & trạng thái UI)**: Grep `guest|chưa đăng nhập`. 0 hit = **#17 hạng A**, bắn P44/P44b. Không nói nút disable/ẩn khi nào, không nói sau thao tác hiển thị gì = **#18**, bắn P45/P45b.
4. **4–5 Lint**: Grep `-i -n` các nhóm 1, 3, 4, 6, 8, 11 của knowledge/40 §2 trên spec đối thủ; mỗi hit trong core flow = ứng viên #3/#4 (chuyển theo knowledge/40 §4).
5. **5–7 Mục 6 (bảng Case + trạng thái)**: với từng logic, đếm case bình thường / biên / lỗi — thiếu loại nào = ứng viên; kiểm **5 gạch** (số · toán tử `>`/`≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng) — gạch nào thiếu thì bắn thẳng vào gạch đó. Bảng trạng thái: không có → #6/#8; có → liệt kê ô trống, đếm tổ hợp (knowledge/31 §5), sự kiện vắng (admin hủy, cọc thất bại, kho lệch, SKU ngừng bán). **Đối thủ chỉ vẽ sơ đồ Mermaid mà không có bảng state × event ⇒ ứng viên hạng A**: sơ đồ chỉ chứa chuyển hợp lệ, nên mọi cặp (trạng thái × sự kiện) không được vẽ đều là chỗ Executor phải đoán — bắn thẳng vào một cặp như vậy.
6. **7–8 Mục 7 + sáu mục hiếm**: mục 7.2 có đủ 4 ca BTC nêu đích danh không (dữ liệu đổi giữa hiển thị và submit → P46 · gửi trùng → P34 · mở link hai lần → P47 · mail fail sau khi đã lưu → P48)? Vắng ca nào = ứng viên sẵn. Rồi Grep "đồng thời|cùng lúc|first-come", "múi giờ|timezone|UTC|ngày lễ", "guest|chưa đăng nhập", "rollback|hoàn nguyên|kiểm kê|lệch", "ưu tiên|ghi đè|trừ khi", "safety|tồn đệm|an toàn|oversell". 0 hit = ứng viên hạng A.
7. **Knowledge boundary sweep**: lập bảng — mỗi dòng ⚠ trong `rtm.md` | spec đối thủ có nói không? (trích mục / KHÔNG / nói KHÁC specs thật) | ứng viên?. Im lặng hoặc nói khác ⇒ ứng viên hạng A (#15) — đáp án chuẩn đã có sẵn trong log. Dòng đối thủ đã viết đúng ⇒ vào bảng "⚠ đã bị chắn" (không bắn).
8. **Soạn 8–10 ứng viên** theo mẫu câu knowledge/50 §2 (thời điểm tuyệt đối, loại khách, số lượng, trạng thái hiện tại; kết thúc bằng một câu hỏi ép con số/trạng thái/ai thắng/hoàn tiền; ≤60 từ; không mã BR, không thuật ngữ riêng của đội mình). Gắn loại # và probe P. Đa dạng theo knowledge/50 §3.
9. **Chấm phạm vi** từng ứng viên theo rubric knowledge/50 §6 (0–3). Điểm 3 loại ngay. Điểm 2 chỉ giữ nếu log có câu trả lời AI Khách hàng về đúng nghiệp vụ đó (trích + timestamp) → hạ xuống 1. Chưa hỏi NGOÀI phạm vi buổi sáng → mọi điểm +1.
10. **Dry-run executor mù**: với từng ứng viên còn lại gọi Agent `subagent_type: executor`, prompt CHỈ gồm:
   ```
   Đường dẫn spec: <đường dẫn tuyệt đối spec đối thủ>
   Tình huống: <văn bản test>
   ```
   Không kèm RTM, log, đáp án chuẩn. Gọi song song. Tiêu chí duy nhất để GIỮ: TRẢ LỜI của executor khác đáp án chuẩn trong RTM về ý nghĩa (trạng thái cuối / con số / ai thắng / tiền). Phân hạng: ĐỘ PHỦ = ĐỦ mà vẫn khác ⇒ spec đối thủ viết TRÁI specs thật — hạng A+ (TRÚNG gần chắc chắn); ĐỘ PHỦ KHÔNG/MỘT PHẦN hoặc ĐA NGHĨA ⇒ hạng A. Executor trả lời trùng đáp án chuẩn (dù spec im lặng) ⇒ TRƯỢT dự kiến → loại (knowledge/50 §2-11).
11. **Chọn 5 + 2 dự phòng** theo phân bổ knowledge/50 §3: 5 loại # khác nhau, phủ ≥4 nhóm N, tối đa 2 test cùng chủ đề. Ưu tiên: #15 → **#16/#17** (message nguyên văn, guest — spec viết theo lối luật nghiệp vụ thuần gần như chắc chắn hở) → #2/#12 → #4/#11 → **#18** → #7/#8 → #9/#10. Dự phòng thay slot yếu nhất.
12. **Hồ sơ finding** cho mỗi test theo knowledge/50 §7: ID `T-<đội><n>`, loại #/probe, tình huống nộp nguyên văn, chỗ spec im lặng/mâu thuẫn (trích số mục hoặc "không mục nào đề cập" + 3 từ khóa đã Ctrl+F), đáp án chuẩn kỳ vọng (trích log nguyên văn + timestamp + ID RTM), dự đoán Executor (từ dry-run), điểm phạm vi, ô "Kết quả máy" và "Đánh giá" để trống.

## Output `tests/<tên-đội>.md` (trong thư mục trận; mkdir nếu cần)
1. Khối "5 TEST NỘP" — chỉ nguyên văn 5 tình huống, đánh số, copy được.
2. 5 hồ sơ finding + 2 dự phòng (đánh dấu DP).
3. Bảng knowledge boundary sweep (bước 7) gồm mục "⚠ đã bị chắn".
3b. Bảng "10 mục BTC của spec đối thủ: có / thiếu / rỗng" (bước 1).
4. Dòng kiểm: 5 loại # khác nhau ✓ | mọi điểm phạm vi ≤1 ✓ | mọi test ≤60 từ ✓ | mọi test có đáp án chuẩn trong log ✓.

## Không được
- Hai test cùng loại #; test nào điểm phạm vi ≥2 không có trích log; test về thuế/kế toán/bảo hiểm/vận chuyển quốc tế/đổi trả sau giao.
- Câu hỏi mở, câu hỏi kép, câu hỏi dùng thuật ngữ hoặc mã của đội mình.
- Đưa RTM/log/đáp án vào prompt gọi executor.
- Nộp test không có đáp án chuẩn nguyên văn trong log (không kháng nghị được).
