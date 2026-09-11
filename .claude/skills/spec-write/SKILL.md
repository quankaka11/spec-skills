---
name: spec-write
description: Viết spec theo cấu trúc 10 mục BTC (基本設計 1–5 / 詳細設計 6–9 / 技術仕様 10) cho AI Executor đọc, từ mô hình bài toán + RTM ngược (dòng `A-xx` đã hỏi và dòng `G-xx` giả định) + log AI Khách hàng, mỗi luật có mã BR truy vết, gắn một mục tiêu và qua cổng khả thi F; ô không hỏi được thì điền bằng mặc định ngành có xếp hạng rủi ro; hoặc nén spec đang có về ≤5.400 token không mất luật. Dùng 10:20–11:52 ngày thi khi người dùng nói "viết spec", "điền template", "nén spec", "spec-write", "rút gọn spec".
argument-hint: "[thư-mục] [nén]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(LC_ALL=C.UTF-8 wc *), Bash(sed *)
---
# /spec-write — Viết spec 10 mục BTC từ mô hình bài toán + RTM

Mục tiêu: mọi dòng RTM (`A-xx` và `G-xx`) thành một luật có mã; mọi mục tiêu của brief có luật phục vụ; mọi luật đứng được trong thực tế (cổng F); đủ **10 mục BTC**, không mục nào trống, mọi ô bảng được điền; **bản nộp ≤5.400 token ước lượng** (hạn mức 6.000 — knowledge/00 §A); không bịa.

## Input
`$ARGUMENTS` = `[thư-mục] [nén]`. Thư mục mặc định `battle/`. Cần: `mo-hinh-bai-toan.md`, `rtm.md`, `log-khach-hang.md`, `brief.md`; có `spec.md` thì là bản đang sửa.

**Điều kiện vào (cứng, không phải khuyến nghị):** thiếu `mo-hinh-bai-toan.md` → **dừng, chạy `/frame` trước**. Bản 08/09 viết mà không có mô hình bài toán đạt mọi cổng hình thức nhưng có 7 lỗi nội dung mức Cao, 3 trong đó phá thẳng mục tiêu brief nêu (knowledge/05 §0).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` — **CẤU TRÚC BẮT BUỘC 10 mục BTC**: §1 nội dung & hình thức từng mục, §2 "ba thứ" của mỗi logic ở mục 6, §3 các quy tắc cũ đã đổi, §4 ánh xạ template cũ → mới, **§5 ngân sách token**, §6 thứ tự cắt, **§7 cú pháp Mermaid cho 4 sơ đồ** (wireframe, state machine, sequence, sơ đồ hệ thống). **Đọc file này TRƯỚC knowledge/30.**
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md`: §1 template 10 mục + **ngân sách token từng mục**, **§1b luật giả định = mặc định ngành**, §2 khung catch-all đặt ở mục 1.x (gồm **0.11–0.15**), §3 cú pháp EARS + mẫu BR-xx, §4 checklist **24 quy tắc** (1–17 cách viết, 18–24 nội dung), §5 kỹ thuật tiết kiệm **token**, §6 thứ tự viết 10:20–11:52, §7 cổng chất lượng **23 dòng**.
- `${CLAUDE_PROJECT_DIR}/knowledge/33-kha-thi-van-hanh.md` — **cổng F**: §1 tám kiểm F1–F8 + 6 lệnh grep, §2 bảng "ĐƯỢC hứa / KHÔNG được hứa" của phụ thuộc ngoài, §3 ba mẫu viết lại dán được, §4 chi phí ẩn, §6 chuyển hit F thành câu hỏi cho C5.
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md`: §2 bảng Mục tiêu↔Luật, §M1 mục tiêu & thước đo, §M4 6 láng giềng, §M6 12 kịch bản suy biến, §4 (ô mô hình không hỏi được thì xử lý thế nào).
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md`: **§4 31 phát biểu mặc định ngành — nguồn giá trị cho mọi ô không hỏi được**, §5 mẫu RTM có `A-xx` + `G-xx` + cột Rủi ro, §3 (5 câu C1–C5 và bảng "phần phải tự điền" ở cuối mục).
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §1 hit policy, §2 chứng minh đủ/rời, §3 bảng state × event và mã hiệu ứng K/T/N/L.
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §2 glossary (chỉ thuật ngữ có dùng trong spec), §6 catalogue ⚠ (nguồn mặc định ngành thứ hai).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 danh sách đen 22 nhóm (tự tránh khi viết).

