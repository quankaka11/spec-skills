---
name: spec-write
description: Viết spec cho AI Executor đọc từ mô hình bài toán + RTM (dòng đã hỏi và dòng giả định) + log AI Khách hàng, theo template 11 mục có mã BR truy vết, mỗi luật gắn một mục tiêu và qua cổng khả thi F; ô không hỏi được thì điền mặc định ngành có xếp hạng rủi ro; hoặc nén spec đang có về ≤5.400 token không mất luật. Dùng 10:20–11:40 ngày thi khi người dùng nói "viết spec", "điền template", "nén spec", "spec-write", "rút gọn spec".
argument-hint: "[thư-mục] [nén]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(LC_ALL=C.UTF-8 wc *), Bash(sed *)
---
# /spec-write — Viết spec từ mô hình bài toán + RTM

Mục tiêu: mọi dòng RTM thành một luật có mã; mọi mục tiêu của brief có luật phục vụ; mọi luật đứng được trong thực tế; mọi ô bảng được điền; **bản nộp ≤5.400 token ước lượng** (hạn mức 6.000 — 00 §A); không bịa.

## Input
`$ARGUMENTS` = `[thư-mục] [nén]`. Thư mục mặc định `battle/`. Cần: `mo-hinh-bai-toan.md`, `rtm.md`, `log-khach-hang.md`, `brief.md`; có `spec.md` thì là bản đang sửa.

**Điều kiện vào:** thiếu `mo-hinh-bai-toan.md` → **dừng, chạy `/frame` trước**. Chốt cứng, không phải khuyến nghị: bản 08/09 viết mà không có mô hình bài toán, đạt mọi cổng hình thức nhưng có 7 lỗi nội dung mức Cao (knowledge/32 §7) — 3 trong đó phá thẳng mục tiêu brief nêu.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md`: §1 **ngân sách token từng mục + quy tắc mermaid**, §1b **luật giả định = mặc định ngành**, §2 khung §0 catch-all (gồm 0.11–0.15), §3 cú pháp EARS + mẫu BR-xx, §4 checklist 17 quy tắc + 7 quy tắc nội dung 18–24, §5 kỹ thuật tiết kiệm token, §6 thứ tự viết 10:20–11:52, §7 cổng chất lượng 16 dòng.
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §2 (bảng Mục tiêu↔Luật), §M4 (6 láng giềng), §M6 (12 kịch bản suy biến), §4 (ô mô hình không hỏi được thì xử lý thế nào).
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md`: §1 cổng F, §2 bảng thực tế phụ thuộc ngoài, §3 ba mẫu viết lại, §4 chi phí ẩn, §6 hit F nào đi vào câu C5.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §4 (**31 phát biểu mặc định ngành — nguồn giá trị cho mọi ô không hỏi được**), §5 (RTM hai loại dòng), §3 bảng ánh xạ cuối mục (phần nào không được hỏi).
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §1 hit policy, §2 chứng minh đủ/rời, §3 bảng state × event và mã hiệu ứng K/T/N/L.
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §2 glossary, §6 catalogue ⚠ (nguồn mặc định ngành thứ hai).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 danh sách đen 22 nhóm (tự tránh khi viết).

## Quy tắc cứng số 1 — mọi luật có một dòng RTM
Mỗi BR/EX kết thúc bằng nguồn: `← A-07` (có câu trả lời của AI Khách hàng) hoặc `← G-12 [GIẢ ĐỊNH]` (mặc định ngành, có xếp hạng rủi ro). **Không có dòng RTM = không được viết**; thiếu dòng thì mở dòng `G-xx` mới trong `rtm.md` trước khi viết luật. Dòng RTM ⛔ (ngoài phạm vi) chỉ vào §1 NGOÀI, không thành BR.

Với 5 câu hỏi, **60–80% BR sẽ là `G-xx`. Đó là bình thường, không phải lỗi quy trình** — điều phải tránh là giả định *ngược mặc định ngành* ở chỗ có tiền hoặc tồn.

