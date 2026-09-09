---
name: frame
description: Dựng mô hình bài toán từ brief trong 20 phút trước khi hỏi AI Khách hàng — mục tiêu & thước đo, dòng tiền, dòng tồn & nguồn chân lý, biên hệ thống 6 láng giềng, mô hình lạm dụng, 12 kịch bản suy biến — rồi sinh danh sách câu hỏi P0. Dùng 9:00–9:30 ngày thi khi người dùng nói "đọc brief", "hiểu bài toán", "mô hình bài toán", "frame", "dựng khung"; chạy lại lúc 11:40 để lập bảng Mục tiêu ↔ Luật.
argument-hint: "[thư-mục] | muc-tieu-luat [thư-mục]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(date *), Bash(mkdir *)
---
# /frame — Mô hình bài toán trước khi hỏi

Mục tiêu: biến brief một trang thành 6 khối mô hình có thể đối chiếu, để mọi luật viết sau đều trả lời được "phục vụ mục tiêu nào" và "có khả thi không". Không có bước này, spec đạt mọi cổng hình thức mà vẫn sai bài toán (bằng chứng: `knowledge/05-hieu-bai-toan.md` §0).

## Input
`$ARGUMENTS` = `[thư-mục]` (mặc định `battle/`) hoặc `muc-tieu-luat [thư-mục]` (chế độ thứ hai, chạy 11:40).
Cần: `brief.md`. Người dùng có thể dán thêm ghi chú miệng từ BTC.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md`: §1 sáu khối M1–M6, §2 bảng Mục tiêu↔Luật, §3 template đầu ra, §4 mô hình lái lượt hỏi, §5 ví dụ đã điền.
- `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md` §2 (bảng thực tế phụ thuộc ngoài — dùng để điền cột "trễ / ngữ nghĩa giao hàng" của M4 khi brief im lặng), §6 (mẫu chuyển hit F thành câu hỏi).
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md`: §1 nhận diện biến thể, §5 danh sách 14 actor.

## Quy tắc cứng
1. **Ô không có trong brief thì ghi `?`, không ghi phỏng đoán.** `?` là tài sản: nó thành câu hỏi P0. Phỏng đoán ghi như dữ kiện là nguồn gốc của 26 dòng `[GIẢ ĐỊNH]` không ai kiểm.
2. **Ô suy ra được từ brief nhưng brief không nói thẳng** → ghi giá trị + `[SUY RA]`.
3. Không viết luật, không viết BR, không chọn con số ở bước này. Đây là mô hình, không phải spec.
4. Mâu thuẫn trong chính brief (ví dụ: "áp dụng mọi khách truy cập" + "chống bot gom hàng") → **không tự hoà giải**. Ghi vào mục "Mâu thuẫn nội tại của brief" và đưa lên P0 — đó là dấu hiệu specs thật có luật thứ ba brief không kể.

