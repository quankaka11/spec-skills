# PROGRESS — Chuẩn bị HBLAB AI Hackathon #02 (Spec Battle)

**Ngày thi:** 12/09/2026 · **Họp BTC:** 09/09 đã diễn ra · **Tài liệu BTC "Spec Battle Anatomy":** 11/09 · **Cập nhật note:** 11/09/2026

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
| 6 | Diễn tập trọn 1 lượt | ❗ **Chưa chạy trên cấu trúc 10 mục** | `/drill` trọn vòng **theo hạn mức 09/09** (5 câu, 1 câu/lượt, không memory, spec 6k token). **Không được bỏ** — mọi số đo cũ đều từ chế độ 6 lượt batch, không còn dùng được |
| 7 | Áp tham số chốt 09/09 + cấu trúc BTC 11/09 vào toàn kit | ✅ **Xong 11/09** | 10 file `knowledge/`, 7 skill, 2 agent, 2 README — xem mục "Đã làm" bên dưới |

---

## Self-test 05/09 — đã bỏ

Bộ self-test T1–T9 và fixture `drill/selftest/` dựng theo **template 11 mục cũ**, nên mọi số đo (T5 93/100, T6 recall 30/33, T7 5/5 TRÚNG) không còn phản ánh kit hiện tại. Fixture chưa từng commit và đã bỏ.

**Cảnh báo khi đọc bảng dưới:** mọi kết quả T1–T9 đo trong chế độ **6 lượt hỏi batch và hạn mức 3.000 từ**. Sau tham số 09/09 (5 câu, 1 câu/lượt, không memory, 6.000 token) các số này **không còn là bằng chứng kit hoạt động** — đặc biệt T4 (khối 8 câu một lượt: chế độ đó không còn tồn tại) và T5 (2.888 từ ≈ 7.200 token: vượt hạn mức mới). T1, T2, T3, T7, T8 vẫn có giá trị vì chúng đo hành vi agent, không đo quy trình hỏi.

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
| **7 lượt hỏi batch** vs **5 câu, 1 câu/lượt** | Luật 5 câu thắng. N15–N19 giữ lại làm nguyên liệu; khối "7 lượt" bỏ |
| Cổng chất lượng 18 dòng vs 16 dòng | Hợp nhất thành **23 dòng** (`knowledge/30` §7) |
| `.gitignore` `drill/` | Đổi thành `/drill/` — `drill/` trần ẩn luôn `.claude/skills/drill/` |

**Hở còn lại sau hợp nhất:** kế hoạch 5 câu C1–C5 được soạn khi template còn 11 mục, nên **không phủ mục 2/3/4/9**. Nặng nhất là **mục 4 message lỗi nguyên văn** — chuỗi nguyên văn không suy được từ mặc định ngành. Ba phương án và đề xuất: `knowledge/20` §3b, **phải chọn trước 9:30 ngày thi**.

**Việc cần làm ngay (chưa xong):**
1. **`/drill` trọn vòng** với true-spec có đủ 10 mục — kit **chưa từng chạy thử** trên cấu trúc mới. Việc quan trọng nhất còn lại trước ngày thi.
2. Hỏi BTC: mục 5 (Design/Wireframe) nộp thế nào khi không có Figma — **Mermaid có được chấp nhận không**? Ảnh/sơ đồ có tính vào hạn mức token không? (ảnh: xem 00 §A2). Trước đây câu này hỏi theo hạn mức 3.000 từ không? (nhập vào §A cùng các ô CHỜ 09/09.)

---

## Đã làm

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
   - **hiệu quả câu restate**: C5 cứu được mấy BR về sau bị bắn (nếu 0 thì phải xét lại việc dùng cả một câu cho restate);
   - **điểm quy đổi** theo +2/+1/−1, so với điểm nếu bỏ các test `EV ≤ 0`;
   - **độ chính xác ước token**: `max(từ×2,5; ký tự/2,2)` lệch bao nhiêu so với token thật, cho cả lượt hỏi và spec.
