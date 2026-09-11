---
name: frame
description: Dựng mô hình bài toán từ brief trong 20 phút trước khi hỏi AI Khách hàng — mục tiêu & thước đo, dòng tiền, dòng tồn & nguồn chân lý, biên hệ thống 6 láng giềng, mô hình lạm dụng, 12 kịch bản suy biến — rồi chia mọi ô chưa biết thành ba nhóm: vào 5 câu hỏi, vào hàng đợi câu restate, hay tự điền mặc định ngành. Dùng 9:00–9:30 ngày thi khi người dùng nói "đọc brief", "hiểu bài toán", "mô hình bài toán", "frame", "dựng khung"; chạy lại lúc 11:30 để lập bảng Mục tiêu ↔ Luật.
argument-hint: "[thư-mục] | muc-tieu-luat [thư-mục]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(date *), Bash(mkdir *)
---
# /frame — Mô hình bài toán trước khi hỏi

Mục tiêu: biến brief một trang thành 6 khối mô hình có thể đối chiếu, để mọi luật viết sau đều trả lời được "phục vụ mục tiêu nào" và "có khả thi không". Không có bước này, spec đạt mọi cổng hình thức mà vẫn sai bài toán (bằng chứng: `knowledge/05-hieu-bai-toan.md` §0).

