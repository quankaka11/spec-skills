---
name: drill
description: Diễn tập trọn vòng Spec Battle với AI Khách hàng giả lập theo luật 11/09 (không giới hạn số câu, một ý mỗi lượt, cấm câu chứa chỉ thị, có nhịp chờ, không memory, 4.000 token; spec ≤ hạn mức L đọc từ brief; điểm +2/+1/−1) — sinh specs thật ẩn có luật phản trực giác, brief mơ hồ, chạy /elicit ↔ agent customer, /spec-write, /spec-review, /attack chính spec vừa viết, chấm bằng executor + customer, tổng kết điểm và 10 chỉ số. Chỉ chạy khi người dùng gõ /drill (10–11/09 trước ngày thi).
argument-hint: "<tên> [tính-năng] [tự-động|thủ-công]"
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(LC_ALL=C.UTF-8 wc *), Bash(mkdir *), Bash(date *), Agent
---
# /drill — Diễn tập trọn vòng

Mục tiêu: đo xem question bank + template + quy trình có thật sự chắn được test không, trước khi thi thật; sinh bài học cụ thể để sửa `knowledge/20` và `knowledge/30`.

## Input
`$ARGUMENTS` = `<tên> [tính-năng] [chế-độ]`. Mặc định: tính năng = "đặt giữ hàng e-commerce" (kiểm domain), chế độ = tự-động. Thư mục làm việc `drill/<tên>/` (layout như `battle/`, thêm `true-spec.md`).

