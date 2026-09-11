---
name: spec-review
description: Red team spec của đội trước khi nộp trên hai trục — trục HÌNH THỨC (đủ 10 mục BTC, lint 22 nhóm, S1–S39, ô trống bảng Case/trạng thái, message nguyên văn, guest, phủ RTM A-xx/G-xx) và trục NỘI DUNG (cổng khả thi F, bảng Mục tiêu↔Luật, 12 ca suy biến, 6 láng giềng, 6 kẻ lạm dụng, giá trị tự nghĩ ra) — chạy eval set qua HAI agent executor mù độc lập (đo đa nghĩa bằng bất đồng giữa hai reader), đếm token bản nộp, cổng chất lượng 23 dòng, kết luận NỘP ĐƯỢC/CHƯA kèm danh sách SỬA / HỎI / RỦI RO ĐÃ BIẾT đã cắt theo số câu hỏi còn lại. Dùng 11:40–11:52 ngày thi hoặc trong diễn tập khi người dùng nói "review spec", "soi spec mình", "red team", "kiểm tra trước khi nộp", "spec-review".
argument-hint: "[đường-dẫn-spec] [n=20] [sửa]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(LC_ALL=C.UTF-8 wc *), Agent
---
# /spec-review — Red team spec của mình

Mục tiêu: tìm mọi chỗ Executor có thể trả lời khác specs thật, xếp theo mức, kèm câu viết lại dán được. Hai trục soi: **hình thức** (10 mục BTC, mơ hồ, ô trống) và **nội dung** (luật có đứng được trong thực tế và có phục vụ mục tiêu brief không). Không sửa spec trừ khi args có `sửa`.

## Input
`$ARGUMENTS` = `[đường-dẫn-spec] [n] [sửa]`. Mặc định spec `battle/spec.md`, n = 20 tình huống eval. `rtm.md` cùng thư mục nếu có.

**Đọc sổ hạn mức ở dòng đầu `log-khach-hang.md` TRƯỚC tiên** (`<k>/5 câu` đã dùng, token còn lại): số câu hỏi còn lại quyết định danh sách `HỎI` được dài bao nhiêu. Còn 1 câu ⇒ `HỎI` tối đa 10 phát biểu, tất cả nhồi vào C5. Còn 0 câu ⇒ **không có danh sách `HỎI`**, mọi phát hiện loại đó chuyển thành `RỦI RO ĐÃ BIẾT` (báo cáo mục 6b).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` §1 (10 mục BTC bắt buộc), §2 ("ba thứ" + 5 gạch của mỗi logic ở mục 6), §5–§6 (ngân sách, thứ tự cắt), §7 (Mermaid — sơ đồ không thay bảng).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md`: §2 danh sách đen phẳng (**22 nhóm** — 1–16 mơ hồ, **17–22 nội dung**), §3 kiểm tra cấu trúc **S1–S39** (S22–S30 = cấu trúc 10 mục, **S31–S39 = kiểm nội dung**), §4 định dạng báo cáo, §5 thang ưu tiên sửa (có bậc 0 và 2b–2f).
- `${CLAUDE_PROJECT_DIR}/knowledge/33-kha-thi-van-hanh.md` — **cổng khả thi là file 33, không phải 32**: §1 cổng F (F1–F8 + 6 lệnh grep), §2 bảng KHÔNG-được-hứa của phụ thuộc ngoài, §3 ba mẫu viết lại, §4 chi phí ẩn, §5 cổng chất lượng bổ sung, §6 chuyển hit F thành câu hỏi.
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md`: §2 bảng Mục tiêu↔Luật, §M4 6 láng giềng, §M5 6 kẻ lạm dụng, §M6 12 ca suy biến.
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §2.2–§2.3 (đếm tổ hợp, soi gap/overlap), §3 (mọi ô state × event), §4 (mức phủ).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (**24 loại lỗ hổng**; #16 message, #17 guest, #18 trạng thái UI từ cấu trúc BTC; **#19–#24** bất khả thi / tự đánh bại mục tiêu / hạn mức không cưỡng chế / thiếu luật thất bại phụ thuộc ngoài / phạm vi NGOÀI rộng hơn brief / lệch đồng thuận chéo), §2 (quy tắc viết tình huống), §5 probe — **P43–P48** (message/guest/UI/7.2) và **P49–P58** (khả thi & mục tiêu).
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §2 (tên **20 nhóm N0–N19** — để tick mục lục spec MÌNH ở khối B), §4 (**31 phát biểu** mặc định ngành — chấm khối G7), §5 (RTM hai loại dòng `A-xx` / `G-xx`).
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md` §1 (ngân sách token 10 mục), **§1b** (luật giả định = mặc định ngành — căn cứ của G7), §2 (catch-all 1.x, đủ **0.1–0.15**), §4 (**24 quy tắc** viết requirement — 18–24 là nội dung), §6 (mốc giờ 11:40–11:52), **§7 (cổng chất lượng 23 dòng)**.

