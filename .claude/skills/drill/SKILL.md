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
Đọc: `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A, §C; `${CLAUDE_PROJECT_DIR}/knowledge/32-cau-truc-spec-btc.md` §1–§2 (10 mục BTC + 5 gạch bảng Case — true-spec phải có dữ kiện cho cả 10 mục), §7 (Mermaid); `${CLAUDE_PROJECT_DIR}/knowledge/10-domain-giu-hang.md` §6 (catalogue ⚠ để cài luật phản trực giác); `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §1 (18 loại để chấm đa dạng test).

## Ranh giới thông tin (quy tắc cứng)
- Agent `executor` KHÔNG BAO GIỜ nhận đường dẫn `true-spec.md`, RTM, log, brief — chỉ `spec.md` + tình huống.
- Agent `customer` KHÔNG nhận `spec.md`, `rtm.md`, `tests/` — chỉ `true-spec.md` + câu hỏi (HỎI) hoặc tình huống + trả lời executor (CHẤM).
- Khi tự đóng vai đội (chế độ tự-động), không mở `true-spec.md`; chỉ dùng câu trả lời của customer. Ghi rõ điều này ở đầu `ket-qua.md`.

## Bước
1. `mkdir -p drill/<tên>/tests`. Ghi `drill/<tên>/README.md`: tham số drill, timestamp (Bash date).
2. **Sinh `true-spec.md`** (specs thật của BTC giả lập): ≥25 luật có mã `TS-xx`; TRONG/NGOÀI phạm vi tường minh (NGOÀI ≥5 mục); actor & quyền; bảng trạng thái đầy đủ; con số có đơn vị; ≥8 luật ⚠ cố ý khác mặc định phổ biến (lấy ý từ knowledge/10 §6 hoặc tương đương cho tính năng khác).
   - **Phải có dữ kiện cho cả 10 mục BTC** (knowledge/32 §1), gồm bốn vùng mới: **item màn hình** (control, mặc định, ẩn/disable, khác biệt login vs guest), **event** (mở màn hình lấy gì, nút gọi gì, double-click), **validation & message lỗi nguyên văn** (≥5 message đầy đủ chữ), **API/tích hợp** (nguồn tồn kho, retry khi thất bại). Nếu thiếu, đội diễn tập không có gì để hỏi ở lượt 7 và bài học sẽ sai.
   - Trong ≥8 luật ⚠, đặt **ít nhất 2 luật ⚠ thuộc vùng message/guest/trạng thái nút** (ví dụ: message vượt tồn ghi số còn lại; guest được tạo hold nhưng không xem lại được) — đây là loại lỗ hổng #16/#17/#18.
   - Ghi `true-spec.answers.md`: bảng `W-xx | TS-xx | phát biểu | mặc định phổ biến nó đi ngược`.
3. **Sinh `brief.md`** 150–250 từ, mơ hồ như BTC, không lộ bất kỳ ⚠ nào, không nêu con số.
4. **Vòng hỏi** (n lượt):
   - Chạy quy trình `/elicit lượt k drill/<tên>` (đọc và làm theo `${CLAUDE_PROJECT_DIR}/.claude/skills/elicit/SKILL.md`) → khối prompt. Với n ≥ 4, **một trong các lượt phải là lượt 7** (màn hình/event/validation/API) — nếu không, spec sẽ trống mục 2, 3, 4, 9.
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
   - **Lỗ hổng viết**: số `W-xx` có trong RTM (⚠) nhưng không có BR trong spec hoặc BR viết sai — liệt kê, kèm dòng template knowledge/30 hoặc knowledge/32 cần sửa.
   - **Phủ 10 mục BTC**: bảng mục 1–10 | spec có / thiếu / rỗng | dữ kiện có trong RTM không. Mục thiếu mà RTM CÓ dữ kiện = lỗ hổng viết; mục thiếu mà RTM KHÔNG có = lỗ hổng elicitation.
   - **Lỗ hổng tấn công**: test TRƯỢT vì Executor đoán trùng specs thật → ghi để sửa knowledge/50 §2-11 nếu lặp lại.
   - Số từ spec (wc -w), thời gian từng pha (nếu đo được).
   - 3 bài học hành động được: mỗi bài = file knowledge + mục + câu cần thêm/sửa. Không tự sửa knowledge — đề xuất để người dùng duyệt.

## Output bắt buộc
- [ ] `drill/<tên>/`: true-spec.md, true-spec.answers.md, brief.md, log-khach-hang.md, rtm.md, spec.md, review.md, eval-set.md, tests/doi-minh.md, ket-qua.md.
- [ ] `ket-qua.md` có 3 chỉ số (TRÚNG, lỗ hổng elicitation, lỗ hổng viết), **bảng phủ 10 mục BTC**, và 3 bài học.

## Không được
- Đưa true-spec cho executor; đưa spec đội cho customer; tự đọc true-spec khi đóng vai đội.
- Bỏ bước chấm (8) hay bước tổng kết (9).
- Sửa file knowledge trực tiếp.
