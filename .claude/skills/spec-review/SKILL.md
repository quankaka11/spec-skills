---
name: spec-review
description: Red team spec của đội trước khi nộp — lint từ mơ hồ và nội dung, kiểm cấu trúc và ô trống bảng, kiểm phủ RTM, cổng khả thi F + bảng Mục tiêu↔Luật + mô hình lạm dụng, eval set qua HAI agent executor mù độc lập (đo đa nghĩa bằng bất đồng), cổng chất lượng 16 dòng, kết luận NỘP ĐƯỢC/CHƯA kèm danh sách SỬA và HỎI. Dùng 11:45–11:55 ngày thi hoặc trong diễn tập khi người dùng nói "review spec", "soi spec mình", "red team", "kiểm tra trước khi nộp", "spec-review".
argument-hint: "[đường-dẫn-spec] [n=20] [sửa]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(wc *), Bash(LC_ALL=C.UTF-8 wc *), Agent
---
# /spec-review — Red team spec của mình

Mục tiêu: tìm mọi chỗ Executor có thể trả lời khác specs thật, xếp theo mức, kèm câu viết lại dán được. Không sửa spec trừ khi args có `sửa`.

## Input
`$ARGUMENTS` = `[đường-dẫn-spec] [n] [sửa]`. Mặc định spec `battle/spec.md`, n = 20 tình huống eval. `rtm.md` cùng thư mục nếu có.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md`: §2 danh sách đen phẳng (**22 nhóm** — 1–16 mơ hồ, 17–22 nội dung), §3 kiểm tra cấu trúc **S1–S30**, §4 định dạng báo cáo, §5 thang ưu tiên sửa.
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md`: §1 cổng F 8 kiểm tra, §2 bảng thực tế phụ thuộc ngoài, §3 mẫu viết lại, §4 chi phí ẩn, §5 cổng chất lượng dòng 12–16, §6 chuyển hit F thành câu hỏi.
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md`: §2 bảng Mục tiêu↔Luật, §M4 6 láng giềng, §M5 6 kẻ lạm dụng, §M6 12 kịch bản suy biến.
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §2.2–§2.3 (đếm tổ hợp, soi gap/overlap), §3 (mọi ô state × event), §4 (mức phủ).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (**20 loại** lỗ hổng — để sinh eval set), §2 (quy tắc viết tình huống), §5 probe P1–P52.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §2 tên 14 nhóm N1–N14 (để tick heading spec MÌNH ở khối B).
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md` §7 (cổng chất lượng **16 dòng**).

## Bước
**A. LINT từ ngữ + nội dung** — với từng nhóm 1–22 ở knowledge/40 §2: Grep `-i`, output_mode content, `-n`, pattern là regex của nhóm, path = spec. Mỗi hit là một dòng báo cáo (vị trí, trích, nhóm, mức theo §3/§5). Bỏ hit trong phần định nghĩa từ khóa hoặc trong ngoặc kép có chủ ý; nhóm 9 và 11 chỉ đánh lỗi khi câu chứa hit thiếu ký hiệu ≤/</[a,b) hoặc thiếu danh sách đánh số. **Nhóm 17–22 chạy trên bản nộp** và mỗi hit là *nghi vấn*, phán quyết thuộc khối G — không kết luận mức ngay tại đây.

**B. HOÀN CHỈNH cấu trúc** — chạy S1–S30 (knowledge/40 §3). Bắt buộc kiểm: (1) bảng state × event: liệt kê mọi ô, ô trống/"—" không giải thích = lỗi Cao S13/S17; (2) decision table: đếm tổ hợp = tích các phân hoạch (knowledge/31 §2.2), thiếu → gap, hai hàng `-` chéo cột không hit policy → overlap #8; (3) Grep `BR-\d+|EX-\d+`: mã trùng, tham chiếu chết S10; (4) bảng actor có Guest và System/Job, và phủ thao tác chỉ-đọc nếu có catch-all "không ✓ = cấm"; (5) có mục ưu tiên xung đột (0.6), timezone (0.2), [a,b) (0.3), catch-all (0.5), **và 0.11–0.14** (khử trùng theo mã giao dịch cổng, tách mốc quyết định/hoàn tất, neo định danh, thất bại thông báo); (6) mọi BR có tác động phụ K/T/N/L và câu KHÔNG ĐƯỢC (S14, S15); (7) Grep các mục hiếm: "đồng thời|cùng lúc", "safety|tồn đệm|an toàn|oversell", "guest|chưa đăng nhập", "múi giờ|UTC|ngày lễ", "rollback|hoàn nguyên|kiểm kê|lệch", "ưu tiên|ghi đè" — 0 hit = lỗ hổng #1 ứng viên, phải có tình huống eval chạm vào ở khối D; (8) **tick 14 nhóm N1–N14 trên mục lục spec MÌNH** (tên nhóm ở knowledge/20 §2) — đúng thao tác `/attack` bước 1 làm với spec đối thủ. Nhóm vắng trên spec mình = chỗ đối thủ sẽ chấm hạng A. Kit cũ chỉ tick cho spec đối thủ, nên spec mình không bao giờ bị soi bằng con mắt đó.

