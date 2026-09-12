---
name: spec-write
description: Viết spec cho AI Executor đọc từ mô hình bài toán + RTM (dòng đã hỏi và dòng giả định) + log AI Khách hàng, theo **cấu trúc 10 mục BTC** (基本設計/詳細設計/技術仕様) có mã BR truy vết, mỗi luật gắn một mục tiêu và qua cổng khả thi F; ô không hỏi được thì tra bảng mặc định của miền, tra không thấy thì KHÔNG tự điền; hoặc nén spec đang có về đúng hạn mức mà không mất luật. Dùng pha viết spec của ngày thi khi người dùng nói "viết spec", "điền template", "nén spec", "spec-write", "rút gọn spec".
argument-hint: "[thư-mục] [nén]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(LC_ALL=C.UTF-8 wc *), Bash(sed *)
---
# /spec-write — Viết spec từ mô hình bài toán + RTM

Mục tiêu: mọi dòng RTM thành một luật có mã; đủ **10 mục BTC**; mọi mục tiêu của brief có luật phục vụ; mọi luật đứng được trong thực tế; **bản nộp ≤ `0,90 × L`** với `L` = hạn mức đọc từ brief (00 §A3); **không bịa, và không điền ô chỉ vì nó đang trống**.

**Chế độ hỏng đổi chiều theo hạn mức — đo trước khi viết, đừng giả định.** Ở bước 10 tính `T_A` (token cho mọi luật có `A-xx` + khung 10 mục + catch-all + message nguyên văn):
- `T_A ≥ 0,75 × L` ⇒ **CHẬT**: không còn chỗ cho giả định. Lỗi hay gặp là **điền bừa**; câu tự vấn là *"câu này tôi lấy đâu ra?"*.
- `T_A < 0,75 × L` ⇒ **RỘNG**: còn đệm. Lỗi hay gặp là **dừng sớm**; câu tự vấn là *"còn ô nào trống?"* — nhưng tiêu đệm vẫn phải theo đúng thang bằng chứng, không phải điền cho đầy.

Ở **cả hai** chế độ, thứ tự đổ token là **thang bằng chứng 9 bậc** (knowledge/33 §5b). `L` chỉ quyết định cắt thang ở bậc nào. **Dừng ở bậc 6 vì hết hạn mức là một bản nộp hợp lệ**; vượt **đường đỏ** (giữa bậc 7 và 8) để cho spec đầy là lỗi Cao.

## Input
`$ARGUMENTS` = `[thư-mục] [nén]`. Thư mục mặc định `battle/`. Cần: `mo-hinh-bai-toan.md`, `rtm.md`, `log-khach-hang.md`, `brief.md`; có `spec.md` thì là bản đang sửa.

**Điều kiện vào:** thiếu `mo-hinh-bai-toan.md` → **dừng, chạy `/frame` trước**. Chốt cứng, không phải khuyến nghị: bản 08/09 viết mà không có mô hình bài toán, đạt mọi cổng hình thức nhưng có 7 lỗi nội dung mức Cao (knowledge/32 §7) — 3 trong đó phá thẳng mục tiêu brief nêu.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/33-cau-truc-spec-btc.md` — **CẤU TRÚC BẮT BUỘC 10 mục BTC**: §1 nội dung & hình thức từng mục, §2 "ba thứ" của mỗi logic ở mục 6, §3 các quy tắc cũ đã đổi, §4 ánh xạ template cũ → mới, §5 ngân sách token, §6 thứ tự cắt, **§7 sáu loại sơ đồ Mermaid** (§7.0 dùng cái nào ở đâu · §7.1 wireframe · §7.2 state machine · §7.3 sequence · §7.4 sơ đồ hệ thống · §7.5 **lưu đồ quyết định `flowchart TD`** · §7.6 erDiagram · **§7.7 lint 10 lỗi gãy render** · §7.8 ngân sách). **Đọc file này TRƯỚC knowledge/30.**
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md`: §1 template 10 mục chi tiết, §2 khung catch-all đặt ở mục 1.x (gồm 0.11–0.15), §3 cú pháp EARS + mẫu BR-xx, §4 checklist 17 quy tắc + 7 quy tắc nội dung (18–24), §1b luật giả định = mặc định ngành, §5 tiết kiệm token, §6 thứ tự viết, §7 cổng chất lượng 30 dòng.
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §2 (bảng Mục tiêu↔Luật), §M4 (6 láng giềng), §M6 (12 kịch bản suy biến), §4 (ô mô hình không hỏi được thì xử lý thế nào).
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md`: §1 cổng F, §2 bảng thực tế phụ thuộc ngoài, §3 ba mẫu viết lại, §4 chi phí ẩn, §6 hit F nào thành một lượt hỏi riêng.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §4b (dựng bảng mặc định cho **miền của đề này**), §4c (quét hằng số), **§4d (mười bốn ô "đề luôn chốt" — checklist lấp lỗ)**, §5 (RTM hai loại dòng), §3 bảng ánh xạ cuối mục. **Nguồn tra giá trị là `battle/mac-dinh-nganh.md`**, không phải §4 — §4 chỉ là ví dụ miền đặt giữ hàng.
- `${CLAUDE_PROJECT_DIR}/knowledge/33-cau-truc-spec-btc.md` **§5b thang bằng chứng 9 bậc + đường đỏ** — bất biến quyết định thứ tự đổ token ở mọi hạn mức.
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md` **§1c bốn cách biến bằng chứng thành khẳng định sai** — chạy trên từng luật trước khi đưa vào bản nộp.
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §1 hit policy, §2 chứng minh đủ/rời, §3 bảng state × event và mã hiệu ứng K/T/N/L.
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §2 glossary, §6 catalogue ⚠ — **chỉ dùng khi miền của đề đúng là "đặt giữ hàng"**.
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 danh sách đen 22 nhóm (tự tránh khi viết).

