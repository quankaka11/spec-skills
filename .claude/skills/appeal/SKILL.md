---
name: appeal
description: Dựng hồ sơ kháng nghị cho tối đa 3 ca từ các kết quả chấm bất lợi trong Spec Battle — phân loại ca, tìm trích dẫn nguyên văn trong spec đã nộp và log AI Khách hàng có timestamp, chấm xác suất thắng, viết lập luận 2 câu và kịch bản nói 60 giây. Dùng 16:00–17:00 ngày thi khi người dùng nói "kháng nghị", "appeal", "bị chấm TRÚNG oan", "bị VÔ HIỆU sai".
argument-hint: "[thư-mục-trận] + dán danh sách kết quả bất lợi"
allowed-tools: Read, Write, Grep, Glob
---
# /appeal — Hồ sơ kháng nghị ≤3 ca

Mục tiêu: chọn 3 ca có bằng chứng trích dẫn mạnh nhất; mỗi ca trình bày được trong 60 giây với số mục và timestamp.

## Input
`$ARGUMENTS` = `[thư-mục-trận]` (mặc định `battle/`) + phần người dùng dán: từng kết quả bất lợi gồm tình huống, kết quả máy (TRÚNG/VÔ HIỆU), lý do đối chiếu của AI So khớp (nếu có).
Đọc: `spec.md` (bản đã nộp), `log-khach-hang.md`, `tests/*.md` (hồ sơ finding), `${CLAUDE_PROJECT_DIR}/knowledge/00-luat-choi.md` §A (số ca, cơ chế), `${CLAUDE_PROJECT_DIR}/knowledge/50-tan-cong.md` §7 (3 loại ca + lập luận mẫu).

## Bước
1. **Phân loại** từng ca vào đúng một loại (knowledge/50 §7): (1) spec mình bị TRÚNG nhưng spec CÓ quy định; (2) test mình bị VÔ HIỆU nhưng AI Khách hàng đã trả lời nghiệp vụ đó buổi sáng; (3) So khớp hiểu sai ngữ nghĩa — hai câu cùng kết quả về trạng thái cuối / tồn / tiền.
2. **Tìm bằng chứng bằng Grep**, không nhớ mò:
   - Loại 1: Grep từ khóa của tình huống trong `spec.md` → trích nguyên văn §/BR-xx bao trùm tình huống. Không tìm được câu bao trùm trực tiếp → ca này yếu.
   - Loại 2: Grep trong `log-khach-hang.md` → trích khối lượt, timestamp, câu trả lời nguyên văn. Đối chiếu mục NGOÀI phạm vi trong log lượt 1: nếu nghiệp vụ nằm trong danh sách NGOÀI → bỏ ca.
   - Loại 3: đặt hai câu cạnh nhau, chỉ ra 3 chiều (trạng thái cuối, tồn kho, tiền) khớp; lệch một chiều → bỏ ca.
   - Ca đối thủ bắn vào spec mình bằng nghiệp vụ NGOÀI phạm vi (theo log lượt 1) mà bị tính TRÚNG → xếp loại 2 (lẽ ra VÔ HIỆU), bằng chứng là log lượt 1.
   - Với mỗi ca, Grep TOÀN BỘ log (mọi lượt) để gom mọi câu trả lời củng cố, không dừng ở câu đầu tìm được; xếp câu trả lời trực tiếp nhất lên đầu.
   - Trích nguyên văn giữ đúng ký tự (kể cả backtick, mã); cắt đoạn thì đánh dấu `[…]`. Khi viện "theo thể lệ", dẫn dòng cụ thể trong `knowledge/00-luat-choi.md` §D.
3. **Xác suất thắng**: Cao = có trích nguyên văn khớp trực tiếp (BR bao trùm, hoặc log trả lời đúng nghiệp vụ); TB = phải suy 1 bước (áp catch-all §0.5/0.6, hoặc log trả lời nghiệp vụ lân cận); Thấp = không có trích dẫn. Bỏ mọi ca Thấp, ghi lý do 1 dòng.
4. **Xếp hạng**: theo xác suất, rồi theo ưu tiên loại 1 > 2 > 3; điểm ảnh hưởng bằng nhau thì ưu tiên ca bảo vệ spec mình. Chọn 3.
5. **Viết** cho mỗi ca chọn: tiêu đề 1 dòng (ID test, kết quả máy → đề nghị); loại; bằng chứng (trích nguyên văn có số mục / lượt + timestamp); lập luận 2 câu theo mẫu knowledge/50 §7; kịch bản nói 60 giây (~120 từ, mở bằng đề nghị, đóng bằng số mục); 2 câu hỏi trọng tài có thể hỏi + trả lời 1 câu mỗi câu.

## Output `appeal.md`
1. Bảng xếp hạng mọi ca: ID | loại | xác suất | chọn/bỏ | lý do 1 dòng.
2. 3 hồ sơ ca theo bước 5.
3. Checklist mang vào phiên: bản spec đã nộp, log có timestamp, hồ sơ finding, lý do đối chiếu đã xin BTC.

## Không được
- Lập luận không có trích dẫn nguyên văn; trích dẫn không tồn tại trong spec/log (Grep xác nhận trước khi ghi).
- Chọn quá 3 ca; chọn ca xác suất Thấp.
- Sửa spec hay log sau khi khóa.
