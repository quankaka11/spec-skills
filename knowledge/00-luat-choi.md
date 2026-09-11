*Đọc đầu tiên: 9:00 ngày 12/09. Tham số chốt tại họp BTC 09/09, **cập nhật sau buổi thi thử 11/09** (khối hỏi AI Khách hàng đổi hẳn); ô nào còn hở ghi rõ ở §A2.*

# 00 — Luật chơi & tham số

## A. Tham số đã chốt (họp BTC 09/09)

| Tham số | Giá trị | Nguồn |
|---|---|---|
| Định dạng nộp spec | **Markdown**. Bảng được. Sơ đồ **chỉ dạng mermaid** trong văn bản — bản nộp **không có ảnh** | Chốt 09/09 |
| Hạn mức spec | **≤ 10.000 token** · **đích 9.000** (10% đệm) · **sàn thực tế 7.500** — dưới mức đó mà còn ô trống là chưa viết xong (33 §5) · khóa 12:00 | Cập nhật 11/09 |
| Hỏi AI Khách hàng | **KHÔNG giới hạn số câu.** Giới hạn là **4.000 token** (hỏi + trả lời, cho cả đội) và **nhịp chờ giữa hai lượt** | Thi thử 11/09 |
| Hình thức mỗi lượt | **Một lượt = một câu hỏi = MỘT Ý (một ẩn số).** Gộp 2–3 ý độc lập vào một lượt ⇒ **bị từ chối** | Thi thử 11/09 |
| Câu hỏi chứa chỉ thị | Câu ra lệnh cho AI cách trả lời (ép format bảng, cap số dòng, cap số từ, "không giải thích") ⇒ **bị từ chối, xếp loại injection** | Thi thử 11/09 |
| Câu bị từ chối | **Không trừ token, không reset nhịp chờ** ⇒ sửa và gửi lại ngay, gần như miễn phí | Thi thử 11/09 |
| Nhịp chờ giữa hai lượt | Thi thử: **45 giây/lượt**. Ngày thi: đọc brief §3 — đây thường là ràng buộc thật, không phải token | Thi thử 11/09 |
| Memory của AI Khách hàng | **KHÔNG có.** Mỗi lượt là một phiên độc lập — không nhớ câu trước, không nhớ câu trả lời trước | Chốt 09/09 |
| Ảnh gửi AI Khách hàng | Đọc được ảnh, **tối đa 3 lần**; thi thử 11/09: **ảnh CÓ tính token** ⇒ không dùng | Thi thử 11/09 |
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

### A1. Cách đo 10.000 token (spec) và 4.000 token (hỏi)

Không có tokenizer trong phòng thi. Dùng **ước lượng thiên an toàn**, lấy giá trị lớn hơn của hai công thức:

```
LC_ALL=C.UTF-8 wc -w -m <file>      # w = số từ (âm tiết), m = số ký tự
token ≈ max( số_từ × 2,5 ,  số_ký_tự / 2,2 )
```

- Tiếng Việt tốn ~2–2,5 token mỗi âm tiết (dấu tách thành nhiều token). Cả hai công thức đều lệch ~±20%, nên **đích của bản nộp là 9.000 token ước lượng**, không phải 10.000. 9.000 token ≈ **3.500–3.700 từ tiếng Việt**.
- **Hạn mức 10.000 đổi loại rủi ro.** Với 6.000, rủi ro là tràn; với 10.000, rủi ro là **dừng sớm** — spec 6.500 token còn ô trống ở message lỗi và ca bất thường là spec chưa viết xong. Phân bổ từng mục và bảng "tiêu đệm vào đâu": knowledge/33 §5.
- Dấu `|` của bảng markdown là token thật: một hàng 6 cột tốn ~7 token chỉ riêng dấu. Bảng vẫn đáng dùng, nhưng đừng dùng bảng cho nội dung chỉ có một cột giá trị.
- **Nếu giao diện nộp hiển thị số token, số đó thắng mọi ước lượng.** Ghi lại tỷ lệ `token thật / số từ` ngay lần đầu thấy được và dùng nó cho các lần đo sau.
- Ước cho một lượt **một-ý**: câu hỏi 20–40 từ ≈ **60–110 token**; câu trả lời nhị phân ("A hay B") ≈ **20–60 token**; câu trả lời mở (danh sách, message nguyên văn, bảng do AI tự chọn) ≈ **150–400 token**. Trung bình **~200 token/lượt** ⇒ 4.000 token ≈ **20 lượt**.
- **Nhưng token hiếm khi là thứ chặn.** Với nhịp 45 giây/lượt, 20 lượt = 15 phút bấm gửi liên tục. Pha hỏi dài bao nhiêu phút thì chia cho nhịp ra **trần lượt thật**; trần đó gần như luôn nhỏ hơn trần token. Kế hoạch hỏi vì thế phải **xếp hạng và có đường cắt** (20 §3), không phải liệt kê cho đủ.
- Câu **bị từ chối không trừ token**: rủi ro của một câu viết sai là *một nhịp chờ*, không phải mất dữ kiện. Đổi lại, cổng kiểm tra trước khi gửi (20 §1-10) vẫn bắt buộc — mỗi lần bị từ chối là mất 45 giây trong pha ngắn nhất của ngày.
- Ảnh: một ảnh chụp màn hình cỡ trung ≈ **1.000–1.600 token**; thi thử 11/09 xác nhận **ảnh có tính token** ⇒ 3 lượt ảnh đó bỏ không dùng, không tiếc (20 §1-9).