## Quy tắc cứng số 1 — mọi luật có một dòng RTM
Mỗi BR/EX kết thúc bằng nguồn: `← A-07` (có câu trả lời của AI Khách hàng) hoặc `← G-12 [GIẢ ĐỊNH]` (mặc định ngành, có xếp hạng rủi ro). **Không có dòng RTM = không được viết**; thiếu dòng thì mở dòng `G-xx` mới trong `rtm.md` trước khi viết luật. Dòng RTM ⛔ (ngoài phạm vi) chỉ vào §1 NGOÀI, không thành BR.

Kể cả khi kịp 15–20 lượt hỏi, **55–75% BR vẫn sẽ là `G-xx`. Đó là bình thường, không phải lỗi quy trình** — điều phải tránh là giả định *ngược mặc định ngành* ở chỗ có tiền hoặc tồn.

## Quy tắc cứng số 2 — giả định = mặc định ngành, không sáng tạo
Chi tiết và lý lẽ: knowledge/30 §1b. Ba điểm áp dụng khi gõ:
- Giá trị cho ô không hỏi được **tra `battle/mac-dinh-nganh.md`** (bảng dựng cho miền của đề này ở `/frame` bước 8b). Viết mặc định ngành ra không xấu hơn im lặng ở bất kỳ ca nào, mà loại được đa nghĩa ở ca ghép; còn viết một giá trị tự nghĩ ra là ca duy nhất tệ hơn im lặng.
- **Tra KHÔNG THẤY ⇒ không được viết một con số** (30 §1b-2b). Ba lối, không nhảy cóc: (a) còn nhịp ⇒ một lượt nhị phân chèn đầu hàng đợi; (b) hết nhịp ⇒ **luật an toàn hai chiều** — phát biểu quan hệ/thứ tự/hành vi khi lệch, không chốt con số (bậc 8, 33 §5b); (c) chỉ khi (a) và (b) đều không dùng được ⇒ một con số tự chọn **kèm** một dòng bảng 7.4 (bậc 9, dưới đường đỏ).
  *Thiếu file `mac-dinh-nganh.md` thì quy tắc này rỗng và người viết sẽ tự nghĩ ra số mà không biết mình đang nghĩ ra — đó là cơ chế đã làm hỏng 6 giá trị của bản thi thử 11/09. Không có file ⇒ chạy `/frame` bước 8b trước, đừng gõ mục 2 và mục 6.*
- Ba loại **không được tự chốt**: luật làm hỏng mục tiêu brief (nhãn `⚡`, F7) · hạn mức không cưỡng chế được (F2) · giả định đảo lại thì đổi hướng tiền. Chúng thành một lượt hỏi riêng (ưu tiên dạng nhị phân), hoặc dùng luật an toàn hai chiều (32 §3.3).
- Hết câu hỏi mà vẫn phải chọn → chọn **phương án phục vụ mục tiêu brief**, ghi `[GIẢ ĐỊNH-MT]`. Brief là căn cứ chung; phương án phá mục tiêu brief gần chắc không phải specs thật.

