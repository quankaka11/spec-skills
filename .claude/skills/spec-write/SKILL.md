---
name: spec-write
description: Viết spec cho AI Executor đọc từ mô hình bài toán + RTM (dòng đã hỏi và dòng giả định) + log AI Khách hàng, theo **cấu trúc 10 mục BTC** (基本設計/詳細設計/技術仕様) có mã BR truy vết, mỗi luật gắn một mục tiêu và qua cổng khả thi F; ô không hỏi được thì điền mặc định ngành có xếp hạng rủi ro; hoặc nén spec đang có về ≤5.400 token không mất luật. Dùng 10:20–11:52 ngày thi khi người dùng nói "viết spec", "điền template", "nén spec", "spec-write", "rút gọn spec".
argument-hint: "[thư-mục] [nén]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(LC_ALL=C.UTF-8 wc *), Bash(sed *)
---
# /spec-write — Viết spec từ mô hình bài toán + RTM

Mục tiêu: mọi dòng RTM thành một luật có mã; đủ **10 mục BTC**, không mục nào trống; mọi mục tiêu của brief có luật phục vụ; mọi luật đứng được trong thực tế; mọi ô bảng được điền; **bản nộp ≤5.400 token ước lượng** (hạn mức 6.000 — 00 §A); không bịa.

## Input
`$ARGUMENTS` = `[thư-mục] [nén]`. Thư mục mặc định `battle/`. Cần: `mo-hinh-bai-toan.md`, `rtm.md`, `log-khach-hang.md`, `brief.md`; có `spec.md` thì là bản đang sửa.

