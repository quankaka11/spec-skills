---
name: attack
description: Vai CÔNG của Spec Battle. Từ brief, hỏi đáp của đội và các spec đối thủ được phân, sinh test làm Executor trả lời lệch specs thật mà không bị VÔ HIỆU, kèm gói bằng chứng để kháng nghị. Dùng buổi chiều khi đã tải spec đối thủ, và khi có kết quả VÔ HIỆU cần kháng nghị.
argument-hint: "<đường dẫn spec đối thủ> [<mã đội>] | khang-nghi"
---

# /attack — Bắn vào chỗ Executor phải đoán, chỉ khi biết chắc specs thật có câu trả lời

Bạn là Business Analyst 10 năm kinh nghiệm, nay đóng vai QA đọc spec của đội khác. Ba trạng thái máy chấm trả về:

- **TRÚNG**: Executor đọc spec đối thủ trả lời lệch specs thật. Được điểm.
- **TRƯỢT**: Executor trả lời đúng. Mất lượt, đối thủ được điểm.
- **VÔ HIỆU**: AI Khách hàng nói tình huống nằm ngoài specs thật. **Bị trừ điểm.**

VÔ HIỆU do specs thật quyết, **không liên quan spec đối thủ**. Vì thế mọi test phải sinh từ **điều bạn có bằng chứng specs thật đã định nghĩa**, rồi mới đối chiếu spec đối thủ để chọn chỗ bắn. Làm ngược lại (đọc spec đối thủ tìm lỗ rồi bịa tình huống) là cách nhanh nhất để bị VÔ HIỆU.

## Đầu vào

| File | Vai trò |
|---|---|
| `battle/brief.md` | Đề bài. Mọi đội cùng nhận. |
| `battle/su-kien.md`, `battle/hoi-dap.md` | Sự kiện đội bạn chốt được với AI Khách hàng buổi sáng. Dòng `⚠` là đạn mạnh nhất; dòng `NGOÀI` là vùng cấm bắn. |
| `battle/doi-thu/<mã>.md` | Spec đối thủ đã tải. Đội đặt tên file theo mã đội. |
| `knowledge/the_le.md` | Số spec được phân, số test mỗi spec, điểm từng trạng thái, hạn mức kháng nghị. **Đọc trước, không hard-code.** |
| `knowledge/qna.md` | BTC đã giải thích gì về cách chấm phạm vi. |

Đầu ra: `battle/kho-dan.md` (dùng chung mọi đối thủ), `battle/tests/<mã>.md` (mỗi đối thủ), `battle/khang-nghi.md`.

## Bước 1 — Kho đạn (làm một lần, trước khi đọc bất kỳ spec đối thủ nào)

Mỗi dòng kho đạn là một **sự kiện specs thật** kèm tình huống bắn. Ghi `battle/kho-dan.md`:

| Mã | Sự kiện specs thật | Bằng chứng | Mức | Tình huống (một câu, số liệu chạm biên) | Câu hỏi đóng | Đáp án chuẩn | Lẽ thường sẽ đoán |
|---|---|---|---|---|---|---|---|
| D-01 | Ngày lễ không tính vào ngày phép | A-02 | B | NV xin nghỉ 28/04–02/05, trong đó 30/04 và 01/05 là lễ | Hệ thống trừ mấy ngày phép? | 3 | 5 |

**Mức bằng chứng** (chỉ bắn A và B; C giữ lại cho Bước 2):

- **A**: brief nêu tường minh nghiệp vụ này **và** AI Khách hàng đã trả lời trong hỏi đáp của bạn.
- **B**: AI Khách hàng đã trả lời trong hỏi đáp của bạn.
- **C**: chỉ brief nhắc, chưa có câu trả lời. Có thể specs thật không định nghĩa.
- **Cấm**: mọi thứ AI Khách hàng đã trả lời "không quy định" (dòng `NGOÀI`), và mọi nghiệp vụ brief không nhắc (thuế, kế toán, bảo hiểm, pháp lý, hệ thống láng giềng brief không nói tới).

Ưu tiên trong kho: sự kiện `⚠` (ngược lẽ thường) đứng đầu, vì Executor thiếu luật sẽ đoán theo lẽ thường và lệch. Sự kiện trùng mặc định ngành đứng cuối: Executor đoán cũng đúng, chỉ TRÚNG khi spec đối thủ viết **sai**.

**Dạng tình huống được phép:** một bước, một actor, số liệu cụ thể chạm đúng mốc (đúng giờ hết hạn, đúng hạn mức, đúng ngày lễ), câu hỏi đóng về một kết quả: con số, trạng thái cuối, message hiện gì, nút ở trạng thái nào, có gửi mail không. **Không** nhiều bước, **không** "nếu … thì sao, còn nếu …", **không** hỏi ý kiến thiết kế.

## Bước 2 — Thu hoạch chéo từ spec đối thủ

Đọc **tất cả** spec được phân trước khi bắn spec nào. Mọi đội hỏi cùng một AI Khách hàng về cùng một đề, nên spec đối thủ là nguồn specs thật miễn phí.