## Quy tắc cứng

### 1 — Mọi luật có một dòng RTM
Mỗi BR/EX kết thúc bằng nguồn: `← A-07` (có câu trả lời của AI Khách hàng trong log) hoặc `← G-12` (mặc định ngành, có xếp hạng rủi ro). **Không có dòng RTM = không được viết**; thiếu dòng thì mở dòng `G-xx` mới trong `rtm.md` trước khi viết luật. Dòng RTM ⛔ (ngoài phạm vi) chỉ vào **mục 1 NGOÀI phạm vi**, không thành BR.

Với 5 câu hỏi, **60–80% BR sẽ là `G-xx`. Đó là bình thường, không phải lỗi quy trình** — điều phải tránh là giả định *ngược mặc định ngành* ở chỗ có tiền hoặc tồn.

### 2 — Giả định = mặc định ngành, không sáng tạo
Lý lẽ đầy đủ: knowledge/30 §1b. Bốn điểm áp dụng khi gõ:
- Giá trị cho ô không hỏi được **lấy từ 20 §4 (31 phát biểu) hoặc 10 §6**, không tự nghĩ ra. Viết mặc định ngành ra không xấu hơn im lặng ở bất kỳ ca nào, mà loại được đa nghĩa ở ca ghép; viết một giá trị *tự nghĩ ra* là ca duy nhất tệ hơn im lặng.
- Ba loại **không được tự chốt**: luật làm hỏng mục tiêu brief (nhãn `⚡`, cổng F7) · hạn mức không cưỡng chế được (F2) · giả định mà đảo lại thì đổi hướng tiền. Chúng đi vào câu C5, hoặc dùng luật an toàn hai chiều (knowledge/33 §3.3).
- Hết câu hỏi mà vẫn phải chọn → chọn **phương án phục vụ mục tiêu brief**, ghi `[GIẢ ĐỊNH-MT]`.
- **Nhãn `[GIẢ ĐỊNH]` khai tập trung ở mục 7.4, không rải trong bài** (30 §1b-5): BTC yêu cầu mục 7.4 gom "điều đang chờ xác nhận" kèm giá trị đang dùng, nên bản nộp *có* khai giả định — nhưng khai ở **7.4**. Còn **thân luật ở mục 6 viết dứt khoát, bỏ nhãn**: với Executor, một luật mang chữ "giả định" là một luật yếu, mời nó tự suy diễn.

### 3 — Mỗi luật gắn một mục tiêu
Nhãn nội bộ đặt cuối mỗi BR: `[M-1]` phục vụ mục tiêu 1 · `[⚡M-2]` **làm hỏng** mục tiêu 2 · `[M-0]` không thuộc mục tiêu nào. `⚡` là **lỗi Cao**: KHÔNG tự chốt, chuyển thành phát biểu cho câu C5 và ghi vào hàng đợi restate. `[M-0]` là **ứng viên cắt đầu tiên** khi thiếu token. Nhãn chỉ ở bản nội bộ.

### 4 — Không hứa hộ bên ngoài
Mọi nghĩa vụ do cổng thanh toán / ngân hàng / ERP / kênh thông báo thực hiện phải tách **mốc hệ thống quyết định (khởi tạo)** khỏi **mốc bên ngoài hoàn tất**, và phải có **nhánh thất bại** (mẫu dán được: knowledge/33 §3.1). Viết `hoàn tất ≤ N giờ` cho việc bên ngoài làm = lỗi Cao, chặn nộp.

## Bước (theo knowledge/30 §6, khung 10:20–11:52)

