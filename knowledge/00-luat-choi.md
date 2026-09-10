*Đọc đầu tiên: 9:00 ngày 12/09. Tham số đã chốt tại họp BTC 09/09; ô nào còn hở ghi rõ ở §A2.*

# 00 — Luật chơi & tham số

## A. Tham số đã chốt (họp BTC 09/09)

| Tham số | Giá trị | Nguồn |
|---|---|---|
| Định dạng nộp spec | **Markdown**. Bảng được. Sơ đồ **chỉ dạng mermaid** trong văn bản — bản nộp **không có ảnh** | Chốt 09/09 |
| Hạn mức spec | **≤ 6.000 token** (BTC có thể chỉnh sau) · **đích 5.400** (10% đệm) · khóa 12:00 | Chốt 09/09 |
| Hỏi AI Khách hàng | **Tối đa 5 câu hỏi**, **mỗi lượt đúng 1 câu**, tổng **5.000 token** cho cả hỏi + trả lời | Chốt 09/09 |
| Memory của AI Khách hàng | **KHÔNG có.** Mỗi câu là một phiên độc lập — không nhớ câu trước, không nhớ câu trả lời trước | Chốt 09/09 |
| Ảnh gửi AI Khách hàng | Đọc được ảnh, **tối đa 3 lần** | Chốt 09/09 |
| Xem lại hội thoại AI Khách hàng | **Được** | Chốt 09/09 |
| Điểm | **CÔNG trúng +2** · **THỦ đỡ được (TRƯỢT) +1** · **CÔNG bị VÔ HIỆU −1** | Chốt 09/09 |
| Test / spec đối thủ | 5 test; 3 spec đối thủ → 15 test bắn | W* |
| Spec đối thủ | **Markdown, tải về được** | Chốt 09/09 |
| Sửa test sau khi nộp | **Không được** | Chốt 09/09 |
| Kháng nghị | **Chỉ khi test CÔNG của mình bị chấm VÔ HIỆU.** Nộp bằng **text gửi AI**, ban tổ chức review | Chốt 09/09 |
| Số ca kháng nghị | ≤ 3 ca/đội | W* — chưa tái xác nhận 09/09 |
| Câu hỏi về vận hành hệ thống thi | **BTC không trả lời** (thông tin bảo mật): model của 3 tác nhân, prompt của Executor, cách chấm bên trong | Chốt 09/09 |
| Thời gian THỦ / CÔNG | 2h30 (9:30–12:00) / 2h (13:00–15:00) | W* |
| Đội hình / đối thủ | 2–3 người; phân ngẫu nhiên, 20–30 đội | W* |

`W*` = số liệu website 04/09, chưa được xác nhận lại.

### A1. Cách đo 6.000 token và 5.000 token

Không có tokenizer trong phòng thi. Dùng **ước lượng thiên an toàn**, lấy giá trị lớn hơn của hai công thức:

```
LC_ALL=C.UTF-8 wc -w -m <file>      # w = số từ (âm tiết), m = số ký tự
token ≈ max( số_từ × 2,5 ,  số_ký_tự / 2,2 )
```

- Tiếng Việt tốn ~2–2,5 token mỗi âm tiết (dấu tách thành nhiều token). Cả hai công thức đều lệch ~±20%, nên **đích của bản nộp là 5.400 token ước lượng**, không phải 6.000.
- Dấu `|` của bảng markdown là token thật: một hàng 6 cột tốn ~7 token chỉ riêng dấu. Bảng vẫn đáng dùng, nhưng đừng dùng bảng cho nội dung chỉ có một cột giá trị.
- **Nếu giao diện nộp hiển thị số token, số đó thắng mọi ước lượng.** Ghi lại tỷ lệ `token thật / số từ` ngay lần đầu thấy được và dùng nó cho các lần đo sau.
- Ước cho lượt hỏi: một câu hỏi ~60 từ ≈ 150 token; một bảng trả lời 12 hàng ≈ 400–600 token. Trung bình **1.000 token/câu** trên 5 câu là ngân sách để lập kế hoạch (20 §3).
- Ảnh: một ảnh chụp màn hình cỡ trung ≈ **1.000–1.600 token** nếu BTC tính ảnh vào hạn mức (chưa rõ, §A2). Vì thế mặc định **không dùng ảnh** — xem 20 §1 quy tắc 9.

### A2. Còn hở — hỏi BTC ngay đầu ngày thi (mỗi câu 1 dòng, hỏi miệng, không tốn token)