## Quy tắc cứng số 3 — mỗi luật gắn một mục tiêu
Nhãn nội bộ: `[M-1]` phục vụ mục tiêu 1 · `[⚡M-2]` làm hỏng mục tiêu 2 · `[M-0]` không thuộc mục tiêu nào. `⚡` là **lỗi Cao**: không tự chốt, chuyển thành một lượt nhị phân và chèn lên đầu hàng đợi hỏi còn lại. `[M-0]` là ứng viên cắt đầu tiên khi thiếu token. Nhãn chỉ ở bản nội bộ.

## Quy tắc cứng số 4 — không hứa hộ bên ngoài
Mọi nghĩa vụ do cổng thanh toán / ngân hàng / ERP / kênh thông báo thực hiện phải tách **mốc hệ thống quyết định** khỏi **mốc bên ngoài hoàn tất**, và phải có nhánh thất bại (mẫu dán được: knowledge/32 §3.1). Viết `hoàn tất ≤ N giờ` cho việc bên ngoài làm = lỗi Cao, chặn nộp.

## Bước (theo knowledge/30 §6)
0. Đọc `mo-hinh-bai-toan.md`. Chép ra ba danh sách dùng suốt các bước sau: (a) **mục tiêu M1** — mỗi mục tiêu ≥1 BR trước khi nộp; (b) **6 láng giềng M4** — mỗi láng giềng một luật cho chế độ lỗi; (c) **12 kịch bản suy biến M6** — mỗi ca ✓ hoặc ⛔. Ba danh sách này là checklist độ phủ **độc lập với RTM**: RTM chỉ phủ những gì đã hỏi và đã dự kiến, ba danh sách này phủ những gì bài toán cần.
1. Đọc `rtm.md` + `log-khach-hang.md`. Xác định biến thể (cart hold / cọc / pickup) từ brief và các lượt đã trả lời về phạm vi + tham số; lập danh sách LOGIC sẽ viết ở mục 6; chọn cụm luật cần nhiều token nhất theo knowledge/10 §1. Liệt kê mọi dòng RTM chưa có Mã BR, **cả `A-xx` và `G-xx`**.
1b. **Bổ khuyết RTM trước khi gõ.** Nếu `rtm.md` chưa có dòng `G-xx` (vì `/elicit nap` chưa chạy bước 5): sinh ngay — mỗi ô mô hình còn `?` và mỗi nhóm ID ở cột phải bảng ánh xạ 20 §3 thành một dòng `G-xx`: tra được ⇒ ghi giá trị; tra không thấy ⇒ ghi `KHÔNG BIẾT` (đừng điền số). Viết spec khi RTM chỉ có dòng `A-xx` là cách chắc chắn nhất để bỏ trắng 70% bài toán.
2. **Mục 1 trước** (9,5% của `L` — 33 §5): tên hệ thống / màn hình / chức năng, trạng thái tài liệu, chức năng làm gì — 4–5 bullet; TRONG/NGOÀI phạm vi **nguyên văn từ lượt hỏi về phạm vi** (20 §3.2 lượt 26 — dòng ⛔ trong `rtm.md`); lượt đó chưa gửi → dùng danh sách NGOÀI của domain (10) và ghi `[GIẢ ĐỊNH]`, đồng thời cảnh báo: buổi chiều mọi test của đội bị +1 điểm phạm vi (50 §6-5). Rồi **1.x catch-all** từ khung knowledge/30 §2 (12 dòng cơ bản **+ bắt buộc 0.11–0.14**; **+ 0.15 nếu spec có sơ đồ mermaid**) và **1.y glossary** nếu ngắn — chỉ thuật ngữ dễ hiểu sai, một khái niệm một tên.
3. **Mục 6 — lớn nhất (35,5% của `L`), viết ngay sau mục 1.** Mỗi logic một mục con (danh sách gợi ý 6.1–6.9 ở knowledge/30 §1), mỗi logic đủ **ba thứ** (knowledge/33 §2):
   - (1) bảng step: ai làm gì / hệ thống phản hồi gì;
   - (2)(3) bảng `Case · Điều kiện · Kết quả mong đợi · Mã · RTM` — **bắt buộc đủ 3 loại case: bình thường, biên, lỗi**, số case mỗi logic **tỷ lệ với ngân sách**: ≥5 case khi mục 6 có ≥2.000 token, ≥3 case (1 bình thường + 1 biên + 1 lỗi) khi chật hơn; mỗi bảng đủ **5 gạch**: con số · toán tử `>` hay `≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng.
   - Cột "Kết quả mong đợi" đủ **7 chiều**: trạng thái cuối · kho · tiền · thông báo · **hiển thị gì** · **trạng thái nút** · **message**.
   - Thứ tự viết luật: `A-xx` ⚠⚠ → `A-xx` ⚠ → `A-xx` còn lại → `G-xx` rủi ro cao → `G-xx` còn lại. Mỗi dòng RTM = 1 mã BR theo mẫu knowledge/30 §3.3 (EARS, 6 thành phần, KHÔNG ĐƯỢC, Fit, `← A-xx`/`← G-xx`) và một nhãn mục tiêu `[M-x]`.
   - Logic `6.1`/`6.2` mang công thức tồn kho (ATP, cột nào đổi khi tạo/hết hạn/hủy/chuyển đơn, giá khóa hay tính lại, giới hạn số lượng) — dữ kiện tối thiểu ở knowledge/30 §1 mục 6.
   - Logic "chuyển trạng thái hold" (`6.3`) = bảng state × event điền 100% ô (`→ ĐÍCH K/T/N/L (BR-xx)` / `KHL` / `Từ chối 0.5`), mã hiệu ứng khai báo một lần (knowledge/31 §3). **Không còn một câu hỏi nào trả về cả bảng** (câu gộp bị từ chối — 20 §1), nên bảng ghép từ bốn nguồn theo knowledge/30 §1 mục 6: các lượt nhị phân đã trả lời về vòng đời (`A-xx`) → mặc định ngành (`G-xx`) → catch-all 0.5 cho mọi ô không có luật riêng → catch-all 0.4 cho ô bị cấm theo quyền. Điển hình chỉ **4–8 ô có `A-xx`**; đó là bình thường, ô trống thì không. Logic ≥2 điều kiện → decision table hit policy U, đếm tổ hợp = tích (knowledge/31 §1–§2).
   - Logic `6.3` kèm **Mermaid `stateDiagram-v2`** (knowledge/33 §7.2) — sơ đồ KHÔNG thay bảng state × event, vì ô `Từ chối 0.5` / `KHL` chỉ có trong bảng. Cuối mục 6: **`sequenceDiagram` end-to-end** (§7.3) — **BTC yêu cầu tường minh sơ đồ này**. Logic `6.9` mang bảng mốc thông báo.
   - **Logic nhiều nhánh nhất kèm một lưu đồ `flowchart TD`** (knowledge/33 §7.5). Kiểm hai điều sau khi vẽ: (a) mọi node điều kiện có **đủ cả hai nhánh**, không nhánh nào cụt — nhánh cụt = một case còn thiếu trong bảng; (b) **thứ tự kiểm trên lưu đồ khớp** thứ tự trong bảng Case và cột FE/BE của mục 4. Lệch nhau là mâu thuẫn nội tại (loại #5).
4. **Mục 4 Validation & message** (11% của `L`; mỗi nhánh từ chối trong mục 6 phải có một dòng message ở đây): bảng `No · FE/BE · Item · Nội dung check · Message`. **Message ghi nguyên văn trong ngoặc kép** — lấy từ RTM (câu trả lời N17-*); không có trong RTM → **chèn ngay một lượt hỏi mở cho message đó** (20 §3b: message nguyên văn là vùng duy nhất không có mặc định ngành, và giờ hỏi được); hết giờ hỏi thì viết message cụ thể và mở dòng `G-xx` rủi ro **Cao**, KHÔNG ghi "hiển thị thông báo lỗi phù hợp". Có cả rule nghiệp vụ, không chỉ rule format.
   **Không suy rộng bảng mã lỗi (30 §1c-4).** Biết một mã ứng với một điều kiện **không** cho biết các mã còn lại ứng với gì, cũng không cho biết danh sách điều kiện có mấy dòng. Ánh xạ mã ↔ điều kiện là dữ liệu tuỳ tiện của khách, **không có mặc định ngành**, và specs thật thường có một điều kiện ta không biết là tồn tại.
   - Còn nhịp ⇒ hỏi **từng mã một** (`<mã> ứng với điều kiện nào?` — một ý, qua được cổng gộp-ý).
   - Hết nhịp ⇒ bậc 8: viết **thứ tự kiểm bằng lời** + luật "chỉ hiện một lỗi, lỗi của điều kiện sớm nhất", và **chỉ gắn mã cho những điều kiện đã có `A-xx`**. Điều kiện chưa hỏi thì mô tả bằng lời, không đánh số mã.
5. **Mục 8 Xác thực & phân quyền** (5% của `L`): bảng actor bắt buộc có **Guest** và **System/Job**; ô không ✓ = ✗ theo 0.4.
   **Giới hạn của 0.4 (30 §1c-1):** 0.4 biến im lặng thành CẤM cho **hành động của hệ thống**, KHÔNG cho **ai được vào màn hình nào**. Actor nào nằm trong danh sách NGOÀI phạm vi ở mục 1 thì mục 8 **không viết luật cấm họ** — viết cấm cũng là một khẳng định về họ, và specs thật thường cho họ tồn tại với đúng một hành vi nhỏ. Ngoài phạm vi ⇒ im lặng, không phải cấm. Vì 0.4 biến mọi im lặng thành CẤM, bảng phải phủ cả thao tác chỉ-đọc (xem hold của mình / mọi hold, xem audit log) và mọi hành động log nêu "được làm". Bảng 2: mỗi actor xác thực bằng gì.
6. **Mục 2 Item màn hình** (9,5% của `L`): bảng `No · 項目名 · コントロール · I/O · 必須 · 備考`, chia theo khu vực. Bắt buộc có **khác biệt login vs guest**, điều kiện **hiển thị/ẩn/disable**, giá trị mặc định, giới hạn, format, text nút nguyên văn. Ô không có dữ kiện RTM → `[GIẢ ĐỊNH — ..]`, không để trống.
7. **Mục 3 Event** (5,5% của `L` — đủ chỗ cho cả event hệ thống: job hết hạn, webhook kho, callback cổng, không chỉ event người bấm): bảng `No · Event · Trigger · Xử lý · Ghi chú` — tối thiểu: mở màn hình lấy gì từ đâu, mỗi nút gọi xử lý gì, double-click (chặn theo 0.10), đổi số lượng/dropdown cập nhật gì.
8. **Mục 7** (10% của `L`; 7.2 nhắm 8–10 ca khi còn đệm, tối thiểu 4 ca BTC nêu đích danh khi chật): 7.1 ràng buộc + giảm nhẹ; **7.2 ca bất thường liên logic — bắt buộc đủ 4 ca BTC nêu đích danh**: dữ liệu đổi giữa lúc hiển thị và lúc submit · gửi trùng · mở link hai lần · mail fail sau khi đã lưu; cộng ca miền giữ hàng (hai khách tranh SKU cuối, kho điều chỉnh khi đang hold, cọc thất bại, tài khoản bị khóa, SKU ngừng bán, hết hạn đúng lúc chuyển đơn); 7.3 lỗi hệ thống chung; **7.4 một bảng riêng gom điều chưa chốt** kèm giả định hiện tại.
9. **Mục 9** (6% của `L`: sơ đồ hệ thống **Mermaid `flowchart LR`** — **BTC yêu cầu tường minh** — knowledge/33 §7.4, + bảng timing + **bảng API** `API · Trigger · Payload · Response OK · Response lỗi → làm gì`), **Mục 10** (4% của `L`: field/perf/security/audit, ghi rõ số nào là giả định; `erDiagram` §7.6 nếu quan hệ dữ liệu không hiển nhiên), **Mục 5** (4% của `L`: **Mermaid `block-beta`** theo knowledge/33 §7.1 + 1–2 câu mô tả bố cục + **bảng ba trạng thái** mặc định/lỗi/thành công với cột Hiện gì · Nút chính · Message). **Bản nộp không có ảnh** — không dùng link Figma dù BTC nêu hình thức đó. Không bỏ trống mục nào.
9b. **Rà lại catch-all 1.x** theo khung knowledge/30 §2. Từng dòng đối chiếu RTM/log: khớp → giữ và ghi `(verify A-xx)`; RTM nói khác → sửa theo RTM; không verify được → giữ và gắn `[GIẢ ĐỊNH]`. Khai báo ký hiệu dùng trong spec ([a,b), →, KHL, K/T/N/L, ✓/✗) ở 0.x — ký hiệu là kỹ thuật tiết kiệm token lãi nhất (30 §5).
9c. **CỔNG F — chạy trước khi đếm token.** Với từng BR/EX và từng dòng catch-all, chấm 8 kiểm tra knowledge/32 §1 (bắt đầu bằng 6 lệnh grep ở cuối §1). Xử lý:
    - ✗ **F1** (hứa hộ bên ngoài) → viết lại theo mẫu 32 §3.1. Sửa ngay, không cần hỏi.
    - ✗ **F5** (phụ thuộc ngoài) → thêm luật thiếu theo mẫu 32 §3.2. Bốn luật catch-all phủ 8/12 ca M6 với ~230 token — làm trước mọi việc khác vì tỷ lệ chắn/token tốt nhất.
    - ✗ **F2** (hạn mức trang trí) → có câu trả lời về đơn vị neo hạn mức (20 §3.2 lượt 2) thì sửa theo đó; không có thì dùng luật bao quát 32 §3.3, **không chọn hộ**.
    - ✗ **F7** (làm hỏng mục tiêu, nhãn `⚡`) → **không sửa nội dung nghiệp vụ**. Viết thành một lượt nhị phân và giữ luật hiện tại; hết giờ hỏi thì áp quy tắc cứng số 2 điểm 3 (`[GIẢ ĐỊNH-MT]`).
    - ✗ F3/F4/F6/F8 → sửa nếu còn ngân sách token; không thì vào bảng tóm tắt.
    Rồi rà ba danh sách bước 0: mục tiêu nào chưa có BR, láng giềng nào chưa có luật lỗi, ca M6 nào còn ✗.
10. Ghi `spec.md`, rồi **sinh ngay bản nộp** `spec.nop.md` (bước 13) và **đếm token trên BẢN NỘP**, không trên `spec.md` (bản nội bộ dài hơn vì mang `← A-xx`, nhãn `[M-x]`, `(verify …)`).
    **10a. Đo chế độ trước khi bàn tới cắt hay tiêu.** Tính `T_A` = token của (mọi luật có `A-xx` + khung 10 mục + catch-all bắt buộc + message nguyên văn). `T_A ≥ 0,75 × L` ⇒ **CHẬT**; `T_A < 0,75 × L` ⇒ **RỘNG** (00 §A3). In con số và chế độ; mọi quyết định ở 10b/10c phụ thuộc vào nó.
    **10b. In vị trí hiện tại trên thang bằng chứng 9 bậc** (33 §5b): đang dừng ở bậc nào, bậc kế tiếp tốn bao nhiêu token, và **có bậc nào dưới đường đỏ đã lỡ viết chưa**. Có luật ở bậc 9 mà chưa hết nhịp hỏi, hoặc chưa thử bậc 8 ⇒ **lỗi Cao, hạ xuống bậc 8 ngay**.
    Cách đo:
    - Lệnh: `LC_ALL=C.UTF-8 wc -w -m <thư-mục>/spec.nop.md` rồi `token ≈ max(số từ × 2,5 ; số ký tự / 2,2)`. Báo cả hai con số và giá trị lấy. **Không** dùng `wc` thiếu `LC_ALL=C.UTF-8`: locale `C` đếm sai ký tự đa byte (`—` `⇒` `→` `✓` `≥` `−` `§`).
    - Nếu giao diện nộp của BTC hiển thị token → số đó thắng; ghi tỷ lệ `token thật / số từ` vào `review.md` để lần đo sau dùng hệ số thật.
    - **> `0,90 × L`** → sang chế độ nén rồi quay lại bước 11. Thứ tự hy sinh: **BR nhãn `[M-0]`** → câu nói về chính spec (40 §2 nhóm 22) → cắt theo knowledge/33 §6 (**mục 10 → 9 → 5 → 3 → 2**), và gộp BR trùng nội dung về đúng mục của nó. **Không cắt mục 1, 6, 7, 4, 8.** **Không cắt** 0.11–0.15 và không cắt luật cho ca M6 có tiền dính vào.
    - **< `0,75 × L` VÀ chế độ RỘNG** → còn đệm; tiêu **theo thang bằng chứng 33 §5b từ bậc đang dừng đi xuống**, dừng lại ở đường đỏ. Thứ tự thực dụng: message nguyên văn cho mọi nhánh từ chối chưa có (bậc 3) → case lỗi/biên **phái sinh từ luật đã có `A-xx`** (bậc 4) → `G-xx` tra được, rủi ro Thấp (bậc 5) → ba sơ đồ BTC (bậc 6) → `G-xx` tra được, rủi ro Trung (bậc 7) → **DỪNG**. In rõ đã tiêu vào đâu và token trước/sau.
    - **< `0,75 × L` VÀ chế độ CHẬT** → **bình thường, không phải lỗi.** Spec ngắn vì bằng chứng ít, không vì lười. Không tiêu đệm, không điền ô cho đầy.
11. Tự kiểm nhanh: (a) Grep `BR-\d+|EX-\d+` — mã duy nhất, mọi "áp dụng BR-xx" tồn tại; (b) Grep blacklist knowledge/40 §2 nhóm 1, 3, 4, 5, 6 **và nhóm nội dung 17, 18, 19, 21, 22** trên bản nộp; (c) mọi dòng RTM có BR; (d) mọi mục tiêu M1 có BR; (e) **khối sơ đồ**: đếm ```` ```mermaid ```` phải ≥ 3 (mục 5, 6, 9 — BTC yêu cầu); §0.15 tồn tại; chạy **lint 10 dòng knowledge/33 §7.7** trên từng khối (ngoặc kép trong `stateDiagram`/`sequenceDiagram`, `-->` dùng nhầm trong sequence, id có dấu, thiếu `end`, `\n` thay `<br/>`); mỗi sơ đồ có 1–2 câu tóm tắt ngay dưới.
12. Điền ngược cột "Mã BR" và "Trạng thái" (✅) vào `rtm.md` bằng Edit — cho **cả** dòng `A-xx` và `G-xx`.
13. Tạo bản nộp `spec.nop.md` = `spec.md` bỏ mọi dấu vết nội bộ (`← A-xx`, `← G-xx`, `(verify A-xx)`, nhãn `[M-x]`/`[⚡M-x]`, `[GIẢ ĐỊNH]`/`[GIẢ ĐỊNH-MT]` → **giữ nội dung luật, bỏ nhãn**) và bỏ mọi câu nói về chính spec (nhóm 22). Grep `A-\d`, `G-\d`, `M-\d`, `GIẢ ĐỊNH`, `ô đã điền` trên bản nộp phải = 0 hit. Chạy sớm ở bước 10 và chạy lại sau mỗi lần sửa.
14. In bảng tóm tắt 6 khối:
    - token (nội bộ / bản nộp, cả hai công thức) + `L` + đệm còn lại so với `L` + **chế độ (CHẬT/RỘNG) và `T_A`** + **bậc đang dừng trên thang 33 §5b** | 10 mục có/thiếu + token thực tế từng mục so với ngân sách knowledge/33 §5 | số BR/EX | số logic mục 6 và **số case mỗi logic (logic nào < 5 case)** | số sơ đồ mermaid | dòng RTM đã phủ / tổng (tách `A-xx` và `G-xx`)
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
- [ ] `spec.md` đủ **10 mục BTC**; bản nộp **≤ `0,90 × L` (đích), tuyệt đối ≤ `L`**. Ngắn hơn `0,75 × L` phải kèm **một trong hai**: chế độ CHẬT, hoặc bằng chứng đã đổ tới đường đỏ của thang 33 §5b.
- [ ] Mục 6: mỗi logic có bảng step + bảng Case đủ 3 loại case (số case tỷ lệ ngân sách: ≥5 khi rộng, ≥3 khi chật) và 5 gạch; cột kết quả đủ 7 chiều.
- [ ] Mục 4: mọi message nguyên văn trong ngoặc kép, có cột FE/BE.
- [ ] Mục 2 và 8: phân biệt rõ Guest / login; mục 2 có điều kiện ẩn/disable.
- [ ] Mục 7.4: một bảng gom điều chưa chốt + giả định (bảng này **giữ trong bản nộp** — BTC yêu cầu).
- [ ] Ba sơ đồ BTC yêu cầu (mục 5 · mục 6 · mục 9) **nếu đã đổ tới bậc 6** của thang 33 §5b; chưa tới bậc 6 vì hết hạn mức thì ghi rõ trong bảng tóm tắt. Lưu đồ `flowchart TD` cho logic nhiều nhánh nhất; mỗi sơ đồ kèm 1–2 câu tóm tắt; đã chạy lint knowledge/33 §7.7.
- [ ] Mọi BR/EX có `← A-xx` hoặc `← G-xx`, và một nhãn `[M-x]` / `[⚡M-x]` / `[M-0]`.
- [ ] Catch-all 1.x có đủ 0.11–0.14 (+0.15 nếu có mermaid); mỗi láng giềng M4 có một luật cho chế độ lỗi.
- [ ] Cổng F đã chạy trên mọi BR; 0 ✗ ở F1 và F5.
- [ ] `rtm.md` cột Mã BR đã điền cho mọi dòng; cột Rủi ro đã điền cho mọi dòng `G-xx`.
- [ ] **Mọi con số trong bản nộp trỏ về một `A-xx` hoặc một `G-xx`** — chạy quét hằng số (20 §4c) trên chính `spec.nop.md`, không trên RTM.
- [ ] **Mười bốn ô "đề luôn chốt" (20 §4d) đều có luật trong bản nộp** — mỗi ô một dòng: `Đ1 bội số ô nhập số · Đ2 trần/sàn ô nhập · Đ3 thời điểm xét điều kiện · Đ4 phép so dùng tổng nào · Đ5 tập con bị loại trừ · Đ6 phụ phí vẫn thu khi đã miễn · Đ7 danh sách mã lỗi đủ + thứ tự · Đ8 gỡ/hoàn tác trước khi chốt · Đ9 lặp lại đúng thao tác đã làm · Đ10 message có kèm con số không · Đ11 trạng thái rỗng · Đ12 thời hạn lưu dữ liệu tạm · Đ13 chuẩn hoá đầu vào · Đ14 phân hạng & mốc riêng`. Ô nào không hỏi được thì tra `mac-dinh-nganh.md` và mở `G-xx`; tra không thấy ⇒ ghi `KHÔNG BIẾT` theo 30 §1b-2b. **Im lặng ở mười bốn ô này không trung lập** — Executor mù lấp bằng mặc định ngành, và đây đúng là chỗ đề hay đặt luật ngược mặc định (thi thử 11/09: 5/9 ô bỏ trống có luật phản trực giác).
- [ ] Đã chạy **30 §1c** trên từng luật: không biến NGOÀI phạm vi thành lệnh cấm · không có luật rộng hơn danh từ đã hỏi · không có con số dựa trên "không có quy định riêng" · không có mã lỗi suy rộng.
- [ ] Bảng tóm tắt 6 khối, **gồm khối `→ HỎI TIẾP` gồm 10 câu hỏi nhị phân đã viết sẵn**.
- [ ] `spec.nop.md` sạch dấu vết nội bộ, không có ảnh.

