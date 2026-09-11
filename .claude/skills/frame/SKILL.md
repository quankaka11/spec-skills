---
name: frame
description: Dựng mô hình bài toán từ brief trong 20 phút trước khi hỏi AI Khách hàng — mục tiêu & thước đo, dòng tiền, dòng tồn & nguồn chân lý, biên hệ thống 6 láng giềng, mô hình lạm dụng, 12 kịch bản suy biến — rồi chia mọi ô chưa biết thành ba nhóm: lên đầu hàng đợi hỏi, xuống dưới đường cắt, hay tự điền mặc định ngành. Dùng 9:00–9:30 ngày thi khi người dùng nói "đọc brief", "hiểu bài toán", "mô hình bài toán", "frame", "dựng khung"; chạy lại lúc 11:30 để lập bảng Mục tiêu ↔ Luật.
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
- `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A (không giới hạn số câu / 4.000 token / một ý mỗi lượt / cấm chỉ thị / nhịp chờ), §A1 (**cách tính trần lượt thật**), **§A2 (ô còn hở phải đọc brief hoặc hỏi BTC trước giờ mở AI)**.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §1 (cổng 8 kiểm tra + dạng câu), §3.1–3.3 (dựng hàng đợi + 28 lượt soạn sẵn), §3.5 (ánh xạ + phần tự điền), §4 (mặc định ngành).
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
8b. **Dựng `mac-dinh-nganh.md` cho miền của đề này** (6 phút, knowledge/20 §4b). Đây là **toán hạng** của quy tắc "không sáng tạo giá trị" (30 §1b); thiếu file này thì quy tắc đó rỗng và người viết sẽ tự nghĩ ra số mà không biết mình đang nghĩ ra. Gọi tên miền từ brief → kê ô theo **chín trục** (ngưỡng · bậc · trần&khoảng · đơn vị&bội số · định dạng · mốc thời gian · thứ tự · kết hợp · **số phần tử của danh sách đóng**) → mỗi ô ghi `Ô | mặc định | nguồn`. Ghi `KHÔNG BIẾT` là kết quả **hợp lệ và quan trọng nhất** — nó đẩy ô đó lên đầu hàng đợi. Cuối cùng chạy **hiệu chỉnh thiên lệch** (20 §4b bước 4): dòng nào đang đoán *rộng và đẹp* (khoảng rộng, độ dài linh hoạt, kiểm nhiều mốc, đơn vị mịn, ngưỡng phẳng, công thức suy ra được) thì sửa về *hẹp và tuỳ tiện*, hoặc hạ xuống `KHÔNG BIẾT`.
   - Miền của đề **không** phải "đặt giữ hàng" ⇒ bảng 31 phát biểu 20 §4 và catalogue 10 §6 là **ví dụ hình dạng**, không phải nguồn tra. Tra miền A trong bảng miền B luôn ra "không thấy".
   - In ra số dòng và tỷ lệ `KHÔNG BIẾT`. Tỷ lệ cao **không** phải thất bại — nó là bản đồ hàng đợi hỏi.
8c. **Quét hằng số** (3 phút, knowledge/20 §4c). Duyệt khung 10 mục, kê **mọi ô sẽ cần một con số trong bản nộp**, kể cả các ô ở **mục 2 và mục 4** và **số phần tử của mọi danh sách đóng** (mấy loại / mấy trạng thái / mấy điều kiện) — trần/độ dài/tập ký tự/bước nhảy/giá trị mặc định của từng ô nhập, số lượng và thứ tự điều kiện kiểm, mã của từng điều kiện, mỗi cột tiền chưa thuế hay đã thuế. Đối chiếu `mac-dinh-nganh.md`; ô nào `KHÔNG BIẾT` thì vào nhóm 1 ở bước 9, **xếp trên mọi câu hỏi quan hệ**.
   Ghi bảng này vào `mo-hinh-bai-toan.md` thành khối `M7 — Hằng số`. Ô nào chưa có con số tới lúc viết spec thì đi theo 30 §1b-2b, **không** tự điền.

9. **Phân loại mọi ô `?` thành ba nhóm.** Số câu hỏi không còn bị chặn (00 §A), nhưng **nhịp chờ chặn**, nên đây là bước xếp hạng chứ không phải bước chọn. Theo bảng knowledge/05 §4:
   - **Nhóm 1 — trên đường cắt**: ô mà đảo lại thì đổi hướng tiền / trạng thái cuối / ai thắng, **và** Executor mù sẽ đoán khác. Xếp hạng giảm dần; ghi rõ mỗi ô hỏi bằng **dạng** nào (nhị phân / một con số / có–không / mở).
   - **Nhóm 2 — dưới đường cắt**: ô đáng hỏi nhưng sẽ không kịp; giữ nguyên thứ tự để gửi thêm nếu chạy nhanh hơn dự kiến.
   - **Nhóm 3 — tự điền mặc định ngành**: mọi ô còn lại **tra được** trong `mac-dinh-nganh.md`, kèm giá trị dự kiến (không tự nghĩ ra — 30 §1b).
   - **Nhóm 4 — không tra được, không hỏi kịp**: ô có `KHÔNG BIẾT` ở `mac-dinh-nganh.md` mà rơi dưới đường cắt. Nhóm này **không** được tự điền: nó đi vào spec dưới dạng **luật an toàn hai chiều** (bậc 8 thang bằng chứng 33 §5b) hoặc im lặng + một dòng bảng 7.4. In riêng thành một danh sách — đây là danh sách nguy hiểm nhất của ngày.
   Ưu tiên trong nhóm 1: mâu thuẫn nội tại của brief → **hằng số `KHÔNG BIẾT` ở M7** (con số đi trước quan hệ — 20 §4c) → **ô không có mặc định ngành** (message nguyên văn, danh sách phạm vi) → M1 điều cấm → M5 neo hạn mức → M3 nguồn chân lý → M2 thất bại tiền → M4 trễ → M6 ca ✗.
   **Đường cắt đặt ở đâu:** `(số phút pha hỏi ÷ nhịp chờ) − 2`, so với `4.000 ÷ token trung bình mỗi lượt`, lấy số nhỏ hơn (00 §A1).
9b. **Đọc brief §3/§4 trước, hỏi BTC phần brief không nói** (không tốn token — 00 §A2): nhịp chờ bao nhiêu giây · 4.000 token của cả đội hay mỗi người · giao diện có hiển thị token không · AI còn mở sau giờ khóa spec không · **lý do VÔ HIỆU của đề này có gồm "sai phạm vi" không**. Bốn ô đầu đổi đường cắt; ô cuối đổi thứ hạng của lượt hỏi về NGOÀI phạm vi.
10. Ghi `<thư-mục>/mo-hinh-bai-toan.md` theo template knowledge/05 §3, có timestamp (Bash `date`).
11. In ra cho người dùng: (a) M1 dạng bảng; (b) danh sách mâu thuẫn nội tại của brief; (c) **ba nhóm ở bước 9**, trong đó nhóm 1 ghi rõ thứ hạng + dạng câu hỏi và nhóm 3 ghi kèm giá trị mặc định dự kiến; (d) danh sách ô cần hỏi BTC ở bước 9b **kèm đường cắt đã tính**; (e) hai dòng đếm: `? = n ô / tổng ô` và `n1 trên đường cắt · n2 dưới đường cắt · n3 tự điền` — tỷ lệ (n2+n3)/n thường 55–70%, và biết trước con số đó là cách duy nhất để không hoảng lúc hết giờ.

## Bước — chế độ `muc-tieu-luat` (chạy 11:40, sau khi có spec)
1. Đọc `mo-hinh-bai-toan.md`, `spec.md`, `rtm.md`.
2. Lập bảng knowledge/05 §2: mỗi mục tiêu M1 một dòng. Cột 2 = mã BR phục vụ mục tiêu; cột 3 = mã BR **làm hỏng** mục tiêu.
3. Với từng BR trong spec, gán đúng một trong ba: `phục vụ M-x` / `làm hỏng M-x` / `không thuộc mục tiêu nào`.
4. Kết luận in ra:
   - Mục tiêu có cột 2 trống ⇒ **lỗi Cao**, spec bỏ trắng đúng phần brief nhấn mạnh.
   - Ô cột 3 có chữ ⇒ **lỗi Cao**, chuyển thành **một lượt nhị phân** (knowledge/32 §6) và chèn lên đầu phần hàng đợi còn lại. Hết giờ hỏi rồi ⇒ chọn phương án phục vụ mục tiêu brief, ghi `[GIẢ ĐỊNH-MT]` (30 §1b-3).
   - BR "không thuộc mục tiêu nào" ⇒ ứng viên cắt đầu tiên nếu bản nộp vượt hạn mức `L` (00 §A3), xếp trước cả mục 9/10.
5. Ghi phần bảng này append vào `mo-hinh-bai-toan.md` và vào `review.md` nếu file đã tồn tại.

## Output bắt buộc
- [ ] `mo-hinh-bai-toan.md` có đủ M1–M6, không khối nào trống.
- [ ] Mọi ô chưa biết là `?` hoặc `[SUY RA]`/`[NGÀNH]` — không có phỏng đoán ghi trần.
- [ ] Danh sách mâu thuẫn nội tại của brief.
- [ ] `mac-dinh-nganh.md` cho **miền của đề này**, kê theo 8 trục, đã chạy hiệu chỉnh thiên lệch, có tỷ lệ `KHÔNG BIẾT`.
- [ ] Khối `M7 — Hằng số`: mọi ô cần một con số trong bản nộp, gồm cả ô ở mục 2 và mục 4.
- [ ] **Bốn** nhóm ô `?`: trên đường cắt (xếp hạng + dạng câu hỏi) · dưới đường cắt · tự điền mặc định ngành (có giá trị) · **không tra được & không hỏi kịp** (danh sách riêng).
- [ ] Đường cắt đã tính bằng số, kèm con số nào đang chặn (nhịp hay token).
- [ ] Danh sách ô cần đọc brief / hỏi BTC ở bước 9b.
- [ ] Chế độ `muc-tieu-luat`: bảng Mục tiêu↔Luật, mỗi BR được gán đúng một nhãn.

## Không được
- Chọn con số, viết luật, hay viết BR trong file mô hình.
- Tự hoà giải mâu thuẫn của brief; tự chọn hộ specs thật khi brief im lặng.
- Điền ô bằng thông lệ ngành mà không đánh `[NGÀNH]` (M4) hoặc `[SUY RA]` (M1–M3).
- Bỏ khối M5 hoặc M6 vì "chưa có dữ kiện" — đó là hai khối sinh nhiều câu hỏi P0 nhất.
- Chạy `/elicit luot 1` trước khi file mô hình tồn tại.
- Để một ô `?` không thuộc nhóm nào ở bước 9, hoặc ghi nhóm 3 mà không kèm giá trị mặc định ngành dự kiến.
- Xếp nhóm 1 mà không đặt đường cắt bằng số — danh sách không có đường cắt là danh sách chưa lập xong.
- Sinh câu hỏi mở dạng "hãy mô tả" hoặc câu có chỉ thị format: câu có vế sai khiến **bị AI Khách hàng từ chối** (00 §A).
