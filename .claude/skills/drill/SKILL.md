---
name: drill
description: Diễn tập trọn vòng Spec Battle với AI Khách hàng giả lập theo luật 09/09 (5 câu hỏi, 1 câu/lượt, không memory, 5.000 token; spec ≤6.000 token; điểm +2/+1/−1) — sinh specs thật ẩn có luật phản trực giác, brief mơ hồ, chạy /elicit ↔ agent customer, /spec-write, /spec-review, /attack chính spec vừa viết, chấm bằng executor + customer, tổng kết điểm và 8 chỉ số. Chỉ chạy khi người dùng gõ /drill (10–11/09 trước ngày thi).
argument-hint: "<tên> [tính-năng] [tự-động|thủ-công]"
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(LC_ALL=C.UTF-8 wc *), Bash(mkdir *), Bash(date *), Agent
---
# /drill — Diễn tập trọn vòng

Mục tiêu: đo xem question bank + template + quy trình có thật sự chắn được test không, trước khi thi thật; sinh bài học cụ thể để sửa `knowledge/20` và `knowledge/30`.

## Input
`$ARGUMENTS` = `<tên> [tính-năng] [chế-độ]`. Mặc định: tính năng = "đặt giữ hàng e-commerce" (kiểm domain), chế độ = tự-động. Thư mục làm việc `drill/<tên>/` (layout như `battle/`, thêm `true-spec.md`).

**Số lượt hỏi không còn là tham số: luật 09/09 chốt 5 câu, mỗi lượt 1 câu, AI không có memory (00 §A).** Diễn tập phải chạy đúng hạn mức đó — chạy 4 lượt batch như bản cũ sẽ cho kết quả tốt giả và không đo được thứ cần đo.
Đọc: `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A (tham số 09/09), §A1 (ước token), §C, §D1 (công thức điểm); `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §6 (catalogue ⚠ để cài luật phản trực giác); `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (21 loại để chấm đa dạng test), §6 luật 8 (điểm kỳ vọng); `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §M5, §M6 (để cài luật vào true-spec ở đúng những chỗ đội hay bỏ); `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md` §2 (để true-spec nói về phụ thuộc ngoài đúng như hệ thật).

## Hạn mức phải tôn trọng khi diễn tập (nếu phá, kết quả vô nghĩa)
- Đúng **5 câu hỏi** cho cả vòng; câu 5 là restate và **chỉ soạn sau khi có bản nháp spec**.
- Mỗi lần gọi agent `customer` chế độ HỎI chỉ chứa **một câu hỏi**. Agent sẽ trả lời câu đầu và đánh dấu phần dư bị bỏ — coi phần bị bỏ là **mất thật**, không gọi lại.
- Mỗi lần gọi là một **phiên độc lập**: không đưa câu hỏi/câu trả lời trước vào prompt (giả lập "không có memory").
- Cộng dồn token ước (00 §A1); vượt 5.000 thì dừng hỏi, dù chưa hết 5 câu.
- Spec: đích ≤5.400 token, tuyệt đối ≤6.000, markdown, không ảnh.

## Ranh giới thông tin (quy tắc cứng)
- Agent `executor` KHÔNG BAO GIỜ nhận đường dẫn `true-spec.md`, RTM, log, brief — chỉ `spec.md` + tình huống.
- Agent `customer` KHÔNG nhận `spec.md`, `rtm.md`, `tests/` — chỉ `true-spec.md` + câu hỏi (HỎI) hoặc tình huống + trả lời executor (CHẤM).
- Khi tự đóng vai đội (chế độ tự-động), không mở `true-spec.md`; chỉ dùng câu trả lời của customer. Ghi rõ điều này ở đầu `ket-qua.md`.

