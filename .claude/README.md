# Spec Battle Kit — skills & agents cho Claude Code

Bộ công cụ dùng chung của đội cho HBLAB AI Hackathon #02 (12/09/2026). Nằm trong repo để `git pull` là dùng được.

**Sau khi pull: mở phiên Claude Code MỚI** trong thư mục repo — agent tùy chỉnh (`executor`, `customer`) và skill mới (`/frame`) chỉ được nạp lúc khởi động phiên.

## Tham số hiện hành (chốt họp BTC 09/09 + tài liệu BTC 11/09)

| Tham số | Giá trị |
|---|---|
| Hỏi AI Khách hàng | **5 câu cho cả ngày · mỗi lượt đúng 1 câu · 5.000 token (hỏi + trả lời) · KHÔNG có memory** |
| Hạn mức spec | **≤ 6.000 token, đích 5.400** (không phải 3.000 từ) · khóa 12:00 |
| Cấu trúc spec nộp | **10 mục** kiểu thiết kế Nhật (tài liệu 11/09) |
| Điểm | CÔNG trúng **+2** · THỦ đỡ được **+1** · CÔNG bị VÔ HIỆU **−1** |
| Kháng nghị | **chỉ ca test CÔNG của mình bị chấm VÔ HIỆU**, nộp bằng text gửi AI, ≤3 ca |
| Spec đối thủ | Markdown, **tải về được** → dùng được bảng đồng thuận chéo |

Bảng đầy đủ + cách đo token + 9 ô còn hở phải hỏi BTC đầu ngày: `knowledge/00-luat-choi.md` §A, §A1, §A2.

## Kiến trúc

| Lớp | Ở đâu | Vai trò |
|---|---|---|
| Tri thức | `knowledge/00…50-*.md` | 10 file chưng cất từ mô tả cuộc thi + tài liệu BTC + 16 PDF (ISTQB, DMN, NASA, Volere, Berry ambiguity, BABOK, ISO 29148, Shopify/IBM/Oracle/Stripe). Skill chỉ dẫn "đọc file X mục Y", không chép lại. **`32-cau-truc-spec-btc.md` = cấu trúc spec 10 mục BTC bắt buộc** — đọc trước `30`. **`05-hieu-bai-toan.md` + `33-kha-thi-van-hanh.md` = trục nội dung** (xem mục dưới). |
| Skills | `.claude/skills/<tên>/SKILL.md` | Quy trình theo từng khoảnh khắc ngày thi. Gõ `/<tên>`. |
| Agents | `.claude/agents/executor.md`, `customer.md` | Hai tác nhân giả lập của BTC, chạy trong context riêng nên **mù bối cảnh thật**. |

## Ngày thi — dùng theo giờ

