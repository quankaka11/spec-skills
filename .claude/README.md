# Spec Battle Kit — skills & agents cho Claude Code

Bộ công cụ dùng chung của đội cho HBLAB AI Hackathon #02 (12/09/2026). Nằm trong repo để `git pull` là dùng được.

**Sau khi pull: mở phiên Claude Code MỚI** trong thư mục repo — agent tùy chỉnh (`executor`, `customer`) chỉ được nạp lúc khởi động phiên.

## Kiến trúc

| Lớp | Ở đâu | Vai trò |
|---|---|---|
| Tri thức | `knowledge/00…50-*.md` | 9 file chưng cất từ mô tả cuộc thi + 16 PDF (ISTQB, DMN, NASA, Volere, Berry ambiguity, BABOK, PMI, HTSM, Shopify/IBM/Oracle inventory) + ISO/IEC/IEEE 29148 và tài liệu vận hành cổng thanh toán. Skill chỉ dẫn "đọc file X mục Y", không chép lại. |
| Skills | `.claude/skills/<tên>/SKILL.md` | Quy trình theo từng khoảnh khắc ngày thi. Gõ `/<tên>`. |
| Agents | `.claude/agents/executor.md`, `customer.md` | Hai tác nhân giả lập của BTC, chạy trong context riêng nên **mù bối cảnh thật**. |

Hai trục kiểm chất lượng, chạy song song:

| Trục | Kiểm gì | File | Cổng |
|---|---|---|---|
| **Hình thức** | mơ hồ, ô trống, truy vết, biên, số từ | 40, 31, 20 §5 | lint 22 nhóm, S1–S30, cổng chất lượng 1–11 |
| **Nội dung** | hiểu bài toán, khả thi, vận hành, phục vụ mục tiêu, chịu được lạm dụng | **05**, **32** | cổng F (8 kiểm tra), bảng Mục tiêu↔Luật, 12 ca suy biến, cổng chất lượng 12–16 |

Trục nội dung là phần thêm sau diễn tập 08/09: bản spec khi đó đạt **toàn bộ** trục hình thức (0 hit lint Cao, 36/36 ô bảng, 15/15 eval "ĐỦ") mà vẫn có 7 lỗi nội dung mức Cao — 3 trong đó phá thẳng mục tiêu brief nêu. Chi tiết: `knowledge/32` §7.

## Ngày thi — dùng theo giờ

| Giờ | Lệnh | Vào | Ra |
|---|---|---|---|
| 9:00–9:30 | `/frame` | `battle/brief.md` | `battle/mo-hinh-bai-toan.md` (M1 mục tiêu · M2 dòng tiền · M3 dòng tồn · M4 biên hệ thống · M5 lạm dụng · M6 12 ca suy biến); mâu thuẫn nội tại của brief; câu hỏi P0 |
| 9:30–11:00 | `/elicit lượt 1` … `lượt 6`, rồi **`/elicit restate`**; sau mỗi câu trả lời `/elicit nạp` + dán | `brief.md`, `mo-hinh-bai-toan.md` | Khối prompt copy-paste; `battle/log-khach-hang.md` (timestamp, nguyên văn); `battle/rtm.md` (RTM ngược, ⚠) |
| 11:00–11:40 | `/spec-write` | `mo-hinh-bai-toan.md`, `rtm.md`, log | `battle/spec.md` đích ≤2.700 từ, mọi BR truy vết `← A-xx` + nhãn mục tiêu; xếp hạng rủi ro giả định; RTM điền Mã BR |
| 11:40–11:44 | `/frame muc-tieu-luat` | `spec.md`, `mo-hinh-bai-toan.md` | Bảng Mục tiêu↔Luật: mục tiêu không có luật, luật phá mục tiêu, BR cắt được |
| 11:45–11:55 | `/spec-review battle/spec.md 20 sửa` | `spec.md`, `rtm.md`, mô hình | `battle/review.md`: NỘP ĐƯỢC/CHƯA, khối khả thi (cổng F, lạm dụng, suy biến), eval set qua **hai** `executor` mù, danh sách `SỬA` và `HỎI` |
| 13:00–14:30 | `/attack battle/doi-thu/B.md B` (×3 đội) | spec đối thủ, `rtm.md` (⚠ = đạn), mô hình | `battle/tests/B.md`: 5 test + 2 dự phòng + hồ sơ finding, điểm phạm vi, dry-run executor |
| 16:00–17:00 | `/appeal` + dán kết quả bất lợi | `spec.md`, log, tests | `battle/appeal.md`: 3 ca, trích dẫn nguyên văn, kịch bản 60 giây |
| 10–11/09 | `/drill <tên> [tính-năng] [lượt] [tự-động]` | — | `drill/<tên>/` trọn vòng + `ket-qua.md` (6 chỉ số) |