**Điều kiện vào:** thiếu `mo-hinh-bai-toan.md` → **dừng, chạy `/frame` trước**. Chốt cứng, không phải khuyến nghị: bản 08/09 viết mà không có mô hình bài toán, đạt mọi cổng hình thức nhưng có 7 lỗi nội dung mức Cao (knowledge/32 §7) — 3 trong đó phá thẳng mục tiêu brief nêu.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/33-cau-truc-spec-btc.md` — **CẤU TRÚC BẮT BUỘC 10 mục BTC**: §1 nội dung & hình thức từng mục, §2 "ba thứ" của mỗi logic ở mục 6, §3 các quy tắc cũ đã đổi, §4 ánh xạ template cũ → mới, §5 ngân sách token, §6 thứ tự cắt, **§7 cú pháp Mermaid cho 4 sơ đồ** (wireframe, state machine, sequence, sơ đồ hệ thống). **Đọc file này TRƯỚC knowledge/30.**
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md`: §1 template 10 mục chi tiết, §2 khung catch-all đặt ở mục 1.x (gồm 0.11–0.15), §3 cú pháp EARS + mẫu BR-xx, §4 checklist 17 quy tắc + 7 quy tắc nội dung (18–24), §1b luật giả định = mặc định ngành, §5 tiết kiệm token, §6 thứ tự viết, §7 cổng chất lượng 23 dòng.
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §2 (bảng Mục tiêu↔Luật), §M4 (6 láng giềng), §M6 (12 kịch bản suy biến), §4 (ô mô hình không hỏi được thì xử lý thế nào).
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md`: §1 cổng F, §2 bảng thực tế phụ thuộc ngoài, §3 ba mẫu viết lại, §4 chi phí ẩn, §6 hit F nào thành một lượt hỏi riêng.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §4 (**31 phát biểu mặc định ngành — nguồn giá trị cho mọi ô không hỏi được**), §5 (RTM hai loại dòng), §3 bảng ánh xạ cuối mục (phần nào không được hỏi).
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §1 hit policy, §2 chứng minh đủ/rời, §3 bảng state × event và mã hiệu ứng K/T/N/L.
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §2 glossary, §6 catalogue ⚠ (nguồn mặc định ngành thứ hai).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 danh sách đen 22 nhóm (tự tránh khi viết).

## Quy tắc cứng số 1 — mọi luật có một dòng RTM
Mỗi BR/EX kết thúc bằng nguồn: `← A-07` (có câu trả lời của AI Khách hàng) hoặc `← G-12 [GIẢ ĐỊNH]` (mặc định ngành, có xếp hạng rủi ro). **Không có dòng RTM = không được viết**; thiếu dòng thì mở dòng `G-xx` mới trong `rtm.md` trước khi viết luật. Dòng RTM ⛔ (ngoài phạm vi) chỉ vào §1 NGOÀI, không thành BR.

Kể cả khi kịp 15–20 lượt hỏi, **55–75% BR vẫn sẽ là `G-xx`. Đó là bình thường, không phải lỗi quy trình** — điều phải tránh là giả định *ngược mặc định ngành* ở chỗ có tiền hoặc tồn.

## Quy tắc cứng số 2 — giả định = mặc định ngành, không sáng tạo
Chi tiết và lý lẽ: knowledge/30 §1b. Ba điểm áp dụng khi gõ:
- Giá trị cho ô không hỏi được **lấy từ 20 §4 hoặc 10 §6**, không tự nghĩ ra. Viết mặc định ngành ra không xấu hơn im lặng ở bất kỳ ca nào, mà loại được đa nghĩa ở ca ghép; còn viết một giá trị tự nghĩ ra là ca duy nhất tệ hơn im lặng.
- Ba loại **không được tự chốt**: luật làm hỏng mục tiêu brief (nhãn `⚡`, F7) · hạn mức không cưỡng chế được (F2) · giả định đảo lại thì đổi hướng tiền. Chúng thành một lượt hỏi riêng (ưu tiên dạng nhị phân), hoặc dùng luật an toàn hai chiều (32 §3.3).
- Hết câu hỏi mà vẫn phải chọn → chọn **phương án phục vụ mục tiêu brief**, ghi `[GIẢ ĐỊNH-MT]`. Brief là căn cứ chung; phương án phá mục tiêu brief gần chắc không phải specs thật.

## Quy tắc cứng số 3 — mỗi luật gắn một mục tiêu
Nhãn nội bộ: `[M-1]` phục vụ mục tiêu 1 · `[⚡M-2]` làm hỏng mục tiêu 2 · `[M-0]` không thuộc mục tiêu nào. `⚡` là **lỗi Cao**: không tự chốt, chuyển thành một lượt nhị phân và chèn lên đầu hàng đợi hỏi còn lại. `[M-0]` là ứng viên cắt đầu tiên khi thiếu token. Nhãn chỉ ở bản nội bộ.

## Quy tắc cứng số 4 — không hứa hộ bên ngoài
Mọi nghĩa vụ do cổng thanh toán / ngân hàng / ERP / kênh thông báo thực hiện phải tách **mốc hệ thống quyết định** khỏi **mốc bên ngoài hoàn tất**, và phải có nhánh thất bại (mẫu dán được: knowledge/32 §3.1). Viết `hoàn tất ≤ N giờ` cho việc bên ngoài làm = lỗi Cao, chặn nộp.

## Bước (theo knowledge/30 §6)
0. Đọc `mo-hinh-bai-toan.md`. Chép ra ba danh sách dùng suốt các bước sau: (a) **mục tiêu M1** — mỗi mục tiêu ≥1 BR trước khi nộp; (b) **6 láng giềng M4** — mỗi láng giềng một luật cho chế độ lỗi; (c) **12 kịch bản suy biến M6** — mỗi ca ✓ hoặc ⛔. Ba danh sách này là checklist độ phủ **độc lập với RTM**: RTM chỉ phủ những gì đã hỏi và đã dự kiến, ba danh sách này phủ những gì bài toán cần.
1. Đọc `rtm.md` + `log-khach-hang.md`. Xác định biến thể (cart hold / cọc / pickup) từ brief và câu trả lời C1/C3; lập danh sách LOGIC sẽ viết ở mục 6; chọn cụm luật cần nhiều token nhất theo knowledge/10 §1. Liệt kê mọi dòng RTM chưa có Mã BR, **cả `A-xx` và `G-xx`**.
1b. **Bổ khuyết RTM trước khi gõ.** Nếu `rtm.md` chưa có dòng `G-xx` (vì `/elicit nap` chưa chạy bước 5): sinh ngay — mỗi ô mô hình còn `?` và mỗi nhóm ID ở cột phải bảng ánh xạ 20 §3 thành một dòng `G-xx` với giá trị mặc định ngành. Viết spec khi RTM chỉ có dòng `A-xx` là cách chắc chắn nhất để bỏ trắng 70% bài toán.
2. **Mục 1 trước** (680 token): tên hệ thống / màn hình / chức năng, trạng thái tài liệu, chức năng làm gì — 4–5 bullet; TRONG/NGOÀI phạm vi **nguyên văn từ câu trả lời C1** (dòng ⛔ trong `rtm.md`); không có C1 → dùng danh sách NGOÀI của domain (10) và ghi `[GIẢ ĐỊNH]`, đồng thời cảnh báo: buổi chiều mọi test của đội bị +1 điểm phạm vi (50 §6-5). Rồi **1.x catch-all** từ khung knowledge/30 §2 (12 dòng cơ bản **+ bắt buộc 0.11–0.14**; **+ 0.15 nếu spec có sơ đồ mermaid**) và **1.y glossary** nếu ngắn — chỉ thuật ngữ dễ hiểu sai, một khái niệm một tên.
3. **Mục 6 — lớn nhất (1.900 token), viết ngay sau mục 1.** Mỗi logic một mục con (danh sách gợi ý 6.1–6.9 ở knowledge/30 §1), mỗi logic đủ **ba thứ** (knowledge/33 §2):
   - (1) bảng step: ai làm gì / hệ thống phản hồi gì;
   - (2)(3) bảng `Case · Điều kiện · Kết quả mong đợi · Mã · RTM` — **bắt buộc đủ 3 loại case: bình thường, biên, lỗi**; mỗi bảng đủ **5 gạch**: con số · toán tử `>` hay `≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng.
   - Cột "Kết quả mong đợi" đủ **7 chiều**: trạng thái cuối · kho · tiền · thông báo · **hiển thị gì** · **trạng thái nút** · **message**.
   - Thứ tự viết luật: `A-xx` ⚠⚠ → `A-xx` ⚠ → `A-xx` còn lại → `G-xx` rủi ro cao → `G-xx` còn lại. Mỗi dòng RTM = 1 mã BR theo mẫu knowledge/30 §3.3 (EARS, 6 thành phần, KHÔNG ĐƯỢC, Fit, `← A-xx`/`← G-xx`) và một nhãn mục tiêu `[M-x]`.
   - Logic `6.1`/`6.2` mang công thức tồn kho (ATP, cột nào đổi khi tạo/hết hạn/hủy/chuyển đơn, giá khóa hay tính lại, giới hạn số lượng) — dữ kiện tối thiểu ở knowledge/30 §1 mục 6.
   - Logic "chuyển trạng thái hold" (`6.3`) = bảng state × event điền 100% ô (`→ ĐÍCH K/T/N/L (BR-xx)` / `KHL` / `Từ chối 0.5`), tập trạng thái lấy từ câu trả lời C2, mã hiệu ứng khai báo một lần (knowledge/31 §3). Ô nào C2 không phủ → mặc định ngành, ghi `G-xx`. Logic ≥2 điều kiện → decision table hit policy U, đếm tổ hợp = tích (knowledge/31 §1–§2).
   - Logic `6.3` kèm **Mermaid `stateDiagram-v2`** (knowledge/33 §7.2) — sơ đồ KHÔNG thay bảng state × event, vì ô `Từ chối 0.5` / `KHL` chỉ có trong bảng. Cuối mục 6: **sequenceDiagram end-to-end** (§7.3) cho luồng chính. Logic `6.9` mang bảng mốc thông báo.
