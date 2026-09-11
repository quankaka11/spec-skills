---
name: spec-write
description: Viết spec "Đặt giữ hàng" ≤3.000 từ cho AI Executor đọc, từ RTM ngược và log AI Khách hàng, theo cấu trúc 10 mục BTC yêu cầu (基本設計/詳細設計/技術仕様) có mã BR truy vết; hoặc nén spec đang có về ≤3.000 từ không mất luật. Dùng 11:00–11:45 ngày thi khi người dùng nói "viết spec", "điền template", "nén spec", "spec-write", "rút gọn còn 3000 từ".
argument-hint: "[thư-mục] [nén]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(wc *)
---
# /spec-write — Viết spec từ RTM

Mục tiêu: mọi dữ kiện ⚠ trong RTM thành một luật có mã trong spec; đủ **10 mục BTC**; mọi ô bảng được điền; ≤3.000 từ; không bịa.

## Input
`$ARGUMENTS` = `[thư-mục] [nén]`. Thư mục mặc định `battle/`. Cần: `rtm.md`, `log-khach-hang.md`, `brief.md`; nếu có `spec.md` thì là bản đang sửa.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` — **CẤU TRÚC BẮT BUỘC 10 mục BTC**: §1 nội dung & hình thức từng mục, §2 "ba thứ" của mỗi logic ở mục 6, §3 các quy tắc cũ đã đổi, §4 ánh xạ template cũ → mới, §5 ngân sách từ, §6 thứ tự cắt, **§7 cú pháp Mermaid cho 4 sơ đồ** (wireframe, state machine, sequence, sơ đồ hệ thống). **Đọc file này TRƯỚC knowledge/30.**
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md`: §1 template 10 mục chi tiết, §2 khung catch-all (đặt ở mục 1.x), §3 cú pháp EARS + mẫu BR-xx, §4 checklist 17 quy tắc, §5 tiết kiệm từ, §6 thứ tự viết, §7 cổng chất lượng 18 dòng.
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §1 hit policy, §2 chứng minh đủ/rời, §3 bảng state × event và mã hiệu ứng K/T/N/L.
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §2 glossary (chỉ lấy thuật ngữ có dùng trong spec).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 danh sách đen (để tự tránh khi viết).

## Quy tắc cứng số 1 — truy vết
Mỗi BR/EX kết thúc bằng nguồn: `← A-07` (dòng RTM) hoặc `[GIẢ ĐỊNH]` (không có trong RTM). Không có nguồn = không được viết. Dòng RTM ⛔ (ngoài phạm vi) chỉ vào mục 1 NGOÀI phạm vi, không thành BR.

## Bước (theo knowledge/30 §6)
1. Đọc `rtm.md` + `log-khach-hang.md`. Xác định biến thể (cart hold / cọc / pickup) từ dòng N1-01. Liệt kê mọi dòng ⚠ chưa có Mã BR. Lập danh sách LOGIC sẽ viết ở mục 6 (knowledge/30 §1 mục 6 gợi ý 6.1–6.9).
2. **Mục 1** trước: tên hệ thống/màn hình, chức năng làm gì (4–5 bullet); TRONG/NGOÀI phạm vi nguyên văn từ lượt 1 (dòng ⛔ trong rtm.md); rồi **1.x catch-all** từ khung knowledge/30 §2 — từng dòng đối chiếu RTM/log: khớp → ghi `(verify A-xx)`; RTM nói khác → sửa theo RTM; không verify được → giữ và gắn `[GIẢ ĐỊNH]`. Khai báo ký hiệu ([a,b), →, KHL, K/T/N/L, ✓/✗) tại 1.x. Glossary vào 1.y nếu ngắn.
3. **Mục 6 — lớn nhất (1.100 từ), viết ngay sau mục 1.** Mỗi logic một mục con, mỗi logic đủ **ba thứ** (knowledge/32 §2):
   - (1) bảng step: ai làm gì / hệ thống phản hồi gì;
   - (2)(3) bảng `Case · Điều kiện · Kết quả mong đợi · Mã · RTM` — **bắt buộc đủ 3 loại case: bình thường, biên, lỗi**; mỗi bảng đủ **5 gạch**: con số · toán tử `>` hay `≥` · múi giờ · giá trị mặc định khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng.
   - Cột "Kết quả mong đợi" đủ **7 chiều**: trạng thái cuối · kho · tiền · thông báo · **hiển thị gì** · **trạng thái nút** · **message**.
   - Dòng ⚠⚠ → ⚠ → còn lại; mỗi dòng RTM = 1 mã BR theo mẫu knowledge/30 §3.3.
   - Logic "chuyển trạng thái hold" (6.3) = bảng state × event điền 100% ô (`→ ĐÍCH K/T/N/L (BR-xx)` / `KHL` / `Từ chối 0.5`), mã hiệu ứng khai báo một lần (knowledge/31 §3). Logic ≥2 điều kiện → decision table hit policy U, đếm tổ hợp = tích (knowledge/31 §1–§2).
   - Logic 6.3 kèm **Mermaid `stateDiagram-v2`** (knowledge/32 §7.2) — sơ đồ KHÔNG thay bảng state × event, vì ô `Từ chối 0.5` / `KHL` chỉ có trong bảng. Cuối mục 6: **sequence diagram end-to-end bằng Mermaid `sequenceDiagram`** (§7.3), một sơ đồ cho luồng chính.