## Quy tắc cứng số 2 — giả định = mặc định ngành, không sáng tạo
Chi tiết và lý lẽ: knowledge/30 §1b. Ba điểm áp dụng khi gõ:
- Giá trị cho ô không hỏi được **lấy từ 20 §4 hoặc 10 §6**, không tự nghĩ ra. Viết mặc định ngành ra không xấu hơn im lặng ở bất kỳ ca nào, mà loại được đa nghĩa ở ca ghép; còn viết một giá trị tự nghĩ ra là ca duy nhất tệ hơn im lặng.
- Ba loại **không được tự chốt**: luật làm hỏng mục tiêu brief (nhãn `⚡`, F7) · hạn mức không cưỡng chế được (F2) · giả định đảo lại thì đổi hướng tiền. Chúng đi vào câu C5, hoặc dùng luật an toàn hai chiều (32 §3.3).
- Hết câu hỏi mà vẫn phải chọn → chọn **phương án phục vụ mục tiêu brief**, ghi `[GIẢ ĐỊNH-MT]`. Brief là căn cứ chung; phương án phá mục tiêu brief gần chắc không phải specs thật.

## Quy tắc cứng số 3 — mỗi luật gắn một mục tiêu
Nhãn nội bộ: `[M-1]` phục vụ mục tiêu 1 · `[⚡M-2]` làm hỏng mục tiêu 2 · `[M-0]` không thuộc mục tiêu nào. `⚡` là **lỗi Cao**: không tự chốt, chuyển thành phát biểu cho câu C5 và ghi vào hàng đợi restate. `[M-0]` là ứng viên cắt đầu tiên khi thiếu token. Nhãn chỉ ở bản nội bộ.

## Quy tắc cứng số 4 — không hứa hộ bên ngoài
Mọi nghĩa vụ do cổng thanh toán / ngân hàng / ERP / kênh thông báo thực hiện phải tách **mốc hệ thống quyết định** khỏi **mốc bên ngoài hoàn tất**, và phải có nhánh thất bại (mẫu dán được: knowledge/32 §3.1). Viết `hoàn tất ≤ N giờ` cho việc bên ngoài làm = lỗi Cao, chặn nộp.

