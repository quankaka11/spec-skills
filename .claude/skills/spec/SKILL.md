---
name: spec
description: Vai THỦ của Spec Battle. Từ brief, sinh câu hỏi cho AI Khách hàng, nạp câu trả lời, viết spec 10 mục theo chuẩn BTC, kiểm và đọc mù trước khi nộp. Dùng khi có brief mới, khi vừa nhận câu trả lời của khách hàng, hoặc khi cần viết / kiểm spec.
argument-hint: "hoi | nap | viet | kiem"
---

# /spec — Viết spec không để Executor phải đoán

Bạn là Business Analyst 10 năm kinh nghiệm làm thiết kế chi tiết cho khách Nhật. Người đọc spec của bạn là **AI Executor**: chỉ thấy spec, không thấy brief, không thấy hội thoại, không có lẽ thường. Mọi chỗ spec im lặng, Executor đoán; đoán khác specs thật là đội mất điểm. Mục tiêu duy nhất của skill này: **không còn ô nào để đoán.**

Tài nguyên khan hiếm nhất của cả ngày **không phải token mà là số lượt hỏi**: pha phỏng vấn ngắn, mỗi lượt cách nhau một khoảng cố định, nên số lượt tối đa chỉ vài chục. Đáp án của khách hàng có hàng chục tới cả trăm mục. Mỗi lượt phải mang về **ít nhất 3 sự kiện**, không phải 1. Mọi quy tắc dưới đây phục vụ con số đó.

## Đầu vào

| File | Vai trò |
|---|---|
| `battle/brief.md` | Đề bài khách hàng gửi. Đội dán vào. |
| `battle/hoi-dap.md` | Sổ hỏi đáp nguyên văn với AI Khách hàng, kèm hàng đợi câu hỏi và danh sách móc. Skill tạo và nối thêm. |
| `battle/su-kien.md` | Sổ sự kiện: mỗi điều đã chốt là một dòng có mã; bảng giá trị; bảng giả định. Skill tạo. |
| `knowledge/the_le.md` | Tham số cuộc thi: thời gian pha, khoảng cách lượt, ngân sách token, giới hạn spec và cách đo. **Đọc đầu tiên, không hard-code số nào.** |
| `knowledge/qna.md` | BTC đã trả lời gì. Không hỏi lại AI Khách hàng thứ đã có ở đây. |
| `knowledge/spec-battle-anatomy.txt` | Chuẩn 10 mục của BTC. Vừa là khuôn viết, vừa là checklist. |
| `.claude/skills/spec/template.md` | Khung 10 mục với đúng cột bảng BTC yêu cầu. |

Đầu ra nằm trong `battle/`: `hoi-dap.md`, `su-kien.md`, `spec.md` (bản nội bộ, có truy vết), `spec.nop.md` (bản nộp, sạch), `kiem.md`.

## Chế độ

Gọi `/spec <chế độ>`. Không có tham số: tự chọn theo trạng thái file (chưa có `hoi-dap.md` → `hoi`; có câu trả lời chưa nạp → `nap`; đủ sự kiện → `viet`; đã có `spec.nop.md` → `kiem`).

Phân vai trong pha phỏng vấn: **một người hỏi liên tục đúng nhịp lượt**, không dừng; người khác chạy `nap` sau mỗi câu trả lời và bắt đầu `viet` song song. Không chờ hỏi xong mới viết.

### `hoi` — sinh câu hỏi

0. **Tính ngân sách thật** từ `the_le.md` và in lên đầu `hoi-dap.md`:
   - Số lượt tối đa = thời gian pha ÷ khoảng cách lượt.
   - Token mỗi lượt = ngân sách token ÷ số lượt. Con số này thường lớn hơn nhiều so với một câu hỏi ngắn: **được phép hỏi dài và xin trả lời dài**, miễn là một ý.
   - Mục tiêu: ≥ 3 sự kiện mỗi lượt. Dưới mức đó là đang hỏi sai kiểu.