Chuẩn bị `battle/`: tạo thư mục, dán đề bài vào `battle/brief.md`. Các file còn lại skill tự tạo.

## Năm quy tắc cứng của kit

1. **Hiểu bài toán trước khi hỏi tham số.** `/frame` chạy trước `/elicit lượt 1`; `/spec-write` từ chối chạy khi chưa có `mo-hinh-bai-toan.md`. Mục tiêu và ràng buộc quyết định *luật nào cần tồn tại*; tham số chỉ điền số vào luật đã biết là cần.
2. **Không bịa dữ kiện nghiệp vụ.** Mọi luật trong spec phải trỏ về một dòng RTM (`← A-07`) hoặc gắn `[GIẢ ĐỊNH]`. Không có nguồn thì để luật bao quát §0.5 chặn.
3. **Không hứa hộ bên ngoài.** Mốc "hoàn tất" của việc do cổng thanh toán / ngân hàng / ERP thực hiện phải tách khỏi mốc "hệ thống quyết định", và phải có nhánh thất bại (`knowledge/32` §3.1).
4. **Executor luôn được gọi mù, và luôn gọi hai lần.** Prompt chỉ có đường dẫn spec + tình huống. Bằng chứng spec rõ là **hai reader trùng kết quả**, không phải reader tự khai "không mơ hồ".
5. **Ép kết quả cụ thể.** Mọi câu hỏi AI Khách hàng và mọi test đều phải trả lời được bằng con số / trạng thái cuối / ai thắng / có hoàn tiền không.

**Và một quy tắc về ranh giới sửa:** phát hiện chia hai loại. `SỬA` = viết lại được ngay (mơ hồ, ô trống, thiếu nhánh lỗi). `HỎI` = đội đang không biết specs thật quy định gì (luật phá mục tiêu, hạn mức neo sai, giả định rủi ro cao) — sửa hộ là đoán lần thứ hai trên cùng một chỗ mù. Skill không tự sửa mục `HỎI`; nó sinh câu hỏi.

## Việc phải làm sau họp BTC 09/09

Điền các ô "CHỜ 09/09" trong `knowledge/00-luat-choi.md` §A (TOKEN_MAX, công thức điểm, định dạng nộp, được dùng AI riêng không…). Skill đọc bảng này; ô trống thì skill dùng giả định và ghi rõ. Các ô này ảnh hưởng *ngân sách và cách nộp*, không ảnh hưởng chất lượng spec — trục nội dung (`knowledge/05`, `32`) chạy được bất kể chúng còn trống.

Nếu BTC **không cho dùng AI trong phòng thi**: kit vẫn dùng để diễn tập và để in artifact mang vào — sáu khối mô hình `knowledge/05` §1 + 12 ca suy biến §M6, cổng F `knowledge/32` §1 + bảng thực tế phụ thuộc ngoài §2 + ba mẫu viết lại §3, question bank `knowledge/20` §2–§4 (gồm nhóm N0), template `knowledge/30` §1–§2 + checklist 24 quy tắc §4, checklist tấn công `knowledge/50` §1, §4, §6, danh sách đen `knowledge/40` §2.

## Tùy chỉnh

- Executor của BTC dùng model gì chưa rõ (câu 7 họp 09/09). Muốn giả lập Executor "kém" hơn để dry-run khắt khe: đổi `model: inherit` → `model: haiku` trong `.claude/agents/executor.md`.
- `drill/selftest/` là bộ fixture sẵn (specs thật giả lập có 8+ luật phản trực giác, spec lỗi có ≥22 lỗi cài) — dùng lại để luyện /attack và /spec-review.