### A2. Còn hở — đọc brief §3 và hỏi BTC ngay đầu ngày (hỏi miệng, không tốn token)

Bốn ô đầu **quyết định trần số lượt**, tức quyết định cả kế hoạch hỏi. Đọc brief trước; brief không nói thì hỏi miệng trước giờ mở AI.

1. **Nhịp chờ giữa hai lượt là bao nhiêu giây?** (thi thử: 45s) → chia thời lượng pha hỏi cho nhịp = **trần lượt thật**; đường cắt của hàng đợi 20 §3 đặt tại đó.
2. **4.000 token là của cả đội hay của mỗi thành viên?** (thi thử: cả đội) → nếu của mỗi người thì hai người hỏi song song hai nhánh khác nhau, chia hàng đợi theo chủ đề để không trùng.
3. **Giao diện có hiển thị token đã dùng / còn lại không?** Nếu có, số đó thắng mọi ước lượng (§A1).
4. **AI Khách hàng còn mở sau giờ khóa spec không?** Nếu còn → để dành các lượt về NGOÀI phạm vi cho pha CÔNG, dùng chống VÔ HIỆU khi bắn.
5. **Lý do VÔ HIỆU của đề này là gì** (brief thường liệt kê tường minh)? Nếu danh sách không có "ngoài phạm vi" thì lượt hỏi về NGOÀI phạm vi **tụt hạng ưu tiên** — đừng chép máy móc thứ tự của kit.
6. Xếp giải theo pool 4 đội hay toàn giải?
7. Giới hạn độ dài mỗi test? Test nhiều bước có được không?
8. Test của đối thủ bị chấm VÔ HIỆU thì THỦ có được +1 không, hay 0?
9. Số ca kháng nghị tối đa vẫn là 3?

**Đã chốt tại thi thử 11/09, không cần hỏi lại:** ảnh có tính token (⇒ không dùng); câu bị từ chối không trừ token và không reset nhịp chờ; câu chứa chỉ thị và câu gộp nhiều ý đều bị từ chối.

## B. Agenda 12/09

| Giờ | Pha | Việc | Output phải có |
|---|---|---|---|
| 9:00–9:30 | Nhận brief | Đọc brief (**§3 nhịp chờ + token, §4 lý do VÔ HIỆU**); **dựng mô hình bài toán (`/frame`, knowledge/05)**; vào vai (§I) | `mo-hinh-bai-toan.md` đủ M1–M6; mâu thuẫn nội tại của brief; **hàng đợi lượt hỏi đã xếp hạng + đường cắt tính theo nhịp** |
| 9:30–10:30 | Hỏi liên tục | Gửi lượt một-ý theo đúng thứ tự hàng đợi, đúng nhịp, không nghỉ; nạp từng câu trả lời ngay khi về | Log nguyên văn từng lượt + RTM cập nhật sau mỗi lượt |
| **9:45–11:05** | **Viết spec song song** | Người thứ hai **bắt đầu viết từ 9:45**, không chờ hỏi xong: khung 10 mục + mọi luật suy được từ brief; ô chưa có câu trả lời để `[..]` rồi điền dần | `spec.md` bản 1; bảng xếp hạng giả định |
| 10:30–11:05 | Hỏi phần đuôi | Tiếp hàng đợi sau đường cắt **chỉ khi khung spec đã xong**; ưu tiên ô giả định rủi ro Cao mà `/spec-write` vừa chấm | RTM đủ; **danh sách ⚠** |
| 11:05–11:20 | Lượt xác nhận | Từng phát biểu một (một ý/lượt), lấy từ **đầu bảng xếp hạng rủi ro giả định** — không gộp 10 phát biểu vào một lượt, sẽ bị từ chối | Danh sách ý "Sai" phải vá |
| 11:20–11:38 | Vá spec | Sửa mọi ý "Sai"; **cổng F + bảng Mục tiêu↔Luật** | 0 ✗ ở F1/F5; mọi mục tiêu có luật |
| 11:38–11:52 | Review & nộp | `/spec-review`; đếm token trên bản nộp; nộp trước 11:52 | Bản nộp 7.500–9.000 token, tuyệt đối ≤10.000; đủ 3 sơ đồ BTC yêu cầu; lưu 1 bản copy |
| 12:00–13:00 | Nghỉ | Chuyển ⚠ + giả định rủi ro cao thành tình huống | ≥ 15 tình huống ứng viên |
| 13:00–15:00 | CÔNG | Tải 3 spec; **bảng đồng thuận chéo 3 spec**; soi im lặng; chấm phạm vi | 15 test + đáp án kỳ vọng + **gói kháng nghị sẵn cho từng test** |
| 15:00–16:00 | Chạy test | Ghi TRÚNG / TRƯỢT / VÔ HIỆU | ≤ 3 ca VÔ HIỆU đáng kháng nghị |
| 16:00–17:00 | Kháng nghị | Chỉ ca CÔNG bị VÔ HIỆU; nộp text | Kết quả từng ca; 17:00 trao giải |

