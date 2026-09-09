# Spec Battle Toolkit

Bộ công cụ Claude Code cho đội thi **HBLAB AI Hackathon #02 — Spec Battle** (12/09/2026): tương tác với AI Khách hàng, viết & review spec, tấn công đội khác, appeal. Chi tiết kiến trúc skill/agent xem [`.claude/README.md`](.claude/README.md); tiến độ chuẩn bị xem [`PROGRESS.md`](PROGRESS.md).

**Sau khi pull: mở phiên Claude Code MỚI** trong thư mục repo — agent tùy chỉnh (`executor`, `customer`) chỉ được nạp lúc khởi động phiên.

## Cấu trúc thư mục

| Đường dẫn | Nội dung |
|---|---|
| `knowledge/00…50-*.md` | 9 file tri thức chưng cất (luật chơi, **hiểu bài toán**, domain, ngân hàng câu hỏi, viết spec, bảng quyết định, **khả thi & vận hành**, từ mơ hồ, tấn công) — skill đọc trực tiếp, không chép lại |
| `.claude/skills/<tên>/SKILL.md` | 7 skill: `frame`, `elicit`, `spec-write`, `spec-review`, `attack`, `appeal`, `drill` |
| `.claude/agents/{executor,customer}.md` | 2 agent giả lập, mù bối cảnh thật |
| `data/` | 16 PDF nguồn (BABOK, ISTQB, NASA SE Handbook, Volere, DMN, Shopify/IBM/Oracle inventory…) |
| `hackathon_descriptions.md` | Tài liệu nền hợp nhất về cuộc thi (mô tả, luật, 9 phần + phụ lục) |
| `battle/` | Tạo khi vào thi thật: brief, log, RTM, spec, test — skill tự sinh các file còn lại |
| `drill/` | Fixture & kết quả diễn tập trước ngày thi |

## Chuẩn bị

1. Pull repo mới nhất
2. Mở Claude Code trong thư mục repo (phiên mới)
3. Tạo file `battle/brief.md` và dán đề bài

## Quy trình thi (Sáng, 9:00–12:00)

### Hiểu bài toán trước (9:00–9:30) — bắt buộc

```bash
/frame                  # brief.md → battle/mo-hinh-bai-toan.md
                        # mục tiêu & thước đo · dòng tiền · dòng tồn & nguồn chân lý
                        # biên hệ thống 6 láng giềng · mô hình lạm dụng · 12 kịch bản suy biến
                        # + mâu thuẫn nội tại của brief + câu hỏi P0 cho lượt 1
```

`/spec-write` **từ chối chạy** khi chưa có file này. Lý do: bản diễn tập 08/09 viết mà không có mô hình bài toán, đạt mọi cổng hình thức mà vẫn có 7 lỗi nội dung mức Cao — 3 trong đó phá thẳng mục tiêu brief nêu (`knowledge/32` §7).

### 6 lượt hỏi-đáp với AI Khách hàng + restate

```bash
/elicit lượt 1          # Sinh khối câu hỏi để copy — mục tiêu & ràng buộc hỏi TRƯỚC tham số
                        # Dán cho AI Khách hàng, copy câu trả lời
/elicit nạp             # Dán câu trả lời — cập nhật RTM ngược + log có timestamp
# Lặp đến lượt 6
/elicit restate         # BẮT BUỘC — xác nhận mọi giả định rủi ro cao; giữ 15% token cho lượt này
```

### Viết spec

```bash
/spec-write             # battle/spec.md, đích ≤2.700 từ, từ mô hình + RTM + log
                        # mỗi BR gắn nhãn mục tiêu; chạy cổng khả thi F trước khi đếm từ
/frame muc-tieu-luat    # 11:40 — bảng Mục tiêu ↔ Luật: mục tiêu nào chưa có luật, luật nào phá mục tiêu
```

### Review & Submit (11:45–11:55, khóa 12:00)

```bash
/spec-review battle/spec.md 20        # Chỉ báo cáo: cổng F + lạm dụng + suy biến;
                                      # eval 20 tình huống qua HAI executor mù (đa nghĩa = hai reader lệch nhau)
/spec-review battle/spec.md 20 sửa    # Thêm "sửa" để áp các mục loại SỬA vào spec.md
                                      # Mục loại HỎI không tự sửa — xuất ra khối câu hỏi cho /elicit restate
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

- **Hai trục kiểm chất lượng.** *Hình thức* (mơ hồ, ô trống, truy vết, số từ) ở `knowledge/40`, `31`. *Nội dung* (hiểu bài toán, khả thi, vận hành, phục vụ mục tiêu, chịu được lạm dụng) ở `knowledge/05`, `32`. Trục nội dung mới thêm 09/09 sau khi đo được rằng một spec có thể đạt 100% trục hình thức mà vẫn sai bài toán. Xem `.claude/README.md` mục Kiến trúc.
- **Ranh giới sửa**: phát hiện loại `SỬA` skill áp được ngay; loại `HỎI` (luật phá mục tiêu, hạn mức neo sai, giả định rủi ro cao) skill **không** tự sửa — nó sinh câu hỏi cho AI Khách hàng, vì sửa hộ là đoán lần thứ hai trên cùng một chỗ mù.
- **Skills tự động**: đọc tri thức từ `knowledge/` và tự gọi agent (`executor`, `customer`) — không gọi agent trực tiếp
- **Executor**: mô phỏng Executor mù bối cảnh để test spec trước khi nộp (`/spec-review` gọi hai reader độc lập, một trong đó dùng model yếu hơn; `/attack` dry-run)
- **Customer**: chỉ dùng khi `/drill` diễn tập
- Muốn giả lập Executor "kém" hơn: đổi `model: inherit` → model khác trong `.claude/agents/executor.md`
