---
name: elicit
description: Quản chuỗi lượt hỏi AI Khách hàng Spec Battle (không giới hạn số câu, 4.000 token, mỗi lượt ĐÚNG MỘT Ý, cấm câu chứa chỉ thị, có nhịp chờ, AI không có memory) — dựng hàng đợi xếp hạng có đường cắt, in từng lượt qua cổng 8 kiểm tra, xử lý câu bị từ chối, nạp câu trả lời vào RTM ngược + log có timestamp, và sinh lượt xác nhận từ bảng xếp hạng rủi ro giả định. Dùng buổi sáng khi người dùng nói "kế hoạch hỏi", "lượt 1", "hỏi AI khách hàng", "bị từ chối", "nạp câu trả lời", "cập nhật RTM", "xác nhận", "elicit".
argument-hint: "ke-hoach [thư-mục] | luot <n> [thư-mục] | tu-choi <n> [thư-mục] | nap [thư-mục] | xac-nhan [thư-mục]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(date *), Bash(mkdir *), Bash(LC_ALL=C.UTF-8 wc *)
---
# /elicit — chuỗi lượt một-ý, gửi theo nhịp, không chờ nhau

Hạn mức cứng (00 §A): **không giới hạn số câu · 4.000 token cho cả hỏi + trả lời · mỗi lượt đúng MỘT Ý · câu chứa chỉ thị hoặc gộp nhiều ý BỊ TỪ CHỐI (không trừ token, không reset nhịp) · nhịp chờ giữa hai lượt (thi thử: 45 giây) · AI Khách hàng KHÔNG có memory · ảnh có tính token ⇒ không dùng**.

Mục tiêu: gửi được **nhiều lượt giá trị cao nhất trong số nhịp có thật**, mọi câu trả lời vào RTM, và mọi ô rớt dưới đường cắt cũng có một dòng RTM (loại `G-xx`) để không chỗ nào trong spec bị bỏ trắng.

**Ràng buộc thật là thời gian, không phải token.** 4.000 token ≈ 20 lượt; nhịp chờ × thời lượng pha hỏi thường cho con số nhỏ hơn. Vì thế mọi chế độ dưới đây đều in `nhịp đã tiêu` bên cạnh `token đã dùng`.

## Input
`$ARGUMENTS` = `ke-hoach [thư-mục]` · `luot <n> [thư-mục]` (`cau <n>` là bí danh cũ, vẫn nhận) · `tu-choi <n> [thư-mục]` · `nap [thư-mục]` · `xac-nhan [thư-mục]`. Thư mục mặc định `battle/`, phải có `brief.md` **và `mo-hinh-bai-toan.md`** — thiếu file mô hình thì chạy `/frame` trước.
Người dùng có thể dán thêm sau lệnh: câu trả lời của AI Khách hàng (chế độ `nap`), nhãn từ chối nguyên văn (chế độ `tu-choi`), hoặc ghi chú (token thật còn lại, nhịp chờ thật đo được).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A (tham số), §A1 (ước token + cách tính trần lượt), §A2 (ô còn hở: nhịp chờ, token của đội hay của người, lý do VÔ HIỆU của đề).
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md`: §1 (10 quy tắc + **cổng 8 kiểm tra** + danh sách đen mệnh lệnh + bảng "muốn biết gì → hỏi dạng nào"), §2 (ngân hàng + **luật chuyển dòng ngân hàng thành lượt gửi được**), §3.1 (dựng hàng đợi), §3.2–3.3 (**28 lượt soạn sẵn**), §3.4 (lượt xác nhận), §3.5 (ánh xạ + phần tự điền), §3b (bốn vùng BTC), §4 (31 phát biểu mặc định ngành), §5 (RTM hai loại dòng), §6 (log, gồm mẫu khối bị từ chối).
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §4 — bảng "ô mô hình → đi vào lượt nào, nếu không hỏi được thì làm gì".
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md`: §1 (nhận diện biến thể), §6 (catalogue ⚠).
- `${CLAUDE_PROJECT_DIR}/knowledge/33-cau-truc-spec-btc.md` §1 — 10 mục spec BTC; mục 4 (message nguyên văn) là vùng **không có mặc định ngành**, nên luôn phải có lượt hỏi nằm trên đường cắt.
- `<thư-mục>/mo-hinh-bai-toan.md` — "Câu hỏi P0", "Mâu thuẫn nội tại của brief".