**C. PHỦ & KHỚP RTM** — tìm `rtm.md` + `log-khach-hang.md` ở thư mục spec, rồi thư mục cha, hoặc đường dẫn người dùng chỉ. Có RTM: (1) dòng ⚠ có cột Mã BR trống hoặc mã không tồn tại trong spec = lỗi Cao; (2) với TỪNG dòng ⚠, so NỘI DUNG BR tương ứng với câu trả lời rút gọn/nguyên văn (con số, trạng thái, hướng tiền, ai được phép) — BR nói khác = lỗi Cao "trái specs thật" (loại TRÚNG chắc nhất, #15), ghi cả hai vế. Không có RTM → ghi "bỏ khối C: không có RTM" và đánh dấu mọi luật có con số/quyền là "cần đối chiếu log".

**D. EVAL SET — hai Executor mù ĐỘC LẬP, đo bằng bất đồng**
1. Sinh n tình huống theo quy tắc knowledge/50 §2 (ép kết quả cụ thể, thời điểm tuyệt đối, ≤60 từ). Phân bổ bắt buộc: **≥ 1/3 số tình huống lấy từ probe P43–P52** (loại #16–#20: hoàn tiền thất bại, hai mốc hoàn tiền, callback trùng/sai thứ tự, job ngừng chạy, ghi giữ thất bại, vượt hạn mức bằng danh tính mới, giữ chỗ miễn phí lặp lại); phần còn lại phủ #1, #2, #4, #6, #7, #8, #11, #12, #15, ưu tiên ô KHL của bảng trạng thái, biên TTL/qty, dòng ⚠ RTM, ca M6 còn ✗, và mọi hit mức Cao ở A/B/G. Ghi vào `<thư-mục>/eval-set.md`.
2. Với TỪNG tình huống gọi **hai** Agent `subagent_type: executor` song song, prompt CHỈ gồm hai dòng và **giống nhau từng chữ**:
   ```
   Đường dẫn spec: <đường dẫn tuyệt đối BẢN NỘP spec.nop.md — đó là file Executor của BTC sẽ đọc>
   Tình huống: <văn bản tình huống>
   ```
   Reader 1 dùng model mặc định của agent. **Reader 2 gọi với `model: haiku`** — Executor của BTC chưa biết là model nào (00 §H câu 7), và một reader yếu hơn là giả lập bảo toàn. Không kèm RTM, brief, đáp án kỳ vọng, hay bất kỳ bối cảnh khác.
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

**E. ĐẾM TỪ** — đếm trên **bản nộp** `spec.nop.md` (không phải `spec.md` nội bộ, dài hơn ~200 từ vì mang `← A-xx` và nhãn `[M-x]`): `LC_ALL=C.UTF-8 wc -w <thư-mục>/spec.nop.md`. **Không** dùng `wc -w` trần — locale `C` đếm sai ký tự đa byte và báo thừa ~80 từ. > 3.000 = lỗi Cao; **> 2.750 = cảnh báo "hết đệm"** kèm danh sách ứng viên cắt (BR nhãn `[M-0]`, hit nhóm lint 22). Nếu thiếu `spec.nop.md` → ghi lỗi Cao "chưa sinh bản nộp" và đếm tạm trên `spec.md` kèm ghi chú.

**F. CỔNG CHẤT LƯỢNG** — **16 dòng** knowledge/30 §7 (11 dòng gốc + 12–16 từ knowledge/32 §5), đánh ✓/✗ với bằng chứng 1 dòng.

## Báo cáo `review.md` (cùng thư mục với spec)
1. Kết luận đầu file: **NỘP ĐƯỢC** hoặc **CHƯA** — CHƯA khi còn ≥1 lỗi mức Cao, hoặc > 3.000 từ, hoặc dòng ⚠ chưa có BR, hoặc cổng chất lượng có ✗, hoặc **còn ✗ ở F1/F5**, hoặc **có mục tiêu brief không có luật phục vụ**, hoặc **có tình huống eval mà hai reader ra kết quả khác nhau**.
2. Bảng lỗ hổng theo định dạng knowledge/40 §4, sắp theo Mức giảm dần rồi theo thang §5 (đã chèn 2b–2e); cột "Viết lại đề xuất" là câu dán được vào spec. Câu viết lại KHÔNG ĐƯỢC bịa con số/giá trị: số có trong RTM/log → ghi số + `(A-xx)`; không có → để `[..]` và ghi "xác nhận với AI Khách hàng"; luật hiện có trong spec chưa đối chiếu được với log → giữ nguyên nội dung, chỉ sửa diễn đạt, và gắn "cần đối chiếu log".
   **Phân biệt hai loại phát hiện, ghi rõ ở cột riêng:** `SỬA` = sửa được ngay bằng viết lại (mơ hồ, ô trống, F1, F5, S25–S27); `HỎI` = đội đang không biết specs thật quy định gì, sửa hộ là đoán lần thứ hai (F2, F7, S22, S23, mọi `[GIẢ ĐỊNH]` rủi ro cao). Mục `HỎI` xuất ra dạng khối câu hỏi đóng dán được vào `/elicit`, theo mẫu knowledge/32 §6.
3. Bảng eval set: tình huống | loại # | ĐỘ PHỦ | **TRẢ LỜI reader 1** | **TRẢ LỜI reader 2** | **lệch?** | đối chiếu RTM | mục cần vá.
4. **Khối G**: bảng cổng F; bảng Mục tiêu↔Luật; 12 ca suy biến; 6 láng giềng; 6 kẻ lạm dụng; 6 chi phí ẩn.
5. Cổng chất lượng 16 dòng ✓/✗. Số từ + đệm còn lại.
6. "Sửa trong 10 phút" — 5 việc đầu theo thang knowledge/40 §5, và **"Hỏi trong lượt tới"** — 5 câu đầu từ danh sách `HỎI`.

Nếu args có `sửa`: áp các câu viết lại mức Cao **thuộc loại `SỬA`** vào spec bằng Edit, chạy lại A, B, E, G-1, in diff số từ. **Không** áp mục loại `HỎI`. Không sửa nội dung nghiệp vụ (không đổi con số/trạng thái) — chỉ sửa diễn đạt, điền ô trống bằng "Từ chối 0.5", thêm luật cho chế độ lỗi của láng giềng (knowledge/32 §3.2, không cần số mới), và tách mốc quyết định/hoàn tất (knowledge/32 §3.1, để `[..]` cho số chưa biết).

## Output bắt buộc
- [ ] `review.md` với kết luận NỘP ĐƯỢC/CHƯA và tiêu chí.
- [ ] Mỗi phát hiện có vị trí, trích, loại, mức, **nhãn `SỬA`/`HỎI`**, viết lại hoặc câu hỏi.
- [ ] Eval set n tình huống đã chạy qua **hai** executor mù độc lập, cả hai câu trả lời được ghi.
- [ ] Khối G đầy đủ 6 bảng.
- [ ] Khối câu hỏi `HỎI` dán được vào `/elicit`.
- [ ] `eval-set.md` lưu lại (tái dùng cho /attack chính spec mình).

## Không được
- Đưa RTM/brief/đáp án vào prompt gọi executor; gửi hai reader hai prompt khác nhau.
- **Dùng ô `ĐA NGHĨA = KHÔNG` do chính reader tự khai làm bằng chứng spec rõ** — bằng chứng duy nhất là hai reader trùng kết quả.
- Sửa spec khi không có `sửa`; đổi con số nghiệp vụ khi `sửa`; áp mục loại `HỎI` vào spec.
- Kết luận NỘP ĐƯỢC khi còn lỗi Cao, còn ✗ F1/F5, hay còn mục tiêu brief không có luật.
- Bỏ khối D hoặc bỏ reader thứ hai vì "hết thời gian" — giảm n xuống 8 và giữ 2 reader.
- Bỏ khối G vì "spec đã sạch lint" — đó chính là trạng thái mà khối G được viết ra để soi.