## Bước — chế độ mặc định (20 phút)
1. Đọc `brief.md`. Xác định biến thể theo knowledge/10 §1; ghi vào đầu file mô hình.
2. **M1 mục tiêu & thước đo** — quét brief tìm mọi cụm chỉ mục đích ("để", "nhằm", "tăng", "chống", "tránh", "công bằng"). Mỗi cụm một dòng: mục tiêu · thước đo · ai chịu thiệt · điều PHẢI KHÔNG xảy ra. Mục tiêu ngầm (không có động từ mục đích nhưng suy ra được) ghi `[SUY RA]`.
3. **M2 dòng tiền** — liệt kê từng chặng tiền di chuyển; điền 5 cột (knowledge/05 §M2). Bắt buộc trả lời 3 câu cuối khối: khoản nào không đảo được · hoàn tiền là quyết định hay hoàn tất · ai trả phí cổng. Ô nào `?` → P0.
4. **M3 dòng tồn & nguồn chân lý** — 6 câu ở knowledge/05 §M3. Cột "nguồn chân lý" và "đồng bộ tức thì/định kỳ" gần như luôn `?` từ brief → luôn thành P0.
5. **M4 biên hệ thống** — điền bảng 6 láng giềng. Cột "trễ" và "ngữ nghĩa giao hàng" điền theo knowledge/32 §2 (sự thật vận hành chung), đánh `[NGÀNH]`; cột "nguồn chân lý của" từ M3.
6. **M5 mô hình lạm dụng** — 6 kẻ cố định. Với mỗi kẻ: được lợi gì trong đúng tính năng này · lách bằng cách nào · luật nào phải chặn · đã có gì trong brief chưa. Kết khối bằng câu: **mọi hạn mức mà brief nhắc tới đang neo vào cái gì, khách có tự đổi được không?**
7. **M6 kịch bản suy biến** — copy 12 ca, đánh dấu ca nào brief đã trả lời (hiếm), còn lại `✗`.
8. **Mâu thuẫn nội tại của brief** — liệt kê từng cặp câu brief chỏi nhau, kèm số câu.
9. **Sinh câu hỏi P0** — theo bảng knowledge/05 §4 + mẫu knowledge/32 §6. Mỗi câu: đóng, ép một kết quả (số / chọn 1 / Đúng-Sai), ≤ 25 từ. Xếp theo: mâu thuẫn brief → M1 điều cấm → M5 neo hạn mức → M3 nguồn chân lý → M2 thất bại tiền → M4 trễ → M6 ca ✗. Ghi rõ câu nào phải vào **lượt 1** (mâu thuẫn brief, M1, M5).
10. Ghi `<thư-mục>/mo-hinh-bai-toan.md` theo template knowledge/05 §3, có timestamp (Bash `date`).
11. In ra cho người dùng: (a) M1 dạng bảng; (b) danh sách mâu thuẫn nội tại của brief; (c) khối câu hỏi P0 đã xếp hạng, đánh dấu câu cho lượt 1; (d) một dòng đếm: `? = n ô / tổng ô` — đây là số lỗ hổng tiềm năng khi bắt đầu ngày.

## Bước — chế độ `muc-tieu-luat` (chạy 11:40, sau khi có spec)
1. Đọc `mo-hinh-bai-toan.md`, `spec.md`, `rtm.md`.
2. Lập bảng knowledge/05 §2: mỗi mục tiêu M1 một dòng. Cột 2 = mã BR phục vụ mục tiêu; cột 3 = mã BR **làm hỏng** mục tiêu.
3. Với từng BR trong spec, gán đúng một trong ba: `phục vụ M-x` / `làm hỏng M-x` / `không thuộc mục tiêu nào`.
4. Kết luận in ra:
   - Mục tiêu có cột 2 trống ⇒ **lỗi Cao**, spec bỏ trắng đúng phần brief nhấn mạnh.
   - Ô cột 3 có chữ ⇒ **lỗi Cao**, kèm câu hỏi verify (knowledge/32 §6) và ghi vào danh sách restate.
   - BR "không thuộc mục tiêu nào" ⇒ ứng viên cắt khi thiếu chỗ trong 3.000 từ, xếp trước cả §7/§10.
5. Ghi phần bảng này append vào `mo-hinh-bai-toan.md` và vào `review.md` nếu file đã tồn tại.

## Output bắt buộc
- [ ] `mo-hinh-bai-toan.md` có đủ M1–M6, không khối nào trống.
- [ ] Mọi ô chưa biết là `?` hoặc `[SUY RA]`/`[NGÀNH]` — không có phỏng đoán ghi trần.
- [ ] Danh sách mâu thuẫn nội tại của brief.
- [ ] Khối câu hỏi P0 xếp hạng, đánh dấu câu bắt buộc vào lượt 1.
- [ ] Chế độ `muc-tieu-luat`: bảng Mục tiêu↔Luật, mỗi BR được gán đúng một nhãn.

## Không được
- Chọn con số, viết luật, hay viết BR trong file mô hình.
- Tự hoà giải mâu thuẫn của brief; tự chọn hộ specs thật khi brief im lặng.
- Điền ô bằng thông lệ ngành mà không đánh `[NGÀNH]` (M4) hoặc `[SUY RA]` (M1–M3).
- Bỏ khối M5 hoặc M6 vì "chưa có dữ kiện" — đó là hai khối sinh nhiều câu hỏi P0 nhất.
- Chạy `/elicit lượt 1` trước khi file mô hình tồn tại.
