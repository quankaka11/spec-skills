*Dùng cho người viết §6 (decision table cho luật ≥ 2 điều kiện: TTL/giá/quyền) và §5 (state table vòng đời hold) của template knowledge/30 §1, buổi sáng 10:30–11:30; và cho người soi spec đối thủ 13:00–14:00 tìm ô trống.*

# 31. Decision table & State transition table — dựng và chứng minh không ô trống

## 1. Decision table (bảng quyết định) — cấu tạo [DMN §8.1–8.2]

| Thành phần | Viết vào spec thế nào |
|---|---|
| Input (biến điều kiện: loại khách, loại hàng, kênh…) | Mỗi input một cột; khai báo **danh sách giá trị cho phép** dưới tên cột [DMN 8.2.4] |
| Input entry / Output entry | Giá trị rời (`VIP`), khoảng (`[0,10)`), `-` / Số + đơn vị (`120 phút`) hoặc mã luật (`Từ chối theo BR-11`) |
| Rule (một hàng = một tổ hợp input → output) | Đánh số R1, R2… để kháng nghị trích được |
| Hit policy (xử lý khi >1 rule khớp) | Một chữ cái ở ô góc bảng + một câu giải nghĩa tại §0 |
| `-` (any) | **Chồng lấn với mọi entry** cùng cột [DMN §8.1] → nguồn overlap số 1 |

Định nghĩa để soi [DMN §8.1, 8.2.4]:
- Hai rule **chồng lấn** khi *mọi* cột đều có giao; **rời** khi *ít nhất một* cột không chung giá trị.
- Bảng **đầy đủ** khi rule phủ *mọi tổ hợp* giá trị cho phép. Giá trị một input phải rời, phủ kín: `<5, <10` chồng lấn; `<5, >5` hở điểm 5.

### Hit policy dùng trong spec [DMN 8.2.11]

