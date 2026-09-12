# PROGRESS — Chuẩn bị HBLAB AI Hackathon #02 (Spec Battle)

**Ngày thi:** 12/09/2026 · **Họp BTC:** 09/09 · **Tài liệu BTC "Spec Battle Anatomy":** 11/09 · **THI THỬ 11/09 — luật hỏi đổi hẳn** · **Cập nhật note:** 12/09/2026

**Mục tiêu cuối:** tài liệu → PDF → LLM Wiki/PageIndex → bộ skills & agents dùng trong ngày thi.

---

## Pipeline & trạng thái

| # | Giai đoạn | Trạng thái | Ghi chú |
|---|---|---|---|
| 1 | Tổng hợp tài liệu nền | ✅ **Xong** 04/09 | → `hackathon_descriptions.md` |
| 2 | Thu thập PDF theo §7 | ✅ Xong, **đã xoá 11/09** | 16 PDF đã chưng cất hết vào `knowledge/` ở giai đoạn 3; xoá để repo gọn (99MB → 44MB), không skill nào đọc `data/`. Giữ `data/btc/` (bản trích nguyên văn tài liệu BTC) |
| 3 | Dựng LLM Wiki / PageIndex | ✅ Thay bằng `knowledge/` | Data nhỏ → chưng cất 10 file thay wiki/RAG |
| 4 | Xây skills & agents | ✅ **Xong** 05/09 · bổ sung trục nội dung 09/09 · **cấu trúc BTC 10 mục 11/09** | 7 skill + 2 agent trong `.claude/`, 10 file `knowledge/` (thêm `33` cấu trúc spec BTC); đánh giá nội dung `battle/danh-gia-noi-dung.md` |
| 5 | Dựng 15 artifact thi đấu | ◐ Phần lớn có trong `knowledge/` | Glossary (10 §2), question bank (20 §2–4), template (30 §1–2), attack checklist (50 §1/§4/§6), blacklist (40 §2), RTM/log mẫu (20 §5–6); còn thiếu: in ra giấy, câu hỏi BTC đã có ở 00 §H |
| 6 | Diễn tập trọn 1 lượt | ❗ **Chưa chạy trên cấu trúc 10 mục và luật hỏi 11/09** | `/drill` trọn vòng **theo hạn mức 11/09** (không giới hạn số câu, một ý mỗi lượt, cấm chỉ thị, nhịp chờ, 4.000 token, spec 6k token). **Không được bỏ** — mọi số đo cũ đều từ chế độ batch hoặc chế độ 5-câu-gộp-bảng, cả hai đều **bị từ chối** ở hệ thật |
| 7 | Áp tham số chốt 09/09 + cấu trúc BTC 11/09 vào toàn kit | ✅ **Xong 11/09** | 10 file `knowledge/`, 7 skill, 2 agent, 2 README — xem mục "Đã làm" bên dưới |
| 9 | Áp **hạn mức spec 10.000 token** + nâng khối lưu đồ Mermaid | ✅ **Xong 11/09 chiều** | `knowledge/33` §5 phân bổ lại 10 mục (đích 9.000, **sàn 7.500**) + §6 + §7 viết lại thành **6 loại sơ đồ** (thêm `flowchart TD` lưu đồ quyết định, `erDiagram`) + **§7.7 lint 10 lỗi gãy render** · `knowledge/30` §1/§5/§6/§7 · `knowledge/00` §A/§A1/§B/§I · `/spec-write`, `/spec-review` (khối 5b lint sơ đồ, ngưỡng dưới token), `/frame`, `/drill` · `32`, `40`, 2 README |
| 11 | **Nạp đạn bằng lượt hỏi AI Khách hàng** (`/attack hoi`) | ✅ **Xong 12/09** | `knowledge/50` **§9** (ba dạng lượt: Δ-probe / scope-probe / đếm-probe; luật đọc kết quả; §9.3 khi AI đã đóng) · `knowledge/20` §5 quy tắc 3 + 8b (**⚠⚠ = `Δ ≠ 0` = băng đạn**) · `/attack` **chế độ `hoi`** + bước 0c · `drill/thi-thu-1109/nap-dan.md` (15 lượt, đã kiểm ngược bằng ground truth) |
| 10 | Áp **bài học CÔNG sau thi thử 11/09** (2/2 viên TRƯỢT) | ✅ **Xong 11/09 tối** | `knowledge/50` §0 + loại #25–#27 + §2-13…§2-16 + luật 9 (EV tương đối) + P59–P61 + **§8 bảng 14 ô "đề luôn chốt"** · `knowledge/20` **§4d** + §3.1 bước 2b · `/attack` bước 0/0b + cổng cấm cược · `/elicit` bước 3c · `/spec-write` checklist 14 ô · `/spec-review` **khối G-10** · `drill/thi-thu-1109/tests/doi-a.v2.md` |
| 8 | Áp **luật hỏi mới sau thi thử 11/09** (bỏ trần 5 câu, một ý/lượt, cấm chỉ thị, nhịp chờ, 4.000 token) | ✅ **Xong 11/09 chiều** | `knowledge/00` §A/§A1/§A2/§B/§C/§F/§H/§I · `knowledge/20` viết lại §1/§2/§3/§3b/§5/§6 · `05` §4 · `30` §1b/§6 · `32` §6 · `50` §2/§4 · `/elicit` viết lại · `/frame`, `/drill`, `/spec-review`, `/appeal` · agent `customer` · 2 README |

---

## Self-test 05/09 — đã bỏ

Bộ self-test T1–T9 và fixture `drill/selftest/` dựng theo **template 11 mục cũ**, nên mọi số đo (T5 93/100, T6 recall 30/33, T7 5/5 TRÚNG) không còn phản ánh kit hiện tại. Fixture chưa từng commit và đã bỏ.