## Bước (theo knowledge/30 §6)
0. Đọc `mo-hinh-bai-toan.md`. Chép ra ba danh sách dùng suốt các bước sau: (a) **mục tiêu M1** — mỗi mục tiêu ≥1 BR trước khi nộp; (b) **6 láng giềng M4** — mỗi láng giềng một luật cho chế độ lỗi; (c) **12 kịch bản suy biến M6** — mỗi ca ✓ hoặc ⛔. Ba danh sách này là checklist độ phủ **độc lập với RTM**: RTM chỉ phủ những gì đã hỏi và đã dự kiến, ba danh sách này phủ những gì bài toán cần.
1. Đọc `rtm.md` + `log-khach-hang.md`. Xác định biến thể từ brief và câu trả lời C1/C3; chọn cụm luật cần nhiều token nhất theo knowledge/10 §1. Liệt kê mọi dòng RTM chưa có Mã BR, **cả `A-xx` và `G-xx`**.
1b. **Bổ khuyết RTM trước khi gõ.** Nếu `rtm.md` chưa có dòng `G-xx` (vì `/elicit nap` chưa chạy bước 5): sinh ngay — mỗi ô mô hình còn `?` và mỗi nhóm ID ở cột phải bảng ánh xạ 20 §3 thành một dòng `G-xx` với giá trị mặc định ngành. Viết spec khi RTM chỉ có dòng `A-xx` là cách chắc chắn nhất để bỏ trắng 70% bài toán.
2. **§6 Luật nghiệp vụ** trước: `A-xx` ⚠⚠ → `A-xx` ⚠ → `A-xx` còn lại → `G-xx` rủi ro cao → `G-xx` còn lại. Mỗi dòng RTM = 1 BR theo mẫu knowledge/30 §3.3 (EARS, 6 thành phần, KHÔNG ĐƯỢC, Fit, `← A-xx`/`← G-xx`). Luật ≥2 điều kiện → decision table hit policy U (knowledge/31 §1–§2): đếm tổ hợp = tích, ô không hợp lệ ghi "Từ chối 0.5". Chọn dạng bảng hay danh sách theo 30 §5 **trước khi gõ chữ đầu tiên**.
3. **§5 State machine**: tập trạng thái lấy từ câu trả lời C2; bảng state × event điền 100% ô (`→ ĐÍCH K/T/N/L (BR-xx)` hoặc `KHL` / `Từ chối 0.5`), khai báo mã hiệu ứng một lần (knowledge/31 §3). Ô nào C2 không phủ → mặc định ngành, mở `G-xx`.
4. **§3 Actor × quyền**: bắt buộc cột Guest và System/Job; ô không ✓ = ✗ theo 0.4. Vì 0.4 biến mọi im lặng thành CẤM, bảng phải phủ cả thao tác chỉ-đọc (xem hold của mình / mọi hold, xem audit log) và mọi hành động log nêu "được làm".
5. **§4 Tồn kho**: công thức ATP, cột nào đổi khi tạo/hết hạn/hủy/chuyển đơn, giá khóa hay không, giới hạn số lượng.
6. **§1 Phạm vi**: TRONG/NGOÀI **nguyên văn từ câu trả lời C1** (dòng ⛔ trong `rtm.md`). Không có C1 → dùng danh sách NGOÀI của domain (10) và ghi `[GIẢ ĐỊNH]`, đồng thời cảnh báo: buổi chiều mọi test của đội bị +1 điểm phạm vi (50 §6-5).
7. **§2 Glossary**: chỉ thuật ngữ dễ hiểu sai và có dùng trong spec; một khái niệm một tên.
8. **§8, §9** theo template. **§7 và §10 chỉ viết nếu đo xong còn đệm** (30 §1) — kế hoạch mặc định ở hạn mức 6.000 token là 9 mục.
9. **§0 cuối cùng** từ khung knowledge/30 §2 (12 dòng cơ bản **+ bắt buộc 0.11–0.14**; **+ 0.15 nếu spec có sơ đồ mermaid**). Từng dòng đối chiếu RTM/log: khớp → giữ và ghi `(verify A-xx)`; RTM nói khác → sửa theo RTM; không verify được → giữ và gắn `[GIẢ ĐỊNH]`. Khai báo ký hiệu dùng trong spec ([a,b), →, KHL, K/T/N/L, ✓/✗) ở 0.x — ký hiệu là kỹ thuật tiết kiệm token lãi nhất (30 §5).
9b. **CỔNG F — chạy trước khi đếm token.** Với từng BR/EX và từng dòng §0, chấm 8 kiểm tra knowledge/32 §1 (bắt đầu bằng 6 lệnh grep ở cuối §1). Xử lý:
    - ✗ **F1** (hứa hộ bên ngoài) → viết lại theo mẫu 32 §3.1. Sửa ngay, không cần hỏi.
    - ✗ **F5** (phụ thuộc ngoài) → thêm luật thiếu theo mẫu 32 §3.2. Bốn luật §0 phủ 8/12 ca M6 với ~230 token — làm trước mọi việc khác vì tỷ lệ chắn/token tốt nhất.
    - ✗ **F2** (hạn mức trang trí) → có câu trả lời C3 thì sửa theo C3; không có thì dùng luật bao quát 32 §3.3, **không chọn hộ**.
    - ✗ **F7** (làm hỏng mục tiêu, nhãn `⚡`) → **không sửa nội dung nghiệp vụ**. Viết thành phát biểu cho câu C5 và giữ luật hiện tại; nếu C5 đã gửi rồi thì áp quy tắc cứng số 2 điểm 3 (`[GIẢ ĐỊNH-MT]`).
    - ✗ F3/F4/F6/F8 → sửa nếu còn ngân sách token; không thì vào bảng tóm tắt.
    Rồi rà ba danh sách bước 0: mục tiêu nào chưa có BR, láng giềng nào chưa có luật lỗi, ca M6 nào còn ✗.