0. **Ba danh sách độ phủ, độc lập với RTM.** Đọc `mo-hinh-bai-toan.md`, chép ra và dùng suốt các bước sau: (a) **mục tiêu M1** — mỗi mục tiêu ≥1 BR trước khi nộp; (b) **6 láng giềng M4** — mỗi láng giềng một luật cho chế độ lỗi của nó; (c) **12 kịch bản suy biến M6** — mỗi ca ✓ hoặc ⛔ tường minh. RTM chỉ phủ những gì đã hỏi và đã dự kiến; ba danh sách này phủ những gì **bài toán** cần.

1. Đọc `rtm.md` + `log-khach-hang.md`. Xác định biến thể (cart hold / cọc / pickup) từ brief và câu trả lời C1/C3 (knowledge/10 §1). Liệt kê mọi dòng RTM chưa có Mã BR, **cả `A-xx` và `G-xx`**. Lập danh sách LOGIC sẽ viết ở mục 6 (knowledge/30 §1 mục 6 gợi ý 6.1–6.9).

1b. **Bổ khuyết RTM trước khi gõ.** Nếu `rtm.md` chưa có dòng `G-xx` (vì `/elicit nạp` chưa chạy bước sinh giả định): sinh ngay — mỗi ô mô hình còn `?` và mỗi nhóm ID ở cột phải bảng ánh xạ knowledge/20 §3 thành một dòng `G-xx` với giá trị mặc định ngành. Viết spec khi RTM chỉ có dòng `A-xx` là cách chắc chắn nhất để bỏ trắng ~70% bài toán.

1c. **Chọn dạng mục 6 TRƯỚC khi gõ chữ đầu tiên** theo tổng số dòng RTM (knowledge/30 §5): < 40 dòng → bảng 5 cột; **≥ 40 dòng → dạng danh sách** `**BR-xx** — luật. Cấm: … ← A-yy`, cộng dồn tác động phụ về catch-all 0.7. Mục 6 viết bằng bảng 5 cột với đề nhiều luật đã đo được **2.235 từ ≈ 5.600 token** — một mục ăn hết hạn mức của cả spec.

2. **Mục 1 trước:** tên hệ thống / màn hình, chức năng làm gì (4–5 bullet); TRONG/NGOÀI phạm vi **nguyên văn từ câu trả lời C1** (dòng ⛔ trong `rtm.md`) — không có C1 thì dùng danh sách NGOÀI của domain (knowledge/10) và ghi `[GIẢ ĐỊNH]`, đồng thời cảnh báo: buổi chiều mọi test của đội bị +1 điểm phạm vi (50 §6-5). Rồi **1.x catch-all** từ khung knowledge/30 §2 (12 dòng cơ bản **+ bắt buộc 0.11–0.14**; **+ 0.15 nếu spec có Mermaid**) — từng dòng đối chiếu RTM/log: khớp → ghi `(verify A-xx)`; RTM nói khác → sửa theo RTM; không verify được → giữ và gắn `[GIẢ ĐỊNH]` (khai lại ở 7.4). Khai báo ký hiệu (`[a,b)`, `→`, `KHL`, `K/T/N/L`, `✓/✗`) tại 1.x — ký hiệu là kỹ thuật tiết kiệm token lãi nhất. Glossary vào 1.y nếu ngắn.

