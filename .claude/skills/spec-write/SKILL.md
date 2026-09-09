---
name: spec-write
description: Viết spec "Đặt giữ hàng" cho AI Executor đọc từ mô hình bài toán + RTM ngược + log AI Khách hàng, theo template 11 mục có mã BR truy vết, mỗi luật gắn một mục tiêu và qua cổng khả thi F; hoặc nén spec đang có về ≤2.700 từ không mất luật. Dùng 11:00–11:45 ngày thi khi người dùng nói "viết spec", "điền template", "nén spec", "spec-write", "rút gọn còn 3000 từ".
argument-hint: "[thư-mục] [nén]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(wc *), Bash(LC_ALL=C.UTF-8 wc *), Bash(sed *)
---
# /spec-write — Viết spec từ mô hình bài toán + RTM

Mục tiêu: mọi dữ kiện ⚠ trong RTM thành một luật có mã; mọi mục tiêu của brief có luật phục vụ; mọi luật đứng được trong thực tế; mọi ô bảng được điền; **đích ≤2.700 từ** trên bản nộp; không bịa.

## Input
`$ARGUMENTS` = `[thư-mục] [nén]`. Thư mục mặc định `battle/`. Cần: `mo-hinh-bai-toan.md`, `rtm.md`, `log-khach-hang.md`, `brief.md`; nếu có `spec.md` thì là bản đang sửa.

**Điều kiện vào:** thiếu `mo-hinh-bai-toan.md` → **dừng, chạy `/frame` trước**. Đây là chốt cứng, không phải khuyến nghị: bản 08/09 viết mà không có mô hình bài toán, đạt mọi cổng hình thức (0 hit lint Cao, 36/36 ô, 15/15 eval ĐỦ) nhưng có 7 lỗi nội dung mức Cao (knowledge/32 §7) — 3 trong đó phá thẳng mục tiêu mà brief nêu.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §2 (bảng Mục tiêu↔Luật), §M4 (6 láng giềng), §M6 (12 kịch bản suy biến).
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md`: §1 cổng F, §2 bảng thực tế phụ thuộc ngoài, §3 ba mẫu viết lại, §4 chi phí ẩn, §6 chuyển hit F thành câu hỏi.
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md`: §1 template 11 mục + ngân sách từ, §2 khung §0 catch-all (gồm 0.11–0.14), §3 cú pháp EARS + mẫu BR-xx, §4 checklist 17 quy tắc **+ 7 quy tắc nội dung 18–24**, §5 tiết kiệm từ, §6 thứ tự viết, §7 cổng chất lượng 16 dòng.
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §1 hit policy, §2 chứng minh đủ/rời, §3 bảng state × event và mã hiệu ứng K/T/N/L.
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §2 glossary (chỉ lấy thuật ngữ có dùng trong spec).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 danh sách đen 22 nhóm (để tự tránh khi viết).

## Quy tắc cứng số 1 — truy vết
Mỗi BR/EX kết thúc bằng nguồn: `← A-07` (dòng RTM) hoặc `[GIẢ ĐỊNH]` (không có trong RTM). Không có nguồn = không được viết. Dòng RTM ⛔ (ngoài phạm vi) chỉ vào §1 NGOÀI, không thành BR.

## Quy tắc cứng số 2 — mỗi luật gắn một mục tiêu
Mỗi BR còn mang thêm một nhãn nội bộ: `[M-1]` (phục vụ mục tiêu 1) · `[⚡M-2]` (làm hỏng mục tiêu 2) · `[M-0]` (không thuộc mục tiêu nào). Nhãn `⚡` là **lỗi Cao**: không tự chốt, chuyển thành câu hỏi verify (knowledge/32 §6) và ghi vào danh sách restate. Nhãn `[M-0]` là ứng viên cắt đầu tiên khi thiếu chỗ. Nhãn này chỉ ở bản nội bộ, bỏ khi sinh bản nộp.

## Quy tắc cứng số 3 — không hứa hộ bên ngoài
Mọi nghĩa vụ do cổng thanh toán / ngân hàng / ERP / kênh thông báo thực hiện phải tách **mốc hệ thống quyết định** khỏi **mốc bên ngoài hoàn tất**, và phải có nhánh thất bại (mẫu dán được: knowledge/32 §3.1). Viết `hoàn tất ≤ N giờ` cho việc bên ngoài làm = lỗi Cao, chặn nộp.