| Ký hiệu | Tên | Ý nghĩa | Khi dùng trong spec giữ hàng |
|---|---|---|---|
| **U** | Unique | Không chồng lấn; đúng 1 rule khớp (mặc định DMN) | **Khuyến nghị mặc định.** Phải tự chứng minh đủ + rời (mục 2) |
| **P** | Priority | Nhiều rule khớp; chọn output **đứng trước trong danh sách ưu tiên output**, *không* theo thứ tự hàng | **Khuyến nghị thứ hai** khi có luật "chặn" (Guest → từ chối; Flash-sale → 15 phút) cắt ngang nhiều luật. Ghi danh sách ưu tiên ngay dưới bảng |
| **F** | First | Lấy rule **đầu tiên theo số hàng** | DMN: "không phải good practice"; nếu dùng **bắt buộc đánh số hàng** |
| **C** | Collect (+ < > #) | Trả **tất cả** rule khớp, gộp bằng tổng / min / max / đếm | Phí phạt cộng dồn (+), TTL = **min** (<). Ghi rõ toán tử |

A, O, R: không dùng trong spec thi (kết quả nhiều hàng/undefined = lỗ hổng #5/#8).

Mẫu câu cho §0:
> "0.x Mọi bảng quyết định dùng hit policy **U** trừ khi ô góc ghi chữ khác. Bảng **P**: nhiều hàng khớp → lấy output đứng **trước** trong danh sách ưu tiên dưới bảng. `-` = mọi giá trị. `[a,b)`: gồm a, không gồm b."

## 2. Chứng minh bảng đầy đủ và không chồng lấn

### 2.1 Phân hoạch miền input
1. Mỗi input: liệt kê **toàn bộ** giá trị cho phép, ghi số phân hoạch n₁, n₂, n₃… Biến rời → tập hữu hạn; biến số → khoảng nửa mở `[a,b)` nối đuôi từ 0 tới `∞`.
2. Khoảng kề: điểm cuối khoảng trước **=** điểm đầu khoảng sau (không hở, không đè) → bịt lỗ hổng #4.
3. Thêm giá trị **"Khác"** cho biến rời nếu AI Khách hàng chưa khẳng định tập đóng (khách chưa đăng nhập, hàng không thuộc loại nào).

### 2.2 Đếm tổ hợp
- Tổng tổ hợp phải phủ **= n₁ × n₂ × n₃** [ISTQB 4.2.3].
- Mỗi rule phủ **tích** các entry của nó; `-` tính bằng nᵢ của cột. VD `(Guest, -, -)` với n₂=3, n₃=2 phủ 6.
- **Đủ và duy nhất** ⇔ Σ(tổ hợp mỗi rule) = tích **và** không cặp rule nào chồng lấn. Tổng < tích → gap; > tích → overlap; = tích kèm overlap → cũng gap.

### 2.3 Thuật toán tay 5 bước
1. **Chuẩn hóa**: mỗi rule một hàng, mỗi cột một entry; thay "còn lại" bằng giá trị tường minh.
2. **Đếm** theo 2.2, ghi tổng cạnh bảng. Tổng ≠ tích → sửa trước.
3. **Soi overlap từng cặp rule**: tìm **một cột** hai entry rời → cặp rời. Không có cột nào → chồng lấn → tách rule hoặc chuyển P/C.
4. **Soi gap bằng cây liệt kê**: cố định input 1, rồi input 2, liệt kê giá trị input 3 và ghi tên rule phủ; ô không tên → gap.
5. **Bắn thử 3 tình huống** như Executor: điểm nối khoảng, giá trị "Khác", giá trị trong vùng `-`. Mỗi tình huống khớp **đúng 1** rule (U) hoặc có ưu tiên (P).

### 2.4 Gộp rule bằng `-`
Chỉ gộp 2 rule **khác đúng 1 cột, cùng output**; dùng `-` chỉ khi gộp hết tập giá trị của cột, nếu không viết `{Web, App}`; đếm lại 2.2 sau gộp. Không dùng `-` ở cột có rule "chặn" trừ khi chuyển P.

### 2.5 Ví dụ: TTL giữ hàng (số liệu giả định — thay bằng đáp án AI Khách hàng)

Phân hoạch: Loại khách {Guest, Member, VIP} n₁=3 · Loại hàng {Thường, Flash-sale, Pre-order} n₂=3 · Kênh {Online, POS} n₃=2 → **18 tổ hợp**.

**DT-1 TTL (phút) — hit policy U**

| # | Loại khách | Loại hàng | Kênh | Kết quả | Phủ |
|---|---|---|---|---|---|
| R1 | Guest | - | - | Từ chối tạo hold (BR-11) | 6 |
| R2 | Member | Thường | Online | 120 | 1 |
| R3 | Member | Thường | POS | 60 | 1 |
| R4 | Member | Flash-sale | - | 15 | 2 |
| R5 | Member | Pre-order | - | 1440 | 2 |
| R6 | VIP | Thường | - | 240 | 2 |
| R7 | VIP | Flash-sale | - | 15 | 2 |
| R8 | VIP | Pre-order | - | 2880 | 2 |
| | | | | **Tổng** | **18 = 3×3×2** |

Chứng minh rời: R1 rời mọi rule ở cột 1; R2–R5 rời R6–R8 ở cột 1; trong mỗi nhóm, rule rời nhau ở cột 2 hoặc 3 ⇒ tổng = tích, không overlap ⇒ **đầy đủ, duy nhất**.

Biến thể P: thay R4+R7 bằng `(-, Flash-sale, -) → 15` → chồng lấn R1 tại (Guest, Flash-sale, *). Phải ghi: "Hit policy P; ưu tiên output: *Từ chối* > 15 > 60 > 120 > 240 > 1440 > 2880". Thiếu = lỗ hổng #8.

Kèm mốc bắt đầu TTL (server tạo hold) và hiệu lực `[tạo, tạo+TTL)` (§0.3); thiếu → đủ ô vẫn bị bắn #3/#4.

## 3. State transition table (bảng chuyển trạng thái)

Luật viết [HD §3.3, §4.7 mục 7]: hàng = trạng thái, cột = event, **mọi ô phải có giá trị**: `→ ĐÍCH + mã hiệu ứng`, hoặc **`KHL`** (từ chối theo §0.5, giữ trạng thái, ghi log). ⚠ ISTQB để trống ô KHL [ISTQB 4.2.4]; spec thi **không được** — Executor tự điền theo lẽ thường [HD §2.2].

Mã hiệu ứng (khai báo một lần ở đầu bảng §5):

| Nhóm | Mã |
|---|---|
| Tồn kho | K0 không đổi · K− *available* → *reserved* · K+ *reserved* → *available* · K→ *reserved* → *committed* |
| Tiền | T0 không thu/hoàn · T− thu cọc (BR cọc ở §6) · T+ hoàn 100% · Tp hoàn sau phạt (BR phí hủy ở §6) · T→ cọc trừ vào đơn |
| Thông báo | N0 không · NC khách · NA admin/CSKH (kênh §9) |
| Log | L ghi audit log (§0.7), kể cả ô KHL |

**ST-1 vòng đời Hold** (tập trạng thái ứng viên — xác nhận với AI Khách hàng trước khi điền)

Giá trị K/T/N trong bảng là **MINH HỌA**; trước khi chép vào spec thay từng ô bằng đáp án Lượt 2 (state machine) và Lượt 3 (con số) của knowledge/20 §3; ô chưa có đáp án → hỏi ở lượt restate hoặc để KHL. `T?` = bắt buộc điền.

| Trạng thái \ Event | E1 create | E2 cọc OK | E3 extend | E4 khách cancel | E5 admin cancel | E6 timeout | E7 checkout | E8 cọc thất bại | E9 tồn giảm < số giữ |
|---|---|---|---|---|---|---|---|---|---|
| **(chưa có)** | →PENDING K− T0 NC L | KHL | KHL | KHL | KHL | KHL | KHL | KHL | KHL |
| **PENDING** | KHL (trùng key → kết quả cũ §0.10) | →ACTIVE K0 T− NC L | KHL | →CANCELLED K+ T0 NC L | →CANCELLED K+ T0 NC+NA L | →EXPIRED K+ T0 NC L | KHL | →CANCELLED K+ T0 NC L | →CANCELLED K+ T0 NC+NA L |
| **ACTIVE** | KHL | KHL | [lần < 2] →ACTIVE TTL mới K0 T0 NC L; [lần ≥ 2] KHL | →CANCELLED K+ T? NC L | →CANCELLED K+ T? NC+NA L | →EXPIRED K+ T? NC L | →FULFILLED K→ T→ NC L | KHL | →CANCELLED K+ T? NC+NA L |
| **FULFILLED** | KHL | KHL | KHL | KHL (hủy đơn ngoài §1) | KHL | KHL | KHL | KHL | KHL |
| **EXPIRED** | KHL (hold mới = E1) | KHL | KHL | KHL | KHL | KHL | KHL | KHL | KHL |
| **CANCELLED** | KHL (hold mới = E1) | KHL | KHL | KHL | KHL | KHL | KHL | KHL | KHL |

Kiểm: 6 × 9 = 54 ô, điền đủ 54. Mỗi ô hợp lệ trả lời đủ 6 câu [HD §3.3]: ai kích hoạt (event + bảng quyền §3), guard trong `[ ]`, K, T, N, L. Event 2 đích không guard = lỗ hổng #5.

## 4. Mức phủ khi tự kiểm và khi soi đối thủ [ISTQB 4.2.4]

| Mức | Dùng trong cuộc thi |
|---|---|
| All-states | Yếu nhất; soi đối thủ **đủ tập trạng thái** chưa (gộp EXPIRED với CANCELLED → bắn "hết hạn có bị phạt như tự hủy?") |
| Valid transitions (0-switch) | Tự kiểm: mỗi ô hợp lệ ST-1 có ≥1 dòng Gherkin/BR |
| All transitions (gồm mọi chuyển KHL, mỗi test 1 chuyển KHL) | Tự kiểm: mỗi ô KHL suy ra được từ §0.5 |
| 1-switch (chuỗi 2 chuyển) | Tình huống "vừa X thì Y": timeout ngay sau extend; checkout cùng giây timeout (#2, #7) |

Chuyển KHL là nguồn test **rẻ nhất**: ST-1 có 54 − 13 = 41 ô KHL, spec happy path im lặng về hầu hết. Ưu tiên ô KHL mà lẽ thường nói **"được"** (hủy hold FULFILLED, extend hold PENDING, admin cancel hold EXPIRED) — đáp án chuẩn có thể là từ chối [HD §2.3].

## 5. Ánh xạ sang tấn công

| Phát hiện trong spec đối thủ | Lỗ hổng [HD §5.1] | Mẫu tình huống bắn |
|---|---|---|
| Decision table thiếu tổ hợp (tổng < tích) | #1 / #2 | "Khách **VIP** giữ hàng **Pre-order** qua **POS**, TTL bao nhiêu phút?" — nêu đúng tổ hợp trống |
| Input số không phân hoạch kín (`<5`, `>5`) | #4 | "Khách giữ **đúng 5** đơn vị — áp mức nào?" |
| Hai rule chồng lấn, không hit policy (thường do `-` chéo cột) | #8 | Tình huống nằm **đúng giao**: "VIP giữ hàng Flash-sale: TTL 240 hay 15?" — Executor phải chọn một |
| Ô trống state table | #6 | "Hold đang **X** lúc hh:mm:ss thì xảy ra **Y**. Hold sang trạng thái nào?" — K/T kỳ vọng ghi vào hồ sơ 50 §7, không đưa vào câu hỏi |
| Chỉ có sơ đồ mũi tên, không bảng | #6 / #12 | Bắn event **hệ thống** vắng trong sơ đồ: "Admin hạ tồn về 0 khi hold ACTIVE"; "Cổng thanh toán báo cọc thất bại sau ACTIVE" |
| Event 2 đích không guard | #5 | "Khách extend lần thứ 3 — được không?" |

Soi 1 bảng đối thủ trong 3 phút: (1) tính n₁×n₂×n₃ theo giá trị **họ tự khai**; (2) cộng tổ hợp mỗi hàng; (3) lệch → tổ hợp trống làm tình huống; (4) 2 hàng có `-` chéo cột → tình huống giao; (5) state table: mọi event trong spec × mọi trạng thái, ô không có câu trả lời → ứng viên.

Trước khi bắn, đối chiếu log buổi sáng: chỉ bắn ô **AI Khách hàng đã trả lời** và đáp án **khác lẽ thường** [HD §2.3]; đáp án = lẽ thường → TRƯỢT, bỏ.