4. **Mục 4 Validation & message**: bảng `No · FE/BE · Item · Nội dung check · Message`. **Message ghi nguyên văn trong ngoặc kép** — lấy từ RTM (câu trả lời N17-*); không có trong RTM → tự viết message cụ thể và gắn `[GIẢ ĐỊNH]`, KHÔNG ghi "hiển thị thông báo lỗi". Gồm cả rule nghiệp vụ (vượt tồn, hết hạn, trạng thái không hợp lệ), không chỉ format.
5. **Mục 8 Xác thực & phân quyền**: bảng actor bắt buộc có **Guest** và **System/Job**; ô không ✓ = ✗ theo 0.4 nên phải phủ cả thao tác chỉ-đọc (xem hold của mình / mọi hold, xem audit log) và mọi hành động log nêu "được làm" — rà từng câu trả lời kiểu "Được: a, b, c / Không được: d". Bảng 2: mỗi actor xác thực bằng gì; giữa các hệ thống dùng gì.
6. **Mục 2 Item màn hình**: bảng `No · 項目名 · コントロール · I/O · 必須 · 備考`, chia theo khu vực. Bắt buộc có **khác biệt login vs guest**, điều kiện **hiển thị/ẩn/disable**, giá trị mặc định, giới hạn, format, text nút nguyên văn. Ô thiếu dữ kiện → `[GIẢ ĐỊNH — ..]`, không để trống.
7. **Mục 3 Event**: bảng `No · Event · Trigger · Xử lý · Ghi chú` — tối thiểu: mở màn hình lấy gì từ đâu, mỗi nút gọi xử lý gì, double-click, đổi số lượng/dropdown cập nhật gì.
8. **Mục 7**: 7.1 ràng buộc + giảm nhẹ; **7.2 ca bất thường liên logic — bắt buộc đủ 4 ca BTC nêu đích danh**: dữ liệu đổi giữa lúc hiển thị và lúc submit · gửi trùng · mở link hai lần · mail fail sau khi đã lưu; cộng ca miền giữ hàng (tranh SKU cuối, kho điều chỉnh, cọc thất bại, SKU ngừng bán, hết hạn đúng lúc chuyển đơn) dạng bảng `EX-xx`; 7.3 lỗi hệ thống chung (log/alert/message chung); **7.4 bảng riêng "điều đang chờ xác nhận" kèm giả định hiện tại** — gom MỌI `[GIẢ ĐỊNH]` vào đây, không rải rác.
9. **Mục 9** (sơ đồ hệ thống bằng **Mermaid `flowchart LR`**, knowledge/32 §7.4, + bảng timing, xử lý khi tích hợp thất bại), **Mục 10** (field/perf/security/audit, ghi rõ số nào là giả định), **Mục 5** (có link Figma thì dán; không có → **Mermaid `block-beta`**, §7.1, + bảng 3 trạng thái màn hình) — viết gọn theo ngân sách knowledge/32 §5. Không mục nào để trống.
10. Ghi `spec.md`. Chạy Bash `wc -w <thư-mục>/spec.md`. Lưu ý `wc -w` đếm cả dấu `|`, `→`, `←` của bảng — thiên an toàn; giữ cách đếm này cho tới khi knowledge/00 §A ghi cách đếm của BTC (câu 3 họp 09/09). Nếu > 2.850 từ → chế độ nén (dưới), cắt theo knowledge/32 §6 (**10 → 9 → 5 → 3 → 2**; không cắt 1, 6, 7, 4, 8) rồi quay lại bước 11; sau 2 vòng nén mà vẫn trong (2.850, 3.000] thì chấp nhận và ghi rõ trong bảng tóm tắt.
11. Tự kiểm nhanh: (a) đủ 10 mục, không mục nào trống; (b) Grep `BR-\d+|EX-\d+` — mã duy nhất, mọi "áp dụng BR-xx" tồn tại; (c) Grep vài nhóm blacklist knowledge/40 §2 (nhóm 1, 3, 4, 5, 6) trên spec.md, sửa hit; (d) mọi dòng ⚠ trong RTM có BR; (e) mục 4 mọi message có ngoặc kép; (f) mục 2 và 8 có Guest; (g) mọi khối ```mermaid có nhãn tiếng Việt bọc ngoặc kép và có 1–2 câu chữ tóm tắt ngay dưới.
12. Điền ngược cột "Mã BR" và "Trạng thái" (✅) vào `rtm.md` bằng Edit.
13. Tạo bản nộp `spec.nop.md` = spec.md bỏ mọi dấu vết nội bộ (`← A-xx`, `(verify A-xx)`, nhãn `[GIẢ ĐỊNH — …]` → giữ nội dung luật, bỏ nhãn; **nhưng giữ nguyên bảng 7.4** vì BTC yêu cầu nêu điều chưa chốt kèm giả định) bằng sed/Edit; `wc -w` lại bản nộp (đây là số từ tính với BTC); spec.md nội bộ giữ nguyên để /spec-review, /appeal truy vết.
14. In bảng tóm tắt: số từ (nội bộ / bản nộp) | 10 mục có/thiếu | số BR/EX | dòng ⚠ đã phủ / tổng ⚠ | số logic ở mục 6 và số case mỗi logic | danh sách `[GIẢ ĐỊNH]` trong bảng 7.4 | dòng RTM chưa thành BR (nếu có, kèm lý do).

## Chế độ `nén`
1. Grep liệt kê toàn bộ mã BR/EX và dòng "←" trước khi sửa.
2. Áp knowledge/30 §5: bảng thay văn, "áp dụng BR-xx" thay lặp, công thức thay câu, luật tổng quát + ngoại lệ thay liệt kê. Cắt sơ đồ Mermaid trước theo knowledge/32 §7.5 (wireframe → sơ đồ hệ thống → sequence → state machine, mỗi cái thay bằng 1–2 câu chữ; **không cắt bảng đi kèm**). Vẫn thừa thì cắt mục theo knowledge/32 §6: **mục 10 → 9 → 5 → 3 → 2**. **Không cắt mục 1, 6, 7, 4, 8.** Không bao giờ cắt message nguyên văn (mục 4) hay cột Guest (mục 2, 8).
3. Grep lại danh sách mã: phải bằng danh sách trước. In diff số từ trước/sau và khẳng định "không mất BR".

## Output bắt buộc
- [ ] `spec.md` đủ **10 mục BTC**, mọi ô bảng có giá trị, ≤3.000 từ (wc -w).
- [ ] Mục 6: mỗi logic có bảng step + bảng Case đủ 3 loại case và 5 gạch.
- [ ] Mục 4: mọi message nguyên văn trong ngoặc kép, có cột FE/BE.
- [ ] Mục 2 và 8: có Guest / login phân biệt rõ.
- [ ] Mục 7.4: một bảng gom điều chưa chốt + giả định.
- [ ] Sơ đồ Mermaid (mục 5, 6, 9) hợp lệ, mỗi cái kèm 1–2 câu chữ tóm tắt.
- [ ] Mọi BR/EX có `← A-xx` hoặc `[GIẢ ĐỊNH]`.
- [ ] `rtm.md` cột Mã BR đã điền cho mọi dòng ⚠.
- [ ] Bảng tóm tắt in ra.

## Không được
- Viết luật không truy vết được về RTM mà không gắn `[GIẢ ĐỊNH]`; viết ngược với RTM.
- Dùng từ trong danh sách đen knowledge/40 §2; tham chiếu tài liệu ngoài; để ô bảng trống.
- Bỏ trống bất kỳ mục nào trong 10 mục; viết catch-all trước khi đọc xong RTM.
- Ghi "hiển thị thông báo lỗi phù hợp" thay vì message nguyên văn (mục 4).
- Rải `[GIẢ ĐỊNH]` khắp bài mà không gom vào bảng 7.4.
- Cắt mục 1, 4, 6, 7, 8 để giảm từ.
- Nộp khi wc -w > 3.000.
