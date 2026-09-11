---
name: elicit
description: Lập lượt hỏi AI Khách hàng cho Spec Battle (batch 5–8 câu đóng, ép format, tối ưu token) và nạp câu trả lời vào RTM ngược + log có timestamp. Dùng buổi sáng 9:30–11:00 khi người dùng nói "lượt hỏi", "hỏi AI khách hàng", "nạp câu trả lời", "cập nhật RTM", "elicit", "elicitation round".
argument-hint: "lượt <n> [thư-mục] | nạp [thư-mục]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(date *), Bash(mkdir *)
---
# /elicit — Hỏi AI Khách hàng & cập nhật RTM

Mục tiêu: mỗi lượt hỏi lấy được nhiều dữ kiện nhất trên mỗi token, và mọi câu trả lời được truy vết vào RTM ngược để không dữ kiện ⚠ nào bị bỏ ngoài spec.

## Input
`$ARGUMENTS` = `lượt <n> [thư-mục]` hoặc `nạp [thư-mục]`. Thư mục mặc định `battle/`. Thư mục phải có `brief.md`.
Người dùng có thể dán thêm sau lệnh: câu trả lời của AI Khách hàng (chế độ nạp) hoặc ghi chú (token còn lại, chủ đề ưu tiên).

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A — lấy TOKEN_MAX. Nếu ô ghi "CHỜ 09/09" và log chưa ghi hạn mức: hỏi người dùng một lần, ghi vào dòng đầu `log-khach-hang.md` dạng `TOKEN_MAX = <n> (giả định/đã chốt)`.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md`: §1 (10 quy tắc hỏi), §2 (ngân hàng câu theo ID, **19 nhóm — N15–N19 phục vụ cấu trúc spec BTC**), §3 (**7 lượt** batch sẵn + ngân sách % token), §4 (30 phát biểu Đúng/Sai), §5 (mẫu RTM), §6 (mẫu log).
- `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` §1 — 10 mục spec BTC yêu cầu; mỗi lượt hỏi phải góp dữ kiện cho ít nhất một mục, và tới 11:00 phải có dữ kiện cho **cả 10 mục**.
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md`: §1 (nhận diện biến thể), §6 (edge case ⚠ để ưu tiên câu hỏi và để đánh ⚠ khi nạp).

## Khởi tạo (chỉ khi thiếu file)
Nếu chưa có `rtm.md` → tạo từ mẫu bảng 7 cột ở knowledge/20 §5 (chỉ header). Nếu chưa có `log-khach-hang.md` → tạo với dòng `TOKEN_MAX = ...` và header theo knowledge/20 §6.

## Chế độ `lượt <n>`
1. Đọc `brief.md`, `rtm.md`, `log-khach-hang.md`. Ghi nhận: biến thể đã xác định chưa (knowledge/10 §1), danh sách ID đã hỏi, token đã dùng.
2. Chọn khối lượt n từ knowledge/20 §3 làm khung (thứ tự chạy đề xuất: L1 → L2 → L3 → **L7** → L4 → L5 → L6 → Restate). Thay các ID bằng câu hỏi nguyên văn từ §2. Loại câu đã hỏi, câu ngoài phạm vi theo brief hoặc theo danh sách NGOÀI đã nhận. Thêm tối đa 2 câu follow-up từ ⚠ lượt trước (câu trả lời mơ hồ → bắt định nghĩa/con số, quy tắc §1-9).
3. Ràng buộc bắt buộc:
   - Lượt 1 phải có: câu nhận diện biến thể (N1-01) và câu liệt kê NGOÀI phạm vi + TRONG phạm vi (N1-02, N1-03).
   - Lượt 5 = bộ Đúng/Sai knowledge/20 §4, bỏ phát biểu đã được trả lời trực tiếp ở lượt trước, giữ đúng số thứ tự còn lại.
   - **Lượt 7 = khối màn hình/event/validation/API** (N15–N19), phục vụ mục 2, 3, 4, 9 và 7 của cấu trúc BTC. Trong đó **không được cắt** câu 1–4 (item + ẩn/disable + guest + message lỗi nguyên văn) — hai vùng Executor đoán sai nhiều nhất là message và guest.
   - **Trước 10:45, nếu chưa hỏi được message lỗi nguyên văn (N17-03/04/05) hoặc khác biệt guest (N15-06)** → chèn vào lượt này bất kể n.
   - Mỗi lượt 5–8 câu (Đúng/Sai được tính là 1 câu cho cả bộ). Dòng đầu khối luôn là câu ép format + cap độ dài (§1-2). Không câu mở đứng một mình, không "vì sao", không dẫn dắt (§1-6, §1-7).
   - Nếu chưa tới 11:00 mà chưa hỏi NGOÀI phạm vi → chèn N1-02 vào lượt này bất kể n.
4. Ước lượng token của khối (số từ × 1,5) + dự kiến câu trả lời (theo % ngân sách lượt ở §3). Nếu tổng vượt token còn lại → cắt theo thứ tự ưu tiên ghi ở §3.
5. Lấy timestamp bằng Bash `date "+%Y-%m-%d %H:%M:%S"`. Ghi vào `log-khach-hang.md` khối `## Lượt n — <tên>` với "Thời gian gửi", "Token trước lượt", "Câu hỏi (nguyên văn)" theo mẫu §6; để trống phần câu trả lời.
6. In ra cho người dùng: (a) khối prompt trong một code block để copy nguyên khối; (b) một dòng "Token ước lượng: hỏi ~X, trả lời ~Y, còn lại ~Z"; (c) danh sách ID vừa hỏi.

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
7. **Bảng phủ 10 mục BTC**: với mỗi mục 1–10 (knowledge/32 §1), đếm số dòng RTM đã có dữ kiện cho mục đó. Mục nào 0 dòng → in cảnh báo "mục n chưa có dữ kiện" kèm ID câu hỏi nên hỏi lượt sau (mục 2 → N15, mục 3 → N16, mục 4 → N17, mục 9 → N18, mục 7 → N19).

## Output bắt buộc
- [ ] Khối prompt copy-paste được (chế độ lượt) hoặc bảng ⚠ mới (chế độ nạp).
- [ ] Chế độ nạp: bảng phủ 10 mục BTC, nêu rõ mục nào chưa có dữ kiện.
- [ ] `log-khach-hang.md` có timestamp, nguyên văn, token trước/sau.
- [ ] `rtm.md` cập nhật, cột Mã BR trống, ⚠ đúng.
- [ ] Dòng token còn lại.

## Không được
- Hỏi mở một mình, hỏi "vì sao", hỏi dẫn dắt.
- Quá 8 câu một lượt (lượt 7 dạng bảng được tối đa 12 mục vì mỗi mục là một ô bảng); bỏ N1-02 (NGOÀI phạm vi) trước 11:00; bỏ lượt 7 (spec sẽ trống mục 2, 3, 4, 9).
- Bịa hoặc suy diễn câu trả lời khi nạp; tóm tắt câu trả lời trong log.
- Điền cột Mã BR (việc của /spec-write).
