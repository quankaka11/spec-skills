---
name: appeal
description: Dựng hồ sơ kháng nghị cho tối đa 3 ca test CÔNG của đội bị chấm VÔ HIỆU trong Spec Battle — sàng điều kiện kháng nghị, lấy gói bằng chứng phạm vi đã trích sẵn từ hồ sơ finding rồi Grep bù, xếp bằng chứng theo 3 mức (lời AI Khách hàng / câu brief / đồng thuận chéo ≥2 spec), chấm xác suất thắng, xếp hạng theo điểm thu lại được, và viết text kháng nghị ≤150 từ mỗi ca để gửi AI. Dùng 16:00–17:00 ngày thi khi người dùng nói "kháng nghị", "appeal", "bị chấm VÔ HIỆU", "test bị vô hiệu oan".
argument-hint: "[thư-mục-trận] + dán danh sách kết quả VÔ HIỆU"
allowed-tools: Read, Write, Grep, Glob
---
# /appeal — Hồ sơ kháng nghị ≤3 ca VÔ HIỆU

**Điều kiện kháng nghị (knowledge/00 §A, chốt 09/09): chỉ ca test CÔNG của mình bị chấm VÔ HIỆU.** Nộp bằng **text gửi AI**, ban tổ chức review; **≤3 ca/đội**.

Hai loại ca của kit cũ **không còn kháng nghị được** — nếu người dùng dán vào, nói rõ và loại ngay ở bước 1, đừng dựng hồ sơ:
- spec mình bị chấm **TRÚNG** dù spec có quy định (TRÚNG oan **không** kháng nghị được — knowledge/00 §D1);
- **AI So khớp hiểu sai ngữ nghĩa** làm test mình thành TRƯỢT.

Hệ quả: rủi ro của vai THỦ **không có đường lùi**, nên đừng dùng giờ kháng nghị để tranh chất lượng spec mình; rủi ro của vai CÔNG cứu được một phần, và đó là toàn bộ việc của skill này.

**Điều duy nhất được tranh: tình huống nằm TRONG phạm vi specs thật.** Không tranh đáp án chuẩn, không tranh cách Executor trả lời, không tranh chất lượng test. Mọi câu trong text kháng nghị phải phục vụ đúng một kết luận đó — câu nào không phục vụ nó thì cắt.

## Input
`$ARGUMENTS` = `[thư-mục-trận]` (mặc định `battle/`) + phần người dùng dán: từng kết quả **VÔ HIỆU** gồm ID test, tình huống nguyên văn, và lý do đối chiếu của AI So khớp (nếu được xem).