10. Ghi `spec.md`, rồi **sinh ngay bản nộp** `spec.nop.md` (bước 13) và **đếm token trên BẢN NỘP**, không trên `spec.md` (bản nội bộ dài hơn ~500 token vì mang `← A-xx`, nhãn `[M-x]`, `(verify …)`):
    - Lệnh: `LC_ALL=C.UTF-8 wc -w -m <thư-mục>/spec.nop.md` rồi `token ≈ max(số từ × 2,5 ; số ký tự / 2,2)`. Báo cả hai con số và giá trị lấy. **Không** dùng `wc` thiếu `LC_ALL=C.UTF-8`: locale `C` đếm sai ký tự đa byte (`—` `⇒` `→` `✓` `≥` `−` `§`).
    - Nếu giao diện nộp của BTC hiển thị token → số đó thắng; ghi tỷ lệ `token thật / số từ` vào `review.md` để lần đo sau dùng hệ số thật.
    - > 5.400 → sang chế độ nén rồi quay lại bước 11. Thứ tự hy sinh: **BR nhãn `[M-0]`** → câu nói về chính spec (40 §2 nhóm 22) → §7 → §10 → §9 → §2, và gộp BR trùng nội dung §1/§2/§5 về đúng mục đó. **Không cắt** 0.11–0.15 và không cắt luật cho ca M6 có tiền dính vào.