## Bước (theo knowledge/30 §6)
0. Đọc `mo-hinh-bai-toan.md`. Chép ra ba danh sách để dùng suốt các bước sau: (a) **mục tiêu M1** — mỗi mục tiêu phải có ≥1 BR trước khi nộp; (b) **6 láng giềng M4** — mỗi láng giềng cần một luật cho chế độ lỗi; (c) **12 kịch bản suy biến M6** — mỗi ca cần ✓ hoặc ⛔. Ba danh sách này là checklist độ phủ **độc lập với RTM**: RTM chỉ phủ những gì đã hỏi, ba danh sách này phủ những gì bài toán cần dù chưa hỏi được.
1. Đọc `rtm.md` + `log-khach-hang.md`. Xác định biến thể (cart hold / cọc / pickup) từ dòng N1-01; chọn cụm luật cần nhiều từ nhất theo knowledge/10 §1. Liệt kê mọi dòng ⚠ chưa có Mã BR.
2. **§6 Luật nghiệp vụ** trước: dòng ⚠⚠ → ⚠ → còn lại. Mỗi dòng RTM = 1 BR theo mẫu knowledge/30 §3.3 (EARS, 6 thành phần, KHÔNG ĐƯỢC, Fit, ← A-xx). Luật ≥2 điều kiện → decision table hit policy U (knowledge/31 §1–§2): đếm tổ hợp = tích, ô không hợp lệ ghi "Từ chối 0.5".
3. **§5 State machine**: tập trạng thái lấy từ lượt 2 của log; bảng state × event điền 100% ô (`→ ĐÍCH K/T/N/L (BR-xx)` hoặc `KHL` / `Từ chối 0.5`), khai báo mã hiệu ứng một lần (knowledge/31 §3).
4. **§3 Actor × quyền**: bắt buộc cột Guest và System/Job; ô không ✓ = ✗ theo 0.4. Vì 0.4 biến mọi im lặng thành CẤM, bảng phải phủ cả thao tác chỉ-đọc (xem hold của mình / mọi hold, xem audit log) và mọi hành động log nêu "được làm" — rà lại từng câu trả lời kiểu "Được: a, b, c / Không được: d" trong log để không bỏ ý nào.
5. **§4 Tồn kho**: công thức ATP, cột nào đổi khi tạo/hết hạn/hủy/chuyển đơn, giá khóa hay không, giới hạn số lượng — tất cả từ RTM.
6. **§1 Phạm vi**: TRONG/NGOÀI nguyên văn từ lượt 1 (dòng ⛔ trong rtm.md).
7. **§2 Glossary**: chỉ thuật ngữ có nguy cơ hiểu sai và có dùng trong spec; một khái niệm một tên (loại bỏ từ đồng nghĩa khỏi toàn spec).
8. **§7, §8, §9, §10** theo template; §8 tối thiểu các EX nêu trong template nếu RTM có dữ kiện, còn lại để 0.5 chặn.
9. **§0 cuối cùng** từ khung knowledge/30 §2 (12 dòng cơ bản **+ bắt buộc 0.11–0.14**: khử trùng theo mã giao dịch cổng, tách mốc quyết định/hoàn tất, neo định danh, thất bại thông báo không đổi trạng thái). Từng dòng đối chiếu với RTM/log; dòng khớp → giữ và ghi `(verify A-xx)`; dòng RTM nói khác → sửa theo RTM; dòng không verify được → giữ nguyên nhưng gắn `[GIẢ ĐỊNH]`. Khai báo ký hiệu dùng trong spec ([a,b), →, KHL, K/T/N/L, ✓/✗) ở 0.x.
9b. **CỔNG F — chạy trước khi đếm từ.** Với từng BR/EX và từng dòng §0, chấm 8 kiểm tra knowledge/32 §1 (bắt đầu bằng 6 lệnh grep ở cuối §1 đó, rồi đọc). Xử lý:
    - ✗ ở **F1** (hứa hộ bên ngoài) → viết lại theo mẫu knowledge/32 §3.1. Sửa được ngay, không cần hỏi.
    - ✗ ở **F5** (phụ thuộc ngoài) → thêm luật thiếu theo mẫu knowledge/32 §3.2. Bốn luật §0 phủ 8/12 ca M6 với ~90 từ — làm trước mọi việc khác vì tỷ lệ chắn/từ tốt nhất.
    - ✗ ở **F2** (hạn mức trang trí) → nếu log có câu trả lời về đơn vị định danh thì sửa theo log; **không có thì dùng luật bao quát knowledge/32 §3.3**, đừng chọn hộ.
    - ✗ ở **F7** (làm hỏng mục tiêu, nhãn `⚡`) → **không sửa nội dung nghiệp vụ**. Ghi vào danh sách câu hỏi verify + restate, và giữ luật hiện tại. Đây là lỗi cần *hỏi*, không phải lỗi cần *viết lại*.
    - ✗ ở F3/F4/F6/F8 → sửa nếu còn ngân sách từ; không thì ghi vào bảng tóm tắt.
    Rồi rà ba danh sách của bước 0: mục tiêu nào chưa có BR, láng giềng nào chưa có luật lỗi, ca M6 nào còn ✗. Mỗi ô còn thiếu là một dòng trong bảng tóm tắt bước 14.