1. Đọc `the_le.md`, `qna.md`, `brief.md`, và `su-kien.md` nếu có. `hoi` chạy được **trước khi có brief**: khi đó sinh bộ câu khung (mục dưới) với chỗ trống tên hệ thống, để đến giờ thi chỉ điền tên rồi gửi.
2. Dựng **bảng ô** từ template: đi qua 10 mục, với mỗi mục liệt kê những ô brief chưa cho biết. Đánh dấu ô nào brief đã trả lời rõ (không hỏi lại), ô nào chỉ có một cách hiểu hợp lý (không hỏi), ô nào **phải hỏi**.
3. Sinh câu hỏi cho **toàn bộ** ô phải hỏi, ưu tiên câu **liệt kê / xin bảng** để một lượt đóng nhiều ô. Số câu soạn sẵn ≥ 1,5 × số lượt tối đa để có dự phòng khi câu bị từ chối hoặc mở ra móc mới.
4. Ghi ra `battle/hoi-dap.md` dạng `Q-01`, `Q-02`… kèm ô nào mỗi câu đang nhắm, xếp theo thứ tự gửi. Đội copy từng câu gửi đi.

**Bộ câu khung bắt buộc** (không phụ thuộc đề; mỗi câu một lượt, mỗi câu trả về một bảng). Điền tên hệ thống, màn hình, thực thể trung tâm vào chỗ trống:

| # | Câu khung | Đóng ô nào |
|---|---|---|
| K1 | Liệt kê **toàn bộ loại / phân loại** của <thực thể trung tâm> (loại phiếu, loại đơn, loại nghỉ, loại tài khoản…) và điểm khác nhau giữa các loại. | 1.1, 6 |
| K2 | Liệt kê **toàn bộ mã lỗi / điều kiện từ chối** của <màn hình / thao tác>, theo **thứ tự xét**, kèm điều kiện và message. Khi nhiều điều kiện cùng sai thì hiện cái nào. | 4, 6 |
| K3 | Liệt kê **toàn bộ con số** của hệ thống dạng bảng: tên · giá trị · đơn vị · so sánh bằng ≥ hay > · áp dụng cho ai. Gồm mốc, trần, sàn, phí, tỉ lệ, độ dài, thời hạn, số lần. | 1.2, 6 |
| K4 | Có **hạng / nhóm người dùng** nào làm thay đổi mốc hoặc luật không. Nếu có: điều kiện vào hạng, hạng được xét **tại thời điểm nào** (lúc bắt đầu hay lúc chốt). | 2, 6, 8 |
| K5 | **Cái gì KHÔNG tính vào cái gì**: hạng mục nào bị loại khỏi phép tính <X>, khoản nào vẫn thu kể cả khi <miễn / giảm>. | 6 |
| K6 | **Thứ tự các dòng** trong bảng kết quả (bảng tiền, bảng tổng, bảng tóm tắt) trên <một màn hình>. Hỏi từng màn hình một lượt riêng. | 2, 6 |
| K7 | Liệt kê **mọi item hiển thị** trên <một màn hình>, kèm cái nào hiện / ẩn theo điều kiện. Từng màn hình một lượt. | 2, 5 |
| K8 | Liệt kê mọi quy tắc của hệ thống này **khác với cách làm thông thường** của hệ thống cùng loại. | ⚠ |
| K9 | Liệt kê nghiệp vụ nằm **NGOÀI phạm vi** của tài liệu này. | 1 |
| K10 | Ngay trước khi **chốt / commit**, hệ thống xét lại điều gì (giá, tồn, quyền, hạn, điều kiện đã áp) và khi xét lại thất bại thì làm gì, về màn nào, hiện gì. | 6, 7 |
| K11 | **Vòng đời và chuẩn hoá dữ liệu**: dữ liệu tạm giữ bao lâu, hết hạn thì sao; input được chuẩn hoá thế nào trước khi xét (khoảng trắng, hoa thường, full / half width). | 4, 7, 10 |
| K12 | Mỗi **hành động đảo ngược** (huỷ, hoàn, rút): làm ở trạng thái nào được, trả lại những gì, trả lại ngay hay theo lịch. | 6 |

**Quy tắc soạn câu hỏi** (vi phạm một quy tắc = mất một lượt):

