---
name: elicit
description: Quản 5 câu hỏi cho AI Khách hàng Spec Battle (mỗi lượt đúng 1 câu, tổng 5.000 token, AI không có memory) — chọn và gọt 5 câu C1–C5 từ mô hình bài toán sao cho phủ cả bốn vùng cấu trúc BTC (item màn hình, event, message lỗi nguyên văn, API), in từng câu qua cổng 5 kiểm tra, nạp câu trả lời vào RTM ngược (dòng `A-xx`) + log có timestamp, sinh dòng `G-xx` cho mọi ô không hỏi được, và soạn câu restate cuối từ bảng xếp hạng rủi ro giả định. Dùng buổi sáng 9:30–11:20 khi người dùng nói "kế hoạch hỏi", "câu 1", "hỏi AI khách hàng", "nạp câu trả lời", "cập nhật RTM", "restate", "elicit".
argument-hint: "ke-hoach [thư-mục] | cau <1..5> [thư-mục] | nap [thư-mục] | restate [thư-mục]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(date *), Bash(mkdir *), Bash(LC_ALL=C.UTF-8 wc *)
---
# /elicit — 5 câu hỏi, không có lần thứ hai

Hạn mức cứng (00 §A): **5 câu hỏi cho cả ngày · mỗi lượt đúng 1 câu · 5.000 token cho cả hỏi + trả lời · AI Khách hàng KHÔNG có memory · ảnh tối đa 3 lần**.

Mục tiêu: 5 câu trả về nhiều dữ kiện nhất có thể, mọi dữ kiện vào RTM, mọi ô **không** hỏi được cũng có một dòng RTM (loại `G-xx`), và **cả 10 mục spec BTC** (knowledge/32 §1) đều có dữ kiện hoặc có giả định đã xếp hạng — không mục nào bị bỏ trắng.

## Input
`$ARGUMENTS` = `ke-hoach [thư-mục]` · `cau <n> [thư-mục]` · `nap [thư-mục]` · `restate [thư-mục]`. Thư mục mặc định `battle/`, phải có `brief.md` **và `mo-hinh-bai-toan.md`** — thiếu file mô hình thì chạy `/frame` trước.
Người dùng có thể dán thêm sau lệnh: câu trả lời của AI Khách hàng (chế độ `nap`) hoặc ghi chú (token còn lại, BTC đã trả lời ảnh có tính token hay không).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A (tham số đã chốt), §A1 (cách ước token), §A2 (hai ô còn hở đổi kế hoạch buổi sáng: ảnh có tính token, AI còn mở sau 12:00), §B (agenda — mốc giờ từng câu), §D1 (số học điểm: phần nào của buổi sáng không được cắt).
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md`: §1 (**10 quy tắc hỏi khi chỉ có 5 câu**, quy tắc 10 = **cổng 5 kiểm tra**), §2 (ngân hàng **166 câu, 20 nhóm** — N0 + N1–N19, trong đó **N15–N19 phục vụ mục 2/3/4/9/7 BTC**), §3 (**5 câu soạn sẵn C1–C5** + bảng **"Ánh xạ ID §2 → câu, và phần phải tự điền"**), §4 (31 phát biểu mặc định ngành), §5 (RTM hai loại dòng `A-xx`/`G-xx`, có cột Rủi ro), §6 (mẫu log một khối mỗi câu).
- `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` §1 — **10 mục spec BTC** (基本設計 1–5 · 詳細設計 6–9 · 技術仕様 10). Bốn mục mới 2, 3, 4, 9 không có nguồn nào khác ngoài buổi sáng.
- `${CLAUDE_PROJECT_DIR}/knowledge/33-kha-thi-van-hanh.md` §1 (cổng F), **§3.3 luật an toàn hai chiều** — dùng khi không hỏi được thứ mà F2/F8 đòi: viết luật bao quát, **không chọn hộ**.
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §4 — bảng "ô mô hình → đi vào câu nào, nếu không hỏi được thì làm gì".
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md`: §1 (nhận diện biến thể), §6 (catalogue ⚠ — dùng khi nạp và khi chọn mặc định ngành).
- `<thư-mục>/mo-hinh-bai-toan.md` — "Câu hỏi P0", "Mâu thuẫn nội tại của brief".