**Cảnh báo khi đọc bảng dưới:** mọi kết quả T1–T9 đo trong chế độ **6 lượt hỏi batch và hạn mức 3.000 từ**. Sau tham số 09/09 và luật hỏi 11/09 (không giới hạn số câu, một ý mỗi lượt, cấm chỉ thị, 4.000 token) các số này **không còn là bằng chứng kit hoạt động** — đặc biệt T4 (khối 8 câu một lượt: chế độ đó vừa không tồn tại vừa bị từ chối) và T5 (2.888 từ ≈ 7.200 token: vượt hạn mức mới). T1, T2, T3, T7, T8 vẫn có giá trị vì chúng đo hành vi agent, không đo quy trình hỏi.

| Test | Kết quả | Ghi chú |
|---|---|---|
| T1 executor mù | ĐẠT 7/7 | đúng 5 mục; từ chối đọc file thứ hai; không rò rỉ |
| T2 customer HỎI | ĐẠT 6/6 + 5/5 | từ chối ngoài phạm vi, "không có quy định riêng" đúng |
| T3 customer CHẤM | ĐẠT 4/4 | VÔ HIỆU/TRƯỢT/TRÚNG/TRÚNG đúng kỳ vọng |
| T4 /elicit lượt 1 + nạp | ĐẠT (tự kiểm) | khối 8 câu, TRONG/NGOÀI, RTM 8 dòng, ⚠ ERP + guest? |
| T5 /spec-write | ĐẠT 93/100 | 2.888 từ, 18 BR, 17/17 ⚠ có BR, không bịa; lỗ hổng: §3 thiếu 'xem hold' + 0.4 |
| T6 /spec-review (spec lỗi) | ĐẠT 89/100 | recall 30/33 (91%), 4/4 lỗi cứng, kết luận CHƯA NỘP ĐƯỢC |
| T7a /attack spec lỗi | **5/5 TRÚNG, 0 VÔ HIỆU** | dry-run loại đúng 4 ứng viên Executor đoán trùng |
| T7b /attack ngược spec mình | **1/5 TRÚNG** | 17/17 ⚠ đã chắn; lỗ hổng: §3 thiếu hàng "xem hold" + 0.4 |
| T8 /appeal | ĐẠT 90/100 | 3 ca đều có trích nguyên văn |
| T9 /drill mini (đặt bàn) | CHƯA HOÀN TẤT | sinh fixture miền mới ✓ (33 luật/19 ⚠); dừng vì giới hạn phiên |

Hai bài học từ đợt đó vẫn giữ nguyên giá trị và **đã nằm trong skill**: `/attack` giữ ứng viên khi Executor khác đáp án (kể cả ĐỘ PHỦ = ĐỦ), và `/spec-review` đo đa nghĩa bằng bất đồng giữa hai reader độc lập.

---

## 11/09 — BTC gửi tài liệu cấu trúc spec ("Spec Battle Anatomy")

Nguồn: bản trích nguyên văn `data/btc/spec-battle-anatomy.txt`. Chỉ nói về **cấu trúc spec**, không đổi tham số (3.000 từ, token, điểm số vẫn như §A).

**Thay đổi bản chất:** spec nộp phải theo **10 mục kiểu thiết kế Nhật** — Basic design 基本設計 (1–5, *theo màn hình*) · Detailed design 詳細設計 (6–9, *theo chức năng*) · Technical spec 技術仕様 (10). Template 11 mục cũ (thuần luật nghiệp vụ + state machine) **không còn là cấu trúc nộp** — đã ánh xạ vào 10 mục, giữ toàn bộ kỹ thuật cũ (catch-all, EARS, decision table, mã BR, RTM).

**Bốn vùng hoàn toàn mới phải hỏi AI Khách hàng:** mục 2 item màn hình · mục 3 event · mục 4 validation & message lỗi **nguyên văn** · mục 9 API/tích hợp.