**Luật hỏi đã đổi lần thứ hai (thi thử 11/09): không giới hạn số câu, nhưng mỗi lượt đúng MỘT Ý, câu chứa chỉ thị bị từ chối, có nhịp chờ giữa hai lượt, AI không có memory (00 §A).** Diễn tập phải chạy đúng chế độ đó. Chạy kiểu batch (bản 08/09) hay kiểu "một câu gộp một bảng" (bản 09/09) đều cho kết quả tốt giả, vì **cả hai kiểu đó bị từ chối ở hệ thật**.
Đọc: `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A (tham số, cập nhật 11/09), §A1 (ước token + cách tính trần lượt), `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §1 (cổng 8 kiểm tra) và §3 (hàng đợi), §C, §D1 (công thức điểm); `${CLAUDE_PROJECT_DIR}/knowledge/33-cau-truc-spec-btc.md` §1–§2 (10 mục BTC + 5 gạch bảng Case — true-spec phải có dữ kiện cho cả 10 mục), §7 (Mermaid); `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §6 (catalogue ⚠ để cài luật phản trực giác); `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (24 loại để chấm đa dạng test), §6 luật 8 (điểm kỳ vọng); `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §M5, §M6 (để cài luật vào true-spec ở đúng những chỗ đội hay bỏ); `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md` §2 (để true-spec nói về phụ thuộc ngoài đúng như hệ thật).

## Hạn mức phải tôn trọng khi diễn tập (nếu phá, kết quả vô nghĩa)
- **Một lần gọi agent `customer` = một lượt = một ẩn số.** Prompt chỉ chứa một câu nghi vấn, **không một chữ mệnh lệnh nào** về cách trả lời.
- Agent sẽ **từ chối** câu chứa chỉ thị hoặc câu gộp nhiều ý. Lượt bị từ chối: **không cộng token**, nhưng **cộng 1 nhịp**, và phải chạy `/elicit tu-choi` để sửa — đúng như ở hệ thật.
- **Nhịp chờ:** chọn một con số trước khi bắt đầu (mặc định 45 giây), ghi vào `README.md` của drill. Không cần chờ thật, đếm nhịp bằng sổ. **Trần lượt = (số phút pha hỏi ÷ nhịp) − 2**, ghi rõ trước khi gửi lượt đầu tiên.
- Mỗi lần gọi là một **phiên độc lập**: không đưa câu hỏi/câu trả lời trước vào prompt (giả lập "không có memory").
- Cộng dồn token ước (00 §A1); vượt ngân sách hỏi `Q` của đề thì dừng hỏi, dù hàng đợi còn. **`Q` và `L` chọn trước mỗi lần drill và ghi vào `README.md` của drill** — drill với `L` khác nhau (chật / rộng) là hai bài tập khác nhau, phải chạy cả hai.
- **Viết spec song song**: bắt đầu viết từ sau lượt thứ 3, không đợi hỏi xong — đây là một trong những thứ drill phải đo.
- Spec: đích **`0,90 × L`**, tuyệt đối ≤ `L`; ngắn hơn `0,75 × L` thì phán quyết theo chế độ CHẬT/RỘNG (00 §A3), **không mặc định là lỗi**. Markdown, không ảnh, đủ 10 mục BTC; ba sơ đồ mermaid BTC yêu cầu nếu thang bằng chứng đã đổ tới bậc 6 (33 §5b).

## Ranh giới thông tin (quy tắc cứng)
- Agent `executor` KHÔNG BAO GIỜ nhận đường dẫn `true-spec.md`, RTM, log, brief — chỉ `spec.md` + tình huống.
- Agent `customer` KHÔNG nhận `spec.md`, `rtm.md`, `tests/` — chỉ `true-spec.md` + câu hỏi (HỎI) hoặc tình huống + trả lời executor (CHẤM).
- Khi tự đóng vai đội (chế độ tự-động), không mở `true-spec.md`; chỉ dùng câu trả lời của customer. Ghi rõ điều này ở đầu `ket-qua.md`.

## Bước
1. `mkdir -p drill/<tên>/tests`. Ghi `drill/<tên>/README.md`: tham số drill, timestamp (Bash date).
2. **Sinh `true-spec.md`** (specs thật của BTC giả lập): ≥25 luật có mã `TS-xx`; TRONG/NGOÀI phạm vi tường minh (NGOÀI ≥5 mục); actor & quyền; bảng trạng thái đầy đủ; con số có đơn vị; ≥8 luật ⚠ cố ý khác mặc định phổ biến (lấy ý từ knowledge/10 §6 hoặc tương đương cho tính năng khác).
   **Bắt buộc thêm, để drill đo được đúng lỗ hổng mà kit vừa vá:** (a) ≥1 luật cho **mỗi** kịch bản trong 12 ca knowledge/05 §M6 — nhất là hoàn tiền thất bại, callback trùng/sai thứ tự, job ngừng chạy, ghi giữ thất bại; (b) một **đơn vị neo hạn mức tường minh** (tài khoản / SĐT đã OTP / thẻ) và một luật chặn kẻ đổi danh tính; (c) hai mốc riêng cho mọi nghĩa vụ hoàn tiền (khởi tạo + hoàn tất, số thực tế theo knowledge/32 §2 — ngày làm việc, không phải giờ); (d) một luật nói **ai chịu phí** khi hold bị bỏ. Nếu true-spec không có bốn nhóm này thì drill sẽ báo "đội chắn tốt" một cách giả, vì không có gì để bắn ở đúng những chỗ đội hay hở.
   - **Phải có dữ kiện cho cả 10 mục BTC** (knowledge/33 §1), gồm bốn vùng mới: **item màn hình** (control, mặc định, ẩn/disable, khác biệt login vs guest), **event** (mở màn hình lấy gì, nút gọi gì, double-click), **validation & message lỗi nguyên văn** (≥5 message đầy đủ chữ), **API/tích hợp** (nguồn tồn kho, retry khi thất bại). Nếu thiếu, đội diễn tập không có gì để hỏi ở lượt 7 và bài học sẽ sai.
   - Trong ≥8 luật ⚠, đặt **ít nhất 2 luật ⚠ thuộc vùng message/guest/trạng thái nút** (ví dụ: message vượt tồn ghi số còn lại; guest được tạo hold nhưng không xem lại được) — đây là loại lỗ hổng #22/#17/#18.
   Ghi `true-spec.answers.md`: bảng `W-xx | TS-xx | phát biểu | mặc định phổ biến nó đi ngược | loại # (knowledge/50 §1)`.