| Giờ | Lệnh | Vào | Ra |
|---|---|---|---|
| 9:00–9:30 | `/frame` | `battle/brief.md` | `battle/mo-hinh-bai-toan.md` đủ M1–M6; mâu thuẫn nội tại của brief; ba nhóm ô `?` (vào 5 câu / hàng đợi C5 / tự điền); 2 câu hỏi miệng cho BTC |
| 9:30 | `/elicit ke-hoach` | mô hình, brief | `battle/ke-hoach-hoi.md`: 4 câu đã gọt + bảng phủ 10 mục BTC + danh sách ô sẽ KHÔNG hỏi |
| 9:30–10:20 | `/elicit cau 1` … `cau 4`, sau mỗi câu `/elicit nap` + dán | ke-hoach | `battle/log-khach-hang.md` (sổ hạn mức, timestamp, nguyên văn); `battle/rtm.md` (dòng `A-xx`) |
| 10:20–11:20 | `/spec-write` | mô hình, `rtm.md`, log | `battle/spec.md` + `battle/spec.nop.md` **đủ 10 mục BTC**, ≤5.400 token, mọi BR truy vết `← A-xx`/`← G-xx`; **bảng xếp hạng rủi ro giả định → C5** |
| 11:20–11:30 | `/elicit restate` (= câu 5) | bảng xếp hạng rủi ro | 8–10 phát biểu Đúng/Sai; danh sách ý "Sai" phải vá |
| 11:30–11:46 | vá spec + `/frame muc-tieu-luat` | spec, mô hình | Bảng Mục tiêu↔Luật; 0 ✗ ở cổng F F1/F5 |
| 11:46–11:52 | `/spec-review battle/spec.nop.md 20 sửa` | spec, `rtm.md` | `battle/review.md`: NỘP ĐƯỢC/CHƯA, `SỬA`/`HỎI`/`RỦI RO ĐÃ BIẾT`, eval set **hai reader mù** |
| 13:00–13:15 | `/attack cheo A.md B.md C.md` | 3 spec đối thủ tải về | `battle/dong-thuan-cheo.md`: nghiệp vụ nào 2 đội có luật mà 1 đội im lặng (#24) |
| 13:15–14:30 | `/attack battle/doi-thu/B.md B` (×3 đội) | spec đối thủ, `rtm.md` (⚠ = đạn) | `battle/tests/B.md`: tối đa 5 test **có `EV > 0`** + hồ sơ finding + **gói bằng chứng phạm vi 3 mức** (thu xong 14:30) |
| 16:00–17:00 | `/appeal` + dán danh sách VÔ HIỆU | `spec.nop.md`, log, tests | `battle/appeal.md`: ≤3 ca, mỗi ca một text ≤150 từ copy nguyên khối |
| trước ngày thi | `/drill <tên> [tính-năng] [tự-động]` | — | `drill/<tên>/` trọn vòng + `ket-qua.md` (điểm quy đổi, phủ 10 mục, 8 chỉ số) |

Chuẩn bị `battle/`: tạo thư mục, dán đề bài vào `battle/brief.md`. Các file còn lại skill tự tạo.

## Cấu trúc spec BTC yêu cầu (tài liệu 11/09)

Spec nộp phải theo **10 mục** kiểu thiết kế Nhật — Basic design 基本設計 (mục 1–5, theo màn hình) · Detailed design 詳細設計 (mục 6–9, theo chức năng) · Technical spec 技術仕様 (mục 10):

`1` Tổng quan & phạm vi · `2` Item trên màn hình · `3` Event · `4` Validation & message lỗi · `5` Design/Wireframe · `6` Flow nghiệp vụ & quy tắc xử lý · `7` Ràng buộc, ca bất thường liên logic, điều chưa chốt · `8` Xác thực & phân quyền · `9` Luồng dữ liệu & API · `10` Data model, performance, security

Chi tiết nội dung + hình thức bảng + ngân sách token từng mục: `knowledge/32-cau-truc-spec-btc.md`. Ba điểm dễ mất điểm nhất: **mục 4 phải ghi message lỗi nguyên văn**, **mục 2 và 8 phải phân biệt login vs guest**, **mục 6 mỗi logic phải có case bình thường + biên + lỗi kèm 5 gạch** (số · toán tử `>`/`≥` · múi giờ · default khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng).

## Hai trục kiểm chất lượng

Kit kiểm spec theo hai trục **độc lập** — đạt hết trục này vẫn có thể sai trục kia:

| Trục | File | Kiểm gì |
|---|---|---|
| **Hình thức & cấu trúc** | `40` (lint 22 nhóm, S1–S30), `31` (bảng quyết định), `32` (10 mục), `30` §7 dòng 1–18 | Mơ hồ, ô trống, đủ mục, truy vết, đếm token |
| **Nội dung** | **`05-hieu-bai-toan.md`** (mô hình bài toán, bảng Mục tiêu↔Luật, 12 ca suy biến, 6 kẻ lạm dụng), **`33-kha-thi-van-hanh.md`** (cổng F, bảng "KHÔNG được hứa"), `40` §2 nhóm 17–22 + S31–S39, `30` §4 quy tắc 18–24, `30` §7 dòng 19–23 | Luật có khả thi không · có cưỡng chế được không · có phục vụ mục tiêu brief không · ai chịu chi phí |

Vì sao cần trục thứ hai: ở diễn tập 08/09, một spec đạt **toàn bộ** trục hình thức (0 hit lint mức Cao, 36/36 ô bảng trạng thái, 15/15 eval "ĐỦ", 2.991/3.000 từ) mà vẫn có **7 lỗi nội dung mức Cao**, 3 trong đó phá thẳng mục tiêu của brief — hạn mức chống bot neo vào email guest tự khai, khóa tồn trước khi thu tiền, và hứa "hoàn tiền hoàn tất ≤24 giờ" cho việc do ngân hàng làm.

## Bảy quy tắc cứng của kit

1. **Hiểu bài toán trước khi hỏi tham số.** Không gõ câu hỏi nào cho AI Khách hàng trước khi `mo-hinh-bai-toan.md` có chữ ở cả 6 khối (`/frame`).
2. **Không bịa, nhưng phải điền mọi ô.** Mọi luật trỏ về một dòng RTM: `← A-07` (có câu trả lời) hoặc `← G-12` (giả định). Giả định lấy từ **mặc định ngành** (`knowledge/20` §4, `10` §6), **không sáng tạo giá trị mới** — đó là ca duy nhất tệ hơn im lặng.
3. **Không hứa hộ bên ngoài.** Mốc "hoàn tất" của việc do cổng/ngân hàng/ERP thực hiện phải tách khỏi mốc "quyết định" của hệ thống, và phải có nhánh thất bại.
4. **Executor luôn được gọi mù, và gọi hai lần.** Prompt chỉ có đường dẫn spec + tình huống. Reader 2 dùng `model: haiku` — đa nghĩa đo bằng **bất đồng giữa hai reader**, không bằng việc reader tự khai "không mơ hồ".
5. **Ép kết quả cụ thể.** Mọi câu hỏi và mọi test phải trả lời được bằng con số / trạng thái cuối / ai thắng / có hoàn tiền không / message nguyên văn / trạng thái nút.
6. **Một lượt = một câu, và chỉ có 5 câu.** Mỗi câu tự chứa (AI không có memory), có ép format + cap, và xin về **một bảng** chứ không một dòng.
7. **Nộp test theo kỳ vọng, không theo số slot.** `EV = 2·P(TRÚNG) − P(VÔ HIỆU)`; EV ≤ 0 thì bỏ slot — bỏ là 0 điểm, bắn bừa là −1.

## Ranh giới sửa: `SỬA` vs `HỎI`

`/spec-review` chia phát hiện làm ba loại. `SỬA` = sửa được bằng viết lại (mơ hồ, ô trống, thiếu nhánh lỗi, giá trị ngược mặc định ngành). `HỎI` = chỗ đội đang **không biết specs thật quy định gì** — sửa hộ ở đây là **đoán lần thứ hai trên cùng một chỗ mù**, nên nó thành phát biểu Đúng/Sai cho câu C5, cắt theo số câu hỏi còn lại. `RỦI RO ĐÃ BIẾT` = hết câu hỏi rồi: ghi vào `review.md` và tự bắn trước khi đối thủ bắn.

## Còn hở sau họp 09/09

`knowledge/00-luat-choi.md` §A2 giữ 9 ô chưa có đáp án. **Hai ô đổi kế hoạch buổi sáng, hỏi miệng trước 9:30:** (1) ảnh có tính vào 5.000 token không — nếu KHÔNG thì ảnh thành phương tiện restate rẻ nhất; (2) AI Khách hàng còn mở sau 12:00 không — nếu còn thì giữ 1 câu cho 13:30 để xin danh sách NGOÀI phạm vi mở rộng. **Hai ô đổi cách viết mục 5, hỏi trước 10:20:** Mermaid `block-beta` có được nhận thay Figma không; sơ đồ có tính token như văn bản không. Các ô còn lại chỉ đổi cách tính điểm kỳ vọng.

Nếu BTC **không cho dùng AI trong phòng thi**: kit vẫn dùng để diễn tập và để in artifact mang vào — 5 câu hỏi soạn sẵn (`knowledge/20` §3), bảng "phần phải tự điền" + 31 phát biểu mặc định ngành (`20` §3 cuối, §4), template 10 mục + catch-all 0.1–0.15 (`30` §1, §2), ngân sách token (`32` §5), cổng F + bảng "KHÔNG được hứa" (`33` §1, §2), 12 ca suy biến + 6 kẻ lạm dụng (`05` §M5, §M6), 24 loại lỗ hổng + rubric phạm vi (`50` §1, §6), danh sách đen 22 nhóm (`40` §2).

## Tùy chỉnh

- **Model của 3 tác nhân là thông tin bảo mật, BTC không công bố** (`00` §H câu 7). Hệ quả: `/spec-review` khối D **luôn** gọi reader thứ hai bằng `model: haiku` — giả lập bảo toàn là lựa chọn duy nhất đúng khi không biết Executor thật mạnh cỡ nào.
- Fixture luyện tập: chạy `/drill <tên>` để sinh mới theo cấu trúc 10 mục. (Fixture self-test 05/09 đã bỏ — dựng theo template 11 mục cũ, không còn dùng được.)