3. **Mục 6 — lớn nhất (~1.980 token), viết ngay sau mục 1.** Thứ tự nạp dòng RTM: `A-xx` ⚠⚠ → `A-xx` ⚠ → `A-xx` còn lại → `G-xx` rủi ro cao → `G-xx` còn lại. Mỗi logic một mục con, mỗi logic đủ **ba thứ** (knowledge/32 §2):
   - (1) **bảng step**: ai làm gì / hệ thống phản hồi gì (chỉ trỏ mã BR, không chép lại luật);
   - (2)(3) bảng `Case · Điều kiện · Kết quả mong đợi · Mã · RTM` — **bắt buộc đủ 3 loại case: bình thường, biên, lỗi**; mỗi bảng đủ **5 gạch**: con số · toán tử `>` hay `≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng.
   - Cột "Kết quả mong đợi" đủ **7 chiều**: trạng thái cuối · kho · tiền · thông báo · **hiển thị gì** · **trạng thái nút** · **message**.
   - Mỗi dòng RTM = 1 mã BR theo mẫu knowledge/30 §3.3 (EARS, 6 thành phần, điều KHÔNG ĐƯỢC, *Fit*, `← A-xx`/`← G-xx`, nhãn `[M-x]`).
   - Logic "chuyển trạng thái hold" (6.3) = **bảng state × event điền 100% ô** (`→ ĐÍCH K/T/N/L (BR-xx)` / `KHL` / `Từ chối 0.5`), mã hiệu ứng khai một lần (knowledge/31 §3); tập trạng thái lấy từ câu trả lời C2, ô C2 không phủ → mặc định ngành + mở `G-xx`. Logic có ≥2 điều kiện → decision table hit policy U, đếm tổ hợp = tích (knowledge/31 §1–§2).
   - Logic 6.3 kèm **Mermaid `stateDiagram-v2`** (knowledge/32 §7.2) — sơ đồ KHÔNG thay bảng, vì ô `Từ chối 0.5` / `KHL` chỉ có trong bảng, mà đó mới là chỗ Executor đoán sai. Hết chỗ thì bỏ sơ đồ, giữ bảng. Cuối mục 6: **sequence diagram end-to-end bằng Mermaid `sequenceDiagram`** (§7.3), một sơ đồ cho luồng chính; nhánh lỗi để trong bảng Case.

4. **Mục 4 Validation & message**: bảng `No · FE/BE · Item · Nội dung check · Message`. **Message ghi nguyên văn trong ngoặc kép** — lấy từ log (câu trả lời C4 ý 9, nhóm N17); không có trong RTM → tự viết message cụ thể theo mặc định ngành và mở dòng `G-xx`, KHÔNG ghi "hiển thị thông báo lỗi phù hợp". Gồm cả rule nghiệp vụ (vượt tồn, hết hạn, trạng thái không hợp lệ), không chỉ rule format. Mặc định cột FE/BE khi không hỏi được = "Cả hai".

5. **Mục 8 Xác thực & phân quyền**: bảng actor bắt buộc có **Guest** và **System/Job**; ô không ✓ = ✗ theo catch-all 0.4 — vì 0.4 biến mọi im lặng thành CẤM, bảng phải phủ cả thao tác **chỉ-đọc** (xem hold của mình / mọi hold, xem audit log) và mọi hành động log nêu "được làm" (rà từng câu trả lời kiểu "Được: a, b, c / Không được: d"). Bảng 2: mỗi actor xác thực bằng gì; giữa các hệ thống, đường truyền nào xác thực bằng gì.

6. **Mục 2 Item màn hình**: bảng `No · 項目名 · コントロール · I/O · 必須 · 備考`, chia theo khu vực (header / danh sách sản phẩm / khối cọc / footer nút). Bắt buộc có **cột khác biệt login vs guest**, điều kiện **hiển thị / ẩn / disable**, giá trị mặc định, giới hạn, format, placeholder, text nút nguyên văn. Ô thiếu dữ kiện → `[GIẢ ĐỊNH — ..]` + dòng 7.4, **không để trống**.

7. **Mục 3 Event**: bảng `No · Event · Trigger · Xử lý · Ghi chú` — tối thiểu: mở màn hình lấy gì từ đâu, mỗi nút gọi xử lý/API nào, double-click (idempotency 0.10), đổi số lượng / dropdown cập nhật gì.

8. **Mục 7**: 7.1 ràng buộc kỹ thuật/nghiệp vụ + cách giảm nhẹ; **7.2 ca bất thường liên logic — bắt buộc đủ 4 ca BTC nêu đích danh**: dữ liệu đổi giữa lúc hiển thị và lúc submit · gửi trùng · mở link hai lần · mail fail sau khi đã lưu; cộng ca miền giữ hàng (tranh SKU cuối, kho điều chỉnh khi đang hold, cọc thất bại, tài khoản bị khóa, SKU ngừng bán, hết hạn đúng lúc chuyển đơn) dạng bảng `EX-xx`; 7.3 lỗi hệ thống chung (log gì / alert ai / message chung); **7.4 bảng riêng "điều đang chờ xác nhận"** `# · Điều chưa chốt · Giả định hiện tại đang dùng · Ảnh hưởng mục` — **gom MỌI `[GIẢ ĐỊNH]` vào đây**, không rải rác, và **giữ nguyên trong bản nộp**.

