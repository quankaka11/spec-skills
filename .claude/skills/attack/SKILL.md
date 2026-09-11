---
name: attack
description: Soi spec đối thủ theo cấu trúc 10 mục BTC trong 10 phút — cổng khả thi F rút gọn, 24 loại lỗ hổng, bảng đồng thuận chéo 3 spec, 12 ca suy biến — rồi sinh tối đa 5 test đa dạng loại kèm hồ sơ finding, gói bằng chứng phạm vi 3 mức trích nguyên văn sẵn để kháng nghị, điểm kỳ vọng EV theo công thức +2/−1, và dry-run qua agent executor mù. Dùng 13:00–14:30 ngày thi khi người dùng nói "soi spec đối thủ", "bắn spec", "tạo 5 test", "attack", "red team đội X", "đồng thuận chéo"; cũng dùng để tự bắn spec mình sau /spec-review.
argument-hint: "cheo <spec1> <spec2> <spec3> [thư-mục] | <đường-dẫn-spec-đối-thủ> <tên-đội> [thư-mục-trận]"
allowed-tools: Read, Write, Edit, Grep, Glob, Agent, Bash(date *), Bash(mkdir *)
---
# /attack — tối đa 5 test cho một spec đối thủ

Mục tiêu: mỗi test một loại lỗ hổng khác nhau, nhắm vào chỗ specs thật phản trực giác mà spec đối thủ im lặng, **mỗi test có điểm kỳ vọng dương và một gói bằng chứng phạm vi đã trích nguyên văn**, đã kiểm bằng executor mù.

**Ba tham số 09/09 đổi cách chấm (knowledge/00 §A, §D1):**
- **CÔNG TRÚNG +2 · THỦ đỡ được +1 · CÔNG bị VÔ HIỆU −1 · bỏ slot 0** ⇒ chỉ nộp khi `EV = 2·P(TRÚNG) − P(VÔ HIỆU) > 0`. **Nộp 4 test tốt hơn nộp 5 test trong đó 1 test đoán bừa.**
- **Test không sửa được sau khi nộp** ⇒ không có "dự phòng thay slot" sau giờ nộp; 2 test dự phòng chỉ để đổi *trước* khi nộp.
- **Kháng nghị chỉ mở cho ca test CÔNG của mình bị chấm VÔ HIỆU** ⇒ gói bằng chứng phạm vi (knowledge/50 §2-9, ba mức) phải trích nguyên văn xong **lúc 14:30**, không phải lúc 16:00.

## Input
`$ARGUMENTS` = `cheo <spec1> <spec2> <spec3> [thư-mục]` (chế độ đồng thuận chéo, **chạy trước tiên lúc 13:00**) hoặc `<spec-đối-thủ> <tên-đội> [thư-mục-trận]` (chế độ soi một spec).