Đọc:
- `tests/*.md` — hồ sơ finding, **gói bằng chứng phạm vi đã thu và trích nguyên văn sẵn lúc 14:30** bởi `/attack` bước 12. Đọc trước khi Grep bất cứ thứ gì.
- `log-khach-hang.md` (5 câu trả lời nguyên văn + timestamp), `brief.md`, `dong-thuan-cheo.md`, `spec.md`, 3 spec đối thủ đã tải về.
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §7 (khối "Kháng nghị — chỉ một loại ca": 3 mức bằng chứng, lập luận mẫu, **mẫu text ≤150 từ**), §2-9 (ba mức bằng chứng phạm vi), §6 luật 8.
- `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A (tham số kháng nghị), §D1 (số học điểm — dùng để xếp hạng theo điểm thu lại được).

## Quy tắc cứng
1. Chỉ dựng hồ sơ cho ca thỏa **cả hai** điều kiện: kết quả máy = **VÔ HIỆU** và test do **đội mình bắn**. Thiếu một điều kiện ⇒ loại, một dòng lý do.
2. Chỉ tranh **phạm vi**. Không tranh đáp án chuẩn, không tranh cách Executor trả lời, không tranh chất lượng test hay chất lượng spec.
3. `"Không có quy định riêng."` trong log **KHÔNG** phải bằng chứng phạm vi (knowledge/50 §6-8) — nó chỉ nói specs thật không quy định, không nói nghiệp vụ thuộc tính năng. Dòng `G-xx` (giả định của đội) cũng **không** phải bằng chứng.
4. **Không trích được câu brief trực tiếp ⇒ không dùng mức 2.** Đừng dựa vào "brief hàm ý".
5. **Bỏ mọi ca xác suất Thấp.** Nộp một ca không bằng chứng làm loãng hai ca còn lại trong mắt người review.
6. Tối đa 3 ca; mỗi ca ≤150 từ; mọi trích dẫn phải được Grep xác nhận tồn tại trước khi ghi.
7. Mỗi ca dùng mức 2 hoặc mức 3 **phải** có dòng `Giới hạn`.

## Bước
1. **Sàng điều kiện** (làm trước mọi việc khác, vì nó loại phần lớn ca của kit cũ). Với từng ca người dùng dán: kết quả máy có đúng là **VÔ HIỆU**? Test đó có phải **đội mình bắn**? Không đủ hai điều kiện ⇒ loại và ghi một dòng lý do, nêu rõ loại ca đó không còn kháng nghị được theo tham số 09/09 để đội không mất thời gian quay lại.
2. **Lấy gói bằng chứng ĐÃ CÓ SẴN** từ hồ sơ finding của test đó trong `tests/*.md` (`/attack` đã trích nguyên văn lúc 14:30). Có sẵn ⇒ nhảy sang bước 4, không Grep lại. Không có hoặc thiếu mức mạnh hơn ⇒ bước 3.
3. **Grep bù bằng chứng**, không nhớ mò, theo thứ tự độ mạnh giảm dần:
   - **Mức 1 — lời AI Khách hàng.** Grep **TOÀN BỘ** `log-khach-hang.md`, **mọi câu C1–C5**, không dừng ở câu đầu tìm được: gom mọi câu trả lời củng cố rồi xếp câu trực tiếp nhất lên đầu. Trích khối `## C<n>`, timestamp, câu hỏi và câu trả lời nguyên văn. Đối chiếu danh sách NGOÀI phạm vi trong câu trả lời C1: nghiệp vụ nằm trong danh sách NGOÀI ⇒ **bỏ ca**, máy chấm đúng.
   - **Mức 2 — câu brief tường minh.** Grep `brief.md` tìm câu nhắc nghiệp vụ đó → trích nguyên văn kèm vị trí (đoạn / câu). Không trích được câu trực tiếp ⇒ **không dùng mức 2** (quy tắc cứng 4).
   - **Mức 3 — đồng thuận chéo.** Grep 3 spec đối thủ đã tải + `dong-thuan-cheo.md`: ≥2/3 spec có luật cho nghiệp vụ đó → trích **số mục của cả hai**.
   - Khi viện "theo thể lệ", dẫn **dòng cụ thể** trong `knowledge/00-luat-choi.md` §A (tham số kháng nghị) hoặc §D1 (số học điểm) — không viện chung chung.
   - Trích nguyên văn giữ đúng ký tự (kể cả backtick, mã); cắt đoạn thì đánh dấu `[…]`.
4. **Chấm xác suất thắng**: **Cao** = mức 1 khớp trực tiếp nghiệp vụ của tình huống · **TB** = mức 2, hoặc mức 1 nói về nghiệp vụ lân cận · **Thấp–TB** = mức 3 · **Thấp** = không có mức nào. Bỏ mọi ca Thấp, ghi lý do 1 dòng (quy tắc cứng 5).
5. **Xếp hạng & chọn ≤3**: theo xác suất, rồi theo mức bằng chứng 1 > 2 > 3, rồi theo **điểm thu lại được** (knowledge/00 §D1): một ca thắng đổi **−1 thành 0 hoặc +2**, tức **lãi 1–3 điểm**. **Ưu tiên ca mà dry-run trong hồ sơ finding dự đoán TRÚNG** — nếu chấm lại phạm vi rồi chấm tiếp thành TRÚNG thì lãi 3.
6. **Viết text cho mỗi ca chọn** theo mẫu knowledge/50 §7, **≤150 từ**, đúng 6 dòng:
   ```
   Test: <ID> — <tình huống nguyên văn>
   Kết quả máy: VÔ HIỆU
   Đề nghị: chấm lại phạm vi.
   Căn cứ: <trích nguyên văn 1 nguồn mạnh nhất + vị trí: mã câu C<n> + timestamp, hoặc câu brief, hoặc §mục của 2 spec>
   Lập luận: <2 câu, chỉ về phạm vi>
   Giới hạn: <1 câu tự nêu điểm yếu của bằng chứng, khi dùng mức 2 hoặc 3>
   ```
   Lập luận theo ưu tiên bằng chứng: mức 1 — "câu trả lời đó là quy định nghiệp vụ cho đúng tình huống này, nên tình huống nằm trong phạm vi specs thật"; mức 2 — "nghiệp vụ này không thể nằm ngoài phạm vi tính năng khi chính đề bài mô tả nó"; mức 3 — "hai đội độc lập cùng xếp nghiệp vụ này trong phạm vi tính năng".
   Dòng **`Giới hạn`** không phải khiêm tốn: mức 2 và mức 3 chỉ chứng minh nghiệp vụ **thuộc tính năng**, không chứng minh specs thật **có luật** cho nó — đó là **lý bác duy nhất người review có** ("brief nhắc không có nghĩa specs thật có luật"). Tự nêu nó trước làm phần còn lại đáng tin hơn, và giữ đề nghị ở đúng mức: chỉ xin chấm lại phạm vi, không xin đổi thành TRÚNG.

## Output bắt buộc
`appeal.md` trong thư mục trận, gồm:
1. **Bảng sàng**: ID | kết quả máy | test của đội mình? | đủ điều kiện kháng nghị? | mức bằng chứng | xác suất | điểm thu lại được | chọn/bỏ | lý do 1 dòng. Ca bị loại ở bước 1 ghi rõ "loại ca này không còn kháng nghị được (tham số 09/09)".
2. **Text kháng nghị của ≤3 ca, mỗi ca trong MỘT code block copy nguyên khối** theo mẫu 6 dòng ở bước 6 — đây là thứ gửi đi, không phải bản tóm tắt.
3. Hai câu hỏi người review có thể hỏi mỗi ca + câu trả lời 1 câu cho mỗi câu hỏi.
4. Dòng cuối: tổng điểm có thể thu lại (số ca × 1 đến 3), và danh sách ca đã bỏ kèm lý do — để rút bài học cho lần sau.

## Không được
- Dựng hồ sơ cho ca không phải VÔ HIỆU, hoặc cho test do đội khác bắn vào spec mình.
- Tranh đáp án chuẩn, tranh cách Executor trả lời, tranh chất lượng test hay chất lượng spec của mình — ngoài phạm vi cơ chế kháng nghị.
- Đi Grep trước khi đọc gói bằng chứng đã có sẵn trong hồ sơ finding — mất phần lớn của một giờ.
- Dừng ở câu trả lời đầu tiên tìm được trong log; phải quét cả 5 câu C1–C5 rồi xếp câu trực tiếp nhất lên đầu.
- Dùng `"Không có quy định riêng."` hoặc dòng `G-xx` làm bằng chứng phạm vi.
- Dùng mức 2 khi không trích được câu brief trực tiếp ("brief hàm ý" không phải bằng chứng).
- Viện "theo thể lệ" mà không dẫn dòng cụ thể trong `knowledge/00-luat-choi.md` §A/§D1.
- Lập luận không có trích dẫn nguyên văn; ghi trích dẫn chưa Grep xác nhận tồn tại trong log / brief / spec đối thủ.
- Chọn quá 3 ca; chọn ca xác suất Thấp; viết quá 150 từ một ca; bỏ dòng `Giới hạn` khi dùng mức 2 hoặc 3.
- Sửa spec, log, hay test sau khi khóa.