9. **Mục 9** (sơ đồ hệ thống bằng **Mermaid `flowchart LR`**, knowledge/32 §7.4, + bảng timing `Khi nào · Từ → Tới · Gửi/nhận · Field điều kiện · Thất bại thì làm gì`), **Mục 10** (bảng field, perf + giới hạn API, security, audit theo 0.7, idempotency — **ghi rõ số nào là giả định**), **Mục 5** (có link Figma thì dán; không có → **Mermaid `block-beta`** hoặc `flowchart TD`, §7.1, + bảng 3 trạng thái màn hình mặc định/lỗi/thành công) — viết gọn theo ngân sách knowledge/32 §5. **Không mục nào để trống.**

10. **CỔNG F — chạy trước khi đếm token.** Với từng BR/EX và từng dòng catch-all 1.x, chấm 8 kiểm tra knowledge/33 §1, **bắt đầu bằng 6 lệnh grep ở cuối §1** (F1 mốc hoàn tất · F2 neo hạn mức · F3 việc giao cho người · F5 phụ thuộc ngoài · F7 bảng Mục tiêu↔Luật · F8 guest/hoàn 100%). Xử lý **phân hóa theo loại hit**:
    - ✗ **F1** (hứa hộ bên ngoài) → **sửa ngay** theo mẫu knowledge/33 §3.1, không cần hỏi.
    - ✗ **F5** (phụ thuộc ngoài không thực tế) → **thêm luật thiếu** theo mẫu knowledge/33 §3.2. Bốn luật đặt ở catch-all phủ **8/12 ca M6 với ~230 token** — làm trước mọi việc khác vì tỷ lệ chắn/token tốt nhất trong kit.
    - ✗ **F2** (hạn mức trang trí) → có câu trả lời C3 thì sửa theo C3; không có thì dùng **luật bao quát** knowledge/33 §3.3 (`yêu cầu không kèm [đơn vị định danh] hợp lệ PHẢI bị từ chối`), **không chọn hộ**.
    - ✗ **F7** (làm hỏng mục tiêu, nhãn `⚡`) → **KHÔNG sửa nội dung nghiệp vụ**. Viết thành phát biểu cho câu C5 (knowledge/33 §6) và giữ luật hiện tại; nếu C5 đã gửi rồi thì áp quy tắc cứng 2 (`[GIẢ ĐỊNH-MT]`).
    - ✗ F3/F4/F6/F8 → sửa nếu còn ngân sách token; không thì ghi vào bảng tóm tắt như rủi ro đã biết.
    Rồi rà **ba danh sách bước 0**: mục tiêu nào chưa có BR, láng giềng M4 nào chưa có luật lỗi, ca M6 nào còn ✗.

11. Ghi `spec.md`, rồi **sinh ngay bản nộp** `spec.nop.md` (bước 13) và **đếm token trên BẢN NỘP**, không trên `spec.md` (bản nội bộ dài hơn ~500 token vì mang `← A-xx`/`← G-xx`, nhãn `[M-x]`, `(verify …)`):
    - Lệnh: `LC_ALL=C.UTF-8 wc -w -m <thư-mục>/spec.nop.md`, rồi `token ≈ max(số từ × 2,5 ; số ký tự / 2,2)`. Báo cả hai con số và giá trị lấy. **Không** dùng `wc` thiếu `LC_ALL=C.UTF-8`: locale `C` đếm sai ký tự đa byte (`—` `→` `✓` `≥` `§` `−`).
    - Nếu giao diện nộp của BTC hiển thị token → **số đó thắng mọi ước lượng**; ghi tỷ lệ `token thật / số từ` vào `review.md` để các lần đo sau dùng hệ số thật.
    - **> 5.400 token → sang chế độ `nén`** rồi quay lại bước 12. Sau 2 vòng nén mà vẫn trong (5.400, 6.000] thì chấp nhận và ghi rõ trong bảng tóm tắt; **> 6.000 là không được nộp**.

