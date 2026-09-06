# PROGRESS — Chuẩn bị HBLAB AI Hackathon #02 (Spec Battle)

**Ngày thi:** 12/09/2026 · **Họp BTC (bắt buộc):** 09/09 · **Cập nhật note:** 04/09/2026

**Mục tiêu cuối:** tài liệu → PDF → LLM Wiki/PageIndex → bộ skills & agents dùng trong ngày thi.

---

## Pipeline & trạng thái

| # | Giai đoạn | Trạng thái | Ghi chú |
|---|---|---|---|
| 1 | Tổng hợp tài liệu nền | ✅ **Xong** 04/09 | → `hackathon_descriptions.md` |
| 2 | Thu thập PDF theo §7 | ✅ Một phần | 16 PDF trong `data/` (P0, P0-domain, P1) |
| 3 | Dựng LLM Wiki / PageIndex | ✅ Thay bằng `knowledge/` | Data nhỏ → chưng cất 7 file thay wiki/RAG |
| 4 | Xây skills & agents | ✅ **Xong** 05/09 | 6 skill + 2 agent trong `.claude/`, 7 file `knowledge/`; self-test `drill/selftest/KET-QUA-SELFTEST.md` |
| 5 | Dựng 15 artifact thi đấu | ◐ Phần lớn có trong `knowledge/` | Glossary (10 §2), question bank (20 §2–4), template (30 §1–2), attack checklist (50 §1/§4/§6), blacklist (40 §2), RTM/log mẫu (20 §5–6); còn thiếu: in ra giấy, câu hỏi BTC đã có ở 00 §H |
| 6 | Diễn tập trọn 1 lượt | ◐ Có `/drill`, chạy mini dở dang | Dự kiến 10–11/09 chạy `/drill` trọn vòng với đội thật. **Không được bỏ** |

---

## Self-test kit (05/09) — trạng thái để tiếp tục

**Đã xong:** `knowledge/00…50` (7 file, critic + fix); `.claude/skills/{elicit,spec-write,spec-review,attack,appeal,drill}/SKILL.md`; `.claude/agents/{executor,customer}.md`; `.claude/README.md`. Fixture self-test ở `drill/selftest/` (true-spec ẩn 49 luật/16 ⚠, RTM 33 dòng, spec lỗi 33 lỗi cài).

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

1. **Commit kit** (`.claude/`, `knowledge/`, `drill/selftest/`) để đồng đội pull; mở phiên Claude Code mới sau khi pull.
2. **Sau họp 09/09:** điền các ô "CHỜ 09/09" trong `knowledge/00-luat-choi.md` §A (TOKEN_MAX, công thức điểm, cách đếm từ, định dạng nộp, được dùng AI/tài liệu không). Nếu BTC không cho dùng AI trong phòng: in `knowledge/20` §2–§4, `30` §1–§2, `40` §2, `50` §1/§4/§6 mang vào.
3. **10–11/09: `/drill` trọn vòng với đội thật** (4 lượt hỏi, tự-động hoặc thủ-công), đo TRÚNG/lỗ hổng hỏi/lỗ hổng viết; sửa `knowledge/20`, `30` theo ket-qua.md.
4. Luyện /attack và /spec-review trên fixture sẵn `drill/selftest/flawed/spec.md` (ground truth ở `planted.md`).
5. Tùy chọn: chạy lại T9 drill mini đầy đủ; đổi `model` của agent `executor` sang model yếu hơn nếu biết Executor của BTC.

---

## Việc bị chặn / chờ

- **§1.1, §1.5, §6.3 của `hackathon_descriptions.md` còn dữ liệu trống** — hạn mức token, công thức điểm, cơ chế kháng nghị, định dạng nộp. Chốt tại **họp 09/09**, sau đó phải quay lại cập nhật file. BTC đã lưu ý số liệu website còn tinh chỉnh.
- Chưa rõ **được mang tài liệu/dùng AI riêng trong phòng thi hay không** (§6.3 câu 8, 9) → ảnh hưởng trực tiếp tới việc bộ agent có dùng được trong ngày thi hay chỉ dùng để luyện trước. **Hỏi sớm.**

---

## Ghi chú

- `phan_tich_1/2/3.md` chờ xoá — mọi thứ cần giữ đã nằm trong `hackathon_descriptions.md` (Phụ lục B ghi lại nhật ký hợp nhất, không cần mở lại file gốc).
- Nguyên tắc khi mở rộng miền tri thức: **chỉ nạp tài liệu về đặc tả / test design / nghiệp vụ inventory**. Không nạp tài liệu bảo mật hệ thống — cuộc thi không có code để pentest, thí sinh không cần biết code.