## Sổ hạn mức (đọc/ghi ở đầu `log-khach-hang.md`)
Dòng đầu file luôn có:
`Đã dùng: <t>/4.000 token · lượt chấp nhận <k> · bị từ chối <r> · nhịp <s> giây · trần lượt ~<N> · đường cắt <hh:mm>`
Không có file → tạo với `0/4.000`, `0`, `0`, nhịp và trần lấy từ brief (không có thì ghi `chưa rõ` và **hỏi người dùng ngay ở output đầu tiên**).

**Chốt mềm, không chốt cứng:** không có trần số câu. Dừng khi (a) token còn <300, hoặc (b) hết thời lượng pha hỏi, hoặc (c) hàng đợi cạn. Khi chạm (a) hoặc (b), mọi lượt chưa gửi **chuyển thành dòng `G-xx`** với giá trị mặc định ngành, và in ra danh sách đó.

## Chế độ `ke-hoach` (chạy một lần, ngay sau `/frame`)
1. Đọc `brief.md`, `mo-hinh-bai-toan.md`. Xác định biến thể (10 §1) và **tên tính năng + tên màn hình đúng như brief gọi** — mọi lượt phải dùng tên đó (quy tắc 20 §1-5).
2. **Tính trần lượt thật** (20 §3.1 bước 1): `(phút pha hỏi ÷ nhịp chờ) − 2`; so với trần token `4.000 ÷ token trung bình mỗi lượt`; **lấy số nhỏ hơn**. Ghi rõ con số nào đang chặn. Brief không nói nhịp → hỏi người dùng, và tạm tính theo 45 giây.
3. Đổ mọi ô `?` của nhóm 1 và nhóm 2 trong `mo-hinh-bai-toan.md` thành danh sách phẳng; thêm ô sinh từ "Mâu thuẫn nội tại của brief" (**luôn xếp lên đầu**).
4. **Chấm giá trị** từng ô theo công thức 20 §3.1 bước 3, xếp giảm dần.
5. **Chọn dạng** từng lượt (20 §1 bảng "muốn biết gì → hỏi dạng nào"); nhắm **≥60% nhị phân**. Ô nào có trong 28 lượt soạn sẵn (20 §3.3) thì lấy nguyên văn về rồi gọt tên; ô mới thì viết theo đúng khung dạng đó.
6. **Chạy cổng 8 kiểm tra trên toàn hàng đợi một lần** (20 §1). In bảng kết quả; còn ✗ thì sửa ngay tại bước này, đừng để tới lúc bấm gửi.
7. **Kẻ đường cắt** tại trần lượt bước 2; tính cộng dồn token tới đường cắt.
8. Ghi `<thư-mục>/ke-hoach-hoi.md`: bảng hàng đợi (số thứ tự · giờ gửi dự kiến theo nhịp · ý · dạng · quyết định điều gì · ~token · cộng dồn) · nguyên văn từng lượt · đường cắt · bảng "ô dưới đường cắt → giá trị mặc định ngành + rủi ro" · bảng cổng 8 kiểm tra.
9. In ra: (a) hàng đợi tới đường cắt kèm giờ gửi; (b) **danh sách ô sẽ KHÔNG hỏi** kèm giá trị mặc định dự kiến và rủi ro — danh sách này quan trọng ngang danh sách trên, nó là phần spec sẽ viết bằng giả định; (c) một dòng nhắc phân vai: **một người bấm gửi đúng nhịp, người còn lại viết spec song song từ lượt thứ 3** (00 §I).

## Chế độ `luot <n>`
1. Đọc sổ hạn mức. Token còn <300 hoặc quá giờ đường cắt → **từ chối sinh lượt mới**, in danh sách việc thay thế (điền mặc định ngành theo 30 §1b, luật an toàn hai chiều 32 §3.3).
2. Lấy lượt n từ `ke-hoach-hoi.md` (chưa có file → chạy `ke-hoach` trước). `n ≤ k` và đã có khối log → in lại từ log thay vì sinh mới.
3. **Cổng 8 kiểm tra (20 §1), in kết quả từng dòng ✓/✗; còn ✗ thì KHÔNG in khối copy:**
   một dấu `?` · đúng một ý (tách được thành hai câu hỏi độc lập ⇒ ✗) · tự chứa (có tên tính năng + màn hình) · **không một chữ mệnh lệnh** (grep danh sách đen 20 §1-2) · không viện dẫn tài liệu · không nhắc bộ máy chấm · không dẫn dắt (nhị phân nêu đủ hai vế) · câu trả lời một từ vẫn dùng được.
