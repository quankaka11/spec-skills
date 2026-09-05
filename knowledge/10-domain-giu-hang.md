# 10 · Miền nghiệp vụ "Đặt giữ hàng"

## 1. Ba biến thể — câu hỏi số 1 buổi sáng [HD §3.1]

| Biến thể | TTL điển hình | Có tiền? | Cụm luật chiếm nhiều từ nhất | Câu hỏi đầu tiên để nhận diện |
|---|---|---|---|---|
| Cart hold (giữ khi checkout) | 5–30 phút, tự động | Không | N4 tồn kho & đồng thời; N2 TTL | "Hold tạo tự động khi thêm vào giỏ, hay khách bấm nút 'Giữ hàng' riêng?" |
| Deposit reserve (đặt cọc giữ hàng) | Giờ → ngày | Có cọc, có refund policy | N5 tiền; N7 hủy/hoàn | "Cọc bao nhiêu để hold có hiệu lực? Hoàn ?% theo trường hợp nào?" |
| Store-pickup hold (giữ tại cửa hàng) | 1–3 ngày, theo giờ mở cửa | Thường không, có thể cọc | N2 TTL theo giờ mở cửa; N9 người nhận; N4 đa địa điểm | "Hold gắn một cửa hàng và khách đến lấy, hay giao tận nơi?" |

Câu chốt: "Liệt kê nghiệp vụ NGOÀI phạm vi tính năng này" [HD N1] — tránh VÔ HIỆU buổi chiều.

## 2. Glossary VN ↔ EN (mỗi thuật ngữ một dòng trong spec)

