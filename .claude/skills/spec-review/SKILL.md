---
name: spec-review
description: Red team spec của đội trước khi nộp — kiểm đủ 10 mục BTC, lint từ mơ hồ và nội dung, kiểm cấu trúc và ô trống bảng, kiểm phủ RTM (cả dòng đã hỏi và dòng giả định), cổng khả thi F + bảng Mục tiêu↔Luật + mô hình lạm dụng + kiểm giá trị tự nghĩ ra, eval set qua HAI agent executor mù độc lập (đo đa nghĩa bằng bất đồng), đếm token bản nộp, cổng chất lượng 25 dòng, kết luận NỘP ĐƯỢC/CHƯA kèm danh sách SỬA và HỎI (mỗi mục một lượt nhị phân, cắt theo số nhịp còn kịp gửi). Dùng 11:38–11:48 ngày thi hoặc trong diễn tập khi người dùng nói "review spec", "soi spec mình", "red team", "kiểm tra trước khi nộp", "spec-review".
argument-hint: "[đường-dẫn-spec] [n=20] [sửa]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(LC_ALL=C.UTF-8 wc *), Agent
---
# /spec-review — Red team spec của mình

Mục tiêu: tìm mọi chỗ Executor có thể trả lời khác specs thật, xếp theo mức, kèm câu viết lại dán được. Không sửa spec trừ khi args có `sửa`.

## Input
`$ARGUMENTS` = `[đường-dẫn-spec] [n] [sửa]`. Mặc định spec `battle/spec.md`, n = 20 tình huống eval. `rtm.md` cùng thư mục nếu có.

