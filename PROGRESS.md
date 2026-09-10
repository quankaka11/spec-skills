# PROGRESS — Chuẩn bị HBLAB AI Hackathon #02 (Spec Battle)

**Ngày thi:** 12/09/2026 · **Họp BTC:** 09/09 đã diễn ra · **Cập nhật note:** 10/09/2026

**Mục tiêu cuối:** tài liệu → PDF → LLM Wiki/PageIndex → bộ skills & agents dùng trong ngày thi.

---

## Pipeline & trạng thái

| # | Giai đoạn | Trạng thái | Ghi chú |
|---|---|---|---|
| 1 | Tổng hợp tài liệu nền | ✅ **Xong** 04/09 | → `hackathon_descriptions.md` |
| 2 | Thu thập PDF theo §7 | ✅ Một phần | 16 PDF trong `data/` (P0, P0-domain, P1) |
| 3 | Dựng LLM Wiki / PageIndex | ✅ Thay bằng `knowledge/` | Data nhỏ → chưng cất 9 file thay wiki/RAG |
| 4 | Xây skills & agents | ✅ **Xong** 05/09, **bổ sung trục nội dung 09/09** | 7 skill + 2 agent trong `.claude/`, 9 file `knowledge/`; self-test `drill/selftest/KET-QUA-SELFTEST.md`; đánh giá nội dung `battle/danh-gia-noi-dung.md` |
| 5 | Dựng 15 artifact thi đấu | ◐ Phần lớn có trong `knowledge/` | Glossary (10 §2), question bank (20 §2–4), template (30 §1–2), attack checklist (50 §1/§4/§6), blacklist (40 §2), RTM/log mẫu (20 §5–6); còn thiếu: in ra giấy, câu hỏi BTC đã có ở 00 §H |
| 6 | Diễn tập trọn 1 lượt | ◐ Có `/drill`, chạy mini dở dang | 10–11/09 chạy `/drill` trọn vòng **theo hạn mức 09/09** (5 câu, 1 câu/lượt, không memory, spec 6k token). **Không được bỏ** — mọi số đo cũ đều từ chế độ 6 lượt batch, không còn dùng được |
| 7 | Áp tham số chốt 09/09 vào toàn kit | ✅ **Xong 10/09** | 9 file `knowledge/`, 7 skill, 2 agent, 2 README — xem mục "Đã làm" bên dưới |

---

## Self-test kit (05/09) — trạng thái để tiếp tục

**Đã xong:** `knowledge/00…50` (7 file, critic + fix); `.claude/skills/{elicit,spec-write,spec-review,attack,appeal,drill}/SKILL.md`; `.claude/agents/{executor,customer}.md`; `.claude/README.md`. Fixture self-test ở `drill/selftest/` (true-spec ẩn 49 luật/16 ⚠, RTM 33 dòng, spec lỗi 33 lỗi cài).

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

**Sửa skill từ bài học self-test:** attack bước 8 (giữ khi Executor khác đáp án, kể cả ĐỘ PHỦ = ĐỦ → spec viết trái); spec-write bước 4 (§3 phủ thao tác chỉ-đọc vì 0.4), bước 10 (cách đếm wc -w); elicit nạp (tách liệt kê thành từng dòng); appeal (quét toàn bộ log, trích `[…]`, dẫn 00 §D).

**Xong 05/09 19:40.** Báo cáo: `drill/selftest/KET-QUA-SELFTEST.md`. Chưa commit.

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

1. **Commit kit** (`.claude/`, `knowledge/`, hai README, `PROGRESS.md`) để đồng đội pull; mở phiên Claude Code mới sau khi pull (agent chỉ nạp lúc khởi động phiên).
2. **`/drill` trọn vòng theo hạn mức mới** — bắt buộc, vì mọi số đo của kit đều từ chế độ 6 lượt batch. Tám chỉ số ở `ket-qua.md`; bốn chỉ số cần xem trước:
   - **lỗ hổng giả định**: bao nhiêu dòng `G-xx` *ngược mặc định ngành và lệch specs thật* (phải = 0 — đây là loại tệ hơn im lặng);
   - **hiệu quả câu restate**: C5 cứu được mấy BR về sau bị bắn (nếu 0 thì phải xét lại việc dùng cả một câu cho restate);
   - **điểm quy đổi** theo +2/+1/−1, so với điểm nếu bỏ các test `EV ≤ 0`;
   - **độ chính xác ước token**: `max(từ×2,5; ký tự/2,2)` lệch bao nhiêu so với token thật, cho cả lượt hỏi và spec.
3. **Đo lại `battle/spec.nop.md` bằng token** (2.991 từ ≈ 7.500 token) — bản 08/09 **vượt hạn mức 6.000 khoảng 25%** theo luật mới. Nén theo `knowledge/30` §5 rồi chạy `/spec-review` để xem cắt gì thì mất luật gì; đây là bài tập nén rẻ nhất vì đã có ground truth.
4. **Hỏi BTC 2 ô đổi kế hoạch** (`knowledge/00` §A2): ảnh có tính vào 5.000 token không; AI Khách hàng còn mở sau 12:00 không. Hỏi trước 9:30 ngày thi, hoặc sớm hơn nếu có kênh liên hệ.
5. Luyện `/attack cheo` — cần 3 spec giả lập cho cùng một brief; `drill/selftest/` hiện chỉ có 1 spec, nên phải sinh thêm 2 bản có mức độ hở khác nhau.
6. Nếu BTC không cho dùng AI trong phòng: in `knowledge/20` §3 (5 câu soạn sẵn) + §4, `30` §1–§1b–§2 + §4, `05` §1+§M6, `32` §1–§3, `40` §2, `50` §1/§4/§4b/§6.

---

## Việc bị chặn / chờ

- **Bảy ô còn hở** ở `knowledge/00` §A2. Hai ô đầu (ảnh có tính token; AI còn mở sau 12:00) đổi kế hoạch buổi sáng; năm ô còn lại chỉ đổi cách tính điểm kỳ vọng.
- Vẫn chưa có văn bản về việc **được dùng AI riêng / mang tài liệu vào phòng thi** (`knowledge/00` §H câu 8, 9). Đang giả định **được** — đây là hackathon AI. Nếu không: kit vẫn dùng để diễn tập và in artifact mang vào (danh sách ở mục "Việc tiếp theo" số 6).
- **Model của 3 tác nhân là thông tin bảo mật, BTC sẽ không trả lời** (§H câu 7). Không còn là việc chờ — đã xử lý bằng cách luôn gọi reader 2 với model yếu hơn trong `/spec-review` khối D.

---

## Ghi chú

- `phan_tich_1/2/3.md` chờ xoá — mọi thứ cần giữ đã nằm trong `hackathon_descriptions.md` (Phụ lục B ghi lại nhật ký hợp nhất, không cần mở lại file gốc).
- Nguyên tắc khi mở rộng miền tri thức: **chỉ nạp tài liệu về đặc tả / test design / nghiệp vụ inventory**. Không nạp tài liệu bảo mật hệ thống — cuộc thi không có code để pentest, thí sinh không cần biết code.