3. **Sinh `brief.md`** 150–250 từ, mơ hồ như BTC, không lộ bất kỳ ⚠ nào, không nêu con số. **Bắt buộc có ≥2 mục tiêu nghiệp vụ tường minh** (kiểu "để tăng X", "chống Y") và **ít nhất một cặp câu chỏi nhau** — đó là chất liệu cho `/frame` M1 và bảng Mục tiêu↔Luật, và là thứ brief thật của BTC luôn có.
3b. **Dựng mô hình bài toán**: chạy quy trình `/frame drill/<tên>` (đọc `${CLAUDE_PROJECT_DIR}/.claude/skills/frame/SKILL.md`) → `mo-hinh-bai-toan.md` + danh sách câu hỏi P0. Khi đóng vai đội, chỉ đọc `brief.md`, không mở `true-spec.md`.
4. **Kế hoạch hỏi**: chạy quy trình `/elicit ke-hoach drill/<tên>` → hàng đợi xếp hạng, đường cắt bằng số, nguyên văn từng lượt, danh sách ô sẽ tự điền.
4b. **Chạy hàng đợi tới đường cắt**, từng lượt một:
   - Chạy `/elicit luot k drill/<tên>` (đọc và làm theo `${CLAUDE_PROJECT_DIR}/.claude/skills/elicit/SKILL.md`) → một dòng câu hỏi.
   - Tự-động: gọi Agent `subagent_type: customer`, prompt = `CHẾ ĐỘ: HỎI` / `Đường dẫn true-spec: <tuyệt đối>` / dòng trống / **đúng một câu nghi vấn, không chỉ thị**. Thủ-công: in ra, chờ người dùng dán.
   - Nhận `⚠ Bị từ chối` → chạy `/elicit tu-choi k drill/<tên>` và gửi lại bản đã sửa. **Đếm số lần bị từ chối** — đó là một chỉ số của drill.
   - Chạy `/elicit nap drill/<tên>` với câu trả lời nhận được; cộng token và nhịp.
   - Các lượt gọi được **song song** (customer không có memory) — nhưng vẫn nạp lần lượt để RTM không bị trộn, và vẫn đếm nhịp như thể gửi tuần tự.