4. **Mục 4 Validation & message** (625 token): bảng `No · FE/BE · Item · Nội dung check · Message`. **Message ghi nguyên văn trong ngoặc kép** — lấy từ RTM (câu trả lời N17-*); không có trong RTM → **chèn ngay một lượt hỏi mở cho message đó** (20 §3b: message nguyên văn là vùng duy nhất không có mặc định ngành, và giờ hỏi được); hết giờ hỏi thì viết message cụ thể và mở dòng `G-xx` rủi ro **Cao**, KHÔNG ghi "hiển thị thông báo lỗi phù hợp". Có cả rule nghiệp vụ, không chỉ rule format.
5. **Mục 8 Xác thực & phân quyền** (300 token): bảng actor bắt buộc có **Guest** và **System/Job**; ô không ✓ = ✗ theo 0.4. Vì 0.4 biến mọi im lặng thành CẤM, bảng phải phủ cả thao tác chỉ-đọc (xem hold của mình / mọi hold, xem audit log) và mọi hành động log nêu "được làm". Bảng 2: mỗi actor xác thực bằng gì.
6. **Mục 2 Item màn hình** (575 token): bảng `No · 項目名 · コントロール · I/O · 必須 · 備考`, chia theo khu vực. Bắt buộc có **khác biệt login vs guest**, điều kiện **hiển thị/ẩn/disable**, giá trị mặc định, giới hạn, format, text nút nguyên văn. Ô không có dữ kiện RTM → `[GIẢ ĐỊNH — ..]`, không để trống.
7. **Mục 3 Event** (300 token): bảng `No · Event · Trigger · Xử lý · Ghi chú` — tối thiểu: mở màn hình lấy gì từ đâu, mỗi nút gọi xử lý gì, double-click (chặn theo 0.10), đổi số lượng/dropdown cập nhật gì.
8. **Mục 7** (575 token): 7.1 ràng buộc + giảm nhẹ; **7.2 ca bất thường liên logic — bắt buộc đủ 4 ca BTC nêu đích danh**: dữ liệu đổi giữa lúc hiển thị và lúc submit · gửi trùng · mở link hai lần · mail fail sau khi đã lưu; cộng ca miền giữ hàng (hai khách tranh SKU cuối, kho điều chỉnh khi đang hold, cọc thất bại, tài khoản bị khóa, SKU ngừng bán, hết hạn đúng lúc chuyển đơn); 7.3 lỗi hệ thống chung; **7.4 một bảng riêng gom điều chưa chốt** kèm giả định hiện tại.
9. **Mục 9** (200 token: sơ đồ hệ thống **Mermaid `flowchart LR`**, knowledge/33 §7.4, + bảng timing, xử lý khi tích hợp thất bại), **Mục 10** (175 token: field/perf/security/audit, ghi rõ số nào là giả định), **Mục 5** (100 token: **Mermaid `block-beta`** theo knowledge/33 §7.1 + 1–2 câu mô tả bố cục; ba trạng thái mặc định/lỗi/thành công ghi bằng bảng ngắn). **Bản nộp không có ảnh** — không dùng link Figma dù BTC nêu hình thức đó. Không bỏ trống mục nào.
9b. **Rà lại catch-all 1.x** theo khung knowledge/30 §2. Từng dòng đối chiếu RTM/log: khớp → giữ và ghi `(verify A-xx)`; RTM nói khác → sửa theo RTM; không verify được → giữ và gắn `[GIẢ ĐỊNH]`. Khai báo ký hiệu dùng trong spec ([a,b), →, KHL, K/T/N/L, ✓/✗) ở 0.x — ký hiệu là kỹ thuật tiết kiệm token lãi nhất (30 §5).
9c. **CỔNG F — chạy trước khi đếm token.** Với từng BR/EX và từng dòng catch-all, chấm 8 kiểm tra knowledge/32 §1 (bắt đầu bằng 6 lệnh grep ở cuối §1). Xử lý:
    - ✗ **F1** (hứa hộ bên ngoài) → viết lại theo mẫu 32 §3.1. Sửa ngay, không cần hỏi.
    - ✗ **F5** (phụ thuộc ngoài) → thêm luật thiếu theo mẫu 32 §3.2. Bốn luật catch-all phủ 8/12 ca M6 với ~230 token — làm trước mọi việc khác vì tỷ lệ chắn/token tốt nhất.
    - ✗ **F2** (hạn mức trang trí) → có câu trả lời C3 thì sửa theo C3; không có thì dùng luật bao quát 32 §3.3, **không chọn hộ**.
    - ✗ **F7** (làm hỏng mục tiêu, nhãn `⚡`) → **không sửa nội dung nghiệp vụ**. Viết thành một lượt nhị phân và giữ luật hiện tại; hết giờ hỏi thì áp quy tắc cứng số 2 điểm 3 (`[GIẢ ĐỊNH-MT]`).
    - ✗ F3/F4/F6/F8 → sửa nếu còn ngân sách token; không thì vào bảng tóm tắt.
    Rồi rà ba danh sách bước 0: mục tiêu nào chưa có BR, láng giềng nào chưa có luật lỗi, ca M6 nào còn ✗.
