# Spec Battle Kit — skills & agents cho Claude Code

Bộ công cụ dùng chung của đội cho HBLAB AI Hackathon #02 (12/09/2026). Nằm trong repo để `git pull` là dùng được.

**Tham số hiện hành (chốt họp BTC 09/09):** nộp markdown ≤ **6.000 token** (đích 5.400), sơ đồ mermaid, không ảnh · hỏi AI Khách hàng **5 câu, mỗi lượt 1 câu, 5.000 token, không memory**, ảnh ≤3 lần · điểm **+2 công / +1 thủ / −1 vô hiệu** · spec đối thủ tải về được · test không sửa sau khi nộp · **kháng nghị chỉ cho ca CÔNG bị VÔ HIỆU**. Bảng đầy đủ và 7 ô còn hở: `knowledge/00` §A.

**Sau khi pull: mở phiên Claude Code MỚI** trong thư mục repo — agent tùy chỉnh (`executor`, `customer`) chỉ được nạp lúc khởi động phiên.

## Kiến trúc

| Lớp | Ở đâu | Vai trò |
|---|---|---|
| Tri thức | `knowledge/00…50-*.md` | **10 file** chưng cất từ mô tả cuộc thi + tài liệu BTC + 16 PDF (ISTQB, DMN, NASA, Volere, Berry ambiguity, BABOK, PMI, HTSM, Shopify/IBM/Oracle inventory) + ISO/IEC/IEEE 29148 và tài liệu vận hành cổng thanh toán. PDF nguồn đã bỏ khỏi repo sau khi chưng cất (xem PROGRESS 11/09). Skill chỉ dẫn "đọc file X mục Y", không chép lại. **`33-cau-truc-spec-btc.md` = cấu trúc spec 10 mục BTC bắt buộc (11/09)** — đọc trước `30`. |
| Skills | `.claude/skills/<tên>/SKILL.md` | Quy trình theo từng khoảnh khắc ngày thi. Gõ `/<tên>`. |
| Agents | `.claude/agents/executor.md`, `customer.md` | Hai tác nhân giả lập của BTC, chạy trong context riêng nên **mù bối cảnh thật**. |

Hai trục kiểm chất lượng, chạy song song:

| Trục | Kiểm gì | File | Cổng |
|---|---|---|---|
| **Hình thức** | mơ hồ, ô trống, truy vết, biên, token | 40, 31, 20 §5 | lint 22 nhóm, S1–S39, cổng chất lượng 1–17 |
| **Nội dung** | hiểu bài toán, khả thi, vận hành, phục vụ mục tiêu, chịu được lạm dụng | **05**, **32** | cổng F (8 kiểm tra), bảng Mục tiêu↔Luật, 12 ca suy biến, cổng chất lượng 18–23 |
| **Cấu trúc BTC** | đủ 10 mục, message nguyên văn, guest, bảng Case đủ 3 loại + 5 gạch | **33**, 40 §3 | B0 của `/spec-review`, S31–S39, cổng chất lượng 1–12 |

Trục nội dung là phần thêm sau diễn tập 08/09: bản spec khi đó đạt **toàn bộ** trục hình thức (0 hit lint Cao, 36/36 ô bảng, 15/15 eval "ĐỦ") mà vẫn có 7 lỗi nội dung mức Cao — 3 trong đó phá thẳng mục tiêu brief nêu. Chi tiết: `knowledge/32` §7.

## Ngày thi — dùng theo giờ