## Mốc giờ (00 §B, 20 §3, 30 §6)

| Câu | Giờ gửi | Nội dung | Vùng BTC được nuôi |
|---|---|---|---|
| C1 | **9:30** | Phạm vi NGOÀI + điều PHẢI ngăn | mục 1 |
| C2 | **9:40** | Bảng chuyển trạng thái + cột hiển thị/nút | mục 6.3, 6, **2, 3** |
| C3 | **9:55** | Bảng tham số | mục 6, 10 |
| C4 | **10:10** | Suy biến + **message lỗi nguyên văn** + **guest** | mục 7, **4**, **8**, 2 |
| C5 | **11:20** | Restate Đúng/Sai từ bảng xếp hạng rủi ro | vá mọi mục |

C1–C4 **không phụ thuộc nhau** (AI không có memory) — gửi liên tiếp, không chờ câu trước. Chỉ C5 phụ thuộc bản nháp spec. Xong C1–C4 trước 10:20 để có 45 phút viết spec trước khi restate.

## Sổ hạn mức (đọc/ghi ở đầu `log-khach-hang.md`)
Dòng đầu file luôn có: `Đã dùng: <k>/5 câu · <t>/5.000 token · ảnh <a>/3 · ảnh tính token: có|không|chưa rõ`.
Không có file → tạo với `0/5`, `0/5.000`, `0/3`, `chưa rõ`, rồi header theo 20 §6.
Không có `rtm.md` → tạo bảng 8 cột theo 20 §5 (chỉ header).

**Chốt cứng:** khi `k = 5`, mọi chế độ `cau`/`restate` **từ chối sinh câu mới** và in ra danh sách việc thay thế:
1. điền ô còn `?` bằng **mặc định ngành** (20 §4 + 10 §6), gắn `[GIẢ ĐỊNH]`, khai tập trung ở **mục 7.4** (30 §1b-5);
2. ô nào F2/F8 đòi mà không biết → **luật an toàn hai chiều** 33 §3.3, không chọn hộ;
3. dòng `G-xx` rủi ro Cao chưa qua C5 → liệt kê trong `review.md` như rủi ro đã biết (20 §5-8), và thành đạn tự bắn buổi chiều.
Không có câu thứ 6 trong bất kỳ hoàn cảnh nào.

## Quy tắc cứng
1. **Một câu hỏi, một bảng trả lời** (20 §1-1). Giới hạn là số *câu hỏi*, không phải số *dữ kiện*. Mỗi câu kết thúc bằng đúng một dấu `?`.
2. **Mỗi câu tự chứa** (20 §1-3). Không tham chiếu lượt trước, không đại từ trôi — AI không có memory.
3. **Không in khối câu hỏi khi cổng 5 kiểm tra còn ✗** (20 §1-10).
4. **Câu trả lời hụt một phần ⇒ mở dòng `G-xx` cho phần đó, KHÔNG hỏi lại.** Hỏi lại tốn một câu trong 5.
5. **Ba cụm khác nhau, không được lẫn** (50 §6-9):
   - `"Ngoài phạm vi tính năng này."` = **bằng chứng phạm vi** ⇒ trạng thái ⛔, vào mục "NGOÀI phạm vi" của `rtm.md`.
   - `"Không có quy định riêng."` = **không** phải bằng chứng phạm vi, **không** đánh ⛔, và **không** phải lý do bỏ trắng ô trong spec ⇒ chuyển thành `G-xx` mặc định ngành.
   - AI **không trả lời** phần đó = `G-xx`, ghi rõ "không trả lời phần <x>".
6. **Câu trả lời dạng liệt kê hoặc bảng nhiều hàng ⇒ mỗi ý/mỗi hàng một dòng RTM**, không gộp (20 §5-6). Ý bị gộp không thành BR và bị **catch-all 0.4/0.5 xử sai** — đây là bài học đắt nhất của diễn tập.
7. **Không hỏi điều suy được** từ brief hoặc từ mặc định ngành (20 §1-5).
8. **Ảnh: mặc định không dùng** (20 §1-9) — xem mục "Ảnh" dưới.