Ba khác biệt so với bản 09/09, đều do luật hỏi đổi (thi thử 11/09):

(a) **Hỏi và viết chạy song song, không nối tiếp.** Số lượt bị nhịp chờ chặn chứ không bị token chặn, nên cả đội ngồi chờ câu trả lời là cách lãng phí lớn nhất trong ngày: một người bấm gửi đúng nhịp và dán câu trả lời, người còn lại viết spec từ 9:45.
(b) **Không còn "câu số 5" thiêng liêng.** Lượt xác nhận giờ chỉ là những lượt rẻ nhất trong hàng đợi (~40–80 token) và vẫn tốn đúng một nhịp như mọi lượt khác — nên một giả định rủi ro Cao đáng hỏi thì hỏi thẳng dạng nhị phân ngay từ đầu, không để dành.
(c) Buổi chiều có bước đồng thuận chéo 3 spec, chỉ làm được vì spec đối thủ tải về được.

## C. Ba tác nhân AI

| Tác nhân | Biết | KHÔNG biết / không làm | Hệ quả |
|---|---|---|---|
| AI Khách hàng | Specs thật; pha chấm: quyết phạm vi, đưa đáp án chuẩn | **Không có memory**; **từ chối** câu chứa chỉ thị và câu gộp nhiều ý; 4.000 token + nhịp chờ; không tự kể | Tri thức chắc chắn của cả ngày = số lượt kịp gửi. Mỗi lượt phải **tự chứa**, **đúng một ý**, và **không ra lệnh cách trả lời** — muốn câu trả lời ngắn thì hỏi dạng nhị phân "A hay B", đó là công cụ duy nhất còn lại |
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
2. **Số câu không còn là hạn mức; nhịp chờ mới là.** Question bank soạn trước lại là *danh sách để hỏi* — nhưng dùng như một **hàng đợi đã xếp hạng có đường cắt**, mỗi mục một lượt một-ý (20 §3). Phần dưới đường cắt vẫn phải thành luật trong spec, bằng **mặc định ngành** (20 §4). Ràng buộc đổi từ "chọn được 5 ô nào" sang "**xếp đúng thứ tự và bấm đúng nhịp**".
3. **Không ra lệnh được cho AI ⇒ hình dạng câu hỏi là công cụ điều khiển duy nhất.** Không ép được format, không cap được độ dài; muốn câu trả lời ngắn và dứt khoát thì phải hỏi **nhị phân** ("A hay B") hoặc hỏi **một con số**. Câu mở ("xử lý thế nào", "có những gì") vẫn dùng được nhưng đắt gấp 3–5 lần và có thể trả về văn xuôi không dùng ngay được. Không có memory ⇒ mỗi lượt vẫn phải tự chứa (cổng 8 kiểm tra ở 20 §1-10).
4. **VÔ HIỆU = −1 và được kháng nghị.** Vẫn bắn core flow tiền/tồn, vẫn tránh biên xa (thuế, kế toán, bảo hiểm), nhưng ngưỡng loại bỏ tính theo kỳ vọng (§D1) chứ không theo "cảm giác rủi ro". Điều kiện đi kèm: **mọi test phải có gói bằng chứng phạm vi thu sẵn trước khi nộp**, vì test không sửa được và kháng nghị chỉ có 1 giờ.
5. **Ranh giới tri thức: biết mà không viết = bề mặt tấn công.** Kể cả khi kịp 15–20 lượt, phần lớn spec vẫn là giả định — nên đọc lại spec như người chưa nghe brief, và đánh dấu chỗ nào là giả định để buổi chiều tự bắn trước khi đối thủ bắn.