5. **Viết**: chạy quy trình `/spec-write drill/<tên>` → bản nháp + **bảng xếp hạng rủi ro giả định**.
5b. **Lượt xác nhận**: chạy `/elicit xac-nhan drill/<tên>` → danh sách lượt rời, **mỗi phát biểu một lượt**, ưu tiên dạng nhị phân. Gửi lần lượt cho `customer` trong số nhịp còn lại, nạp, rồi vá spec: mọi câu trả lời khác giả định phải sửa BR tương ứng. Ghi số ô được sửa — **đó là số lỗ hổng mà pha xác nhận cứu được**.
6. **Review**: chạy `/spec-review drill/<tên>/spec.md 12 sửa` (executor gọi mù theo quy trình đó). Ghi số lỗi Cao trước/sau, và số hit khối G-7 (giá trị tự nghĩ ra).
7. **Đổi vai — tự bắn**: chạy `/attack drill/<tên>/spec.md doi-minh drill/<tên>` → tối đa 5 test có `EV > 0` (executor mù theo quy trình đó).
8. **Chấm** mỗi test: (a) Agent `executor` với `Đường dẫn spec: drill/<tên>/spec.md` + tình huống; (b) Agent `customer` với `CHẾ ĐỘ: CHẤM` / `Đường dẫn true-spec: ...` / `Tình huống: ...` / `Trả lời Executor: <output (a)>` → KẾT QUẢ. Gọi (a) song song 5 test, rồi (b) song song.
9. **Tổng kết `ket-qua.md`**:
   - Bảng: test | loại # | ĐỘ PHỦ executor | KẾT QUẢ | lý do đối chiếu (rút gọn).
   - Tỷ lệ TRÚNG / TRƯỢT / VÔ HIỆU trên số test đã nộp, **và điểm quy đổi theo công thức 09/09**: `TRÚNG×2 − VÔ HIỆU×1` cho vai CÔNG; số test TRƯỢT × 1 cho vai THỦ nếu tính ngược lại. So với điểm nếu bỏ các test `EV ≤ 0` — để kiểm quy tắc "bỏ slot còn hơn bắn bừa".
   - **Lỗ hổng elicitation**: số dòng `W-xx` trong `true-spec.answers.md` KHÔNG có dòng RTM tương ứng (đội không hỏi tới) — liệt kê, kèm câu hỏi nên thêm vào knowledge/20 §2 (ID nhóm N).
   - **Lỗ hổng viết**: số `W-xx` có trong RTM (⚠) nhưng không có BR trong spec hoặc BR viết sai — liệt kê, kèm dòng template knowledge/30 hoặc knowledge/32 cần sửa.
   - **Phủ 10 mục BTC**: bảng mục 1–10 | spec có / thiếu / rỗng | dữ kiện có trong RTM không. Mục thiếu mà RTM CÓ dữ kiện = lỗ hổng viết; mục thiếu mà RTM KHÔNG có = lỗ hổng elicitation.
   - **Lỗ hổng tấn công**: test TRƯỢT vì Executor đoán trùng specs thật → ghi để sửa knowledge/50 §2-11 nếu lặp lại.
   - **Lỗ hổng khả thi**: với mỗi `W-xx` thuộc loại #16–#20, đối chiếu spec đội — spec có luật đúng, có luật sai, hay im lặng. Đây là chỉ số đo trực tiếp cổng F có hoạt động không.
   - **Lỗ hổng mục tiêu**: cặp câu chỏi nhau ở brief (bước 3) — đội có phát hiện và hỏi lại không, hay tự hoà giải rồi viết luật một chiều? Đo bằng: `/frame` bước 9 có xếp cặp đó lên trên đường cắt không, và hàng đợi có lượt nào chạm tới không.
   - **Chỉ số đa nghĩa thật**: số tình huống eval mà hai reader ở `/spec-review` khối D ra kết quả khác nhau, so với số ca reader tự khai `ĐA NGHĨA = KHÔNG`. Hai số này lệch nhau nhiều = bằng chứng vòng tự khai không dùng được.
   - **Lỗ hổng giả định**: với mỗi dòng `G-xx` trong RTM, đối chiếu `true-spec.answers.md` — giả định trùng specs thật / trùng mặc định ngành nhưng lệch specs thật / **ngược mặc định ngành và lệch specs thật** (loại tệ nhất, phải = 0). Đây là chỉ số đo trực tiếp quy tắc 30 §1b.
   - **Hiệu quả pha xác nhận**: số câu trả lời khác giả định · số BR đã sửa nhờ đó · trong số đó bao nhiêu BR về sau bị test bắn vào. So với chi phí nhịp: một lượt xác nhận cứu được ít hơn một lượt nhị phân mới trong hàng đợi thì lần sau hỏi thẳng, đừng để dành.
   - **Chi phí bị từ chối**: số lượt bị từ chối / tổng lượt, tách theo hai nhãn (chứa chỉ thị / nhiều ý). Mỗi lần từ chối = một nhịp mất trắng. >10% nghĩa là cổng 8 kiểm tra ở `/elicit` chưa chặn được thứ đáng lẽ phải chặn — ghi thành bài học sửa `knowledge/20` §1.
   - **Đúng nhịp hay không**: số lượt thật sự gửi / trần lượt đã tính ở bước 4. Dưới 70% = đội ngồi chờ câu trả lời, hoặc viết spec nối tiếp thay vì song song.
   - **Độ chính xác ước token**: token thật (nếu đo được) / ước theo `max(từ×2,5; ký tự/2,2)` — cho cả lượt hỏi và cho spec; lệch >20% thì chỉnh hệ số ở 00 §A1.
   - Token spec (cả hai công thức) + `L` + đệm + **chế độ CHẬT/RỘNG kèm `T_A`** + **bậc dừng trên thang bằng chứng**, token thực tế từng mục so với **% của `L`** (33 §5), thời gian từng pha (nếu đo được).
   - **Dùng hết ngân sách chưa** *(chỉ chấm ở chế độ RỘNG)*: số nhánh từ chối chưa có message ở mục 4 · số logic mục 6 dưới ngưỡng case · số ca ở 7.2 · số sơ đồ mermaid. Ở chế độ CHẬT các con số này thấp là **đúng**, không phải thiếu.
   - **Hằng số không nguồn** *(chỉ số mới, quan trọng nhất)*: chạy quét hằng số 20 §4c trên bản nộp; đếm `số hằng số không trỏ về A-xx/G-xx nào`. Mục tiêu **= 0**. Tách riêng số hằng số nằm ở **mục 2 và mục 4** — đây là vùng mù cũ của cổng G-7.
   - **Vượt đường đỏ**: số luật ở bậc 9 (con số tự chọn) trong khi bậc 8 chưa thử hoặc còn nhịp hỏi. Mục tiêu **= 0**.
   - **Bốn kiểu G-9**: số hit ở G-9a (ngoài phạm vi → cấm) · G-9b (luật rộng hơn danh từ đã hỏi) · G-9c (con số trên câu deflection) · G-9d (mã lỗi suy rộng). Mục tiêu **= 0** cả bốn.
   - **Danh từ lạ bị bỏ qua**: đếm danh từ mới xuất hiện trong câu trả lời của customer; bao nhiêu cái có lượt truy theo. Tỷ lệ truy theo dưới 50% ⇒ hàng đợi đang bỏ phí nguồn dữ kiện rẻ nhất.
   - **Hằng số trước quan hệ**: trong các lượt đã gửi, đếm `số câu hằng số` / `số câu quan hệ`, và liệt kê hằng số nào bị viết vào spec mà **chưa từng có một lượt hỏi**. Đây là chỉ số đo trực tiếp bài học lớn nhất của thi thử 11/09.
   - **Phủ 14 ô "đề luôn chốt"** *(chỉ số mới)*: chạy bảng 20 §4d hai lần — (a) **mặt hỏi**: mấy ô có ít nhất một lượt đã gửi; (b) **mặt viết**: mấy ô có ít nhất một luật trong bản nộp (khối G-10 của `/spec-review`). Mục tiêu (b) **= 14/14**; (a) đo đường cắt chọn đúng chưa. Với mỗi ô hở, đối chiếu `true-spec.answers.md` xem specs thật có luật **ngược mặc định ngành** ở đó không — ô hở + luật ngược = một viên đạn đối thủ chắc chắn có. Thi thử 11/09 đo được 5/14 ở mặt hỏi và 5 ô hở có luật phản trực giác.
   - **`HỎI` có được thực thi không**: số mục trong danh sách `HỎI` của `/spec-review` · bao nhiêu mục thật sự được gửi trước khi nộp · **ngân sách hỏi còn thừa lúc nộp**. Nộp khi còn >30% ngân sách và danh sách `HỎI` chưa rỗng là **thất bại quy trình**, ghi riêng thành một dòng đỏ ở đầu `ket-qua.md`.
   - 3 bài học hành động được: mỗi bài = file knowledge + mục + câu cần thêm/sửa. Không tự sửa knowledge — đề xuất để người dùng duyệt.

