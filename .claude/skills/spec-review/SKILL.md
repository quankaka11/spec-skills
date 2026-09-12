---
name: spec-review
description: Red team spec của đội trước khi nộp — kiểm đủ 10 mục BTC, lint từ mơ hồ và nội dung, kiểm cấu trúc và ô trống bảng, kiểm phủ RTM (cả dòng đã hỏi và dòng giả định), cổng khả thi F + bảng Mục tiêu↔Luật + mô hình lạm dụng + kiểm giá trị tự nghĩ ra, eval set qua HAI agent executor mù độc lập (đo đa nghĩa bằng bất đồng), đếm token bản nộp, cổng chất lượng 30 dòng, kết luận NỘP ĐƯỢC/CHƯA kèm danh sách SỬA và HỎI (mỗi mục một lượt nhị phân, cắt theo số nhịp còn kịp gửi). Dùng 11:38–11:48 ngày thi hoặc trong diễn tập khi người dùng nói "review spec", "soi spec mình", "red team", "kiểm tra trước khi nộp", "spec-review".
argument-hint: "[đường-dẫn-spec] [n=20] [sửa]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(LC_ALL=C.UTF-8 wc *), Agent
---
# /spec-review — Red team spec của mình

Mục tiêu: tìm mọi chỗ Executor có thể trả lời khác specs thật, xếp theo mức, kèm câu viết lại dán được. Không sửa spec trừ khi args có `sửa`.

## Input
`$ARGUMENTS` = `[đường-dẫn-spec] [n] [sửa]`. Mặc định spec `battle/spec.md`, n = 20 tình huống eval. `rtm.md` cùng thư mục nếu có.

