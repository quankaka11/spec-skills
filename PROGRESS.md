# PROGRESS — Chuẩn bị HBLAB AI Hackathon #02 (Spec Battle)

**Ngày thi:** 12/09/2026 · **Họp BTC:** 09/09 ✅ đã họp · **Cập nhật note:** 11/09/2026

**Mục tiêu cuối:** tài liệu → PDF → LLM Wiki/PageIndex → bộ skills & agents dùng trong ngày thi.

---

## Pipeline & trạng thái

| # | Giai đoạn | Trạng thái | Ghi chú |
|---|---|---|---|
| 1 | Tổng hợp tài liệu nền | ✅ **Xong** 04/09 | → `hackathon_descriptions.md` |
| 2 | Thu thập PDF theo §7 | ✅ Xong, **đã xoá 11/09** | 16 PDF đã chưng cất hết vào `knowledge/` ở giai đoạn 3; xoá để repo gọn, không skill nào đọc `data/` |
| 3 | Dựng LLM Wiki / PageIndex | ✅ Thay bằng `knowledge/` | Data nhỏ → chưng cất 10 file thay wiki/RAG |
| 4 | Xây skills & agents | ✅ Xong 05/09 · ✅ Cập nhật 11/09 (cấu trúc BTC) · ✅ **Hợp nhất nhánh `improve-spec`** | **7** skill (thêm `/frame`) + 2 agent trong `.claude/`, **10** file `knowledge/` (thêm `05` mô hình bài toán, `32` cấu trúc spec BTC, `33` cổng khả thi) |
| 5 | Dựng 15 artifact thi đấu | ◐ Phần lớn có trong `knowledge/` | Glossary (10 §2), question bank (20 §2–4), **5 câu soạn sẵn (20 §3)**, template + catch-all 0.1–0.15 (30 §1–2), ngân sách token (32 §5), **cổng F + bảng KHÔNG-được-hứa (33 §1–2)**, **12 ca suy biến + 6 kẻ lạm dụng (05 §M5–M6)**, attack checklist (50 §1/§4/§6), blacklist 22 nhóm (40 §2), RTM/log mẫu (20 §5–6); còn thiếu: **in ra giấy** |
| 6 | Diễn tập trọn 1 lượt | ❗ **Chưa chạy trên kit hợp nhất** | `/drill` trọn vòng: true-spec đủ 10 mục BTC + 5 câu hỏi + cổng F. **Việc quan trọng nhất còn lại** |

---

## Self-test 05/09 — đã bỏ

Bộ self-test T1–T9 và fixture `drill/selftest/` dựng theo **template 11 mục cũ**, nên mọi số đo (T5 93/100, T6 recall 30/33, T7 5/5 TRÚNG) không còn phản ánh kit hiện tại. Fixture chưa từng commit và đã bỏ.

Hai bài học từ đợt đó vẫn giữ nguyên giá trị và **đã nằm trong skill**: `/attack` bước 10 (giữ ứng viên khi Executor khác đáp án, kể cả ĐỘ PHỦ = ĐỦ ⇒ spec viết trái specs thật); `/elicit` nạp (tách câu trả lời dạng liệt kê thành từng dòng RTM, tránh bị catch-all 0.4/0.5 xử sai).

---

## 11/09 — BTC gửi tài liệu cấu trúc spec ("Spec Battle Anatomy")

Nguồn: `knowledge/Spec Battle Anatomy.html` (nội dung thật nằm trong iframe `Spec Battle Anatomy_files/saved_resource.html`). Chỉ nói về **cấu trúc spec**, không đổi tham số nào. *(Ghi chú thêm sau khi hợp nhất: tham số đúng là bộ đã chốt tại họp 09/09 — ≤6.000 token, 5 câu hỏi, điểm +2/+1/−1. Con số "3.000 từ" ghi ở đây là của bản trước họp.)*

**Thay đổi bản chất:** spec nộp phải theo **10 mục kiểu thiết kế Nhật** — Basic design 基本設計 (1–5, *theo màn hình*) · Detailed design 詳細設計 (6–9, *theo chức năng*) · Technical spec 技術仕様 (10). Template 11 mục cũ (thuần luật nghiệp vụ + state machine) **không còn là cấu trúc nộp** — đã ánh xạ vào 10 mục, giữ toàn bộ kỹ thuật cũ (catch-all, EARS, decision table, mã BR, RTM).

