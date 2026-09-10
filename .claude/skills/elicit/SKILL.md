---
name: elicit
description: Quản 5 câu hỏi cho AI Khách hàng Spec Battle (mỗi lượt 1 câu, 5.000 token, AI không có memory) — chọn và gọt 5 câu từ mô hình bài toán, in từng câu qua cổng 5 kiểm tra, nạp câu trả lời vào RTM ngược + log có timestamp, và soạn câu restate cuối từ bảng xếp hạng rủi ro giả định. Dùng buổi sáng 9:30–11:20 khi người dùng nói "kế hoạch hỏi", "câu 1", "hỏi AI khách hàng", "nạp câu trả lời", "cập nhật RTM", "restate", "elicit".
argument-hint: "ke-hoach [thư-mục] | cau <1..5> [thư-mục] | nap [thư-mục] | restate [thư-mục]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(date *), Bash(mkdir *), Bash(LC_ALL=C.UTF-8 wc *)
---
# /elicit — 5 câu hỏi, không có lần thứ hai

Hạn mức cứng (00 §A): **5 câu hỏi cho cả ngày · mỗi lượt đúng 1 câu · 5.000 token cho cả hỏi + trả lời · AI Khách hàng KHÔNG có memory · ảnh tối đa 3 lần**.

Mục tiêu: 5 câu trả về nhiều dữ kiện nhất có thể, mọi dữ kiện vào RTM, và mọi ô **không** hỏi được cũng có một dòng RTM (loại `G-xx`) để không có chỗ nào trong spec bị bỏ trắng.

## Input
`$ARGUMENTS` = `ke-hoach [thư-mục]` · `cau <n> [thư-mục]` · `nap [thư-mục]` · `restate [thư-mục]`. Thư mục mặc định `battle/`, phải có `brief.md` **và `mo-hinh-bai-toan.md`** — thiếu file mô hình thì chạy `/frame` trước.
Người dùng có thể dán thêm sau lệnh: câu trả lời của AI Khách hàng (chế độ `nap`) hoặc ghi chú (token còn lại, BTC đã trả lời ảnh có tính token hay không).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A (tham số), §A1 (cách ước token), §A2 (hai ô còn hở đổi kế hoạch: ảnh có tính token, AI còn mở sau 12:00).
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md`: §1 (10 quy tắc + **cổng 5 kiểm tra**), §2 (ngân hàng nguyên liệu), §3 (**5 câu soạn sẵn C1–C5 + bảng ánh xạ ID → câu**), §4 (31 phát biểu mặc định ngành), §5 (RTM hai loại dòng), §6 (log).
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §4 — bảng "ô mô hình → đi vào câu nào, nếu không hỏi được thì làm gì".
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md`: §1 (nhận diện biến thể), §6 (catalogue ⚠ — dùng khi nạp và khi chọn mặc định ngành).
- `<thư-mục>/mo-hinh-bai-toan.md` — "Câu hỏi P0", "Mâu thuẫn nội tại của brief".

## Sổ hạn mức (đọc/ghi ở đầu `log-khach-hang.md`)
Dòng đầu file luôn có: `Đã dùng: <k>/5 câu · <t>/5.000 token · ảnh <a>/3 · ảnh tính token: có|không|chưa rõ`.
Không có file → tạo với `0/5`, `0/5.000`, `0/3`, `chưa rõ`.

**Chốt cứng:** khi `k = 5`, mọi chế độ `cau`/`restate` **từ chối sinh câu mới** và in ra danh sách việc thay thế (điền mặc định ngành theo 30 §1b, luật an toàn hai chiều 32 §3.3). Không có câu thứ 6 trong bất kỳ hoàn cảnh nào.

## Chế độ `ke-hoach` (9:30, chạy một lần)
1. Đọc `brief.md`, `mo-hinh-bai-toan.md`. Xác định biến thể (10 §1) và **tên tính năng đúng như brief gọi** — mọi câu hỏi phải dùng tên đó, không dùng từ riêng của đội (quy tắc 20 §1-10).
2. Lấy C1–C4 ở 20 §3 làm khung, rồi **gọt theo brief**: thay `<TÊN TÍNH NĂNG>`; bỏ khỏi phần liệt kê những ô mà brief đã trả lời tường minh; chèn tối đa 2 ô sinh từ mục "Mâu thuẫn nội tại của brief" — mâu thuẫn của brief là dấu hiệu specs thật có luật thứ ba, ưu tiên trên mọi tham số.
3. Đối chiếu bảng 05 §4: mỗi ô mô hình `?` phải rơi vào một trong ba chỗ — một câu trong C1–C4, hàng đợi cho C5, hoặc "tự điền mặc định ngành". **Không được có ô `?` không thuộc chỗ nào.**
4. Ước token từng câu (00 §A1) và tổng; giữ **≥1.000 token cho C5**.
5. Ghi `<thư-mục>/ke-hoach-hoi.md`: 4 câu nguyên văn đã gọt · ước token · bảng "ô mô hình → câu nào / tự điền" · danh sách hàng đợi C5.
6. In ra: bảng 4 câu + ước token, và **danh sách ô sẽ KHÔNG hỏi** kèm giá trị mặc định ngành dự kiến (20 §4). Danh sách thứ hai quan trọng bằng danh sách thứ nhất: đó là phần spec sẽ viết bằng giả định.