## Input
`$ARGUMENTS` = `[thư-mục]` (mặc định `battle/`) hoặc `muc-tieu-luat [thư-mục]` (chế độ thứ hai, chạy 11:30).
Cần: `brief.md`. Người dùng có thể dán thêm ghi chú miệng từ BTC.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md`: §1 sáu khối M1–M6, §2 bảng Mục tiêu↔Luật, §3 template đầu ra, **§4 ô mô hình nào đi vào câu nào và ô nào phải tự điền**, §5 ví dụ đã điền.
- `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A (5 câu hỏi / 5.000 token), **§A2 (hai ô còn hở phải hỏi BTC trước 9:30)**.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §3 (5 câu soạn sẵn + bảng ánh xạ ID → câu), §4 (mặc định ngành).
- `${CLAUDE_PROJECT_DIR}/knowledge/33-kha-thi-van-hanh.md` §2 (bảng thực tế phụ thuộc ngoài — dùng để điền cột "trễ / ngữ nghĩa giao hàng" của M4 khi brief im lặng), §6 (mẫu chuyển hit F thành câu hỏi).
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md`: §1 nhận diện biến thể, §5 danh sách 14 actor.
- `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` §1 (10 mục spec phải nộp) — đọc để biết mô hình này sẽ phải nuôi những mục nào; đặc biệt bốn vùng BTC 11/09 yêu cầu tường minh: **mục 2 item màn hình · mục 3 event · mục 4 message lỗi nguyên văn · mục 9 API**.

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
5. **M4 biên hệ thống** — điền bảng 6 láng giềng. Cột "trễ" và "ngữ nghĩa giao hàng" điền theo knowledge/33 §2 (sự thật vận hành chung), đánh `[NGÀNH]`; cột "nguồn chân lý của" từ M3.
6. **M5 mô hình lạm dụng** — 6 kẻ cố định. Với mỗi kẻ: được lợi gì trong đúng tính năng này · lách bằng cách nào · luật nào phải chặn · đã có gì trong brief chưa. Kết khối bằng câu: **mọi hạn mức mà brief nhắc tới đang neo vào cái gì, khách có tự đổi được không?**
7. **M6 kịch bản suy biến** — copy 12 ca, đánh dấu ca nào brief đã trả lời (hiếm), còn lại `✗`.
8. **Mâu thuẫn nội tại của brief** — liệt kê từng cặp câu brief chỏi nhau, kèm số câu.
8b. **Đối chiếu mô hình với 10 mục spec phải nộp** (knowledge/32 §1) — mỗi mục ghi: mô hình đã có dữ kiện gì, còn thiếu gì. Bốn mục sau gần như luôn trống sau khi đọc brief, và đó là thông tin phải biết trước khi chọn 5 câu: **mục 2** (item màn hình, điều kiện ẩn/disable, khác biệt login vs guest) · **mục 3** (event mở màn hình, bấm nút, bấm hai lần) · **mục 4** (**message lỗi nguyên văn** — không suy được từ mặc định ngành) · **mục 9** (nguồn tồn kho, retry, timeout). Ô nào ở đây vào được C2/C4 thì ghi rõ; phần còn lại xếp nhóm 3.
9. **Phân loại mọi ô `?` thành ba nhóm** — đây là bước thay cho "sinh câu hỏi P0" của bản cũ, vì chỉ có 5 câu hỏi cho cả ngày (00 §A). Theo bảng knowledge/05 §4:
   - **Nhóm 1 — vào C1–C4** (4 câu gửi trước 10:20): ô nào chen được vào phần liệt kê của một câu soạn sẵn ở 20 §3. Ghi rõ ô nào vào câu nào. Nhớ rằng **C2 đã mang hai cột hiển thị/nút** và **C4 đã mang ý message lỗi nguyên văn + guest** — nên ô thuộc mục 2/3/4 chen vào đó, không cần câu riêng.
   - **Nhóm 2 — hàng đợi C5**: ô mà đảo lại thì đổi hướng tiền / trạng thái cuối / ai thắng, nhưng không chen được vào C1–C4. Tối đa 10 ô sẽ lọt; xếp hạng ngay.
   - **Nhóm 3 — tự điền mặc định ngành**: mọi ô còn lại, kèm **giá trị dự kiến** lấy từ 20 §4 hoặc 10 §6 (không tự nghĩ ra — 30 §1b).
   Ưu tiên khi tranh chỗ trong nhóm 1: mâu thuẫn nội tại của brief → M1 điều cấm → M5 neo hạn mức → M3 nguồn chân lý → M2 thất bại tiền → M4 trễ → M6 ca ✗.
9b. **Hai câu hỏi cho BTC** (không tốn token, hỏi miệng trước 9:30 — 00 §A2): ảnh có tính vào 5.000 token không; AI Khách hàng còn mở sau 12:00 không. Câu trả lời đổi kế hoạch: ảnh không tính token ⇒ C5 chuyển sang phương án ảnh; AI còn mở buổi chiều ⇒ giữ 1 câu để xin danh sách NGOÀI phạm vi mở rộng lúc 13:30.
10. Ghi `<thư-mục>/mo-hinh-bai-toan.md` theo template knowledge/05 §3, có timestamp (Bash `date`).
11. In ra cho người dùng: (a) M1 dạng bảng; (b) danh sách mâu thuẫn nội tại của brief; (c) **ba nhóm ở bước 9**, trong đó nhóm 1 ghi rõ "ô này vào câu C mấy" và nhóm 3 ghi kèm giá trị mặc định dự kiến; (d) hai câu hỏi cho BTC ở bước 9b; (e) hai dòng đếm: `? = n ô / tổng ô` và `n1 vào 5 câu · n2 hàng đợi C5 · n3 tự điền` — tỷ lệ n3/n thường 60–75%, và biết trước con số đó là cách duy nhất để 10:20 không hoảng.

## Bước — chế độ `muc-tieu-luat` (chạy 11:40, sau khi có spec)
1. Đọc `mo-hinh-bai-toan.md`, `spec.md`, `rtm.md`.
2. Lập bảng knowledge/05 §2: mỗi mục tiêu M1 một dòng. Cột 2 = mã BR phục vụ mục tiêu; cột 3 = mã BR **làm hỏng** mục tiêu.
3. Với từng BR trong spec, gán đúng một trong ba: `phục vụ M-x` / `làm hỏng M-x` / `không thuộc mục tiêu nào`.
4. Kết luận in ra:
   - Mục tiêu có cột 2 trống ⇒ **lỗi Cao**, spec bỏ trắng đúng phần brief nhấn mạnh.
   - Ô cột 3 có chữ ⇒ **lỗi Cao**, chuyển thành **phát biểu Đúng/Sai** cho câu C5 (knowledge/33 §6) và ghi vào hàng đợi restate. C5 đã gửi rồi ⇒ chọn phương án phục vụ mục tiêu brief, ghi `[GIẢ ĐỊNH-MT]` (30 §1b-3).
   - BR "không thuộc mục tiêu nào" ⇒ ứng viên cắt khi thiếu chỗ trong 6.000 token, xếp trước cả §7/§10.
5. Ghi phần bảng này append vào `mo-hinh-bai-toan.md` và vào `review.md` nếu file đã tồn tại.

## Output bắt buộc
- [ ] `mo-hinh-bai-toan.md` có đủ M1–M6, không khối nào trống.
- [ ] Mọi ô chưa biết là `?` hoặc `[SUY RA]`/`[NGÀNH]` — không có phỏng đoán ghi trần.
- [ ] Danh sách mâu thuẫn nội tại của brief.
- [ ] Ba nhóm ô `?`: vào C1–C4 (ghi rõ câu nào) · hàng đợi C5 (xếp hạng) · tự điền mặc định ngành (có giá trị dự kiến).
- [ ] Hai câu hỏi cho BTC ở bước 9b.
- [ ] Bảng đối chiếu mô hình ↔ 10 mục spec (bước 8b), nêu rõ bốn vùng BTC còn thiếu gì.
- [ ] Chế độ `muc-tieu-luat`: bảng Mục tiêu↔Luật, mỗi BR được gán đúng một nhãn.

## Không được
- Chọn con số, viết luật, hay viết BR trong file mô hình.
- Tự hoà giải mâu thuẫn của brief; tự chọn hộ specs thật khi brief im lặng.
- Điền ô bằng thông lệ ngành mà không đánh `[NGÀNH]` (M4) hoặc `[SUY RA]` (M1–M3).
- Bỏ khối M5 hoặc M6 vì "chưa có dữ kiện" — đó là hai khối sinh nhiều câu hỏi P0 nhất.
- Chạy `/elicit cau 1` trước khi file mô hình tồn tại.
- Để một ô `?` không thuộc nhóm nào ở bước 9, hoặc ghi nhóm 3 mà không kèm giá trị mặc định ngành dự kiến.
- Sinh nhiều hơn 5 câu hỏi, hoặc sinh câu hỏi mở dạng "hãy mô tả" — hạn mức là 5 câu cho cả ngày.
