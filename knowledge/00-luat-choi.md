*Đọc đầu tiên: trước họp BTC 09/09 và 9:00 ngày 12/09.*

# 00 — Luật chơi & tham số

## A. Tham số

BTC ghi số liệu đang được tinh chỉnh; mọi ô phải được xác nhận lại tại họp 09/09 [HD đầu file], điền ô "CHỜ" trước. `W*` = Website 04/09 — tái xác nhận 09/09.

| Tham số | Giá trị | Trạng thái |
|---|---|---|
| Giới hạn từ spec | ≤ 3.000 từ; khóa 12:00 | W* [HD 1.3] |
| Test / spec đối thủ | 5 | W* |
| Spec đối thủ | 3 spec → 15 test bắn; 60 test/đợt chạy | W* (pool 4 đội = suy luận, hỏi câu 6) |
| Xếp hạng | Có thể so toàn giải → cần điểm phòng thủ tuyệt đối, không chỉ hơn pool | Suy luận [HD 1.3]; câu 6 |
| Kháng nghị | ≤ 3 ca/đội; trọng tài xử có/không | W* |
| Thời gian THỦ / CÔNG | 2h30 (9:30–12:00) / 2h (13:00–15:00) | W* |
| Đội hình / đối thủ | 2–3 người; phân ngẫu nhiên, 20–30 đội | W* [HD 1.1] |
| Token AI Khách hàng (TOKEN_MAX) | Chung hỏi + trả lời; — | CHỜ 09/09 (câu 1) |
| Công thức điểm (trọng số công/thủ, điểm TRÚNG, trừ VÔ HIỆU?) | — | CHỜ 09/09 (câu 5) |
| Định dạng nộp spec (từ trong bảng?) / test (độ dài, nhiều bước?) | — | CHỜ 09/09 (câu 3, 10) |
| Lượt hỏi riêng; hình ảnh; xếp giải pool/toàn giải; AI riêng; tài liệu mang vào; model 3 tác nhân | — | CHỜ 09/09 (câu 2, 4, 6–9) |

## B. Agenda 12/09

| Giờ | Pha | Việc | Output phải có |
|---|---|---|---|
| 9:00–9:30 | Nhận brief | Đọc brief; **dựng mô hình bài toán (`/frame`, knowledge/05)**; vào vai (§I) | `mo-hinh-bai-toan.md` đủ M1–M6; danh sách mâu thuẫn nội tại của brief; câu hỏi P0 xếp ưu tiên |
| 9:30–12:00 | THỦ | Hỏi đóng (mục tiêu & ràng buộc trước tham số); đánh ⚠; **lượt restate**; viết spec; RTM ngược; **cổng F + bảng Mục tiêu↔Luật**; nộp trước 11:50 | Spec đã nộp ≤2.700 từ; log; RTM đủ; danh sách ⚠; 0 ✗ ở F1/F5 |
| 12:00–13:00 | Nghỉ | Chuyển ⚠ thành tình huống | ≥ 15 tình huống ứng viên |
| 13:00–15:00 | CÔNG | Đọc 3 spec; tìm im lặng trùng ⚠; kiểm phạm vi | 15 test + đáp án kỳ vọng + căn cứ phạm vi |
| 15:00–16:00 | Chạy 60 test | Ghi TRÚNG/VÔ HIỆU đáng ngờ | ≤ 3 ứng viên kháng nghị kèm mã luật |
| 16:00–17:00 | Kháng nghị | Dẫn mã luật + lý do đối chiếu | Kết quả từng ca; 17:00 trao giải |

## C. Ba tác nhân AI [HD 1.4]

| Tác nhân | Biết | KHÔNG biết / không làm | Hệ quả |
|---|---|---|---|
| AI Khách hàng | Specs thật; pha chấm: quyết phạm vi, đáp án chuẩn | Không tự kể; chỉ trả lời điều được hỏi; hạn mức token | Không hỏi = spec trống. Hỏi đóng, mật độ cao; đánh ⚠ trả lời bất ngờ |
| AI Executor | CHỈ spec bị bắn | Brief, specs thật, hội thoại, lẽ thường | Spec tự chứa 100%; cấm "như thông lệ". Im lặng → đoán theo prior |
| AI So khớp | Đáp án chuẩn + trả lời Executor | Không so từ khóa; so ý nghĩa | Đúng ý là đủ; mơ hồ vẫn bị bắt |

## D. Ba trạng thái [HD 1.4]

- TRÚNG — Executor lệch specs thật vì spec hở → CÔNG ghi điểm, THỦ mất điểm.
- TRƯỢT — Executor khớp specs thật → THỦ bảo toàn, CÔNG mất lượt.
- VÔ HIỆU — ngoài phạm vi specs thật → CÔNG đốt test.

Ví dụ BTC (app nghỉ phép): TRÚNG — "Nghỉ qua ngày lễ tính phép sao?" spec im lặng, Executor tính cả lễ. TRƯỢT — "Đơn bị từ chối sửa & nộp lại?" spec rõ. VÔ HIỆU — "Bảo hiểm khi nghỉ không lương?" ngoài phạm vi.