4. Ước token lượt này; nếu vượt token còn lại → đổi lượt mở thành lượt nhị phân hẹp hơn và nói rõ đã đổi gì, hoặc bỏ lượt và chuyển ô sang `G-xx`.
5. Timestamp bằng Bash `date "+%Y-%m-%d %H:%M:%S"`. Ghi khối `## L<n> — <tên ý>` vào `log-khach-hang.md` theo mẫu 20 §6, `Trạng thái: chờ`, để trống phần câu trả lời. Tăng `k`.
6. In ra: (a) **một dòng câu hỏi duy nhất** trong code block, copy nguyên dòng; (b) `Token: hỏi ~X · dự kiến trả lời ~Y · còn lại ~Z / 4.000 · lượt tiếp theo gửi lúc ~<hh:mm:ss>` (giờ hiện tại + nhịp); (c) một dòng "nếu câu trả lời không rõ thì ô này tự điền bằng: …".

## Chế độ `tu-choi <n>`
1. Nhận nhãn từ chối nguyên văn từ người dùng. Phân loại: **chứa chỉ thị** · **nhiều câu hỏi trong một lượt** · lý do khác (ghi nguyên văn).
2. Ghi khối `## L<n> — … [BỊ TỪ CHỐI]` theo mẫu 20 §6: **không cộng token**, cộng 1 vào `bị từ chối` và vào nhịp đã tiêu.
3. Sửa đúng lỗi đó, không sửa thêm: chỉ thị ⇒ xóa sạch vế mệnh lệnh, giữ nguyên phần nghi vấn; nhiều ý ⇒ **tách thành nhiều lượt**, xếp hạng lại và chỉ gửi lượt cao nhất ngay, phần còn lại chèn vào hàng đợi.
4. Chạy lại cổng 8 kiểm tra, in bản đã sửa thành khối `L<n>b` để gửi lại ngay.
5. Nếu cùng một lượt bị từ chối **hai lần**: bỏ lượt đó, chuyển ô sang `G-xx`, và nói rõ — nhịp chờ đắt hơn dữ kiện đó.

## Chế độ `xac-nhan` (chạy sau khi có bản nháp spec)
1. Điều kiện vào: `spec.md` tồn tại **và** `/spec-write` đã in bảng xếp hạng rủi ro giả định. Thiếu → dừng, nói rõ vì sao.
2. Nguồn, ưu tiên giảm dần (20 §3.4): (a) đầu bảng xếp hạng rủi ro giả định; (b) BR nhãn `⚡` hoặc ✗ cổng F2/F7; (c) ô M1/M2/M5 còn `?`; (d) phát biểu 20 §4 có tiền/tồn dính vào.
3. **Mỗi phát biểu một lượt riêng.** Ưu tiên viết lại thành **câu nhị phân** ("…A hay B?") thay vì câu xác nhận ("…điều này đúng không?"): cùng giá, không dẫn dắt, nên dùng được làm bằng chứng kháng nghị. Chỉ dùng dạng xác nhận khi ô đó không có hai phương án rõ ràng.
4. Sinh danh sách xếp theo rủi ro giảm dần, kèm ước token cộng dồn và giờ gửi theo nhịp; cắt tại token còn 300 hoặc hết giờ.
5. Chạy cổng 8 kiểm tra từng lượt. Ghi log, tăng `k` theo số lượt thật sự gửi.
6. In kèm: bảng "lượt → BR nào trong spec phải sửa nếu câu trả lời khác giả định" — để vá được trong 15 phút mà không phải suy lại.

## Chế độ `nap`
1. Lấy câu trả lời từ phần dán sau lệnh hoặc file người dùng chỉ. **Không sửa chữ, không tóm tắt** khi ghi log.
2. Ghi vào khối `## L<n>` gần nhất còn trống: `Trạng thái: chấp nhận`, "Câu trả lời (nguyên văn)", "Token sau lượt này" (thật nếu người dùng cung cấp, không thì ước — ghi rõ "thật" hay "ước"), timestamp nhận. Cập nhật sổ hạn mức.
3. Tách **từng ý** thành một dòng RTM `A-xx` (20 §5): `A-xx | L<n> | nội dung ≤12 từ | ⚠? | — | (trống) | ❌ | hh:mm`. Câu trả lời liệt kê nhiều hàng → **mỗi hàng một dòng RTM**, không gộp.
   - Đánh ⚠ khi khác mặc định ngành (đối chiếu 31 phát biểu 20 §4 và catalogue 10 §6). ⚠⚠ khi vừa khác mặc định vừa có con số/trạng thái cụ thể.
   - "Ngoài phạm vi tính năng này" ⇒ trạng thái ⛔, ghi vào mục "NGOÀI phạm vi" cuối `rtm.md` (nguồn cho §1 spec **và** rào chống VÔ HIỆU buổi chiều — 50 §6).
   - "Không có quy định riêng." ⇒ ghi đúng cụm đó, **không** đánh ⛔ và **không** coi là bằng chứng phạm vi (50 §6-9). Ô này chuyển thành `G-xx` mặc định ngành.
   - Câu trả lời lạc đề hoặc chỉ trả lời một nửa ⇒ ghi đúng những gì nhận được, mở `G-xx` cho phần thiếu. **Hỏi lại chỉ khi ô đó ở nhóm rủi ro Cao** — hỏi lại giờ chỉ tốn một nhịp, không mất một trong năm câu như luật cũ, nhưng vẫn phải cân với lượt kế tiếp trong hàng đợi.