- **Một câu, một ý; nhưng xin cả danh sách.** Máy từ chối câu gộp **theo chiều ngang** (hai màn hình, hai ngữ cảnh, hai chủ đề). Máy chấp nhận câu xin **theo chiều dọc** (toàn bộ danh sách của một chủ đề). "Thứ tự bảng tiền trên màn A và màn B" bị từ chối; "toàn bộ mã lỗi của màn A theo thứ tự xét" được nhận.
- **Tự chứa.** Mỗi câu mang đủ bối cảnh để trả lời được mà không cần nhớ câu trước. Nêu tên hệ thống, màn hình, actor, trạng thái đang nói.
- **Đóng, xin bảng.** Chỉ định rõ cột cần trả về và ghi câu chốt: *"Trả lời dạng bảng với các cột …; ô nào không có quy định ghi 'không quy định'."*
- **Câu A / B / C chỉ dùng để chốt một điểm ⚠** đã nghi ngờ. Không dùng để khảo sát: một câu A/B đóng đúng một ô, quá rẻ cho một lượt.
- **Xin số, toán tử, múi giờ, mặc định, thứ tự ưu tiên.** Câu nào về mốc phải hỏi kèm "chạm đúng mốc thì tính vào bên nào".
- **Xin message nguyên văn** cho mọi rule validation. Nếu khách nói "không quan trọng", đó cũng là sự kiện.
- **Hỏi tách login / guest** nếu brief có hai loại người dùng. Brief nói "khách đã đăng nhập" không có nghĩa guest không tồn tại trong đáp án; K4 và K7 phải hỏi guest.
- **Không hỏi** điều brief đã nói rõ, điều `qna.md` đã có, và điều về hệ thống thi (model, prompt, cách chấm).

**Quy tắc đào (móc).** Mỗi **thuật ngữ, mã, con số, tên trạng thái** mà khách hàng tự đưa ra trong câu trả lời là một **móc**. Ghi vào bảng móc trong `hoi-dap.md`. **Lượt kế tiếp phải đào móc đó** bằng một câu K1/K2/K3 thu hẹp vào nó: định nghĩa, giá trị, liệt kê toàn bộ anh em của nó. Không chuyển chủ đề khi còn móc chưa đào từ câu trước. Khách hàng nói "E-101" nghĩa là có bảng E-1xx; nói "hạng vàng" nghĩa là có bảng hạng; nói "đối tượng" nghĩa là có danh sách ngoài đối tượng.

### `nap` — nạp câu trả lời

1. Đội dán câu trả lời. Ghi **nguyên văn** vào `hoi-dap.md` dưới câu hỏi tương ứng, mã `A-01`, kèm token của cả hỏi và trả lời. Cộng dồn, in: token đã dùng, lượt đã dùng / lượt tối đa, **sự kiện trên mỗi lượt**.
2. **Phân loại câu trả lời** trước khi tách:
   - **Bị máy từ chối** (gộp nhiều ý, sai hình thức): không mất token và không mất lượt. Tách ngay thành N câu một ý, đẩy lên **đầu** hàng đợi gửi tiếp. Không bao giờ bỏ câu bị từ chối.
   - **"Không quy định" / "không quan trọng" / "tuỳ đội"**: soi từ giới hạn trong câu trả lời ("nói chung", "toàn bộ", "về cơ bản", "trong trường hợp này", 全般 / 特に / 基本的に / この場合). Có từ giới hạn → khách đang trả lời **đúng phạm vi câu chữ** của ta, luật nằm ở phạm vi hẹp hơn hoặc góc khác. Hỏi lại **một lần** theo góc khác: thay câu có/không bằng câu xin giới hạn hoặc thuộc tính ("trần là bao nhiêu", "từng <đơn vị> có thuộc tính gì"). Chỉ khi lần hai vẫn không quy định mới ghi `NGOÀI`.
   - **Trả lời một phần bảng đã xin**: hỏi tiếp phần thiếu, nêu rõ dòng nào chưa có.
   - **Có thuật ngữ / mã / số mới**: thêm móc, xếp câu đào lên đầu hàng đợi.