1. **Ảnh có tính vào 5.000 token không?** Nếu KHÔNG → ảnh thành phương tiện restate tốt nhất (gửi bảng 25 giả định bằng ảnh, xin về "dòng nào sai"), đảo hẳn quy tắc 20 §1-9.
2. **AI Khách hàng còn mở sau 12:00 không?** Nếu còn → giữ 1 câu cho 13:30 để xin danh sách NGOÀI phạm vi mở rộng, dùng chống VÔ HIỆU khi bắn.
3. Giao diện có hiển thị token đã dùng / còn lại?
4. Xếp giải theo pool 4 đội hay toàn giải?
5. Giới hạn độ dài mỗi test? Test nhiều bước có được không?
6. Test của đối thủ bị chấm VÔ HIỆU thì THỦ có được +1 không, hay 0?
7. Số ca kháng nghị tối đa vẫn là 3?

Ô 1 và 2 đổi kế hoạch buổi sáng; hỏi trước 9:30. Các ô còn lại chỉ đổi cách tính điểm kỳ vọng, hỏi lúc nào cũng được.

## B. Agenda 12/09

| Giờ | Pha | Việc | Output phải có |
|---|---|---|---|
| 9:00–9:30 | Nhận brief | Đọc brief; **dựng mô hình bài toán (`/frame`, knowledge/05)**; vào vai (§I) | `mo-hinh-bai-toan.md` đủ M1–M6; mâu thuẫn nội tại của brief; **5 câu hỏi đã chọn** + lý do loại các câu khác |
| 9:30–9:55 | Hỏi C1, C2 | Gửi câu 1 (phạm vi & điều cấm), câu 2 (bảng chuyển trạng thái); nạp ngay | 2 khối log nguyên văn + RTM |
| 9:55–10:20 | Hỏi C3, C4 | Gửi câu 3 (bảng tham số), câu 4 (8 kịch bản suy biến); nạp ngay | RTM đủ 4 câu; **danh sách ⚠** |
| 10:20–11:05 | Viết spec | `/spec-write` — mọi ô không hỏi được điền bằng **mặc định ngành**, gắn `[GIẢ ĐỊNH]` + xếp hạng rủi ro | `spec.md` bản 1; bảng xếp hạng giả định |
| 11:05–11:20 | Hỏi C5 (restate) | 8–10 phát biểu Đúng/Sai lấy từ **đầu bảng xếp hạng rủi ro**, không phải từ kế hoạch soạn trước | Câu trả lời cuối cùng; danh sách "Sai" phải vá |
| 11:20–11:38 | Vá spec | Sửa mọi ý "Sai"; **cổng F + bảng Mục tiêu↔Luật** | 0 ✗ ở F1/F5; mọi mục tiêu có luật |
| 11:38–11:52 | Review & nộp | `/spec-review`; đếm token trên bản nộp; nộp trước 11:52 | Bản nộp ≤5.400 token; lưu 1 bản copy |
| 12:00–13:00 | Nghỉ | Chuyển ⚠ + giả định rủi ro cao thành tình huống | ≥ 15 tình huống ứng viên |
| 13:00–15:00 | CÔNG | Tải 3 spec; **bảng đồng thuận chéo 3 spec**; soi im lặng; chấm phạm vi | 15 test + đáp án kỳ vọng + **gói kháng nghị sẵn cho từng test** |
| 15:00–16:00 | Chạy test | Ghi TRÚNG / TRƯỢT / VÔ HIỆU | ≤ 3 ca VÔ HIỆU đáng kháng nghị |
| 16:00–17:00 | Kháng nghị | Chỉ ca CÔNG bị VÔ HIỆU; nộp text | Kết quả từng ca; 17:00 trao giải |

Khác bản cũ ở hai chỗ: (a) hỏi xong 4 câu trước 10:20 nên **có 45 phút viết spec trước khi restate** — lượt restate vì thế nhắm đúng giả định đã thật sự vào spec, thay vì nhắm vào kế hoạch soạn từ hôm trước; (b) buổi chiều có bước đồng thuận chéo 3 spec, chỉ làm được vì spec đối thủ tải về được.

## C. Ba tác nhân AI

| Tác nhân | Biết | KHÔNG biết / không làm | Hệ quả |
|---|---|---|---|
| AI Khách hàng | Specs thật; pha chấm: quyết phạm vi, đưa đáp án chuẩn | **Không có memory**; chỉ trả lời đúng 1 câu được hỏi mỗi lượt; tổng 5 câu / 5.000 token; không tự kể | 5 câu là toàn bộ tri thức chắc chắn của cả ngày. Mỗi câu phải **tự chứa** và trả về **một bảng**, không phải một dòng |
| AI Executor | CHỈ spec bị bắn | Brief, specs thật, hội thoại, lẽ thường | Spec tự chứa 100%; cấm "như thông lệ". Im lặng → đoán theo mặc định ngành |
| AI So khớp | Đáp án chuẩn + trả lời Executor | Không so từ khóa; so ý nghĩa | Đúng ý là đủ; mơ hồ vẫn bị bắt |