Thư mục trận mặc định `battle/` — lấy `rtm.md` (dòng `A-xx` ⚠ = băng đạn chắc nhất, có bằng chứng mức 1; dòng `G-xx` = giả định của đội, **không** phải bằng chứng phạm vi; mục NGOÀI phạm vi = rào chống VÔ HIỆU), `log-khach-hang.md` (5 câu trả lời nguyên văn + timestamp), `brief.md` (bằng chứng phạm vi mức 2), `dong-thuan-cheo.md` (bằng chứng mức 3, do chế độ `cheo` sinh ra), `review.md` mục `RỦI RO ĐÃ BIẾT` (khi tự bắn spec mình).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` §1 (**10 mục BTC** — dùng để tick mục vắng), §2 (5 gạch bắt buộc của bảng Case ở mục 6), §7.2 (Mermaid — **sơ đồ không thay được bảng state × event**).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md`: §1 (**24 loại**; #16–#18 từ cấu trúc BTC, #19–#24 từ cổng khả thi), §2 (quy tắc viết tình huống + **§2-9 ba mức bằng chứng phạm vi** + §2-10 bỏ slot), §3 (phân bổ 5 test + trọng số nguồn đạn đã đổi), §4 (quy trình 10 phút theo 10 mục + cổng F rút gọn), **§4b (bảng đồng thuận chéo 3 spec)**, §5 (probe **P1–P58**), §6 (rubric phạm vi 0–3 + **luật 7 điểm kỳ vọng EV** + **luật 8 "Không có quy định riêng."**), §7 (hồ sơ finding + kháng nghị chỉ ca VÔ HIỆU).
- `${CLAUDE_PROJECT_DIR}/knowledge/33-kha-thi-van-hanh.md` §1 (cổng F — 8 kiểm tra + khối grep chạy nhanh, **chạy trên spec đối thủ**), §2 (bảng thực tế phụ thuộc ngoài — dùng để chứng minh luật đối thủ bất khả thi).
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §M6 (**12 kịch bản suy biến** — checklist đối chiếu spec đối thủ), §M5 (6 kẻ lạm dụng).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 (regex danh sách đen 16 nhóm từ ngữ + **6 nhóm nội dung 17–22**), §3 (S22–S30 kiểm cấu trúc 10 mục), §4 (chuyển hit → tình huống).
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md` §5 (ô trống/overlap → tình huống).
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §6 (edge case ⚠ để đối chiếu nhanh).
- `<thư-mục-trận>/mo-hinh-bai-toan.md` — M1 mục tiêu (soi #20 trên spec đối thủ), M4 láng giềng, M6 12 ca.

## Quy tắc cứng
1. **Chế độ `cheo` chạy MỘT LẦN lúc 13:00–13:15, trước khi soi bất kỳ spec nào.** Nó sinh nguồn ứng viên lớn nhất cho cả ba spec và không tốn câu hỏi nào.
2. Mọi test phải có `EV > 0`. `EV ≤ 0` ⇒ **bỏ slot**; nộp `k < 5` test là hợp lệ và tốt hơn bắn bừa.
3. Mọi test phải có gói bằng chứng phạm vi mức 1/2/3 **đã trích nguyên văn vào hồ sơ finding** trước 14:30.
4. `"Không có quy định riêng."` trong log **không** phải bằng chứng phạm vi (knowledge/50 §6-8) — nó chỉ nói specs thật không quy định, không nói nghiệp vụ thuộc tính năng. Dòng `G-xx` cũng không phải.
5. **"Cả 3 spec đều im lặng" KHÔNG phải bằng chứng ngoài phạm vi** (đó là điểm mù chung), và cũng không phải bằng chứng trong phạm vi. Muốn bắn ô đó thì phải có bằng chứng mức 1 hoặc 2.
6. Test loại **#23** (phạm vi NGOÀI rộng hơn brief) chỉ nộp khi **trích được câu brief** nhắc nghiệp vụ đó.
7. Test loại **#19** (luật bất khả thi) phải nêu trong hồ sơ finding **vì sao** luật của đối thủ bất khả thi, dẫn dòng tương ứng của bảng knowledge/33 §2.
8. Một test = một câu hỏi, ≤60 từ, 4 dữ kiện (thời điểm tuyệt đối, loại khách, số lượng, trạng thái hold hiện tại), kết thúc bằng ép con số / trạng thái cuối / ai thắng / có hoàn tiền không. **Không nêu giá trị dẫn xuất** (`expires_at`, `deposit_due`, `ATP`, mức cọc, số tiền hoàn) — chỉ nêu dữ kiện gốc, để Executor tự tính.
9. Không đưa RTM / log / đáp án chuẩn vào prompt gọi agent `executor`.

## Chế độ `cheo` (13:00–13:15, chạy MỘT LẦN)
1. Đọc cả ba spec đối thủ (markdown, tải về được — knowledge/00 §A). Lập danh sách ~25 nghiệp vụ từ 20 nhóm N0–N19 (knowledge/20 §2) + 12 ca suy biến (knowledge/05 §M6). **Lấy đủ các dòng N15–N19** (item màn hình, event, message lỗi, API, ca bất thường) — bốn vùng cấu trúc BTC mới, khả năng có đội bỏ trống rất cao.
2. Điền bảng knowledge/50 §4b bằng grep, không đọc tuần tự: mỗi dòng một lệnh `grep -n -i -E "<3 từ khóa>" A.md B.md C.md` + `spec.nop.md` của đội mình.
3. Xuất `<thư-mục>/dong-thuan-cheo.md`: bảng đầy đủ + ba danh sách ứng viên xếp theo độ mạnh giảm dần —
   - (a) **hai spec chỏi nhau** về cùng một nghiệp vụ ⇒ ít nhất một đội trái specs thật; một tình huống bắn được cả hai;
   - (b) **một spec im lặng, ≥2 spec có luật** ⇒ **#24**, bằng chứng phạm vi mức 3 có sẵn (trích số mục của cả hai spec kia);
   - (c) **cả ba im lặng** ⇒ điểm mù chung; chỉ bắn khi có bằng chứng mức 1 hoặc 2 (quy tắc cứng 5).
4. In thêm cột **"Spec mình"**: dòng nào ta im lặng mà ≥2 đội có luật = chỗ ta gần chắc bị bắn. Spec khóa 12:00 nên không sửa được — ghi vào bài học và vào khối "cần kiểm chéo".

## Bước — chế độ soi một spec (mốc phút theo knowledge/50 §4)
1. **0–1,5 Mục lục**: tick **10 mục BTC** (knowledge/32 §1) có / thiếu / rỗng (có heading mà không có bảng = rỗng = coi như thiếu), rồi tick 20 nhóm nghiệp vụ N0–N19 (knowledge/20 §2). Mục hoặc nhóm vắng = ứng viên hạng A (#1). **Mục 4 vắng ⇒ #16; mục 2 hoặc 8 vắng cột/dòng Guest ⇒ #17; mục 2/3 không nói trạng thái nút ⇒ #18.**
2. **1,5–3 Cổng F rút gọn — 5 lệnh grep** (knowledge/33 §1 cuối mục; `B.md` = spec đối thủ), dán được nguyên khối:
   ```
   grep -n -iE "hoàn tất|tiền về|đối soát xong|xong trong|hoàn tiền trong" B.md     # → #19
   grep -n -iE "theo email|theo số điện thoại|tự khai|khai lúc|mỗi khách" B.md      # → #21
   grep -n -iE "cổng|ERP|WMS|callback|webhook|job|cron|đồng bộ|thông báo" B.md       # → #22
   grep -n -iE "guest|chưa đăng nhập|hoàn 100|không phí|miễn phí" B.md              # → #20
   grep -n '```mermaid' B.md ; grep -c "^|" B.md                                     # → chỉ có sơ đồ, không có bảng = #6
   ```
   Đọc kết quả:
   - **#19 luật bất khả thi** — mốc *hoàn tất* cho việc do cổng/ngân hàng/ERP thực hiện. Specs thật **không thể** viết vậy (knowledge/33 §2: hoàn tiền thẻ không hoàn tất trong giờ, và hoàn về thẻ đã đóng **có thể thất bại**) ⇒ mọi ca hoàn tiền đều lệch ⇒ probe P49/P50.
   - **#21 hạn mức không cưỡng chế được** — hạn mức neo vào email / SĐT chưa xác thực / tên tự khai ⇒ probe P53.
   - **#22 thiếu luật cho thất bại của phụ thuộc ngoài** — mỗi hit cổng/ERP/job/thông báo **không có nhánh lỗi nào** đi kèm. **Loại hở nhiều nhất**: hầu hết spec nhắc cổng thanh toán nhưng chỉ viết nhánh thành công ⇒ probe P54–P57.
   - **#20 luật tự đánh bại mục tiêu** — đọc mục 6 luồng chính: tài nguyên bị khóa **trước** hay **sau** rào tiền/xác thực; hủy phí 0 có trần không ⇒ probe P51/P52.
   - **Chỉ có sơ đồ, không có bảng** — xem bước 5.
   Mỗi hit ở bước này là ứng viên **hạng A**, và có đáp án chuẩn nếu buổi sáng đã hỏi nhóm N0.
3. **3–4 Mục 4 (validation & message)**: Grep mục validation — có chuỗi message **nguyên văn trong ngoặc kép** không? Chỉ ghi "hiển thị thông báo lỗi" / "báo lỗi phù hợp" = **#16 hạng A**, bắn P43/P43b ("hệ thống hiển thị message gì, nguyên văn?"). Thiếu cột FE/BE = ứng viên phụ.
4. **4–5 Mục 2 + 8 (guest & trạng thái UI)**: Grep `guest|chưa đăng nhập`. 0 hit = **#17 hạng A**, bắn P44/P44b. Không nói nút disable/ẩn khi nào, không nói sau thao tác màn hình hiển thị gì = **#18**, bắn P45/P45b.
5. **5–5,5 Lint**: Grep `-i -n` các nhóm 1, 3, 4, 6, 8, 11 **và 17–22** của knowledge/40 §2 trên spec đối thủ; mỗi hit trong core flow = ứng viên #3/#4 (nhóm 1–16) hoặc #19–#22 (nhóm 17–22), chuyển theo knowledge/40 §4.
6. **5,5–7 Mục 6 (bảng Case + bảng trạng thái)**: với từng logic, đếm case **bình thường / biên / lỗi** — thiếu loại nào = ứng viên; kiểm **5 gạch** của knowledge/32 §2 (số · toán tử `>`/`≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng) — **gạch nào thiếu thì bắn thẳng vào gạch đó**. Bảng trạng thái: không có → #6/#8; có → liệt kê ô trống, đếm tổ hợp (knowledge/31 §5), sự kiện vắng (admin hủy, cọc thất bại, kho lệch, SKU ngừng bán).
   **Đối thủ chỉ vẽ Mermaid `stateDiagram-v2` mà không có bảng state × event ⇒ ứng viên hạng A** (knowledge/32 §7.2): sơ đồ chỉ chứa chuyển **hợp lệ**, nên mọi cặp (trạng thái × sự kiện) không được vẽ đều là chỗ Executor phải đoán — bắn thẳng vào một cặp như vậy.