10. Ghi `spec.md`, rồi **sinh ngay bản nộp** `spec.nop.md` (bước 13) và **đếm từ trên BẢN NỘP**, không trên `spec.md`: hạn mức 3.000 của BTC áp cho bản nộp, còn bản nội bộ dài hơn ~200 từ vì mang `← A-xx`, nhãn `[M-x]` và `(verify …)` — đo bản nội bộ sẽ tự cắt oan luật. **Đích là ≤2.700, không phải ≤3.000**: 300 từ đệm là chỗ để vá sau khi elicit trả về số thật (bản 08/09 về đích 2.991 → còn 9 từ, không vá được gì).
    - Lệnh: `LC_ALL=C.UTF-8 wc -w <thư-mục>/spec.nop.md`. **Không** dùng `wc -w` trần: locale `C`/`POSIX` đếm sai ký tự đa byte (`—` `⇒` `→` `✓` `≥` `−` `§`) và báo thừa ~80 từ — đủ để tưởng là vượt hạn mức khi vẫn còn đệm.
    - `wc -w` đếm cả dấu `|` của bảng (mỗi hàng ~4 "từ"); giữ cách đếm thiên an toàn này cho tới khi knowledge/00 §A ghi cách đếm của BTC (câu 3 họp 09/09). Nếu BTC chốt "từ trong bảng không tính" thì đo lại bằng cách bỏ `|` và `---`, thường dư thêm ~230 từ.
    - Nếu > 2.700 từ → sang chế độ nén (dưới) rồi quay lại bước 11. Thứ tự hy sinh khi cạn: **BR nhãn `[M-0]`** (không phục vụ mục tiêu nào) → câu nói về chính spec (knowledge/40 §2 nhóm 22) → §7 → §10 → §9 → §2, và gộp BR trùng nội dung §1/§2/§5 về đúng mục đó (RTM trỏ tới `§1`/`§2`/`§5` thay vì mã BR — vẫn truy vết được). **Không cắt** 0.11–0.14 và không cắt luật cho ca M6 có tiền dính vào.
