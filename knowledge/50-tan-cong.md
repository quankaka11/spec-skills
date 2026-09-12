*Dùng cho vai CÔNG 13:00–15:00 (3 spec đối thủ, 15 test) và red team nội bộ 11:38.*

**Ba tham số 09/09 đổi cách chơi vai CÔNG (00 §A, §D1):** điểm **TRÚNG +2 / VÔ HIỆU −1** ⇒ một test đáng bắn khi `P(TRÚNG) > P(VÔ HIỆU)/2`, và bỏ test là 0 điểm chứ không phải −1. **Spec đối thủ là markdown tải về được** ⇒ grep được, và so chéo 3 spec được (§4b). **Test không sửa được sau khi nộp và chỉ ca VÔ HIỆU được kháng nghị** ⇒ bằng chứng phạm vi phải thu xong *trước* khi nộp test, không phải lúc 16:00.

# 50 — Sổ tay tấn công

Quy ước: `<TTL>`, `<QTY_MAX>`, `<HOLD_MAX>`, `<GH_MAX>` (số lần gia hạn), `<%CỌC>`, `<TZ>` = số lấy từ AI Khách hàng buổi sáng; điền số thật trước khi nộp. Dùng thời điểm tuyệt đối (10:00:00) thay vì "sau TTL".

## 0. Bài học thi thử 11/09 — 2/2 viên TRƯỢT, và vì sao

Trận thử đề ハナマルストア: kit sinh 2 test cho spec đội A, **cả hai TRƯỢT**. Đọc lại bằng ground truth (`battle/ground-truth-thi-thu.md`) thì hai viên trượt vì **hai lỗi khác nhau, cả hai đều nằm trong quy trình cũ, không phải do xui**:

| Viên | Giả thiết kit đặt ra | Sự thật của đề | Lỗi của quy trình |
|---|---|---|---|
|T-A1 — しきい値 4.000円|"AI Khách hàng nói **một** 「しきい値」 số ít, không nhắc hạng thành viên ⇒ đối thủ tự bịa hai ngưỡng 5.000/3.000 theo hạng"|V-03: mốc **đúng là** 5.000円 thường / 3.000円 Gold. Đối thủ **biết nhiều hơn ta**|**Im lặng của AI Khách hàng bị đọc thành bằng chứng phủ định.** Khách trả lời đúng một ẩn số mỗi lượt; những gì khách không nhắc **không phải** là những gì không tồn tại|
|T-A2 — coupon hết hạn giữa giỏ và xác nhận|"specs thật gần như chắc chắn tái kiểm hạn lúc chốt, nếu không thì mã hết hạn giảm giá vô thời hạn"|V-10: hạn xét **tại lúc ÁP**, không phải lúc chốt. GTD còn ghi rõ đây là "điểm dễ nhầm nhất của đề"|**Cược vào đáp án chuẩn bằng suy luận "hợp lý".** Suy luận hợp lý là đúng thứ mà đề cố tình bẻ — và cũng là đúng thứ Executor mù sẽ đoán|

Ba hệ quả, đã thành luật cứng ở §2-13…§2-16:

1. **Không có lời khách nguyên văn về ĐÁP ÁN ⇒ không được lấy suy luận thay vào.** Bằng chứng mức 1/2/3 ở §2-9 chỉ chứng minh **phạm vi** (chống VÔ HIỆU). Nó không chứng minh ta biết đáp án. Hai việc khác nhau, kit cũ trộn làm một.
2. **Đừng bắn vào con số đối thủ tự đặt** khi ta chỉ có im lặng để chống lại nó. Đối thủ có thể đã hỏi được ô ta không hỏi.
3. **TRƯỢT không phải 0 điểm** — nó cho đội thủ +1. Xem §6 luật 9: ngưỡng nộp thật là `P(TRÚNG) > 1/3`, không phải `> P(VÔ HIỆU)/2`.

**Nhưng bài học đắt nhất là chỗ kit đã đúng mà không dám đi tiếp.** Bước dry-run loại đúng 4 ứng viên mạnh (A-12/A-14/A-15/A-17: đối thủ im lặng nhưng mặc định ngành trùng specs thật). Loại là đúng. Sai là ở chỗ **thay chúng bằng hai canh bạc thay vì đổi nguồn đạn**. Nguồn đạn đúng có sẵn ngay trong file đối thủ mà kit đọc rồi bỏ qua: **mục 8「Điểm chưa chốt」 I-01…I-05 — chỗ đối thủ TỰ KHAI là chưa biết.** Đối chiếu với ground truth thì 3 trong 5 dòng đó có luật riêng, phản trực giác, và Executor buộc phải bịa:

| Đối thủ tự khai chưa chốt | Đề có luật | Executor mù sẽ nói |
|---|---|---|
|I-05 nhập lại coupon khi đã áp một coupon thì báo gì|R-214 + DT-05: **E-105 (併用不可)**, kể cả khi nhập **đúng mã đang áp**; KHÔNG thay thế cái cũ|"đã áp rồi" / "thay bằng mã mới" — lệch|
|I-01 còn mã lỗi nào từ E-104 trở đi|DT-03: còn **E-104** (giỏ không có 対象商品), **E-105**, **E-106** (phiếu 1 lần đã dùng), và thứ tự xét 1→6|Chỉ biết E-101/102/103 ⇒ ca "giỏ toàn hàng sale" trả về E-103 thay vì E-104|
|I-02 điểm có trả được phí ship không|V-06 + V-07 + R-303: điểm chỉ dùng **bội số 100**, trần 50% 税抜商品小計, không ảnh hưởng 判定額|Cho dùng 1.250pt — lệch ngay ở đơn vị|

⇒ **Luật nguồn đạn mới (§4 bước 0):** đọc mục "Điểm chưa chốt / TBD / 確認中 / 課題" của spec đối thủ **trước mọi bước khác**. Đó là danh sách đối thủ tự viết ra những chỗ Executor của họ sẽ phải bịa.

## 1. Bảng 27 loại lỗ hổng (#16–#20 thêm 09/09; #21 sau họp BTC; #22–#24 từ cấu trúc 10 mục 11/09)

> Loại #22–#24 sinh ra từ cấu trúc spec BTC 10 mục (knowledge/33). Đội nào viết spec theo lối luật nghiệp vụ thuần (không có mục 2/3/4) sẽ hở toàn bộ ba loại này.

| # | Tên | Dấu hiệu trong spec (Ctrl+F / thiếu mục) | Probe §5 | Tỷ lệ TRÚNG | Rủi ro VÔ HIỆU |
|---|---|---|---|---|---|
| 1 | Khoảng trống hoàn toàn (silent gap) | Heading thiếu cả một nhóm N1–N14 [HD §3.5]; Ctrl+F "cọc", "hoàn", "gia hạn", "guest" = 0 hit | P20 / P21 | Cao | Thấp |
| 2 | Ngoại lệ vắng mặt | Chỉ có luồng tạo → thanh toán → xong; không có mục "Ngoại lệ"/"Lỗi" | P2 / P3 | Cao | Thấp |
| 3 | Định lượng mơ hồ | Hit danh sách đen [HD §4.6]: "nhanh", "một khoảng thời gian", "hợp lý", "nhiều" | P4 | Cao | Thấp |
| 4 | Biên & off-by-one | Có số nhưng thiếu "≤/<", "bao gồm/không bao gồm", "[đầu, cuối)" | P1 | Cao | Thấp |
| 5 | Mâu thuẫn nội tại | Cùng tham số có 2 giá trị ở 2 mục; bảng khác văn bản | "Khách VIP tạo hold 10:00. Hold hết hạn đúng lúc nào?" (khi §A nói 60 phút, §B nói 120) | Cao | Thấp |
| 6 | Trạng thái không xác định | Không có bảng trạng thái; bảng có ô trống; thiếu trạng thái "hết hạn chưa dọn" | P36 / P42 | Cao | TB |
| 7 | Đồng thời / race condition | Ctrl+F "đồng thời", "cùng lúc", "first-come", "khóa" = 0 hit | P13 | Cao | Thấp |
| 8 | Thiếu ưu tiên xung đột | Nhiều luật cùng chủ đề; không có "ưu tiên", "ghi đè", "trừ khi" | P35 | TB | Thấp |
| 9 | Actor bị bỏ sót | Chỉ có "khách hàng"; không nhắc guest, VIP, CSKH, admin, cron | P24 | Cao | Thấp |
| 10 | Quyền hạn không rõ | Không có bảng actor × hành động | P26 | Cao | TB |
| 11 | Đơn vị / múi giờ / lịch | Không có <TZ>, "UTC", "ngày lễ"; dùng "giờ làm việc" không định nghĩa | P28 | Cao | TB |
| 12 | Rollback / bù trừ thiếu | Không có "hoàn nguyên", "rollback", "nhả tồn" | P33 | Cao | TB |
| 13 | Phụ thuộc ngoài không định nghĩa | Nhắc ERP/cổng thanh toán/kho nhưng không nêu nguồn sự thật, timeout, retry | P16 | TB | TB |
| 14 | Từ ngữ đa nghĩa | Dùng lẫn "giữ hàng"/"đặt trước"/"đặt cọc"/"khóa hàng"/"giỏ hàng" | "Khách 'đặt trước' 1 sản phẩm tồn = 0 sắp về. Hold được tạo hay từ chối?" | TB | TB |
| 15 | Luật phản trực giác bị im lặng | Đối chiếu danh sách ⚠ buổi sáng; spec không nói → bắn thẳng [HD §2.3] | P19 | Rất cao | Thấp |
| 16 | **Luật bất khả thi** — hứa hộ bên ngoài | Grep nhóm 40 §2-17: `hoàn tất|tiền về|đối soát xong` + số giờ/ngày. Specs thật **không thể** viết vậy ⇒ mọi ca hoàn tiền lệch | P43 / P44 | Rất cao | Thấp |
| 17 | **Luật tự đánh bại mục tiêu** | Brief có mục tiêu chống-gì-đó, spec có luật mở cửa cho đúng việc đó (khóa tài nguyên trước khi thu tiền/xác thực; hủy phí 0 không trần) | P45 / P46 | Cao | Thấp |
| 18 | **Hạn mức không cưỡng chế được** | Grep nhóm 40 §2-18: hạn mức neo vào email/SĐT/tên khách tự khai | P47 | Rất cao | Thấp |
| 19 | **Thiếu luật cho thất bại của phụ thuộc ngoài** | Spec nhắc cổng/ERP/job/thông báo nhưng không có nhánh lỗi nào (nhóm 40 §2-19). Đối chiếu 12 ca 05 §M6 | P48–P51 | Rất cao | Thấp |
| 20 | **Phạm vi NGOÀI tự đặt rộng hơn brief** | Đọc danh sách NGOÀI phạm vi của đối thủ, so với brief: nghiệp vụ nào brief nhắc mà họ đẩy ra ngoài | P52 | Cao | **Thấp** — vì brief là căn cứ chung, không phải suy diễn của ta |
| 21 | **Lệch đồng thuận chéo** (mới 09/09) | Cả 3 spec tải về đều bàn một nghiệp vụ, **một** spec im lặng hoặc nói khác hai spec kia (§4b) | theo chủ đề | Cao | **Thấp** — hai spec kia là bằng chứng nghiệp vụ nằm trong phạm vi |

