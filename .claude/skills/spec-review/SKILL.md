---
name: spec-review
description: Red team spec của đội trước khi nộp — lint từ mơ hồ và cấu trúc, kiểm ô trống bảng trạng thái/decision table, kiểm phủ RTM, chạy eval set qua agent executor mù, cổng chất lượng, kết luận NỘP ĐƯỢC/CHƯA. Dùng 11:45–11:55 ngày thi hoặc trong diễn tập khi người dùng nói "review spec", "soi spec mình", "red team", "kiểm tra trước khi nộp", "spec-review".
argument-hint: "[đường-dẫn-spec] [n=20] [sửa]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(wc *), Bash(LC_ALL=C.UTF-8 wc *), Agent
---
# /spec-review — Red team spec của mình

Mục tiêu: tìm mọi chỗ Executor có thể trả lời khác specs thật, xếp theo mức, kèm câu viết lại dán được. Không sửa spec trừ khi args có `sửa`.

## Input
`$ARGUMENTS` = `[đường-dẫn-spec] [n] [sửa]`. Mặc định spec `battle/spec.md`, n = 20 tình huống eval. `rtm.md` cùng thư mục nếu có.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md`: §2 danh sách đen phẳng (16 nhóm regex), §3 kiểm tra cấu trúc S1–S21, §4 định dạng báo cáo, §5 thang ưu tiên sửa.
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §2.2–§2.3 (đếm tổ hợp, soi gap/overlap), §3 (mọi ô state × event), §4 (mức phủ).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (15 loại lỗ hổng — để sinh eval set) và §2 (quy tắc viết tình huống).
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md` §7 (cổng chất lượng 11 dòng).

## Bước
**A. LINT từ ngữ** — với từng nhóm 1–16 ở knowledge/40 §2: Grep `-i`, output_mode content, `-n`, pattern là regex của nhóm, path = spec. Mỗi hit là một dòng báo cáo (vị trí, trích, nhóm, mức theo §3/§5). Bỏ hit trong phần định nghĩa từ khóa hoặc trong ngoặc kép có chủ ý; nhóm 9 và 11 chỉ đánh lỗi khi câu chứa hit thiếu ký hiệu ≤/</[a,b) hoặc thiếu danh sách đánh số.

**B. HOÀN CHỈNH cấu trúc** — chạy S1–S21 (knowledge/40 §3). Bắt buộc kiểm: (1) bảng state × event: liệt kê mọi ô, ô trống/"—" không giải thích = lỗi Cao S13/S17; (2) decision table: đếm tổ hợp = tích các phân hoạch (knowledge/31 §2.2), thiếu → gap, hai hàng `-` chéo cột không hit policy → overlap #8; (3) Grep `BR-\d+|EX-\d+`: mã trùng, tham chiếu chết S10; (4) bảng actor có Guest và System/Job, và phủ thao tác chỉ-đọc nếu có catch-all "không ✓ = cấm"; (5) có mục ưu tiên xung đột (0.6), timezone (0.2), [a,b) (0.3), catch-all (0.5); (6) mọi BR có tác động phụ K/T/N/L và câu KHÔNG ĐƯỢC (S14, S15); (7) Grep các mục hiếm: "đồng thời|cùng lúc", "safety|tồn đệm|an toàn|oversell", "guest|chưa đăng nhập", "múi giờ|UTC|ngày lễ", "rollback|hoàn nguyên|kiểm kê|lệch", "ưu tiên|ghi đè" — 0 hit = lỗ hổng #1 ứng viên, phải có tình huống eval chạm vào ở khối D.

