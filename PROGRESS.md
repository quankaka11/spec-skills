# PROGRESS — Chuẩn bị HBLAB AI Hackathon #02 (Spec Battle)

**Ngày thi:** 12/09/2026 · **Họp BTC (bắt buộc):** 09/09 · **Cập nhật note:** 04/09/2026

**Mục tiêu cuối:** tài liệu → PDF → LLM Wiki/PageIndex → bộ skills & agents dùng trong ngày thi.

---

## Pipeline & trạng thái

| # | Giai đoạn | Trạng thái | Ghi chú |
|---|---|---|---|
| 1 | Tổng hợp tài liệu nền | ✅ **Xong** 04/09 | → `hackathon_descriptions.md` |
| 2 | Thu thập PDF theo §7 | ⬜ Chưa | 40 tài liệu, 5 tầng ưu tiên |
| 3 | Dựng LLM Wiki / PageIndex | ⬜ Chưa | Cấu trúc thư mục ở §7.6 |
| 4 | Xây skills & agents | ⬜ Chưa | Blueprint 9 agent ở §8 |
| 5 | Dựng 15 artifact thi đấu | ⬜ Chưa | Checklist ở Phụ lục A |
| 6 | Diễn tập trọn 1 lượt | ⬜ Chưa | Dự kiến 10–11/09. **Không được bỏ** |

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

1. **Lấy ~12 PDF free trước** (đủ để bắt đầu index): EARS paper · NASA "How to Write a Good Requirement" · ISTQB FL v4 syllabus · IREB CPRE handbook · OMG DMN spec · Volere template · Bach HTSM · Hendrickson cheat sheet · Oracle/SAP ATP docs.
2. **Lấy tầng P0-DOMAIN** (#23–29 trong §7.3) — ROI cao nhất vì đề bài là "đặt giữ hàng". Web docs → in PDF.
3. Dựng cây `knowledge/` theo §7.6, chunk theo mục có số, gắn metadata `source · section_id · tier · use_for · vuln_type`.
4. Song song: dựng **Question Bank ~100 câu** (§3.5) và **Spec Template** (§4.7) — hai artifact này không cần chờ PDF.
5. Agent làm trước: `interrogator` → `spec-writer` → `red-teamer`. `executor-simulator` cần có sớm để chạy vòng lặp eval (§8).

---

## Việc bị chặn / chờ

- **§1.1, §1.5, §6.3 của `hackathon_descriptions.md` còn dữ liệu trống** — hạn mức token, công thức điểm, cơ chế kháng nghị, định dạng nộp. Chốt tại **họp 09/09**, sau đó phải quay lại cập nhật file. BTC đã lưu ý số liệu website còn tinh chỉnh.
- Chưa rõ **được mang tài liệu/dùng AI riêng trong phòng thi hay không** (§6.3 câu 8, 9) → ảnh hưởng trực tiếp tới việc bộ agent có dùng được trong ngày thi hay chỉ dùng để luyện trước. **Hỏi sớm.**

---

## Ghi chú

- `phan_tich_1/2/3.md` chờ xoá — mọi thứ cần giữ đã nằm trong `hackathon_descriptions.md` (Phụ lục B ghi lại nhật ký hợp nhất, không cần mở lại file gốc).
- Nguyên tắc khi mở rộng miền tri thức: **chỉ nạp tài liệu về đặc tả / test design / nghiệp vụ inventory**. Không nạp tài liệu bảo mật hệ thống — cuộc thi không có code để pentest, thí sinh không cần biết code.