| # | VN | EN | Định nghĩa kiểm chứng được |
|---|---|---|---|
| 1 | Biến thể / mã hàng | Variant / SKU | Đơn vị nhỏ nhất được đếm tồn và được giữ; hold luôn trỏ vào SKU. |
| 2 | Địa điểm | Location | Nơi đếm tồn; mỗi SKU có bộ số lượng riêng tại mỗi địa điểm [Shopify]. |
| 3 | Tồn thực tế | On-hand | Tổng đơn vị vật lý tại địa điểm; công thức ở §3 [Shopify]. |
| 4 | Tồn khả bán | Available (ATP) | = on-hand − committed − unavailable; không gồm incoming [Shopify]. |
| 5 | Đang bị giữ | Reserved | Để riêng cho một khách, chưa thành đơn; thuộc unavailable [Shopify]. |
| 6 | Đã cam kết | Committed | Đơn vị thuộc đơn chưa giao; hệ thống tự quản, không chỉnh tay [Shopify]. |
| 7 | Đã phân bổ | Allocated | Tồn đã gán cho một đơn/kênh: hard (xuất kho) hoặc soft (tính toán) [Oracle]. |
| 8 | Tồn đệm an toàn | Safety stock | Phần on-hand cố ý không bán chống oversell; không được giữ [Shopify]. |
| 9 | Không khả bán | Unavailable | Nhóm gồm reserved, damaged, quality_control, safety_stock [Shopify]. |
| 10 | Hàng đang về | Incoming | Đang vận chuyển tới; không bán được cho tới khi nhận [Shopify]. |
| 11 | Bán vượt tồn | Oversell | Tổng cam kết + giữ > on-hand. |
| 12 | Đặt hàng chờ | Backorder | Nhận đơn khi available = 0, hứa ngày giao sau [Oracle]. |
| 13 | Danh sách chờ | Waitlist | Hàng đợi khách được ưu tiên khi hold khác giải phóng. |
| 14 | Đặt trước | Pre-order | Đặt mua hàng chưa có tồn; hold cần tồn thật. |
| 15 | Đặt giữ hàng / hold / khóa hàng / giữ chỗ | Hold / Reservation | Bản ghi khóa số lượng SKU cho một khách trong thời hạn; spec **khai báo bốn từ này là một** [HD §3.2]. |
| 16 | Dòng giữ | Hold line | Cặp (SKU, số lượng, địa điểm) trong một hold. |
| 17 | Thời hạn giữ | TTL / expires_at | Thời điểm hold tự mất hiệu lực; ghi rõ mốc bắt đầu và cách đếm. |
| 18 | Gia hạn | Extend | Đổi expires_at; ghi rõ cộng dồn hay reset, số lần tối đa. |
| 19 | Thời gian ân hạn | Grace period | Khoảng sau expires_at vẫn chấp nhận chốt đơn / chưa giải phóng tồn. |
| 20 | Hết hạn | Expire | Chuyển hold sang EXPIRED và giải phóng tồn. |
| 21 | Hủy | Cancel | Chấm dứt chủ động bởi actor; khác expire (thời gian). |
| 22 | Chuyển đổi / tiêu thụ | Convert / Consume | Hold thành đơn hàng (IBM: consume reservation) [IBM]. |
| 23 | Giải phóng | Release | Trả số lượng từ reserved về available. |
| 24 | Giữ một phần | Partial hold | Chấp nhận số lượng ít hơn yêu cầu. |
| 25 | Thời gian chờ tạo lại | Cooldown | Khoảng cấm tạo hold mới sau khi hủy/hết hạn. |
| 26 | Tiền cọc | Deposit | Khoản trả trước để hold có hiệu lực; cố định hoặc % giá trị. |
| 27 | Hoàn cọc | Refund | Trả lại cọc; quy định theo nguyên nhân kết thúc hold. |
| 28 | Khấu trừ cọc | Deposit offset | Cọc trừ vào tiền hàng khi convert. |
| 29 | Phí hủy / mất cọc | Cancellation fee / forfeit | Phần cọc không hoàn khi khách hủy. |
| 30 | Khóa giá | Price lock | Giá lúc tạo hold giữ đến khi convert. |
| 31 | Cổng thanh toán | Payment gateway | Bên ngoài xác nhận thanh toán; kết quả có thể trễ (async). |
| 32 | Đa kho | Multi-source inventory | SKU có tồn ở nhiều địa điểm; hold phải ghi gắn địa điểm nào. |
| 33 | Nhật ký kiểm toán | Audit log | Bản ghi bất biến: ai, lúc nào, from→to, lý do. |
| 34 | Tính lũy đẳng | Idempotency | Một yêu cầu gửi N lần chỉ tạo 1 hold; nhận diện bằng idempotency key. |
| 35 | Tranh chấp đồng thời | Race condition | Hai yêu cầu cùng đọc available rồi cùng trừ → oversell. |
| 36 | Đến trước được trước | FCFS | Xử lý theo thời điểm server tiếp nhận yêu cầu [HD §0.8]; tiêu chí thay thế (commit DB trước, cọc trước, hạng khách) là câu hỏi 20/N4-04 — spec phải ghi đúng một tiêu chí. |
| 37 | So-và-đổi | Compare-and-swap | Ghi chỉ thành công nếu giá trị hiện tại = giá trị kỳ vọng; sai → CHANGE_FROM_QUANTITY_STALE [Shopify]. |
| 38 | Danh sách đen | Blacklist | Khách bị cấm/hạn chế tạo hold. |

⚠ Cặp dễ lẫn phải tách: **reserved ≠ committed** (chưa/đã có đơn); **hold ≠ pre-order** (có/chưa có tồn); **expire ≠ cancel** (thời gian/hành động); **available ≠ on-hand** (bán được/có trong kho).

## 3. Mô hình trạng thái số lượng tồn kho

**Công thức trong spec (chọn một, viết ra):**
- `on_hand = available + committed + reserved + damaged + safety_stock + quality_control` [Shopify]
- `available_to_hold = available − safety_stock` (safety stock đã ngoài available thì bỏ số trừ — phải hỏi)

**Điều gì làm mỗi số đổi:**

| Sự kiện | available | reserved | committed | on_hand |
|---|---|---|---|---|
| Tạo hold | −q | +q | 0 | 0 |
| Hold → đơn | 0 | −q | +q | 0 |
| Hủy/hết hạn hold | +q | −q | 0 | 0 |