7. **7–8 Mục 7 + sáu mục hiếm**: mục 7.2 có đủ **4 ca bất thường BTC** nêu đích danh không (dữ liệu đổi giữa hiển thị và submit → P46 · gửi trùng → P34 · mở link hai lần → P47 · mail fail sau khi đã lưu → P48)? Vắng ca nào = tình huống bắn sẵn. Rồi Grep "đồng thời|cùng lúc|first-come", "múi giờ|timezone|UTC|ngày lễ", "guest|chưa đăng nhập", "rollback|hoàn nguyên|kiểm kê|lệch", "ưu tiên|ghi đè|trừ khi", "safety|tồn đệm|an toàn|oversell". 0 hit = ứng viên hạng A.
8. **8,5–9 Đối chiếu 12 ca suy biến + phạm vi NGOÀI**: mỗi ca knowledge/05 §M6 → spec đối thủ có luật bao trùm không; ca không có luật = ứng viên **#22**. Rồi đọc **danh sách NGOÀI phạm vi của họ** và so với `brief.md`: nghiệp vụ nào brief nhắc **tường minh** mà họ đẩy ra ngoài ⇒ **#23**, probe P58 — **chỉ nộp khi trích được câu brief** (quy tắc cứng 6).
9. **Knowledge boundary sweep**: lập bảng — mỗi dòng `A-xx` ⚠ trong `rtm.md` | spec đối thủ có nói không? (trích số mục / KHÔNG / nói KHÁC specs thật) | ứng viên?. Im lặng hoặc nói khác ⇒ ứng viên hạng A (#15), **bằng chứng phạm vi mức 1 có sẵn**. Dòng đối thủ đã viết đúng ⇒ vào bảng "⚠ đã bị chắn" (không bắn). Hai người: một sweep, một soi cấu trúc, song song từ phút 0.
10. **Nạp ứng viên từ `dong-thuan-cheo.md`**: mọi dòng thuộc danh sách (a) và (b) liên quan tới spec này ⇒ ứng viên **#24**, bằng chứng mức 3 có sẵn. Với 5 câu hỏi buổi sáng, danh sách ⚠ ở bước 9 chỉ 8–20 dòng, nên bước này thường là **nguồn ứng viên lớn nhất** (knowledge/50 §3, trọng số nguồn đạn đã đổi).
11. **Soạn 8–10 ứng viên** theo mẫu câu knowledge/50 §2 (quy tắc cứng 8). Gắn loại # và probe P. Đa dạng theo knowledge/50 §3.
12. **Chấm phạm vi + gói bằng chứng 3 mức** từng ứng viên theo rubric knowledge/50 §6. Điểm 3 loại ngay. Điểm 2 hạ xuống **1** khi có bằng chứng **mức 1** (lời AI Khách hàng nguyên văn + timestamp) hoặc **mức 2** (câu brief tường minh); hạ xuống **1,5** khi chỉ có **mức 3** (≥2/3 spec có luật — trích số mục cả hai). Không có mức nào ⇒ chấm theo luật 7 và chỉ nộp nếu kỳ vọng dương. Buổi sáng chưa hỏi danh sách NGOÀI phạm vi (không có câu trả lời C1) → mọi điểm **+1**.
    **TRÍCH NGUYÊN VĂN NGAY tại bước này** vào hồ sơ finding — đây là toàn bộ hồ sơ kháng nghị nếu bị VÔ HIỆU, và 16:00 không còn thời gian đi tìm. Gói phải xong **14:30**.
13. **Dry-run executor mù**: với từng ứng viên còn lại gọi Agent `subagent_type: executor`, prompt CHỈ gồm hai dòng:
    ```
    Đường dẫn spec: <đường dẫn tuyệt đối spec đối thủ>
    Tình huống: <văn bản test>
    ```
    Không kèm RTM, log, đáp án chuẩn. Gọi song song. Tiêu chí duy nhất để GIỮ: **trả lời của executor khác đáp án chuẩn về ý nghĩa** (trạng thái cuối / con số / ai thắng / tiền). Phân hạng:
    - **ĐỘ PHỦ = ĐỦ mà vẫn khác đáp án chuẩn ⇒ spec đối thủ viết TRÁI specs thật — hạng A+, TRÚNG gần chắc chắn. GIỮ, đừng loại vì thấy "spec họ có quy định".**
    - ĐỘ PHỦ KHÔNG / MỘT PHẦN, hoặc ĐA NGHĨA ⇒ hạng A.
    - Executor trả lời **trùng** đáp án chuẩn (dù spec im lặng) ⇒ TRƯỢT dự kiến → loại (knowledge/50 §2-12).
14. **Chấm điểm kỳ vọng EV** cho từng ứng viên còn lại (knowledge/50 §6 luật 7; knowledge/00 §D1):
    - `P(TRÚNG)` từ dry-run: khác đáp án chuẩn + ĐỘ PHỦ ĐỦ ⇒ **0,8** · khác + ĐỘ PHỦ KHÔNG ⇒ **0,6** · trùng đáp án chuẩn ⇒ **0,1**.
    - `P(VÔ HIỆU)` từ điểm phạm vi: 0 ⇒ **0,05** · 1 ⇒ **0,15** · 1,5 ⇒ **0,30** · 2 không bằng chứng ⇒ **0,50** (không nộp).
    - `EV = 2·P(TRÚNG) − P(VÔ HIỆU)`. **`EV ≤ 0` ⇒ loại, ghi lý do.**
15. **Chọn tối đa 5 + 2 dự phòng** theo phân bổ knowledge/50 §3: các loại # khác nhau, phủ ≥4 nhóm N, tối đa 2 test cùng chủ đề. Ưu tiên: #15 → **#19/#21/#22** (cổng F, hầu như không đội nào chạy) → **#16/#17** (message nguyên văn, guest — tỷ lệ TRÚNG rất cao) → **#24** (đồng thuận chéo, rẻ nhất) → #2/#12 → #4/#11/#7/#8/**#20** → **#18**. Slot #9/#10 (actor, quyền) xuống dự phòng.
    **Nếu số ứng viên `EV > 0` ít hơn 5 thì nộp ít hơn 5.** Bỏ slot = 0 điểm; nộp một test đoán bừa = −1 và **không cứu được bằng kháng nghị** (kháng nghị chỉ tranh *phạm vi*, không tranh chất lượng test). Ghi rõ trong output: "nộp k/5, bỏ 5−k slot vì EV ≤ 0".
16. **Kiểm chéo với spec mình**: mỗi loại # vừa bắn được đối thủ, đánh dấu để `/spec-review` kiểm lại chính spec mình cùng loại đó (khối G — cổng khả thi knowledge/33). Lỗ hổng tìm thấy ở đối thủ thường cũng có ở ta: **cả hai đội đọc cùng một brief**. Cộng thêm các dòng cột "Spec mình" của `dong-thuan-cheo.md`.
17. **Hồ sơ finding** cho mỗi test theo knowledge/50 §7: ID `T-<đội><n>`, loại #/probe, tình huống nộp nguyên văn, chỗ spec im lặng/mâu thuẫn (trích số mục hoặc "không mục nào đề cập" + 3 từ khóa đã grep), đáp án chuẩn kỳ vọng + **nguồn** (`log C<n> hh:mm` / `brief câu …` / `mặc định ngành 10 §6`), dự đoán Executor (từ dry-run), **gói bằng chứng phạm vi 3 mức đã trích nguyên văn**, **điểm phạm vi & kỳ vọng** (`P(TRÚNG)`, `P(VÔ HIỆU)`, `EV`), ô "Kết quả máy" và "Đánh giá" để trống.

## Output bắt buộc
Chế độ `cheo`: `<thư-mục>/dong-thuan-cheo.md` — bảng đối chiếu ~25 nghiệp vụ × 3 spec đối thủ + cột "Spec mình", ba danh sách ứng viên (a)/(b)/(c), và danh sách dòng ta im lặng mà ≥2 đội có luật.

Chế độ soi một spec: `tests/<tên-đội>.md` (trong thư mục trận; `mkdir` nếu cần), gồm:
1. Khối **"TEST NỘP (k ≤ 5)"** — chỉ nguyên văn các tình huống được chọn, đánh số, copy được. Nếu `k < 5`, ghi ngay dưới khối: "bỏ `5−k` slot vì EV ≤ 0" kèm ứng viên bị loại và lý do.
2. Hồ sơ finding từng test đã chọn + 2 dự phòng (đánh dấu DP) — **mỗi hồ sơ có gói bằng chứng phạm vi đã trích nguyên văn** và dòng `P(TRÚNG) / P(VÔ HIỆU) / EV`.
3. Bảng **"10 mục BTC của spec đối thủ: có / thiếu / rỗng"** (bước 1).
4. Bảng knowledge boundary sweep (bước 9), gồm mục "⚠ đã bị chắn".
5. Bảng **cổng F trên spec đối thủ** (bước 2 + bước 8): hit nào ở #19/#20/#21/#22, ca suy biến nào trong 12 ca hở, nghiệp vụ nào brief nhắc mà họ đẩy ra NGOÀI phạm vi — kể cả ứng viên không được chọn, để tái dùng khi soi spec khác.
6. Danh sách loại # cần **kiểm chéo lại trên spec mình** (bước 16).
7. **Dòng kiểm**: các loại # khác nhau ✓ | mọi điểm phạm vi ≤1,5 ✓ | mọi test ≤60 từ, một câu hỏi ✓ | **mọi test `EV > 0`** ✓ | **≥1 test thuộc #19–#24** ✓ | **≥1 test thuộc #16–#18** ✓ | mọi test có gói bằng chứng phạm vi mức 1/2/3 đã trích nguyên văn ✓ | **số test nộp = k** (nêu rõ nếu `k < 5`).

## Không được
- **Nộp cho đủ 5 slot khi ứng viên còn lại có `EV ≤ 0`** — mỗi test bừa là −1 điểm gần như chắc chắn, và bỏ slot chỉ là 0.
- Nộp test mà gói bằng chứng phạm vi chưa trích nguyên văn vào hồ sơ; test đã nộp không sửa được và kháng nghị chỉ mở cho ca VÔ HIỆU.
- Hai test cùng loại #; test điểm phạm vi ≥2 không có bằng chứng mức 1/2/3; test điểm phạm vi 3 (thuế, kế toán, hóa đơn, bảo hiểm, vận chuyển quốc tế, đổi trả sau giao, chống gian lận, bảo vệ dữ liệu, KPI).
- Coi **"cả 3 spec đều im lặng"** là bằng chứng ngoài phạm vi (đó là điểm mù chung), hoặc coi nó là bằng chứng trong phạm vi.
- Dùng `"Không có quy định riêng."` hoặc dòng `G-xx` làm bằng chứng phạm vi.
- Nộp test **#23** mà không trích được câu brief nhắc nghiệp vụ đó.
- Nộp test **#19** mà không nêu trong hồ sơ finding *vì sao* luật của đối thủ bất khả thi (dẫn dòng bảng knowledge/33 §2) — thiếu phần này thì lập luận rỗng nếu bị chấm TRƯỢT.
- Loại ứng viên chỉ vì executor báo **ĐỘ PHỦ = ĐỦ**: ĐỦ mà vẫn khác đáp án chuẩn là dấu hiệu mạnh nhất rằng spec đối thủ viết trái specs thật.
- Nêu giá trị dẫn xuất trong tình huống; khẳng định số liệu của spec đối thủ; câu hỏi mở, câu hỏi kép, câu hỏi dùng thuật ngữ hoặc mã BR của đội mình.
- Đưa RTM / log / đáp án chuẩn vào prompt gọi agent `executor`.
- Bỏ chế độ `cheo` rồi soi từng spec — mất nguồn ứng viên lớn nhất và mất bằng chứng phạm vi mức 3.
