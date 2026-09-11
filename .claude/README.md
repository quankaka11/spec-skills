# Spec Battle Kit — skills & agents cho Claude Code

Bộ công cụ dùng chung của đội cho HBLAB AI Hackathon #02 (12/09/2026). Nằm trong repo để `git pull` là dùng được.

**Sau khi pull: mở phiên Claude Code MỚI** trong thư mục repo — agent tùy chỉnh (`executor`, `customer`) chỉ được nạp lúc khởi động phiên.

## Kiến trúc

| Lớp | Ở đâu | Vai trò |
|---|---|---|
| Tri thức | `knowledge/00…50-*.md` | 8 file chưng cất từ mô tả cuộc thi + tài liệu BTC + 16 PDF (ISTQB, DMN, NASA, Volere, Berry ambiguity, BABOK, Shopify/IBM/Oracle inventory). Skill chỉ dẫn "đọc file X mục Y", không chép lại. **`32-cau-truc-spec-btc.md` = cấu trúc spec 10 mục BTC bắt buộc (11/09)** — đọc trước `30`. |
| Skills | `.claude/skills/<tên>/SKILL.md` | Quy trình theo từng khoảnh khắc ngày thi. Gõ `/<tên>`. |
| Agents | `.claude/agents/executor.md`, `customer.md` | Hai tác nhân giả lập của BTC, chạy trong context riêng nên **mù bối cảnh thật**. |

## Ngày thi — dùng theo giờ

| Giờ | Lệnh | Vào | Ra |
|---|---|---|---|
| 9:30–11:00 | `/elicit lượt 1` … `lượt 7` (L7 = màn hình/event/validation/API), sau mỗi câu trả lời `/elicit nạp` + dán | `battle/brief.md` | Khối prompt copy-paste; `battle/log-khach-hang.md` (timestamp, nguyên văn); `battle/rtm.md` (RTM ngược, ⚠) |
| 11:00–11:45 | `/spec-write` | `rtm.md`, log | `battle/spec.md` **đủ 10 mục BTC**, ≤3.000 từ, mọi BR truy vết `← A-xx`; RTM điền Mã BR |
| 11:45–11:55 | `/spec-review battle/spec.md 20 sửa` | `spec.md`, `rtm.md` | `battle/review.md`: NỘP ĐƯỢC/CHƯA, lỗ hổng xếp mức, eval set qua `executor` mù |
| 13:00–14:30 | `/attack battle/doi-thu/B.md B` (×3 đội) | spec đối thủ, `rtm.md` (⚠ = đạn) | `battle/tests/B.md`: 5 test + 2 dự phòng + hồ sơ finding, điểm phạm vi, dry-run executor |
| 16:00–17:00 | `/appeal` + dán kết quả bất lợi | `spec.md`, log, tests | `battle/appeal.md`: 3 ca, trích dẫn nguyên văn, kịch bản 60 giây |
| 10–11/09 | `/drill <tên> [tính-năng] [lượt] [tự-động]` | — | `drill/<tên>/` trọn vòng + `ket-qua.md` (tỷ lệ TRÚNG, lỗ hổng hỏi/viết) |

Chuẩn bị `battle/`: tạo thư mục, dán đề bài vào `battle/brief.md`. Các file còn lại skill tự tạo.

## Cấu trúc spec BTC yêu cầu (cập nhật 11/09)

Spec nộp phải theo **10 mục** kiểu thiết kế Nhật — Basic design 基本設計 (mục 1–5, theo màn hình) · Detailed design 詳細設計 (mục 6–9, theo chức năng) · Technical spec 技術仕様 (mục 10):

`1` Tổng quan & phạm vi · `2` Item trên màn hình · `3` Event · `4` Validation & message lỗi · `5` Design/Wireframe · `6` Flow nghiệp vụ & quy tắc xử lý · `7` Ràng buộc, ca bất thường liên logic, điều chưa chốt · `8` Xác thực & phân quyền · `9` Luồng dữ liệu & API · `10` Data model, performance, security

Chi tiết nội dung + hình thức bảng từng mục: `knowledge/32-cau-truc-spec-btc.md`. Ba điểm dễ mất điểm nhất: **mục 4 phải ghi message lỗi nguyên văn**, **mục 2 và 8 phải phân biệt login vs guest**, **mục 6 mỗi logic phải có case bình thường + biên + lỗi kèm 5 gạch** (số · toán tử `>`/`≥` · múi giờ · default khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng).

## Ba quy tắc cứng của kit

1. **Không bịa dữ kiện nghiệp vụ.** Mọi luật trong spec phải trỏ về một dòng RTM (`← A-07`) hoặc gắn `[GIẢ ĐỊNH]`. Không có nguồn thì để luật bao quát §0.5 chặn.
2. **Executor luôn được gọi mù.** Prompt gửi `executor` chỉ có đường dẫn spec + tình huống. Không RTM, không brief, không đáp án.
3. **Ép kết quả cụ thể.** Mọi câu hỏi AI Khách hàng và mọi test đều phải trả lời được bằng con số / trạng thái cuối / ai thắng / có hoàn tiền không.

## Việc phải làm sau họp BTC 09/09

Điền các ô "CHỜ 09/09" trong `knowledge/00-luat-choi.md` §A (TOKEN_MAX, công thức điểm, định dạng nộp, được dùng AI riêng không…). Skill đọc bảng này; ô trống thì skill dùng giả định và ghi rõ.

Nếu BTC **không cho dùng AI trong phòng thi**: kit vẫn dùng để diễn tập và để in artifact mang vào (question bank `knowledge/20` §2–§4, template `knowledge/30` §1–§2, checklist tấn công `knowledge/50` §1, §4, §6, danh sách đen `knowledge/40` §2).

## Tùy chỉnh

- Executor của BTC dùng model gì chưa rõ (câu 7 họp 09/09). Muốn giả lập Executor "kém" hơn để dry-run khắt khe: đổi `model: inherit` → `model: haiku` trong `.claude/agents/executor.md`.
- Fixture luyện tập: chạy `/drill <tên>` để sinh mới theo cấu trúc 10 mục. (Fixture self-test 05/09 đã bỏ — dựng theo template 11 mục cũ, không còn dùng được.)