## Chế độ `cau <n>` (n = 1..4)
1. Đọc sổ hạn mức. `k ≥ 5` → từ chối (xem chốt cứng). `n ≤ k` → cảnh báo "câu này đã gửi" và in lại từ log thay vì sinh mới.
2. Lấy câu n từ `ke-hoach-hoi.md` (chưa có file → chạy `ke-hoach` trước).
3. **Cổng 5 kiểm tra (20 §1-10), in kết quả từng dòng ✓/✗; còn ✗ thì KHÔNG in khối copy:**
   - đúng **một** dấu `?`, và câu là một câu hỏi duy nhất;
   - không tham chiếu lượt trước, không đại từ trôi ("việc đó", "như trên") — AI không có memory;
   - có ép format + cap số dòng + cap số từ mỗi ô + "không giải thích";
   - câu trả lời tệ nhất có thể vẫn dùng được (không thể là một chữ "Có"/"Không");
   - mọi thuật ngữ đều là từ của brief.
4. Ước token câu hỏi + dự kiến câu trả lời; nếu vượt token còn lại sau khi trừ 1.000 cho C5 → cắt phần liệt kê cuối của câu (thứ tự cắt ghi ở 20 §3 từng câu) và in rõ đã cắt gì.
5. Timestamp bằng Bash `date "+%Y-%m-%d %H:%M:%S"`. Ghi khối `## C<n> — <tên>` vào `log-khach-hang.md` theo mẫu 20 §6, để trống phần câu trả lời. Tăng `k`.
6. In ra: (a) khối câu hỏi trong một code block, copy nguyên khối; (b) `Token: hỏi ~X · dự kiến trả lời ~Y · còn lại ~Z / 5.000 · câu còn lại <5−k>`; (c) một dòng "nếu câu trả lời hụt phần nào thì phần đó tự điền bằng: …" — chuẩn bị trước cho khả năng AI chỉ trả lời nửa đầu.

## Chế độ `restate` (= câu 5, chạy 11:05, SAU khi có bản nháp spec)
1. Điều kiện vào: `spec.md` tồn tại **và** `/spec-write` đã in bảng xếp hạng rủi ro giả định. Thiếu → dừng, nói rõ vì sao: câu 5 phải nhắm vào giả định đã thật sự vào spec, chứ không phải kế hoạch soạn trước.
2. Nguồn phát biểu, ưu tiên giảm dần (20 §3 C5): (a) 10 dòng đầu bảng xếp hạng rủi ro giả định; (b) BR nhãn `⚡` hoặc ✗ cổng F2/F7; (c) ô M1/M2/M5 còn `?`; (d) phát biểu 20 §4 có tiền/tồn dính vào.
3. Viết mỗi ý thành một phát biểu **có con số hoặc trạng thái cụ thể**, ≤15 từ, để AI chỉ cần Đúng/Sai; ý "Sai" xin giá trị đúng ≤8 từ trong cùng câu. Tối đa 10 phát biểu; xếp rủi ro cao lên đầu vì câu trả lời có thể bị cắt giữa.
4. Chạy cổng 5 kiểm tra như chế độ `cau`. Ghi log, tăng `k` lên 5.
5. In kèm: bảng "phát biểu → BR nào trong spec sẽ phải sửa nếu trả lời Sai" — để lúc 11:20 vá được trong 15 phút mà không phải suy lại.