| File | Đã sửa |
|---|---|
| `knowledge/33-cau-truc-spec-btc.md` | **MỚI** — 10 mục (nội dung + hình thức bảng BTC yêu cầu), "ba thứ" của mỗi logic ở mục 6, quy tắc cũ nào đổi, ánh xạ 11→10 mục, ngân sách từ, thứ tự cắt |
| `knowledge/30-viet-spec.md` | §1 template viết lại theo 10 mục; §2 catch-all chuyển về mục 1.x; §4 quy tắc 13 (cho phép mô tả UI ở mục 2–5, TBD → bảng 7.4) và 15 (4 → **7 chiều** kết quả); §5 bỏ "không mô tả màn hình"; §6 lịch viết mới; §7 cổng chất lượng 11 → **17 dòng** |
| `knowledge/20-ngan-hang-cau-hoi.md` | Thêm **N15–N19** (49 câu: item màn hình, event, validation/message, API, ràng buộc/chưa chốt) → 158 câu/20 nhóm; thêm **Lượt 7**; ngân sách token 7 lượt; thứ tự chạy L1→L2→L3→**L7**→L4→L5→L6 |
| `knowledge/40-tu-mo-ho.md` | Thêm **S31–S39** (thiếu mục · message không nguyên văn · thiếu FE/BE · bảng Case thiếu loại case · thiếu 5 gạch · không phân biệt guest · TBD rải rác); thang ưu tiên sửa thêm mức 0 và 2b |
| `knowledge/50-tan-cong.md` | 21 → **24 loại lỗ hổng** (#22 message, #23 guest, #24 trạng thái UI — tỷ lệ TRÚNG rất cao); quy trình soi 10 phút đi theo 10 mục; thêm probe **P43–P48** |
| `.claude/skills/spec-write` | Bước 1–14 viết lại theo 10 mục; thứ tự viết mục 1 → 6 → 4 → 8 → 2 → 3 → 7 → 9/10/5; chế độ nén cắt theo 32 §6 |
| `.claude/skills/spec-review` | Thêm khối **B0** (đủ 10 mục, chạy trước mọi kiểm); B kiểm mục 6 (3 loại case + 5 gạch), mục 4 (message nguyên văn), mục 2/8 (guest), mục 7 (4 ca BTC + bảng 7.4); eval set bắt buộc phủ #16/#17/#18 |
| `.claude/skills/attack` | Quy trình 10 phút theo 10 mục; ưu tiên chọn test: #15 → #16/#18/#19/#21 → **#22/#23** → … ; bảng "10 mục BTC của spec đối thủ" trong output |
| `.claude/skills/elicit` | Đọc thêm knowledge/33; **bảng phủ 10 mục** khi nạp |
| `.claude/skills/drill` | true-spec phải có dữ kiện cho cả 10 mục + ≥2 luật ⚠ vùng message/guest/nút; ket-qua.md thêm bảng phủ 10 mục |
| `.claude/agents/executor.md` | Trả lời phải gồm message nguyên văn / trạng thái nút; thêm ví dụ 0 (spec ghi "thông báo lỗi phù hợp" → Executor tự bịa message) |
| `README.md`, `.claude/README.md` | Mô tả cấu trúc 10 mục, 10 file knowledge |

Sơ đồ trong spec dùng **Mermaid** (knowledge/33 §7): `block-beta` wireframe (mục 5) · `stateDiagram-v2` state machine (mục 6) · `sequenceDiagram` flow end-to-end (mục 6) · `flowchart LR` sơ đồ hệ thống (mục 9). Quy tắc cứng: **sơ đồ không thay bảng** — sơ đồ chỉ vẽ chuyển hợp lệ, còn ô `Từ chối 0.5` / `KHL` (chỗ Executor đoán sai) chỉ có trong bảng; hết chỗ thì bỏ sơ đồ, giữ bảng. Kiểm S29/S30 trong knowledge/40 bắt lỗi này, `/attack` coi "chỉ có sơ đồ, không có bảng" là ứng viên hạng A.

**Hợp nhất với nhánh tham số 09/09 (11/09, sau khi merge):** hai nhánh cập nhật song song nên phải đánh số lại để không đụng mã —

| Đụng | Cách giải |
|---|---|
| Hai file cùng số `32` | Cấu trúc BTC thành **`knowledge/33-cau-truc-spec-btc.md`**; `32-kha-thi-van-hanh.md` (cổng F) giữ số cũ vì đã được tham chiếu từ 3 commit |
| Hai bộ `S22–S30` ở `knowledge/40` §3 | Bộ cấu trúc 10 mục đổi thành **S31–S39**; bộ nội dung/khả thi giữ S22–S30 |
| Hai bộ loại lỗ hổng `#16–#18` ở `knowledge/50` §1 | Bộ cấu trúc đổi thành **#22–#24**; bảng thành **24 loại** |
| Hai bộ probe `P43–P45` | Bộ cấu trúc đổi thành **P53–P58** |
| Ngân sách **3.000 từ** vs **6.000 token** | Token thắng (tham số chốt 09/09). `knowledge/33` §5 phân bổ lại 10 mục theo đích **5.400 token ≈ 2.170 từ** |
| **7 lượt hỏi batch** vs **5 câu, 1 câu/lượt** | Cả hai đều bỏ sau thi thử 11/09 — luật hiện hành là **chuỗi lượt một-ý, không trần số câu, cấm chỉ thị**. N15–N19 giữ làm nguyên liệu |
| Cổng chất lượng 18 dòng vs 16 dòng | Hợp nhất thành **23 dòng** (`knowledge/30` §7) |
| `.gitignore` `drill/` | Đổi thành `/drill/` — `drill/` trần ẩn luôn `.claude/skills/drill/` |

**Hở đó đã đóng sau 11/09:** bỏ trần 5 câu nghĩa là **mục 4 message lỗi nguyên văn hỏi được và phải hỏi** — mỗi message một lượt mở (~300 token), nằm trên đường cắt. Hở còn lại chỉ là **nhịp chờ**: mục 2/3/9 vẫn tự điền. Chi tiết: `knowledge/20` §3b.

**Việc cần làm ngay (chưa xong):**
1. **`/drill` trọn vòng** với true-spec có đủ 10 mục **và luật hỏi 11/09** — kit chưa từng chạy thử ở chế độ chuỗi lượt một-ý. Việc quan trọng nhất còn lại trước ngày thi. Hai chỉ số mới phải đọc: **chi phí bị từ chối** (>10% = cổng 8 kiểm tra chưa chặn đủ) và **đúng nhịp hay không** (<70% = đội ngồi chờ thay vì viết song song).
2. Hỏi BTC: mục 5 (Design/Wireframe) nộp thế nào khi không có Figma — **Mermaid có được chấp nhận không**? Ảnh/sơ đồ có tính vào hạn mức token không? (ảnh: xem 00 §A2). Trước đây câu này hỏi theo hạn mức 3.000 từ không? (nhập vào §A cùng các ô CHỜ 09/09.)

---

## Đã làm

**12/09 — `Δ` là biến quyết định, không phải "đối thủ im lặng"**

Đọc ba thẻ kết quả của hệ chấm (TRÚNG / TRƯỢT / VÔ HIỆU) thì lộ ra hai điều kit đang hiểu sai:

1. **TRÚNG cần hai vế, kit cũ chỉ kiểm một.** Thẻ TRÚNG ghi "Spec B: không mô tả cách xử lý X · Executor: `<mặc định>` · Intent thật: `<khác>`". Vế "đối thủ im lặng" chỉ mở cửa; vế quyết định là **`Δ` = chênh lệch giữa specs thật và mặc định ngành**. `Δ = 0` thì Executor lấp chỗ im lặng bằng đúng đáp án ⇒ TRƯỢT ⇒ **+1 cho đối thủ**. Đây chính là 4 ứng viên bị dry-run loại ở 11/09 — dry-run đã đo `Δ` gián tiếp, nhưng chỉ ở cuối quy trình và chỉ trên ứng viên đã soạn xong.
2. **VÔ HIỆU do chính AI Khách hàng phán phạm vi**, Executor thậm chí không được gọi. Nghĩa là **một lượt hỏi "X có thuộc phạm vi không" là bằng chứng mức 1 mạnh nhất** và dập `P(VÔ HIỆU)` xuống ~0,02 — rẻ hơn kháng nghị, vốn chỉ mở cho ca VÔ HIỆU và cũng chỉ tranh lại đúng câu phán này.

⇒ **Cả hai vế đều hỏi được trước khi bắn.** Thêm chế độ **`/attack hoi`**: sau khi đọc spec đối thủ và tìm ô im lặng, sinh ba dạng lượt — **Δ-probe** (hỏi thẳng giá trị của ô), **scope-probe** (hỏi phạm vi, gửi TRƯỚC Δ-probe vì đáp "ngoài phạm vi" làm Δ-probe thành lãng phí nhịp), **đếm-probe** (`全部で何種類` cho danh sách đóng — một con số mở ra nhiều ứng viên nhất trên một nhịp). Luật đọc kết quả: đáp **lệch** mặc định ngành ⇒ BẮN, `P` lên 0,85 · đáp **trùng** ⇒ BỎ · **deflection ⇒ BỎ** (không có đáp án chuẩn thì không có ca TRÚNG).

**Không thêm cột RTM mới:** `⚠⚠` sẵn có đã đúng nghĩa `Δ ≠ 0`. Chỉ làm rõ rằng nó phục vụ **hai vai** bằng một dấu, và thêm quy tắc 8b — in mọi dòng `⚠⚠` thành danh sách riêng cuối `rtm.md` để 13:00 chỉ việc đối chiếu với spec đối thủ, không phải đọc lại log.

**Kiểm chứng trên hồ sơ 11/09** (`drill/thi-thu-1109/nap-dan.md`): 15 lượt → **8 ứng viên BẮN** (cơ chế A) · 2 ứng viên bị loại sớm đúng · 1 rào VÔ HIỆU được gỡ. Hai lượt đáng giá nhất lại là hai lượt **ngăn một phát bắn hỏng**:
- **S3** (`基準額 có đổi theo hạng không`) → đáp "có" ⇒ chặn đúng viên **T-A1** trước khi bắn.
- **S1** (`遠隔地追加送料 có thuộc bảng tiền không`) → đáp "có" ⇒ **gỡ rào** cho ứng viên 440円 mà bản cũ đã loại vì ước `P(VÔ HIỆU)` = 0,25 dựa trên A-23. Hoá ra `NG-002` chỉ loại 包装料・代引手数料, không loại phụ phí vùng xa.

Đó là chỗ khác biệt thật: thi thử thua vì **bắn khi chưa biết**, không phải vì thiếu ứng viên.

**Nếu AI Khách hàng đóng trước pha CÔNG** (00 §A2-4, vẫn chưa có văn bản): `50` §9.3 — hỏi trước theo bảng 14 ô §8, và đánh `Δ` **ngay lúc nạp câu trả lời** thay vì đợi 13:00.

**11/09 tối — Bài học vai CÔNG: 2/2 viên TRƯỢT, và nguồn đạn bị bỏ sót**

Soi lại buổi thi thử bằng ground truth thật (`drill/thi-thu-1109/ground-truth-thi-thu.md`, khôi phục từ hồ sơ trận). Kit sinh 2 test cho spec đội A, **trượt cả hai — và cả hai trượt vì lỗi quy trình, không phải xui**:

1. **T-A1 (しきい値)** — kit suy "AI Khách hàng nói 「しきい値」 số ít, không nhắc hạng thành viên ⇒ đối thủ tự bịa hai mốc 5.000/3.000". Ground truth V-03: **hai mốc đó đúng**, đội A biết nhiều hơn ta. Lỗi: **im lặng của AI Khách hàng bị đọc thành bằng chứng phủ định** — khách trả lời đúng một ẩn số mỗi lượt, ô ta không hỏi thì khách không kể.
2. **T-A2 (coupon hết hạn giữa giỏ và xác nhận)** — kit suy "specs thật gần như chắc chắn tái kiểm hạn lúc chốt". Ground truth V-10: hạn xét **tại lúc ÁP**, và GTD còn ghi rõ đây là *"điểm dễ nhầm nhất của đề"*. Lỗi: **cược vào đáp án chuẩn bằng suy luận hợp lý** — mà suy luận hợp lý là đúng thứ đề cố bẻ, và cũng là đúng thứ Executor mù sẽ đoán ⇒ hai bên trùng nhau ⇒ TRƯỢT.

**Chỗ kit đã làm đúng mà không dám đi tiếp.** Bước dry-run loại đúng 4 ứng viên mạnh nhất (A-12/A-14/A-15/A-17: đối thủ im lặng nhưng mặc định ngành trùng specs thật). Loại là đúng. Sai là ở chỗ **thay chúng bằng hai canh bạc thay vì đổi nguồn đạn** — nguồn đúng nằm ngay trong file đối thủ mà kit đọc rồi bỏ qua: **mục 8「Điểm chưa chốt」I-01…I-05**, tức danh sách đối thủ tự viết ra những ô Executor của họ sẽ phải bịa. Đối chiếu ground truth: 3/5 dòng đó có luật riêng, phản trực giác.

Bốn thay đổi, tất cả đã áp:

1. **Hai cơ chế TRÚNG, không có cơ chế thứ ba** (`50` §2-14). **A** = ta có lời khách nguyên văn *và* đối thủ nói khác nó. **B** = ô thuộc bảng §8, đối thủ im lặng hoặc tự khai chưa chốt, và **không gian đáp án `W ≥ 4`** — thắng nhờ độ rộng, không nhờ ta đoán đúng. Không thuộc A cũng không B ⇒ **bỏ slot**.
2. **`EV` tính lại cho đúng luật chấm** (`50` §6 luật 9). Luật 8 cũ coi TRƯỢT = 0; thật ra TRƯỢT **cho đội thủ +1**. `EV_rel = 3·P(TRÚNG) − 1 − 2·P(VÔ HIỆU)` ⇒ ngưỡng nộp là `P(TRÚNG) > 0,37`, không phải `> P(VÔ HIỆU)/2`. Thêm **bảng gán `P(TRÚNG)` theo bằng chứng** — cấm gán theo cảm giác, vì hai viên trượt đều được gán 0,55–0,60 không có cơ sở (gán lại đúng thì cả hai rơi xuống 0,25 và bị loại).
3. **Bảng 14 ô "đề luôn chốt"** (`50` §8 = `20` §4d) — mười bốn ô mà đề nghiệp vụ nào cũng chốt sẵn: đơn vị/bội số, trần/sàn, thời điểm xét, phép so dùng tổng nào, tập con loại trừ, phụ phí vẫn thu, danh sách mã lỗi + thứ tự, thao tác gỡ, lặp lại thao tác, message kèm số, trạng thái rỗng, thời hạn lưu, chuẩn hoá đầu vào, phân hạng. **Đây là bảng hai mặt**: nguồn đạn khi soi đối thủ, hàng đợi hỏi và checklist lấp lỗ khi viết spec mình.
4. **Ba loại lỗ hổng mới + ba probe** — #25 ô đối thủ tự khai chưa chốt (P59), #26 đơn vị/bội số/trần (P60), #27 thao tác hoàn tác không tồn tại (P61). Ba loại này sống sót được cả khi đối thủ đã chắn hết dòng ⚠ của ta — tình huống **11/14 dòng bị chắn** của thi thử là bình thường, vì hai đội đọc cùng brief và hỏi cùng một AI Khách hàng.

**Kiểm chứng:** chạy lại `/attack` bản mới trên đúng spec đội A → 5 test (`drill/thi-thu-1109/tests/doi-a.v2.md`), **cả 5 dry-run qua executor mù đều lệch ground truth**, trong đó một ca `ĐỘ PHỦ = ĐỦ · ĐA NGHĨA = KHÔNG` (spec A nói trái sự thật, hạng A+). Cả 5 đều cơ chế B — **không test nào cần ta biết đáp án**.

**Số đo đắt nhất của buổi này:** ta hỏi 14 lượt tính token nhưng **9/14 ô của bảng §8 không lượt nào chạm tới** (Đ1, Đ2, Đ5, Đ6, Đ8, Đ9, Đ12, Đ13, Đ14), và **5 trong 9 ô bỏ trống đó có luật phản trực giác**. Vừa mất đạn công vừa hở giáp thủ, từ cùng một chỗ. Vì thế bảng đã được nối vào cả ba pha: `/elicit` bước 3c (sàn bắt buộc của hàng đợi), `/spec-write` (một dòng checklist), `/spec-review` khối G-10 (grep 14 ô trên bản nộp).

**Còn lại:** `/drill` trọn vòng vẫn chưa chạy — giờ nó phải đo thêm **tỷ lệ phủ 14 ô §8** ở cả hai vai.

**11/09 chiều (2) — Hạn mức spec 6.000 → 10.000 token; lưu đồ Mermaid thành bắt buộc**

Hạn mức spec nâng lên **10.000 token** (đích 9.000 với 10% đệm). Điều này **đổi loại rủi ro**, không chỉ đổi con số — và đó là phần dễ bỏ sót nhất:

1. **Rủi ro đổi chiều: từ "tràn" sang "dừng sớm".** Với 6.000, việc khó là chọn cắt gì. Với 10.000, spec 6.500 token còn ô trống ở mục 4 (message) và 7.2 (ca bất thường) là spec **chưa viết xong**, không phải spec gọn. Kit vì thế có thêm một **sàn 7.500 token**: dưới mức đó mà còn ô trống là lỗi, và `/spec-review` báo nó như một phát hiện có mức, kèm danh sách "tiêu đệm vào đâu".
2. **Phân bổ lại 10 mục** (`knowledge/33` §5): mục 6 flow 1.900 → **3.200**, mục 4 validation 625 → **1.000**, mục 7 575 → **900**, mục 2 575 → **850**, mục 9 200 → **550**, mục 5 100 → **350**. Thứ tự tiêu 3.570 token thêm, theo lãi giảm dần: thêm case lỗi/biên → thêm message nguyên văn → nâng 7.2 lên 8–10 ca → lưu đồ → bảng 3 trạng thái màn hình.
3. **Tối thiểu 5 case mỗi logic** (1 bình thường + 2 biên + 2 lỗi). Với 6.000 nhiều logic phải dừng ở 3; giờ không còn lý do.
4. **Ngưỡng bảng-hay-danh-sách của mục 6 nâng từ 40 lên 60 dòng RTM** — bảng dễ soi ô trống hơn, giờ đủ chỗ để dùng bảng.
5. **Lưu đồ Mermaid từ "cắt đầu tiên" thành "phải có".** Đọc lại tài liệu BTC (`data/btc/spec-battle-anatomy.txt`) thì thấy **ba sơ đồ được yêu cầu tường minh trong cột Hình thức**: mục 5 wireframe, mục 6 sequence diagram end-to-end, mục 9 sơ đồ hệ thống. Cả bộ chỉ ~1.100 token = 12% ngân sách, nên thiếu là mất điểm hình thức vô cớ. `knowledge/33` §7 viết lại thành **6 loại sơ đồ** kèm §7.0 bảng "dùng cái nào ở đâu".
6. **Thêm `flowchart TD` — lưu đồ quyết định** (§7.5), loại sơ đồ kit chưa từng có. Giá trị riêng của nó không phải trực quan: nó làm lộ **nhánh cụt** (node điều kiện chỉ có một nhánh = một case còn thiếu mà bảng Case giấu được) và nó **chốt thứ tự kiểm**, tức trả lời luôn gạch thứ 5 "thứ tự ưu tiên khi nhiều case cùng đúng".
7. **Thêm §7.7 lint 10 lỗi làm gãy render.** Không có tool render trong phòng thi nên Ctrl+F là cách kiểm duy nhất. Hai lỗi trong đó **chính kit cũ đang mắc**: bọc ngoặc kép sau `:` trong `stateDiagram-v2` và sau `as` trong `sequenceDiagram` — ngoặc sẽ hiện ra trên hình. Các ví dụ ở §7 đã sửa. Mẹo rẻ nhất: viết nhãn sơ đồ **không dấu**, để phần có dấu ở câu tóm tắt.
8. **Hệ quả cho bài tập nén:** `battle/spec.nop.md` (2.991 từ ≈ 7.500 token) trước đây **vượt** hạn mức 25%; với 10.000 thì nó **nằm đúng vùng đích**. Bài tập đổi từ "nén cho vừa" thành "**dùng chỗ trống mới vào đâu**" — đúng bài tập mà hạn mức mới đòi hỏi.

**11/09 chiều — Thi thử: luật hỏi đổi lần thứ hai, và đổi mạnh hơn lần đầu**

Quan sát từ buổi thi thử (đề giả ハナマルストア, 4.000 token, nhịp 45 giây):

1. **Bỏ trần số câu.** Không còn "5 câu cho cả ngày". Giới hạn là **4.000 token** cho cả hỏi + trả lời, **và nhịp chờ giữa hai lượt** — nhịp mới là thứ chặn thật: 17 lượt × 45 giây = 12 phút 45, trong khi 17 lượt hỗn hợp chỉ tốn ~2.500–3.200 token.
2. **Mỗi lượt đúng một ý.** Câu gộp 2–3 ẩn số **bị từ chối**.
3. **Câu chứa chỉ thị bị từ chối, xếp loại injection.** Đây là cú đau nhất: quy tắc trung tâm của kit 09/09 — "một câu hỏi nhưng ép format, cap dòng, cap từ, không giải thích" — **chính là thứ bị chặn**. Không ra lệnh được cho AI nữa, nên công cụ điều khiển duy nhất còn lại là **hình dạng câu hỏi**: câu nhị phân "A hay B" cho câu trả lời ngắn 20–60 token mà không cần một chỉ thị nào.
4. **Câu bị từ chối không trừ token và không reset nhịp chờ** ⇒ sửa và gửi lại gần như miễn phí; giá của một câu viết sai là **một nhịp**, không phải một dữ kiện.
5. **Ảnh CÓ tính token** ⇒ 3 lượt ảnh bỏ hẳn.

Hệ quả chiến thuật, đã áp vào kit:

- Kế hoạch hỏi thành **hàng đợi xếp hạng có đường cắt bằng số** (`knowledge/20` §3), không còn là "chọn 5 ô nào". Đường cắt = `min((phút pha hỏi ÷ nhịp) − 2 ; 4.000 ÷ ~200)`.
- **Hỏi và viết chạy song song**: một người chỉ bấm gửi đúng nhịp, người còn lại viết spec từ lượt thứ 3. Cả đội ngồi chờ là cách mất giờ lớn nhất.
- **Không còn "câu restate" gộp 10 phát biểu** (gộp sẽ bị từ chối). Thay bằng lượt xác nhận rời — và vì một lượt xác nhận tốn đúng một nhịp như mọi lượt khác, **giả định rủi ro Cao thì hỏi thẳng dạng nhị phân ngay từ đầu hàng đợi**, không để dành.
- **Mục 4 (message lỗi nguyên văn) hết hở**: giờ hỏi được, mỗi message một lượt.
- Agent `customer` học cách **từ chối** đúng hai nhãn của hệ thật, để `/drill` không cho kết quả tốt giả.

**10/09 — Áp tham số chốt 09/09; ba thay đổi chiến thuật**

BTC chốt: nộp **markdown ≤ 6.000 token** (sơ đồ mermaid, không ảnh) · hỏi AI Khách hàng **5 câu, mỗi lượt 1 câu, 5.000 token, KHÔNG memory**, ảnh ≤3 lần · điểm **CÔNG trúng +2 / THỦ đỡ +1 / CÔNG vô hiệu −1** · spec đối thủ markdown tải về được · **kháng nghị chỉ cho ca CÔNG bị VÔ HIỆU**, nộp bằng text gửi AI · không sửa test sau khi nộp · được xem lại hội thoại · câu hỏi về vận hành hệ thống thi là thông tin bảo mật, BTC không trả lời.

Ba tham số đổi chiến thuật, không chỉ đổi con số:

1. **5 câu hỏi, không memory** — kiến trúc "6 lượt × 5–8 câu batch" của kit cũ không còn dùng được. Question bank 104 câu đổi vai: từ *danh sách để hỏi* thành **nguồn mặc định ngành để tự điền** cho ~70% spec, cộng kho phát biểu cho câu restate. Kèm theo là quy tắc mới `knowledge/30` §1b: **điền mặc định ngành, không sáng tạo giá trị mới** — vì Executor mù khi gặp im lặng cũng đoán theo mặc định ngành, nên viết mặc định ra không xấu hơn im lặng ở bất kỳ ca nào, còn tự nghĩ ra một giá trị lạ là ca duy nhất tệ hơn im lặng. Lịch buổi sáng đảo lại: **hỏi 4 câu trước 10:20 → viết spec → câu 5 restate lúc 11:05** nhắm đúng giả định đã vào spec.
2. **Công +2 / thủ +1** — trần điểm công 30 vs thủ 15. Lời khuyên cũ "cạn giờ thì ưu tiên phòng thủ" đã sửa (`knowledge/00` §D1). Ngưỡng nộp test thành `EV = 2·P(TRÚNG) − P(VÔ HIỆU) > 0`, và **bỏ slot (0 điểm) tốt hơn bắn bừa (−1)**.
3. **Kháng nghị chỉ cho ca VÔ HIỆU** — `/appeal` mất 2 trong 4 loại ca (spec mình bị TRÚNG oan, So khớp hiểu sai ngữ nghĩa). Bằng chứng phải thu **trước** khi nộp test. Bù lại có nguồn bằng chứng mới: **đồng thuận chéo 3 spec tải về** (loại lỗ hổng #21 + bằng chứng phạm vi mức 3).

- **Mới**: `knowledge/00` §A1 cách đo token (`max(từ×2,5; ký tự/2,2)`), §A2 bảy ô còn hở, §D1 số học công thức điểm · `knowledge/20` §1 mười quy tắc hỏi cho chế độ 1-câu/lượt + cổng 5 kiểm tra, §3 **5 câu C1–C5 soạn sẵn** + bảng ánh xạ "ID nào không được hỏi", §5 RTM có dòng `G-xx` · `knowledge/30` §1 ngân sách **token** từng mục + quy tắc mermaid, §1b luật giả định · `knowledge/50` #21 đồng thuận chéo, §4b bảng chéo, §6 luật 8 điểm kỳ vọng, §7 mẫu text kháng nghị · `/elicit` chế độ `ke-hoach`/`cau n`/`nap`/`restate` với sổ hạn mức và chốt cứng 5 câu · `/attack cheo` · `/spec-review` khối G-7 (giá trị tự nghĩ ra) và loại phát hiện `RỦI RO ĐÃ BIẾT`.
- **Sửa**: cả 9 file `knowledge/`, 7 skill, 2 agent (`customer` chỉ trả lời 1 câu/lượt và đánh dấu `[BỎ QUA]`, hệ số token 2,5; `executor` đọc mermaid), 2 README.
- **Chưa làm**: diễn tập lại trọn vòng theo hạn mức mới; hỏi BTC 2 ô đổi kế hoạch (ảnh có tính token không, AI còn mở sau 12:00 không).

**09/09 — Thêm trục kiểm NỘI DUNG cho bộ skills**

Vấn đề đo được: `battle/spec.nop.md` (diễn tập 08/09) đạt **toàn bộ** trục hình thức — 0 hit lint mức Cao, 36/36 ô bảng trạng thái, DT-1 đủ/rời, 15/15 eval "ĐỦ không đa nghĩa", 2.991/3.000 từ — mà vẫn có **10 lỗi nội dung, 6 mức Cao**, trong đó 3 lỗi khiến spec tự phá mục tiêu brief nêu tường minh (khóa tồn miễn phí cho bot; hạn mức neo vào email tự khai; hứa hoàn tiền ≤24 giờ — bất khả thi). Không cổng nào của kit bắt được, vì mọi cổng kiểm *cách viết*, không kiểm *điều được viết*. Báo cáo đầy đủ: `battle/danh-gia-noi-dung.md`.

- **Mới**: `knowledge/05-hieu-bai-toan.md` (6 khối mô hình bài toán, bảng Mục tiêu↔Luật, 6 kẻ lạm dụng, 12 kịch bản suy biến) · `knowledge/32-kha-thi-van-hanh.md` (cổng F 8 kiểm tra, bảng thực tế 7 phụ thuộc ngoài, 3 mẫu viết lại, 6 chi phí ẩn) · skill `/frame`.
- **Sửa**: `spec-write` (chốt cứng mô hình bài toán, nhãn mục tiêu, cổng F, đích 2.700 từ, xếp hạng rủi ro giả định) · `spec-review` (khối G khả thi/mục tiêu/lạm dụng; **hai reader độc lập, đa nghĩa đo bằng bất đồng**; nhãn SỬA/HỎI) · `elicit` (nhóm N0 hỏi trước tham số, hệ số token 2,5, lượt `restate` bắt buộc) · `attack` (loại #16–#20, probe P43–P52, cổng F trên spec đối thủ) · `drill` (true-spec bắt buộc có 4 nhóm luật để drill không cho kết quả tốt giả, 6 chỉ số) · `knowledge/00, 20, 30, 40, 50` · hai README.
- Nguồn nền bổ sung: ISO/IEC/IEEE 29148 (`feasible`, `affordable`), BABOK v3 + PMI Guide to BA (Business Objectives Model, Scope Modelling, Feasibility/Risk Analysis), NASA SE Handbook (ConOps off-nominal), HTSM + WWWWWHKE + SPECS WATCHLIST, tài liệu vận hành cổng thanh toán, IBM Sterling / Oracle GOP / Shopify.
- **Chưa làm**: diễn tập lại trọn vòng với `/frame` ở đầu; vá 6 mục `SỬA` trong `spec.nop.md`.


**04/09 — Giai đoạn 1 hoàn tất**
- Đọc & đánh giá 3 bản phân tích: `phan_tich_1/2/3.md`.
  - #1: nguồn duy nhất có dữ liệu thật (website + Google Doc BTC) → làm xương sống, giữ ~95%.
  - #2: generic (tự khai không truy cập được web) → giữ 5 ý: mẫu requirement kiểm thử được, "spec là contract cho máy đọc", mẫu finding, quality gate, nguyên tắc 1 owner/artifact.
  - #3: **hiểu sai bản chất cuộc thi** (tưởng pentest Red/Blue Team) → giữ 4 ý: taxonomy tài liệu BA, ý tưởng RTM, quy trình viết spec, customer analysis; + khái niệm trust boundary → chuyển hoá thành *knowledge boundary*.
- Hợp nhất → `hackathon_descriptions.md` (9 phần + 2 phụ lục), tự chứa.
- Bổ sung ngoài 3 file gốc: tầng P0-DOMAIN e-commerce (Magento MSI, Shopify, ATP, ASCM), OMG DMN spec, cấu trúc index + metadata `vuln_type`, blueprint 9 agent, §2.4 "RTM ngược".
- Loại toàn bộ phần pentest của #3 (Metasploit/Nmap/Burp/SQLmap/OWASP secure coding) — sẽ làm nhiễu RAG và khiến agent sinh test sai loại → VÔ HIỆU. Lý do ghi ở Phụ lục B.
- Sửa 5 lỗi dữ kiện của #3 (tác giả sách, đội hình 2–3 người, độ dài sự kiện, vai HackerSME/Coop không tồn tại).

---

## Việc tiếp theo (ưu tiên giảm dần)

0. **`/drill` trọn vòng trên cấu trúc 10 mục mới** — ưu tiên cao nhất: kit chưa từng chạy thử với cấu trúc BTC, và true-spec phải có dữ kiện cho cả 10 mục.
1. **Commit kit** (`.claude/`, `knowledge/`, `data/btc/`, hai README, `PROGRESS.md`) để đồng đội pull; mở phiên Claude Code mới sau khi pull (agent chỉ nạp lúc khởi động phiên).
2. **`/drill` trọn vòng theo hạn mức mới** — bắt buộc, vì mọi số đo của kit đều từ chế độ 6 lượt batch. Tám chỉ số ở `ket-qua.md`; bốn chỉ số cần xem trước:
   - **lỗ hổng giả định**: bao nhiêu dòng `G-xx` *ngược mặc định ngành và lệch specs thật* (phải = 0 — đây là loại tệ hơn im lặng);
   - **hiệu quả pha xác nhận**: lượt xác nhận cứu được mấy BR về sau bị bắn, so với chi phí nhịp; **chi phí bị từ chối** và **tỷ lệ gửi đúng nhịp**;
   - **điểm quy đổi** theo +2/+1/−1, so với điểm nếu bỏ các test `EV ≤ 0`;
   - **độ chính xác ước token**: `max(từ×2,5; ký tự/2,2)` lệch bao nhiêu so với token thật, cho cả lượt hỏi và spec.
3. **Đo lại `battle/spec.nop.md` bằng token** (2.991 từ ≈ 7.500 token) — với hạn mức **10.000** thì bản 08/09 **nằm trong vùng đích**, không còn phải nén. Bài tập đổi chiều: chạy `/spec-review` để xem **1.500 token còn lại nên tiêu vào đâu** (case lỗi/biên thiếu, message chưa nguyên văn, ca 7.2, ba sơ đồ BTC yêu cầu) — đây là bài tập rẻ nhất cho hạn mức mới vì đã có ground truth.
4. **Đọc brief §3/§4 và hỏi BTC các ô đổi đường cắt** (`knowledge/00` §A2): nhịp chờ bao nhiêu giây; 4.000 token của cả đội hay mỗi người; giao diện có hiển thị token không; AI còn mở sau giờ khóa spec không; lý do VÔ HIỆU của đề có gồm "sai phạm vi" không.
5. Luyện `/attack cheo` — cần 3 spec giả lập cho cùng một brief; sinh bằng `/drill` với ba mức độ hở khác nhau (bộ self-test 05/09 đã bỏ vì dựng theo template 11 mục).
6. Nếu BTC không cho dùng AI trong phòng: in `knowledge/20` §1 (cổng 8 kiểm tra) + §3.2–3.3 (28 lượt soạn sẵn) + §4, `30` §1–§1b–§2 + §4, `05` §1+§M6, `32` §1–§3, `33` §1–§2+§7, `40` §2, `50` §1/§4/§4b/§6.

---

## Việc bị chặn / chờ

- **Chín ô còn hở** ở `knowledge/00` §A2. Bốn ô đầu (nhịp chờ, token của đội hay của người, giao diện hiển thị token, AI còn mở sau giờ khóa spec) **quyết định đường cắt của hàng đợi hỏi**; ô thứ năm (lý do VÔ HIỆU của đề) đổi thứ hạng; bốn ô còn lại chỉ đổi cách tính điểm kỳ vọng.
- Vẫn chưa có văn bản về việc **được dùng AI riêng / mang tài liệu vào phòng thi** (`knowledge/00` §H câu 8, 9). Đang giả định **được** — đây là hackathon AI. Nếu không: kit vẫn dùng để diễn tập và in artifact mang vào (danh sách ở mục "Việc tiếp theo" số 6).
- **Model của 3 tác nhân là thông tin bảo mật, BTC sẽ không trả lời** (§H câu 7). Không còn là việc chờ — đã xử lý bằng cách luôn gọi reader 2 với model yếu hơn trong `/spec-review` khối D.

---

## Ghi chú

- `phan_tich_1/2/3.md` đã xoá — mọi thứ cần giữ đã nằm trong `hackathon_descriptions.md` (Phụ lục B ghi lại nhật ký hợp nhất).
- Tài liệu BTC 11/09 bản trích nguyên văn giữ ở `data/btc/spec-battle-anatomy.txt`; bản chưng cất để skill dùng là `knowledge/33-cau-truc-spec-btc.md`. Trang HTML đã lưu (~750KB JS/CSS) đã xoá.
- **16 PDF nguồn đã xoá 11/09** (repo 99MB → 44MB). An toàn vì không skill/agent nào trỏ tới `data/`: `/spec-write` liệt kê đích danh 5 file `knowledge/` phải đọc, `allowed-tools` của cả 6 skill không có công cụ mở PDF, agent `executor` bị khoá Read đúng một lần vào file spec. Tri thức đã nằm trong `knowledge/` dưới dạng dẫn nguồn (`[ISTQB 4.2.4]`, `[DMN 8.2.11]`, `[Berry §5.4.7 E244]`) — chú thích xuất xứ, không phải đường dẫn máy đọc. Lịch sử git vẫn còn file nếu cần lấy lại: `git show HEAD:data/<tên>.pdf > /tmp/x.pdf`. Danh sách 16 PDF: `hackathon_descriptions.md` §7.
- Nguyên tắc khi mở rộng miền tri thức: **chỉ nạp tài liệu về đặc tả / test design / nghiệp vụ inventory**. Không nạp tài liệu bảo mật hệ thống — cuộc thi không có code để pentest, thí sinh không cần biết code.