11. Tự kiểm nhanh: (a) Grep `BR-\d+|EX-\d+` — mã duy nhất, mọi "áp dụng BR-xx" tồn tại; (b) Grep blacklist knowledge/40 §2 nhóm 1, 3, 4, 5, 6 **và nhóm nội dung 17, 18, 19, 21, 22** trên bản nộp, sửa hit; (c) mọi dòng ⚠ trong RTM có BR; (d) mọi mục tiêu M1 có BR.
12. Điền ngược cột "Mã BR" và "Trạng thái" (✅) vào `rtm.md` bằng Edit.
13. Tạo bản nộp `spec.nop.md` = spec.md bỏ mọi dấu vết nội bộ (`← A-xx`, `(verify A-xx)`, nhãn `[M-x]`/`[⚡M-x]`, `[GIẢ ĐỊNH — …]` → giữ nội dung luật, bỏ nhãn) **và bỏ mọi câu nói về chính spec** (nhóm 22: "36/36 ô đã điền", chứng minh phủ tổ hợp, "không chồng lấn") bằng sed/Edit. Bước này chạy sớm ở bước 10 để có số đếm đúng, và chạy lại sau mỗi lần sửa `spec.md`. Grep `A-\d`, `M-\d`, `ô đã điền` trên bản nộp phải = 0 hit. `spec.md` nội bộ giữ nguyên để /spec-review, /appeal truy vết.
14. In bảng tóm tắt gồm 6 khối:
    - số từ (nội bộ / bản nộp) + đệm còn lại so với 3.000 | số BR/EX | dòng ⚠ đã phủ / tổng ⚠
    - **Độ phủ mục tiêu**: mỗi mục tiêu M1 → BR phục vụ; mục tiêu trống = lỗi Cao
    - **Nhãn `⚡`**: mọi BR làm hỏng mục tiêu, kèm câu hỏi verify đề xuất
    - **Cổng F**: đếm ✗ theo F1..F8, liệt kê ✗ ở F1/F2/F5/F7 (chặn nộp)
    - **Độ phủ suy biến**: 12 ca M6 → ✓ / ⛔ / ✗; và 6 láng giềng M4 → có luật lỗi hay không
    - **Xếp hạng rủi ro giả định**: mọi `[GIẢ ĐỊNH]` sắp theo rủi ro giảm dần, tính bằng ba tiêu chí — (1) đảo lại thì đổi kết quả quan sát được (tiền / trạng thái cuối / ai thắng) hay chỉ đổi diễn đạt; (2) Executor mù có đoán trùng giả định này không (giả định **trùng mặc định ngành** thì rủi ro thấp, vì cả ta và Executor đều đoán như nhau; giả định **ngược mặc định** thì rủi ro cao); (3) có nằm trong core flow tiền/tồn không. Cột cuối: câu hỏi verify để dán vào `/elicit`. Đây là danh sách việc cho lượt hỏi kế tiếp, không phải phụ lục.

## Chế độ `nén`
1. Grep liệt kê toàn bộ mã BR/EX và dòng "←" trước khi sửa.
2. Áp knowledge/30 §5: bảng thay văn, "áp dụng BR-xx" thay lặp, công thức thay câu, luật tổng quát + ngoại lệ thay liệt kê, cắt §7 → §10 → §9 → §2 nếu vẫn thừa. Không cắt §0, §3, §5, §6, §8.
3. Grep lại danh sách mã: phải bằng danh sách trước. In diff số từ trước/sau và khẳng định "không mất BR".

## Output bắt buộc
- [ ] `spec.md` đủ 11 mục, mọi ô bảng có giá trị; bản nộp ≤2.700 từ (đích) và tuyệt đối ≤3.000.
- [ ] Mọi BR/EX có `← A-xx` hoặc `[GIẢ ĐỊNH]`, và một nhãn `[M-x]` / `[⚡M-x]` / `[M-0]`.
- [ ] §0 có đủ 0.11–0.14; mỗi láng giềng M4 có một luật cho chế độ lỗi.
- [ ] Cổng F đã chạy trên mọi BR; 0 ✗ ở F1 và F5 (F2/F7 được phép còn ✗ nếu đã sinh câu hỏi verify).
- [ ] `rtm.md` cột Mã BR đã điền cho mọi dòng ⚠.
- [ ] Bảng tóm tắt 6 khối in ra, gồm xếp hạng rủi ro giả định.

## Không được
- Chạy khi chưa có `mo-hinh-bai-toan.md`.
- Viết luật không truy vết được về RTM mà không gắn `[GIẢ ĐỊNH]`; viết ngược với RTM.
- Hứa mốc **hoàn tất** cho việc do cổng / ngân hàng / ERP thực hiện.
- Neo hạn mức vào dữ liệu khách tự khai khi chưa hỏi được đơn vị định danh — dùng luật bao quát knowledge/32 §3.3 thay vì chọn hộ.
- **Tự sửa nội dung nghiệp vụ của một BR nhãn `⚡` để nó "hợp lý hơn"** — mâu thuẫn với mục tiêu brief là tín hiệu phải hỏi, sửa hộ là đoán lần thứ hai trên cùng một chỗ mù.
- Dùng từ trong danh sách đen knowledge/40 §2 (22 nhóm); tham chiếu tài liệu ngoài; để ô bảng trống.
- Để câu nói về chính spec trong bản nộp.
- Viết §0 trước khi đọc xong RTM.
- Nộp khi wc -w bản nộp > 3.000.