**Vì sao #21 rẻ nhất.** Nó không cần biết specs thật: chỉ cần ba file markdown tải về và một bảng đối chiếu. Hai đội khác viết luật cho nghiệp vụ X là bằng chứng gián tiếp rằng X thuộc phạm vi (họ đã hỏi, hoặc brief có nói), nên rủi ro VÔ HIỆU thấp; còn đội im lặng thì Executor phải đoán. Cẩn trọng duy nhất: **cả 3 im lặng không phải bằng chứng ngoài phạm vi** — đó là điểm mù chung, muốn bắn thì phải có brief hoặc log đỡ.

**Vì sao #16–#20 là đạn tốt nhất năm nay.** Chúng nhắm vào *nội dung* chứ không vào *cách viết*, nên (a) đội nào cũng hở — checklist phổ biến chỉ dạy chống mơ hồ; (b) rủi ro VÔ HIỆU thấp vì đều nằm trong core flow tiền/tồn; (c) đáp án chuẩn dễ có trong log nếu buổi sáng đã hỏi nhóm N0 (20 §2). Đo trên `battle/spec.nop.md` — một spec đạt mọi cổng hình thức: 0 hit mức Cao ở loại #3/#4/#5/#6, nhưng **7 điểm bắn** ở #16–#20 (knowledge/32 §7).
| 22 | **Message lỗi không nguyên văn** (mục 4 BTC) | Mục 4 vắng, hoặc chỉ ghi "hiển thị thông báo lỗi" / "báo lỗi phù hợp" — không có chuỗi trong ngoặc kép | P53 | **Rất cao** | Thấp |
| 23 | **Guest vs login không phân biệt** (mục 2, 8 BTC) | Mục 2 không có cột guest; mục 8 không có dòng Guest; Ctrl+F "guest / chưa đăng nhập" = 0 hit | P54 | **Rất cao** | Thấp |
| 24 | **Trạng thái UI không xác định** (mục 2, 3 BTC) | Không nói nút disable/ẩn khi nào; không nói sau thao tác màn hình hiển thị gì; bảng Case thiếu cột "hiển thị / trạng thái nút" | P55 | Cao | TB |

| 25 | **Ô đối thủ TỰ KHAI chưa chốt** (mục "Điểm chưa chốt / TBD / 確認中 / 課題") | Có mục liệt kê câu hỏi mở của chính đối thủ. Mỗi dòng = một chỗ Executor của họ buộc phải bịa | P59 | **Rất cao** | Thấp — đối thủ đã tự nhận nghiệp vụ đó thuộc phạm vi bằng cách viết nó ra |
| 26 | **Đơn vị / bước nhảy / trần của ô nhập số** | Spec nói "1 điểm = 1 yên", "nhập số lượng" mà **không** nói bội số, bước nhảy, trần trên, có số 0 không | P60 | **Rất cao** | Thấp |
| 27 | **Thao tác hoàn tác không tồn tại trong spec** (gỡ / huỷ áp / đổi lựa chọn trước khi chốt) | Ctrl+F "gỡ / 解除 / huỷ áp / đổi / thay" = 0 hit, trong khi spec có thao tác "áp / chọn" | P61 | Cao | Thấp |

**Vì sao #25–#27 là ba loại đáng bắn nhất sau thi thử 11/09.** Cả ba đều **không cần ta biết đáp án chuẩn** — chúng chỉ cần **không gian đáp án rộng**. Bắn vào một ô nhị phân (có/không, giữ/gỡ) thì Executor trùng đáp án 50% do may. Bắn vào một ô có ≥4 giá trị hợp lý (bội số 100 hay 1? trần 50% hay toàn bộ? mã lỗi nào trong 6 mã? gỡ được mấy lần?) thì xác suất trùng tụt xuống ≤25% **mà không cần ta đoán đúng gì cả**. Đây là cách duy nhất kit có được đạn khi đối thủ đã chắn hết các dòng ⚠ của ta — tình huống 11/14 dòng bị chắn của thi thử 11/09 sẽ lặp lại, vì hai đội đọc cùng một brief và hỏi cùng một AI Khách hàng.

**Vì sao #22–#24 rẻ.** Ba loại này chỉ cần đọc mục lục và hai bảng của spec đối thủ — không cần biết specs thật, không cần suy luận nghiệp vụ. Mục 4 vắng message nguyên văn là hở gần như chắc chắn ở các đội còn viết theo template luật nghiệp vụ thuần.

## 2. Quy tắc viết tình huống test

1. Ép kết quả cụ thể: câu hỏi kết thúc bằng một trong bốn dạng — con số / trạng thái cuối / ai thắng / có hoàn tiền không [HD §5.2 ②].
2. Một tình huống = một câu hỏi. Hai câu hỏi → cách AI So khớp xử lý câu hỏi kép chưa được BTC nêu (hỏi câu 10, 00 §H) — mặc định coi là rủi ro TRƯỢT. Kiểm nhanh: đếm dấu ? và dấu , trước câu hỏi — > 1 câu hỏi = viết lại.
3. Đủ 4 dữ kiện: thời điểm tuyệt đối, loại khách, số lượng, trạng thái hiện tại của hold.
4. Không hỏi mở một mình ("xử lý thế nào?"), không hỏi "vì sao", không hỏi "có nên".
5. Không khẳng định số liệu spec đối thủ ("TTL là 120 phút"); đưa mốc tuyệt đối để Executor tự tra. Áp riêng cho **giá trị dẫn xuất** mà spec tự tính ra — `expires_at`, `deposit_due`, `ATP`, mức cọc, số tiền hoàn: chỉ nêu dữ kiện gốc (`created_at`, `on_hand`, giá niêm yết) và để Executor tính. Nêu sẵn giá trị dẫn xuất mà lệch luật của spec sẽ khiến Executor báo ĐA NGHĨA giả và làm hỏng cả tình huống.
6. Hiểu được chỉ với brief + lẽ thường: không dùng mã BR, tên mục, thuật ngữ riêng đội mình.
7. Nằm trong core flow hold (tạo, giữ tồn, cọc, gia hạn, hết hạn, hủy, chuyển đơn, quyền actor). Chấm rubric §6 trước khi nộp.
8. ≤ 60 từ (giới hạn tự đặt để Executor không bỏ dữ kiện; độ dài tối đa BTC cho phép vẫn chưa rõ — 00 §A2-5).
9. **Chỉ nộp khi đã có gói bằng chứng phạm vi**, theo ba mức (đủ một mức là nộp được):
   - **Mức 1 — câu trả lời AI Khách hàng nguyên văn** về đúng nghiệp vụ đó (khối `L<n>` đã chấp nhận, có timestamp). Mạnh nhất; với 15–20 lượt buổi sáng thì không còn hiếm như bản 5 câu, nhưng vẫn chỉ phủ đúng những ô đã hỏi. Khối `[BỊ TỪ CHỐI]` **không** phải bằng chứng.
   - **Mức 2 — câu brief tường minh** nhắc nghiệp vụ đó. Brief là tài liệu BTC phát cho mọi đội nên không bác được bằng lý "suy diễn của đội bạn".
   - **Mức 3 — đồng thuận chéo:** ≥2 trong 3 spec tải về có luật cho nghiệp vụ đó (trích số mục của cả hai). Yếu hơn hai mức trên nhưng dùng được khi kháng nghị.
   Không có mức nào ⇒ không nộp. Test không sửa được sau khi nộp, và kháng nghị chỉ mở cho ca VÔ HIỆU (00 §A) — nghĩa là gói bằng chứng phải xong lúc 14:30, không phải lúc 16:00.