## Bước
1. `mkdir -p drill/<tên>/tests`. Ghi `drill/<tên>/README.md`: tham số drill, timestamp (Bash date).
2. **Sinh `true-spec.md`** (specs thật của BTC giả lập): ≥25 luật có mã `TS-xx`; TRONG/NGOÀI phạm vi tường minh (NGOÀI ≥5 mục); actor & quyền; bảng trạng thái đầy đủ; con số có đơn vị; ≥8 luật ⚠ cố ý khác mặc định phổ biến (lấy ý từ knowledge/10 §6 hoặc tương đương cho tính năng khác).
   **Bắt buộc thêm, để drill đo được đúng lỗ hổng mà kit vừa vá:** (a) ≥1 luật cho **mỗi** kịch bản trong 12 ca knowledge/05 §M6 — nhất là hoàn tiền thất bại, callback trùng/sai thứ tự, job ngừng chạy, ghi giữ thất bại; (b) một **đơn vị neo hạn mức tường minh** (tài khoản / SĐT đã OTP / thẻ) và một luật chặn kẻ đổi danh tính; (c) hai mốc riêng cho mọi nghĩa vụ hoàn tiền (khởi tạo + hoàn tất, số thực tế theo knowledge/32 §2 — ngày làm việc, không phải giờ); (d) một luật nói **ai chịu phí** khi hold bị bỏ. Nếu true-spec không có bốn nhóm này thì drill sẽ báo "đội chắn tốt" một cách giả, vì không có gì để bắn ở đúng những chỗ đội hay hở.
   Ghi `true-spec.answers.md`: bảng `W-xx | TS-xx | phát biểu | mặc định phổ biến nó đi ngược | loại # (knowledge/50 §1)`.
3. **Sinh `brief.md`** 150–250 từ, mơ hồ như BTC, không lộ bất kỳ ⚠ nào, không nêu con số. **Bắt buộc có ≥2 mục tiêu nghiệp vụ tường minh** (kiểu "để tăng X", "chống Y") và **ít nhất một cặp câu chỏi nhau** — đó là chất liệu cho `/frame` M1 và bảng Mục tiêu↔Luật, và là thứ brief thật của BTC luôn có.
3b. **Dựng mô hình bài toán**: chạy quy trình `/frame drill/<tên>` (đọc `${CLAUDE_PROJECT_DIR}/.claude/skills/frame/SKILL.md`) → `mo-hinh-bai-toan.md` + danh sách câu hỏi P0. Khi đóng vai đội, chỉ đọc `brief.md`, không mở `true-spec.md`.
4. **Kế hoạch hỏi**: chạy quy trình `/elicit ke-hoach drill/<tên>` → 4 câu đã gọt + danh sách ô sẽ tự điền.
4b. **Bốn câu hỏi C1–C4**, từng câu một:
   - Chạy `/elicit cau k drill/<tên>` (đọc và làm theo `${CLAUDE_PROJECT_DIR}/.claude/skills/elicit/SKILL.md`) → một khối câu hỏi.
   - Tự-động: gọi Agent `subagent_type: customer`, prompt = `CHẾ ĐỘ: HỎI` / `Đường dẫn true-spec: <tuyệt đối>` / dòng trống / **đúng một câu hỏi**. Thủ-công: in khối, chờ người dùng dán.
   - Chạy `/elicit nap drill/<tên>` với câu trả lời nhận được; cộng token.
   - Bốn câu này gọi được **song song** (customer không có memory nên thứ tự không ảnh hưởng) — nhưng vẫn nạp lần lượt để RTM không bị trộn.
