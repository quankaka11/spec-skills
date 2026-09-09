---
name: elicit
description: Lập lượt hỏi AI Khách hàng cho Spec Battle (batch 5–8 câu đóng, mục tiêu & ràng buộc hỏi trước tham số, ép format, ước token đúng cho tiếng Việt) và nạp câu trả lời vào RTM ngược + log có timestamp; có lượt restate bắt buộc để xác nhận giả định. Dùng buổi sáng 9:30–11:00 khi người dùng nói "lượt hỏi", "hỏi AI khách hàng", "nạp câu trả lời", "cập nhật RTM", "restate", "elicit", "elicitation round".
argument-hint: "lượt <n> [thư-mục] | restate [thư-mục] | nạp [thư-mục]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(date *), Bash(mkdir *)
---
# /elicit — Hỏi AI Khách hàng & cập nhật RTM

Mục tiêu: mỗi lượt hỏi lấy được nhiều dữ kiện nhất trên mỗi token, và mọi câu trả lời được truy vết vào RTM ngược để không dữ kiện ⚠ nào bị bỏ ngoài spec.

## Input
`$ARGUMENTS` = `lượt <n> [thư-mục]`, `restate [thư-mục]`, hoặc `nạp [thư-mục]`. Thư mục mặc định `battle/`. Thư mục phải có `brief.md` **và `mo-hinh-bai-toan.md`** — thiếu file mô hình thì chạy `/frame` trước, vì lượt 1 lấy câu hỏi từ đó.
Người dùng có thể dán thêm sau lệnh: câu trả lời của AI Khách hàng (chế độ nạp) hoặc ghi chú (token còn lại, chủ đề ưu tiên).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A — lấy TOKEN_MAX. Nếu ô ghi "CHỜ 09/09" và log chưa ghi hạn mức: hỏi người dùng một lần, ghi vào dòng đầu `log-khach-hang.md` dạng `TOKEN_MAX = <n> (giả định/đã chốt)`.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md`: §1 (10 quy tắc hỏi), §2 (ngân hàng câu theo ID, **gồm nhóm N0 mục tiêu & ràng buộc**), §3 (6 lượt batch sẵn + ngân sách % token + hệ số token tiếng Việt), §4 (phát biểu Đúng/Sai), §5 (mẫu RTM), §6 (mẫu log).
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §4 — bảng "ô mô hình → sinh câu hỏi loại nào, vào lượt nào".
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md` §6 — sáu câu chuyển từ hit cổng F; dùng khi `/spec-review` đã trả về danh sách `HỎI`.
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md`: §1 (nhận diện biến thể), §6 (edge case ⚠ để ưu tiên câu hỏi và để đánh ⚠ khi nạp).
- `<thư-mục>/mo-hinh-bai-toan.md` — mục "Câu hỏi P0 sinh ra từ mô hình" và "Mâu thuẫn nội tại của brief".

## Khởi tạo (chỉ khi thiếu file)
Nếu chưa có `rtm.md` → tạo từ mẫu bảng 7 cột ở knowledge/20 §5 (chỉ header). Nếu chưa có `log-khach-hang.md` → tạo với dòng `TOKEN_MAX = ...` và header theo knowledge/20 §6.

## Chế độ `lượt <n>`
1. Đọc `brief.md`, `rtm.md`, `log-khach-hang.md`. Ghi nhận: biến thể đã xác định chưa (knowledge/10 §1), danh sách ID đã hỏi, token đã dùng.
2. Chọn khối lượt n từ knowledge/20 §3 làm khung. Thay các ID bằng câu hỏi nguyên văn từ §2. Loại câu đã hỏi, câu ngoài phạm vi theo brief hoặc theo danh sách NGOÀI đã nhận. Thêm tối đa 2 câu follow-up từ ⚠ lượt trước (câu trả lời mơ hồ → bắt định nghĩa/con số, quy tắc §1-9).
3. Ràng buộc bắt buộc:
   - **Lượt 1 phải có, theo đúng thứ tự này:** (a) mọi câu sinh từ mục "Mâu thuẫn nội tại của brief" trong `mo-hinh-bai-toan.md`; (b) N0-01 (điều PHẢI KHÔNG xảy ra); (c) N1-02 + N1-03 (NGOÀI + TRONG phạm vi); (d) N0-06 và N0-02 (guest + đơn vị neo hạn mức); (e) N1-01 nhận diện biến thể. Mục tiêu và ràng buộc hỏi **trước** tham số: chúng quyết định luật nào cần tồn tại, còn tham số chỉ điền số vào luật đã biết là cần.
   - Lượt 5 = bộ Đúng/Sai knowledge/20 §4, bỏ phát biểu đã được trả lời trực tiếp ở lượt trước, giữ đúng số thứ tự còn lại.
   - Mỗi lượt 5–8 câu (Đúng/Sai được tính là 1 câu cho cả bộ). Dòng đầu khối luôn là câu ép format + cap độ dài (§1-2). Không câu mở đứng một mình, không "vì sao", không dẫn dắt (§1-6, §1-7).
   - Nếu chưa tới 11:00 mà chưa hỏi NGOÀI phạm vi → chèn N1-02 vào lượt này bất kể n.
   - Ô mô hình còn `?` sau lượt 4 mà thuộc M1/M2/M5 → chèn vào lượt này, ưu tiên trên câu tham số P1.
4. **Ước lượng token**: phần tiếng Việt `số từ × 2,5`, phần mã/số/tiếng Anh `× 1,5` (knowledge/20 §3). Hệ số 1,5 dùng chung cho cả khối là **ước thiếu ~40%** và làm cháy token trước lượt restate. Sau lượt 1, nếu người dùng cung cấp số token thực tế → tính hệ số đo được (`token thực / số từ đã gửi`) và ghi vào log để dùng cho các lượt sau. Cộng thêm dự kiến câu trả lời (theo % ngân sách lượt ở §3). Nếu tổng vượt token còn lại → cắt theo thứ tự ưu tiên ghi ở §3.
4b. **Bảo vệ 15% token cho lượt restate.** Nếu token còn lại sau lượt này < 15% TOKEN_MAX → cảnh báo người dùng ngay trong output và đề nghị bỏ lượt 6 để chạy restate. Restate là lượt duy nhất kiểm được rằng đội hiểu đúng; hết token mà chưa restate thì mọi dòng ⚠ vẫn là suy luận một chiều.
5. Lấy timestamp bằng Bash `date "+%Y-%m-%d %H:%M:%S"`. Ghi vào `log-khach-hang.md` khối `## Lượt n — <tên>` với "Thời gian gửi", "Token trước lượt", "Câu hỏi (nguyên văn)" theo mẫu §6; để trống phần câu trả lời.
6. In ra cho người dùng: (a) khối prompt trong một code block để copy nguyên khối; (b) một dòng "Token ước lượng: hỏi ~X, trả lời ~Y, còn lại ~Z"; (c) danh sách ID vừa hỏi.