**Điểm ⚠ khác lẽ thường:**
1. ⚠ Shopify: hold **không trừ on_hand**, chỉ chuyển available → reserved [Shopify]. Executor không có spec thường đoán "trừ tồn".
2. ⚠ IBM: reservation **có thể thất bại dù truy vấn thấy còn hàng** (tồn không bị lock, bị giao dịch khác tiêu thụ) [IBM]. Spec phải nói người sau nhận gì.
3. ⚠ IBM: reservation có **expiration time** và purge agent dọn; hold hết hạn mất hiệu lực do job, không tức thời [IBM].
4. ⚠ Oracle: ATP tính theo **ngày**, thiếu hôm nay có thể "mượn" cung ngày mai; e-commerce hold thường không cho [Oracle 2-12].
5. ⚠ Oracle Allocated ATP: kênh ưu tiên có thể **lấy phần phân bổ của kênh thấp hơn** [Oracle 7182] → N14: VIP có "cướp" tồn đang hold của khách thường?
6. ⚠ Oracle: có chế độ "không xét reservation" khi tính ATP [Oracle 3-38] → hai hệ thống hiển thị tồn khác nhau; hỏi N12 nguồn chân lý.
7. ⚠ Safety stock: Shopify để trong unavailable; HD viết `Available = On-hand − Reserved − Safety stock` [HD §3.2] — kết quả khớp, nhưng spec phải nói tồn hiển thị cho khách có gồm safety stock không.
8. ⚠ Incoming không được giữ [Shopify]; đề cho phép giữ hàng đang về = ngoại lệ phải ghi.

## 4. Vòng đời một hold [HD §3.3]

**Tập trạng thái ứng viên (xác nhận với AI Khách hàng):** PENDING (chờ cọc/xác nhận) · ACTIVE · EXPIRED · CANCELLED · FULFILLED/CONVERTED · tùy chọn: PARTIALLY_FULFILLED, RELEASED_BY_STAFF, PAYMENT_FAILED.

**Bảng state × event (điền hết ô; ô "—" = "từ chối, mã lỗi X"):**

| Trạng thái \ Sự kiện | create | pay_deposit | confirm | extend | cancel | timeout | convert | stock_lost |
|---|---|---|---|---|---|---|---|---|
| (none) | → PENDING hoặc ACTIVE (không cọc) | — | — | — | — | — | — | — |
| PENDING | — (idempotent: trả hold cũ) | → ACTIVE | → ACTIVE | ?⚠ | → CANCELLED | → EXPIRED (TTL cọc riêng?) | ?⚠ | → CANCELLED(system) |
| ACTIVE | — | — | — | → ACTIVE, TTL mới | → CANCELLED | → EXPIRED (+grace?) | → FULFILLED | ?⚠ ai ưu tiên |
| EXPIRED | — | ?⚠ (tiền đến sau) | — | ?⚠ hồi sinh? | — | — | ?⚠ | — |
| CANCELLED | — | ?⚠ refund | — | — | — | — | — | — |
| FULFILLED | — | — | — | — | (luật đơn hàng, ngoài phạm vi?) | — | — | — |

Ô "?⚠" = chỗ spec thật hay phản trực giác — ưu tiên hỏi và bắn.

**6 câu cho MỖI mũi tên:**
1. Ai kích hoạt (khách / CSKH / admin / cron / cổng thanh toán / WMS)?
2. Tiền đề (trạng thái nguồn, quyền, còn lượt gia hạn, giờ mở cửa)?
3. Tồn kho biến động cột nào, bao nhiêu, tại địa điểm nào?
4. Cọc: giữ / hoàn ?% / khấu trừ — trong bao lâu?
5. Thông báo gì, cho ai, qua kênh nào?
6. Ghi audit log không, nội dung tối thiểu?

## 5. Actor và journey [HD §3.4]

**Actor (bỏ sót = lỗ hổng):** 1 guest chưa đăng nhập · 2 member mới · 3 member VIP/thân thiết · 4 khách blacklist / hủy nhiều · 5 CSKH · 6 sale/telesale · 7 nhân viên cửa hàng (POS) · 8 quản lý kho · 9 admin hệ thống · 10 cron job hết hạn · 11 cổng thanh toán (callback) · 12 ERP/WMS (stock sync) · 13 người được ủy quyền đến lấy hàng · 14 hệ thống khuyến mãi.

**Journey một hold — hỏi "thất bại ở đây → ?":**