**Đọc sổ hạn mức ở dòng đầu `log-khach-hang.md` trước tiên** (`<t>/<Q> token · lượt · nhịp · đường cắt`): thứ còn lại là **token và số nhịp trước đường cắt**, không phải số câu. Ước `số lượt còn kịp = min(token còn lại ÷ 150 ; số phút còn lại của pha hỏi × 60 ÷ nhịp)`; danh sách `HỎI` cắt đúng con số đó, **mỗi mục một lượt một ý, ưu tiên dạng nhị phân**. Còn 0 lượt ⇒ **không có danh sách `HỎI`**, mọi phát hiện loại đó chuyển thành `RỦI RO ĐÃ BIẾT` (xem báo cáo mục 6b).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/33-cau-truc-spec-btc.md` §1 (10 mục BTC bắt buộc), §2 ("ba thứ" + 5 gạch của mỗi logic ở mục 6), §5–§6 (ngân sách token, thứ tự cắt), §7 (Mermaid — sơ đồ không thay bảng).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md`: §2 danh sách đen phẳng (**22 nhóm** — 1–16 mơ hồ, 17–22 nội dung), §3 kiểm tra cấu trúc **S1–S39** (S31–S39 = cấu trúc 10 mục), §4 định dạng báo cáo, §5 thang ưu tiên sửa.
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md`: §1 cổng F 8 kiểm tra, §2 bảng thực tế phụ thuộc ngoài, §3 mẫu viết lại, §4 chi phí ẩn, §5 cổng chất lượng dòng 12–16, §6 chuyển hit F thành câu hỏi.
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md`: §2 bảng Mục tiêu↔Luật, §M4 6 láng giềng, §M5 6 kẻ lạm dụng, §M6 12 kịch bản suy biến.
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §2.2–§2.3 (đếm tổ hợp, soi gap/overlap), §3 (mọi ô state × event), §4 (mức phủ).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (**24 loại** lỗ hổng — để sinh eval set; #22 message, #23 guest, #24 trạng thái UI là loại mới 11/09), §2 (quy tắc viết tình huống), §5 probe P1–P58.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §2 tên 14 nhóm N1–N14 (để tick heading spec MÌNH ở khối B) + §1 (cổng 8 kiểm tra — mọi mục trong danh sách `HỎI` phải qua cổng này trước khi in) + §3b (bốn vùng BTC: mục 4 message giờ hỏi được).
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md` §1 (ngân sách token, quy tắc mermaid), §1b (**luật giả định = mặc định ngành** — căn cứ của khối G-7), §7 (cổng chất lượng **30 dòng**).
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §4b (bảng mặc định của miền — nguồn tra là `battle/mac-dinh-nganh.md`, **không** phải §4), §4c (quét hằng số — thuật toán của khối G-7), §5 (RTM hai loại dòng).
- `${CLAUDE_PROJECT_DIR}/knowledge/33-cau-truc-spec-btc.md` **§5b thang bằng chứng + đường đỏ** (căn cứ chấm "spec ngắn" là đúng hay thiếu) và `knowledge/30-viet-spec.md` **§1c bốn cách biến bằng chứng thành khẳng định sai** (căn cứ khối G-9).

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
7. **Giá trị tự nghĩ ra — quét CHỮ TRONG BẢN NỘP, không quét sổ RTM.** Đây là điểm sửa quan trọng nhất của khối G: bản cũ lặp trên "mỗi BR nguồn `G-xx`", nên mọi giá trị viết thẳng vào **mục 2 và mục 4** mà không ai mở dòng `G-xx` đều **vô hình** với cổng này. Ở thi thử 11/09, 6 trong 19 lỗi nằm đúng vùng mù đó, và cổng chỉ báo 2 hit.

   **Thuật toán (cơ học, không phán đoán):**
   a. Grep trên `spec.nop.md` mọi **chữ số** và mọi mẫu định lượng. **Danh sách đơn vị phải theo ngôn ngữ của bản nộp** — đây là chỗ đo được ở drill "lich-hen": chạy với danh sách đơn vị tiếng Nhật trên một spec tiếng Việt **bỏ sót 2/3 vi phạm cài sẵn**, mở rộng đơn vị xong thì bắt đủ 3/3.
      - Nhật: `以内|以上|以下|まで|単位|文字|桁|回|枚|件|割|%|分|時間|日|ポイント|円`
      - Việt: `đồng|điểm|ký tự|chữ số|giờ|phút|ngày|buổi|lần|chỗ|người|%`
      - Khoảng: `N〜M`, `N-M`, và **`từ N đến M`** — dạng chữ dễ bị bỏ nhất.
   b. Bỏ hit là **mã định danh** (BR-xx, E-1xx, số thứ tự mục, số thứ tự dòng bảng, số thứ tự điều kiện).
   c. Mỗi hit còn lại là một **hằng số nghiệp vụ** và phải trỏ về một `A-xx` hoặc một `G-xx` trong `rtm.md`. Không trỏ về đâu ⇒ **lỗi Cao "hằng số không có nguồn"**, kể cả khi nó nằm trong mục 2 hay mục 4.
   d. In bảng: `Hằng số | vị trí (mục) | nguồn A-xx/G-xx/KHÔNG CÓ | tra mac-dinh-nganh.md thấy gì | mức`.
   e. Đối chiếu tổng số hằng số với khối `M7 — Hằng số` của `mo-hinh-bai-toan.md`. Hằng số trong spec **không có** trong M7 ⇒ ô này chưa từng được ai cân nhắc: mức Cao.

   Với mỗi hằng số đã có nguồn `G-xx`, đối chiếu `battle/mac-dinh-nganh.md`. Ba kết quả:
   - Trùng mặc định ngành ⇒ ✓, không phải rủi ro (Executor mù cũng đoán như vậy).
   - **Ngược mặc định ngành mà không có dòng `A-xx` đỡ ⇒ lỗi Cao.** Đây là ca duy nhất tệ hơn im lặng: nó biến một TRƯỢT tiềm năng thành TRÚNG gần chắc chắn. Cách xử lý: đổi về mặc định ngành (loại `SỬA`, làm được ngay), hoặc nếu còn câu hỏi thì đưa vào C5.
   - **Tra `KHÔNG BIẾT` (miền không có mặc định cho ô đó) ⇒ lỗi Cao, không phải Trung.** Ô này Executor mù cũng không đoán được, nên spec đang là nguồn duy nhất và đang nói một con số không ai kiểm được. Xử lý theo 30 §1b-2b: còn nhịp ⇒ `HỎI`; hết nhịp ⇒ `SỬA` hạ xuống **luật an toàn hai chiều** (bậc 8, 33 §5b) — bỏ con số, giữ quan hệ.
   - Thiếu `battle/mac-dinh-nganh.md` ⇒ **lỗi Cao ở mức quy trình**: cổng G-7 không chạy được, mọi giá trị trong spec đang không có gì để đối chiếu. Ghi rõ trong kết luận thay vì báo "0 hit".

9. **Khối G-9 — bốn cách biến bằng chứng thành khẳng định sai** (knowledge/30 §1c). Bốn kiểm cơ học, chạy trên bản nộp:
   - **G-9a `NGOÀI PHẠM VI` biến thành lệnh CẤM.** Lấy danh sách NGOÀI phạm vi ở mục 1; với mỗi mục trong đó, grep tên nó trên toàn spec. Xuất hiện ở bất kỳ chỗ nào **ngoài** mục 1 — nhất là ở mục 8 dưới dạng "không được / không sử dụng được / 利用できない" — là **lỗi Cao**.
     **Bắt buộc có bước đồng nghĩa, grep literal là không đủ.** Đã kiểm chứng trên hồ sơ 11/09: danh sách NGOÀI phạm vi ghi 「ゲストのカート」 còn lệnh cấm ở mục 8 viết 「未ログイン利用者」 — grep literal ra **0 hit**, kiểm đồng nghĩa ra **1 hit Cao**. Cách làm đúng, đo được ở drill: **trích DANH TỪ actor/đối tượng bên trong mục, đừng grep cả cụm**. Mục "màn quản trị của lễ tân" thì thứ phải grep là `lễ tân`, không phải cả cụm — grep cả cụm ra 0 hit trong khi spec có luật `Lễ tân không được đổi hoặc huỷ lịch hộ khách`. Sau đó với mỗi danh từ, liệt kê 2–3 cách gọi khác (ゲスト↔未ログイン↔非会員; lễ tân↔nhân viên↔quản trị) rồi grep từng cách, chỉ trên các câu có mẫu cấm (`không được|không thể|利用できない|できない`).
     Kiểm thêm: có dòng catch-all "im lặng = CẤM" nào đang phủ lên một actor thuộc danh sách NGOÀI phạm vi không. Ngoài phạm vi ⇒ im lặng, không phải cấm.
   - **G-9b luật rộng hơn danh từ đã hỏi.** Với mỗi BR có `← A-xx`, mở `log-khach-hang.md` lấy **nguyên văn câu hỏi** sinh ra `A-xx` đó. Danh từ chủ ngữ của BR rộng hơn danh từ trong câu hỏi ⇒ lỗi Cao, và phần rộng ra phải tách thành `G-xx`. (Ví dụ đã xảy ra: hỏi về *bảng tiền* → viết luật cho *đơn giá từng dòng*; hỏi *trần chung* → viết *không có trần nào*.)
   - **G-9c luật khẳng định dựng trên câu deflection.** Tách `log-khach-hang.md` theo từng khối lượt, đánh dấu lượt nào có câu trả lời dạng "không có quy định riêng / 特に定めていません / 特にこだわりがありません / tuỳ đội". Mọi dòng RTM (**cả `A-xx` lẫn `G-xx`**) có nguồn là một lượt như vậy thì nội dung **phải** là `KHÔNG BIẾT` hoặc một mặc định tra được — một **luật khẳng định do đội tự phát biểu** là lỗi Cao, **kể cả khi nó không chứa con số**.
     Kiểm chứng trên hồ sơ 11/09: bốn hit, trong đó `Không giới hạn số lần dùng một mã` và `ポイント không trừ vào 送料` đều là câu khẳng định **không có chữ số nào** — và cả hai đều sai so với đáp án chuẩn. Lọc theo "có chứa con số" sẽ bỏ sót cả hai; đừng lọc như vậy.
     **Phần nào cơ học được, phần nào không** (đo ở drill): nửa "duyệt dòng RTM có nguồn là lượt deflection" **chạy bằng script được** và đã bắt đúng 4 hit trên hồ sơ 11/09. Nửa "luật trong spec phủ lên một ô đã đánh `KHÔNG BIẾT`" **không** grep được — phải đọc. Bằng chứng: khi dựng một spec drill *cố ý viết sạch*, vẫn lọt một luật kiểu `không có loại hẹn nào được miễn theo quy tắc riêng` phủ đúng ô khách đã nói "không có quy định riêng". Vì thế bước này bắt buộc **đọc từng luật đối chiếu danh sách ô `KHÔNG BIẾT`**, không được thay bằng grep rồi báo 0 hit.

     **Ngoại lệ hợp lệ:** một lượt có thể vừa trả lời thật vừa deflection ("X giữ nguyên, còn phần sau thì không quy định"). Khi đó phần trả lời thật tách thành một dòng riêng và **không** tính hit — điều kiện là nội dung dòng đó nằm trong nguyên văn câu trả lời. Kiểm bằng cách đối chiếu nguyên văn, không bằng cách tin nhãn.
   - **G-9d bảng mã lỗi suy rộng.** Đếm số mã lỗi trong mục 4; đếm số mã có `A-xx` đỡ. Chênh lệch > 0 ⇒ mức Cao nếu chênh ≥ 2, Trung nếu chênh = 1. Mã không có `A-xx` phải hạ về mô tả điều kiện bằng lời (bậc 8), không đánh số.

10. **Khối G-10 — mười bốn ô "đề luôn chốt"** (20 §4d = 50 §8, hai mặt của cùng một bảng). Đây là khối **đo được bằng grep** và là khối duy nhất nói cho biết **đối thủ sẽ bắn vào đâu**, vì họ dùng đúng bảng này làm nguồn đạn (`/attack` bước 0b). Chạy một lệnh grep cho mỗi ô trên `spec.nop.md`, bằng từ khoá của miền:

    | Ô | Grep trên bản nộp | Thiếu ⇒ |
    |---|---|---|
    |Đ1 bội số/bước nhảy ô nhập số|`単位\|bội số\|bước nhảy\|刻み`|Cao|
    |Đ2 trần/sàn ô nhập|`上限\|最大\|trần\|tối đa`|Cao|
    |Đ3 thời điểm xét điều kiện|`時点\|lúc\|tại thời điểm`|Cao|
    |Đ4 phép so dùng tổng nào|tên từng tổng trong glossary|Cao|
    |Đ5 tập con bị loại trừ|`対象外\|除く\|loại trừ\|không áp dụng cho`|Cao|
    |Đ6 phụ phí vẫn thu khi đã miễn|`追加\|phụ phí\|vẫn thu`|Trung|
    |Đ7 mã lỗi đủ + thứ tự|đếm mã ở mục 4 (trùng G-9d)|Cao|
    |Đ8 gỡ/hoàn tác trước khi chốt|`解除\|gỡ\|huỷ áp\|hoàn tác`|Cao|
    |Đ9 lặp lại đúng thao tác đã làm|`同じ\|lại\|trùng\|再`|Cao|
    |Đ10 message kèm con số|mục 4: message có `{ }` hay không|Trung|
    |Đ11 trạng thái rỗng|`0件\|rỗng\|trống\|không có`|Trung|
    |Đ12 thời hạn lưu dữ liệu tạm|`保持\|ngày\|日間\|hết hạn`|Cao|
    |Đ13 chuẩn hoá đầu vào|`桁\|độ dài\|大文字\|hoa thường\|trim`|Trung|
    |Đ14 phân hạng & mốc riêng|`会員\|hạng\|ランク\|区分`|Cao|

    Ô **0 hit** ⇒ một phát hiện, mức ở cột phải, loại `SỬA` nếu tra được `mac-dinh-nganh.md`, loại `HỎI` nếu còn nhịp và ô đó `⚠ Cao`. Ô có hit nhưng giá trị không trỏ về `A-xx`/`G-xx` nào ⇒ chuyển sang G-7.
    **Không được báo "đủ 14/14" chỉ vì grep có hit** — hit chứng minh có chữ, không chứng minh có luật. Với mỗi ô có hit, trích đúng câu luật vào báo cáo; trích không ra câu nào thì tính là 0 hit.
8. **Markdown & sơ đồ**: bản nộp không có ảnh (`grep -n '!\[' `), không có link ngoài; nếu có khối ```mermaid thì dòng catch-all 0.15 (sơ đồ chỉ minh họa, bảng và luật có mã thắng) phải tồn tại — thiếu = lỗi Cao loại #5, và sơ đồ lặp lại nội dung bảng state × event 6.3 = mức Trung, ứng viên cắt token.

**E. ĐẾM TOKEN** — đếm trên **bản nộp** `spec.nop.md` (không phải `spec.md` nội bộ, dài hơn ~500 token vì mang `← A-xx` và nhãn `[M-x]`):
```
LC_ALL=C.UTF-8 wc -w -m <thư-mục>/spec.nop.md
token ≈ max( số_từ × 2,5 , số_ký_tự / 2,2 )
```
In cả hai con số và giá trị lấy. **Không** dùng `wc` thiếu `LC_ALL=C.UTF-8` — locale `C` đếm sai ký tự đa byte.

Ba ngưỡng, **cả ngưỡng dưới cũng là phát hiện**:
- **> `L` = lỗi Cao** (chặn nộp). `L` đọc từ brief / `battle/tham-so.md` (00 §A3); không có ⇒ hỏi người dùng trước khi chấm ngưỡng.
- **> `0,90 × L` = cảnh báo "hết đệm"** kèm danh sách ứng viên cắt (BR nhãn `[M-0]`, hit nhóm lint 22, mục 10 → 9 → 5 → 3 → 2 theo knowledge/33 §6, sơ đồ mermaid trùng bảng).
- **< `0,75 × L`** ⇒ **chưa kết luận được, phải đo chế độ trước** (00 §A3). Tính `T_A` (token của mọi luật có `A-xx` + khung 10 mục + catch-all + message nguyên văn):
  - `T_A < 0,75 × L` (**RỘNG**) ⇒ lỗi Trung "chưa tiêu hết ngân sách", nâng **Cao** nếu đồng thời có nhánh từ chối chưa có message. Kèm danh sách tiêu vào đâu **theo thang bằng chứng 33 §5b từ bậc đang dừng đi xuống, dừng ở đường đỏ**.
  - `T_A ≥ 0,75 × L` (**CHẬT**) ⇒ **không phải lỗi**. Spec ngắn vì bằng chứng ít. Ở chế độ này, báo "chưa tiêu hết ngân sách" là chẩn đoán sai và đẩy người viết đi điền bừa — đúng cơ chế đã làm hỏng bản thi thử 11/09.
- In kèm **bậc đang dừng trên thang bằng chứng** và **có luật nào đã vượt đường đỏ chưa** (bậc 9 khi bậc 8 chưa thử, hoặc khi còn nhịp hỏi) — vượt đường đỏ = lỗi Cao.

Kèm **bảng token theo mục** (đo bằng cách cắt file theo heading), so với **% của `L`** ở knowledge/33 §5: mục nào vượt quá 30%, mục nào dưới 50% — cả hai đều là tín hiệu phân bổ sai. Nếu giao diện nộp của BTC hiển thị token thật → lấy số đó và ghi hệ số `token thật / số từ` vào báo cáo. Thiếu `spec.nop.md` → lỗi Cao "chưa sinh bản nộp", đếm tạm trên `spec.md` kèm ghi chú.

**F. CỔNG CHẤT LƯỢNG** — **30 dòng** knowledge/30 §7 (gồm 22b sơ đồ/lưu đồ, 24 ngưỡng dưới theo chế độ, và 25–30 hằng số/đường đỏ/G-9) (gồm 12 dòng cấu trúc 10 mục + dòng 18–23 từ knowledge/32 §5), đánh ✓/✗ với bằng chứng 1 dòng.

## Báo cáo `review.md` (cùng thư mục với spec)
1. Kết luận đầu file: **NỘP ĐƯỢC** hoặc **CHƯA** — CHƯA khi còn ≥1 lỗi mức Cao, hoặc **thiếu một trong 10 mục BTC**, hoặc **> `L` token**, hoặc dòng RTM chưa có BR, hoặc cổng chất lượng có ✗, hoặc **còn ✗ ở F1/F5**, hoặc **có mục tiêu brief không có luật phục vụ**, hoặc **có tình huống eval mà hai reader ra kết quả khác nhau**, hoặc **có hằng số trong bản nộp không trỏ về `A-xx`/`G-xx` nào** (G-7c), hoặc **có giá trị ngược mặc định ngành không có `A-xx` đỡ**, hoặc **có luật đã vượt đường đỏ** (33 §5b), hoặc **còn hit ở G-9a…G-9d**.

1b. **`CHƯA` là CỔNG CHẶN, không phải lời khuyên.** Đây là chỗ hỏng đắt nhất của kit cũ: thi thử 11/09 kết luận `CHƯA` kèm 9 câu hỏi xếp hạng đúng, còn 73% ngân sách hỏi và vài phút đồng hồ — rồi bản nộp vẫn đi ra y nguyên, và 10 trong 19 lỗi nằm đúng trong 9 câu đó.

   Khi kết luận là `CHƯA`, skill **bắt buộc**:
   - In ngay dưới kết luận: `→ CHƯA ĐƯỢC NỘP. Việc kế tiếp: /elicit xac-nhan <thư-mục>` kèm **danh sách `HỎI` đã dán sẵn**, và một dòng `Còn <n> lượt kịp gửi trước <hh:mm> · <t> token còn lại` lấy từ sổ hạn mức.
   - **Không sinh và không cập nhật `spec.nop.md`** trong lần chạy này (kể cả khi args có `sửa`), trừ khi mọi mục `HỎI` đã rỗng hoặc đã hết nhịp. Bản nộp chỉ được sinh từ một lần chạy kết luận `NỘP ĐƯỢC`, hoặc từ một lần chạy `CHƯA` mà danh sách `HỎI` rỗng vì hết giờ — và khi đó phải ghi rõ `nộp dưới rủi ro đã biết: <số> mục`.
   - Hỏi người dùng đúng một câu ở cuối output: **"còn nhịp hỏi không?"** Còn ⇒ chạy `/elicit xac-nhan`. Hết ⇒ chạy lại `/spec-review` với mọi mục `HỎI` đã chuyển xuống `RỦI RO ĐÃ BIẾT`, và khi đó mới sinh bản nộp.
   - Mục `HỎI` nào đã hỏi và có câu trả lời thì lần chạy sau phải thấy nó biến mất khỏi danh sách; còn nguyên ⇒ in cảnh báo `HỎI chưa được thực thi qua <n> lần chạy` ở đầu báo cáo.
2. Bảng lỗ hổng theo định dạng knowledge/40 §4, sắp theo Mức giảm dần rồi theo thang §5 (đã chèn 2b–2e); cột "Viết lại đề xuất" là câu dán được vào spec. Câu viết lại KHÔNG ĐƯỢC bịa con số/giá trị: số có trong RTM/log → ghi số + `(A-xx)`; không có → để `[..]` và ghi "xác nhận với AI Khách hàng"; luật hiện có trong spec chưa đối chiếu được với log → giữ nguyên nội dung, chỉ sửa diễn đạt, và gắn "cần đối chiếu log".
   **Phân biệt ba loại phát hiện, ghi rõ ở cột riêng:**
   - `SỬA` = sửa được ngay bằng viết lại (mơ hồ, ô trống, F1, F5, S25–S27, thiếu mục / thiếu loại case / message không nguyên văn / thiếu cột Guest (S31–S37), **và giá trị ngược mặc định ngành ở khối G-7** — đổi về mặc định ngành là sửa, không phải đoán).
   - `HỎI` = đội đang không biết specs thật quy định gì và **còn nhịp để hỏi** (F2, F7, S22, S23, `G-xx` rủi ro cao). Xuất thành **lượt nhị phân rời** dán vào `/elicit xac-nhan` — mỗi mục một lượt, một ý, không chỉ thị, không phải câu hỏi mở.
   - `RỦI RO ĐÃ BIẾT` = đúng loại `HỎI` nhưng **hết câu hỏi** hoặc không lọt vào 10 phát biểu của C5. Không sửa, không hỏi; ghi lại kèm tình huống mà đối thủ sẽ dùng để bắn, để buổi chiều tự bắn trước và để rút bài học.
   Cắt danh sách `HỎI` theo **số lượt còn kịp gửi trước đường cắt** (ước ở đầu skill); phần dư xuống `RỦI RO ĐÃ BIẾT`, xếp theo rủi ro giảm dần.
3. Bảng eval set: tình huống | loại # | ĐỘ PHỦ | **TRẢ LỜI reader 1** | **TRẢ LỜI reader 2** | **lệch?** | đối chiếu RTM | mục cần vá.
4. **Khối G**: bảng cổng F; bảng Mục tiêu↔Luật; 12 ca suy biến; 6 láng giềng; 6 kẻ lạm dụng; 6 chi phí ẩn; **bảng 14 ô "đề luôn chốt" (G-10) kèm câu luật đã trích cho mỗi ô có hit**.
5. Cổng chất lượng 30 dòng ✓/✗. **Token bản nộp (cả hai công thức) + `L` + đệm còn lại + chế độ CHẬT/RỘNG kèm `T_A` + bậc đang dừng trên thang bằng chứng + bảng token theo mục.**
6. "Sửa trong 10 phút" — 5 việc đầu theo thang knowledge/40 §5, và **danh sách lượt hỏi còn kịp gửi** — mỗi mục đã viết sẵn thành một câu nhị phân, xếp theo rủi ro, dán được vào `/elicit xac-nhan`.
6b. **`RỦI RO ĐÃ BIẾT`** — bảng: giả định | mặc định ngành hay ngược | tình huống đối thủ sẽ dùng | mức. Đây là đầu vào cho `/attack` tự bắn spec mình lúc 12:00–13:00.

Nếu args có `sửa`: áp các câu viết lại mức Cao **thuộc loại `SỬA`** vào spec bằng Edit, chạy lại A, B, E, G-1, G-7, in diff token. **Mục thiếu hẳn thì không tự bịa nội dung** — dựng khung bảng rỗng có tên cột đúng và ghi rõ cần điền từ RTM. **Không** áp mục loại `HỎI`. Không sửa nội dung nghiệp vụ (không đổi con số/trạng thái) — chỉ sửa diễn đạt, điền ô trống bằng "Từ chối 0.5", thêm luật cho chế độ lỗi của láng giềng (knowledge/32 §3.2, không cần số mới), và tách mốc quyết định/hoàn tất (knowledge/32 §3.1, để `[..]` cho số chưa biết).

## Output bắt buộc
- [ ] `review.md` với kết luận NỘP ĐƯỢC/CHƯA và tiêu chí.
- [ ] Kết luận `CHƯA` ⇒ dòng `→ CHƯA ĐƯỢC NỘP. Việc kế tiếp: /elicit xac-nhan …` + số lượt/token còn lại + câu hỏi "còn nhịp hỏi không?"; và **`spec.nop.md` KHÔNG được sinh/cập nhật**.
- [ ] Bảng **hằng số trong bản nộp** (G-7): mỗi con số → nguồn `A-xx`/`G-xx`/KHÔNG CÓ. Quét trên `spec.nop.md`, không trên RTM.
- [ ] Bốn kiểm **G-9a…G-9d** đã chạy, mỗi kiểm có số hit (0 cũng phải ghi).
- [ ] **Chế độ CHẬT/RỘNG + `T_A` + bậc đang dừng trên thang bằng chứng** ở khối token.
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
- **Sinh hoặc cập nhật `spec.nop.md` trong một lần chạy kết luận `CHƯA` mà danh sách `HỎI` chưa rỗng và vẫn còn nhịp hỏi.**
- **Báo "chưa tiêu hết ngân sách" khi chưa đo `T_A`** — ở chế độ CHẬT đó là chẩn đoán sai và đẩy người viết đi điền bừa.
- Chấm khối G-7 bằng cách duyệt sổ RTM thay vì quét chữ trong bản nộp.
- Báo "G-7: 0 hit" khi `battle/mac-dinh-nganh.md` không tồn tại — khi đó cổng không chạy được, phải nói đúng như vậy.
- Bỏ khối D hoặc bỏ reader thứ hai vì "hết thời gian" — giảm n xuống 8 và giữ 2 reader.
- Bỏ khối G vì "spec đã sạch lint" — đó chính là trạng thái mà khối G được viết ra để soi.