## Chế độ `restate` (bắt buộc, chạy sau 10:45 hoặc ngay sau `/spec-review`)
1. Nguồn phát biểu, ưu tiên giảm dần: (a) danh sách `HỎI` do `/spec-review` sinh ra; (b) mọi dòng `[GIẢ ĐỊNH]` xếp hạng rủi ro cao do `/spec-write` sinh ra; (c) dòng ⚠ trong RTM có mâu thuẫn `↔`; (d) ô mô hình M1/M2/M5 còn `?`.
2. Viết mỗi ý thành một phát biểu **có con số hoặc trạng thái cụ thể**, để AI Khách hàng chỉ cần Đúng/Sai. Không hỏi "có đúng không" chung chung. Ý nào "Sai" thì xin giá trị đúng ≤8 từ ngay trong cùng lượt.
3. Tối đa 12 phát biểu một lượt; xếp phát biểu rủi ro cao lên đầu vì lượt có thể bị cắt giữa.
4. Câu cuối lượt: "Còn quy tắc nào về <chủ đề còn `?` nhiều nhất> chưa được hỏi? Liệt kê tên, không mô tả."
5. Ghi log như chế độ `lượt`; sau khi nạp, mỗi phát biểu "Sai" tạo một dòng RTM mới ⚠⚠ và một việc sửa spec.