10. Ghi `spec.md`, rồi **sinh ngay bản nộp** `spec.nop.md` (bước 13) và **đếm token trên BẢN NỘP**, không trên `spec.md` (bản nội bộ dài hơn ~500 token vì mang `← A-xx`, nhãn `[M-x]`, `(verify …)`):
    - Lệnh: `LC_ALL=C.UTF-8 wc -w -m <thư-mục>/spec.nop.md` rồi `token ≈ max(số từ × 2,5 ; số ký tự / 2,2)`. Báo cả hai con số và giá trị lấy. **Không** dùng `wc` thiếu `LC_ALL=C.UTF-8`: locale `C` đếm sai ký tự đa byte (`—` `⇒` `→` `✓` `≥` `−` `§`).
    - Nếu giao diện nộp của BTC hiển thị token → số đó thắng; ghi tỷ lệ `token thật / số từ` vào `review.md` để lần đo sau dùng hệ số thật.
    - > 5.400 → sang chế độ nén rồi quay lại bước 11. Thứ tự hy sinh: **BR nhãn `[M-0]`** → câu nói về chính spec (40 §2 nhóm 22) → cắt theo knowledge/33 §6 (**mục 10 → 9 → 5 → 3 → 2**), và gộp BR trùng nội dung về đúng mục của nó. **Không cắt mục 1, 6, 7, 4, 8.** **Không cắt** 0.11–0.15 và không cắt luật cho ca M6 có tiền dính vào.