10. **Bỏ một slot còn hơn nộp một test âm kỳ vọng.** Không nộp = 0 điểm; VÔ HIỆU = −1. Với 5 slot × 3 spec, nộp 13 test tốt hơn nộp 15 test trong đó 2 test đoán bừa.
11. Không dùng từ mơ hồ trong câu hỏi ("một lúc sau", "khá nhiều").
12. Tự đóng vai Executor: đoán mặc định khi spec im lặng vẫn trùng đáp án chuẩn → TRƯỢT cao, đổi test [HD §2.3].

13. **Cổng hình thức thứ tư — hỏi HÀNH VI HỆ THỐNG, không hỏi GIÁ TRỊ** (phát hiện 11/09, hệ thống trả câu về kèm hướng dẫn). Câu 「送料はいくらになりますか」 bị chặn; dạng được nhận là 「…のとき、システムは〜をどう計算し、画面にどう表示しますか」. Ba động từ an toàn: **表示する · 計算する · 適用する**, thêm **制御する / 許可する / 拒否する** cho ca quyền và nút. Lợi ích phụ: dạng hành vi ép Executor cam kết **cả con số lẫn chuỗi hiển thị** ⇒ hai điểm để lệch thay vì một.
14. **Cấm cược vào đáp án chuẩn.** Không nộp test mà cơ sở TRÚNG là một câu bắt đầu bằng "specs thật gần như chắc chắn…", "nếu không thì sẽ vô lý…", "theo lẽ thường phải là…". Suy luận hợp lý chính là thứ đề cố tình bẻ, và cũng là thứ Executor mù sẽ đoán ⇒ hai bên trùng nhau ⇒ TRƯỢT. Một test chỉ được nộp khi TRÚNG đến từ **một** trong hai cơ chế:
   - **Cơ chế A — ta biết đáp án:** có lời AI Khách hàng nguyên văn xác định kết quả, **và** spec đối thủ nói khác nó (không phải im lặng — im lặng thì xem cơ chế B).
   - **Cơ chế B — không ai biết đáp án nhưng không gian đáp án rộng:** ô thuộc bảng §8, spec đối thủ im lặng hoặc tự khai chưa chốt, và liệt kê được **≥4 giá trị hợp lý** mà Executor có thể xuất ra. Xác suất trùng ≤ 1/4 mà ta không phải đoán đúng gì.
   Không thuộc A cũng không thuộc B ⇒ **bỏ slot**.
15. **Im lặng của AI Khách hàng không phải bằng chứng phủ định.** Khách chỉ trả lời đúng một ẩn số mỗi lượt và không có memory; ô ta không hỏi thì khách không kể. Cấm suy "khách nói 「しきい値」 số ít nên chỉ có một mốc", "khách không nhắc hạng thành viên nên không có hạng". Hệ quả trực tiếp: **không bắn vào một con số đối thủ tự đặt** trừ khi có lời khách nguyên văn nêu **con số khác**.
16. **Ưu tiên ô có không gian đáp án rộng hơn ô nhị phân.** Xếp hạng ứng viên theo `W` = số giá trị hợp lý mà Executor có thể xuất: `W ≥ 4` (đơn vị/bội số, trần, chọn 1 trong ≥4 mã lỗi, số lần cho phép) > `W = 3` (ba nhánh trạng thái) > `W = 2` (có/không, giữ/gỡ). Với cùng độ im lặng, `W = 2` cho `P(TRÚNG) ≈ 0,5` là **dưới ngưỡng nộp** của §6 luật 9.

Mẫu câu chuẩn:
> "[Loại khách] tạo hold [qty] đơn vị sản phẩm [X] lúc [hh:mm:ss, ngày, TZ]; hold hiện ở trạng thái [S]. Lúc [hh:mm:ss] xảy ra [sự kiện]. [Trạng thái cuối của hold là gì / tồn khả dụng bằng bao nhiêu / tiền cọc hoàn bao nhiêu / ai được hàng]?"

## 3. Phân bổ 5 test / spec [HD §5.3]

| Test | Loại | Nguồn đạn |
|---|---|---|
| 1 | #15 / #1 / **#21** — khoảng trống lớn nhất | Danh sách ⚠ × sweep §4 × **bảng đồng thuận chéo §4b** |
| 2 | **#16 / #18 / #19 — khả thi, cưỡng chế, thất bại phụ thuộc ngoài** | Cổng F trên spec đối thủ (32 §1); P43–P51 |
| 3 | **#22 / #23 / #24 — message nguyên văn, guest vs login, trạng thái UI** (mới 11/09) | Mục 4 và mục 2/8 của spec đối thủ; P53–P55b |
| 4 | #4 / #11 / #2 / #12 — biên, đơn vị, múi giờ, ngoại lệ & rollback | Probe BVA, Time, N12, N5; P39 / P41 / P42 |
| 5 | #7 / #8 / #17 — đồng thời, ưu tiên xung đột, tự đánh bại mục tiêu | Probe N4, N14; P45 / P46 |

Slot #9/#10 (actor, quyền) xuống dự phòng: hầu hết đội có bảng actor, còn cổng F thì hầu như không đội nào chạy. Luật phân tán: 5 test phủ ≥ 4 nhóm N; tối đa 2 test cùng chủ đề. Slot đối thủ quá tốt → thay bằng #15, #19 hoặc #21 thứ hai ở chủ đề khác.

**Slot 3 đổi sau tài liệu BTC 11/09.** Ba loại #22–#24 có mức "Rất cao / Cao" mà rủi ro VÔ HIỆU "Thấp", và phát hiện được chỉ bằng mục lục + hai bảng — rẻ hơn mọi slot khác. Ngoại lệ & rollback (#2/#12) dồn vào slot 4 vì cùng đọc từ bảng Case của mục 6.