## Chế độ `nạp`
1. Lấy câu trả lời từ phần dán sau lệnh hoặc từ file người dùng chỉ. Không sửa chữ, không tóm tắt khi ghi log.
2. Ghi vào khối lượt gần nhất trong `log-khach-hang.md`: "Câu trả lời (nguyên văn)", "Token sau lượt" (ước lượng nếu người dùng không cung cấp), timestamp nhận (Bash date).
3. Tách từng ý trả lời thành một dòng RTM (knowledge/20 §5): `A-xx | ID câu hỏi | câu trả lời rút gọn ≤12 từ | ⚠? | (trống) | ❌ | hh:mm`. Câu trả lời dạng liệt kê ("Được: a, b, c / Không được: d, e"; bảng nhiều hàng) → MỖI ý một dòng RTM, không gộp thành một dòng ⚠ chính (ý bị gộp sẽ không thành BR và bị catch-all 0.4/0.5 xử sai). Cột "Mã BR" để trống — /spec-write sẽ điền.
   - Đánh ⚠ khi câu trả lời khác mặc định phổ biến: đối chiếu với 30 phát biểu knowledge/20 §4 (trả lời "Sai" ⇒ ⚠) và catalogue ⚠ knowledge/10 §6. Đánh ⚠⚠ khi vừa khác mặc định vừa có con số/trạng thái cụ thể.
   - "Ngoài phạm vi" ⇒ trạng thái ⛔, ghi vào mục "NGOÀI phạm vi" cuối rtm.md (nguồn cho §1 spec và cho chấm phạm vi buổi chiều).
   - Mâu thuẫn với dòng RTM cũ ⇒ ghi cả 2 ID, đánh dấu `↔ A-yy`, đưa vào danh sách restate.
4. Không bịa: ý nào AI Khách hàng không trả lời rõ → dòng RTM ghi "chưa rõ" và đề xuất câu follow-up.
5. Cập nhật dòng "Trích rút → RTM:" trong khối log.
6. In ra: bảng ⚠ mới (ID, nội dung, vì sao phản trực giác), số dòng RTM tổng / số ⚠, danh sách ≤3 câu follow-up đóng cho lượt sau, danh sách mâu thuẫn cần restate.

## Output bắt buộc
- [ ] Khối prompt copy-paste được (chế độ lượt) hoặc bảng ⚠ mới (chế độ nạp).
- [ ] `log-khach-hang.md` có timestamp, nguyên văn, token trước/sau.
- [ ] `rtm.md` cập nhật, cột Mã BR trống, ⚠ đúng.
- [ ] Dòng token còn lại.

## Không được
- Chạy `lượt 1` khi chưa có `mo-hinh-bai-toan.md`.
- Hỏi mở một mình, hỏi "vì sao", hỏi dẫn dắt.
- Quá 8 câu một lượt; bỏ N1-02 (NGOÀI phạm vi) trước 11:00; bỏ N0-01/N0-02/N0-06 khỏi lượt 1.
- Ước token bằng hệ số 1,5 cho phần tiếng Việt.
- Tiêu hết token mà chưa chạy lượt `restate`.
- Bịa hoặc suy diễn câu trả lời khi nạp; tóm tắt câu trả lời trong log.
- Điền cột Mã BR (việc của /spec-write).