3. **Đo lại `battle/spec.nop.md` bằng token** (2.991 từ ≈ 7.500 token) — bản 08/09 **vượt hạn mức 6.000 khoảng 25%** theo luật mới. Nén theo `knowledge/30` §5 rồi chạy `/spec-review` để xem cắt gì thì mất luật gì; đây là bài tập nén rẻ nhất vì đã có ground truth.
4. **Hỏi BTC 2 ô đổi kế hoạch** (`knowledge/00` §A2): ảnh có tính vào 5.000 token không; AI Khách hàng còn mở sau 12:00 không. Hỏi trước 9:30 ngày thi, hoặc sớm hơn nếu có kênh liên hệ.
5. Luyện `/attack cheo` — cần 3 spec giả lập cho cùng một brief; sinh bằng `/drill` với ba mức độ hở khác nhau (bộ self-test 05/09 đã bỏ vì dựng theo template 11 mục).
6. Nếu BTC không cho dùng AI trong phòng: in `knowledge/20` §3 (5 câu soạn sẵn) + §4, `30` §1–§1b–§2 + §4, `05` §1+§M6, `32` §1–§3, `33` §1–§2+§7, `40` §2, `50` §1/§4/§4b/§6.

---

## Việc bị chặn / chờ

- **Bảy ô còn hở** ở `knowledge/00` §A2. Hai ô đầu (ảnh có tính token; AI còn mở sau 12:00) đổi kế hoạch buổi sáng; năm ô còn lại chỉ đổi cách tính điểm kỳ vọng.
- Vẫn chưa có văn bản về việc **được dùng AI riêng / mang tài liệu vào phòng thi** (`knowledge/00` §H câu 8, 9). Đang giả định **được** — đây là hackathon AI. Nếu không: kit vẫn dùng để diễn tập và in artifact mang vào (danh sách ở mục "Việc tiếp theo" số 6).
- **Model của 3 tác nhân là thông tin bảo mật, BTC sẽ không trả lời** (§H câu 7). Không còn là việc chờ — đã xử lý bằng cách luôn gọi reader 2 với model yếu hơn trong `/spec-review` khối D.

---

## Ghi chú

- `phan_tich_1/2/3.md` đã xoá — mọi thứ cần giữ đã nằm trong `hackathon_descriptions.md` (Phụ lục B ghi lại nhật ký hợp nhất).
- Tài liệu BTC 11/09 bản trích nguyên văn giữ ở `data/btc/spec-battle-anatomy.txt`; bản chưng cất để skill dùng là `knowledge/33-cau-truc-spec-btc.md`. Trang HTML đã lưu (~750KB JS/CSS) đã xoá.
- **16 PDF nguồn đã xoá 11/09** (repo 99MB → 44MB). An toàn vì không skill/agent nào trỏ tới `data/`: `/spec-write` liệt kê đích danh 5 file `knowledge/` phải đọc, `allowed-tools` của cả 6 skill không có công cụ mở PDF, agent `executor` bị khoá Read đúng một lần vào file spec. Tri thức đã nằm trong `knowledge/` dưới dạng dẫn nguồn (`[ISTQB 4.2.4]`, `[DMN 8.2.11]`, `[Berry §5.4.7 E244]`) — chú thích xuất xứ, không phải đường dẫn máy đọc. Lịch sử git vẫn còn file nếu cần lấy lại: `git show HEAD:data/<tên>.pdf > /tmp/x.pdf`. Danh sách 16 PDF: `hackathon_descriptions.md` §7.
- Nguyên tắc khi mở rộng miền tri thức: **chỉ nạp tài liệu về đặc tả / test design / nghiệp vụ inventory**. Không nạp tài liệu bảo mật hệ thống — cuộc thi không có code để pentest, thí sinh không cần biết code.