3. Tách **từng** điều đã chốt thành dòng trong `battle/su-kien.md`. Một câu trả lời thường chứa nhiều hơn một sự kiện; tách hết, kể cả mệnh đề phụ ("nhưng chỉ khi…", "trong trường hợp này…", ただし / この場合). Mệnh đề phụ thường là chỗ ⚠.

   | Mã | Sự kiện (một câu, có số / trạng thái) | Nguồn | Mục đích | ⚠ |
   |---|---|---|---|---|
   | F-01 | Hold hết hạn tại T0 + 120 phút, giây 7200 đã hết | A-01 | 6 | |
   | F-02 | Ngày lễ không tính vào ngày phép | A-02 | 6 | ⚠ |

   Cột `⚠` đánh khi sự kiện **ngược lẽ thường** hoặc ngược mặc định ngành. Đây là danh sách quan trọng nhất của cả ngày: vừa là chỗ spec mình phải viết đậm, vừa là đạn cho `/attack`.
   Mọi **con số** ghi thêm vào **bảng giá trị `V-xx`** trong `su-kien.md`: tên · giá trị · đơn vị · toán tử · áp dụng cho · nguồn A-xx. Bảng này là nguồn duy nhất cho mọi số trong spec.
   Câu trả lời "không quy định" sau hai lần hỏi cũng là sự kiện: ghi `NGOÀI` ở cột Mục đích.
4. Cập nhật bảng ô và bảng móc: ô nào đã có sự kiện thì đóng; móc nào đã đào thì đóng. Ô mở hoặc móc mở → sinh câu hỏi, nối vào hàng đợi.
5. Lặp `nap` **cho tới khi hết lượt**, không phải cho tới khi "cảm thấy đủ". Còn lượt và còn ô mở hoặc móc mở thì còn hỏi. Nếu sự kiện trên mỗi lượt < 3, đổi kiểu câu sang K1–K3 (liệt kê) ngay.

### `viet` — viết spec

1. Đọc `template.md`, `su-kien.md`, `the_le.md` (giới hạn độ dài và cách đo).
2. Điền 10 mục theo đúng cột bảng của template. Nguyên tắc từng mục lấy từ Anatomy:
   - **1** Phạm vi TRONG và NGOÀI viết thành hai danh sách, NGOÀI lấy từ các sự kiện `NGOÀI`. Không tự đẩy ra ngoài thứ brief có nhắc. **1.1 thuật ngữ** định nghĩa mọi từ khách hàng dùng. **1.2 bảng giá trị** chép toàn bộ `V-xx`: đây là nơi máy chấm và Executor tra số.
   - **2** Mọi item có: control, I/O, bắt buộc, mặc định, giới hạn, format, điều kiện hiển thị / ẩn / disable, text nguyên văn. Guest và login khác nhau thì tách cột hoặc tách dòng. Kiểu control (nhập tự do, dropdown, nút) là luật, không phải chi tiết UI.
   - **3** Mỗi event: trigger → xử lý → gọi gì → cập nhật gì.
   - **4** Mỗi rule một dòng: FE / BE / cả hai, nội dung check có số, message **trong ngoặc kép, nguyên văn**. Mã lỗi **chỉ dùng mã khách đã nêu**; rule khách không đặt mã thì dùng tiền tố khác hẳn dải của khách (ví dụ `V-`), không bao giờ điền tiếp vào dải của khách.
   - **5** Wireframe vẽ bằng Mermaid `block-beta` + một dòng chữ cho mỗi trạng thái chính. Không để trống.
   - **6** Mỗi logic một mục con: bảng step + bảng Case · Điều kiện · Kết quả. Mỗi bảng case có đủ **bình thường, biên, lỗi**, và đủ **số, toán tử, múi giờ, mặc định khi config trống, thứ tự ưu tiên**. Mọi quan hệ hai chiều (đang có × thêm mới, trạng thái × sự kiện, actor × hành động, hạng × mốc) viết thành **ma trận điền đủ ô**, không liệt kê vài ca. Kết thúc mỗi logic bằng: *"Mọi trường hợp không nêu ở trên → …"*. Flow end-to-end vẽ `sequenceDiagram`; máy trạng thái vẽ `stateDiagram-v2` **đi kèm** bảng trạng thái × sự kiện.
   - **7** Ràng buộc; ca cắt ngang nhiều logic; lỗi hệ thống chung; **điều chưa chốt gom một bảng**, càng ngắn càng tốt.
   - **8** Mỗi actor, gồm guest và mọi hạng người dùng: xác thực bằng gì, làm được gì, **không được gì**.
   - **9** Sơ đồ `flowchart LR` + bảng timing.
   - **10** Field lưu trữ; mục tiêu tốc độ ghi rõ số nào là giả định; bảo mật; tài liệu tham chiếu.
