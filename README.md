# Spec Battle Toolkit

Bộ công cụ Claude Code cho đội thi **HBLAB AI Hackathon #02 — Spec Battle** (12/09/2026): dựng mô hình bài toán, hỏi AI Khách hàng, viết & review spec, tấn công đội khác, kháng nghị. Chi tiết kiến trúc skill/agent xem [`.claude/README.md`](.claude/README.md); tiến độ chuẩn bị xem [`PROGRESS.md`](PROGRESS.md).

**Sau khi pull: mở phiên Claude Code MỚI** trong thư mục repo — agent tùy chỉnh (`executor`, `customer`) và skill mới (`/frame`) chỉ được nạp lúc khởi động phiên.

## Tham số cuộc thi (chốt họp BTC 09/09 + tài liệu 11/09)

| Tham số | Giá trị |
|---|---|
| Hỏi AI Khách hàng | **5 câu cho cả ngày · mỗi lượt đúng 1 câu · 5.000 token · KHÔNG có memory** |
| Hạn mức spec | **≤ 6.000 token, đích 5.400** · khóa 12:00 |
| Cấu trúc spec nộp | **10 mục** kiểu thiết kế Nhật (基本設計 / 詳細設計 / 技術仕様) |
| Điểm | CÔNG trúng **+2** · THỦ đỡ được **+1** · CÔNG bị VÔ HIỆU **−1** |
| Kháng nghị | chỉ ca test CÔNG bị chấm **VÔ HIỆU**, text gửi AI, ≤3 ca |

Đầy đủ + cách đo token + 9 ô còn hở phải hỏi BTC: [`knowledge/00-luat-choi.md`](knowledge/00-luat-choi.md) §A, §A1, §A2.

## Cấu trúc thư mục

| Đường dẫn | Nội dung |
|---|---|
| `knowledge/00…50-*.md` | 10 file tri thức chưng cất (luật chơi, **mô hình bài toán**, domain, ngân hàng câu hỏi, **cấu trúc spec BTC 10 mục**, viết spec, **cổng khả thi**, từ mơ hồ, tấn công) — skill đọc trực tiếp, không chép lại |
| `.claude/skills/<tên>/SKILL.md` | 7 skill: `frame`, `elicit`, `spec-write`, `spec-review`, `attack`, `appeal`, `drill` |
| `.claude/agents/{executor,customer}.md` | 2 agent giả lập, mù bối cảnh thật |
| `data/btc/` | Tài liệu BTC gửi, bản trích nguyên văn (đối chiếu khi tranh luận) |
| `hackathon_descriptions.md` | Tài liệu nền hợp nhất về cuộc thi (mô tả, luật, 9 phần + phụ lục) |
| `battle/` | Tạo khi vào thi thật: brief, mô hình, log, RTM, spec, test — skill tự sinh các file còn lại |
| `drill/` | Fixture & kết quả diễn tập trước ngày thi |

## Chuẩn bị

1. Pull repo mới nhất
2. Mở Claude Code trong thư mục repo (phiên mới)
3. Tạo file `battle/brief.md` và dán đề bài

## Quy trình thi (Sáng, 9:00–12:00)

### Hiểu bài toán trước khi hỏi (9:00–9:30)

```bash
/frame                  # → battle/mo-hinh-bai-toan.md: 6 khối M1–M6
```

Sáu khối: mục tiêu & thước đo · dòng tiền · dòng tồn & nguồn chân lý · biên hệ thống 6 láng giềng · mô hình lạm dụng · 12 kịch bản suy biến. Đầu ra quan trọng nhất là **ba nhóm ô chưa biết**: ô nào vào 5 câu hỏi, ô nào vào hàng đợi câu restate, ô nào tự điền bằng mặc định ngành.

**Không gõ câu hỏi nào trước khi bước này xong.** Ở diễn tập 08/09, một spec đạt mọi cổng hình thức vẫn có 3 luật phá thẳng mục tiêu của brief — chỉ mô hình bài toán mới bắt được loại lỗi đó.

### Năm câu hỏi cho AI Khách hàng (9:30–10:20, câu cuối 11:20)

```bash
/elicit ke-hoach        # Gọt 4 câu từ mô hình + bảng phủ 10 mục BTC + danh sách ô sẽ KHÔNG hỏi
/elicit cau 1           # Sinh đúng MỘT câu, đã qua cổng 5 kiểm tra → copy gửi
/elicit nap             # Dán câu trả lời — cập nhật RTM ngược + log có timestamp
# Lặp cau 2, 3, 4 (gửi xong trước 10:20)
```

Chỉ có **5 câu cho cả ngày** và AI **không có memory**, nên mỗi câu phải tự chứa và xin về **một bảng** chứ không một dòng. Bốn câu đầu: phạm vi & điều cấm · bảng chuyển trạng thái (kèm cột hiển thị/nút) · bảng tham số · kịch bản suy biến (kèm **message lỗi nguyên văn** và **guest vs login**). Câu thứ năm để cuối, sau khi có bản nháp — xem dưới.

~70% ngân hàng câu hỏi **không được hỏi**; phần đó viết bằng **mặc định ngành** ([`knowledge/20`](knowledge/20-ngan-hang-cau-hoi.md) §4), gắn dòng `G-xx` trong RTM, và ứng viên rủi ro cao nhất đi vào câu 5.

### Viết spec (10:20–11:20)

```bash
/spec-write             # → battle/spec.md (nội bộ) + battle/spec.nop.md (bản nộp) ≤5.400 token
```