## Chế độ `ke-hoach` (9:30, chạy một lần)
1. Đọc `brief.md`, `mo-hinh-bai-toan.md`. Xác định biến thể (10 §1) và **tên tính năng đúng như brief gọi** — mọi câu hỏi phải dùng tên đó, không dùng từ riêng của đội (20 §1-10 kiểm 5).
2. Lấy C1–C4 ở 20 §3 làm khung, rồi **gọt theo brief**: thay `<TÊN TÍNH NĂNG>`; bỏ khỏi phần liệt kê những ô mà brief đã trả lời tường minh; chèn tối đa 2 ô sinh từ mục "Mâu thuẫn nội tại của brief" — mâu thuẫn của brief là dấu hiệu specs thật có luật thứ ba, ưu tiên trên mọi tham số.
3. **Giữ nguyên bốn vùng cấu trúc BTC đã được cấp chỗ** (20 §3, mở đầu). Khi gọt, không được cắt:
   - **C2**: hai cột cuối `Màn hình hiển thị gì | Nút nào bật/tắt/ẩn` — nguồn duy nhất cho **mục 2** (điều kiện ẩn/disable, N15-05) và **mục 3** (event, N16-03);
   - **C4 ý (8)**: khách chưa đăng nhập tạo lượt giữ được không, khác gì khách đã đăng nhập — **guest vs login** (N15-06), nuôi mục 2 và **mục 8**;
   - **C4 ý (9)**: **message NGUYÊN VĂN từng chữ** cho vượt tồn / số lượng 0 / sản phẩm vừa ngừng bán (N17-01/03/04) — nuôi **mục 4**; message nguyên văn **không suy được** từ mặc định ngành.
   - **Mục 9 (API)** không được cấp chỗ trong 5 câu: toàn nhóm N18 viết bằng mặc định ngành theo bảng 33 §2, khai ở mục 7.4. Nói rõ điều này trong output để không ai đi tìm dữ kiện không tồn tại.
4. Đối chiếu bảng 05 §4: mỗi ô mô hình `?` phải rơi vào một trong ba chỗ — một câu trong C1–C4, hàng đợi cho C5, hoặc "tự điền mặc định ngành". **Không được có ô `?` không thuộc chỗ nào.**
5. Đối chiếu **bảng phủ 10 mục BTC**: với mỗi mục 1–10 (32 §1), ghi câu nào sẽ góp dữ kiện. Mục nào không có câu nào → đánh dấu "viết bằng mặc định ngành + khai 7.4", không để trống ô trong bảng.
6. Ước token từng câu (00 §A1) và tổng; giữ **≥1.000 token cho C5**.
7. Ghi `<thư-mục>/ke-hoach-hoi.md`: 4 câu nguyên văn đã gọt · ước token · bảng "ô mô hình → câu nào / tự điền" · **bảng phủ 10 mục BTC** · danh sách hàng đợi C5.
8. In ra: (a) bảng 4 câu + ước token + mốc giờ; (b) bảng phủ 10 mục BTC; (c) **danh sách ô sẽ KHÔNG hỏi** kèm giá trị mặc định ngành dự kiến (20 §4). Danh sách (c) quan trọng bằng (a): đó là phần spec sẽ viết bằng giả định — **~70% ngân hàng câu hỏi không được hỏi** (20 §3, bảng ánh xạ).

## Chế độ `cau <n>` (n = 1..4)
1. Đọc sổ hạn mức. `k ≥ 5` → từ chối (xem chốt cứng). `n ≤ k` → cảnh báo "câu này đã gửi" và in lại từ log thay vì sinh mới.
2. Lấy câu n từ `ke-hoach-hoi.md` (chưa có file → chạy `ke-hoach` trước).
3. **Cổng 5 kiểm tra (20 §1-10), in kết quả từng dòng ✓/✗; còn ✗ thì KHÔNG in khối copy:**
   - đúng **một** dấu `?`, và câu là một câu hỏi duy nhất;
   - không tham chiếu lượt trước, không đại từ trôi ("việc đó", "như trên") — AI không có memory;
   - có ép format + cap số dòng + cap số từ mỗi ô + "không giải thích";
   - câu trả lời tệ nhất có thể vẫn dùng được (không thể là một chữ "Có"/"Không");
   - mọi thuật ngữ đều là từ của brief.