Model của cả ba tác nhân là **thông tin bảo mật, BTC không công bố**. Hệ quả thực tế: khi dry-run bằng agent `executor`, luôn giả lập bảo toàn — một reader dùng model yếu hơn (`/spec-review` khối D gọi reader 2 bằng `model: haiku`).

## D. Ba trạng thái và điểm

| Trạng thái | Nghĩa | Điểm |
|---|---|---|
| TRÚNG | Executor lệch specs thật vì spec đối thủ hở | **CÔNG +2** |
| TRƯỢT | Executor khớp specs thật | **THỦ +1** (đội bị bắn), CÔNG mất lượt |
| VÔ HIỆU | Tình huống ngoài phạm vi specs thật | **CÔNG −1**, được kháng nghị |

Ví dụ BTC (app nghỉ phép): TRÚNG — "Nghỉ qua ngày lễ tính phép sao?" spec im lặng, Executor tính cả lễ. TRƯỢT — "Đơn bị từ chối sửa & nộp lại?" spec rõ. VÔ HIỆU — "Bảo hiểm khi nghỉ không lương?" ngoài phạm vi.

### D1. Số học của công thức điểm — đọc trước khi chia thời gian

- Trần điểm CÔNG = 15 test × 2 = **30**. Trần điểm THỦ = 15 test đối thủ bắn vào ta × 1 = **15**.
- Một test đáng bắn khi `2 × P(TRÚNG) − 1 × P(VÔ HIỆU) > 0`, tức **P(TRÚNG) > P(VÔ HIỆU) / 2**. Một test có 40% trúng và 30% vô hiệu vẫn dương (+0,5). Ngưỡng này rộng hơn trực giác — nhưng nó chỉ dương khi hai xác suất được chấm thật (rubric 50 §6), không phải chấm bằng cảm tính.
- Đổi lại: **bỏ một test là 0 điểm, không phải −1**. Chỉ nộp đủ 5 test khi cả 5 đều dương kỳ vọng; test thứ 5 yếu hơn "không nộp" thì đừng nộp.
- VÔ HIỆU được kháng nghị, TRÚNG oan **không** được. Nghĩa là: rủi ro của vai CÔNG có đường lùi, rủi ro của vai THỦ không có. Cách xử lý bất đối xứng này: dồn *cẩn trọng* vào chất lượng spec (không cứu được), dồn *dũng cảm* vào việc bắn (cứu được một phần).
- Bản cũ của tài liệu này khuyên "ưu tiên phòng thủ khi cạn thời gian". **Sai chiều theo công thức 09/09**: cùng một giờ bỏ vào buổi chiều sinh điểm gấp đôi. Nhưng buổi sáng vẫn không được cắt, vì nó tạo cả 15 điểm thủ *và* toàn bộ bằng chứng kháng nghị của buổi chiều.

## F. Năm nhận định cấu trúc (đã cập nhật theo tham số 09/09)

1. **Executor mù bối cảnh ⇒ spec tự chứa 100%.** "Ai chả biết" là lỗ hổng.
2. **AI Khách hàng chỉ trả lời khi được hỏi, và chỉ 5 lần.** Question bank soạn trước vẫn là tài sản số 1, nhưng đổi vai: nó không còn là *danh sách để hỏi*, nó là **danh sách mặc định ngành để tự điền** (20 §4) và là **kho phát biểu cho câu restate**. Chỉ 5 câu được gửi đi thật.
3. **Không có memory ⇒ không có hội thoại, chỉ có 5 lần xin dữ liệu.** Không hỏi tiếp được, không sửa format ở lượt sau mà không mất một câu trong 5. Mỗi câu phải tự chứa và đúng ngay lần đầu (cổng 5 kiểm tra ở 20 §1-10).
4. **VÔ HIỆU = −1 và được kháng nghị.** Vẫn bắn core flow tiền/tồn, vẫn tránh biên xa (thuế, kế toán, bảo hiểm), nhưng ngưỡng loại bỏ tính theo kỳ vọng (§D1) chứ không theo "cảm giác rủi ro". Điều kiện đi kèm: **mọi test phải có gói bằng chứng phạm vi thu sẵn trước khi nộp**, vì test không sửa được và kháng nghị chỉ có 1 giờ.
5. **Ranh giới tri thức: biết mà không viết = bề mặt tấn công.** Với 5 câu trả lời, phần lớn spec là giả định — nên đọc lại spec như người chưa nghe brief, và đánh dấu chỗ nào là giả định để buổi chiều tự bắn trước khi đối thủ bắn.

## G. Quy tắc vàng & RTM ngược