## Output bắt buộc
- [ ] `drill/<tên>/`: true-spec.md, true-spec.answers.md, brief.md, mo-hinh-bai-toan.md, log-khach-hang.md, rtm.md, spec.md, review.md, eval-set.md, tests/doi-minh.md, ket-qua.md.
- [ ] `ket-qua.md` có 17 chỉ số (điểm quy đổi, lỗ hổng elicitation, lỗ hổng viết, lỗ hổng khả thi, lỗ hổng mục tiêu, đa nghĩa thật, lỗ hổng giả định, hiệu quả pha xác nhận, chi phí bị từ chối, đúng nhịp hay không, **hằng số không nguồn**, **vượt đường đỏ**, **bốn kiểu G-9**, **danh từ lạ bị bỏ qua**, **hằng số trước quan hệ**, **phủ 14 ô "đề luôn chốt"**, **`HỎI` có được thực thi không**), bảng phủ 10 mục BTC, và 3 bài học.

## Không được
- Đưa true-spec cho executor; đưa spec đội cho customer; tự đọc true-spec khi đóng vai đội (kể cả khi dựng mô hình bài toán ở bước 3b).
- Sinh true-spec thiếu bốn nhóm luật bắt buộc ở bước 2 — drill sẽ cho kết quả tốt giả.
- Bỏ bước chấm (8) hay bước tổng kết (9); bỏ pha `xac-nhan`.
- **Đưa chỉ thị format vào prompt gọi `customer`, gộp nhiều ý vào một lượt, hay đưa hội thoại trước vào prompt** — ba việc này phá đúng hạn mức mà diễn tập cần đo.
- Bỏ qua lượt bị từ chối như thể nó không xảy ra: phải ghi log, phải cộng nhịp.
- Chạy `xac-nhan` trước khi có bản nháp spec và bảng xếp hạng rủi ro giả định.
- Hỏi hết hàng đợi rồi mới bắt đầu viết spec.
- Sửa file knowledge trực tiếp.