12. Tự kiểm nhanh: (a) đủ **10 mục**, không mục nào trống; (b) Grep `BR-\d+|EX-\d+` — mã duy nhất, mọi "áp dụng BR-xx" tồn tại; (c) Grep blacklist knowledge/40 §2 nhóm 1, 3, 4, 5, 6 **và nhóm nội dung 17, 18, 19, 21, 22** trên bản nộp, sửa hit; (d) mọi dòng RTM (`A-xx` + `G-xx`) có BR; (e) **mọi mục tiêu M1 có BR**; (f) mục 4 mọi message có ngoặc kép + cột FE/BE; (g) mục 2 và 8 có **Guest**; (h) mọi khối ```mermaid có nhãn tiếng Việt bọc ngoặc kép, ≤15 node, và có 1–2 câu chữ tóm tắt ngay dưới; (i) có Mermaid thì **catch-all 0.15 tồn tại**.

13. Tạo bản nộp `spec.nop.md` = `spec.md` bỏ mọi dấu vết nội bộ, bằng sed/Edit:
    - bỏ `← A-xx`, `← G-xx`, `(verify A-xx)`; bỏ nhãn mục tiêu `[M-x]` / `[⚡M-x]` / `[M-0]`; bỏ nhãn `[GIẢ ĐỊNH]` / `[GIẢ ĐỊNH-MT]` / `[GIẢ ĐỊNH — ..]` → **giữ nội dung luật, bỏ nhãn**;
    - bỏ mọi **câu nói về chính spec** (knowledge/40 §2 nhóm 22: "đã điền đủ 36/36 ô", "phân hoạch không chồng lấn") → chuyển sang `review.md`;
    - **GIỮ NGUYÊN bảng mục 7.4** — BTC yêu cầu khai điều chưa chốt kèm giả định hiện tại; đây là ngoại lệ duy nhất của việc bỏ nhãn giả định.
    - Kiểm: Grep `A-\d`, `G-\d`, `M-\d`, `GIẢ ĐỊNH`, `verify` trên bản nộp phải = 0 hit **ngoài mục 7.4**. Chạy sớm ở bước 11 và chạy lại sau mỗi lần sửa. `spec.md` nội bộ giữ nguyên để `/spec-review` và `/appeal` truy vết.

14. Điền ngược `rtm.md` bằng Edit: cột "Mã BR" và "Trạng thái" (✅) cho **cả** dòng `A-xx` và `G-xx`; cột **"Rủi ro"** cho mọi dòng `G-xx`.

15. **Khối XẾP HẠNG RỦI RO GIẢ ĐỊNH** — bước quan trọng nhất, vì nó là **nội dung câu hỏi cuối cùng còn lại (C5)**. Xếp mọi dòng `G-xx` theo rủi ro giảm dần, tính bằng **ba tiêu chí**:
    1. **Đảo lại có đổi kết quả quan sát được không?** (tiền / trạng thái cuối / ai thắng) hay chỉ đổi diễn đạt.
    2. **Executor mù có đoán trùng giả định này không?** — trùng mặc định ngành ⇒ rủi ro **thấp** (cả ta và Executor đoán như nhau); **ngược** mặc định ngành ⇒ rủi ro **cao**.
    3. **Có nằm trong core flow tiền / tồn không?**

    | # | Dòng `G-xx` | Giả định đang dùng | (1) Đảo lại đổi gì | (2) Executor đoán trùng? | (3) Core flow tiền/tồn | Rủi ro | **Phát biểu Đúng/Sai viết sẵn** |
    |---|---|---|---|---|---|---|---|

    Cột cuối là **phát biểu Đúng/Sai đã viết sẵn**, mỗi phát biểu có con số hoặc trạng thái cụ thể, dán thẳng được vào `/elicit` câu C5. **In 10 dòng đầu riêng thành một khối `→ C5`.**

16. In bảng tóm tắt 6 khối:
    - **Kích thước & truy vết**: token (nội bộ / bản nộp, cả hai công thức) + đệm còn lại so với 6.000 | 10 mục có/thiếu | số BR/EX | dòng RTM đã phủ / tổng (tách `A-xx` và `G-xx`) | số logic ở mục 6 và số case mỗi logic.
    - **Độ phủ mục tiêu**: mỗi mục tiêu M1 → BR phục vụ; mục tiêu trống = **lỗi Cao**.
    - **Nhãn `⚡`**: mọi BR làm hỏng mục tiêu, kèm phát biểu đề xuất cho C5.
    - **Cổng F**: đếm ✗ theo F1..F8, liệt kê ✗ ở F1/F2/F5/F7 (**chặn nộp**).
    - **Độ phủ suy biến**: 12 ca M6 → ✓ / ⛔ / ✗; 6 láng giềng M4 → có luật lỗi hay không.
    - **Xếp hạng rủi ro giả định** + khối `→ C5` (bước 15); và danh sách `[GIẢ ĐỊNH]` trong bảng 7.4; dòng RTM chưa thành BR (nếu có, kèm lý do).

### Mốc giờ (knowledge/30 §6, knowledge/00 §B)

| Phút | Việc |
|---|---|
| 10:20–10:30 | Bước 0, 1, 1b, 1c + **mục 1** (phạm vi + catch-all 1.x đủ 0.1–0.15) |
| 10:30–10:55 | **Mục 6** — bảng step + bảng Case (3 loại case, 5 gạch, 7 chiều) |
| 10:55–11:00 | **Mục 6.3** state machine, bảng state × event 100% ô |
| 11:00–11:05 | **Mục 4** message nguyên văn + **mục 8** actor (Guest, System/Job) |
| 11:05–11:15 | **Mục 2** item màn hình + **mục 3** event + **mục 7** (7.2 bốn ca BTC, 7.4 chưa chốt) |
| 11:15–11:20 | **Mục 9, 10, 5** gọn — đủ 10 mục |
| 11:20–11:30 | **Xếp hạng rủi ro giả định** (bước 15) → 8–10 phát biểu Đúng/Sai cho **câu C5**; gửi C5 |
| 11:30–11:40 | Vá spec theo mọi ý C5 trả lời "Sai"; dòng `G-xx` liên quan chuyển thành `A-xx` (⚠⚠) |
| 11:40–11:46 | **Cổng F** (bước 10) + bảng Mục tiêu↔Luật |
| 11:46–11:49 | Rà catch-all 1.x đối chiếu log; red team eval set **hai reader mù**; Ctrl+F blacklist 40 §2 |
| 11:49–11:52 | Đếm **token bản nộp**, đích ≤5.400; cắt theo knowledge/32 §6; nộp; lưu bản copy |

## Chế độ `nén`
1. Grep liệt kê toàn bộ mã BR/EX và dòng `←` **trước** khi sửa.
2. Thứ tự hy sinh: **BR nhãn `[M-0]`** (không thuộc mục tiêu nào) → câu nói về chính spec (40 §2 nhóm 22) → kỹ thuật knowledge/30 §5 (ký hiệu thay cụm tiếng Việt · công thức thay câu · "áp dụng BR-xx" thay lặp · bảng 2 cột đổi thành danh sách `**Khóa** — giá trị` · dồn tác động phụ về 0.7 · luật tổng quát + ngoại lệ thay liệt kê · mục 6 đổi bảng 5 cột → danh sách) → cắt **sơ đồ Mermaid** theo knowledge/32 §7.5 (wireframe → sơ đồ hệ thống → sequence → state machine), mỗi cái thay bằng 1–2 câu chữ, **không cắt bảng đi kèm**.
3. Vẫn thừa thì cắt **mục** theo knowledge/32 §6: **10 → 9 → 5 → 3 → 2**. **Không cắt mục 1, 6, 7, 4, 8.** Không cắt catch-all **0.11–0.15**; không cắt luật cho ca M6 có tiền dính vào; không bao giờ cắt **message nguyên văn** (mục 4) hay cột **Guest** (mục 2, 8).
4. Grep lại danh sách mã: phải bằng danh sách trước. In diff token trước/sau (cả hai công thức) và khẳng định "không mất BR".

## Output bắt buộc
- [ ] `spec.md` đủ **10 mục BTC**, mọi ô bảng có giá trị; bản nộp ≤**5.400 token** (đích), tuyệt đối ≤6.000.
- [ ] Mục 6: mỗi logic có bảng step + bảng Case đủ **3 loại case**, **5 gạch**, cột kết quả **7 chiều**.
- [ ] Mục 6.3: bảng state × event điền 100% ô, kể cả `Từ chối 0.5` / `KHL`.
- [ ] Mục 4: mọi **message nguyên văn** trong ngoặc kép, có cột FE/BE.
- [ ] Mục 2 và 8: có **Guest** / login phân biệt rõ; mục 8 có System/Job và thao tác chỉ-đọc.
- [ ] Mục 7.2: ≥4 ca bất thường BTC nêu đích danh. **Mục 7.4: một bảng gom điều chưa chốt + giả định** (giữ cả trong bản nộp).
- [ ] Sơ đồ **Mermaid** (mục 5, 6, 9) hợp lệ, mỗi cái kèm 1–2 câu chữ tóm tắt; có Mermaid thì catch-all **0.15** tồn tại.
- [ ] Mọi BR/EX có `← A-xx` hoặc `← G-xx`, và một nhãn `[M-x]` / `[⚡M-x]` / `[M-0]`.
- [ ] Catch-all 1.x có đủ **0.11–0.14**; mỗi láng giềng M4 có một luật cho chế độ lỗi.
- [ ] **Cổng F (knowledge/33 §1)** đã chạy trên mọi BR; 0 ✗ ở F1, F2, F5, F7.
- [ ] `rtm.md`: cột Mã BR đã điền cho mọi dòng; cột **Rủi ro** đã điền cho mọi dòng `G-xx`.
- [ ] Hai bản: `spec.md` (nội bộ, có `← A-xx`/`← G-xx` và nhãn `[M-x]`) và **`spec.nop.md`** (bản nộp, sạch dấu vết nội bộ, không có ảnh, giữ mục 7.4).
- [ ] Bảng tóm tắt 6 khối, **gồm khối `→ C5` với 10 phát biểu Đúng/Sai đã viết sẵn**.

## Không được
- Chạy khi chưa có `mo-hinh-bai-toan.md`; gõ mục 6 khi RTM chưa có dòng `G-xx`.
- Viết luật không có dòng RTM nào; viết ngược với dòng `A-xx`.
- **Tự nghĩ ra giá trị cho ô không hỏi được** thay vì lấy mặc định ngành ở knowledge/20 §4 / knowledge/10 §6.
- Hứa mốc **hoàn tất** cho việc do cổng / ngân hàng / ERP / kênh thông báo thực hiện.
- Neo hạn mức vào dữ liệu khách tự khai khi chưa hỏi được đơn vị định danh — dùng luật bao quát knowledge/33 §3.3.
- **Tự sửa nội dung nghiệp vụ của một BR nhãn `⚡` để nó "hợp lý hơn"** khi câu C5 chưa gửi.
- Ghi "hiển thị thông báo lỗi phù hợp" thay vì **message nguyên văn** (mục 4).
- Rải `[GIẢ ĐỊNH]` khắp bài mà không gom vào **bảng 7.4**; hoặc để nhãn `[GIẢ ĐỊNH]` trong thân luật mục 6 của bản nộp.
- Bỏ trống bất kỳ mục nào trong 10 mục; viết catch-all trước khi đọc xong RTM.
- Vẽ sơ đồ Mermaid lặp lại nội dung bảng state × event; có Mermaid mà thiếu catch-all 0.15.
- Cắt mục **1, 4, 6, 7, 8** để giảm token; cắt catch-all 0.11–0.15.
- Dùng từ trong danh sách đen knowledge/40 §2; tham chiếu tài liệu ngoài; để ô bảng trống.
- Để câu nói về chính spec (nhóm lint 22) trong bản nộp; đếm token trên `spec.md` thay vì `spec.nop.md`; dùng `wc` thiếu `LC_ALL=C.UTF-8`.
- Nộp khi token bản nộp > 6.000.