**Bốn vùng hoàn toàn mới phải hỏi AI Khách hàng:** mục 2 item màn hình · mục 3 event · mục 4 validation & message lỗi **nguyên văn** · mục 9 API/tích hợp.

| File | Đã sửa |
|---|---|
| `knowledge/32-cau-truc-spec-btc.md` | **MỚI** — 10 mục (nội dung + hình thức bảng BTC yêu cầu), "ba thứ" của mỗi logic ở mục 6, quy tắc cũ nào đổi, ánh xạ 11→10 mục, ngân sách từ, thứ tự cắt |
| `knowledge/30-viet-spec.md` | §1 template viết lại theo 10 mục; §2 catch-all chuyển về mục 1.x; §4 quy tắc 13 (cho phép mô tả UI ở mục 2–5, TBD → bảng 7.4) và 15 (4 → **7 chiều** kết quả); §5 bỏ "không mô tả màn hình"; §6 lịch viết mới; §7 cổng chất lượng 11 → **17 dòng** |
| `knowledge/20-ngan-hang-cau-hoi.md` | Thêm **N15–N19** (49 câu: item màn hình, event, validation/message, API, ràng buộc/chưa chốt) → 152 câu/19 nhóm; thêm **Lượt 7**; ngân sách token 7 lượt; thứ tự chạy L1→L2→L3→**L7**→L4→L5→L6 |
| `knowledge/40-tu-mo-ho.md` | Thêm **S22–S28** (thiếu mục · message không nguyên văn · thiếu FE/BE · bảng Case thiếu loại case · thiếu 5 gạch · không phân biệt guest · TBD rải rác); thang ưu tiên sửa thêm mức 0 và 2b |
| `knowledge/50-tan-cong.md` | 15 → **18 loại lỗ hổng** (#16 message, #17 guest, #18 trạng thái UI — tỷ lệ TRÚNG rất cao); quy trình soi 10 phút đi theo 10 mục; thêm probe **P43–P48** |
| `.claude/skills/spec-write` | Bước 1–14 viết lại theo 10 mục; thứ tự viết mục 1 → 6 → 4 → 8 → 2 → 3 → 7 → 9/10/5; chế độ nén cắt theo 32 §6 |
| `.claude/skills/spec-review` | Thêm khối **B0** (đủ 10 mục, chạy trước mọi kiểm); B kiểm mục 6 (3 loại case + 5 gạch), mục 4 (message nguyên văn), mục 2/8 (guest), mục 7 (4 ca BTC + bảng 7.4); eval set bắt buộc phủ #16/#17/#18 |
| `.claude/skills/attack` | Quy trình 10 phút theo 10 mục; ưu tiên chọn test: #15 → **#16/#17** → … ; bảng "10 mục BTC của spec đối thủ" trong output |
| `.claude/skills/elicit` | Đọc thêm knowledge/32; lượt 7 bắt buộc; chèn N17/N15-06 nếu tới 10:45 chưa hỏi message/guest; **bảng phủ 10 mục** khi nạp |
| `.claude/skills/drill` | true-spec phải có dữ kiện cho cả 10 mục + ≥2 luật ⚠ vùng message/guest/nút; ket-qua.md thêm bảng phủ 10 mục |
| `.claude/agents/executor.md` | Trả lời phải gồm message nguyên văn / trạng thái nút; thêm ví dụ 0 (spec ghi "thông báo lỗi phù hợp" → Executor tự bịa message) |
| `README.md`, `.claude/README.md` | Mô tả cấu trúc 10 mục, 7 lượt hỏi, 8 file knowledge |

Sơ đồ trong spec dùng **Mermaid** (knowledge/32 §7): `block-beta` wireframe (mục 5) · `stateDiagram-v2` state machine (mục 6) · `sequenceDiagram` flow end-to-end (mục 6) · `flowchart LR` sơ đồ hệ thống (mục 9). Quy tắc cứng: **sơ đồ không thay bảng** — sơ đồ chỉ vẽ chuyển hợp lệ, còn ô `Từ chối 0.5` / `KHL` (chỗ Executor đoán sai) chỉ có trong bảng; hết chỗ thì bỏ sơ đồ, giữ bảng. Kiểm S29/S30 trong knowledge/40 bắt lỗi này, `/attack` coi "chỉ có sơ đồ, không có bảng" là ứng viên hạng A.

---

## Hợp nhất nhánh `improve-spec` vào `cunx`

Hai nhánh chạy song song từ `245caa5` và **mỗi nhánh mới hơn ở một mặt khác nhau**, nên hợp nhất là *cộng hai lớp*, không phải chọn một bên:

- `improve-spec` mang **tham số luật thi đã chốt tại họp 09/09** mà `cunx` còn để trống ("CHỜ 09/09"): 5 câu hỏi / 1 câu mỗi lượt / 5.000 token / AI Khách hàng **không có memory**; spec **≤6.000 token** (không phải 3.000 từ); điểm **+2/+1/−1**; kháng nghị **chỉ cho ca CÔNG bị VÔ HIỆU**; spec đối thủ **tải về được**.
- `cunx` mang **cấu trúc spec 10 mục** của tài liệu BTC 11/09 (đến *sau* họp, không đổi tham số nào ở §A) cùng toàn bộ phần phục vụ nó: N15–N19, S22–S30, loại lỗ hổng #16–#18, probe P43–P48, Mermaid.
- `improve-spec` còn mang **trục kiểm nội dung** mà `cunx` hoàn toàn không có: mô hình bài toán, cổng khả thi, mô hình lạm dụng. Bằng chứng cần nó: bản `spec.nop.md` 08/09 đạt **mọi** cổng hình thức (0 hit lint Cao, 36/36 ô bảng, 15/15 eval "ĐỦ", 2.991/3.000 từ) mà vẫn có **7 lỗi nội dung mức Cao**, 3 trong đó phá thẳng mục tiêu của brief.

**Ba việc đánh số lại phải làm thủ công** (hai nhánh dùng cùng mã cho nội dung khác nhau — merge máy móc sẽ làm sai tham chiếu một cách âm thầm):

| Mã | `cunx` giữ nguyên | `improve-spec` đánh lại thành |
|---|---|---|
| `knowledge/40` §3 | S22–S30 = cấu trúc 10 mục BTC | **S31–S39** = kiểm nội dung |
| `knowledge/50` §1 | #16 message · #17 guest · #18 trạng thái UI | **#19–#24** = bất khả thi · tự đánh bại mục tiêu · hạn mức không cưỡng chế · thiếu luật thất bại phụ thuộc ngoài · phạm vi NGOÀI rộng hơn brief · lệch đồng thuận chéo |
| `knowledge/50` §5 | P43–P48 = message/guest/UI | **P49–P58** = khả thi & mục tiêu |

Tên file cũng đụng nhau: cả hai nhánh đều có `knowledge/32-*`. Giữ `32-cau-truc-spec-btc.md` (cấu trúc BTC), cổng khả thi đổi thành **`33-kha-thi-van-hanh.md`**.

| File | Xử lý khi hợp nhất |
|---|---|
| `knowledge/00-luat-choi.md` | Lấy §A/§A1/§A2/§D/§D1/§H của improve-spec (tham số đã chốt, cách đo token, số học điểm); **thêm** dòng cấu trúc 10 mục + 2 ô hỏi BTC về mục 5/Mermaid + ghi chú thứ tự hai nguồn tham số |
| `knowledge/05-hieu-bai-toan.md` | **MỚI** — nguyên từ improve-spec (6 khối M1–M6, bảng Mục tiêu↔Luật, 12 ca suy biến) |
| `knowledge/20-ngan-hang-cau-hoi.md` | §1 → 10 quy tắc hỏi khi chỉ có 5 câu; §2 → **166 câu / 20 nhóm** (N0 mới + giữ trọn N1–N19); §3 → **5 câu C1–C5**, trong đó **C2 thêm cột hiển thị/nút** và **C4 gộp message lỗi nguyên văn + guest** (giải bài toán "5 câu không đủ chỗ cho 4 vùng BTC"); §4 → 31 phát biểu; §5 → RTM hai loại dòng `A-xx`/`G-xx` |
| `knowledge/30-viet-spec.md` | Giữ template 10 mục, **đổi ngân sách từ → token** (3.000 từ ≈ 7.500 token, vượt hạn mức); thêm catch-all **0.11–0.15**, §1b mặc định ngành, quy tắc **18–24**, kinh tế token, lịch viết 10:20–11:52, cổng chất lượng **23 dòng** |
| `knowledge/32-cau-truc-spec-btc.md` | §5 ngân sách đổi sang token (tổng ~5.580) |
| `knowledge/33-kha-thi-van-hanh.md` | **MỚI** — cổng F 8 kiểm + bảng "KHÔNG được hứa" + 3 mẫu viết lại + chi phí ẩn |
| `knowledge/40-tu-mo-ho.md` | Thêm **nhóm lint 17–22** (nội dung) + **S31–S39**; thang ưu tiên thêm bậc 2c–2f; đếm token |
| `knowledge/50-tan-cong.md` | **24 loại lỗ hổng**; quy tắc 9 gói bằng chứng 3 mức + quy tắc 10 bỏ slot; **§4b đồng thuận chéo**; cổng F grep trong quy trình soi; **P49–P58**; rubric thêm luật 7 (EV) và 8; §7 thay khối kháng nghị bằng bản 1-loại-ca + mẫu text ≤150 từ |
| `.claude/skills/frame/` | **MỚI** — nguyên từ improve-spec, thêm bước 8b đối chiếu mô hình ↔ 10 mục BTC |
| `.claude/skills/elicit` | Khung 5 câu + sổ hạn mức + cổng 5 kiểm tra + `G-xx`; **giữ** bảng phủ 10 mục BTC của cunx |
| `.claude/skills/spec-write` | **Giữ trọn** 10 mục BTC của cunx; thêm điều kiện vào `/frame`, nhãn mục tiêu `[M-x]`/`[⚡M-x]`, cổng F, bảng xếp hạng rủi ro → C5, đếm token bản nộp. Catch-all 0.11–0.15 chuyển từ §0 sang **mục 1.x** |
| `.claude/skills/spec-review` | **Giữ** khối B0 (10 mục) + 5 kiểm chuyên biệt; thêm **khối G** (8 kiểm nội dung), **eval 2 reader** (reader 2 `model: haiku`), 3 nhãn `SỬA`/`HỎI`/`RỦI RO ĐÃ BIẾT` |
| `.claude/skills/attack` | **Giữ** quy trình 10 mục + quy tắc Mermaid-không-bảng; thêm chế độ `cheo`, cổng F grep, chấm **EV**, gói bằng chứng trích sẵn 14:30, kiểm chéo spec mình |
| `.claude/skills/appeal` | **Thay** bằng cơ chế 1-loại-ca + text ≤150 từ (bản cunx sai luật 09/09); giữ 2 kỹ thuật Grep của cunx |
| `.claude/skills/drill` | Bỏ tham số `[lượt]`; thêm mục hạn mức diễn tập, `/frame`, C1–C4 từng câu, C5 restate, cổng F; **giữ** yêu cầu true-spec phủ 10 mục + chỉ số "Phủ 10 mục BTC" |
| `.claude/agents/customer.md` | Lấy bản improve-spec (một lượt = một câu, không memory, **hệ số token ×2,5** thay ×1,5 vốn ước thiếu ~40%, câu hỏi vận hành cuộc thi = bảo mật) |
| `.claude/agents/executor.md` | **Giữ** bản cunx (message nguyên văn + trạng thái nút + Ví dụ 0); **thêm** quy tắc 4b xử lý sơ đồ mermaid chỏi bảng |

Không lấy gì từ `improve-spec` ở: template 11 mục §0–§10, ngân hàng N1–N14 (bản đã xoá N15–N19), quy tắc 13/15 (4 chiều), "không mô tả màn hình" — `cunx` đúng theo yêu cầu BTC ở cả bốn chỗ.

**Việc cần làm ngay (chưa xong):**
1. **`/drill` trọn vòng trên kit hợp nhất** — true-spec đủ 10 mục BTC, 5 câu hỏi thật, cổng F. Kit **chưa từng chạy thử** sau khi hợp nhất. Việc quan trọng nhất còn lại.
2. Hỏi BTC 9 ô ở `knowledge/00` §A2 — hai ô đổi kế hoạch buổi sáng (ảnh có tính token không; AI còn mở sau 12:00 không) hỏi **trước 9:30**; hai ô về mục 5 (Mermaid `block-beta` có được nhận thay Figma không; sơ đồ có tính token như văn bản không) hỏi **trước 10:20**.

---

## Đã làm

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

0. **`/drill` trọn vòng trên kit hợp nhất** — ưu tiên cao nhất, kit chưa từng chạy thử sau khi hợp nhất hai nhánh.
1. **Commit kit** (`.claude/`, `knowledge/`, `data/btc/`) để đồng đội pull; mở phiên Claude Code mới sau khi pull (skill `/frame` và agent `customer` mới chỉ nạp lúc khởi động phiên).
2. **Hỏi BTC 9 ô còn hở** ở `knowledge/00` §A2 — xem thứ tự ưu tiên và hạn giờ ở mục "Hợp nhất nhánh" trên. Nếu BTC không cho dùng AI trong phòng: in danh sách artifact ở `.claude/README.md` mục "Còn hở sau họp 09/09".
3. **`/drill` trọn vòng với đội thật** (5 câu hỏi thật, tự-động hoặc thủ-công), đo điểm quy đổi + 10 chỉ số; sửa `knowledge/20`, `30`, `33` theo ket-qua.md. Đặc biệt xem chỉ số **"Độ chính xác ước token"** — lệch >20% thì chỉnh hệ số ở `00` §A1.
4. Luyện /attack và /spec-review trên fixture do `/drill` sinh ra (ground truth ở `true-spec.answers.md`).
5. Tùy chọn: đổi `model` của agent `executor` sang model yếu hơn nếu biết Executor của BTC.

---

## Việc bị chặn / chờ

- **`hackathon_descriptions.md` §1.1, §1.5, §6.3 vẫn còn số liệu trước họp 09/09** — hạn mức token, công thức điểm, cơ chế kháng nghị, định dạng nộp. Nguồn đúng hiện nay là `knowledge/00-luat-choi.md` §A; file nền chưa được cập nhật lại. Khi hai file chỏi nhau, **lấy `knowledge/00` §A**.
- Chưa rõ **được mang tài liệu/dùng AI riêng trong phòng thi hay không** (§6.3 câu 8, 9) → ảnh hưởng trực tiếp tới việc bộ agent có dùng được trong ngày thi hay chỉ dùng để luyện trước. **Hỏi sớm.**

---

## Ghi chú

- `phan_tich_1/2/3.md` đã xoá — mọi thứ cần giữ đã nằm trong `hackathon_descriptions.md` (Phụ lục B ghi lại nhật ký hợp nhất).
- Tài liệu BTC 11/09 bản trích nguyên văn giữ ở `data/btc/spec-battle-anatomy.txt`; bản chưng cất để skill dùng là `knowledge/32-cau-truc-spec-btc.md`. Trang HTML đã lưu (~750KB JS/CSS) đã xoá.
- **16 PDF nguồn đã xoá 11/09** (repo 99MB → 44MB). An toàn vì không skill/agent nào trỏ tới `data/`: `/spec-write` liệt kê đích danh 5 file `knowledge/` phải đọc, `allowed-tools` của cả 6 skill không có công cụ mở PDF, agent `executor` bị khoá Read đúng một lần vào file spec. Tri thức đã nằm trong `knowledge/` dưới dạng dẫn nguồn (`[ISTQB 4.2.4]`, `[DMN 8.2.11]`, `[Berry §5.4.7 E244]`) — chú thích xuất xứ, không phải đường dẫn máy đọc. Lịch sử git vẫn còn file nếu cần lấy lại: `git show HEAD:data/<tên>.pdf > /tmp/x.pdf`. Danh sách 16 PDF: `hackathon_descriptions.md` §7.
- Nguyên tắc khi mở rộng miền tri thức: **chỉ nạp tài liệu về đặc tả / test design / nghiệp vụ inventory**. Không nạp tài liệu bảo mật hệ thống — cuộc thi không có code để pentest, thí sinh không cần biết code.