| Giờ | Lệnh | Vào | Ra |
|---|---|---|---|
| 9:00–9:30 | `/frame` | `battle/brief.md` | `battle/mo-hinh-bai-toan.md` (M1 mục tiêu · M2 dòng tiền · M3 dòng tồn · M4 biên hệ thống · M5 lạm dụng · M6 12 ca suy biến); mâu thuẫn nội tại của brief; câu hỏi P0 |
| 9:00–9:30 | `/elicit ke-hoach` | `mo-hinh-bai-toan.md` | `ke-hoach-hoi.md`: 4 câu đã gọt + ước token + **danh sách ô sẽ KHÔNG hỏi kèm giá trị mặc định ngành** |
| 9:30–10:20 | `/elicit cau 1` … `cau 4`, mỗi câu kèm `/elicit nap` | `brief.md`, `ke-hoach-hoi.md` | Khối câu hỏi qua cổng 5 kiểm tra; `log-khach-hang.md` (sổ hạn mức, timestamp, nguyên văn); `rtm.md` (dòng `A-xx` + `G-xx`) |
| 10:20–11:05 | `/spec-write` | `mo-hinh-bai-toan.md`, `rtm.md`, log | `battle/spec.md` đích ≤5.400 token, mọi BR có `← A-xx` hoặc `← G-xx` + nhãn mục tiêu; **khối `→ C5`: 10 phát biểu Đúng/Sai** |
| 11:05–11:20 | `/elicit restate` (= câu 5) + `/elicit nap` | `spec.md`, bảng xếp hạng rủi ro | Câu hỏi cuối cùng; mỗi ý "Sai" = một việc sửa BR |
| 11:20–11:38 | `/frame muc-tieu-luat` | `spec.md`, `mo-hinh-bai-toan.md` | Bảng Mục tiêu↔Luật: mục tiêu không có luật, luật phá mục tiêu, BR cắt được |
| 11:38–11:52 | `/spec-review battle/spec.md 20 sửa` | `spec.md`, `rtm.md`, mô hình | `review.md`: NỘP ĐƯỢC/CHƯA, cổng F + lạm dụng + suy biến + **G-7 giá trị tự nghĩ ra**, eval qua **hai** `executor` mù, đếm token, `SỬA` / `HỎI` / `RỦI RO ĐÃ BIẾT` |
| 13:00–13:15 | `/attack cheo A.md B.md C.md` | 3 spec đối thủ (markdown), `spec.nop.md` | `dong-thuan-cheo.md`: hai spec chỏi nhau · một spec im lặng (#21) · cả ba im lặng |
| 13:15–14:30 | `/attack battle/doi-thu/B.md B` (×3 đội) | spec đối thủ, `rtm.md`, `dong-thuan-cheo.md`, brief | `battle/tests/B.md`: **tối đa** 5 test có `EV > 0` + hồ sơ finding + **gói bằng chứng phạm vi 3 mức** |
| 16:00–17:00 | `/appeal` + dán các ca **VÔ HIỆU** | log, brief, tests, 3 spec đối thủ | `battle/appeal.md`: ≤3 ca, text ≤150 từ/ca gửi AI |
| 10–11/09 | `/drill <tên> [tính-năng] [tự-động]` | — | `drill/<tên>/` trọn vòng + `ket-qua.md` (8 chỉ số) |

Chuẩn bị `battle/`: tạo thư mục, dán đề bài vào `battle/brief.md`. Các file còn lại skill tự tạo.

## Cấu trúc spec BTC yêu cầu (cập nhật 11/09)

Spec nộp phải theo **10 mục** kiểu thiết kế Nhật — Basic design 基本設計 (mục 1–5, theo màn hình) · Detailed design 詳細設計 (mục 6–9, theo chức năng) · Technical spec 技術仕様 (mục 10):

`1` Tổng quan & phạm vi · `2` Item trên màn hình · `3` Event · `4` Validation & message lỗi · `5` Design/Wireframe · `6` Flow nghiệp vụ & quy tắc xử lý · `7` Ràng buộc, ca bất thường liên logic, điều chưa chốt · `8` Xác thực & phân quyền · `9` Luồng dữ liệu & API · `10` Data model, performance, security

Chi tiết nội dung + hình thức bảng từng mục: `knowledge/33-cau-truc-spec-btc.md`. Ba điểm dễ mất điểm nhất: **mục 4 phải ghi message lỗi nguyên văn**, **mục 2 và 8 phải phân biệt login vs guest**, **mục 6 mỗi logic phải có case bình thường + biên + lỗi kèm 5 gạch** (số · toán tử `>`/`≥` · múi giờ · default khi config trống · thứ tự ưu tiên khi nhiều case cùng đúng).

## Năm quy tắc cứng của kit

1. **Hiểu bài toán trước khi hỏi tham số.** `/frame` chạy trước `/elicit cau 1`; `/spec-write` từ chối chạy khi chưa có `mo-hinh-bai-toan.md`. Với 5 câu hỏi, mô hình bài toán không còn dùng để *sinh* câu hỏi mà để **chọn** ô nào xứng đáng chiếm chỗ trong 5 câu (`knowledge/05` §4).
2. **Không bịa dữ kiện nghiệp vụ — nhưng phải điền mọi ô.** Mọi luật trỏ về một dòng RTM: `← A-07` (có câu trả lời) hoặc `← G-12 [GIẢ ĐỊNH]` (mặc định ngành, có xếp hạng rủi ro). Giá trị giả định **lấy từ `knowledge/20` §4 hoặc `10` §6, không tự nghĩ ra** — lý lẽ ở `knowledge/30` §1b. Ba loại không được tự chốt: luật phá mục tiêu brief, hạn mức không cưỡng chế được, giả định đảo lại thì đổi hướng tiền.
3. **Không hứa hộ bên ngoài.** Mốc "hoàn tất" của việc do cổng thanh toán / ngân hàng / ERP thực hiện phải tách khỏi mốc "hệ thống quyết định", và phải có nhánh thất bại (`knowledge/32` §3.1).
4. **Executor luôn được gọi mù, và luôn gọi hai lần.** Prompt chỉ có đường dẫn spec + tình huống. Bằng chứng spec rõ là **hai reader trùng kết quả**, không phải reader tự khai "không mơ hồ".
5. **Ép kết quả cụ thể.** Mọi câu hỏi AI Khách hàng và mọi test đều phải trả lời được bằng con số / trạng thái cuối / ai thắng / có hoàn tiền không.
6. **Một lượt hỏi = một câu hỏi, và chỉ có 5 lượt.** Mỗi câu tự chứa (AI không có memory), xin về một bảng, có cap dòng/từ, và qua cổng 5 kiểm tra trước khi gửi. Câu cuối là restate và **chỉ soạn sau khi có bản nháp spec** — đó là cách duy nhất để nó nhắm vào giả định đã thật sự vào spec.
7. **Nộp test theo kỳ vọng, không theo số slot.** `EV = 2·P(TRÚNG) − P(VÔ HIỆU)`; `EV ≤ 0` thì bỏ slot. Và mọi test phải có gói bằng chứng phạm vi thu sẵn — kháng nghị chỉ mở cho ca VÔ HIỆU.

**Và một quy tắc về ranh giới sửa:** phát hiện chia hai loại. `SỬA` = viết lại được ngay (mơ hồ, ô trống, thiếu nhánh lỗi). `HỎI` = đội đang không biết specs thật quy định gì (luật phá mục tiêu, hạn mức neo sai, giả định rủi ro cao) — sửa hộ là đoán lần thứ hai trên cùng một chỗ mù. Skill không tự sửa mục `HỎI`; nó sinh câu hỏi.

## Còn hở sau họp 09/09

Bảy ô ở `knowledge/00` §A2. Hai ô đổi kế hoạch buổi sáng, phải hỏi BTC trước 9:30:

1. **Ảnh có tính vào 5.000 token không?** Nếu không → ảnh thành phương tiện restate rẻ nhất (gửi ảnh bảng 25 giả định, xin về "dòng nào sai"), đảo hẳn quy tắc `knowledge/20` §1-9.
2. **AI Khách hàng còn mở sau 12:00 không?** Nếu còn → giữ 1 câu cho 13:30 để xin danh sách NGOÀI phạm vi mở rộng, dùng chống VÔ HIỆU khi bắn.

Năm ô còn lại (xếp giải pool/toàn giải, độ dài test, VÔ HIỆU của đối thủ có cho thủ +1 không, số ca kháng nghị, được xem lý do đối chiếu trước không) chỉ đổi cách tính điểm kỳ vọng, không đổi quy trình.

Nếu BTC **không cho dùng AI trong phòng thi**: kit vẫn dùng để diễn tập và để in artifact mang vào — sáu khối mô hình `knowledge/05` §1 + 12 ca suy biến §M6, cổng F `knowledge/32` §1 + bảng thực tế phụ thuộc ngoài §2 + ba mẫu viết lại §3, **5 câu hỏi soạn sẵn `knowledge/20` §3** + 31 phát biểu mặc định ngành §4 + bảng ánh xạ cuối §3, template `knowledge/30` §1–§1b–§2 + checklist 24 quy tắc §4, checklist tấn công `knowledge/50` §1, §4, §4b, §6, danh sách đen `knowledge/40` §2.

## Tùy chỉnh

- Model Executor của BTC là **thông tin bảo mật, BTC không công bố** (00 §H câu 7) — nên giả lập bảo toàn là lựa chọn duy nhất đúng: `/spec-review` khối D luôn gọi reader 2 bằng `model: haiku`. Muốn khắt khe hơn nữa: đổi `model: inherit` → model yếu hơn trong `.claude/agents/executor.md`.
- Fixture luyện tập: chạy `/drill <tên>` để sinh mới theo cấu trúc 10 mục. (Bộ self-test 05/09 đã bỏ — dựng theo template 11 mục cũ.)