## F. Năm nhận định cấu trúc [HD 2.1]

1. Executor mù bối cảnh ⇒ spec tự chứa 100%; "ai chả biết" là lỗ hổng.
2. AI Khách hàng chỉ trả lời khi hỏi ⇒ question bank soạn trước là tài sản số 1.
3. Token chung hỏi + trả lời ⇒ hỏi đóng dạng bảng; câu mở chỉ để lấy danh sách đóng (liệt kê tên, không mô tả) — 20 §1 rule 3. Bắn 3 spec, giữ 1 ⇒ cạn thời gian → ưu tiên phòng thủ [HD 2.2].
4. VÔ HIỆU là phạt kép ⇒ bắn core flow + ngoại lệ hiển nhiên; tránh biên xa (bảo hiểm, kế toán, thuế). Sau buổi sáng đã biết specs thật → soi chỗ đối thủ chưa hỏi [HD 2.2].
5. Ranh giới tri thức: biết mà không viết = bề mặt tấn công ⇒ đọc lại spec như người chưa nghe brief. Lý do đối chiếu được lưu → xin xem trước kháng nghị (câu 12) [HD 2.2].

## G. Quy tắc vàng & RTM ngược

**CÔNG [HD 2.3]:** Bắn chỗ specs thật phản trực giác mà spec đối thủ im lặng. Executor đoán lẽ thường: = specs thật → TRƯỢT; ≠ → TRÚNG. Danh sách ⚠ sáng = băng đạn chiều.

**THỦ [HD 2.4]:** Mọi trả lời ⚠ truy vết tới một luật có mã trong spec; ô trống = chắc chắn bị bắn. RTM là bằng chứng kháng nghị.

Mẫu RTM ngược đầy đủ (7 cột, timestamp) + quy tắc điền: knowledge/20-ngan-hang-cau-hoi.md §5.

## H. 14 câu hỏi cho BTC 09/09 [HD 6.3]

1. Hạn mức token AI Khách hàng? Tính cả input + output? Hiển thị token còn lại?
2. Giới hạn lượt hỏi riêng ngoài token?
3. Spec nộp định dạng gì? Bảng, sơ đồ được không? Từ trong bảng tính vào 3.000?
4. Hạn mức hình ảnh? Executor "đọc" được hình?
5. Công thức điểm: trọng số CÔNG/THỦ? TRÚNG mấy điểm? VÔ HIỆU bị trừ?
6. Xếp giải theo pool 4 đội hay toàn giải?
7. Model nào đóng 3 tác nhân? Executor được prompt thế nào?
8. Được dùng AI riêng để viết và soi spec?
9. Được mang tài liệu chuẩn bị trước (template, checklist, question bank)? File hay giấy?
10. Test nộp định dạng nào? Giới hạn độ dài? Test nhiều bước?
11. Spec đối thủ cấp dạng nào, lúc nào? Tải về được?
12. Kháng nghị: form hay miệng? Được xem lý do đối chiếu của AI So khớp trước?
13. Được xem lại hội thoại với AI Khách hàng sau khóa spec?
14. Được sửa test sau khi nộp, trước 15:00?

## I. Phân vai & owner [HD 6.1]

Mỗi artifact một owner.

| Vai | 9:00–9:30 (mô hình) | Sáng (THỦ) | Chiều (CÔNG) |
|---|---|---|---|
| Interrogator | M1 mục tiêu, M2 dòng tiền | Hỏi theo 20 §3 (**nhóm N0 mục tiêu & ràng buộc trước tham số**), quản token bằng hệ số 2,5, **giữ 15% cho lượt restate**, điền RTM (20 §5), lưu log (20 §6) | Chấm phạm vi (50 §6) |
| Spec Writer | M3 dòng tồn, M4 biên hệ thống | Gõ spec theo 30 §1, gắn nhãn mục tiêu `[M-x]` cho từng BR, quản **đích 2.700 từ** | Soi spec #1, #2 (50 §4) |
| Red Teamer | M5 lạm dụng, M6 suy biến, bảng mâu thuẫn brief | **Cổng F (32 §1)** + bảng Mục tiêu↔Luật + eval set **hai reader** + lint 22 nhóm (40) + cổng chất lượng **16 dòng** (30 §7) | Soi spec #3, tổng hợp 15 hồ sơ 50 §7, một mình giữ bằng chứng kháng nghị |

- Đội 2 người: A = Interrogator + Spec Writer, B = Red Teamer + log; chiều chia 1,5 spec/người.
- **Khối 9:00–9:30 làm chung, không chia.** Mô hình bài toán là hiểu biết dùng chung; chia ra thì mỗi người viết luật theo một hình dung khác nhau và §0 sẽ chỏi §6.
- Diễn tập bắt buộc 10–11/09 [HD 6.2]: dựng mô hình 20′ → hỏi 60′ (có restate) → viết 90′ → cổng F → đổi spec tự bắn → đo TRÚNG/TRƯỢT/VÔ HIỆU.