1. Với mỗi spec, trích mọi **giá trị cụ thể** (số, mốc, trạng thái, message, quy tắc) thành bảng `Giá trị | Spec nào ghi | Trùng mặc định ngành?`.
2. Giá trị mà **≥ 2 spec cùng ghi** và **không phải mặc định ngành** → gần chắc trong specs thật. Thêm vào kho đạn với mức **ĐT** (đồng thuận), ngang mức B. Nếu trùng với dòng đã có mức C thì nâng C lên ĐT.
3. Giá trị **chỉ 1 spec ghi** và khác với sự kiện của bạn → đội đó chép sai hoặc đoán. Ghi chú để bắn đúng dòng đó.

## Bước 3 — Chọn test cho từng đối thủ

Với mỗi `battle/doi-thu/<mã>.md`:

1. **Dò lỗ hổng** bằng checklist Anatomy: thiếu mục nào trong 10 mục; bảng case thiếu loại bình thường / biên / lỗi; thiếu số, toán tử, múi giờ, mặc định, ưu tiên; message không nguyên văn; không tách guest; ma trận trạng thái có ô trống; hai chỗ mâu thuẫn; tham chiếu tài liệu ngoài; từ mờ ("phù hợp", "kịp thời", "có thể").
2. **Đối chiếu kho đạn** với spec: với mỗi dòng D-xx mức A / B / ĐT, xét spec đối thủ **im lặng**, **mơ hồ** hay **viết khác** đáp án chuẩn. Viết đúng → bỏ qua dòng đó cho đội này.
3. **Xếp hạng** ứng viên theo: bằng chứng phạm vi mạnh nhất trước (A > B = ĐT), rồi `⚠` trước, rồi im lặng / viết sai trước mơ hồ. Lấy đủ số test `the_le.md` quy định.
4. **Kiểm bằng đọc mù trước khi chốt.** Gọi subagent bằng Agent tool, prompt **chỉ gồm** spec đối thủ + tình huống + yêu cầu trả lời theo mẫu *"Kết quả: … | Căn cứ: trích dòng spec | hoặc: SPEC KHÔNG QUY ĐỊNH, tôi đoán …"*. Không đưa brief, kho đạn, hỏi đáp. Subagent trả **đúng đáp án chuẩn** → test này sẽ TRƯỢT, thay bằng ứng viên kế. Trả lệch hoặc "không quy định" → giữ.
5. Ghi `battle/tests/<mã>.md`:

   | # | Tình huống gửi đi (copy nguyên văn) | Đáp án chuẩn | Executor dự đoán (đọc mù) | Bằng chứng phạm vi | Vị trí lỗ hổng trong spec đối thủ |
   |---|---|---|---|---|---|

   Cột **Bằng chứng phạm vi** ghi đủ để kháng nghị: trích dòng brief, mã `A-xx` và trích câu trả lời khách hàng, hoặc "spec đội X mục 6.2 và đội Y mục 4 cùng ghi …". Thu **trước khi nộp**, vì test không sửa được sau khi nộp và kháng nghị có hạn giờ.

**Spec đối thủ hỏng, rỗng, sai định dạng, lệch đề, viết linh tinh:** không dừng, không bỏ lượt. Coi spec im lặng ở mọi chỗ, lấy thẳng các dòng kho đạn theo thứ tự xếp hạng, vẫn chạy đọc mù ở bước 4. Đây là đối thủ dễ ăn điểm nhất; rủi ro duy nhất là VÔ HIỆU, và bằng chứng phạm vi đã lo việc đó.

**Spec có luật bao quát "mọi ca khác → từ chối":** vẫn bắn nếu đáp án chuẩn không phải "từ chối". Executor trả "từ chối" là lệch.

**Không đủ ứng viên A / B / ĐT để lấp số test:** lấy tiếp mức C theo thứ tự brief nhắc rõ nhất, ghi rõ mức C trong file để đội quyết nộp hay bỏ slot. Bỏ slot là 0, VÔ HIỆU là âm; đội quyết theo điểm trong `the_le.md`.

## Bước 4 — `khang-nghi`

Gọi `/attack khang-nghi` và dán danh sách test bị VÔ HIỆU. Với mỗi ca, lấy cột Bằng chứng phạm vi từ `tests/<mã>.md`, viết một đoạn theo mẫu, ghi `battle/khang-nghi.md`:

> Test: <tình huống nguyên văn>. Tình huống thuộc phạm vi vì: (1) brief đoạn "…" nêu nghiệp vụ này; (2) AI Khách hàng đã trả lời "…" khi đội hỏi "…"; (3) <nếu có> spec đội X và Y cùng quy định. Đề nghị chấm lại theo đáp án "…".

Chỉ kháng nghị ca có ít nhất bằng chứng (1) hoặc (2). Ca chỉ có mức C mà không có gì thêm: không kháng nghị, ghi lý do. Số ca tối đa theo `the_le.md`; xếp ca có bằng chứng mạnh nhất trước.

## Điều không bao giờ làm

- Không bịa tình huống từ lỗ hổng spec đối thủ khi không có sự kiện specs thật đứng sau.
- Không bắn vào nghiệp vụ AI Khách hàng đã nói "không quy định" hay brief không nhắc.
- Không nộp test chưa qua đọc mù.
- Không bắn hai test vào cùng một lỗ hổng của cùng một đối thủ.
- Không ghi số test, số spec, điểm vào skill; đọc từ `the_le.md`.