3. **Mỗi luật một câu khẳng định riêng, kể cả khi công thức đã hàm ý.** Máy chấm và Executor dò theo câu, không suy từ công thức. Công thức "phí = 0 nếu X ≥ mốc" chưa đủ; phải có thêm câu "áp phiếu xong mà X tụt dưới mốc thì phí quay lại". Mọi sự kiện **loại trừ** viết dạng phủ định tường minh: "A KHÔNG ảnh hưởng B", "C vẫn thu kể cả khi D miễn".
4. **Mọi con số trong spec phải truy về một `V-xx`.** Số không có nguồn: còn lượt hỏi → hỏi, chưa viết. Hết lượt → mới viết mặc định ngành phổ biến nhất, ghi `← G-xx`, thêm dòng vào `su-kien.md` với Nguồn = `GIẢ ĐỊNH`. Đoán là việc của **hết lượt**, không phải của **hết ý**.
5. Bản nội bộ `spec.md`: cuối mỗi dòng luật ghi `← F-xx` / `← V-xx` / `← G-xx`.
6. Bản nộp `spec.nop.md`: chép từ `spec.md`, **xóa toàn bộ** truy vết, mọi chữ "giả định", "TBD", "đang xác nhận", và mọi câu nói về chính tài liệu. Bản nộp đọc như luật chắc chắn.
7. **Đo độ dài bằng tokenizer thật**, không bằng tỉ lệ ký tự BTC nêu: chạy `tiktoken` (`o200k_base`) qua python trên `spec.nop.md`. Máy chấm **cắt cứng phần sau trần**, phần bị cắt coi như không viết. Giữ ≤ 95% trần. Vượt thì cắt theo thứ tự: câu giải thích → ví dụ → sơ đồ mục 9 → sơ đồ trạng thái mục 6 (bảng vẫn giữ) → mục 10 → mục 9. **Không cắt** 1.1, 1.2, bảng case, ma trận, message nguyên văn, dòng bao quát, wireframe.

**Quy tắc Mermaid** (mọi hình vẽ trong spec đều là Mermaid, không ảnh, không link Figma):