4. **Kiểm bốn vùng BTC trước khi gửi** (thay cho ràng buộc "mốc giờ" của bản cũ):
   - `n = 2` → khối phải còn hai cột `Màn hình hiển thị gì | Nút nào bật/tắt/ẩn`;
   - `n = 4` → khối phải còn ý (8) guest và ý (9) message nguyên văn;
   - thiếu → **không gửi**, khôi phục cột/ý rồi chạy lại cổng. Nếu token không đủ chỗ cho chúng: cắt phần rẻ hơn của cùng câu (C2: bớt sự kiện cuối; C4: bỏ ý (7), mặc định ngành FCFS theo timestamp server đủ an toàn), chứ không cắt hai vùng này.
5. Ước token câu hỏi + dự kiến câu trả lời; nếu vượt token còn lại sau khi trừ 1.000 cho C5 → cắt theo thứ tự ghi ở 20 §3 từng câu và in rõ đã cắt gì.
6. Timestamp bằng Bash `date "+%Y-%m-%d %H:%M:%S"`. Ghi khối `## C<n> — <tên>` vào `log-khach-hang.md` theo mẫu 20 §6, để trống phần câu trả lời. Tăng `k`.
7. In ra: (a) khối câu hỏi trong một code block, copy nguyên khối; (b) `Token: hỏi ~X · dự kiến trả lời ~Y · còn lại ~Z / 5.000 · câu còn lại <5−k>`; (c) một dòng "nếu câu trả lời hụt phần nào thì phần đó tự điền bằng: …" — chuẩn bị trước cho khả năng AI chỉ trả lời nửa đầu.

## Chế độ `restate` (= câu 5, gửi **11:20**, SAU khi có bản nháp spec)
1. **Điều kiện vào cứng:** `spec.md` tồn tại **và** `/spec-write` đã in **bảng xếp hạng rủi ro giả định**. Thiếu một trong hai → dừng, nói rõ vì sao: câu 5 phải nhắm vào giả định đã thật sự vào spec, chứ không phải kế hoạch soạn trước.
2. Nguồn phát biểu, ưu tiên giảm dần (20 §3 C5): (a) 10 dòng đầu bảng xếp hạng rủi ro giả định; (b) BR bị `/spec-review` gắn nhãn `⚡` hoặc ✗ ở cổng **F2/F7** (33 §1); (c) ô M1/M2/M5 còn `?` sau C1–C4 (05 §4); (d) **cột/ý bị hụt ở C2 hoặc C4** — trạng thái nút, message của ca còn lại, khác biệt guest; (e) phát biểu 20 §4 chưa được C1–C4 trả lời, chọn dòng có tiền hoặc tồn dính vào.
3. Viết mỗi ý thành một phát biểu **có con số hoặc trạng thái cụ thể**, ≤15 từ, để AI chỉ cần Đúng/Sai; ý "Sai" xin giá trị đúng ≤8 từ trong cùng câu. Tối đa 10 phát biểu; xếp rủi ro cao lên đầu vì câu trả lời có thể bị cắt giữa.
4. Chạy cổng 5 kiểm tra như chế độ `cau`. Ghi log, tăng `k` lên 5.
5. In kèm: bảng **"phát biểu → BR nào trong spec sẽ phải sửa nếu trả lời Sai"** — để lúc 11:30 vá được trong 10 phút mà không phải suy lại.