**C. PHỦ & KHỚP RTM** — tìm `rtm.md` + `log-khach-hang.md` ở thư mục spec, rồi thư mục cha, hoặc đường dẫn người dùng chỉ. Có RTM: (1) dòng ⚠ có cột Mã BR trống hoặc mã không tồn tại trong spec = lỗi Cao; (2) với TỪNG dòng ⚠, so NỘI DUNG BR tương ứng với câu trả lời rút gọn/nguyên văn (con số, trạng thái, hướng tiền, ai được phép) — BR nói khác = lỗi Cao "trái specs thật" (loại TRÚNG chắc nhất, #15), ghi cả hai vế. Không có RTM → ghi "bỏ khối C: không có RTM" và đánh dấu mọi luật có con số/quyền là "cần đối chiếu log".

**D. EVAL SET (Executor mù)**
1. Sinh n tình huống theo quy tắc knowledge/50 §2 (ép kết quả cụ thể, thời điểm tuyệt đối, ≤60 từ), phủ các loại #1, #2, #4, #6, #7, #8, #9, #11, #12, #15 (knowledge/50 §1), ưu tiên ô KHL của bảng trạng thái, biên TTL/qty, dòng ⚠ RTM, và mọi hit mức Cao ở A/B. Ghi vào `<thư-mục>/eval-set.md`.
2. Với TỪNG tình huống gọi Agent, `subagent_type: executor`, prompt CHỈ gồm hai dòng:
   ```
   Đường dẫn spec: <đường dẫn tuyệt đối BẢN NỘP spec.nop.md — đó là file Executor của BTC sẽ đọc>
   Tình huống: <văn bản tình huống>
   ```
   Không kèm RTM, brief, đáp án kỳ vọng, hay bất kỳ bối cảnh khác. Gọi song song nhiều Agent trong một lượt.
3. Phân loại: `ĐỘ PHỦ ≠ ĐỦ` hoặc `ĐA NGHĨA ≠ KHÔNG` ⇒ lỗ hổng (mức Cao nếu tình huống chạm dòng ⚠ RTM hoặc ĐỘ PHỦ = KHÔNG; Trung nếu MỘT PHẦN). Ghi kèm TRẢ LỜI của executor để người viết thấy Executor sẽ đoán gì, và mục spec cần vá.

**E. ĐẾM TỪ** — đếm trên **bản nộp** `spec.nop.md` (không phải `spec.md` nội bộ, dài hơn ~200 từ vì mang `← A-xx`): `LC_ALL=C.UTF-8 wc -w <thư-mục>/spec.nop.md`. **Không** dùng `wc -w` trần — locale `C` đếm sai ký tự đa byte và báo thừa ~80 từ. > 3.000 = lỗi Cao; > 2.850 = cảnh báo. Nếu thiếu `spec.nop.md` → ghi lỗi Cao "chưa sinh bản nộp" và đếm tạm trên `spec.md` kèm ghi chú.

**F. CỔNG CHẤT LƯỢNG** — 11 dòng knowledge/30 §7, đánh ✓/✗ với bằng chứng 1 dòng.

## Báo cáo `review.md` (cùng thư mục với spec)
1. Kết luận đầu file: **NỘP ĐƯỢC** hoặc **CHƯA** — CHƯA khi còn ≥1 lỗi mức Cao, hoặc > 3.000 từ, hoặc dòng ⚠ chưa có BR, hoặc cổng chất lượng có ✗.
2. Bảng lỗ hổng theo định dạng knowledge/40 §4, sắp theo Mức giảm dần rồi theo thang §5; cột "Viết lại đề xuất" là câu dán được vào spec. Câu viết lại KHÔNG ĐƯỢC bịa con số/giá trị: số có trong RTM/log → ghi số + `(A-xx)`; không có → để `[..]` và ghi "xác nhận với AI Khách hàng"; luật hiện có trong spec chưa đối chiếu được với log → giữ nguyên nội dung, chỉ sửa diễn đạt, và gắn "cần đối chiếu log". Gộp hit trùng câu thành một dòng.
3. Bảng eval set: tình huống | loại # | ĐỘ PHỦ | ĐA NGHĨA | TRẢ LỜI executor (rút gọn) | mục cần vá.
4. Cổng chất lượng ✓/✗. Số từ.
5. "Sửa trong 10 phút" — 5 việc đầu theo thang knowledge/40 §5.

Nếu args có `sửa`: áp các câu viết lại mức Cao vào spec bằng Edit, chạy lại A, B, E, in diff số từ. Không sửa nội dung nghiệp vụ (không đổi con số/trạng thái) — chỉ sửa diễn đạt và điền ô trống bằng "Từ chối 0.5".

## Output bắt buộc
- [ ] `review.md` với kết luận NỘP ĐƯỢC/CHƯA và tiêu chí.
- [ ] Mỗi phát hiện có vị trí, trích, loại, mức, viết lại.
- [ ] Eval set n tình huống đã chạy qua executor mù, kết quả ghi đủ.
- [ ] `eval-set.md` lưu lại (tái dùng cho /attack chính spec mình).

## Không được
- Đưa RTM/brief/đáp án vào prompt gọi executor.
- Sửa spec khi không có `sửa`; đổi con số nghiệp vụ khi `sửa`.
- Kết luận NỘP ĐƯỢC khi còn lỗi Cao.
- Bỏ khối D vì "hết thời gian" — giảm n xuống 8 thay vì bỏ.
