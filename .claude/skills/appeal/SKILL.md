---
name: appeal
description: Dựng hồ sơ kháng nghị cho tối đa 3 ca test CÔNG bị chấm VÔ HIỆU trong Spec Battle — xác nhận ca có đủ điều kiện kháng nghị, xếp bằng chứng phạm vi theo 3 mức (lời AI Khách hàng / câu brief / đồng thuận chéo 2 spec), chấm xác suất thắng, và viết text kháng nghị ≤150 từ gửi AI. Dùng 16:00–17:00 ngày thi khi người dùng nói "kháng nghị", "appeal", "bị chấm VÔ HIỆU".
argument-hint: "[thư-mục-trận] + dán danh sách kết quả VÔ HIỆU"
allowed-tools: Read, Write, Grep, Glob
---
# /appeal — Hồ sơ kháng nghị ≤3 ca VÔ HIỆU

**Điều kiện kháng nghị (00 §A, chốt 09/09): chỉ ca test CÔNG của mình bị chấm VÔ HIỆU.** Nộp bằng **text gửi AI**, ban tổ chức review; ≤3 ca/đội.

Hai loại ca của kit cũ **không còn kháng nghị được** — nếu người dùng dán vào, nói rõ và loại ngay, đừng dựng hồ sơ:
- spec mình bị chấm TRÚNG dù spec có quy định;
- AI So khớp hiểu sai ngữ nghĩa làm test mình thành TRƯỢT.

**Điều duy nhất được tranh: tình huống nằm TRONG phạm vi specs thật.** Không tranh đáp án chuẩn, không tranh cách Executor trả lời, không tranh chất lượng test. Mọi câu trong text kháng nghị phải phục vụ đúng một kết luận đó.

## Input
`$ARGUMENTS` = `[thư-mục-trận]` (mặc định `battle/`) + phần người dùng dán: từng kết quả VÔ HIỆU gồm ID test, tình huống, lý do đối chiếu của AI So khớp (nếu được xem).
Đọc: `tests/*.md` (hồ sơ finding — **gói bằng chứng phạm vi đã thu sẵn lúc 14:30**), `log-khach-hang.md` (mọi câu trả lời nguyên văn), `brief.md`, `dong-thuan-cheo.md`, `spec.md`, `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §7 (mẫu text + 3 mức bằng chứng), §2-9, `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A, §D.

## Bước
1. **Sàng điều kiện.** Mỗi ca người dùng dán: kết quả máy có đúng là **VÔ HIỆU** và test đó do **đội mình bắn** không? Không đủ hai điều kiện ⇒ loại, ghi một dòng lý do. Đây là bước đầu vì nó loại phần lớn ca của kit cũ.
2. **Lấy gói bằng chứng có sẵn** từ hồ sơ finding của test đó (`/attack` bước 7 đã trích nguyên văn). Có sẵn ⇒ sang bước 4. Không có ⇒ bước 3.
3. **Tìm bằng chứng bằng Grep**, không nhớ mò, theo thứ tự độ mạnh:
   - **Mức 1** — Grep toàn bộ `log-khach-hang.md` (mọi lượt đã chấp nhận; bỏ qua khối `[BỊ TỪ CHỐI]`) tìm câu trả lời nghiệp vụ về đúng tình huống đó → trích khối `## L<n>`, timestamp, câu hỏi và câu trả lời nguyên văn. Đối chiếu mục NGOÀI phạm vi trong câu trả lời về phạm vi (nếu lượt đó đã gửi): nghiệp vụ nằm trong danh sách NGOÀI ⇒ **bỏ ca**, máy chấm đúng.
   - **Mức 2** — Grep `brief.md` tìm câu nhắc nghiệp vụ đó → trích nguyên văn kèm vị trí (đoạn/câu). Không trích được câu trực tiếp ⇒ không dùng mức 2 (đừng dựa vào "brief hàm ý").
   - **Mức 3** — Grep 3 spec đối thủ đã tải + `dong-thuan-cheo.md`: ≥2 spec có luật cho nghiệp vụ đó → trích số mục của **cả hai**.
   - `"Không có quy định riêng."` trong log **không phải** bằng chứng phạm vi (50 §6-9) — nó chỉ nói specs thật không quy định. Dòng `G-xx` (giả định của đội) cũng không phải bằng chứng.
   - Trích nguyên văn giữ đúng ký tự (kể cả backtick, mã); cắt đoạn thì đánh dấu `[…]`.
4. **Xác suất thắng**: Cao = mức 1 khớp trực tiếp nghiệp vụ · TB = mức 2, hoặc mức 1 nói về nghiệp vụ lân cận · Thấp–TB = mức 3 · Thấp = không có mức nào. **Bỏ mọi ca Thấp**, ghi lý do 1 dòng — nộp ca không bằng chứng làm loãng hai ca còn lại trong mắt người review.
5. **Xếp hạng & chọn 3**: theo xác suất, rồi theo mức bằng chứng 1 > 2 > 3, rồi theo điểm thu lại được (một ca thắng đổi −1 thành 0 hoặc +2, tức lãi 1–3 điểm — 00 §D1; ưu tiên ca mà dry-run trước đó dự đoán TRÚNG, vì nó lãi 3).
6. **Viết text cho mỗi ca chọn** theo mẫu knowledge/50 §7, **≤150 từ**, đúng 6 dòng: `Test` · `Kết quả máy` · `Đề nghị` (chấm lại phạm vi) · `Căn cứ` (trích nguyên văn 1 nguồn mạnh nhất + vị trí) · `Lập luận` (2 câu, chỉ về phạm vi) · `Giới hạn` (1 câu tự nêu điểm yếu của bằng chứng khi dùng mức 2 hoặc 3).
   Dòng `Giới hạn` không phải khiêm tốn: nó chặn trước lý bác duy nhất người review có ("brief nhắc không có nghĩa specs thật có luật"), và làm phần còn lại đáng tin hơn.

## Output `appeal.md`
1. Bảng sàng: ID | kết quả máy | đủ điều kiện kháng nghị? | mức bằng chứng | xác suất | chọn/bỏ | lý do 1 dòng.
2. **Text kháng nghị của 3 ca, mỗi ca trong một code block copy nguyên khối** — đây là thứ gửi đi, không phải bản tóm tắt.
3. Hai câu hỏi người review có thể hỏi mỗi ca + trả lời 1 câu mỗi câu.
4. Dòng cuối: tổng điểm có thể thu lại (số ca × 1 đến 3), và các ca đã bỏ kèm lý do — để rút bài học cho lần sau.

## Không được
- Dựng hồ sơ cho ca không phải VÔ HIỆU, hoặc cho test do đội khác bắn vào spec mình.
- Tranh đáp án chuẩn, tranh cách Executor trả lời, tranh chất lượng spec của mình — ngoài phạm vi cơ chế kháng nghị.
- Lập luận không có trích dẫn nguyên văn; trích dẫn không tồn tại trong log/brief/spec đối thủ (Grep xác nhận trước khi ghi).
- Dùng `"Không có quy định riêng."` hoặc dòng `G-xx` làm bằng chứng.
- Chọn quá 3 ca; chọn ca xác suất Thấp; viết quá 150 từ một ca.
- Sửa spec, log, hay test sau khi khóa.