4. **Không bịa, không suy diễn.** Ý nào AI không trả lời rõ → `G-xx` với giá trị mặc định ngành, không phải `A-xx` với giá trị đoán.
5. **Khi chạm đường cắt** (hết giờ, hết token, hoặc hàng đợi cạn): sinh **toàn bộ dòng `G-xx`** — mỗi ô mô hình còn `?` và mỗi lượt chưa gửi thành một dòng `G-xx` với giá trị mặc định ngành (nguồn: 20 §4 / 10 §6), cột Rủi ro để trống cho `/spec-write` chấm.
6. Cập nhật dòng "Trích rút → RTM:" trong khối log.
6b. **Bảng phủ 10 mục BTC**: với mỗi mục 1–10 (knowledge/33 §1), đếm số dòng RTM đã có dữ kiện. Mục nào 0 dòng → cảnh báo "mục n chưa có dữ kiện, sẽ viết bằng mặc định ngành" kèm nhóm ID lẽ ra phủ nó (mục 2 → N15, mục 3 → N16, mục 4 → N17, mục 9 → N18, mục 7 → N19). **Mục 4 trắng là lỗi nặng nhất** — message nguyên văn không có mặc định ngành, và giờ nó hỏi được (20 §3b), nên mục 4 trắng nghĩa là hàng đợi xếp sai thứ tự chứ không phải thiếu hạn mức.
7. In ra: bảng ⚠ mới (ID, nội dung, vì sao phản trực giác) · số dòng `A-xx` / `G-xx` / tổng ⚠ · mâu thuẫn với dòng RTM cũ (gắn `↔`, đưa lên đầu hàng đợi còn lại) · token còn lại · nhịp đã tiêu · số lượt còn kịp gửi trước đường cắt.

## Output bắt buộc
- [ ] Khối câu hỏi **một dòng, một ý**, copy-paste được, đã qua cổng 8 kiểm tra (chế độ `luot`/`xac-nhan`/`tu-choi`), hoặc bảng ⚠ mới (chế độ `nap`).
- [ ] `log-khach-hang.md` có sổ hạn mức ở dòng đầu, timestamp, trạng thái từng lượt (chấp nhận / bị từ chối), nguyên văn.
- [ ] `rtm.md` có cả dòng `A-xx` và `G-xx`; không dòng nào thiếu cả hai cột "Mã BR" và "Rủi ro".
- [ ] Chế độ `nap`: bảng phủ 10 mục BTC, nêu rõ mục nào chưa có dữ kiện.
- [ ] Một dòng `Token: … / 4.000 · lượt đã gửi … · còn kịp ~… lượt trước <hh:mm>` ở cuối mọi output.

## Không được
- In khối câu hỏi khi cổng 8 kiểm tra còn ✗.
- **Viết bất kỳ chỉ thị nào cho AI Khách hàng**: ép format, xin bảng, cap số dòng/số từ, "không giải thích", "chọn 1", "liệt kê", "hãy…". Đây là lỗi bị gán nhãn injection và bị từ chối (00 §A).
- Gộp hai ý vào một lượt để "tiết kiệm nhịp" — bị từ chối, mất đúng cái nhịp định tiết kiệm.
- Gộp nhiều phát biểu xác nhận vào một lượt (kiểu câu restate cũ).
- Viết lượt tham chiếu lượt trước ("như đã nói", "bổ sung ý 3") — AI không có memory.
- Hỏi dạng mở ở ô đã có mặc định ngành an toàn; hỏi "vì sao"; hỏi dẫn dắt.
- Chạy `xac-nhan` trước khi có `spec.md` và bảng xếp hạng rủi ro giả định.
- Coi `"Không có quy định riêng."` là bằng chứng phạm vi, hoặc là lý do bỏ trắng ô trong spec.
- Bịa câu trả lời khi nạp; tóm tắt câu trả lời trong log; ghi giá trị đoán thành dòng `A-xx`.
- Dùng ảnh (có tính token — 00 §A).
- Điền cột Mã BR (việc của `/spec-write`).
