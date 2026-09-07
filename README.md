# Spec Battle Toolkit

Bộ công cụ Claude Code cho đội thi **HBLAB AI Hackathon #02 — Spec Battle** (12/09/2026): tương tác với AI Khách hàng, viết & review spec, tấn công đội khác, appeal. Chi tiết kiến trúc skill/agent xem [`.claude/README.md`](.claude/README.md); tiến độ chuẩn bị xem [`PROGRESS.md`](PROGRESS.md).

**Sau khi pull: mở phiên Claude Code MỚI** trong thư mục repo — agent tùy chỉnh (`executor`, `customer`) chỉ được nạp lúc khởi động phiên.

## Cấu trúc thư mục

| Đường dẫn | Nội dung |
|---|---|
| `knowledge/00…50-*.md` | 7 file tri thức chưng cất (luật chơi, domain, ngân hàng câu hỏi, viết spec, từ mơ hồ, tấn công) — skill đọc trực tiếp, không chép lại |
| `.claude/skills/<tên>/SKILL.md` | 6 skill: `elicit`, `spec-write`, `spec-review`, `attack`, `appeal`, `drill` |
| `.claude/agents/{executor,customer}.md` | 2 agent giả lập, mù bối cảnh thật |
| `data/` | 16 PDF nguồn (BABOK, ISTQB, NASA SE Handbook, Volere, DMN, Shopify/IBM/Oracle inventory…) |
| `hackathon_descriptions.md` | Tài liệu nền hợp nhất về cuộc thi (mô tả, luật, 9 phần + phụ lục) |
| `battle/` | Tạo khi vào thi thật: brief, log, RTM, spec, test — skill tự sinh các file còn lại |
| `drill/` | Fixture & kết quả diễn tập trước ngày thi |

## Chuẩn bị

1. Pull repo mới nhất
2. Mở Claude Code trong thư mục repo (phiên mới)
3. Tạo file `battle/brief.md` và dán đề bài

## Quy trình thi (Sáng, 9:30–12:00)

### 6 lượt hỏi-đáp với AI Khách hàng

```bash
/elicit lượt 1          # Sinh khối câu hỏi để copy
                        # Dán cho AI Khách hàng, copy câu trả lời
/elicit nạp             # Dán câu trả lời — cập nhật RTM ngược + log có timestamp
# Lặp đến lượt 6
```

### Viết spec

```bash
/spec-write             # Viết battle/spec.md ≤3.000 từ từ RTM + log (mặc định thư mục battle/)
```

### Review & Submit (11:45–11:55, khóa 12:00)

```bash
/spec-review battle/spec.md 20        # Chỉ báo cáo: eval 20 tình huống, liệt kê finding theo mức
/spec-review battle/spec.md 20 sửa    # Thêm "sửa" để skill áp luôn các sửa vào spec.md
```

## Tấn công (Chiều, 13:00–15:00)

```bash
/attack battle/doi-thu/B.md B    # Soi spec đội B, sinh 5 test (+2 dự phòng) + hồ sơ finding
# Lặp cho từng đội đối thủ
```

## Kháng nghị (16:00–17:00)

```bash
/appeal                 # Dán kèm danh sách kết quả bất lợi (tình huống, TRÚNG/VÔ HIỆU, lý do đối chiếu)
                        # → battle/appeal.md: tối đa 3 ca, trích dẫn nguyên văn, kịch bản nói 60 giây
```

## Diễn tập (bắt buộc 10–11/09, trước ngày thi)

```bash
/drill <tên> [tính-năng] [lượt=4] [tự-động|thủ-công]   # Chỉ chạy khi gõ lệnh trực tiếp
```

Trọn vòng: sinh specs thật ẩn → `/elicit` ↔ agent `customer` → `/spec-write` → `/spec-review` → `/attack` chính spec vừa viết → chấm bằng `executor` + `customer` → tổng kết tỷ lệ TRÚNG và lỗ hổng elicitation/viết vào `drill/<tên>/ket-qua.md`.

## Cấu hình (sau họp BTC 09/09)

Điền các ô `CHỜ 09/09` trong [`knowledge/00-luat-choi.md`](knowledge/00-luat-choi.md) §A (token, công thức điểm, định dạng nộp…) — skill tự đọc tham số từ đó, ô trống thì dùng giả định và ghi rõ. Xem §H cùng file để biết danh sách 14 câu cần hỏi BTC.

## Ghi chú

- **Skills tự động**: đọc tri thức từ `knowledge/` và tự gọi agent (`executor`, `customer`) — không gọi agent trực tiếp
- **Executor**: mô phỏng Executor mù bối cảnh để test spec trước khi nộp (`/spec-review`, `/attack`)
- **Customer**: chỉ dùng khi `/drill` diễn tập
- Muốn giả lập Executor "kém" hơn: đổi `model: inherit` → model khác trong `.claude/agents/executor.md`
