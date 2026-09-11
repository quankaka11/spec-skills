---
name: drill
description: Diễn tập trọn vòng Spec Battle với AI Khách hàng giả lập theo luật 09/09 (5 câu hỏi, mỗi lượt 1 câu, AI không có memory, 5.000 token; spec ≤6.000 token đích 5.400; điểm +2/+1/−1) — sinh specs thật ẩn có luật phản trực giác và phủ đủ 10 mục BTC (item màn hình, event, message lỗi nguyên văn, API), brief mơ hồ có mục tiêu chỏi nhau, chạy /frame → /elicit ↔ agent customer → /spec-write → /elicit restate → cổng F → /spec-review → /attack chính spec vừa viết, chấm bằng executor + customer, tổng kết điểm quy đổi và 9 chỉ số. Chỉ chạy khi người dùng gõ /drill (10–11/09 trước ngày thi).
argument-hint: "<tên> [tính-năng] [tự-động|thủ-công]"
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(LC_ALL=C.UTF-8 wc *), Bash(mkdir *), Bash(date *), Agent
---
# /drill — Diễn tập trọn vòng

Mục tiêu: đo xem mô hình bài toán + 5 câu hỏi + template 10 mục + cổng F có thật sự chắn được test không, trước khi thi thật; sinh bài học cụ thể để sửa `knowledge/20`, `knowledge/30`, `knowledge/32`, `knowledge/33`.

## Input
`$ARGUMENTS` = `<tên> [tính-năng] [chế-độ]`. Mặc định: tính năng = "đặt giữ hàng e-commerce" (kiểm domain), chế độ = tự-động. Thư mục làm việc `drill/<tên>/` (layout như `battle/`, thêm `true-spec.md`).

**Số lượt hỏi không còn là tham số: luật 09/09 chốt 5 câu, mỗi lượt đúng 1 câu, AI Khách hàng KHÔNG có memory (00 §A).** Diễn tập phải chạy đúng hạn mức đó — chạy nhiều lượt gộp nhiều câu như bản cũ sẽ cho kết quả tốt giả và không đo được thứ cần đo.