## Chế độ `nap`
1. Lấy câu trả lời từ phần dán sau lệnh hoặc file người dùng chỉ. **Không sửa chữ, không tóm tắt** khi ghi log.
2. Ghi vào khối `## C<n>` gần nhất còn trống: "Câu trả lời (nguyên văn)", "Token sau câu này" (thật nếu người dùng cung cấp, không thì ước — **ghi rõ "thật" hay "ước"**), timestamp nhận. Cập nhật sổ hạn mức.
3. Tách **từng ý** thành một dòng RTM `A-xx` (20 §5): `A-xx | C<n> | nội dung ≤12 từ | ⚠? | — | (trống) | ❌ | hh:mm`. Bảng nhiều hàng hoặc câu trả lời liệt kê → **mỗi hàng/mỗi ý một dòng RTM**, không gộp (quy tắc cứng 6).
   - Đánh ⚠ khi khác mặc định ngành: đối chiếu **31 phát biểu** 20 §4 và catalogue 10 §6. ⚠⚠ khi vừa khác mặc định vừa có con số/trạng thái cụ thể.
   - `"Ngoài phạm vi tính năng này"` ⇒ ⛔, vào mục "NGOÀI phạm vi" cuối `rtm.md` (nguồn cho mục 1 spec **và** rào chống VÔ HIỆU buổi chiều — 50 §6).
   - `"Không có quy định riêng."` ⇒ ghi đúng cụm đó, **không** ⛔, **không** coi là bằng chứng phạm vi; ô này thành `G-xx` mặc định ngành.
   - Câu trả lời hụt một phần ⇒ ghi "không trả lời phần <x>" và mở ngay `G-xx` cho phần đó. **Không hỏi lại.**
   - Mâu thuẫn với dòng RTM cũ ⇒ ghi cả hai ID, gắn `↔ A-yy`, đưa vào hàng đợi C5.
4. **Không bịa, không suy diễn.** Ý nào AI không trả lời rõ → `G-xx` với giá trị mặc định ngành, không phải `A-xx` với giá trị đoán.
5. **Kiểm bốn vùng BTC sau khi nạp C2 và C4:**
   - nạp C2 → có dòng RTM cho *hiển thị* và *trạng thái nút* không?
   - nạp C4 → có dòng RTM cho *message lỗi nguyên văn* và *guest vs login* không?
   - Hụt vùng nào: (a) nếu còn câu chưa gửi → đưa vùng đó vào **hàng đợi C5** (phát biểu Đúng/Sai có con số, ví dụ message đề xuất đặt trong ngoặc kép để AI sửa); (b) nếu đã hết câu → **điền mặc định ngành** (20 §4 phát biểu 1 cho guest, 27–31 cho hiển thị/nút; message theo 10 §6) và **khai ở mục 7.4** của spec. In rõ vùng nào đi nhánh (a), vùng nào nhánh (b).
6. **Bảng phủ 10 mục BTC** (in mỗi lần nạp): với mỗi mục 1–10 (32 §1), đếm số dòng RTM (`A-xx` + `G-xx`) đã có dữ kiện cho mục đó. Mục nào 0 dòng → cảnh báo "mục n chưa có dữ kiện" kèm nhóm ID nên dùng làm mặc định: mục 2 → N15, mục 3 → N16, mục 4 → N17, mục 9 → N18, mục 7 → N19.
7. **Sau khi nạp câu 4** (hoặc sau câu cuối trong C1–C4 đã gửi): sinh **toàn bộ dòng `G-xx`** — mỗi ô mô hình còn `?` (05 §4) và mỗi nhóm ID ở **cột phải của bảng ánh xạ 20 §3** thành một dòng `G-xx` với giá trị mặc định ngành đề xuất (nguồn: 20 §4 / 10 §6 / 33 §2) và cột **Rủi ro** để trống cho `/spec-write` bước 14 chấm. Ô nào F2/F8 đòi mà không biết → ghi `G-xx` dạng **luật an toàn hai chiều** (33 §3.3), không chọn hộ một phương án. Đây là bước biến "chưa hỏi" thành "đã có kế hoạch viết", và là điều kiện để `/spec-write` không bỏ trắng mục nào.
8. Cập nhật dòng "Trích rút → RTM:" trong khối log.
9. In ra: bảng ⚠ mới (ID, nội dung, vì sao phản trực giác) · số dòng `A-xx` / `G-xx` / tổng ⚠ · mâu thuẫn `↔` cần restate · bảng phủ 10 mục BTC · token còn lại · câu còn lại.

