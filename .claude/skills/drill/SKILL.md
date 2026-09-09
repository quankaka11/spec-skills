---
name: drill
description: Diễn tập trọn vòng Spec Battle với AI Khách hàng giả lập — sinh specs thật ẩn có luật phản trực giác, brief mơ hồ, chạy /elicit ↔ agent customer, /spec-write, /spec-review, /attack chính spec vừa viết, chấm bằng executor + customer, tổng kết tỷ lệ TRÚNG và lỗ hổng elicitation/viết. Chỉ chạy khi người dùng gõ /drill (10–11/09 trước ngày thi).
argument-hint: "<tên> [tính-năng] [lượt=4] [tự-động|thủ-công]"
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(wc *), Bash(mkdir *), Bash(date *), Agent
---
# /drill — Diễn tập trọn vòng

Mục tiêu: đo xem question bank + template + quy trình có thật sự chắn được test không, trước khi thi thật; sinh bài học cụ thể để sửa `knowledge/20` và `knowledge/30`.

## Input
`$ARGUMENTS` = `<tên> [tính-năng] [lượt] [chế-độ]`. Mặc định: tính năng = "đặt giữ hàng e-commerce" (kiểm domain), lượt = 4, chế độ = tự-động. Thư mục làm việc `drill/<tên>/` (layout như `battle/`, thêm `true-spec.md`).
Đọc: `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A, §C; `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §6 (catalogue ⚠ để cài luật phản trực giác); `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (20 loại để chấm đa dạng test); `${CLAUDE_PROJECT_DIR}/knowledge/05-hieu-bai-toan.md` §M5, §M6 (để cài luật vào true-spec ở đúng những chỗ đội hay bỏ); `${CLAUDE_PROJECT_DIR}/knowledge/32-kha-thi-van-hanh.md` §2 (để true-spec nói về phụ thuộc ngoài đúng như hệ thật).

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
4. **Vòng hỏi** (n lượt), trong đó lượt cuối cùng luôn là `restate`:
   - Chạy quy trình `/elicit lượt k drill/<tên>` (đọc và làm theo `${CLAUDE_PROJECT_DIR}/.claude/skills/elicit/SKILL.md`) → khối prompt.
   - Tự-động: gọi Agent `subagent_type: customer`, prompt = `CHẾ ĐỘ: HỎI` / `Đường dẫn true-spec: <tuyệt đối>` / dòng trống / khối prompt nguyên văn. Thủ-công: in khối, chờ người dùng dán câu trả lời.
   - Chạy `/elicit nạp drill/<tên>` với câu trả lời nhận được.
   - TOKEN_MAX: dùng giá trị knowledge/00 §A; nếu "CHỜ 09/09" thì đặt 20.000 và ghi rõ giả định.
5. **Viết**: chạy quy trình `/spec-write drill/<tên>`.
6. **Review**: chạy `/spec-review drill/<tên>/spec.md 12 sửa` (executor gọi mù theo quy trình đó). Ghi số lỗi Cao trước/sau.
7. **Đổi vai — tự bắn**: chạy `/attack drill/<tên>/spec.md doi-minh drill/<tên>` → 5 test (executor mù theo quy trình đó).
8. **Chấm** mỗi test: (a) Agent `executor` với `Đường dẫn spec: drill/<tên>/spec.md` + tình huống; (b) Agent `customer` với `CHẾ ĐỘ: CHẤM` / `Đường dẫn true-spec: ...` / `Tình huống: ...` / `Trả lời Executor: <output (a)>` → KẾT QUẢ. Gọi (a) song song 5 test, rồi (b) song song.
9. **Tổng kết `ket-qua.md`**:
   - Bảng: test | loại # | ĐỘ PHỦ executor | KẾT QUẢ | lý do đối chiếu (rút gọn).
   - Tỷ lệ TRÚNG / TRƯỢT / VÔ HIỆU trên 5.
   - **Lỗ hổng elicitation**: số dòng `W-xx` trong `true-spec.answers.md` KHÔNG có dòng RTM tương ứng (đội không hỏi tới) — liệt kê, kèm câu hỏi nên thêm vào knowledge/20 §2 (ID nhóm N).
   - **Lỗ hổng viết**: số `W-xx` có trong RTM (⚠) nhưng không có BR trong spec hoặc BR viết sai — liệt kê, kèm dòng template knowledge/30 cần sửa.
   - **Lỗ hổng tấn công**: test TRƯỢT vì Executor đoán trùng specs thật → ghi để sửa knowledge/50 §2-11 nếu lặp lại.
   - **Lỗ hổng khả thi**: với mỗi `W-xx` thuộc loại #16–#20, đối chiếu spec đội — spec có luật đúng, có luật sai, hay im lặng. Đây là chỉ số đo trực tiếp cổng F có hoạt động không.
   - **Lỗ hổng mục tiêu**: cặp câu chỏi nhau ở brief (bước 3) — đội có phát hiện và hỏi lại không, hay tự hoà giải rồi viết luật một chiều? Đo bằng: câu hỏi P0 của `/frame` có nêu cặp đó không, và lượt 1 có hỏi không.
   - **Chỉ số đa nghĩa thật**: số tình huống eval mà hai reader ở `/spec-review` khối D ra kết quả khác nhau, so với số ca reader tự khai `ĐA NGHĨA = KHÔNG`. Hai số này lệch nhau nhiều = bằng chứng vòng tự khai không dùng được.
   - **Độ chính xác ước token**: hệ số token thực tế đo được / 2,5 — để chỉnh knowledge/20 §3 nếu lệch.
   - Số từ spec (wc -w) + đệm còn lại, thời gian từng pha (nếu đo được).
   - 3 bài học hành động được: mỗi bài = file knowledge + mục + câu cần thêm/sửa. Không tự sửa knowledge — đề xuất để người dùng duyệt.

## Output bắt buộc
- [ ] `drill/<tên>/`: true-spec.md, true-spec.answers.md, brief.md, mo-hinh-bai-toan.md, log-khach-hang.md, rtm.md, spec.md, review.md, eval-set.md, tests/doi-minh.md, ket-qua.md.
- [ ] `ket-qua.md` có 6 chỉ số (TRÚNG, lỗ hổng elicitation, lỗ hổng viết, lỗ hổng khả thi, lỗ hổng mục tiêu, đa nghĩa thật) và 3 bài học.

## Không được
- Đưa true-spec cho executor; đưa spec đội cho customer; tự đọc true-spec khi đóng vai đội (kể cả khi dựng mô hình bài toán ở bước 3b).
- Sinh true-spec thiếu bốn nhóm luật bắt buộc ở bước 2 — drill sẽ cho kết quả tốt giả.
- Bỏ bước chấm (8) hay bước tổng kết (9); bỏ lượt `restate`.
- Sửa file knowledge trực tiếp.