Đọc trước:
- `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A (tham số 09/09), **§A1 (công thức ước token)**, §C, **§D1 (công thức điểm +2/+1/−1)**.
- `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` §1–§2 (**10 mục BTC** + "ba thứ" và 5 gạch của bảng Case — true-spec phải có dữ kiện cho **cả 10 mục**), §7 (Mermaid).
- `${CLAUDE_PROJECT_DIR}/knowledge/33-kha-thi-van-hanh.md` §1 (**cổng F F1–F8**), §2 (bảng thực tế phụ thuộc ngoài — để true-spec nói về ngày làm việc / retry / callback đúng như hệ thật).
- `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §M5 (6 kẻ lạm dụng), §M6 (**12 ca suy biến**) — để cài luật vào true-spec đúng những chỗ đội hay bỏ.
- `${CLAUDE_PROJECT_DIR}/knowledge/20-ngan-hang-cau-hoi.md` §3 (**5 câu C1–C5**: C2 có cột hiển thị/trạng thái nút, C4 có message lỗi nguyên văn + guest), §5 (RTM hai loại dòng `A-xx` / `G-xx`).
- `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §6 (catalogue ⚠ để cài luật phản trực giác).
- `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (**24 loại lỗ hổng** — #16 message / #17 guest / #18 trạng thái UI từ cấu trúc BTC; #19–#24 từ cổng khả thi), §6 luật 7 (điểm kỳ vọng `EV`).

## Quy tắc cứng

### Hạn mức phải tôn trọng khi diễn tập (nếu phá, kết quả vô nghĩa)
- Đúng **5 câu hỏi** cho cả vòng; câu 5 là restate và **chỉ soạn sau khi có bản nháp spec**.
- Mỗi lần gọi agent `customer` chế độ HỎI chỉ chứa **một câu hỏi**. Agent trả lời câu đầu và đánh dấu phần dư bị bỏ (`[BỎ QUA: n câu hỏi ngoài một câu]`) — coi phần bị bỏ/bị cắt là **mất thật**, KHÔNG gọi lại để lấy nốt.
- Mỗi lần gọi là một **phiên độc lập**: KHÔNG đưa câu hỏi / câu trả lời của lượt trước vào prompt (giả lập "không có memory").
- Cộng dồn token ước theo 00 §A1 (`max(từ×2,5; ký tự/2,2)`, hệ số ×2,5 cho tiếng Việt); **vượt 5.000 thì dừng hỏi**, dù chưa hết 5 câu.
- Spec: đích **≤5.400 token** ước lượng, tuyệt đối **≤6.000**, markdown, không ảnh.

### Ranh giới thông tin
- Agent `executor` KHÔNG BAO GIỜ nhận đường dẫn `true-spec.md`, RTM, log, brief — chỉ `spec.md` + tình huống.
- Agent `customer` KHÔNG nhận `spec.md`, `rtm.md`, `tests/` — chỉ `true-spec.md` + một câu hỏi (HỎI) hoặc tình huống + trả lời executor (CHẤM).
- Khi tự đóng vai đội (chế độ tự-động), không mở `true-spec.md` — kể cả ở bước dựng mô hình bài toán; chỉ dùng `brief.md` + câu trả lời của customer. Ghi rõ điều này ở đầu `ket-qua.md`.

## Bước

1. `mkdir -p drill/<tên>/tests`. Ghi `drill/<tên>/README.md`: tham số drill, timestamp (Bash date).

2. **Sinh `true-spec.md`** (specs thật của BTC giả lập): ≥25 luật có mã `TS-xx`; TRONG/NGOÀI phạm vi tường minh (NGOÀI ≥5 mục); actor & quyền; bảng trạng thái đầy đủ; con số có đơn vị; ≥8 luật ⚠ cố ý khác mặc định phổ biến (lấy ý từ knowledge/10 §6 hoặc tương đương cho tính năng khác).

   **2a. Phải có dữ kiện cho cả 10 mục BTC** (knowledge/32 §1) — nếu thiếu, đội diễn tập không có gì để hỏi ở C2/C4 và bài học sẽ sai. Nêu đích danh bốn vùng:
   - **Mục 2 — item màn hình**: từng control (text / dropdown / button / list), giá trị mặc định, giới hạn, điều kiện ẩn / disable, **khác biệt login vs guest**, text hiển thị nguyên văn.
   - **Mục 3 — event**: mở màn hình lấy dữ liệu gì từ đâu, bấm nút gọi API nào, **bấm hai lần** thì sao.
   - **Mục 4 — validation & ≥5 message lỗi NGUYÊN VĂN** (đủ chữ, trong ngoặc kép), ghi rule chạy FE / BE / cả hai.
   - **Mục 9 — API / tích hợp**: nguồn sự thật của tồn kho, retry, timeout, gửi lại tay khi thất bại.

   **2b. Trong ≥8 luật ⚠, đặt ít nhất 2 luật ⚠ thuộc vùng message / guest / trạng thái nút** (loại #16 / #17 / #18 — tỷ lệ TRÚNG "Rất cao"). Ví dụ: message vượt tồn phải ghi số còn lại nguyên văn; guest được tạo hold nhưng không xem lại được; nút Đặt giữ disable khi tồn = 0 chứ không ẩn.

   **2c. Bốn nhóm luật bắt buộc thêm**, để drill đo được đúng lỗ hổng mà kit vừa vá:
   - (a) **≥1 luật cho MỖI ca trong 12 ca suy biến** knowledge/05 §M6 — nhất là hoàn tiền thất bại, callback trùng / sai thứ tự, job ngừng chạy, ghi giữ thất bại.
   - (b) Một **đơn vị neo hạn mức tường minh** (tài khoản đã xác thực / SĐT đã OTP / phương tiện thanh toán) và một luật **chặn kẻ đổi danh tính** (F2).
   - (c) **Hai mốc riêng cho mọi nghĩa vụ hoàn tiền**: mốc *khởi tạo* và mốc *hoàn tất*, số thực tế theo knowledge/33 §2 — tính bằng **ngày làm việc**, không phải giờ (F1).
   - (d) Một luật nói **ai chịu phí** khi hold bị bỏ / bị hủy (F4).

   Nếu true-spec thiếu 2a–2c thì drill sẽ báo "đội chắn tốt" một cách giả, vì không có gì để bắn ở đúng những chỗ đội hay hở.

   Ghi `true-spec.answers.md`: bảng `W-xx | TS-xx | phát biểu | mặc định phổ biến nó đi ngược | mục BTC liên quan | loại # (knowledge/50 §1)`.

3. **Sinh `brief.md`** 150–250 từ, mơ hồ như BTC, không lộ bất kỳ ⚠ nào, không nêu con số. **Bắt buộc có ≥2 mục tiêu nghiệp vụ tường minh** (kiểu "để tăng X", "chống Y") và **ít nhất một cặp câu chỏi nhau** — đó là chất liệu cho `/frame` M1 và bảng Mục tiêu↔Luật, và là thứ brief thật của BTC luôn có.

3b. **Dựng mô hình bài toán**: chạy quy trình `/frame drill/<tên>` (đọc `${CLAUDE_PROJECT_DIR}/.claude/skills/frame/SKILL.md`) → `mo-hinh-bai-toan.md` + danh sách "Câu hỏi P0" + "Mâu thuẫn nội tại của brief". Khi đóng vai đội, chỉ đọc `brief.md`, không mở `true-spec.md`.

4. **Kế hoạch hỏi**: chạy quy trình `/elicit ke-hoach drill/<tên>` (đọc `${CLAUDE_PROJECT_DIR}/.claude/skills/elicit/SKILL.md`) → 5 câu đã gọt (C1–C5, trong đó C5 để trống chờ bản nháp) + danh sách ô **phải tự điền** bằng mặc định ngành.

4b. **Bốn câu hỏi C1–C4, từng câu một**:
   - Chạy `/elicit cau <k> drill/<tên>` với k = 1..4 → một khối câu hỏi đã qua cổng 5 kiểm tra.
   - Tự-động: gọi Agent `subagent_type: customer`, prompt = `CHẾ ĐỘ: HỎI` / `Đường dẫn true-spec: <tuyệt đối>` / dòng trống / **đúng một câu hỏi nguyên văn**. Thủ-công: in khối, chờ người dùng dán câu trả lời.
   - Chạy `/elicit nap drill/<tên>` với câu trả lời nhận được → dòng `A-xx` trong RTM + khối log có timestamp; cộng dồn token ước.
   - Bốn câu này **gọi được song song** (customer không có memory nên thứ tự không ảnh hưởng) — nhưng vẫn phải tôn trọng hạn mức token cộng dồn, và vẫn nạp lần lượt để RTM không bị trộn.
   - Mọi ô không câu nào phủ tới → sinh dòng `G-xx` (giả định, mặc định ngành, có xếp hạng rủi ro).

5. **Viết**: chạy quy trình `/spec-write drill/<tên>` → bản nháp `spec.md` theo **10 mục BTC** + **bảng xếp hạng rủi ro giả định**.

5b. **Câu 5 — restate**: chạy `/elicit restate drill/<tên>` (lấy ~10 phát biểu từ bảng xếp hạng rủi ro), gửi cho `customer` như một phiên độc lập, nạp kết quả, rồi **vá spec**: mọi ý "Sai" phải sửa BR tương ứng. Ghi số ý "Sai" — **đó là số lỗ hổng mà câu restate cứu được**.

5c. **Cổng khả thi**: chạy cổng F (`knowledge/33` §1, F1–F8) trên `spec.md` của mình, đánh ✓/✗ từng BR. ✗ ở F1/F2/F5/F7 = mức Cao, phải vá trước bước 6.

6. **Review**: chạy `/spec-review drill/<tên>/spec.md 12 sửa` (executor mù theo quy trình đó, eval set **hai reader** — reader 2 dùng model yếu hơn). Ghi số lỗi Cao trước / sau, và số hit "giá trị tự nghĩ ra" (dòng `G-xx` lệch cả mặc định ngành lẫn log).

7. **Đổi vai — tự bắn**: chạy `/attack drill/<tên>/spec.md doi-minh drill/<tên>` → tối đa 5 test, **chỉ nộp test có `EV > 0`** (executor mù dry-run theo quy trình đó), ≥1 test thuộc nhóm #19–#24, ≥2 test thuộc #16/#17/#18.

8. **Chấm** mỗi test: (a) Agent `executor` với prompt hai dòng `Đường dẫn spec: drill/<tên>/spec.md` + `Tình huống: ...`; (b) Agent `customer` với `CHẾ ĐỘ: CHẤM` / `Đường dẫn true-spec: ...` / `Tình huống: ...` / `Trả lời Executor: <output (a)>` → KẾT QUẢ. Gọi (a) song song cho mọi test, rồi (b) song song.

9. **Tổng kết `ket-qua.md`** — bảng test | loại # | ĐỘ PHỦ executor | KẾT QUẢ | lý do đối chiếu (rút gọn), rồi các chỉ số:
   - **Tỷ lệ TRÚNG / TRƯỢT / VÔ HIỆU** trên số test đã nộp.
   - **Điểm quy đổi** theo công thức 09/09 (00 §D1): `TRÚNG×2 − VÔ HIỆU×1` cho vai CÔNG. So với điểm nếu bỏ các test `EV ≤ 0` — để kiểm quy tắc "bỏ slot còn hơn bắn bừa" (bỏ slot = 0 điểm, VÔ HIỆU = −1).
   - **Phủ 10 mục BTC**: bảng mục 1–10 | spec có / thiếu / rỗng | dữ kiện có trong RTM không. **Mục thiếu mà RTM CÓ dữ kiện = lỗ hổng viết; mục thiếu mà RTM KHÔNG có dữ kiện = lỗ hổng elicitation.**
   - **Lỗ hổng elicitation**: số dòng `W-xx` trong `true-spec.answers.md` KHÔNG có dòng RTM tương ứng (đội không hỏi tới) — liệt kê, kèm câu hỏi nên thêm vào knowledge/20 §2 (ID nhóm N).
   - **Lỗ hổng viết**: số `W-xx` có trong RTM (⚠) nhưng không có BR trong spec, hoặc BR viết sai — liệt kê, kèm dòng template knowledge/30 hoặc knowledge/32 cần sửa.
   - **Lỗ hổng giả định**: với mỗi dòng `G-xx` trong RTM, đối chiếu `true-spec.answers.md` và phân ba loại — trùng specs thật / trùng mặc định ngành nhưng lệch specs thật / **ngược mặc định ngành và lệch specs thật (loại tệ nhất, phải = 0)**. Đo trực tiếp quy tắc knowledge/30 §1b.
   - **Đa nghĩa thật**: số tình huống eval mà **hai reader** ở `/spec-review` khối D ra kết quả khác nhau, so với số ca reader tự khai `ĐA NGHĨA = KHÔNG`. Hai số lệch nhau nhiều = bằng chứng vòng tự khai không dùng được.
   - **Hiệu quả câu restate**: số ý "Sai" ở C5 · số BR đã sửa nhờ đó · trong số đó bao nhiêu BR về sau bị test bắn vào. ≥2 BR bị bắn được cứu ⇒ xác nhận quy tắc "viết trước, restate sau"; 0 ⇒ xét lại việc dùng cả một câu hỏi cho restate.
   - **Lỗ hổng mục tiêu**: cặp câu chỏi nhau ở brief (bước 3) — đội có phát hiện và hỏi lại không, hay tự hoà giải rồi viết luật một chiều? Đo bằng: `/frame` có xếp cặp đó vào nhóm "vào 5 câu hỏi" không, và C1 có hỏi tới không.
   - **Lỗ hổng khả thi**: số ✗ cổng F (knowledge/33 §1) trên spec của mình, tách theo F1–F8; và với mỗi `W-xx` thuộc loại #19–#24, spec đội có luật đúng / có luật sai / im lặng. Đây là chỉ số đo trực tiếp cổng F có hoạt động không.
   - **Lỗ hổng tấn công**: test TRƯỢT vì Executor đoán trùng specs thật → ghi để sửa knowledge/50 §2–§11 nếu lặp lại.
   - **Độ chính xác ước token**: token thật (nếu đo được) / ước theo `max(từ×2,5; ký tự/2,2)` — cho cả lượt hỏi và cho spec; **lệch >20% thì chỉnh hệ số ở knowledge/00 §A1**.
   - Token spec (cả hai công thức) + đệm còn lại dưới 6.000; thời gian từng pha (nếu đo được).
   - **3 bài học hành động được**: mỗi bài = file knowledge + mục + câu cần thêm / sửa. Không tự sửa knowledge — đề xuất để người dùng duyệt.

## Output bắt buộc
- [ ] `drill/<tên>/`: true-spec.md, true-spec.answers.md, brief.md, mo-hinh-bai-toan.md, log-khach-hang.md, rtm.md, spec.md, review.md, eval-set.md, tests/doi-minh.md, ket-qua.md.
- [ ] `ket-qua.md` có đủ các chỉ số: điểm quy đổi, **phủ 10 mục BTC**, lỗ hổng elicitation, lỗ hổng viết, **lỗ hổng giả định**, **đa nghĩa thật**, **hiệu quả câu restate**, lỗ hổng mục tiêu, **lỗ hổng khả thi**, độ chính xác ước token — và 3 bài học.
- [ ] Dòng đầu `ket-qua.md` khẳng định: khi đóng vai đội đã KHÔNG mở `true-spec.md`.

## Không được
- Đưa true-spec cho executor; đưa spec đội cho customer; tự đọc true-spec khi đóng vai đội (kể cả ở bước 3b dựng mô hình bài toán).
- Sinh true-spec thiếu dữ kiện cho một trong **10 mục BTC**, thiếu 2 luật ⚠ vùng message / guest / trạng thái nút, hay thiếu bốn nhóm luật 2c — drill sẽ cho kết quả tốt giả.
- **Gửi quá 5 câu hỏi, gộp nhiều câu hỏi vào một lần gọi `customer`, hay đưa hội thoại trước vào prompt** — ba việc này phá đúng hạn mức mà diễn tập cần đo.
- Hỏi lại phần mà `customer` đã đánh dấu bỏ / cắt.
- Soạn câu 5 trước khi có bản nháp spec và bảng xếp hạng rủi ro giả định.
- Bỏ cổng F (5c), bước chấm (8) hay bước tổng kết (9); bỏ câu `restate`.
- Nộp test có `EV ≤ 0` trong bước 7 rồi tính vào điểm chính (ghi riêng để so sánh).
- Sửa file knowledge trực tiếp.