**Đọc sổ hạn mức ở dòng đầu `log-khach-hang.md` trước tiên** (`<t>/4.000 token · lượt · nhịp · đường cắt`): thứ còn lại là **token và số nhịp trước đường cắt**, không phải số câu. Ước `số lượt còn kịp = min(token còn lại ÷ 150 ; số phút còn lại của pha hỏi × 60 ÷ nhịp)`; danh sách `HỎI` cắt đúng con số đó, **mỗi mục một lượt một ý, ưu tiên dạng nhị phân**. Còn 0 lượt ⇒ **không có danh sách `HỎI`**, mọi phát hiện loại đó chuyển thành `RỦI RO ĐÃ BIẾT` (xem báo cáo mục 6b).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/33-cau-truc-spec-btc.md` §1 (10 mục BTC bắt buộc), §2 ("ba thứ" + 5 gạch của mỗi logic ở mục 6), §5–§6 (ngân sách token, thứ tự cắt), §7 (Mermaid — sơ đồ không thay bảng).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md`: §2 danh sách đen phẳng (**22 nhóm** — 1–16 mơ hồ, 17–22 nội dung), §3 kiểm tra cấu trúc **S1–S39** (S31–S39 = cấu trúc 10 mục), §4 định dạng báo cáo, §5 thang ưu tiên sửa.
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md`: §1 cổng F 8 kiểm tra, §2 bảng thực tế phụ thuộc ngoài, §3 mẫu viết lại, §4 chi phí ẩn, §5 cổng chất lượng dòng 12–16, §6 chuyển hit F thành câu hỏi.
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md`: §2 bảng Mục tiêu↔Luật, §M4 6 láng giềng, §M5 6 kẻ lạm dụng, §M6 12 kịch bản suy biến.
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §2.2–§2.3 (đếm tổ hợp, soi gap/overlap), §3 (mọi ô state × event), §4 (mức phủ).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (**24 loại** lỗ hổng — để sinh eval set; #22 message, #23 guest, #24 trạng thái UI là loại mới 11/09), §2 (quy tắc viết tình huống), §5 probe P1–P58.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §2 tên 14 nhóm N1–N14 (để tick heading spec MÌNH ở khối B) + §1 (cổng 8 kiểm tra — mọi mục trong danh sách `HỎI` phải qua cổng này trước khi in) + §3b (bốn vùng BTC: mục 4 message giờ hỏi được).
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md` §1 (ngân sách token, quy tắc mermaid), §1b (**luật giả định = mặc định ngành** — căn cứ của khối G-7), §7 (cổng chất lượng **25 dòng**).
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §4 (31 phát biểu mặc định ngành — dùng để chấm khối G-7), §5 (RTM hai loại dòng).

## Bước
**A. LINT từ ngữ + nội dung** — với từng nhóm 1–22 ở knowledge/40 §2: Grep `-i`, output_mode content, `-n`, pattern là regex của nhóm, path = spec. Mỗi hit là một dòng báo cáo (vị trí, trích, nhóm, mức theo §3/§5). Bỏ hit trong phần định nghĩa từ khóa hoặc trong ngoặc kép có chủ ý; nhóm 9 và 11 chỉ đánh lỗi khi câu chứa hit thiếu ký hiệu ≤/</[a,b) hoặc thiếu danh sách đánh số. **Nhóm 17–22 chạy trên bản nộp** và mỗi hit là *nghi vấn*, phán quyết thuộc khối G — không kết luận mức ngay tại đây.

**B. HOÀN CHỈNH cấu trúc** — chạy S1–S39 (knowledge/40 §3).

**B0. Đủ 10 mục BTC (S31) — chạy TRƯỚC mọi kiểm khác.** Đối chiếu mục lục spec với knowledge/33 §1. Mục nào vắng = lỗi **Cao**, ghi rõ "thiếu mục n — cả vùng trống". Mục có heading nhưng không có bảng/nội dung = coi như vắng.

Rồi bắt buộc kiểm:
1. **Mục 6 — từng logic** (trọng tâm): mỗi logic có bảng step? có bảng Case? bảng Case đủ **3 loại case** (bình thường, biên, lỗi) — thiếu loại nào = lỗi Cao S34; đủ **5 gạch** (số · toán tử `>`/`≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng) — thiếu gạch nào = lỗi Cao S26, ghi tên gạch thiếu; cột Kết quả mong đợi đủ 7 chiều (trạng thái cuối, kho, tiền, thông báo, hiển thị, trạng thái nút, message).
2. **Mục 4** (S32, S33): Grep message — mọi dòng phải có chuỗi trong ngoặc kép; câu kiểu "hiển thị thông báo lỗi/báo lỗi phù hợp" = lỗi **Cao** S32. Thiếu cột FE/BE = S33 Trung.
3. **Mục 2 + 8** (S36): Grep `guest|Guest|chưa đăng nhập` — mục 2 thiếu cột phân biệt login/guest hoặc mục 8 thiếu dòng Guest = lỗi **Cao**. Mục 8 phải có System/Job và phủ thao tác chỉ-đọc nếu có catch-all "không ✓ = cấm". Mục 2 phải có điều kiện ẩn/disable.
4. **Mục 7** (S37): 7.2 có đủ 4 ca BTC nêu đích danh (dữ liệu đổi giữa hiển thị và submit · gửi trùng · mở link hai lần · mail fail sau khi đã lưu)? thiếu ca nào = lỗi Cao, ghi tên ca. 7.4 có gom bảng riêng điều chưa chốt? `[GIẢ ĐỊNH]`/"TBD" rải rác ngoài bảng 7.4 = S28 Trung.
5. Bảng state × event (trong logic 6.3): liệt kê mọi ô, ô trống/"—" không giải thích = lỗi Cao S13/S17. **Có sơ đồ Mermaid `stateDiagram-v2` mà KHÔNG có bảng = lỗi Cao** — sơ đồ chỉ vẽ chuyển hợp lệ, thiếu hẳn ô `Từ chối 0.5` / `KHL`, đúng chỗ Executor đoán sai.
5b. **Sơ đồ Mermaid — đếm, rồi lint** (knowledge/33 §7):
   - **Đếm khối** ```` ```mermaid ````: **< 3 = lỗi Cao** — BTC yêu cầu tường minh ba sơ đồ (mục 5 wireframe · mục 6 sequence end-to-end · mục 9 sơ đồ hệ thống). Ghi rõ mục nào thiếu.
   - Logic nhiều nhánh nhất của mục 6 không có lưu đồ `flowchart TD` = lỗi Trung (knowledge/33 §7.5).
   - **Lint 10 dòng knowledge/33 §7.7** trên từng khối: nhãn flowchart chứa `(` `)` `,` `:` `#` không bọc ngoặc kép · ngoặc kép sau `:` trong `stateDiagram-v2` hoặc sau `as` trong `sequenceDiagram` (ngoặc sẽ hiện trên hình) · id node/state có dấu tiếng Việt hoặc khoảng trắng · `-->` dùng trong `sequenceDiagram` thay vì `->>` · `|` trần trong nhãn · thiếu `end` đóng `block:`/`subgraph` · `;` nối lệnh · `\n` thay `<br/>` · khối `%%{init}%%`. Mỗi hit = lỗi **Trung** (sơ đồ gãy không hỏng spec nhưng mất điểm hình thức).
   - Thiếu 1–2 câu chữ tóm tắt ngay dưới sơ đồ = lỗi Trung.
   - Có sơ đồ mà catch-all thiếu dòng 0.15 ("bảng và luật có mã thắng") = lỗi **Cao** — tự tạo mâu thuẫn nội tại, Executor có hai cách đọc hợp lệ.
   - Lưu đồ `flowchart TD` có node điều kiện **chỉ một nhánh** (nhánh cụt) = lỗi Cao: một case còn thiếu trong bảng. Thứ tự kiểm trên lưu đồ lệch với bảng Case hoặc cột FE/BE mục 4 = lỗi Cao (mâu thuẫn nội tại #5).
6. Decision table: đếm tổ hợp = tích các phân hoạch (knowledge/31 §2.2), thiếu → gap, hai hàng `-` chéo cột không hit policy → overlap #8.
7. Grep `BR-\d+|EX-\d+`: mã trùng, tham chiếu chết S10.
8. Catch-all mục 1.x: có ưu tiên xung đột (0.6), timezone (0.2), [a,b) (0.3), tình huống không khớp luật nào (0.5)?
9. Mọi BR có tác động phụ K/T/N/L và câu KHÔNG ĐƯỢC (S14, S15).
10. Grep các mục hiếm: "đồng thời|cùng lúc", "safety|tồn đệm|an toàn|oversell", "guest|chưa đăng nhập", "múi giờ|UTC|ngày lễ", "rollback|hoàn nguyên|kiểm kê|lệch", "ưu tiên|ghi đè" — 0 hit = lỗ hổng #1 ứng viên, phải có tình huống eval chạm vào ở khối D.
11. **Lint nội dung 17–22** (knowledge/40 §2): mỗi hit ghi vị trí. Nhóm 22 (câu nói về chính spec) phải bằng 0 trên bản nộp.
12. **S22–S30** (knowledge/40 §3): mục tiêu brief không có luật phục vụ (S22), luật làm hỏng mục tiêu (S23), ca suy biến không có luật (S24), nghĩa vụ tiền một mốc (S25), không có luật cho hoàn tiền thất bại (S26), coi việc giữ tài nguyên là chắc thành công (S27), rào bảo vệ sau khi khóa tài nguyên (S28), phạm vi NGOÀI rộng hơn brief (S29).

**C. PHỦ & KHỚP RTM** — tìm `rtm.md` + `log-khach-hang.md` ở thư mục spec, rồi thư mục cha, hoặc đường dẫn người dùng chỉ. Có RTM:
1. Dòng nào (`A-xx` **hoặc** `G-xx`) có cột Mã BR trống hoặc mã không tồn tại trong spec = lỗi Cao.
2. Với TỪNG dòng `A-xx`, so NỘI DUNG BR tương ứng với câu trả lời nguyên văn (con số, trạng thái, hướng tiền, ai được phép) — BR nói khác = lỗi Cao "trái specs thật" (loại TRÚNG chắc nhất, #15), ghi cả hai vế. Đây là hạng lỗi nghiêm trọng nhất trong cả báo cáo: đội đã *biết* mà viết sai.
3. Với TỪNG dòng `G-xx`, kiểm cột Rủi ro đã điền chưa; dòng rủi ro Cao mà **chưa** được lượt hỏi nào chạm tới = ghi vào `RỦI RO ĐÃ BIẾT`, mức Trung (không phải lỗi — là thông tin để buổi chiều tự bắn trước).
4. Đếm và in: `A-xx` / `G-xx` / tổng, tỷ lệ luật có nguồn từ AI Khách hàng. Tỷ lệ này thấp (25–45%) là bình thường ngay cả khi kịp 15–20 lượt; nó chỉ là cảnh báo khi **dòng `A-xx` chưa được dùng hết** — có câu trả lời mà không thành luật là lỗi Cao.
Không có RTM → ghi "bỏ khối C: không có RTM" và đánh dấu mọi luật có con số/quyền là "cần đối chiếu log".

**D. EVAL SET — hai Executor mù ĐỘC LẬP, đo bằng bất đồng**
1. Sinh n tình huống theo quy tắc knowledge/50 §2 (ép kết quả cụ thể, thời điểm tuyệt đối, ≤60 từ). Phân bổ bắt buộc: **≥ 1/3 số tình huống lấy từ probe P43–P52** và **≥ 2 tình huống từ P53–P55b** (loại #22 hỏi message nguyên văn, #23 guest, #24 trạng thái nút / màn hình hiển thị gì) (loại #16–#20: hoàn tiền thất bại, hai mốc hoàn tiền, callback trùng/sai thứ tự, job ngừng chạy, ghi giữ thất bại, vượt hạn mức bằng danh tính mới, giữ chỗ miễn phí lặp lại); phần còn lại phủ #1, #2, #4, #6, #7, #8, #11, #12, #15, ưu tiên ô KHL của bảng trạng thái, biên TTL/qty, dòng ⚠ RTM, ca M6 còn ✗, và mọi hit mức Cao ở A/B/G. Ghi vào `<thư-mục>/eval-set.md`.
2. Với TỪNG tình huống gọi **hai** Agent `subagent_type: executor` song song, prompt CHỈ gồm hai dòng và **giống nhau từng chữ**:
   ```
   Đường dẫn spec: <đường dẫn tuyệt đối BẢN NỘP spec.nop.md — đó là file Executor của BTC sẽ đọc>
   Tình huống: <văn bản tình huống>
   ```
   Reader 1 dùng model mặc định của agent. **Reader 2 gọi với `model: haiku`** — model Executor của BTC là thông tin bảo mật, BTC không công bố (00 §H câu 7), nên một reader yếu hơn là giả lập bảo toàn duy nhất đúng. Không kèm RTM, brief, đáp án kỳ vọng, hay bất kỳ bối cảnh khác.
3. Phân loại theo **bất đồng giữa hai reader**, không theo tự khai:
   - Hai reader ra **kết quả quan sát được khác nhau** (trạng thái cuối / con số / hướng tiền / ai thắng) ⇒ **ĐA NGHĨA thật, mức Cao**. Đây là tiêu chí chính. Ô `ĐA NGHĨA = KHÔNG` do chính reader tự khai **không** được dùng làm bằng chứng spec rõ: reader vừa đọc spec vừa tự chấm nó rõ là vòng tự xác nhận — bản 08/09 ra 15/15 "ĐỦ, KHÔNG mơ hồ" trong khi vẫn còn 7 lỗi nội dung mức Cao.
   - Hai reader **trùng nhau** nhưng `ĐỘ PHỦ ≠ ĐỦ` ⇒ lỗ hổng im lặng (Cao nếu ĐỘ PHỦ = KHÔNG hoặc tình huống chạm dòng ⚠ RTM; Trung nếu MỘT PHẦN).
   - Hai reader trùng nhau, ĐỘ PHỦ = ĐỦ, nhưng câu trả lời **trái với đáp án trong RTM/log** ⇒ spec viết trái specs thật, mức Cao (loại TRÚNG chắc nhất).
   - Hai reader trùng nhau, ĐỦ, khớp RTM ⇒ ✓.
   Ghi cả hai TRẢ LỜI cạnh nhau để người viết thấy chỗ lệch, kèm mục spec cần vá.
4. Nếu hết thời gian: giảm n xuống 8 nhưng **giữ hai reader**. Một reader trên 20 tình huống yếu hơn hai reader trên 8 — vì tiêu chí phát hiện đa nghĩa là bất đồng, không phải số lượng.

**G. KHẢ THI, MỤC TIÊU, LẠM DỤNG** *(khối mới — phần mà kit cũ không nhìn thấy)*
1. **Cổng F** (knowledge/32 §1): chạy 6 lệnh grep ở cuối §1 rồi chấm 8 kiểm tra F1–F8 cho từng BR/EX và từng dòng §0. Bảng kết quả: `BR | F1..F8 | ✗ nào | mức | cách xử lý (viết lại theo 32 §3 / hỏi theo 32 §6)`. ✗ ở F1, F2, F5, F7 = mức Cao.
2. **Bảng Mục tiêu↔Luật**: đọc `mo-hinh-bai-toan.md` M1; nếu chưa có bảng thì lập tại đây theo knowledge/05 §2. Kết luận: mục tiêu nào không có luật phục vụ (S22, Cao); luật nào làm hỏng mục tiêu (S23, Cao — **xử lý bằng câu hỏi verify, không bằng viết lại**); BR nào không thuộc mục tiêu nào (ứng viên cắt).
3. **Độ phủ suy biến**: 12 ca knowledge/05 §M6 × spec → ✓ có luật bao trùm / ⛔ nằm trong NGOÀI phạm vi tường minh / ✗ hở. Mỗi ✗ có tiền dính vào = mức Cao và **phải** có tình huống eval tương ứng ở khối D.
4. **Độ phủ láng giềng**: 6 láng giềng knowledge/05 §M4 × spec → có luật cho chế độ lỗi hay không. Thiếu = lỗi Cao loại #19.
5. **Bảng lạm dụng**: 6 kẻ knowledge/05 §M5 × spec → luật nào chặn, lách được không. Với mỗi kẻ, viết một câu "kẻ này làm gì với spec hiện tại" — nếu câu đó mô tả một lợi thế đạt được mà không vi phạm luật nào, đó là lỗi Cao loại #17/#18.
6. **Chi phí ẩn**: 6 khoản knowledge/32 §4 → spec có nói ai chịu / có trần hay không (mức Trung).
7. **Giá trị tự nghĩ ra** *(kiểm mới, đi cùng 30 §1b)*: với mỗi BR nguồn `G-xx` có con số hoặc lựa chọn nhị phân, đối chiếu 31 phát biểu knowledge/20 §4 và catalogue 10 §6. Ba kết quả:
   - Trùng mặc định ngành ⇒ ✓, không phải rủi ro (Executor mù cũng đoán như vậy).
   - **Ngược mặc định ngành mà không có dòng `A-xx` đỡ ⇒ lỗi Cao.** Đây là ca duy nhất tệ hơn im lặng: nó biến một TRƯỢT tiềm năng thành TRÚNG gần chắc chắn. Cách xử lý: đổi về mặc định ngành (loại `SỬA`, làm được ngay), hoặc nếu còn câu hỏi thì đưa vào C5.
   - Không có mặc định ngành nào cho ô đó ⇒ mức Trung, ghi vào `RỦI RO ĐÃ BIẾT`.
8. **Markdown & sơ đồ**: bản nộp không có ảnh (`grep -n '!\[' `), không có link ngoài; nếu có khối ```mermaid thì dòng catch-all 0.15 (sơ đồ chỉ minh họa, bảng và luật có mã thắng) phải tồn tại — thiếu = lỗi Cao loại #5, và sơ đồ lặp lại nội dung bảng state × event 6.3 = mức Trung, ứng viên cắt token.

**E. ĐẾM TOKEN** — đếm trên **bản nộp** `spec.nop.md` (không phải `spec.md` nội bộ, dài hơn ~500 token vì mang `← A-xx` và nhãn `[M-x]`):
```
LC_ALL=C.UTF-8 wc -w -m <thư-mục>/spec.nop.md
token ≈ max( số_từ × 2,5 , số_ký_tự / 2,2 )
```
In cả hai con số và giá trị lấy. **Không** dùng `wc` thiếu `LC_ALL=C.UTF-8` — locale `C` đếm sai ký tự đa byte.

Ba ngưỡng, **cả ngưỡng dưới cũng là phát hiện**:
- **> 10.000 = lỗi Cao** (chặn nộp).
- **> 9.000 = cảnh báo "hết đệm"** kèm danh sách ứng viên cắt (BR nhãn `[M-0]`, hit nhóm lint 22, mục 10 → 9 → 5 → 3 → 2 theo knowledge/33 §6, sơ đồ mermaid trùng bảng).
- **< 7.500 = lỗi Trung "chưa tiêu hết ngân sách"**, nâng lên **Cao** nếu đồng thời có ô trống, có logic mục 6 dưới 5 case, hoặc có nhánh từ chối chưa có message. Kèm danh sách **tiêu vào đâu** theo bảng ưu tiên knowledge/33 §5: thêm case lỗi/biên → thêm message mục 4 → nâng 7.2 lên 8–10 ca → lưu đồ `flowchart TD` → bảng 3 trạng thái mục 5 → `erDiagram`. Với hạn mức 10.000, spec ngắn không phải spec gọn: chỗ trống là chỗ Executor đoán.

Kèm **bảng token theo mục** (đo bằng cách cắt file theo heading): mục nào vượt ngân sách knowledge/33 §5 quá 30%, mục nào dưới 50% — cả hai đều là tín hiệu phân bổ sai. Nếu giao diện nộp của BTC hiển thị token thật → lấy số đó và ghi hệ số `token thật / số từ` vào báo cáo. Thiếu `spec.nop.md` → lỗi Cao "chưa sinh bản nộp", đếm tạm trên `spec.md` kèm ghi chú.

**F. CỔNG CHẤT LƯỢNG** — **25 dòng** knowledge/30 §7 (gồm 22b sơ đồ/lưu đồ và 24 ngưỡng dưới token) (gồm 12 dòng cấu trúc 10 mục + dòng 18–23 từ knowledge/32 §5), đánh ✓/✗ với bằng chứng 1 dòng.

## Báo cáo `review.md` (cùng thư mục với spec)
1. Kết luận đầu file: **NỘP ĐƯỢC** hoặc **CHƯA** — CHƯA khi còn ≥1 lỗi mức Cao, hoặc **thiếu một trong 10 mục BTC**, hoặc **> 10.000 token**, hoặc **thiếu một trong ba sơ đồ BTC yêu cầu** (mục 5, 6, 9), hoặc dòng RTM chưa có BR, hoặc cổng chất lượng có ✗, hoặc **còn ✗ ở F1/F5**, hoặc **có mục tiêu brief không có luật phục vụ**, hoặc **có tình huống eval mà hai reader ra kết quả khác nhau**, hoặc **có BR mang giá trị ngược mặc định ngành mà không có `A-xx` đỡ** (khối G-7).
2. Bảng lỗ hổng theo định dạng knowledge/40 §4, sắp theo Mức giảm dần rồi theo thang §5 (đã chèn 2b–2e); cột "Viết lại đề xuất" là câu dán được vào spec. Câu viết lại KHÔNG ĐƯỢC bịa con số/giá trị: số có trong RTM/log → ghi số + `(A-xx)`; không có → để `[..]` và ghi "xác nhận với AI Khách hàng"; luật hiện có trong spec chưa đối chiếu được với log → giữ nguyên nội dung, chỉ sửa diễn đạt, và gắn "cần đối chiếu log".
   **Phân biệt ba loại phát hiện, ghi rõ ở cột riêng:**
   - `SỬA` = sửa được ngay bằng viết lại (mơ hồ, ô trống, F1, F5, S25–S27, thiếu mục / thiếu loại case / message không nguyên văn / thiếu cột Guest (S31–S37), **và giá trị ngược mặc định ngành ở khối G-7** — đổi về mặc định ngành là sửa, không phải đoán).
   - `HỎI` = đội đang không biết specs thật quy định gì và **còn nhịp để hỏi** (F2, F7, S22, S23, `G-xx` rủi ro cao). Xuất thành **lượt nhị phân rời** dán vào `/elicit xac-nhan` — mỗi mục một lượt, một ý, không chỉ thị, không phải câu hỏi mở.
   - `RỦI RO ĐÃ BIẾT` = đúng loại `HỎI` nhưng **hết câu hỏi** hoặc không lọt vào 10 phát biểu của C5. Không sửa, không hỏi; ghi lại kèm tình huống mà đối thủ sẽ dùng để bắn, để buổi chiều tự bắn trước và để rút bài học.
   Cắt danh sách `HỎI` theo **số lượt còn kịp gửi trước đường cắt** (ước ở đầu skill); phần dư xuống `RỦI RO ĐÃ BIẾT`, xếp theo rủi ro giảm dần.
3. Bảng eval set: tình huống | loại # | ĐỘ PHỦ | **TRẢ LỜI reader 1** | **TRẢ LỜI reader 2** | **lệch?** | đối chiếu RTM | mục cần vá.
4. **Khối G**: bảng cổng F; bảng Mục tiêu↔Luật; 12 ca suy biến; 6 láng giềng; 6 kẻ lạm dụng; 6 chi phí ẩn.
5. Cổng chất lượng 25 dòng ✓/✗. **Token bản nộp (cả hai công thức) + đệm còn lại dưới 10.000 + khoảng cách tới sàn 7.500 + bảng token theo mục.**
6. "Sửa trong 10 phút" — 5 việc đầu theo thang knowledge/40 §5, và **danh sách lượt hỏi còn kịp gửi** — mỗi mục đã viết sẵn thành một câu nhị phân, xếp theo rủi ro, dán được vào `/elicit xac-nhan`.
6b. **`RỦI RO ĐÃ BIẾT`** — bảng: giả định | mặc định ngành hay ngược | tình huống đối thủ sẽ dùng | mức. Đây là đầu vào cho `/attack` tự bắn spec mình lúc 12:00–13:00.

Nếu args có `sửa`: áp các câu viết lại mức Cao **thuộc loại `SỬA`** vào spec bằng Edit, chạy lại A, B, E, G-1, G-7, in diff token. **Mục thiếu hẳn thì không tự bịa nội dung** — dựng khung bảng rỗng có tên cột đúng và ghi rõ cần điền từ RTM. **Không** áp mục loại `HỎI`. Không sửa nội dung nghiệp vụ (không đổi con số/trạng thái) — chỉ sửa diễn đạt, điền ô trống bằng "Từ chối 0.5", thêm luật cho chế độ lỗi của láng giềng (knowledge/32 §3.2, không cần số mới), và tách mốc quyết định/hoàn tất (knowledge/32 §3.1, để `[..]` cho số chưa biết).

## Output bắt buộc
- [ ] `review.md` với kết luận NỘP ĐƯỢC/CHƯA và tiêu chí.
- [ ] Bảng "10 mục BTC: có / thiếu / rỗng" ở đầu báo cáo.
- [ ] Mỗi phát hiện có vị trí, trích, loại, mức, **nhãn `SỬA`/`HỎI`**, viết lại hoặc câu hỏi.
- [ ] Eval set n tình huống đã chạy qua **hai** executor mù độc lập, cả hai câu trả lời được ghi.
- [ ] Khối G đầy đủ 6 bảng.
- [ ] Khối câu hỏi `HỎI` dán được vào `/elicit`.
- [ ] `eval-set.md` lưu lại (tái dùng cho /attack chính spec mình).

## Không được
- Đưa RTM/brief/đáp án vào prompt gọi executor; gửi hai reader hai prompt khác nhau.
- **Dùng ô `ĐA NGHĨA = KHÔNG` do chính reader tự khai làm bằng chứng spec rõ** — bằng chứng duy nhất là hai reader trùng kết quả.
- Sửa spec khi không có `sửa`; đổi con số nghiệp vụ khi `sửa` **trừ** ca G-7 (đưa một giá trị tự nghĩ ra về mặc định ngành, có ghi rõ trong diff); áp mục loại `HỎI` vào spec.
- Sinh danh sách `HỎI` dài hơn số câu hỏi còn lại cho phép; sinh `HỎI` dạng câu hỏi mở thay vì phát biểu Đúng/Sai.
- Kết luận NỘP ĐƯỢC khi còn lỗi Cao, còn ✗ F1/F5, hay còn mục tiêu brief không có luật.
- Bỏ khối D hoặc bỏ reader thứ hai vì "hết thời gian" — giảm n xuống 8 và giữ 2 reader.
- Bỏ khối G vì "spec đã sạch lint" — đó chính là trạng thái mà khối G được viết ra để soi.