## Chế độ `nap`
1. Lấy câu trả lời từ phần dán sau lệnh hoặc file người dùng chỉ. **Không sửa chữ, không tóm tắt** khi ghi log.
2. Ghi vào khối `## C<n>` gần nhất còn trống: "Câu trả lời (nguyên văn)", "Token sau câu này" (thật nếu người dùng cung cấp, không thì ước — ghi rõ "thật" hay "ước"), timestamp nhận. Cập nhật sổ hạn mức.
3. Tách **từng ý** thành một dòng RTM `A-xx` (20 §5): `A-xx | C<n> | nội dung ≤12 từ | ⚠? | — | (trống) | ❌ | hh:mm`. Bảng nhiều hàng hoặc câu trả lời liệt kê → **mỗi hàng/mỗi ý một dòng RTM**, không gộp.
   - Đánh ⚠ khi khác mặc định ngành: đối chiếu 31 phát biểu 20 §4 và catalogue 10 §6. ⚠⚠ khi vừa khác mặc định vừa có con số/trạng thái cụ thể.
   - "Ngoài phạm vi tính năng này" ⇒ trạng thái ⛔, ghi vào mục "NGOÀI phạm vi" cuối `rtm.md` (nguồn cho §1 spec **và** cho rào chống VÔ HIỆU buổi chiều — 50 §6).
   - "Không có quy định riêng." ⇒ dòng RTM ghi đúng cụm đó, **không** đánh ⛔ và **không** coi là bằng chứng phạm vi (50 §6-9). Ô này chuyển thành `G-xx` mặc định ngành.
   - Câu trả lời hụt một phần câu hỏi ⇒ ghi "không trả lời phần <x>" và mở ngay dòng `G-xx` cho phần đó. Không hỏi lại — hỏi lại tốn một câu trong 5.
4. **Không bịa, không suy diễn.** Ý nào AI không trả lời rõ → `G-xx` với giá trị mặc định ngành, không phải `A-xx` với giá trị đoán.
5. **Sau khi nạp câu 4** (hoặc sau câu cuối trong C1–C4 đã gửi): sinh **toàn bộ dòng `G-xx`** — mỗi ô mô hình còn `?` và mỗi nhóm ID ở cột phải của bảng ánh xạ 20 §3 thành một dòng `G-xx` với giá trị mặc định ngành đề xuất (nguồn: 20 §4 / 10 §6) và cột Rủi ro để trống cho `/spec-write` chấm. Đây là bước biến "chưa hỏi" thành "đã có kế hoạch viết", và là điều kiện để `/spec-write` không bỏ trắng mục nào.
6. Cập nhật dòng "Trích rút → RTM:" trong khối log.
7. In ra: bảng ⚠ mới (ID, nội dung, vì sao phản trực giác) · số dòng `A-xx` / `G-xx` / tổng ⚠ · mâu thuẫn với dòng RTM cũ (gắn `↔`, đưa vào hàng đợi C5) · token còn lại · câu còn lại.

## Ảnh (tối đa 3 lần)
Mặc định **không dùng** (20 §1-9): một ảnh ≈ 1.000–1.600 token, còn bảng viết bằng text chỉ 400–600. Chỉ đảo quyết định khi người dùng xác nhận BTC nói **ảnh không tính vào 5.000 token** — lúc đó ghi `ảnh tính token: không` vào sổ hạn mức, và chế độ `restate` chuyển sang phương án ảnh: xuất một bảng markdown ≤25 dòng giả định để người dùng chụp gửi kèm, câu hỏi text chỉ còn "trong ảnh, dòng nào Sai; ghi giá trị đúng ≤8 từ?".

## Output bắt buộc
- [ ] Khối câu hỏi copy-paste được, đã qua cổng 5 kiểm tra (chế độ `cau`/`restate`), hoặc bảng ⚠ mới (chế độ `nap`).
- [ ] `log-khach-hang.md` có sổ hạn mức ở dòng đầu, timestamp, nguyên văn.
- [ ] `rtm.md` có cả dòng `A-xx` và `G-xx`; không dòng nào thiếu cả hai cột "Mã BR" và "Rủi ro".
- [ ] Một dòng `Token: … / 5.000 · câu còn lại: …` ở cuối mọi output.

## Không được
- Sinh câu thứ 6, hoặc gộp hai câu hỏi vào một lượt để "tiết kiệm".
- In khối câu hỏi khi cổng 5 kiểm tra còn ✗.
- Viết câu hỏi tham chiếu lượt trước ("như đã nói", "bổ sung ý 3") — AI không có memory.
- Chạy `restate` trước khi có `spec.md` và bảng xếp hạng rủi ro giả định.
- Hỏi lại một phần đã bị trả lời hụt; hỏi "vì sao"; hỏi dẫn dắt; hỏi điều suy được từ mặc định ngành.
- Coi `"Không có quy định riêng."` là bằng chứng phạm vi, hoặc là lý do bỏ trắng ô trong spec.
- Bịa câu trả lời khi nạp; tóm tắt câu trả lời trong log; ghi giá trị đoán thành dòng `A-xx`.
- Dùng ảnh khi chưa xác nhận ảnh có tính token hay không.
- Điền cột Mã BR (việc của `/spec-write`).