**Trọng số nguồn đạn theo số lượt hỏi thật.** Với 15–20 lượt buổi sáng, danh sách ⚠ thường 12–25 dòng — nhiều hơn bản 5 câu nhưng vẫn ít hơn kit 08/09 giả định, và 3 spec đối thủ thì tải về được. Thứ tự nguồn đạn: (1) ⚠ từ các lượt đã trả lời — ít nhưng chắc; (2) **bảng đồng thuận chéo** (#21) — nhiều và rẻ; (3) cổng F trên spec đối thủ (#16–#19) — không cần biết specs thật; (4) catalogue ⚠ của domain (10 §6) — đoán theo mặc định ngành, rủi ro TRƯỢT cao nhất vì Executor cũng đoán y hệt.

## 4. Quy trình soi 10 phút / spec [HD §5.4]

| Phút | Việc | Đầu ra |
|---|---|---|
| 0–1,5 | **Tick 10 mục BTC** (knowledge/33 §1) có/không; rồi tick 14 nhóm nghiệp vụ N1–N14 [HD §3.5] (20 §2 tên nhóm) | Mục vắng = hạng A (#1, và #22/#23/#24 nếu là mục 4 / 2 / 3); nhóm KHÔNG có = hạng A |
| 1,5–3 | **Cổng F rút gọn** — 4 lệnh grep của knowledge/32 §1 (F1 mốc hoàn tất · F2 hạn mức tự khai · F5 láng giềng không có nhánh lỗi · F8 khóa tài nguyên trước rào) | Mỗi hit = ứng viên #16/#18/#19/#17, **hạng A** |
| 3–4 | **Mục 4** (validation & message): có message **nguyên văn trong ngoặc kép** không? có cột FE/BE không? **Mục 2 + 8**: có cột/dòng **Guest**, có điều kiện ẩn/disable không? | Không nguyên văn = #22 hạng A ("hiện message gì"); vắng Guest = #23/#24 hạng A |
| 4–5 | Ctrl+F danh sách đen [HD §4.6] nhóm 1, 3, 4, 6, 8, 11 + nhóm nội dung 17–22 (40 §2) | Mỗi hit = 1 điểm #3, ghi số mục |
| 5–6,5 | **Mục 6**: với từng logic, đếm case **bình thường / biên / lỗi**; kiểm 5 gạch (số · toán tử `>`/`≥` · múi giờ · default khi config trống · ưu tiên khi nhiều case cùng đúng). Bảng trạng thái: không có → #6/#8; có → ô trống, sự kiện thiếu (admin hủy, hết hàng, thanh toán lỗi) | Case thiếu = #2/#4/#6; danh sách ô trống |
| 6,5–7,5 | **Mục 7**: có 4 ca bất thường BTC nêu đích danh không (dữ liệu đổi giữa hiển thị và submit · gửi trùng · mở link hai lần · mail fail sau khi đã lưu)? Ctrl+F 5 mục hiếm: "đồng thời", "múi giờ", "guest", "rollback", "ưu tiên" | Vắng ca nào = tình huống bắn sẵn; 0 hit = hạng A |
| 7,5–8 | **Đối chiếu 12 kịch bản suy biến** (05 §M6) với spec đối thủ; đọc danh sách NGOÀI phạm vi của họ so với brief (#20) | Mỗi ca không có luật = ứng viên #19 |
| 8–10 | Chốt 5 test theo §3; viết theo mẫu §2; chấm rubric §6; **gói bằng chứng phạm vi (§2-9)**; mở hồ sơ §7 | 5 test điểm ≤1, mỗi test có bằng chứng |

Spec đối thủ là markdown tải về được (00 §A), nên các bước grep chạy bằng lệnh chứ không bằng mắt:

```
grep -n -i -E "hoàn tất|tiền về|đối soát" B.md            # F1 → #16
grep -n -i -E "email|số điện thoại|tự khai|khai báo" B.md  # F2 → #18
grep -n -i -E "cổng|ERP|WMS|job|callback|thông báo" B.md   # đếm nhánh lỗi → #19
grep -n -i -E "đồng thời|cùng lúc|múi giờ|UTC|ngày lễ|guest|chưa đăng nhập|rollback|kiểm kê|ưu tiên|oversell|safety" B.md   # 0 hit = hạng A
grep -n -i -E "\"[^\"]{8,}\"" B.md                        # có message nguyên văn không → #22
grep -c "^|" B.md ; grep -n '```mermaid' B.md              # có bảng không; chỉ có sơ đồ = #6 (31 §5)
```

Knowledge boundary sweep [HD §2.1 ⑤]: Ctrl+F từng dòng danh sách ⚠ + bảng tham số đội mình (TTL, qty, hold max, %cọc, gia hạn, giá khóa, guest, hoàn cọc) trong spec đối thủ; không thấy hoặc khác specs thật → test hạng A, đáp án chuẩn có sẵn. 2 người: một sweep, một soi cấu trúc, song song từ phút 0.

## 4b. Bảng đồng thuận chéo 3 spec (bước mới, 13:00–13:15)

Làm **một lần cho cả ba spec**, trước khi soi từng spec. Chỉ làm được vì spec đối thủ tải về được ở dạng markdown.

| Nghiệp vụ | Spec A | Spec B | Spec C | Spec mình | Kết luận |
|---|---|---|---|---|---|
| Hoàn cọc khi hết hạn | §6 BR-09: hoàn 100% | §5: mất cọc | im lặng | hoàn 100% | A và B **chỏi nhau** ⇒ ít nhất một đội sai ⇒ bắn cả hai bằng cùng một tình huống; C im lặng ⇒ #21 |
| Guest tạo lượt giữ | cấm | cấm | im lặng | cấm | C im lặng, hai đội cấm ⇒ #21, phạm vi có bằng chứng mức 3 |
| Callback trùng | im lặng | im lặng | im lặng | có 0.11 | Điểm mù chung ⇒ **không** phải bằng chứng ngoài phạm vi; bắn cả 3 **chỉ khi** có brief/log đỡ |

Cách dùng:

1. Lấy danh sách nghiệp vụ từ 14 nhóm N1–N14 (20 §2) + 12 ca suy biến (05 §M6) — khoảng 25 dòng là đủ.
2. Điền bằng grep, không đọc tuần tự: mỗi dòng một lệnh `grep -n -i -E "<3 từ khóa>" A.md B.md C.md`.
3. Ba kết luận đáng bắn, theo độ mạnh giảm dần:
   - **Hai spec chỏi nhau về cùng một nghiệp vụ** — ít nhất một đội trái specs thật. Một tình huống bắn được cả hai, và với đội sai thì gần chắc TRÚNG.
   - **Một spec im lặng, ≥2 spec có luật** (#21) — bằng chứng phạm vi mức 3 có sẵn.
   - **Cả ba im lặng** — điểm mù chung, giá trị cao nếu đúng nhưng phải có bằng chứng mức 1 hoặc 2 mới nộp.
4. Cột "Spec mình" là phần lợi kép: dòng nào ta im lặng mà ≥2 đội có luật là chỗ ta gần chắc bị bắn — ghi lại để rút bài học, không sửa được nữa (spec đã khóa 12:00).

## 5. Bảng PROBE

Nguồn: EP [ISTQB 4.2.1]; BVA 3-value [4.2.2]; decision table [4.2.3]; state table [4.2.4]; error guessing [4.4.1]; SFDIPOT [HTSM]; cheat sheet [Hendrickson]. N theo [HD §3.5].

| # | Probe | Nguồn | N | Ví dụ tình huống viết sẵn |
|---|---|---|---|---|
| P1 | TTL đúng mốc | BVA 3-value | N2 | "Hold tạo 10:00:00. Khách thanh toán đúng 10:00:00 + <TTL>. Hold còn hiệu lực không?" |
| P2 | TTL mốc +1 giây | BVA 3-value | N2 | "Hold tạo 10:00:00, thanh toán tại mốc <TTL> + 1 giây. Đơn có được tạo không?" |
| P3 | Mốc −1 giây, xử lý trễ | BVA; Time/Pacing [HTSM] | N2 | "Khách bấm thanh toán 1 giây trước hết hạn; cổng xử lý mất 40 giây. Đơn có được tạo không?" |
| P4 | Mốc bắt đầu TTL | Error guessing | N2 | "Bấm 'Giữ' 10:00:00, cọc thành công 10:07:00. Hold hết hạn lúc mấy giờ?" |
| P5 | Gia hạn lần <GH_MAX>+1 | BVA | N2 | "Hold đã gia hạn <GH_MAX> lần. Khách yêu cầu gia hạn tiếp 5 phút trước hết hạn. Chấp nhận hay từ chối?" |
| P6 | Gia hạn cộng dồn hay reset | Error guessing | N2 | "Hold tạo 10:00, gia hạn lúc 11:50. Hạn mới tính từ 11:50 hay từ mốc hết hạn cũ? Nêu giờ." |
| P7 | qty = 0 | Data: zero [Hendrickson] | N3 | "Khách nhập số lượng 0, bấm 'Giữ'. Tạo hold, từ chối, hay tự sửa thành 1?" |
| P8 | qty âm | Data: negative | N3 | "Khách gửi qty = −1 qua API. Tồn khả dụng thay đổi bao nhiêu?" |
| P9 | qty = <QTY_MAX>+1 | BVA 3-value | N3 | "Tồn 100. Khách thường giữ <QTY_MAX>+1 đơn vị. Hold tạo với số lượng nào?" |
| P10 | qty > tồn | Data: huge | N3 | "Tồn 3. Khách giữ 5. Kết quả: hold 3, hold 0, hay từ chối?" |
| P11 | Hold thứ <HOLD_MAX>+1 | BVA; Count [Hendrickson] | N3 | "Khách có <HOLD_MAX> hold ACTIVE, tạo thêm 1 hold sản phẩm khác. Chấp nhận hay từ chối?" |
| P12 | Cùng SKU 2 hold | Data: duplicates | N3 | "A đã hold 2 đơn vị SKU X, tạo thêm hold 1 đơn vị SKU X. Hai hold riêng, gộp thành 3, hay từ chối?" |
| P13 | Race đơn vị cuối | Multi-User [Hendrickson]; Concurrency [HTSM] | N4 | "Tồn 1. A và B gửi giữ cùng mili-giây, server nhận A trước 5 ms. Ai được hold?" |
| P13b | Race: phản hồi cho người thua | Multi-User; Concurrency | N4 | "Tồn 1. A và B gửi giữ cùng mili-giây, server nhận A trước 5 ms. B nhận trạng thái nào: hết hàng / waitlist / PENDING?" |
| P14 | Double-click | Flood [Hendrickson] | N4 | "Khách bấm 'Giữ' 2 lần trong 300 ms cùng sản phẩm qty 1. Số hold tạo ra và tồn bị trừ?" |
| P15 | Hold vs mua thẳng | Multi-User | N4 | "Tồn 1, A đang hold. B mua thẳng không hold. B thanh toán thành công không?" |
| P16 | Nguồn sự thật tồn | Interfaces [HTSM] | N4 | "Hệ thống báo tồn 5, ERP báo 0. Khách giữ 1. Chấp nhận hay từ chối?" |
| P17 | Cọc thất bại sau giữ | Error handling [HTSM] | N5 | "Hold tạo, tồn đã trừ; cọc bị ngân hàng từ chối. Hold ở trạng thái nào?" |
| P18 | Cổng thanh toán timeout | Interfaces: timeout | N5 | "Tồn 10. Khách giữ 2; cổng thanh toán không phản hồi 60 giây khi cọc. Tồn khả dụng bằng bao nhiêu?" |
| P19 | Giá đổi trong TTL | Error guessing (prior ⚠) | N6 | "Hold khi giá 100.000đ; lúc thanh toán 120.000đ. Khách trả bao nhiêu?" |
| P20 | Hủy sau cọc | Silent gap | N7 | "Khách tự hủy hold đã cọc 30 phút sau khi tạo. Hoàn bao nhiêu % cọc?" |
| P21 | Hết hạn: cọc | Silent gap (⚠) | N7 | "Hold hết hạn, khách không thanh toán. Cọc: hoàn 100%, một phần, hay mất?" |
| P22 | Hết hạn: nhả tồn khi nào | Operations: cron [HTSM] | N7 | "Hold hết hạn 12:00:00. Lúc 12:00:01 khách khác tra tồn. Tồn khả dụng đã gồm hàng vừa nhả chưa?" |
| P23 | Thanh toán hold đã hủy | State: invalid transition | N8 | "Hold HỦY 5 phút trước; khách bấm thanh toán qua link cũ. Đơn tạo hay từ chối?" |
| P24 | Guest | EP loại khách | N9 | "Người chưa đăng nhập bấm 'Giữ' qty 1. Tạo hay từ chối; nếu tạo, TTL?" |
| P25 | VIP | EP loại khách | N9 | "Khách VIP tạo hold 10:00. Hold hết hạn lúc mấy giờ?" |
| P25b | Khách thường | EP loại khách | N9 | "Khách thường tạo hold 10:00. Hold hết hạn lúc mấy giờ?" |
| P26 | CSKH gia hạn thay khách | Authorization [HTSM] | N9 | "CSKH gia hạn 60 phút cho hold khách A khi khách không yêu cầu. Chấp nhận hay từ chối?" |
| P27 | Admin hủy hold đã cọc | Authorization | N9 | "Admin hủy hold ĐÃ CỌC của khách. Hoàn cọc bao nhiêu %?" |
| P28 | Múi giờ | Time zones [Hendrickson; HTSM] | N2 | "Khách Tokyo tạo hold 23:30 JST thứ 7. Hết hạn lúc mấy giờ theo giờ Việt Nam?" |
| P29 | Qua 00:00 / cuối tháng | Date boundary | N2 | "Hold tạo 23:50 ngày 30/09. Hết hạn lúc mấy giờ, ngày nào?" |
| P30 | Ngày lễ / ngoài giờ | Business holidays [HTSM Time] | N2 | "Hold tạo 17:30 ngày 01/09; cửa hàng nghỉ 02/09. Hold hết hạn lúc nào?" |
| P31 | DST | DST changeover [Hendrickson] | N2 | "Khách ở châu Âu tạo hold 01:30 đêm đổi giờ mùa hè. TTL tính theo đồng hồ nào, hết hạn lúc nào?" |
| P32 | Đồng hồ client lệch | Time difference between machines | N2 | "Điện thoại khách chậm 10 phút so với server. Hold hết hạn theo mốc nào; giờ cụ thể?" |
| P33 | Lỗi giữa trừ tồn và ghi hold | Data integrity [HTSM]; #12 | N12 | "Đã trừ tồn 2, ghi hold lỗi DB. Tồn khả dụng cuối cùng so với trước?" |
| P34 | Retry / idempotency | Data: duplicates | N12 | "Client không nhận phản hồi, gửi lại đúng yêu cầu giữ sau 2 giây. Có 1 hay 2 hold?" |
| P35 | Hai luật cùng lúc | Decision table gap | N14 | "Đúng 12:00 hold hết hạn và khách gửi gia hạn hợp lệ cùng lúc. Kết quả cuối?" |
| P36 | Hết hàng vs hold đã cọc | Priority conflict | N14 | "Kho báo hư hỏng, tồn thật = 0 khi A có hold ĐÃ CỌC 1 đơn vị. Hold A còn ACTIVE hay bị hủy?" |
| P37 | Chốt một phần | Selection Some/None/All [Hendrickson] | N8 | "Hold 5 đơn vị SKU X tạo 10:00, chốt đơn 3 đơn vị lúc 10:20. Hai đơn vị còn lại được giải phóng lúc mấy giờ?" |
| P38 | Hold nhiều SKU, 1 dòng hết hàng | Dependencies; Count [Hendrickson] | N4 | "Hold gồm SKU X (qty 1) và SKU Y (qty 1); kho báo Y hỏng, tồn Y = 0. Hold ở trạng thái nào?" |
| P39 | Tài khoản bị khóa khi hold ACTIVE | Interruptions [Hendrickson] | N9 | "Tài khoản khách bị khóa lúc 10:30 khi hold ACTIVE tới 12:00. Lúc 11:00 hold ở trạng thái nào?" |
| P40 | Hủy rồi tạo lại ngay | Sequences [Hendrickson] | N3 | "Khách hủy hold 10:05, tạo lại hold cùng SKU lúc 10:06. Hold mới được tạo hay bị từ chối?" |
| P41 | Hủy khi cổng đang xử lý cọc | Interruptions: Cancel [Hendrickson] | N5 | "Khách bấm Hủy lúc cổng thanh toán đang xử lý cọc (chưa callback). Cọc về đâu: không thu / hoàn 100% / giữ?" |
| P42 | SKU ngừng bán khi hold ACTIVE | State: invalid transition | N12 | "Admin ngừng bán SKU X lúc 10:30 khi hold ACTIVE tới 12:00. Hold ở trạng thái nào?" |
| **P53** | **Message lỗi nguyên văn** | #22; mục 4 BTC | N17 | "Khách nhập số lượng 5 khi tồn khả dụng còn 2, bấm Giữ hàng. Hệ thống hiển thị message gì, nguyên văn?" |
| **P53b** | **Nhiều lỗi cùng lúc** | #22; mục 4 | N17 | "Khách để trống số điện thoại VÀ nhập số lượng vượt tồn, bấm Giữ hàng. Hiện một hay nhiều message, message nào trước?" |
| **P54** | **Guest vs login** | #23; mục 2, 8 BTC | N15 | "Khách CHƯA đăng nhập bấm Giữ hàng cho SKU còn 3 đơn vị. Hold được tạo hay bị từ chối; màn hình hiện gì?" |
| **P54b** | **Guest xem hold** | #23; mục 8 | N15 | "Guest đã tạo hold lúc 10:00, đóng trình duyệt, mở lại lúc 10:30. Guest có xem được hold của mình không?" |
| **P55** | **Trạng thái nút** | #24; mục 2, 3 BTC | N15 | "Tồn khả dụng của SKU X = 0 khi khách mở màn hình. Nút Giữ hàng ở trạng thái nào: bật, disable hay ẩn?" |
| **P55b** | **Sau thao tác hiển thị gì** | #24; mục 3 | N16 | "Khách tạo hold thành công lúc 10:00. Ngay sau đó màn hình hiển thị gì và nút chuyển thành gì?" |
| **P56** | **Dữ liệu đổi giữa hiển thị và submit** | mục 7.2 BTC | N19 | "Khách mở màn hình lúc 10:00 thấy tồn 3; lúc 10:02 tồn thật còn 1; khách bấm Giữ hàng 2 đơn vị lúc 10:03. Kết quả và message?" |
| **P57** | **Mở link hai lần** | mục 7.2 BTC | N19 | "Khách mở link xác nhận cọc hai lần trong 5 giây. Có mấy hold, thu cọc mấy lần?" |
| **P58** | **Mail fail sau khi đã lưu** | mục 7.2 BTC | N18 | "Hold đã lưu thành công nhưng gửi email xác nhận thất bại. Hold ở trạng thái nào, khách thấy gì?" |

**P53–P58** (in đậm ở cuối bảng trên) là probe của cấu trúc 10 mục — loại #22–#24; nguồn: tài liệu BTC "Spec Battle Anatomy" 11/09 (knowledge/33). Ba loại này đọc được bằng mắt từ mục lục và hai bảng của spec đối thủ, không cần biết specs thật.

### Probe khả thi & mục tiêu (P43–P52) — dùng cho loại #16–#20

Nguồn: ISO/IEC/IEEE 29148 `feasible`/`affordable`; HTSM *Operations: disfavored use* + *Quality Criteria conflict*; WWWWWHKE (so *Why* với *How*); bảng thực tế phụ thuộc ngoài ở knowledge/32 §2.

| # | Probe | Loại | N | Ví dụ tình huống viết sẵn |
|---|---|---|---|---|
| P43 | Hoàn tiền về phương thức không dùng được | #16 | N5 | "Khách trả cọc bằng thẻ, sau đó thẻ bị ngân hàng đóng. Hold hết hạn. Khách nhận lại tiền bằng cách nào?" |
| P44 | Hai mốc hoàn tiền | #16 | N5 | "Hold hết hạn lúc 12/09 10:00. Đến 13/09 10:00 tiền cọc vẫn chưa vào tài khoản khách. Hệ thống ghi hold ở trạng thái gì và khoản cọc ở trạng thái gì?" |
| P45 | Khóa tài nguyên trước khi thu tiền | #17 | N4 | "Khách bấm 'Đặt cọc giữ hàng' cho đơn vị cuối cùng rồi không thanh toán. Trong lúc chờ, khách khác mua sản phẩm đó được không?" |
| P46 | Giữ chỗ miễn phí lặp lại | #17 | N3 | "Một khách tạo hold rồi tự hủy 20 lần trong một giờ cho các SKU khác nhau. Lần thứ 21 hệ thống tạo hold hay từ chối?" |
| P47 | Vượt hạn mức bằng danh tính mới | #18 | N9 | "Khách chưa đăng nhập tạo hold bằng email A, rồi tạo tiếp bằng email B cho cùng sản phẩm. Hold thứ hai được tạo hay bị từ chối?" |
| P48 | Callback trùng | #19 | N5 | "Cổng thanh toán báo cọc thành công hai lần cho cùng một giao dịch. Số tiền đã thu của khách là bao nhiêu?" |
| P49 | Callback sai thứ tự | #19 | N5 | "Cổng gửi 'thất bại' lúc 10:00:05 rồi gửi 'thành công' của cùng giao dịch lúc 10:00:02 nhưng đến sau. Hold ở trạng thái nào?" |
| P50 | Job hết hạn ngừng chạy | #19 | N12 | "Job dọn hết hạn ngừng chạy từ 10:00 tới 14:00. Lúc 14:00 tồn khả bán của SKU có hold hết hạn 11:00 bằng bao nhiêu?" |
| P51 | Ghi giữ thất bại sau khi kiểm thấy còn hàng | #19 | N4 | "Hệ thống kiểm thấy còn 1 đơn vị nhưng khi ghi giữ thì số tồn đã bị giao dịch khác lấy. Khách nhận kết quả gì?" |
| P52 | Nghiệp vụ brief nhắc bị đẩy ra ngoài phạm vi | #20 | N1 | "Khách đã chốt đơn từ hold và còn phải trả phần tiền còn lại. Khách trả nốt trong thời hạn nào?" *(chỉ bắn khi brief nhắc tường minh phần tiền còn lại)* |

### Probe cơ chế B (P59–P61) — dùng cho loại #25–#27, không cần biết đáp án chuẩn

Nguồn: bài học thi thử 11/09 (§0) + bảng "ô đề luôn chốt" §8. Ba probe này khác mọi probe trên ở chỗ **ta không đoán đáp án** — ta chỉ chọn ô mà Executor buộc phải xuất một giá trị trong không gian ≥4 khả năng.

| # | Probe | Loại | Ô §8 | Khuôn câu (thay danh từ của miền) |
|---|---|---|---|---|
| P59 | Bắn thẳng vào dòng đối thủ tự khai chưa chốt | #25 | theo dòng | Lấy nguyên dòng ở mục "Điểm chưa chốt / TBD / 確認中", dựng một tình huống khách thật rơi đúng vào đó, kết bằng 「…のとき、システムは〜をどう判定し、画面にどう表示しますか」 |
| P60 | Đơn vị / bội số / trần của ô nhập số | #26 | Đ1, Đ2 | "Khách có `<tài nguyên>` là `<N lớn>`, muốn dùng `<M không tròn, dưới trần>`. Hệ thống chấp nhận con số ấy thế nào và bảng tiền tính ra sao?" |
| P61 | Thao tác hoàn tác / làm lại trước khi chốt | #27, #25 | Đ8, Đ9 | "Khách đã áp `<X>`, rồi muốn đổi sang `<X'>` (hoặc nhập lại đúng `<X>`) trước khi chốt. Hệ thống xử lý yêu cầu ấy thế nào và hiện gì?" |

**Vì sao ba probe này sống sót được khi mọi dòng ⚠ đã bị chắn.** Chúng không tranh với đối thủ về *kiến thức đề* (ô đó đối thủ cũng không biết — họ tự khai), mà tranh về *độ rộng không gian đáp án*. Thi thử 11/09: 11/14 dòng ⚠ của ta bị spec A chắn, nhưng cả ba probe này đều có đạn (I-05 → Đ9 · I-02 → Đ1 · I-01 → Đ7).

**Cảnh báo phạm vi cho P43–P52:** chấm rubric §6 như mọi test. P43, P44, P48–P51 điểm 0–1 vì nằm trong luồng tiền/tồn của chính tính năng. P52 chỉ nộp khi **trích được câu brief** nhắc nghiệp vụ đó — brief là căn cứ dùng chung, mạnh hơn suy diễn, nhưng vẫn yếu hơn một câu trả lời của AI Khách hàng.

## 6. Rubric kiểm phạm vi (chống VÔ HIỆU)

| Điểm | Tiêu chí | Ví dụ |
|---|---|---|
| 0 | Core flow hold (như §2 rule 7) VÀ trong danh sách TRONG phạm vi hoặc AI Khách hàng đã trả lời trực tiếp | TTL, race, guest, hoàn cọc |
| 1 | Core flow nhưng AI Khách hàng chưa trả lời trực tiếp; chắc chắn thuộc tính năng theo brief | Retry idempotency, hủy 2 lần |
| 2 | Miền kề có chạm hold: khuyến mãi, giá, kiểm kê, catalog, ERP, kênh thông báo, DST | Voucher hết hạn trong TTL, ERP lệch tồn |
| 3 | Miền xa hoặc trong danh sách NGOÀI phạm vi: thuế, kế toán, hóa đơn, bảo hiểm, vận chuyển, đổi trả sau giao, chống gian lận, bảo vệ dữ liệu, KPI | "Xuất hóa đơn cọc thế nào?" |

Luật:
1. Chỉ nộp test điểm ≤ 1.
2. Điểm 2 hạ xuống 1 khi có bằng chứng phạm vi **mức 1 hoặc mức 2** (§2-9): lời AI Khách hàng nguyên văn, hoặc câu brief tường minh. Bằng chứng **mức 3** (đồng thuận chéo) hạ điểm 2 xuống 1,5 — nộp được nếu ước `P(TRÚNG)` cao, xem luật 8.
3. Điểm 3 không nộp với bất kỳ lý do.
4. Câu hỏi chạm 2 miền → lấy điểm cao nhất.
5. Buổi sáng chưa hỏi "liệt kê nghiệp vụ NGOÀI phạm vi" [HD §5.6] → mọi test +1 điểm.
6. Người chấm phạm vi ≠ người viết test; ghi điểm vào hồ sơ §7.
8. **Chấm kỳ vọng trước khi chốt slot** (00 §D1). Ước hai xác suất rồi tính `EV = 2·P(TRÚNG) − 1·P(VÔ HIỆU)`:

   | Điểm phạm vi | P(VÔ HIỆU) ước | Nộp khi |
   |---|---|---|
   | 0 | ~0,05 | P(TRÚNG) ≥ 0,05 — tức gần như luôn nộp |
   | 1 | ~0,15 | P(TRÚNG) ≥ 0,10 |
   | 1,5 (điểm 2 + bằng chứng mức 3) | ~0,30 | P(TRÚNG) ≥ 0,20 |
   | 2 không có bằng chứng | ~0,50 | không nộp |

   Ước `P(TRÚNG)` từ dry-run executor mù: executor trả lời khác đáp án chuẩn và ĐỘ PHỦ = ĐỦ ⇒ 0,8; khác và ĐỘ PHỦ = KHÔNG ⇒ 0,6; trùng đáp án chuẩn ⇒ 0,1 (đổi test).
9. Câu trả lời `"Không có quy định riêng."` **KHÔNG** phải bằng chứng trong phạm vi — nó chỉ nói specs thật không quy định, không nói nghiệp vụ thuộc tính năng. Chỉ hai thứ hạ được điểm phạm vi: (a) mục nằm trong danh sách TRONG phạm vi (N1-03), hoặc (b) một câu trả lời nghiệp vụ cụ thể (con số / trạng thái cuối / hướng tiền / ai thắng) về đúng nghiệp vụ đó. Dòng "chưa rõ" trong RTM giữ nguyên điểm phạm vi gốc — đừng coi im lặng của specs thật là giấy thông hành.

### Luật 9 — điểm kỳ vọng TƯƠNG ĐỐI (sửa 11/09, thay luật 8)

Luật 8 cũ tính `EV = 2·P(TRÚNG) − P(VÔ HIỆU)` và coi TRƯỢT = 0. Sai: **TRƯỢT cho đội thủ +1 điểm thủ**, nên so với việc bỏ slot, một viên trượt làm ta thua thêm 1 điểm chênh lệch.

```
EV_rel = 2·P(TRÚNG) − 1·P(TRƯỢT) − 1·P(VÔ HIỆU)
       = 3·P(TRÚNG) − 1 − 2·P(VÔ HIỆU)          (vì P(TRƯỢT)=1−P(TRÚNG)−P(VÔ HIỆU))
```

⇒ **Ngưỡng nộp: `P(TRÚNG) > (1 + 2·P(VÔ HIỆU)) / 3`.** Với `P(VÔ HIỆU)=0,05` thì cần `P(TRÚNG) > 0,37`. Hai viên của thi thử 11/09 được gán 0,55–0,60 **không có cơ sở** (đều là cược §2-14); gán lại theo bảng dưới thì cả hai đều rơi xuống 0,25 và bị loại đúng.

**Bảng gán `P(TRÚNG)` — chỉ theo bằng chứng, cấm gán theo cảm giác:**

| Tình huống | P(TRÚNG) |
|---|---|
|Cơ chế A: có lời khách nguyên văn **và** spec đối thủ nói **khác** nó|**0,85**|
|Cơ chế A yếu: có lời khách nguyên văn, spec đối thủ im lặng, dry-run cho ra kết quả **khác** lời khách|**0,70**|
|Cơ chế B: ô bảng §8, đối thủ **tự khai chưa chốt** (#25), `W ≥ 4`|**0,70**|
|Cơ chế B: ô bảng §8, đối thủ im lặng, `W ≥ 4`|**0,55**|
|Cơ chế B: `W = 3`|**0,40**|
|`W = 2` (nhị phân) bất kể im lặng cỡ nào|**0,25** ⇒ loại|
|Cược §2-14 ("specs thật chắc phải thế")|**0,25** ⇒ loại|
|Dry-run cho ra kết quả **trùng** lời khách|**0,10** ⇒ loại|

`P(VÔ HIỆU)` giữ nguyên theo điểm phạm vi: 0 ⇒ 0,05 · 1 ⇒ 0,15 · 1,5 ⇒ 0,30.


## 7. Hồ sơ finding [HD §5.7] và kháng nghị [HD §5.8]

Một hồ sơ / test, lập trước khi nộp.

| Trường | Nội dung |
|---|---|
| ID | `T-B3` (test 3, spec đội B) |
| Loại lỗ hổng | # theo §1, probe theo §5 |
| Tình huống nộp | Nguyên văn |
| Chỗ spec im lặng / mâu thuẫn | Trích dẫn có số mục; hoặc "không mục nào đề cập" + 3 từ khóa Ctrl+F |
| Đáp án chuẩn kỳ vọng | Kết quả mong đợi + **nguồn**: `log C<n> hh:mm (nguyên văn)` / `brief câu …` / `mặc định ngành (10 §6)`. Ghi cả hệ quả phụ (tồn về đâu, ai được thông báo) |
| **Gói bằng chứng phạm vi** | Mức 1 / 2 / 3 theo §2-9, kèm trích nguyên văn đã copy sẵn — đây là toàn bộ hồ sơ kháng nghị nếu bị VÔ HIỆU |
| Dự đoán Executor | Câu Executor sẽ trả lời nếu spec im lặng (prior) |
| Điểm phạm vi & kỳ vọng | 0–3 theo §6; `P(TRÚNG)`, `P(VÔ HIỆU)`, `EV` theo §6 luật 8; người chấm |
| Kết quả máy | TRÚNG / TRƯỢT / VÔ HIỆU + lý do đối chiếu của AI So khớp |
| Đánh giá | Đồng ý / **Ứng viên kháng nghị** (chỉ khi kết quả máy = VÔ HIỆU; ưu tiên theo mức bằng chứng 1 > 2 > 3) |

### Kháng nghị — chỉ một loại ca

**Tham số 09/09 (00 §A):** kháng nghị **chỉ mở cho ca test CÔNG của mình bị chấm VÔ HIỆU**, nộp bằng **text gửi AI**, ban tổ chức review; ≤3 ca/đội.

Hai loại ca của kit cũ **không còn kháng nghị được**: (a) spec mình bị chấm TRÚNG dù có quy định; (b) AI So khớp hiểu sai ngữ nghĩa làm test mình thành TRƯỢT. Hệ quả trực tiếp: **rủi ro của vai THỦ không có đường lùi**, nên mọi cẩn trọng dồn vào chất lượng spec trước 12:00; còn rủi ro của vai CÔNG cứu được một phần, nên ngưỡng nộp test tính theo kỳ vọng (§6 luật 8).

Điều duy nhất phải chứng minh khi kháng nghị: **tình huống nằm TRONG phạm vi specs thật**. Không tranh đáp án chuẩn, không tranh cách Executor trả lời — chấm lại phạm vi là toàn bộ điều đang xin.

| Ưu tiên | Bằng chứng | Lập luận mẫu (2 câu) |
|---|---|---|
| 1 | **Mức 1** — AI Khách hàng đã trả lời nghiệp vụ đó | "Lúc [hh:mm], khi được hỏi [trích câu hỏi], AI Khách hàng trả lời [trích nguyên văn]. Câu trả lời đó là quy định nghiệp vụ cho đúng tình huống của test này, nên tình huống nằm trong phạm vi specs thật; đề nghị chấm lại phạm vi." |
| 2 | **Mức 2** — brief nhắc tường minh | "Brief do BTC phát ghi nguyên văn [trích câu brief]. Nghiệp vụ này không thể nằm ngoài phạm vi tính năng khi chính đề bài mô tả nó; đề nghị chấm lại phạm vi." |
| 3 | **Mức 3** — đồng thuận chéo | "Spec của đội [X] mục [§] và đội [Y] mục [§] đều có luật cho nghiệp vụ này [trích cả hai]. Hai đội độc lập cùng xếp nghiệp vụ này trong phạm vi tính năng; đề nghị chấm lại phạm vi." |

Giới hạn phải tự biết và nói trước khi trọng tài chỉ ra: mức 2 và mức 3 chỉ chứng minh nghiệp vụ **thuộc tính năng**, không chứng minh specs thật **có luật** cho nó. Vì vậy lập luận chỉ xin chấm lại phạm vi, không xin đổi thành TRÚNG.

**Mẫu text kháng nghị (≤150 từ/ca, gửi AI):**

```
Test: <ID> — <tình huống nguyên văn>
Kết quả máy: VÔ HIỆU
Đề nghị: chấm lại phạm vi.
Căn cứ: <trích nguyên văn 1 nguồn mạnh nhất + vị trí: mã câu + timestamp, hoặc câu brief, hoặc §mục của 2 spec>
Lập luận: <2 câu, chỉ về phạm vi>
Giới hạn: <1 câu tự nêu điểm yếu của bằng chứng, nếu là mức 2 hoặc 3>
```

Câu "Giới hạn" không phải khiêm tốn: người review thấy đội tự nêu đúng giới hạn của bằng chứng sẽ tin phần còn lại hơn, và nó chặn trước lý bác duy nhất mà họ có.

Bằng chứng chuẩn bị trước 15:00 (không kịp thu lúc 16:00): log AI Khách hàng có timestamp + ảnh chụp từng câu trả lời, bản copy spec đã nộp, 15 hồ sơ finding **kèm gói bằng chứng phạm vi 3 mức**, bảng đồng thuận chéo §4b, 3 spec đối thủ đã tải về.

## 8. Bảng "ô đề luôn chốt" — nguồn đạn cơ chế B (mới 11/09)

Mọi đề nghiệp vụ do BTC dựng đều **chốt sẵn** những ô dưới đây trong specs thật, vì đó là chỗ hai người đọc cùng một brief sẽ hiểu khác nhau. Đối thủ im lặng hoặc tự khai chưa chốt ở một ô ⇒ ứng viên cơ chế B, **không cần ta biết đáp án**. Cột `W` là số giá trị hợp lý Executor có thể xuất — chính là tử số của `P(TRÚNG)`.

| Ô | Câu hỏi soi trên spec đối thủ | W | Ví dụ đề thi thử 11/09 (ground truth) |
|---|---|---|---|
|**Đ1 Đơn vị & bước nhảy của ô nhập số**|Nhập được số lẻ hay phải bội số? Bước nhảy bao nhiêu?|4–6|V-06: điểm chỉ dùng **bội số 100**, spec A chỉ ghi 1点=1円|
|**Đ2 Trần trên & sàn dưới của ô nhập**|Tối đa bao nhiêu? Có chọn 0 được không? Trần neo vào đâu|4–6|V-02: số lượng tối đa **10**, ô chỉ có 1..10, không có 0|
|**Đ3 Thời điểm xét điều kiện**|Xét lúc thao tác hay lúc chốt? Có tái kiểm không|3–4|V-10: hạn phiếu xét lúc **ÁP**, không phải lúc chốt|
|**Đ4 Ô nào KHÔNG tính vào phép so nào**|Phép so X dùng tổng nào — toàn giỏ hay tập con? Có trừ phí/điểm không|4–6|G-07/R-209: mức mua tối thiểu so với **対象商品小計**, không tính phí ship và điểm|
|**Đ5 Tập con bị loại trừ**|Hạng mục nào không được tính vào tập đối tượng|3–5|G-06: セール価格・定期便・ギフトカード **không** là 対象商品|
|**Đ6 Phụ phí luôn thu**|Khoản nào vẫn thu kể cả khi đã miễn|3|V-04/R-105: 遠隔地追加送料 440円 **vẫn thu** khi ship miễn phí|
|**Đ7 Danh sách mã lỗi đầy đủ + thứ tự xét**|Có mấy mã? Thứ tự nào? Nhiều điều kiện cùng hỏng thì mã nào|5–8|DT-03: 6 điều kiện E-101→E-106 theo thứ tự; spec A chỉ có 3|
|**Đ8 Thao tác hoàn tác trước khi chốt**|Gỡ / đổi / huỷ áp được không, mấy lần, có tiêu lượt không|4|R-212: 「解除」 gỡ được, **không tiêu lượt**, bao nhiêu lần cũng được|
|**Đ9 Lặp lại chính thao tác đã làm**|Nhập lại đúng thứ đang áp thì sao|4|R-214: ra **E-105**, không có câu riêng "đã áp rồi"|
|**Đ10 Thông báo phải kèm con số**|Message có bắt buộc chứa số/khoản còn thiếu không|3–4|R-213: E-103 phải hiện **số tiền còn thiếu**|
|**Đ11 Trạng thái rỗng / cạn**|Màn hình hiện gì khi tập rỗng — ẩn hết hay hiện xám|3–4|R-107: giỏ rỗng **ẩn hết** bảng tiền, thanh, ô phiếu, nút; chỉ còn một dòng|
|**Đ12 Thời hạn lưu dữ liệu tạm**|Giỏ / nháp / phiên giữ bao lâu|4–6|V-05: giỏ giữ **30 ngày** kể từ lần sửa cuối|
|**Đ13 Chuẩn hoá đầu vào**|Cắt khoảng trắng? Phân biệt hoa thường? Độ dài cố định?|3–5|V-08/R-205/R-206/R-207: 12 ký tự, không phân biệt hoa thường, trim, thiếu ký tự ⇒ E-101|
|**Đ14 Phân hạng / phân khúc & mốc riêng**|Có hạng khách không, mốc khác nhau không, xét hạng lúc nào|3–5|V-03 + R-304: 5.000/3.000円 theo hạng, **hạng xét tại lúc chốt đơn**|

**Cách dùng (2 phút):** grep 14 ô này trên spec đối thủ bằng từ khoá của miền. Ô nào 0 hit **và** nằm trong 3 màn hình/phạm vi brief ⇒ ứng viên cơ chế B. Xếp theo `W` giảm dần, lấy từ trên xuống. Ô nào đối thủ đã viết rõ ⇒ bỏ, trừ khi ta có lời khách nguyên văn nói **khác** (khi đó thành cơ chế A, `P = 0,85`).

**Cách dùng ngược (quan trọng hơn):** 14 ô này cũng là **hàng đợi hỏi AI Khách hàng buổi sáng** và **checklist lấp lỗ spec mình**. Ô nào ta không hỏi được thì phải tự điền mặc định ngành — vì đối thủ sẽ bắn đúng đây. Thi thử 11/09: ta hỏi 14 lượt tính token nhưng **không lượt nào** chạm Đ1, Đ2, Đ5, Đ6, Đ8, Đ9, Đ12, Đ13, Đ14 — tức bỏ trống 9/14 ô vừa là đạn vừa là giáp.

## 9. Nạp đạn bằng lượt hỏi AI Khách hàng (mới 12/09)

Ba thẻ kết quả của hệ chấm nói rõ ba điều kiện, và cả ba đều **đo được bằng một lượt hỏi**:

| Thẻ | Hệ thống ghi gì | Điều kiện thật |
|---|---|---|
|**TRÚNG**|"Spec B: không mô tả cách xử lý X · Executor: `<mặc định>` · Intent thật: `<khác>`"|spec đối thủ **im lặng** ở X **VÀ** specs thật có luật **lệch mặc định ngành**|
|**TRƯỢT**|"Spec A: mô tả rõ hành vi · Intent thật: khớp với câu trả lời"|đối thủ có luật, **hoặc** im lặng nhưng mặc định ngành trùng specs thật|
|**VÔ HIỆU**|"**AI Khách hàng: nghiệp vụ này nằm ngoài phạm vi ứng dụng** · Executor: không cần được gọi để trả lời"|**AI Khách hàng** phán ngoài phạm vi — máy xét trước, Executor không chạy|

Hai hệ quả đọc thẳng ra từ đây:

1. **Biến quyết định của TRÚNG không phải "đối thủ im lặng" mà là `Δ`** — **chênh lệch giữa specs thật và mặc định ngành**. Đối thủ im lặng chỉ mở cửa; `Δ ≠ 0` mới là viên đạn. `Δ = 0` thì Executor lấp chỗ im lặng bằng đúng đáp án ⇒ TRƯỢT ⇒ tặng đối thủ +1. Đây chính là 4 ứng viên bị dry-run loại ở thi thử 11/09 (§0).
2. **VÔ HIỆU do AI Khách hàng phán, không do người chấm suy** ⇒ **một lượt hỏi "X có thuộc phạm vi không" là bằng chứng mức 1 mạnh nhất**, và nó dập `P(VÔ HIỆU)` xuống ~0,02. Rẻ hơn mọi cách kháng nghị, vì kháng nghị chỉ mở cho ca VÔ HIỆU và chỉ tranh lại đúng câu phán này.

### 9.1 Ba dạng lượt nạp đạn

Cả ba đều phải qua **cổng 8 kiểm tra** của 20 §1 (một ý, không chỉ thị, không nhắc bộ máy chấm, không viện dẫn tài liệu). Câu bị từ chối không mất token, không mất nhịp — sai thì sửa và gửi lại.

| Dạng | Dùng khi | Khuôn | Đọc kết quả |
|---|---|---|---|
|**Δ-probe** (đo chênh lệch)|Ứng viên cơ chế B: đối thủ im lặng ở một ô §8|Hỏi thẳng **giá trị** của ô đó, dạng số hoặc nhị phân: `<Hệ thống>で<ô>は<A>と<B>のどちらでしょうか？`|Đáp **lệch** mặc định ngành ⇒ `Δ ≠ 0` ⇒ **bắn, cơ chế A, `P` = 0,85** · Đáp **trùng** mặc định ngành ⇒ `Δ = 0` ⇒ **bỏ ứng viên** (Executor sẽ đoán trúng) · Đáp deflection (`特に定めていません`) ⇒ **bỏ** — không có đáp án chuẩn thì không TRÚNG được|
|**Scope-probe** (diệt VÔ HIỆU)|Ứng viên nằm ở rìa phạm vi, hoặc điểm phạm vi ≥ 1|`<Hệ thống>の<màn hình/tính năng>で<nghiệp vụ X>は対象に含まれるのでしょうか？`|Đáp **có** ⇒ bằng chứng mức 1, `P(VÔ HIỆU)` = 0,02 · Đáp **ngoài phạm vi** ⇒ **xoá ứng viên ngay**, và ghi vào danh sách rào (bắn vào đây là −1 chắc chắn)|
|**Đếm-probe** (mở rộng băng đạn)|Ô Đ7 và mọi danh sách đóng|`<màn hình>で表示されるエラーは全部で何種類でしょうか？`|Số đáp lớn hơn số mã đối thủ có ⇒ **mỗi mã chênh là một ứng viên**; hỏi tiếp từng mã bằng lượt riêng|

**Thứ tự gửi:** Scope-probe trước Δ-probe cho cùng một ứng viên. Lý do: scope-probe trả về "ngoài phạm vi" thì Δ-probe thành lãng phí một nhịp, còn chiều ngược lại không đúng.

### 9.2 Bảng chuyển ứng viên → lượt hỏi

Chạy sau bước 0b của `/attack`, khi đã có danh sách ô §8 mà đối thủ im lặng.

| Cột | Nội dung |
|---|---|
|Ứng viên|ô Đ / dòng I-xx đối thủ tự khai|
|Mặc định ngành|tra `mac-dinh-nganh.md` — **phải điền trước khi gửi**, nếu không thì không đọc được kết quả|
|Lượt hỏi|nguyên văn, đã qua cổng 8 kiểm tra|
|Đáp = mặc định ⇒|**BỎ** (ghi lý do: `Δ = 0`)|
|Đáp ≠ mặc định ⇒|**BẮN**, nâng lên cơ chế A, `P` = 0,85|
|Deflection ⇒|**BỎ** — 20 §5 luật 9: không có đáp án chuẩn thì không có ca TRÚNG|

**Một lượt hỏi phục vụ hai vai.** Cùng câu đó vừa nạp đạn cho vai CÔNG vừa lấp lỗ spec mình cho vai THỦ — nên khi hàng đợi buổi sáng còn chỗ, ô §8 luôn đáng hỏi kể cả chưa thấy spec đối thủ.

### 9.3 Khi AI Khách hàng đã đóng trước pha CÔNG

Kiểm tra đầu ngày (00 §A2-4). Nếu đóng thì **không có lượt nạp đạn nào sau khi thấy spec đối thủ** — mọi thứ phải chốt từ pha hỏi. Hai việc bù:

1. **Hỏi trước theo bảng 14 ô §8** (20 §4d), vì đó là ô đề hay chốt nhất và ta chưa biết đối thủ hở đâu. Đây là cách duy nhất giữ optionality khi chưa thấy spec họ.
2. **Ghi `Δ` ngay lúc nạp câu trả lời**, không đợi tới pha CÔNG: mỗi dòng `A-xx` đánh dấu `Δ ≠ 0` nếu đáp lệch mặc định ngành. Cột này là **băng đạn xếp sẵn** — 13:00 chỉ việc lọc `Δ ≠ 0` rồi đối chiếu spec đối thủ, không phải đọc lại log.

Hệ quả cho `/elicit`: cột `Δ` thành cột bắt buộc của RTM, và những dòng `Δ ≠ 0` là thứ **không được viết rõ ràng quá** trong spec mình — viết rõ thì đối thủ đọc spec ta cũng biết. (Không phải giấu luật: luật vẫn phải có, chỉ là đừng làm nó nổi bật hơn các luật khác.)