## Thứ tự chạy — B0 → A → B → C → D → G → E → F

Khớp mốc giờ knowledge/30 §6: **11:40–11:46** cổng F + bảng Mục tiêu↔Luật (khối G); **11:46–11:49** rà catch-all + eval set hai reader + Ctrl+F 22 nhóm (khối A, B, C, D); **11:49–11:52** đếm token bản nộp + cắt (khối E, F). Trong diễn tập chạy đúng thứ tự khối dưới đây; ngày thi thì khối G phải xong trước 11:46 vì nó sinh phát biểu cho C5.

**B0. ĐỦ 10 MỤC BTC (S22) — chạy TRƯỚC mọi kiểm khác.** Đối chiếu mục lục spec với knowledge/32 §1. Mục nào vắng = lỗi **Cao**, ghi rõ "thiếu mục n — cả vùng trống". Mục có heading nhưng không có bảng/nội dung = **coi như vắng**, cùng mức Cao. Output bắt buộc: bảng **"10 mục: có / thiếu / rỗng"** đặt ở đầu `review.md` — đây là thứ người rà đọc trước tiên, và là thứ đối thủ soi đầu tiên lúc 13:00.

**A. LINT từ ngữ + nội dung — chạy cả 22 nhóm.** Với từng nhóm 1–22 ở knowledge/40 §2: Grep `-i`, output_mode content, `-n`, pattern là regex của nhóm, path = spec. Mỗi hit là một dòng báo cáo (vị trí, trích, nhóm, mức theo §3/§5). Bỏ hit trong phần định nghĩa từ khóa hoặc trong ngoặc kép có chủ ý; nhóm 9 và 11 chỉ đánh lỗi khi câu chứa hit thiếu ký hiệu ≤/</[a,b) hoặc thiếu danh sách đánh số.

**Nhóm 17–22 chạy trên BẢN NỘP** (`spec.nop.md`), không phải bản nội bộ — nhóm 22 đúng ra chỉ tồn tại ở bản nội bộ. Mỗi hit ở nhóm **17–20 là *nghi vấn*, KHÔNG kết luận mức tại đây**: phán quyết thuộc **cổng F ở khối G1**. Nhóm 21 (chi phí không ai nhận) → G6. Nhóm 22 (câu nói về chính spec) → S39, mức Thấp về rủi ro TRÚNG nhưng **luôn cắt** vì tiêu token.

**B. HOÀN CHỈNH cấu trúc — chạy S1–S39** (knowledge/40 §3). Rồi bắt buộc kiểm:

