# Thể lệ — tham số cuộc thi

Skill `/spec` và `/attack` đọc bảng này, không hard-code số. Điền khi BTC công bố; ô chưa có ghi `?`.

| Tham số | Giá trị | Nguồn / ngày |
|---|---|---|
| Ngân sách token hỏi AI Khách hàng (hỏi + trả lời) | 4.000 token cả đội (thi thử) | brief thi thử 11/09 |
| AI Khách hàng có nhớ câu trước không | ? | |
| Ảnh gửi AI Khách hàng: được không, có tính token không | ? | |
| Giới hạn độ dài spec (token / từ / ký tự) và cách đo | Trần nội bộ đội: ≤ 10.000 token, áp min(thể lệ, 10.000). Thi thử là 2.500. **Đo bằng tokenizer**: python `tiktoken` `o200k_base` trên `spec.nop.md` (bản 2.530 o200k bị máy chấm cắt đuôi ở 2.500 → tokenizer chấm ≈ o200k). Không đo bằng `wc -m` hay tỉ lệ ký tự; máy cắt cứng phần sau trần. | Quyết định đội 11/09 + kết quả thi thử |
| Định dạng nộp spec | Markdown; mọi hình vẽ bằng Mermaid, không ảnh | Quyết định đội 11/09 |
| Mermaid có tính vào giới hạn độ dài không | Có, tính như văn bản | thi thử 11/09 |
| Giờ khóa spec | ? | |
| Số spec đối thủ được phân | 2 (thi thử) | brief thi thử 11/09 |
| Số test mỗi spec đối thủ | 2 (thi thử) | brief thi thử 11/09 |
| Giới hạn độ dài mỗi test | ? | |
| Sửa test sau khi nộp | ? | |
| Điểm TRÚNG / TRƯỢT / VÔ HIỆU | +2 công / +1 thủ cho đội viết spec / −1 (thi thử) | brief thi thử 11/09 |
| Kháng nghị: điều kiện, số ca, định dạng, hạn giờ | 2 lượt (thi thử) | brief thi thử 11/09 |
| Lịch các pha trong ngày | Thi thử: phỏng vấn + viết spec 16 phút · bắn test 15 phút · đối chiếu 10 phút | brief thi thử 11/09 |
| Khoảng cách giữa hai lượt hỏi | 45 giây, đúng một câu mỗi lượt → ≈ 21 lượt trong 16 phút. Câu bị từ chối sai hình thức không mất token, không reset thời gian chờ. **Số lượt là ràng buộc chặt hơn token**: thi thử dùng 17 lượt nhưng chỉ 27% token. | brief + kết quả thi thử 11/09 |
| Cách máy chấm spec | Đối chiếu từng mục đáp án (G thuật ngữ · V giá trị · R luật theo màn · DT bảng quyết định · A actor · SM trạng thái). Số đoán sai = SAI; không viết = THIẾU; luật chỉ nằm trong công thức = MƠ HỒ; phần sau trần token = mất. Mỗi luật cần một câu riêng. | kết quả thi thử 11/09 |