| Bước | Câu "thất bại" cần hỏi |
|---|---|
| Xem sản phẩm | Tồn hiển thị có trừ hàng đang hold? "Còn 1" nhưng 1 đó đang bị giữ → hiển thị "còn 1" / "còn 0" / "đang được giữ" — chọn 1. |
| Bấm giữ | Hai khách cùng bấm đơn vị cuối → ai thắng; người sau: báo hết hàng / vào waitlist — chọn 1. Bấm 2 lần → 1 hay 2 hold? |
| Thanh toán cọc | Cổng timeout/không callback → PENDING sống ? phút? Tiền về sau khi PENDING hết hạn → ? |
| Nhận xác nhận | Email lỗi → hold vẫn hiệu lực? Mốc TTL bắt đầu từ đâu? |
| Chờ | Kiểm kê thiếu trong lúc hold → hủy hold nào (mới nhất / thường trước VIP) và hoàn cọc ?%? |
| Nhắc sắp hết hạn | Không gửi được → có gia hạn bù không? |
| Đến lấy / chốt đơn | Đến sau expires_at 10 phút → grace ? phút? Người khác lấy thay? Đổi SKU/số lượng khi chốt? |
| Hoàn tất | Hủy đơn sau convert → tồn về available hay tạo hold mới? Cọc hoàn ?%. |

## 6. Edge case theo N1–N14 [HD §3.5] (⚠ = phản trực giác)

**N1 Phạm vi**
1. Combo: giữ từng SKU thành phần hay giữ combo; một thành phần hết → cả hold fail? ⚠
2. Hàng digital / dịch vụ: có hold không (tồn vô hạn)? ⚠
3. Hàng flash sale/khuyến mãi bị loại khỏi hold? ⚠
4. Kênh POS/hotline tạo hold có TTL khác kênh web?

**N2 TTL**
5. TTL đếm giờ liên tục hay chỉ giờ mở cửa/ngày làm việc? ⚠
6. Mốc bắt đầu: lúc bấm / cọc thành công / nhân viên xác nhận — chọn 1. ⚠
7. Gia hạn: cộng dồn từ expires_at cũ hay reset từ lúc bấm gia hạn? ⚠
8. Gia hạn khi đã EXPIRED (hồi sinh) có được không? ⚠
9. Job hết hạn chạy mỗi N phút → hold quá hạn chưa bị quét có còn chốt được? ⚠ [IBM]
10. Downtime hệ thống làm TTL chạy quá → bù giờ hay không? ⚠
11. Múi giờ hiển thị vs múi giờ server khi khách ở nước khác.

**N3 Số lượng**
12. Khách muốn 10, còn 6 → từ chối toàn bộ hay giữ 6 (partial)? ⚠
13. Tối đa N hold/khách; hold N+1 bị từ chối hay thay hold cũ nhất? ⚠
14. Cùng SKU tạo 2 hold riêng → gộp hay từ chối? ⚠
15. Hủy rồi tạo lại ngay → cooldown ? phút; tính vào lịch sử hủy?

**N4 Tồn kho & đồng thời**
16. Hold trừ available, không trừ on_hand ⚠ [Shopify].
17. Hold có được lấy vào safety stock không (mặc định: không) ⚠ [Shopify].
18. Hai khách tranh đơn vị cuối — tiêu chí thắng: server nhận trước (mặc định §0.8) hay commit DB / cọc trước / hạng khách? Người sau: báo hết hàng / vào waitlist — chọn 1. ⚠ [IBM]
19. Reservation thất bại dù UI hiển thị còn hàng (stale read) → người bị từ chối: báo hết hàng / retry tự động / waitlist — chọn 1. ⚠ [IBM]
20. Oversell được phép ngưỡng X% hay 0? ⚠
21. Đa kho: hold gắn một địa điểm; kho A hết, kho B còn → tự chuyển hay từ chối? ⚠
22. Kiểm kê thiếu khi có hold: hủy hold nào trước (mới nhất / thấp ưu tiên / chưa cọc)? ⚠
23. Hàng incoming có được hold trước khi nhận? ⚠ [Shopify: không]
24. Tồn hiển thị cho khách khác có trừ hàng đang hold ("còn 3" hay "còn 0")? ⚠