11. Tự kiểm nhanh: (a) Grep `BR-\d+|EX-\d+` — mã duy nhất, mọi "áp dụng BR-xx" tồn tại; (b) Grep blacklist knowledge/40 §2 nhóm 1, 3, 4, 5, 6 **và nhóm nội dung 17, 18, 19, 21, 22** trên bản nộp; (c) mọi dòng RTM có BR; (d) mọi mục tiêu M1 có BR; (e) nếu spec có khối ```mermaid thì §0.15 tồn tại.
12. Điền ngược cột "Mã BR" và "Trạng thái" (✅) vào `rtm.md` bằng Edit — cho **cả** dòng `A-xx` và `G-xx`.
13. Tạo bản nộp `spec.nop.md` = `spec.md` bỏ mọi dấu vết nội bộ (`← A-xx`, `← G-xx`, `(verify A-xx)`, nhãn `[M-x]`/`[⚡M-x]`, `[GIẢ ĐỊNH]`/`[GIẢ ĐỊNH-MT]` → **giữ nội dung luật, bỏ nhãn**) và bỏ mọi câu nói về chính spec (nhóm 22). Grep `A-\d`, `G-\d`, `M-\d`, `GIẢ ĐỊNH`, `ô đã điền` trên bản nộp phải = 0 hit. Chạy sớm ở bước 10 và chạy lại sau mỗi lần sửa.
14. In bảng tóm tắt 6 khối:
    - token (nội bộ / bản nộp, cả hai công thức) + đệm còn lại so với 6.000 | 10 mục có/thiếu | số BR/EX | số logic mục 6 và số case mỗi logic | dòng RTM đã phủ / tổng (tách `A-xx` và `G-xx`)
    - **Độ phủ mục tiêu**: mỗi mục tiêu M1 → BR phục vụ; mục tiêu trống = lỗi Cao
    - **Nhãn `⚡`**: mọi BR làm hỏng mục tiêu, kèm phát biểu đề xuất cho C5
    - **Cổng F**: đếm ✗ theo F1..F8, liệt kê ✗ ở F1/F2/F5/F7 (chặn nộp)
    - **Độ phủ suy biến**: 12 ca M6 → ✓ / ⛔ / ✗; 6 láng giềng M4 → có luật lỗi hay không
    - **XẾP HẠNG RỦI RO GIẢ ĐỊNH** — khối quan trọng nhất của bước này, vì nó là **nguồn của mọi lượt hỏi còn lại** (20 §3.4). Mọi dòng `G-xx` sắp theo rủi ro giảm dần, tính bằng ba tiêu chí: (1) đảo lại thì đổi kết quả quan sát được (tiền / trạng thái cuối / ai thắng) hay chỉ đổi diễn đạt; (2) Executor mù có đoán trùng giả định này không — **trùng mặc định ngành thì rủi ro thấp** (cả ta và Executor đoán như nhau), **ngược mặc định ngành thì rủi ro cao**; (3) có nằm trong core flow tiền/tồn không. Cột cuối: **câu hỏi nhị phân đã viết sẵn** (không phải phát biểu Đúng/Sai — nhị phân cùng giá mà không dẫn dắt) để dán vào `/elicit xac-nhan`. In 10 dòng đầu riêng thành một khối `→ HỎI TIẾP`.
    Ghi đồng thời cột "Rủi ro" vào `rtm.md`.

## Chế độ `nén`
1. Grep liệt kê toàn bộ mã BR/EX và dòng `←` trước khi sửa.
2. Áp knowledge/30 §5: ký hiệu thay cụm tiếng Việt · công thức thay câu · "áp dụng BR-xx" thay lặp · bảng ≥3 cột nội dung, bảng 2 cột đổi thành danh sách · dồn tác động phụ về §0.7 · luật tổng quát + ngoại lệ thay liệt kê · cắt §7 → §10 → §9 → §2. Không cắt §0, §3, §5, §6, §8.
3. Grep lại danh sách mã: phải bằng danh sách trước. In diff token trước/sau (cả hai công thức) và khẳng định "không mất BR".

## Output bắt buộc
- [ ] `spec.md` đủ **10 mục BTC**, không mục nào trống, mọi ô bảng có giá trị; bản nộp ≤5.400 token (đích), tuyệt đối ≤6.000.
- [ ] Mục 6: mỗi logic có bảng step + bảng Case đủ 3 loại case và 5 gạch; cột kết quả đủ 7 chiều.
- [ ] Mục 4: mọi message nguyên văn trong ngoặc kép, có cột FE/BE.
- [ ] Mục 2 và 8: phân biệt rõ Guest / login; mục 2 có điều kiện ẩn/disable.
- [ ] Mục 7.4: một bảng gom điều chưa chốt + giả định (bảng này **giữ trong bản nộp** — BTC yêu cầu).
- [ ] Sơ đồ Mermaid (mục 5, 6, 9) hợp lệ, mỗi cái kèm 1–2 câu chữ tóm tắt, nhãn có dấu bọc ngoặc kép.
- [ ] Mọi BR/EX có `← A-xx` hoặc `← G-xx`, và một nhãn `[M-x]` / `[⚡M-x]` / `[M-0]`.
- [ ] Catch-all 1.x có đủ 0.11–0.14 (+0.15 nếu có mermaid); mỗi láng giềng M4 có một luật cho chế độ lỗi.
- [ ] Cổng F đã chạy trên mọi BR; 0 ✗ ở F1 và F5.
- [ ] `rtm.md` cột Mã BR đã điền cho mọi dòng; cột Rủi ro đã điền cho mọi dòng `G-xx`.
- [ ] Bảng tóm tắt 6 khối, **gồm khối `→ C5` gồm 10 phát biểu Đúng/Sai đã viết sẵn**.
- [ ] `spec.nop.md` sạch dấu vết nội bộ, không có ảnh.

## Không được
- Chạy khi chưa có `mo-hinh-bai-toan.md`; gõ mục 6 khi RTM chưa có dòng `G-xx`.
- Viết luật không có dòng RTM nào; viết ngược với dòng `A-xx`.
- **Tự nghĩ ra giá trị cho ô không hỏi được** thay vì lấy mặc định ngành ở 20 §4 / 10 §6.
- Hứa mốc **hoàn tất** cho việc do cổng / ngân hàng / ERP thực hiện.
- Neo hạn mức vào dữ liệu khách tự khai khi chưa hỏi được đơn vị định danh — dùng luật bao quát 32 §3.3.
- **Tự sửa nội dung nghiệp vụ của một BR nhãn `⚡` để nó "hợp lý hơn"** khi vẫn còn nhịp để hỏi.
- Vẽ sơ đồ mermaid lặp lại nội dung bảng state × event 6.3; có mermaid mà thiếu dòng catch-all 0.15.
- Dùng từ trong danh sách đen knowledge/40 §2; tham chiếu tài liệu ngoài; để ô bảng trống.
- Để câu nói về chính spec, hoặc **nhãn** `[GIẢ ĐỊNH]`, trong bản nộp — nhưng **bảng 7.4 thì giữ**: BTC yêu cầu nêu điều chưa chốt kèm giả định hiện tại, viết bằng chữ thường ("giả định hiện tại: …"), không bằng nhãn nội bộ.
- Rải điều chưa chốt khắp bài thay vì gom vào bảng 7.4.
- Bỏ trống bất kỳ mục nào trong 10 mục; viết catch-all trước khi đọc xong RTM.
- Ghi "hiển thị thông báo lỗi phù hợp" thay vì message nguyên văn (mục 4).
- Cắt mục 1, 4, 6, 7, 8 để giảm token.
- Nộp khi token bản nộp > 6.000.