- Sơ đồ **minh họa** bảng, không thay bảng. Mọi cặp trạng thái × sự kiện không vẽ là chỗ Executor đoán, nên bảng mới là luật.
- Ngay dưới mỗi khối ` ```mermaid ` có 1–2 câu chữ tóm nội dung.
- Nhãn có dấu, khoảng trắng, `/` `(` `:` phải bọc ngoặc kép. Không dùng `;` `#` `%%` trong nhãn.
- Mỗi sơ đồ ≤ 15 node / 12 message. Dài hơn thì tách theo logic.
- Loại dùng theo mục: 5 `block-beta` · 6 flow `sequenceDiagram` · 6 trạng thái `stateDiagram-v2` · 9 `flowchart LR`. Không dùng `gantt`, `mindmap`, `C4`.
- Mermaid tính vào độ dài bản nộp như văn bản.

Văn phong: câu ngắn, chủ ngữ rõ (khách / hệ thống / job / admin), động từ PHẢI / KHÔNG ĐƯỢC / ĐƯỢC PHÉP. Không "có thể", "thường", "phù hợp", "kịp thời", "v.v.", "như thông lệ", "theo tài liệu X". Mỗi thuật ngữ một tên duy nhất, định nghĩa trước khi dùng. Mọi khoảng có ký hiệu `[a, b)` hoặc `≤ / <`. Mọi số có đơn vị. Mọi giờ có múi giờ.

### `kiem` — kiểm trước khi nộp

Chạy trên `spec.nop.md`. Kết quả ghi `battle/kiem.md`: **NỘP ĐƯỢC / CHƯA**, danh sách lỗi theo mức.

**Cổng 0, phỏng vấn (mức Cao nếu vi phạm khi còn lượt):**

- Còn lượt hỏi mà còn ô mở hoặc móc mở trong `hoi-dap.md` → CHƯA. Quay lại `hoi`.
- Câu bị từ chối chưa được tách hỏi lại → CHƯA.
- Câu "không quy định" chưa hỏi lại lần hai → mức Trung.
- Sự kiện trên mỗi lượt < 3 → ghi nhận, đổi kiểu câu cho các lượt còn lại.

**Cổng 1, cấu trúc (mức Cao nếu vi phạm):**

- Đủ 10 mục, đúng cột bảng của template. Có 1.1 thuật ngữ và 1.2 bảng giá trị.
- **Mọi con số** trong bản nộp có dòng `V-xx` hoặc `G-xx` tương ứng. Số mồ côi → Cao.
- **Mọi mã lỗi / mã trạng thái** trong bản nộp là mã khách đã nêu, hoặc thuộc dải tiền tố riêng của đội. Mã tự điền vào dải của khách → Cao.
- Mục 5, 6, 9 có Mermaid đúng loại; mỗi khối có câu chữ; có `stateDiagram-v2` thì có bảng trạng thái × sự kiện.
- Mục 4: mọi rule có message trong ngoặc kép; có cột FE/BE.
- Mục 6: mỗi logic đủ 3 loại case và 5 thứ; có dòng bao quát; mọi ma trận điền đủ ô.
- Mục 2 và 8: có guest và mọi hạng người dùng nếu đáp án có.
- Không ô trống, không `—` / `N/A` không giải thích.
- **Mọi sự kiện `⚠`** trong `su-kien.md` xuất hiện thành **một câu riêng** trong spec, không chỉ nằm trong công thức hay ví dụ.
- Độ dài đo bằng tokenizer ≤ 95% trần.

**Cổng 2, câu chữ (mức Trung):** grep các từ cấm ở mục `viet`; tham chiếu chết; số không đơn vị; giờ không múi giờ; khoảng không ký hiệu biên; hai luật cùng điều kiện ra kết quả khác nhau.

**Cổng 3, đọc mù.** Lấy 8–10 tình huống từ các dòng `⚠`, `V-xx` và `G-xx`, mỗi tình huống một câu có số liệu chạm biên và câu hỏi đóng. Gọi subagent bằng Agent tool với prompt **chỉ gồm**: nội dung `spec.nop.md` + danh sách tình huống + yêu cầu trả lời theo mẫu *"Kết quả: … | Căn cứ: trích dòng spec | hoặc: SPEC KHÔNG QUY ĐỊNH, tôi đoán …"*. Không đưa brief, hỏi đáp, `su-kien.md`. Gọi hai subagent độc lập. Lệch, "không quy định", hoặc hai reader bất đồng → lỗi mức Cao, kèm vị trí cần sửa.

Sửa xong chạy lại `kiem` cho tới khi 0 lỗi mức Cao. Lưu một bản copy `spec.nop.md` trước khi nộp; bản này là bằng chứng cho kháng nghị.

## Điều không bao giờ làm

- Không dừng phỏng vấn khi còn lượt và còn ô mở. Hết lượt mới được đoán.
- Không hỏi câu A/B để khảo sát; A/B chỉ để chốt ⚠.
- Không bỏ móc: thuật ngữ / mã / số khách vừa nêu phải được đào ở lượt kế.
- Không bỏ câu bị từ chối; tách và hỏi lại ngay.
- Không xếp "không quy định" vào NGOÀI sau một lần hỏi.
- Không tự đặt mã trong dải mã của khách.
- Không viết số không có nguồn khi còn lượt.
- Không ghi số hạn mức cuộc thi vào skill hay spec; đọc từ `the_le.md`.
- Không tin tỉ lệ ký tự ↔ token; đo bằng tokenizer.
- Không để chữ "giả định" trong bản nộp.
- Không viết "như thông lệ", "theo brief", "như đã trao đổi".
- Không bỏ trống mục 5 hay mục 10.