5. **Viết**: chạy quy trình `/spec-write drill/<tên>` → bản nháp + **bảng xếp hạng rủi ro giả định**.
5b. **Câu 5 — restate**: chạy `/elicit restate drill/<tên>` (lấy 10 phát biểu từ bảng xếp hạng), gửi cho `customer`, nạp, rồi vá spec: mọi ý "Sai" phải sửa BR tương ứng. Ghi số ý Sai — **đó là số lỗ hổng mà restate cứu được**.
6. **Review**: chạy `/spec-review drill/<tên>/spec.md 12 sửa` (executor gọi mù theo quy trình đó). Ghi số lỗi Cao trước/sau, và số hit khối G-7 (giá trị tự nghĩ ra).
7. **Đổi vai — tự bắn**: chạy `/attack drill/<tên>/spec.md doi-minh drill/<tên>` → tối đa 5 test có `EV > 0` (executor mù theo quy trình đó).
8. **Chấm** mỗi test: (a) Agent `executor` với `Đường dẫn spec: drill/<tên>/spec.md` + tình huống; (b) Agent `customer` với `CHẾ ĐỘ: CHẤM` / `Đường dẫn true-spec: ...` / `Tình huống: ...` / `Trả lời Executor: <output (a)>` → KẾT QUẢ. Gọi (a) song song 5 test, rồi (b) song song.
9. **Tổng kết `ket-qua.md`**:
   - Bảng: test | loại # | ĐỘ PHỦ executor | KẾT QUẢ | lý do đối chiếu (rút gọn).
   - Tỷ lệ TRÚNG / TRƯỢT / VÔ HIỆU trên số test đã nộp, **và điểm quy đổi theo công thức 09/09**: `TRÚNG×2 − VÔ HIỆU×1` cho vai CÔNG; số test TRƯỢT × 1 cho vai THỦ nếu tính ngược lại. So với điểm nếu bỏ các test `EV ≤ 0` — để kiểm quy tắc "bỏ slot còn hơn bắn bừa".
   - **Lỗ hổng elicitation**: số dòng `W-xx` trong `true-spec.answers.md` KHÔNG có dòng RTM tương ứng (đội không hỏi tới) — liệt kê, kèm câu hỏi nên thêm vào knowledge/20 §2 (ID nhóm N).
   - **Lỗ hổng viết**: số `W-xx` có trong RTM (⚠) nhưng không có BR trong spec hoặc BR viết sai — liệt kê, kèm dòng template knowledge/30 cần sửa.
   - **Lỗ hổng tấn công**: test TRƯỢT vì Executor đoán trùng specs thật → ghi để sửa knowledge/50 §2-11 nếu lặp lại.
   - **Lỗ hổng khả thi**: với mỗi `W-xx` thuộc loại #16–#20, đối chiếu spec đội — spec có luật đúng, có luật sai, hay im lặng. Đây là chỉ số đo trực tiếp cổng F có hoạt động không.
   - **Lỗ hổng mục tiêu**: cặp câu chỏi nhau ở brief (bước 3) — đội có phát hiện và hỏi lại không, hay tự hoà giải rồi viết luật một chiều? Đo bằng: `/frame` bước 9 có xếp cặp đó vào nhóm 1 không, và C1 có hỏi tới không.
   - **Chỉ số đa nghĩa thật**: số tình huống eval mà hai reader ở `/spec-review` khối D ra kết quả khác nhau, so với số ca reader tự khai `ĐA NGHĨA = KHÔNG`. Hai số này lệch nhau nhiều = bằng chứng vòng tự khai không dùng được.
   - **Lỗ hổng giả định**: với mỗi dòng `G-xx` trong RTM, đối chiếu `true-spec.answers.md` — giả định trùng specs thật / trùng mặc định ngành nhưng lệch specs thật / **ngược mặc định ngành và lệch specs thật** (loại tệ nhất, phải = 0). Đây là chỉ số đo trực tiếp quy tắc 30 §1b.
   - **Hiệu quả câu restate**: số ý "Sai" ở C5 · số BR đã sửa nhờ đó · trong số đó bao nhiêu BR về sau bị test bắn vào. Nếu C5 cứu được ≥2 BR bị bắn thì quy tắc "viết trước, restate sau" được xác nhận; nếu 0 thì xét lại việc dùng cả một câu hỏi cho restate.
   - **Độ chính xác ước token**: token thật (nếu đo được) / ước theo `max(từ×2,5; ký tự/2,2)` — cho cả lượt hỏi và cho spec; lệch >20% thì chỉnh hệ số ở 00 §A1.
   - Token spec (cả hai công thức) + đệm còn lại dưới 6.000, thời gian từng pha (nếu đo được).
   - 3 bài học hành động được: mỗi bài = file knowledge + mục + câu cần thêm/sửa. Không tự sửa knowledge — đề xuất để người dùng duyệt.

## Output bắt buộc
- [ ] `drill/<tên>/`: true-spec.md, true-spec.answers.md, brief.md, mo-hinh-bai-toan.md, log-khach-hang.md, rtm.md, spec.md, review.md, eval-set.md, tests/doi-minh.md, ket-qua.md.
- [ ] `ket-qua.md` có 8 chỉ số (điểm quy đổi, lỗ hổng elicitation, lỗ hổng viết, lỗ hổng khả thi, lỗ hổng mục tiêu, đa nghĩa thật, **lỗ hổng giả định**, **hiệu quả câu restate**) và 3 bài học.

## Không được
- Đưa true-spec cho executor; đưa spec đội cho customer; tự đọc true-spec khi đóng vai đội (kể cả khi dựng mô hình bài toán ở bước 3b).
- Sinh true-spec thiếu bốn nhóm luật bắt buộc ở bước 2 — drill sẽ cho kết quả tốt giả.
- Bỏ bước chấm (8) hay bước tổng kết (9); bỏ câu `restate`.
- **Gửi quá 5 câu hỏi, gộp nhiều câu hỏi vào một lần gọi `customer`, hay đưa hội thoại trước vào prompt** — ba việc này phá đúng hạn mức mà diễn tập cần đo.
- Soạn câu 5 trước khi có bản nháp spec và bảng xếp hạng rủi ro giả định.
- Hỏi lại phần mà `customer` đã đánh dấu bỏ.
- Sửa file knowledge trực tiếp.