**N5 Cọc & thanh toán**
25. **Thanh toán thành công sau khi hold đã hết hạn** → tự hoàn / tái tạo hold nếu còn hàng / chuyển đơn — chọn 1. ⚠⚠
26. Cọc thất bại → PENDING sống thêm ? phút, retry ? lần?
27. Callback cổng đến 2 lần (duplicate) → cọc ghi 1 lần (idempotency)? ⚠
28. Cọc có trừ vào tiền hàng khi convert, hay là phí riêng? ⚠
29. Cọc = % giá trị: tính trên giá lúc giữ hay lúc chốt?
30. Hoàn cọc theo nguyên nhân: khách hủy ?% · hệ thống hủy vì kho lỗi ?% · hết hạn ?% ⚠

**N6 Giá & khuyến mãi**
31. Giá khóa lúc giữ hay tính lại lúc chốt? ⚠ (HD ví dụ A-03: không khóa)
32. Giá giảm trong lúc hold → khách được giá thấp hơn? ⚠
33. Voucher/flash sale hết hạn giữa kỳ hold → còn áp dụng? ⚠
34. Phí ship/thuế tính ở bước nào?

**N7 Hủy & hết hạn**
35. Hủy sau khi đã cọc: được trước mốc nào, phí bao nhiêu? ⚠
36. Hết hạn → tồn về available ngay hay ưu tiên waitlist trong X phút? ⚠
37. Hủy nhiều lần → tự động blacklist/giảm hạn mức? ⚠
38. CSKH hủy hộ: lý do bắt buộc? thông báo khách?

**N8 Chuyển đổi**
39. Order tạo mới (ID mới) hay hold đổi trạng thái giữ ID? ⚠
40. Chốt với số lượng ít hơn hold → phần dư giải phóng ngay hay giữ đến TTL? ⚠
41. Đổi SKU khi chốt → hủy hold cũ, hold mới xếp hàng lại từ đầu?
42. Convert ngay sau expires_at trong grace period → cho hay không? ⚠

**N9 Actor & quyền**
43. Guest được hold không (HD ví dụ A-02: không)? ⚠
44. VIP có TTL/hạn mức khác, có được ưu tiên khi tranh chấp? ⚠ [Oracle Allocated ATP]
45. CSKH chuyển hold sang khách khác được không?
46. Hold giá trị lớn cần approval — khi chờ duyệt tồn đã trừ chưa? ⚠

**N10 Thông báo**
47. Nhắc trước X phút, mấy lần, kênh nào; không gửi được: gia hạn bù ? phút / không bù — chọn 1.

**N11 Hiển thị**
48. Đếm ngược theo giờ server hay client; lệch giờ máy khách thì tin ai? ⚠
49. Sản phẩm bị hold 100% hiển thị "hết hàng" hay "đang được giữ"? ⚠

**N12 Ngoại lệ hệ thống**
50. Sản phẩm bị ẩn/ngừng bán trong lúc hold → hold tự hủy, cọc hoàn ?%? ⚠
51. Cửa hàng đóng/tạm ngưng → hold chuyển địa điểm hay hủy? ⚠
52. ERP sync ghi đè tồn thấp hơn số đang hold → nguồn chân lý: e-commerce hay ERP? ⚠ [Oracle 3-38]
53. Bấm 2 lần / gửi lại request → 1 hold (idempotency key) hay 2? ⚠

**N13 Audit & phi chức năng**
54. Audit log tối thiểu: hold_id, actor, from→to, timestamp, lý do, delta tồn; ai được xem?

**N14 Ưu tiên xung đột**
55. VIP hết hạn vs khách thường đang waitlist → ai được đơn vị vừa giải phóng? ⚠
56. Kiểm kê thiếu 1 đơn vị: hủy hold chưa cọc trước hold đã cọc? ⚠

**Quy tắc chung:** Mọi câu ở file này phải trả lời được bằng số / tên trạng thái / 1 phương án; câu kết bằng thế nào/gì/thì sao bị coi là hỏng.

**Mẫu câu hỏi đóng cho AI Khách hàng:** "Liệt kê dạng bảng: trạng thái hold | sự kiện | đích | tồn kho | cọc | ai kích hoạt" — một câu phủ §4 [HD §2.1 ③].
