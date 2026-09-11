# PROGRESS — Chuẩn bị HBLAB AI Hackathon #02 (Spec Battle)

**Ngày thi:** 12/09/2026 · **Họp BTC (bắt buộc):** 09/09 · **Cập nhật note:** 11/09/2026

**Mục tiêu cuối:** tài liệu → PDF → LLM Wiki/PageIndex → bộ skills & agents dùng trong ngày thi.

---

## Pipeline & trạng thái

| # | Giai đoạn | Trạng thái | Ghi chú |
|---|---|---|---|
| 1 | Tổng hợp tài liệu nền | ✅ **Xong** 04/09 | → `hackathon_descriptions.md` |
| 2 | Thu thập PDF theo §7 | ✅ Xong, **đã xoá 11/09** | 16 PDF đã chưng cất hết vào `knowledge/` ở giai đoạn 3; xoá để repo gọn, không skill nào đọc `data/` |
| 3 | Dựng LLM Wiki / PageIndex | ✅ Thay bằng `knowledge/` | Data nhỏ → chưng cất 7 file thay wiki/RAG |
| 4 | Xây skills & agents | ✅ Xong 05/09 · ✅ **Cập nhật 11/09** theo cấu trúc BTC | 6 skill + 2 agent trong `.claude/`, **8** file `knowledge/` (thêm `32` cấu trúc spec BTC) |
| 5 | Dựng 15 artifact thi đấu | ◐ Phần lớn có trong `knowledge/` | Glossary (10 §2), question bank (20 §2–4), template (30 §1–2), attack checklist (50 §1/§4/§6), blacklist (40 §2), RTM/log mẫu (20 §5–6); còn thiếu: in ra giấy, câu hỏi BTC đã có ở 00 §H |
| 6 | Diễn tập trọn 1 lượt | ❗ **Chưa chạy trên cấu trúc mới** | `/drill` trọn vòng với true-spec đủ 10 mục. **Việc quan trọng nhất còn lại** |

---

## Self-test 05/09 — đã bỏ

Bộ self-test T1–T9 và fixture `drill/selftest/` dựng theo **template 11 mục cũ**, nên mọi số đo (T5 93/100, T6 recall 30/33, T7 5/5 TRÚNG) không còn phản ánh kit hiện tại. Fixture chưa từng commit và đã bỏ.

Hai bài học từ đợt đó vẫn giữ nguyên giá trị và **đã nằm trong skill**: `/attack` bước 10 (giữ ứng viên khi Executor khác đáp án, kể cả ĐỘ PHỦ = ĐỦ ⇒ spec viết trái specs thật); `/elicit` nạp (tách câu trả lời dạng liệt kê thành từng dòng RTM, tránh bị catch-all 0.4/0.5 xử sai).

---

## 11/09 — BTC gửi tài liệu cấu trúc spec ("Spec Battle Anatomy")

Nguồn: `knowledge/Spec Battle Anatomy.html` (nội dung thật nằm trong iframe `Spec Battle Anatomy_files/saved_resource.html`). Chỉ nói về **cấu trúc spec**, không đổi tham số (3.000 từ, token, điểm số vẫn như §A).

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

**Việc cần làm ngay (chưa xong):**
1. **`/drill` trọn vòng** với true-spec có đủ 10 mục — kit **chưa từng chạy thử** trên cấu trúc mới. Việc quan trọng nhất còn lại trước ngày thi.
2. Hỏi BTC: mục 5 (Design/Wireframe) nộp thế nào khi không có Figma — **Mermaid có được chấp nhận không**? Ảnh/sơ đồ có tính vào 3.000 từ không? (nhập vào §A cùng các ô CHỜ 09/09.)

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

0. **`/drill` trọn vòng trên cấu trúc 10 mục mới** — ưu tiên cao nhất, kit chưa từng chạy thử với cấu trúc BTC mới.
1. **Commit kit** (`.claude/`, `knowledge/`, `data/btc/`) để đồng đội pull; mở phiên Claude Code mới sau khi pull.
2. **Sau họp 09/09:** điền các ô "CHỜ 09/09" trong `knowledge/00-luat-choi.md` §A (TOKEN_MAX, công thức điểm, cách đếm từ, định dạng nộp, được dùng AI/tài liệu không). Nếu BTC không cho dùng AI trong phòng: in `knowledge/20` §2–§4, `30` §1–§2, `40` §2, `50` §1/§4/§6 mang vào.
3. **10–11/09: `/drill` trọn vòng với đội thật** (4 lượt hỏi, tự-động hoặc thủ-công), đo TRÚNG/lỗ hổng hỏi/lỗ hổng viết; sửa `knowledge/20`, `30` theo ket-qua.md.
4. Luyện /attack và /spec-review trên fixture do `/drill` sinh ra (ground truth ở `true-spec.answers.md`).
5. Tùy chọn: đổi `model` của agent `executor` sang model yếu hơn nếu biết Executor của BTC.

---

## Việc bị chặn / chờ

- **§1.1, §1.5, §6.3 của `hackathon_descriptions.md` còn dữ liệu trống** — hạn mức token, công thức điểm, cơ chế kháng nghị, định dạng nộp. Chốt tại **họp 09/09**, sau đó phải quay lại cập nhật file. BTC đã lưu ý số liệu website còn tinh chỉnh.
- Chưa rõ **được mang tài liệu/dùng AI riêng trong phòng thi hay không** (§6.3 câu 8, 9) → ảnh hưởng trực tiếp tới việc bộ agent có dùng được trong ngày thi hay chỉ dùng để luyện trước. **Hỏi sớm.**

---

## Ghi chú

- `phan_tich_1/2/3.md` đã xoá — mọi thứ cần giữ đã nằm trong `hackathon_descriptions.md` (Phụ lục B ghi lại nhật ký hợp nhất).
- Tài liệu BTC 11/09 bản trích nguyên văn giữ ở `data/btc/spec-battle-anatomy.txt`; bản chưng cất để skill dùng là `knowledge/32-cau-truc-spec-btc.md`. Trang HTML đã lưu (~750KB JS/CSS) đã xoá.
- **16 PDF nguồn đã xoá 11/09** (repo 99MB → 44MB). An toàn vì không skill/agent nào trỏ tới `data/`: `/spec-write` liệt kê đích danh 5 file `knowledge/` phải đọc, `allowed-tools` của cả 6 skill không có công cụ mở PDF, agent `executor` bị khoá Read đúng một lần vào file spec. Tri thức đã nằm trong `knowledge/` dưới dạng dẫn nguồn (`[ISTQB 4.2.4]`, `[DMN 8.2.11]`, `[Berry §5.4.7 E244]`) — chú thích xuất xứ, không phải đường dẫn máy đọc. Lịch sử git vẫn còn file nếu cần lấy lại: `git show HEAD:data/<tên>.pdf > /tmp/x.pdf`. Danh sách 16 PDF: `hackathon_descriptions.md` §7.
- Nguyên tắc khi mở rộng miền tri thức: **chỉ nạp tài liệu về đặc tả / test design / nghiệp vụ inventory**. Không nạp tài liệu bảo mật hệ thống — cuộc thi không có code để pentest, thí sinh không cần biết code.