11. Tự kiểm nhanh: (a) Grep `BR-\d+|EX-\d+` — mã duy nhất, mọi "áp dụng BR-xx" tồn tại; (b) Grep blacklist knowledge/40 §2 nhóm 1, 3, 4, 5, 6 **và nhóm nội dung 17, 18, 19, 21, 22** trên bản nộp; (c) mọi dòng RTM có BR; (d) mọi mục tiêu M1 có BR; (e) nếu spec có khối ```mermaid thì §0.15 tồn tại.
12. Điền ngược cột "Mã BR" và "Trạng thái" (✅) vào `rtm.md` bằng Edit — cho **cả** dòng `A-xx` và `G-xx`.
13. Tạo bản nộp `spec.nop.md` = `spec.md` bỏ mọi dấu vết nội bộ (`← A-xx`, `← G-xx`, `(verify A-xx)`, nhãn `[M-x]`/`[⚡M-x]`, `[GIẢ ĐỊNH]`/`[GIẢ ĐỊNH-MT]` → **giữ nội dung luật, bỏ nhãn**) và bỏ mọi câu nói về chính spec (nhóm 22). Grep `A-\d`, `G-\d`, `M-\d`, `GIẢ ĐỊNH`, `ô đã điền` trên bản nộp phải = 0 hit. Chạy sớm ở bước 10 và chạy lại sau mỗi lần sửa.
14. In bảng tóm tắt 6 khối:
    - token (nội bộ / bản nộp, cả hai công thức) + đệm còn lại so với 6.000 | số BR/EX | dòng RTM đã phủ / tổng (tách `A-xx` và `G-xx`)
    - **Độ phủ mục tiêu**: mỗi mục tiêu M1 → BR phục vụ; mục tiêu trống = lỗi Cao
    - **Nhãn `⚡`**: mọi BR làm hỏng mục tiêu, kèm phát biểu đề xuất cho C5
    - **Cổng F**: đếm ✗ theo F1..F8, liệt kê ✗ ở F1/F2/F5/F7 (chặn nộp)
    - **Độ phủ suy biến**: 12 ca M6 → ✓ / ⛔ / ✗; 6 láng giềng M4 → có luật lỗi hay không
    - **XẾP HẠNG RỦI RO GIẢ ĐỊNH** — khối quan trọng nhất của bước này, vì nó là **nội dung câu hỏi cuối cùng còn lại**. Mọi dòng `G-xx` sắp theo rủi ro giảm dần, tính bằng ba tiêu chí: (1) đảo lại thì đổi kết quả quan sát được (tiền / trạng thái cuối / ai thắng) hay chỉ đổi diễn đạt; (2) Executor mù có đoán trùng giả định này không — **trùng mặc định ngành thì rủi ro thấp** (cả ta và Executor đoán như nhau), **ngược mặc định ngành thì rủi ro cao**; (3) có nằm trong core flow tiền/tồn không. Cột cuối: **phát biểu Đúng/Sai đã viết sẵn** để dán vào `/elicit restate`. In 10 dòng đầu riêng thành một khối `→ C5`.
    Ghi đồng thời cột "Rủi ro" vào `rtm.md`.

## Chế độ `nén`
1. Grep liệt kê toàn bộ mã BR/EX và dòng `←` trước khi sửa.
2. Áp knowledge/30 §5: ký hiệu thay cụm tiếng Việt · công thức thay câu · "áp dụng BR-xx" thay lặp · bảng ≥3 cột nội dung, bảng 2 cột đổi thành danh sách · dồn tác động phụ về §0.7 · luật tổng quát + ngoại lệ thay liệt kê · cắt §7 → §10 → §9 → §2. Không cắt §0, §3, §5, §6, §8.
3. Grep lại danh sách mã: phải bằng danh sách trước. In diff token trước/sau (cả hai công thức) và khẳng định "không mất BR".

## Output bắt buộc
- [ ] `spec.md` đủ mục theo kế hoạch (≥9 mục), mọi ô bảng có giá trị; bản nộp ≤5.400 token (đích), tuyệt đối ≤6.000.
- [ ] Mọi BR/EX có `← A-xx` hoặc `← G-xx`, và một nhãn `[M-x]` / `[⚡M-x]` / `[M-0]`.
- [ ] §0 có đủ 0.11–0.14 (+0.15 nếu có mermaid); mỗi láng giềng M4 có một luật cho chế độ lỗi.
- [ ] Cổng F đã chạy trên mọi BR; 0 ✗ ở F1 và F5.
- [ ] `rtm.md` cột Mã BR đã điền cho mọi dòng; cột Rủi ro đã điền cho mọi dòng `G-xx`.
- [ ] Bảng tóm tắt 6 khối, **gồm khối `→ C5` gồm 10 phát biểu Đúng/Sai đã viết sẵn**.
- [ ] `spec.nop.md` sạch dấu vết nội bộ, không có ảnh.

## Không được
- Chạy khi chưa có `mo-hinh-bai-toan.md`; gõ §6 khi RTM chưa có dòng `G-xx`.
- Viết luật không có dòng RTM nào; viết ngược với dòng `A-xx`.
- **Tự nghĩ ra giá trị cho ô không hỏi được** thay vì lấy mặc định ngành ở 20 §4 / 10 §6.
- Hứa mốc **hoàn tất** cho việc do cổng / ngân hàng / ERP thực hiện.
- Neo hạn mức vào dữ liệu khách tự khai khi chưa hỏi được đơn vị định danh — dùng luật bao quát 32 §3.3.
- **Tự sửa nội dung nghiệp vụ của một BR nhãn `⚡` để nó "hợp lý hơn"** khi câu C5 chưa gửi.
- Vẽ sơ đồ mermaid lặp lại nội dung bảng §5; có mermaid mà thiếu §0.15.
- Dùng từ trong danh sách đen knowledge/40 §2; tham chiếu tài liệu ngoài; để ô bảng trống.
- Để câu nói về chính spec, hoặc nhãn `[GIẢ ĐỊNH]`, trong bản nộp.
- Nộp khi token bản nộp > 6.000.