**CÔNG:** Bắn chỗ specs thật phản trực giác mà spec đối thủ im lặng. Executor đoán mặc định ngành: = specs thật → TRƯỢT; ≠ → TRÚNG. Ba nguồn đạn, theo độ mạnh giảm dần: (1) 4–5 câu trả lời buổi sáng; (2) **đồng thuận chéo 3 spec tải về** — nghiệp vụ 2 đội có luật mà đội thứ 3 im lặng; (3) catalogue ⚠ của domain (10 §6).

**THỦ:** Mọi trả lời ⚠ truy vết tới một luật có mã trong spec; ô trống = chắc chắn bị bắn. Chỗ không hỏi được thì **viết mặc định ngành tường minh**, không bỏ trống và không sáng tạo giá trị mới (30 §1b).

Mẫu RTM ngược (7 cột, có cả dòng giả định `G-xx`) + quy tắc điền: knowledge/20 §5.

## H. Trạng thái 14 câu hỏi BTC

| # | Câu | Kết quả |
|---|---|---|
| 1 | Hạn mức token AI Khách hàng | ✅ 5.000 token, gồm hỏi + trả lời |
| 2 | Giới hạn lượt hỏi | ✅ 5 câu, mỗi lượt 1 câu, không memory |
| 3 | Định dạng nộp spec, bảng/sơ đồ, cách đếm | ✅ Markdown, bảng được, sơ đồ mermaid, hạn mức tính bằng token |
| 4 | Hạn mức hình ảnh | ✅ 3 lần với AI Khách hàng; bản nộp không có ảnh · ❓ ảnh có tính token không (§A2-1) |
| 5 | Công thức điểm | ✅ +2 / +1 / −1 · ❓ VÔ HIỆU của đối thủ có cho THỦ +1 không (§A2-6) |
| 6 | Xếp giải pool hay toàn giải | ❓ chưa có |
| 7 | Model 3 tác nhân, prompt Executor | ⛔ **BTC không trả lời — bảo mật** |
| 8 | Được dùng AI riêng để viết & soi spec | ❓ chưa có văn bản; giả định **được** (đây là hackathon AI). Nếu không: in artifact theo danh sách ở `.claude/README.md` |
| 9 | Được mang tài liệu chuẩn bị trước | ❓ chưa có; giả định **được** |
| 10 | Định dạng & độ dài test | ❓ độ dài chưa có (§A2-5) · ✅ không sửa được sau khi nộp |
| 11 | Spec đối thủ cấp dạng nào | ✅ Markdown, tải về được |
| 12 | Kháng nghị: form hay miệng; xem lý do đối chiếu | ✅ text gửi AI, BTC review; **chỉ cho ca CÔNG bị VÔ HIỆU** · ❓ có được xem lý do đối chiếu trước không |
| 13 | Xem lại hội thoại AI Khách hàng | ✅ Được |
| 14 | Sửa test sau khi nộp | ✅ Không |

## I. Phân vai & owner

Mỗi artifact một owner.

| Vai | 9:00–9:30 (mô hình) | Sáng (THỦ) | Chiều (CÔNG) |
|---|---|---|---|
| Interrogator | M1 mục tiêu, M2 dòng tiền | Chọn và gọt **5 câu** (20 §1, §3), gửi từng câu, nạp nguyên văn vào log + RTM, **giữ câu 5 cho restate** | Chấm phạm vi (50 §6), giữ gói bằng chứng kháng nghị |
| Spec Writer | M3 dòng tồn, M4 biên hệ thống | Gõ spec theo 30 §1, gắn nhãn mục tiêu `[M-x]`, quản **đích 5.400 token** | Soi spec #1, #2 (50 §4) |
| Red Teamer | M5 lạm dụng, M6 suy biến, bảng mâu thuẫn brief | **Cổng F (32 §1)** + bảng Mục tiêu↔Luật + eval set **hai reader** + lint 22 nhóm (40) + cổng chất lượng **16 dòng** (30 §7) + **xếp hạng rủi ro giả định để nuôi câu 5** | Soi spec #3, bảng đồng thuận chéo, tổng hợp 15 hồ sơ 50 §7 |

- Đội 2 người: A = Interrogator + Spec Writer, B = Red Teamer + log; chiều chia 1,5 spec/người.
- **Khối 9:00–9:30 làm chung, không chia.** Mô hình bài toán là hiểu biết dùng chung; chia ra thì mỗi người viết luật theo một hình dung khác nhau.
- **Việc mới của Red Teamer, quan trọng nhất trong ngày:** xếp hạng rủi ro giả định lúc 11:00 để chọn 8–10 phát biểu cho câu 5. Chọn sai ở bước này là mất câu hỏi cuối cùng vào một giả định vô hại.
- Diễn tập bắt buộc 10–11/09: dựng mô hình 20′ → 4 câu hỏi → viết 45′ → restate → cổng F → đổi spec tự bắn → đo TRÚNG/TRƯỢT/VÔ HIỆU theo điểm +2/+1/−1.
