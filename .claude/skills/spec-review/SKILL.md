---
name: spec-review
description: Red team spec của đội trước khi nộp — kiểm đủ 10 mục BTC, lint từ mơ hồ và cấu trúc, kiểm ô trống bảng Case/trạng thái, kiểm message nguyên văn và phân biệt guest, kiểm phủ RTM, chạy eval set qua agent executor mù, cổng chất lượng, kết luận NỘP ĐƯỢC/CHƯA. Dùng 11:45–11:55 ngày thi hoặc trong diễn tập khi người dùng nói "review spec", "soi spec mình", "red team", "kiểm tra trước khi nộp", "spec-review".
argument-hint: "[đường-dẫn-spec] [n=20] [sửa]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(wc *), Agent
---
# /spec-review — Red team spec của mình

Mục tiêu: tìm mọi chỗ Executor có thể trả lời khác specs thật, xếp theo mức, kèm câu viết lại dán được. Không sửa spec trừ khi args có `sửa`.

## Input
`$ARGUMENTS` = `[đường-dẫn-spec] [n] [sửa]`. Mặc định spec `battle/spec.md`, n = 20 tình huống eval. `rtm.md` cùng thư mục nếu có.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` §1 (10 mục BTC bắt buộc), §2 ("ba thứ" + 5 gạch của mỗi logic ở mục 6), §5–§6 (ngân sách, thứ tự cắt), §7 (Mermaid — sơ đồ không thay bảng).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md`: §2 danh sách đen phẳng (16 nhóm regex), §3 kiểm tra cấu trúc S1–S30 (S22–S30 = kiểm cấu trúc 10 mục), §4 định dạng báo cáo, §5 thang ưu tiên sửa.
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §2.2–§2.3 (đếm tổ hợp, soi gap/overlap), §3 (mọi ô state × event), §4 (mức phủ).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (18 loại lỗ hổng — để sinh eval set; #16 message, #17 guest, #18 trạng thái UI là loại mới) và §2 (quy tắc viết tình huống).
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md` §7 (cổng chất lượng 18 dòng).

## Bước
**A. LINT từ ngữ** — với từng nhóm 1–16 ở knowledge/40 §2: Grep `-i`, output_mode content, `-n`, pattern là regex của nhóm, path = spec. Mỗi hit là một dòng báo cáo (vị trí, trích, nhóm, mức theo §3/§5). Bỏ hit trong phần định nghĩa từ khóa hoặc trong ngoặc kép có chủ ý; nhóm 9 và 11 chỉ đánh lỗi khi câu chứa hit thiếu ký hiệu ≤/</[a,b) hoặc thiếu danh sách đánh số.

**B. HOÀN CHỈNH cấu trúc** — chạy S1–S30 (knowledge/40 §3).

**B0. Đủ 10 mục BTC (S22) — chạy TRƯỚC mọi kiểm khác.** Đối chiếu mục lục spec với knowledge/32 §1. Mục nào vắng = lỗi **Cao**, ghi rõ "thiếu mục n — cả vùng trống". Mục có heading nhưng không có bảng/nội dung = coi như vắng.

Rồi bắt buộc kiểm:
1. **Mục 6 — từng logic** (trọng tâm): mỗi logic có bảng step? có bảng Case? bảng Case đủ **3 loại case** (bình thường, biên, lỗi) — thiếu loại nào = lỗi Cao S25; đủ **5 gạch** (số · toán tử `>`/`≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng) — thiếu gạch nào = lỗi Cao S26, ghi tên gạch thiếu; cột Kết quả mong đợi đủ 7 chiều (trạng thái cuối, kho, tiền, thông báo, hiển thị, trạng thái nút, message).
2. **Mục 4** (S23, S24): Grep message — mọi dòng phải có chuỗi trong ngoặc kép; câu kiểu "hiển thị thông báo lỗi/báo lỗi phù hợp" = lỗi **Cao** S23. Thiếu cột FE/BE = S24 Trung.
3. **Mục 2 + 8** (S27): Grep `guest|Guest|chưa đăng nhập` — mục 2 thiếu cột phân biệt login/guest hoặc mục 8 thiếu dòng Guest = lỗi **Cao**. Mục 8 phải có System/Job và phủ thao tác chỉ-đọc nếu có catch-all "không ✓ = cấm". Mục 2 phải có điều kiện ẩn/disable.
4. **Mục 7** (S28): 7.2 có đủ 4 ca BTC nêu đích danh (dữ liệu đổi giữa hiển thị và submit · gửi trùng · mở link hai lần · mail fail sau khi đã lưu)? thiếu ca nào = lỗi Cao, ghi tên ca. 7.4 có gom bảng riêng điều chưa chốt? `[GIẢ ĐỊNH]`/"TBD" rải rác ngoài bảng 7.4 = S28 Trung.
5. Bảng state × event (trong logic 6.3): liệt kê mọi ô, ô trống/"—" không giải thích = lỗi Cao S13/S17. **Có sơ đồ Mermaid `stateDiagram-v2` mà KHÔNG có bảng = lỗi Cao** — sơ đồ chỉ vẽ chuyển hợp lệ, thiếu hẳn ô `Từ chối 0.5` / `KHL`, đúng chỗ Executor đoán sai.
5b. Mermaid: mỗi khối ```mermaid có nhãn tiếng Việt bọc ngoặc kép? có 1–2 câu chữ tóm tắt ngay dưới (phòng render lỗi)? Thiếu câu chữ = lỗi Trung.
6. Decision table: đếm tổ hợp = tích các phân hoạch (knowledge/31 §2.2), thiếu → gap, hai hàng `-` chéo cột không hit policy → overlap #8.
7. Grep `BR-\d+|EX-\d+`: mã trùng, tham chiếu chết S10.
8. Catch-all mục 1.x: có ưu tiên xung đột (0.6), timezone (0.2), [a,b) (0.3), tình huống không khớp luật nào (0.5)?
9. Mọi BR có tác động phụ K/T/N/L và câu KHÔNG ĐƯỢC (S14, S15).
10. Grep các mục hiếm: "đồng thời|cùng lúc", "safety|tồn đệm|an toàn|oversell", "guest|chưa đăng nhập", "múi giờ|UTC|ngày lễ", "rollback|hoàn nguyên|kiểm kê|lệch", "ưu tiên|ghi đè" — 0 hit = lỗ hổng #1 ứng viên, phải có tình huống eval chạm vào ở khối D.

**C. PHỦ & KHỚP RTM** — tìm `rtm.md` + `log-khach-hang.md` ở thư mục spec, rồi thư mục cha, hoặc đường dẫn người dùng chỉ. Có RTM: (1) dòng ⚠ có cột Mã BR trống hoặc mã không tồn tại trong spec = lỗi Cao; (2) với TỪNG dòng ⚠, so NỘI DUNG BR tương ứng với câu trả lời rút gọn/nguyên văn (con số, trạng thái, hướng tiền, ai được phép) — BR nói khác = lỗi Cao "trái specs thật" (loại TRÚNG chắc nhất, #15), ghi cả hai vế. Không có RTM → ghi "bỏ khối C: không có RTM" và đánh dấu mọi luật có con số/quyền là "cần đối chiếu log".

**D. EVAL SET (Executor mù)**
1. Sinh n tình huống theo quy tắc knowledge/50 §2 (ép kết quả cụ thể, thời điểm tuyệt đối, ≤60 từ), phủ các loại #1, #2, #4, #6, #7, #8, #9, #11, #12, #15 **và bắt buộc #16 (hỏi message nguyên văn), #17 (guest), #18 (trạng thái nút / màn hình hiển thị gì)** (knowledge/50 §1; probe P43–P48). Ưu tiên ô KHL của bảng trạng thái, biên TTL/qty, dòng ⚠ RTM, và mọi hit mức Cao ở A/B. Ghi vào `<thư-mục>/eval-set.md`.
2. Với TỪNG tình huống gọi Agent, `subagent_type: executor`, prompt CHỈ gồm hai dòng:
   ```
   Đường dẫn spec: <đường dẫn tuyệt đối spec>
   Tình huống: <văn bản tình huống>
   ```
   Không kèm RTM, brief, đáp án kỳ vọng, hay bất kỳ bối cảnh khác. Gọi song song nhiều Agent trong một lượt.
3. Phân loại: `ĐỘ PHỦ ≠ ĐỦ` hoặc `ĐA NGHĨA ≠ KHÔNG` ⇒ lỗ hổng (mức Cao nếu tình huống chạm dòng ⚠ RTM hoặc ĐỘ PHỦ = KHÔNG; Trung nếu MỘT PHẦN). Ghi kèm TRẢ LỜI của executor để người viết thấy Executor sẽ đoán gì, và mục spec cần vá.

**E. ĐẾM TỪ** — Bash `wc -w <spec>`; > 3.000 = lỗi Cao; > 2.850 = cảnh báo.

**F. CỔNG CHẤT LƯỢNG** — 18 dòng knowledge/30 §7, đánh ✓/✗ với bằng chứng 1 dòng.

## Báo cáo `review.md` (cùng thư mục với spec)
1. Kết luận đầu file: **NỘP ĐƯỢC** hoặc **CHƯA** — CHƯA khi còn ≥1 lỗi mức Cao, hoặc **thiếu một trong 10 mục BTC**, hoặc > 3.000 từ, hoặc dòng ⚠ chưa có BR, hoặc cổng chất lượng có ✗.
2. Bảng lỗ hổng theo định dạng knowledge/40 §4, sắp theo Mức giảm dần rồi theo thang §5; cột "Viết lại đề xuất" là câu dán được vào spec. Câu viết lại KHÔNG ĐƯỢC bịa con số/giá trị: số có trong RTM/log → ghi số + `(A-xx)`; không có → để `[..]` và ghi "xác nhận với AI Khách hàng"; luật hiện có trong spec chưa đối chiếu được với log → giữ nguyên nội dung, chỉ sửa diễn đạt, và gắn "cần đối chiếu log". Gộp hit trùng câu thành một dòng.
3. Bảng eval set: tình huống | loại # | ĐỘ PHỦ | ĐA NGHĨA | TRẢ LỜI executor (rút gọn) | mục cần vá.
4. Cổng chất lượng ✓/✗. Số từ.
5. "Sửa trong 10 phút" — 5 việc đầu theo thang knowledge/40 §5.

Nếu args có `sửa`: áp các câu viết lại mức Cao vào spec bằng Edit, chạy lại A, B, E, in diff số từ. Không sửa nội dung nghiệp vụ (không đổi con số/trạng thái) — chỉ sửa diễn đạt và điền ô trống bằng "Từ chối 0.5". **Mục thiếu hẳn (S22) thì KHÔNG tự bịa nội dung** — chỉ tạo heading + bảng rỗng có cột đúng và ghi một dòng cảnh báo trong review.md rằng mục này cần người viết điền từ RTM.

## Output bắt buộc
- [ ] `review.md` với kết luận NỘP ĐƯỢC/CHƯA và tiêu chí.
- [ ] Bảng "10 mục BTC: có / thiếu / rỗng" ở đầu báo cáo.
- [ ] Mỗi phát hiện có vị trí, trích, loại, mức, viết lại.
- [ ] Eval set n tình huống đã chạy qua executor mù, kết quả ghi đủ.
- [ ] `eval-set.md` lưu lại (tái dùng cho /attack chính spec mình).

## Không được
- Đưa RTM/brief/đáp án vào prompt gọi executor.
- Sửa spec khi không có `sửa`; đổi con số nghiệp vụ khi `sửa`.
- Kết luận NỘP ĐƯỢC khi còn lỗi Cao.
- Bỏ khối D vì "hết thời gian" — giảm n xuống 8 thay vì bỏ.