## Ảnh (tối đa 3 lần)
Mặc định **không dùng** (20 §1-9): một ảnh ≈ 1.000–1.600 token, còn bảng viết bằng text chỉ 400–600. Chỉ đảo quyết định khi người dùng xác nhận BTC nói **ảnh không tính vào 5.000 token** (00 §A2-1) — lúc đó ghi `ảnh tính token: không` vào sổ hạn mức, và chế độ `restate` chuyển sang phương án ảnh: xuất một bảng markdown ≤25 dòng giả định để người dùng chụp gửi kèm, câu hỏi text chỉ còn "trong ảnh, dòng nào Sai; ghi giá trị đúng ≤8 từ?".

## Bước (chuỗi chuẩn trong ngày)
1. 9:30 `ke-hoach` → `ke-hoach-hoi.md` + bảng phủ 10 mục BTC + danh sách ô tự điền.
2. 9:30 / 9:40 / 9:55 / 10:10 `cau 1` … `cau 4`, mỗi câu qua cổng 5 kiểm tra rồi gửi; `nap` ngay sau mỗi câu.
3. Sau `nap` C4: sinh toàn bộ `G-xx`, in bảng phủ 10 mục, bàn RTM cho `/spec-write`.
4. 11:20 `restate` (chỉ khi có `spec.md` + bảng xếp hạng rủi ro) → `nap` → dòng `G-xx` bị trả lời "Sai" chuyển thành `A-xx` ⚠⚠ (20 §5-7) + danh sách việc vá.

## Output bắt buộc
- [ ] Khối câu hỏi copy-paste được, đã qua cổng 5 kiểm tra (chế độ `cau`/`restate`), hoặc bảng ⚠ mới (chế độ `nap`).
- [ ] Chế độ `ke-hoach` và `nap`: **bảng phủ 10 mục BTC**, nêu rõ mục nào chưa có dữ kiện và sẽ điền bằng gì.
- [ ] `log-khach-hang.md` có sổ hạn mức ở dòng đầu, timestamp, nguyên văn, token trước/sau (ghi rõ thật/ước).
- [ ] `rtm.md` có cả dòng `A-xx` và `G-xx`; không dòng nào thiếu cả hai cột "Mã BR" và "Rủi ro".
- [ ] Chế độ `nap` C2/C4: kết luận rõ bốn vùng BTC (hiển thị, nút, message nguyên văn, guest) đã có dữ kiện hay đi nhánh mặc định ngành.
- [ ] Một dòng `Token: … / 5.000 · câu còn lại: …` ở cuối mọi output.

## Không được
- Sinh câu thứ 6, hoặc gộp hai câu hỏi vào một lượt để "tiết kiệm".
- In khối câu hỏi khi cổng 5 kiểm tra còn ✗.
- Gửi C2 mà thiếu cột hiển thị/nút, hoặc gửi C4 mà thiếu ý guest hoặc ý message lỗi nguyên văn.
- Viết câu hỏi tham chiếu lượt trước ("như đã nói", "bổ sung ý 3") — AI không có memory.
- Chạy `restate` trước khi có `spec.md` và bảng xếp hạng rủi ro giả định.
- Hỏi lại một phần đã bị trả lời hụt; hỏi "vì sao"; hỏi dẫn dắt; hỏi điều suy được từ brief hoặc mặc định ngành.
- Coi `"Không có quy định riêng."` là bằng chứng phạm vi, hoặc là lý do bỏ trắng ô trong spec.
- Gộp một câu trả lời liệt kê thành một dòng RTM (ý bị gộp không thành BR và bị catch-all 0.4/0.5 xử sai).
- Bịa câu trả lời khi nạp; tóm tắt câu trả lời trong log; ghi giá trị đoán thành dòng `A-xx`.
- Chọn hộ một phương án cho ô mà cổng F2/F8 đòi và AI chưa trả lời — dùng luật an toàn hai chiều 33 §3.3.
- Dùng ảnh khi chưa xác nhận ảnh có tính token hay không.
- Điền cột Mã BR (việc của `/spec-write`).