## Không được
- Chạy khi chưa có `mo-hinh-bai-toan.md`; gõ mục 6 khi RTM chưa có dòng `G-xx`.
- Viết luật không có dòng RTM nào; viết ngược với dòng `A-xx`.
- **Tự nghĩ ra giá trị cho ô không hỏi được** thay vì tra `battle/mac-dinh-nganh.md`; và **tra không thấy mà vẫn điền một con số** thay vì đi nhánh 30 §1b-2b.
- **Viết một luật CẤM cho actor / đối tượng nằm trong danh sách NGOÀI phạm vi** (30 §1c-1).
- **Viết một luật rộng hơn danh từ của câu hỏi đã sinh ra nó** (30 §1c-2).
- **Gắn mã lỗi cho một điều kiện chưa có `A-xx`** (30 §1c-4).
- Gõ mục 2 hoặc mục 6 khi chưa có `battle/mac-dinh-nganh.md`.
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
- Nộp khi token bản nộp > `L`.
- **Ở chế độ RỘNG:** dừng dưới `0,75 × L` trong khi thang bằng chứng còn bậc chưa đổ và chưa chạm đường đỏ. Chỗ trống là chỗ Executor đoán.
- **Ở chế độ CHẬT:** điền thêm ô cho spec "đủ đầy" bằng giá trị dưới đường đỏ. Chỗ bịa là chỗ đối thủ ăn điểm chắc — tệ hơn chỗ trống.
- Bỏ một trong ba sơ đồ BTC yêu cầu tường minh (mục 5, 6, 9) khi token còn dưới `0,90 × L` **và** thang bằng chứng đã đổ qua bậc 6.