## G. Quy tắc vàng & RTM ngược

**CÔNG:** Bắn chỗ specs thật phản trực giác mà spec đối thủ im lặng. Executor đoán mặc định ngành: = specs thật → TRƯỢT; ≠ → TRÚNG. Ba nguồn đạn, theo độ mạnh giảm dần: (1) các câu trả lời buổi sáng; (2) **đồng thuận chéo 3 spec tải về** — nghiệp vụ 2 đội có luật mà đội thứ 3 im lặng; (3) catalogue ⚠ của domain (10 §6).

**THỦ:** Mọi trả lời ⚠ truy vết tới một luật có mã trong spec; ô trống = chắc chắn bị bắn. Chỗ không hỏi được thì **viết mặc định ngành tường minh**, không bỏ trống và không sáng tạo giá trị mới (30 §1b).

Mẫu RTM ngược (7 cột, có cả dòng giả định `G-xx`) + quy tắc điền: knowledge/20 §5.

## H. Trạng thái 14 câu hỏi BTC

| # | Câu | Kết quả |
|---|---|---|
| 1 | Hạn mức token AI Khách hàng | ✅ **4.000 token** (thi thử 11/09), gồm hỏi + trả lời · ❓ của cả đội hay mỗi người (§A2-2) |
| 2 | Giới hạn lượt hỏi | ✅ **Không giới hạn số câu**; mỗi lượt đúng **một ý**, không memory; **câu chứa chỉ thị hoặc gộp nhiều ý bị từ chối** (không trừ token) · ❓ nhịp chờ bao nhiêu giây ở ngày thi (§A2-1) |
| 3 | Định dạng nộp spec, bảng/sơ đồ, cách đếm | ✅ Markdown, bảng được, sơ đồ mermaid, hạn mức tính bằng token |
| 4 | Hạn mức hình ảnh | ✅ 3 lần với AI Khách hàng; bản nộp không có ảnh · ✅ **ảnh CÓ tính token** (thi thử 11/09) ⇒ không dùng |
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
| Interrogator | M1 mục tiêu, M2 dòng tiền | Xếp **hàng đợi lượt hỏi + đường cắt** (20 §1, §3), **bấm gửi đúng nhịp không nghỉ**, nạp nguyên văn vào log + RTM | Chấm phạm vi (50 §6), giữ gói bằng chứng kháng nghị |
| Spec Writer | M3 dòng tồn, M4 biên hệ thống | Gõ spec theo 30 §1, gắn nhãn mục tiêu `[M-x]`, quản **đích 9.000 token** và **vẽ đủ 3 sơ đồ BTC yêu cầu** (33 §7) | Soi spec #1, #2 (50 §4) |
| Red Teamer | M5 lạm dụng, M6 suy biến, bảng mâu thuẫn brief | **Cổng F (32 §1)** + bảng Mục tiêu↔Luật + eval set **hai reader** + lint 22 nhóm (40) + cổng chất lượng **16 dòng** (30 §7) + **xếp hạng rủi ro giả định để nuôi câu 5** | Soi spec #3, bảng đồng thuận chéo, tổng hợp 15 hồ sơ 50 §7 |

- Đội 2 người: A = Interrogator (**chỉ bấm gửi và dán câu trả lời, không viết spec**), B = Spec Writer + Red Teamer; chiều chia 1,5 spec/người. Phân vai này là bắt buộc: với nhịp chờ, hỏi là việc *chiếm người* chứ không chiếm trí, còn viết spec mới là đường găng.
- **Khối 9:00–9:30 làm chung, không chia.** Mô hình bài toán là hiểu biết dùng chung; chia ra thì mỗi người viết luật theo một hình dung khác nhau.
- **Việc mới của Red Teamer, quan trọng nhất trong ngày:** xếp hạng rủi ro giả định **liên tục từ 10:15**, không đợi tới 11:00 — mỗi giả định rủi ro Cao được chấm sớm là một lượt hỏi còn kịp gửi trước khi hết giờ. Xếp hạng muộn = ô đó vĩnh viễn là giả định.
- Diễn tập bắt buộc 10–11/09: dựng mô hình 20′ → **hàng đợi lượt một-ý, gửi theo nhịp, viết song song** → lượt xác nhận → cổng F → đổi spec tự bắn → đo TRÚNG/TRƯỢT/VÔ HIỆU theo điểm +2/+1/−1.