Spec theo **cấu trúc 10 mục BTC** (tài liệu "Spec Battle Anatomy", 11/09): `1` Tổng quan & phạm vi · `2` Item màn hình · `3` Event · `4` Validation & message lỗi · `5` Wireframe · `6` Flow & quy tắc xử lý · `7` Ràng buộc/bất thường/chưa chốt · `8` Xác thực & phân quyền · `9` Luồng dữ liệu & API · `10` Data model, perf, security. Chi tiết + ngân sách token từng mục: [`knowledge/32-cau-truc-spec-btc.md`](knowledge/32-cau-truc-spec-btc.md).

Sơ đồ vẽ bằng **Mermaid** (không có Figma trong phòng thi): `block-beta` wireframe · `stateDiagram-v2` state machine · `sequenceDiagram` flow end-to-end · `flowchart LR` sơ đồ hệ thống. **Sơ đồ không thay bảng** — sơ đồ chỉ vẽ chuyển hợp lệ, ô `Từ chối 0.5`/`KHL` chỉ có trong bảng; hết chỗ thì bỏ sơ đồ, giữ bảng. Spec có sơ đồ thì **bắt buộc có dòng catch-all 0.15** (bảng và luật có mã thắng khi sơ đồ chỏi bảng).

Đầu ra then chốt của bước này ngoài spec: **bảng xếp hạng rủi ro giả định** — nguyên liệu cho câu hỏi cuối.

### Câu 5 — restate, rồi vá (11:20–11:46)

```bash
/elicit restate         # 8–10 phát biểu Đúng/Sai lấy từ đầu bảng xếp hạng rủi ro
/frame muc-tieu-luat    # Bảng Mục tiêu ↔ Luật: mục tiêu nào không có luật, luật nào phá mục tiêu
```

Câu restate soạn **sau** khi có bản nháp, không soạn trước — để nó nhắm vào giả định đã thật sự vào spec. Mọi ý AI trả lời "Sai" là một chỗ phải vá ngay.

### Review & nộp (11:46–11:52, khóa 12:00)

```bash
/spec-review battle/spec.nop.md 20        # Chỉ báo cáo: hai trục kiểm, eval 20 tình huống
/spec-review battle/spec.nop.md 20 sửa    # Thêm "sửa" để skill áp luôn các sửa vào spec
```

Review chạy **hai trục**: hình thức (đủ 10 mục, lint 22 nhóm, S1–S39, ô trống bảng) và **nội dung** (cổng khả thi F, bảng Mục tiêu↔Luật, 12 ca suy biến, 6 kẻ lạm dụng). Eval set gọi **hai** Executor mù độc lập — đa nghĩa đo bằng **bất đồng giữa hai reader**, không bằng việc reader tự khai "không mơ hồ".

## Tấn công (Chiều, 13:00–15:00)

```bash
/attack cheo A.md B.md C.md      # 13:00–13:15: bảng đồng thuận chéo 3 spec, làm MỘT LẦN trước
/attack battle/doi-thu/B.md B    # Soi spec đội B → tối đa 5 test có EV > 0 + hồ sơ finding
# Lặp cho từng đội đối thủ
```

Spec đối thủ tải về được, nên bước **đồng thuận chéo** là nguồn đạn rẻ nhất: nghiệp vụ mà 2 đội có luật và 1 đội im lặng vừa là lỗ hổng vừa có sẵn bằng chứng phạm vi.

Mỗi test phải có **`EV = 2·P(TRÚNG) − P(VÔ HIỆU)` dương** — bỏ một slot là 0 điểm, bắn bừa là −1. Và **gói bằng chứng phạm vi phải trích nguyên văn xong lúc 14:30**, không để tới 16:00.

## Kháng nghị (16:00–17:00)

```bash
/appeal                 # Dán danh sách test CÔNG bị chấm VÔ HIỆU
                        # → battle/appeal.md: ≤3 ca, mỗi ca một text ≤150 từ copy nguyên khối
```

Kháng nghị **chỉ mở cho ca test CÔNG của mình bị chấm VÔ HIỆU**. Điều duy nhất được tranh: tình huống nằm **TRONG phạm vi** specs thật — không tranh đáp án chuẩn, không tranh cách Executor trả lời.

## Diễn tập (bắt buộc trước ngày thi)

```bash
/drill <tên> [tính-năng] [tự-động|thủ-công]   # Chỉ chạy khi gõ lệnh trực tiếp
```

Trọn vòng: sinh specs thật ẩn → `/frame` → `/elicit` ↔ agent `customer` → `/spec-write` → câu 5 restate → `/spec-review` → `/attack` chính spec vừa viết → chấm bằng `executor` + `customer` → `drill/<tên>/ket-qua.md`.

Diễn tập phải **tôn trọng hạn mức thật** (5 câu, không memory, mỗi lần gọi `customer` là phiên độc lập) — phá hạn mức thì kết quả tốt một cách giả và không đo được thứ cần đo.

## Ghi chú

- **Skills tự động**: đọc tri thức từ `knowledge/` và tự gọi agent (`executor`, `customer`) — không gọi agent trực tiếp
- **Executor**: mô phỏng Executor mù bối cảnh để test spec trước khi nộp (`/spec-review`, `/attack`)
- **Customer**: chỉ dùng khi `/drill` diễn tập
- **Model của 3 tác nhân là thông tin bảo mật, BTC không công bố** — nên `/spec-review` luôn gọi reader thứ hai bằng `model: haiku` (giả lập bảo toàn)
