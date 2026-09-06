---
name: spec-write
description: Viết spec "Đặt giữ hàng" ≤3.000 từ cho AI Executor đọc, từ RTM ngược và log AI Khách hàng, theo template 11 mục có mã BR truy vết; hoặc nén spec đang có về ≤3.000 từ không mất luật. Dùng 11:00–11:45 ngày thi khi người dùng nói "viết spec", "điền template", "nén spec", "spec-write", "rút gọn còn 3000 từ".
argument-hint: "[thư-mục] [nén]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(wc *)
---
# /spec-write — Viết spec từ RTM

Mục tiêu: mọi dữ kiện ⚠ trong RTM thành một luật có mã trong spec; mọi ô bảng được điền; ≤3.000 từ; không bịa.

## Input
`$ARGUMENTS` = `[thư-mục] [nén]`. Thư mục mặc định `battle/`. Cần: `rtm.md`, `log-khach-hang.md`, `brief.md`; nếu có `spec.md` thì là bản đang sửa.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/30-viet-spec.md`: §1 template 11 mục + ngân sách từ, §2 khung §0 catch-all, §3 cú pháp EARS + mẫu BR-xx, §4 checklist 17 quy tắc, §5 tiết kiệm từ, §6 thứ tự viết, §7 cổng chất lượng.
- `${CLAUDE_PROJECT_DIR}/knowledge/31-bang-quyet-dinh-trang-thai.md`: §1 hit policy, §2 chứng minh đủ/rời, §3 bảng state × event và mã hiệu ứng K/T/N/L.
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §2 glossary (chỉ lấy thuật ngữ có dùng trong spec).
- `${CLAUDE_PROJECT_DIR}/knowledge/40-tu-mo-ho.md` §2 danh sách đen (để tự tránh khi viết).

## Quy tắc cứng số 1 — truy vết
Mỗi BR/EX kết thúc bằng nguồn: `← A-07` (dòng RTM) hoặc `[GIẢ ĐỊNH]` (không có trong RTM). Không có nguồn = không được viết. Dòng RTM ⛔ (ngoài phạm vi) chỉ vào §1 NGOÀI, không thành BR.

## Bước (theo knowledge/30 §6)
1. Đọc `rtm.md` + `log-khach-hang.md`. Xác định biến thể (cart hold / cọc / pickup) từ dòng N1-01; chọn cụm luật cần nhiều từ nhất theo knowledge/10 §1. Liệt kê mọi dòng ⚠ chưa có Mã BR.
2. **§6 Luật nghiệp vụ** trước: dòng ⚠⚠ → ⚠ → còn lại. Mỗi dòng RTM = 1 BR theo mẫu knowledge/30 §3.3 (EARS, 6 thành phần, KHÔNG ĐƯỢC, Fit, ← A-xx). Luật ≥2 điều kiện → decision table hit policy U (knowledge/31 §1–§2): đếm tổ hợp = tích, ô không hợp lệ ghi "Từ chối 0.5".
3. **§5 State machine**: tập trạng thái lấy từ lượt 2 của log; bảng state × event điền 100% ô (`→ ĐÍCH K/T/N/L (BR-xx)` hoặc `KHL` / `Từ chối 0.5`), khai báo mã hiệu ứng một lần (knowledge/31 §3).
4. **§3 Actor × quyền**: bắt buộc cột Guest và System/Job; ô không ✓ = ✗ theo 0.4. Vì 0.4 biến mọi im lặng thành CẤM, bảng phải phủ cả thao tác chỉ-đọc (xem hold của mình / mọi hold, xem audit log) và mọi hành động log nêu "được làm" — rà lại từng câu trả lời kiểu "Được: a, b, c / Không được: d" trong log để không bỏ ý nào.
5. **§4 Tồn kho**: công thức ATP, cột nào đổi khi tạo/hết hạn/hủy/chuyển đơn, giá khóa hay không, giới hạn số lượng — tất cả từ RTM.
6. **§1 Phạm vi**: TRONG/NGOÀI nguyên văn từ lượt 1 (dòng ⛔ trong rtm.md).
7. **§2 Glossary**: chỉ thuật ngữ có nguy cơ hiểu sai và có dùng trong spec; một khái niệm một tên (loại bỏ từ đồng nghĩa khỏi toàn spec).
8. **§7, §8, §9, §10** theo template; §8 tối thiểu các EX nêu trong template nếu RTM có dữ kiện, còn lại để 0.5 chặn.
9. **§0 cuối cùng** từ khung knowledge/30 §2: từng dòng đối chiếu với RTM/log; dòng khớp → giữ và ghi `(verify A-xx)`; dòng RTM nói khác → sửa theo RTM; dòng không verify được → giữ nguyên nhưng gắn `[GIẢ ĐỊNH]`. Khai báo ký hiệu dùng trong spec ([a,b), →, KHL, K/T/N/L, ✓/✗) ở 0.x.
10. Ghi `spec.md`. Chạy Bash `wc -w <thư-mục>/spec.md`. Lưu ý `wc -w` đếm cả dấu `|`, `→`, `←` của bảng — thiên an toàn (bảng 6×10 tốn ~70 "từ" ký hiệu); giữ cách đếm này cho tới khi knowledge/00 §A ghi cách đếm của BTC (câu 3 họp 09/09), khi đó đổi lệnh cho khớp. Nếu > 2.850 từ → sang chế độ nén (dưới) rồi quay lại bước 11; sau 2 vòng nén mà vẫn trong (2.850, 3.000] thì chấp nhận và ghi rõ trong bảng tóm tắt.
11. Tự kiểm nhanh: (a) Grep `BR-\d+|EX-\d+` — mã duy nhất, mọi "áp dụng BR-xx" tồn tại; (b) Grep vài nhóm blacklist knowledge/40 §2 (nhóm 1, 3, 4, 5, 6) trên spec.md, sửa hit; (c) mọi dòng ⚠ trong RTM có BR.
12. Điền ngược cột "Mã BR" và "Trạng thái" (✅) vào `rtm.md` bằng Edit.
13. Tạo bản nộp `spec.nop.md` = spec.md bỏ mọi dấu vết nội bộ (`← A-xx`, `(verify A-xx)`, `[GIẢ ĐỊNH — …]` → giữ nội dung luật, bỏ nhãn) bằng sed/Edit; `wc -w` lại bản nộp (đây là số từ tính với BTC); spec.md nội bộ giữ nguyên để /spec-review, /appeal truy vết.
14. In bảng tóm tắt: số từ (nội bộ / bản nộp) | số BR/EX | dòng ⚠ đã phủ / tổng ⚠ | danh sách `[GIẢ ĐỊNH]` (đề xuất câu verify ở lượt restate) | dòng RTM chưa thành BR (nếu có, kèm lý do).

## Chế độ `nén`
1. Grep liệt kê toàn bộ mã BR/EX và dòng "←" trước khi sửa.
2. Áp knowledge/30 §5: bảng thay văn, "áp dụng BR-xx" thay lặp, công thức thay câu, luật tổng quát + ngoại lệ thay liệt kê, cắt §7 → §10 → §9 → §2 nếu vẫn thừa. Không cắt §0, §3, §5, §6, §8.
3. Grep lại danh sách mã: phải bằng danh sách trước. In diff số từ trước/sau và khẳng định "không mất BR".

## Output bắt buộc
- [ ] `spec.md` đủ 11 mục, mọi ô bảng có giá trị, ≤3.000 từ (wc -w).
- [ ] Mọi BR/EX có `← A-xx` hoặc `[GIẢ ĐỊNH]`.
- [ ] `rtm.md` cột Mã BR đã điền cho mọi dòng ⚠.
- [ ] Bảng tóm tắt in ra.

## Không được
- Viết luật không truy vết được về RTM mà không gắn `[GIẢ ĐỊNH]`; viết ngược với RTM.
- Dùng từ trong danh sách đen knowledge/40 §2; tham chiếu tài liệu ngoài; để ô bảng trống.
- Viết §0 trước khi đọc xong RTM.
- Nộp khi wc -w > 3.000.