1. **Mục 6 — từng logic** (trọng tâm): mỗi logic có bảng step? có bảng Case? bảng Case đủ **3 loại case** (bình thường, biên, lỗi) — thiếu loại nào = lỗi Cao S25; đủ **5 gạch** (số · toán tử `>`/`≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng) — thiếu gạch nào = lỗi Cao S26, ghi tên gạch thiếu; cột Kết quả mong đợi đủ **7 chiều** (trạng thái cuối, kho, tiền, thông báo, hiển thị, trạng thái nút, message).
2. **Mục 4** (S23, S24): Grep message — mọi dòng phải có chuỗi trong **ngoặc kép**; câu kiểu "hiển thị thông báo lỗi/báo lỗi phù hợp" = lỗi **Cao** S23. Thiếu cột FE/BE = S24 Trung.
3. **Mục 2 + 8** (S27): Grep `guest|Guest|chưa đăng nhập` — mục 2 thiếu cột phân biệt login/guest hoặc mục 8 thiếu dòng Guest = lỗi **Cao**. Mục 8 phải có System/Job và phủ thao tác chỉ-đọc nếu có catch-all "không ✓ = cấm". Mục 2 phải có điều kiện ẩn/disable.
4. **Mục 7** (S28): 7.2 có đủ **4 ca BTC** nêu đích danh (dữ liệu đổi giữa hiển thị và submit · gửi trùng · mở link hai lần · mail fail sau khi đã lưu)? thiếu ca nào = lỗi Cao, ghi tên ca. **7.4 có gom bảng riêng** điều chưa chốt? `[GIẢ ĐỊNH]`/"TBD" rải rác ngoài bảng 7.4 = S28 Trung.
5. **Bảng state × event** (trong logic 6.3): liệt kê mọi ô, ô trống/"—" không giải thích = lỗi Cao S13/S17. **Có sơ đồ Mermaid `stateDiagram-v2` mà KHÔNG có bảng state × event = lỗi Cao** (S29) — sơ đồ chỉ vẽ chuyển hợp lệ, thiếu hẳn ô `Từ chối 0.5` / `KHL`, đúng chỗ Executor đoán sai.
5b. **Mermaid** (S30): mỗi khối ```mermaid có nhãn tiếng Việt bọc ngoặc kép? có 1–2 câu chữ tóm tắt ngay dưới (phòng render lỗi)? Thiếu câu chữ = lỗi Trung.
6. **Decision table**: đếm tổ hợp = tích các phân hoạch (knowledge/31 §2.2), thiếu → gap, hai hàng `-` chéo cột không hit policy → overlap #8.
7. Grep `BR-\d+|EX-\d+`: mã trùng, tham chiếu chết S10.
8. **Catch-all mục 1.x — đủ 0.1–0.15**: có ưu tiên xung đột (0.6), timezone (0.2), `[a,b)` (0.3), tình huống không khớp luật nào (0.5), idempotency (0.10), **và 0.11–0.14** (0.11 khử trùng theo mã giao dịch cổng · 0.12 tách mốc quyết định/hoàn tất · 0.13 neo định danh hạn mức · 0.14 thất bại thông báo không đổi trạng thái), **và 0.15 nếu spec có khối mermaid** (sơ đồ chỉ minh họa, bảng và luật có mã thắng). Thiếu 0.11–0.14 = lỗi Cao — bốn dòng này ~90 từ phủ 8/12 ca suy biến (knowledge/30 §2, 33 §3.2). Thiếu 0.15 khi có mermaid = lỗi Cao loại #5.
9. Mọi BR có tác động phụ K/T/N/L và câu KHÔNG ĐƯỢC (S14, S15).
10. Grep các mục hiếm: "đồng thời|cùng lúc", "safety|tồn đệm|an toàn|oversell", "guest|chưa đăng nhập", "múi giờ|UTC|ngày lễ", "rollback|hoàn nguyên|kiểm kê|lệch", "ưu tiên|ghi đè" — 0 hit = lỗ hổng #1 ứng viên, **phải có tình huống eval chạm vào ở khối D**.
11. **Tick 20 nhóm N0–N19 trên mục lục spec MÌNH** (tên nhóm ở knowledge/20 §2) — đúng thao tác `/attack` bước 1 làm với spec đối thủ. Nhóm vắng trên spec mình = chỗ đối thủ sẽ chấm hạng A (lỗ hổng #1 silent gap). **Kit cũ chỉ tick cho spec đối thủ**, nên spec mình không bao giờ bị soi bằng con mắt đó. Bảng: `N0..N19 | có heading/bảng? | mã BR đại diện | nếu vắng: probe đối thủ sẽ dùng`.

**C. PHỦ & KHỚP RTM** — tìm `rtm.md` + `log-khach-hang.md` ở thư mục spec, rồi thư mục cha, hoặc đường dẫn người dùng chỉ. Có RTM (hai loại dòng, knowledge/20 §5):

1. Dòng nào (`A-xx` **hoặc** `G-xx`) có cột Mã BR **trống** hoặc mã không tồn tại trong spec = lỗi **Cao**.
2. Với TỪNG dòng **`A-xx`**, so NỘI DUNG BR tương ứng với câu trả lời nguyên văn (con số, trạng thái, hướng tiền, ai được phép) — BR nói khác = lỗi **Cao** "trái specs thật" (loại TRÚNG chắc nhất, #15), ghi cả hai vế. **Đây là hạng lỗi nặng nhất trong cả báo cáo: đội đã *biết* mà viết sai.** Dòng `A-xx` chưa được dùng hết (có câu trả lời mà không thành luật nào) = lỗi **Cao** cùng hạng.
3. Với TỪNG dòng **`G-xx`**, kiểm cột Rủi ro đã điền chưa; dòng rủi ro Cao mà **không** nằm trong 10 phát biểu đã gửi ở C5 = ghi vào `RỦI RO ĐÃ BIẾT`, mức Trung (không phải lỗi — là thông tin để buổi chiều tự bắn trước).
4. Đếm và in: số `A-xx` / số `G-xx` / tổng, tỷ lệ luật có nguồn từ AI Khách hàng. **Với 5 câu hỏi, tỷ lệ `A` chỉ 20–40% là BÌNH THƯỜNG, không phải báo động** — ~70% ngân hàng câu hỏi không được hỏi (knowledge/30 §1b), phần còn lại điền bằng mặc định ngành là đúng quy tắc. Tỷ lệ thấp chỉ thành lỗi ở đúng một ca: **dòng `A-xx` chưa dùng hết** (mục 2 trên).

Không có RTM → ghi "bỏ khối C: không có RTM" và đánh dấu mọi luật có con số/quyền là "cần đối chiếu log".

**D. EVAL SET — HAI Executor mù ĐỘC LẬP, đo bằng BẤT ĐỒNG**

1. Sinh n tình huống theo quy tắc knowledge/50 §2 (ép kết quả cụ thể, thời điểm tuyệt đối, ≤60 từ). **Phân bổ bắt buộc:**
   - **≥ 1/3 số tình huống lấy từ probe P43–P48 và P49–P58** (knowledge/50 §5): P43/P43b message nguyên văn · P44/P44b guest · P45/P45b trạng thái nút & sau-thao-tác-hiển-thị-gì · P46–P48 ba ca 7.2 · P49/P50 hoàn tiền thất bại & hai mốc hoàn tiền · P51/P52 khóa tài nguyên trước khi thu tiền & giữ chỗ miễn phí lặp lại · P53 vượt hạn mức bằng danh tính mới · P54–P57 callback trùng / sai thứ tự / job ngừng chạy / ghi giữ thất bại · P58 nghiệp vụ brief nhắc bị đẩy ra ngoài.
   - Phần còn lại phủ loại **#1, #2, #4, #6, #7, #8, #11, #12, #15**.
   - Ưu tiên: **ô KHL / ô trống bảng trạng thái**, **biên TTL/qty**, **dòng ⚠ RTM**, **ca M6 còn ✗** (từ G3), và **mọi hit mức Cao ở A / B / G**.
   Ghi vào `<thư-mục>/eval-set.md`.
2. Với TỪNG tình huống gọi **HAI** Agent `subagent_type: executor` song song, prompt CHỈ gồm hai dòng và **giống nhau từng chữ**:
   ```
   Đường dẫn spec: <đường dẫn tuyệt đối BẢN NỘP spec.nop.md — đó là file Executor của BTC sẽ đọc>
   Tình huống: <văn bản tình huống>
   ```
   **Reader 1** dùng model mặc định của agent. **Reader 2 gọi với `model: haiku`** — model Executor của BTC là thông tin bảo mật, BTC không công bố, nên một reader yếu hơn là **giả lập bảo toàn** duy nhất đúng: spec nào chỉ rõ với reader mạnh mà mờ với reader yếu thì vẫn là spec mờ. Không kèm RTM, brief, đáp án kỳ vọng, hay bất kỳ bối cảnh khác. Gọi nhiều cặp Agent trong cùng một lượt.
3. **Phân loại theo BẤT ĐỒNG giữa hai reader, KHÔNG theo tự khai:**
   - Hai reader ra **kết quả quan sát được khác nhau** (trạng thái cuối / con số / hướng tiền / message / ai thắng) ⇒ **ĐA NGHĨA thật, mức Cao**. Đây là tiêu chí chính.
   - Hai reader **trùng nhau** nhưng `ĐỘ PHỦ ≠ ĐỦ` ⇒ lỗ hổng im lặng (Cao nếu ĐỘ PHỦ = KHÔNG hoặc tình huống chạm dòng ⚠ RTM; Trung nếu MỘT PHẦN).
   - Hai reader trùng nhau, ĐỦ, nhưng câu trả lời **trái đáp án trong RTM/log** ⇒ spec viết trái specs thật, mức Cao (loại TRÚNG chắc nhất #15).
   - Hai reader trùng nhau, ĐỦ, khớp RTM ⇒ ✓.
   **Ô `ĐA NGHĨA = KHÔNG` do chính reader tự khai KHÔNG được dùng làm bằng chứng spec rõ** — reader vừa đọc spec vừa tự chấm nó rõ là **vòng tự xác nhận**: bản diễn tập 08/09 ra 15/15 "ĐỦ, KHÔNG mơ hồ" trong khi vẫn còn 7 ✗ cổng F mức Cao. Ghi cả hai TRẢ LỜI cạnh nhau để người viết thấy chỗ lệch, kèm mục spec cần vá.
4. Hết giờ: **giảm n xuống 8 nhưng GIỮ HAI READER**. Một reader trên 20 tình huống yếu hơn hai reader trên 8 — vì tiêu chí phát hiện đa nghĩa là bất đồng, không phải số lượng.

**G. KHẢ THI, MỤC TIÊU, LẠM DỤNG** *(khối nội dung — phần mà lint hình thức không nhìn thấy; 8 kiểm)*

- **G1. Cổng F** (knowledge/33 §1): chạy 6 lệnh grep ở cuối §1, rồi chấm **F1–F8** cho từng BR/EX và từng dòng catch-all 1.x. Đây là nơi **phán quyết** mọi hit nghi vấn của nhóm lint 17–20 ở khối A. Bảng: `BR | F1..F8 | ✗ nào | mức | cách xử lý (viết lại theo 33 §3 / hỏi theo 33 §6)`. ✗ ở **F1, F2, F5, F7 = mức Cao, chặn nộp**; ✗ ở F3, F4, F6, F8 = Trung. Đối chiếu mọi giả định về phụ thuộc ngoài với **bảng "KHÔNG được hứa" (33 §2)** — câu nào nằm ở cột đó là câu chặn nộp.
- **G2. Bảng Mục tiêu↔Luật** (S31/S32): đọc `mo-hinh-bai-toan.md` khối M1; nếu chưa có bảng thì lập tại đây theo knowledge/05 §2. Kết luận ba dòng: (a) **mục tiêu brief nào không có luật phục vụ** (cột 2 trống) = S31, **Cao**; (b) **luật nào làm hỏng mục tiêu** (cột 3 có chữ) = S32, **Cao** — **xử lý bằng CÂU HỎI verify, KHÔNG bằng viết lại**: không ai thiết kế tính năng tự phá mục tiêu của nó, nên specs thật gần chắc có luật thứ ba mà đội chưa biết; (c) BR không thuộc cột 2 lẫn cột 3 của mục tiêu nào ⇒ đội tự nghĩ ra ⇒ ứng viên cắt token + ứng viên G7.
- **G3. Độ phủ 12 ca suy biến** (S33): 12 ca knowledge/05 §M6 × spec → ✓ có luật bao trùm / ⛔ nằm trong NGOÀI phạm vi **tường minh** / ✗ hở. **Mỗi ✗ có tiền dính vào = mức Cao VÀ PHẢI có một tình huống eval tương ứng ở khối D.** Nhắc: bốn luật §0.11–0.14 phủ 8/12 ca với ~90 từ (33 §3.2) — đây là tỷ lệ chắn/token tốt nhất trong kit.
- **G4. Độ phủ 6 láng giềng** (nhóm lint 19): 6 láng giềng knowledge/05 §M4 (kênh khách · cổng thanh toán + callback · ERP/WMS · job hết hạn · kênh thông báo · bảng điều khiển CSKH) × spec → **có luật cho chế độ lỗi của láng giềng đó hay không**. Nhắc mà không có luật lỗi = lỗi **Cao** loại #22. Mẫu một luật: 33 §3.2.
- **G5. Bảng lạm dụng 6 kẻ**: 6 kẻ knowledge/05 §M5 (bot gom hàng · kẻ đổi danh tính · kẻ hủy chuỗi · kẻ săn giá · người trong CSKH · khách vô ý) × spec → luật nào chặn, lách được không. **Với mỗi kẻ viết đúng MỘT câu "kẻ này làm gì với spec hiện tại"** — nếu câu đó mô tả một lợi thế đạt được mà **không vi phạm luật nào**, đó là lỗi **Cao** loại #20/#21. Luật cứng: mỗi hạn mức phải trả lời được "neo vào cái gì, khách có tự đổi được cái đó không?".
- **G6. Chi phí ẩn** (knowledge/33 §4): 6 khoản (phí cổng trên khoản hoàn · đối soát thủ công · giờ CSKH · tồn bị khóa không sinh doanh thu · thông báo · lưu trữ audit) → spec có nói **ai chịu**, có **trần**, hay đã đẩy ra NGOÀI phạm vi tường minh? Thiếu = mức **Trung**. Nhận hit nhóm lint 21 từ khối A.
- **G7. GIÁ TRỊ TỰ NGHĨ RA** *(đi cùng knowledge/30 §1b)*: với mỗi BR nguồn `G-xx` có con số hoặc lựa chọn nhị phân, đối chiếu **31 phát biểu knowledge/20 §4** + catalogue ⚠ knowledge/10 §6. Ba kết quả:
  - **Trùng mặc định ngành ⇒ ✓**, không phải rủi ro — Executor mù cũng đoán như vậy, viết ra chỉ loại thêm đa nghĩa ở các ca ghép.
  - **Ngược mặc định ngành mà KHÔNG có dòng `A-xx` đỡ ⇒ lỗi Cao.** Đây là **ca duy nhất tệ hơn im lặng**: im lặng thì Executor đoán mặc định ngành (TRƯỢT nếu specs thật = mặc định), còn một giá trị tự nghĩ ra biến một TRƯỢT tiềm năng thành TRÚNG gần chắc chắn. Xử lý: **đổi về mặc định ngành — đây là ngoại lệ DUY NHẤT được đổi con số nghiệp vụ, loại `SỬA`**; hoặc nếu còn câu hỏi thì đưa vào C5.
  - **Không có mặc định ngành nào cho ô đó ⇒ mức Trung**, ghi vào `RỦI RO ĐÃ BIẾT`.
- **G8. Markdown & sơ đồ**: bản nộp **không có ảnh** (`grep -n '!\['`), không có link ngoài (S12); nếu **có khối ```mermaid thì §0.15 PHẢI tồn tại** (sơ đồ chỉ minh họa; bảng và luật có mã thắng) — thiếu = lỗi **Cao** loại #5, vì mỗi sơ đồ khi đó là một mâu thuẫn nội tại và Executor có hai cách đọc hợp lệ. Sơ đồ lặp lại nội dung bảng = mức Trung, ứng viên cắt token (thứ tự cắt: wireframe → sơ đồ hệ thống → sequence → state machine, knowledge/30 §5).

**E. ĐẾM TOKEN** — đếm trên **BẢN NỘP** `spec.nop.md` (không phải `spec.md` nội bộ, dài hơn ~500 token vì mang `← A-xx` và nhãn `[M-x]`):
```
LC_ALL=C.UTF-8 wc -w -m <thư-mục>/spec.nop.md
token ≈ max( số_từ × 2,5 ; số_ký_tự / 2,2 )
```
In cả hai con số và giá trị lấy (max). **Không** dùng `wc` thiếu `LC_ALL=C.UTF-8` — locale `C` đếm sai ký tự đa byte. **> 6.000 token = lỗi Cao** (chặn nộp); **> 5.400 token = cảnh báo "hết đệm"** (5.400 là đích, knowledge/30 §1) kèm danh sách ứng viên cắt: BR không thuộc mục tiêu nào (G2c) → hit nhóm lint 22 → mục 10 → mục 9 → mục 5 → mục 3 → mục 2; **KHÔNG cắt mục 1, 6, 7, 4, 8 và KHÔNG cắt 0.11–0.14**. Nếu giao diện nộp của BTC hiển thị token thật → lấy số đó và ghi hệ số `token thật / số từ` vào báo cáo. Thiếu `spec.nop.md` → lỗi Cao "chưa sinh bản nộp", đếm tạm trên `spec.md` kèm ghi chú.

**F. CỔNG CHẤT LƯỢNG** — **23 dòng** knowledge/30 §7 (**dòng 1–18 hình thức và cấu trúc 10 mục; dòng 19–23 nội dung**), đánh ✓/✗ với **bằng chứng 1 dòng** cho từng dòng. Không được để dòng nào trống. Dòng 16 (hai reader không lệch) lấy kết quả từ khối D; dòng 19–23 lấy từ khối G; dòng 18 lấy từ khối E.

## Ba loại phát hiện — `SỬA` / `HỎI` / `RỦI RO ĐÃ BIẾT`

Nguyên tắc chi phối cả báo cáo: **"sửa hộ là đoán lần thứ hai trên cùng một chỗ mù."** Chỗ nào đội không biết specs thật quy định gì thì viết lại chỉ đổi một phán đoán sai thành một phán đoán sai khác, tự tin hơn.

| Nhãn | Khi nào | Xuất ra thế nào |
|---|---|---|
| **`SỬA`** | Sửa được ngay bằng viết lại, không cần biết thêm gì: mơ hồ nhóm 1–16, ô trống bảng, S13/S17/S25–S27, ✗ F1, ✗ F5, thiếu 0.11–0.15, thiếu câu chữ dưới mermaid, **và G7 giá trị ngược mặc định ngành** (đổi về mặc định ngành là sửa, không phải đoán) | Câu dán được vào spec |
| **`HỎI`** | Đội đang **không biết** specs thật quy định gì **và còn câu hỏi**: ✗ F2, ✗ F7, S31, S32, dòng `G-xx` rủi ro Cao | **Phát biểu Đúng/Sai** cho câu **C5** (mẫu knowledge/20 §4, chuyển hit theo 33 §6) — **KHÔNG phải câu hỏi mở**, vì chỉ còn một câu và nó là C5 |
| **`RỦI RO ĐÃ BIẾT`** | Đúng loại `HỎI` nhưng **hết câu hỏi**, hoặc không lọt vào 10 phát biểu của C5 | Không sửa, không hỏi; ghi kèm tình huống mà đối thủ sẽ dùng để bắn |

**Cắt danh sách `HỎI` theo số câu hỏi còn lại** (đọc sổ hạn mức ở đầu `log-khach-hang.md` TRƯỚC khi cắt): còn 1 câu ⇒ **tối đa 10 phát biểu**, xếp theo rủi ro giảm dần, tất cả vào C5; còn 0 câu ⇒ **0 phát biểu**, toàn bộ xuống `RỦI RO ĐÃ BIẾT`. Phần dư luôn xuống `RỦI RO ĐÃ BIẾT`, không bao giờ bị bỏ im lặng.

**Ngoại lệ DUY NHẤT được đổi con số nghiệp vụ: G7 ngược mặc định ngành** — đổi về mặc định ngành, ghi rõ trong diff. Mọi con số khác: số có trong RTM/log → ghi số + `(A-xx)`; không có → để `[..]` + "xác nhận với AI Khách hàng"; luật đang có trong spec chưa đối chiếu được với log → giữ nguyên nội dung, chỉ sửa diễn đạt, gắn "cần đối chiếu log".

## Báo cáo `review.md` (cùng thư mục với spec)

1. **Kết luận đầu file: NỘP ĐƯỢC hoặc CHƯA.** **CHƯA** khi bất kỳ điều nào dưới đây đúng:
   - còn ≥ 1 lỗi mức **Cao**;
   - **> 6.000 token** ở bản nộp;
   - có dòng RTM (`A-xx` hoặc `G-xx`) **chưa có mã BR**;
   - **cổng chất lượng 23 dòng còn ✗**;
   - **thiếu một mục trong 10 mục BTC** (hoặc mục rỗng);
   - **còn ✗ ở F1 hoặc F5**;
   - **có mục tiêu brief không có luật phục vụ** (S31);
   - **có tình huống eval mà HAI READER ra kết quả khác nhau**;
   - **G7: có BR mang giá trị ngược mặc định ngành mà không có `A-xx` đỡ**.
2. **Bảng "10 mục BTC: có / thiếu / rỗng"** (từ B0) — đặt ngay sau kết luận.
3. **Bảng lỗ hổng** theo định dạng knowledge/40 §4, sắp theo Mức giảm dần rồi theo thang §5 (**gồm bậc 0 và 2b–2f**). Cột bắt buộc: `Vị trí | Trích | Loại (nhóm lint / Sxx / Fx / #xx) | Mức | Nhãn SỬA/HỎI/RỦI RO | Viết lại đề xuất hoặc phát biểu C5`. Gộp hit trùng câu thành một dòng.
4. **Bảng tick 20 nhóm N0–N19** trên spec mình (từ B11).
5. **Bảng eval set**: `tình huống | probe/loại # | ĐỘ PHỦ | TRẢ LỜI reader 1 | TRẢ LỜI reader 2 (haiku) | LỆCH? | đối chiếu RTM | mục cần vá`.
6. **Khối G — 8 bảng**: cổng F (F1–F8 × BR) · Mục tiêu↔Luật · 12 ca suy biến ✓/⛔/✗ · 6 láng giềng · 6 kẻ lạm dụng (mỗi kẻ một câu) · 6 chi phí ẩn · G7 giá trị tự nghĩ ra · G8 markdown & sơ đồ.
7. **Cổng chất lượng 23 dòng** ✓/✗ kèm bằng chứng. **Token bản nộp (cả hai công thức + max) và đệm còn lại dưới 6.000 / dưới đích 5.400.**
8. **"Sửa trong 10 phút"** — 5 việc đầu theo thang knowledge/40 §5, và **"10 phát biểu cho câu C5"** viết sẵn dạng Đúng/Sai, xếp theo rủi ro, dán được vào `/elicit restate` (cắt theo số câu còn lại).
9. **`RỦI RO ĐÃ BIẾT`** — bảng: `giả định | mặc định ngành hay ngược | tình huống đối thủ sẽ dùng | mức`. Đây là đầu vào cho `/attack` tự bắn spec mình lúc 12:00–13:00.

Nếu args có `sửa`: áp các câu viết lại mức Cao **thuộc loại `SỬA`** vào spec bằng Edit, chạy lại A, B, E, G1, G7, in diff token. **Không** áp mục loại `HỎI`. Không sửa nội dung nghiệp vụ (không đổi con số/trạng thái) **trừ ca G7** — chỉ sửa diễn đạt, điền ô trống bằng "Từ chối 0.5", thêm luật cho chế độ lỗi của láng giềng (33 §3.2, không cần số mới), tách mốc quyết định/hoàn tất (33 §3.1, để `[..]` cho số chưa biết), thêm 0.11–0.15 nếu thiếu. **Mục thiếu hẳn (S22) thì KHÔNG tự bịa nội dung** — chỉ tạo heading + bảng rỗng có cột đúng và ghi một dòng cảnh báo trong `review.md` rằng mục này cần người viết điền từ RTM.

## Output bắt buộc
- [ ] `review.md` với kết luận NỘP ĐƯỢC/CHƯA và đủ 9 tiêu chí CHƯA.
- [ ] Bảng "10 mục BTC: có / thiếu / rỗng" ở đầu báo cáo (khối B0).
- [ ] Mỗi phát hiện có vị trí, trích, loại, mức, **nhãn `SỬA`/`HỎI`/`RỦI RO ĐÃ BIẾT`**, viết lại hoặc phát biểu Đúng/Sai.
- [ ] Eval set n tình huống đã chạy qua **HAI** executor mù độc lập (reader 2 = `model: haiku`), cả hai câu trả lời được ghi, cột LỆCH? đã điền.
- [ ] Khối G đầy đủ 8 bảng.
- [ ] Cổng chất lượng **23 dòng** ✓/✗ có bằng chứng.
- [ ] Danh sách `HỎI` dán được vào `/elicit restate`, đã cắt theo số câu còn lại.
- [ ] `eval-set.md` lưu lại (tái dùng cho `/attack` tự bắn spec mình).

## Không được
- Bỏ khối **B0**, hoặc chạy khối khác trước B0 — thiếu một mục là cả vùng trống, mọi kiểm khác đều vô nghĩa trên vùng đó.
- Đưa RTM/brief/đáp án vào prompt gọi executor; gửi hai reader hai prompt khác nhau; gọi reader 2 bằng model mặc định (phải là `model: haiku`).
- **Dùng ô `ĐA NGHĨA = KHÔNG` do chính reader tự khai làm bằng chứng spec rõ** — bằng chứng duy nhất là hai reader trùng kết quả.
- Kết luận mức cho hit nhóm lint 17–20 ngay tại khối A — phán quyết thuộc cổng F ở khối G1.
- Sửa spec khi không có `sửa`; đổi con số nghiệp vụ khi `sửa` **trừ** ca G7; áp mục loại `HỎI` vào spec.
- Sinh danh sách `HỎI` dài hơn số câu hỏi còn lại cho phép; sinh `HỎI` dạng câu hỏi mở thay vì phát biểu Đúng/Sai.
- Báo động vì tỷ lệ `A-xx` thấp (20–40% là bình thường với 5 câu hỏi) — chỉ báo động khi **dòng `A-xx` chưa dùng hết**.
- Kết luận NỘP ĐƯỢC khi còn lỗi Cao, còn ✗ F1/F5, còn mục tiêu brief không có luật, hay hai reader còn lệch.
- Bỏ khối D hoặc bỏ reader thứ hai vì "hết thời gian" — giảm n xuống 8 và **giữ hai reader**.
- Bỏ khối G vì "spec đã sạch lint" — đó chính là trạng thái mà khối G được viết ra để soi (một spec đạt mọi cổng hình thức vẫn ra 7 ✗ cổng F, knowledge/33 §7).
