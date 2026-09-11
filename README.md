# Spec Battle Toolkit

Bộ công cụ Claude Code cho đội thi **HBLAB AI Hackathon #02 — Spec Battle** (12/09/2026): hỏi AI Khách hàng theo hàng đợi lượt một-ý trong hạn mức 4.000 token, viết & review spec ≤6.000 token, tấn công đội khác, kháng nghị. Chi tiết kiến trúc skill/agent xem [`.claude/README.md`](.claude/README.md); tiến độ chuẩn bị xem [`PROGRESS.md`](PROGRESS.md).

**Sau khi pull: mở phiên Claude Code MỚI** trong thư mục repo — agent tùy chỉnh (`executor`, `customer`) chỉ được nạp lúc khởi động phiên.

## Tham số đã chốt (họp BTC 09/09, khối hỏi cập nhật sau thi thử 11/09)

| Tham số | Giá trị |
|---|---|
| Nộp spec | **Markdown**, **≤ 10.000 token** (đích 9.000, sàn thực tế 7.500), sơ đồ chỉ dạng **mermaid**, **không ảnh**, khóa 12:00 |
| Hỏi AI Khách hàng | **KHÔNG giới hạn số câu · 4.000 token (hỏi + trả lời) · mỗi lượt đúng MỘT Ý · KHÔNG có memory** |
| Câu bị từ chối | Câu **chứa chỉ thị** (ép format, cap dòng/từ, "không giải thích") và câu **gộp nhiều ý** đều bị từ chối — không trừ token, không reset nhịp chờ |
| Nhịp chờ giữa hai lượt | Thi thử **45 giây** — đây thường là ràng buộc thật, không phải token |
| Ảnh gửi AI Khách hàng | 3 lần, **có tính token** ⇒ không dùng (`knowledge/20` §1-9) |
| Điểm | **CÔNG trúng +2 · THỦ đỡ được +1 · CÔNG bị VÔ HIỆU −1** |
| Spec đối thủ | Markdown, tải về được |
| Kháng nghị | Text gửi AI, BTC review — **chỉ khi test CÔNG của mình bị VÔ HIỆU** |
| Sửa test sau khi nộp | Không |
| Hội thoại AI Khách hàng | Được xem lại |
| Câu hỏi về vận hành hệ thống thi | BTC không trả lời (bảo mật) |

Bảng đầy đủ + cách đo token + 9 ô còn hở: [`knowledge/00-luat-choi.md`](knowledge/00-luat-choi.md) §A.

**Bốn thay đổi đổi cả chiến thuật, không chỉ đổi con số:**
1. **Không giới hạn số câu, nhưng cấm chỉ thị và mỗi lượt một ý** — hỏi thành **chuỗi lượt ngắn xếp hạng có đường cắt** (`knowledge/20` §3). Không ép được format nữa, nên **câu nhị phân "A hay B"** là công cụ duy nhất giữ câu trả lời ngắn. Ràng buộc thật là **nhịp chờ**, không phải token: ~65% ngân hàng câu hỏi vẫn phải tự điền bằng mặc định ngành (`knowledge/30` §1b).
2. **Công +2 / thủ +1** — trần điểm công 30, trần điểm thủ 15. Cùng một giờ bỏ vào buổi chiều sinh điểm gấp đôi; lời khuyên cũ "cạn giờ thì ưu tiên phòng thủ" đã bị sửa (`knowledge/00` §D1).
3. **Kháng nghị chỉ cho ca VÔ HIỆU** — rủi ro vai THỦ không có đường lùi, rủi ro vai CÔNG cứu được một phần. Bằng chứng phạm vi phải thu xong lúc 14:30, không phải 16:00.
4. **Cấu trúc spec = 10 mục BTC** (tài liệu "Spec Battle Anatomy" 11/09) — template 11 mục luật-nghiệp-vụ không còn là cấu trúc nộp. Bốn vùng mới (item màn hình, event, validation & message lỗi nguyên văn, API); **message lỗi nguyên văn giờ hỏi được và phải hỏi** — mỗi message một lượt, xem `knowledge/20` §3b.

## Cấu trúc thư mục

| Đường dẫn | Nội dung |
|---|---|
| `knowledge/00…50-*.md` | 10 file tri thức chưng cất (luật chơi & tham số, hiểu bài toán, domain, hàng đợi lượt hỏi + ngân hàng, viết spec, bảng quyết định, khả thi & vận hành, **cấu trúc spec BTC 10 mục**, từ mơ hồ, tấn công) — skill đọc trực tiếp |
| `.claude/skills/<tên>/SKILL.md` | 7 skill: `frame`, `elicit`, `spec-write`, `spec-review`, `attack`, `appeal`, `drill` |
| `.claude/agents/{executor,customer}.md` | 2 agent giả lập, mù bối cảnh thật |
| `data/btc/` | Tài liệu BTC gửi, bản trích nguyên văn (đối chiếu khi tranh luận). 16 PDF nguồn đã chưng cất hết vào `knowledge/` và bỏ khỏi repo — xem PROGRESS 11/09 |
| `hackathon_descriptions.md` | Tài liệu nền hợp nhất (số liệu tiền-09/09; tham số hiện hành ở `knowledge/00` §A) |
| `battle/` | Tạo khi vào thi thật: brief, log, RTM, spec, test |
| `drill/` | Fixture & kết quả diễn tập |

## Quy trình thi — buổi sáng (THỦ)

### 9:00–9:30 — Hiểu bài toán, và xếp hàng đợi hỏi

```bash
/frame                  # brief.md → battle/mo-hinh-bai-toan.md
                        # 6 khối mô hình + mâu thuẫn nội tại của brief
                        # → chia mọi ô chưa biết thành 3 nhóm:
                        #   (1) trên đường cắt  (2) dưới đường cắt  (3) tự điền mặc định ngành
/elicit ke-hoach        # hàng đợi xếp hạng + ĐƯỜNG CẮT bằng số + nguyên văn từng lượt
                        # + danh sách ô sẽ KHÔNG hỏi kèm giá trị mặc định
```

Đọc brief §3/§4, phần brief không nói thì hỏi BTC miệng (không tốn token): **nhịp chờ bao nhiêu giây** · **4.000 token của cả đội hay mỗi người** · **lý do VÔ HIỆU của đề có gồm "sai phạm vi" không**. Ba ô này đổi đường cắt và thứ hạng (`knowledge/00` §A2).

**Đường cắt** = `min( (phút pha hỏi ÷ nhịp) − 2 ; 4.000 ÷ ~200 token mỗi lượt )`.

### 9:30–10:30 — Chạy hàng đợi, viết spec song song

```bash
/elicit luot 1          # một dòng câu hỏi, một ý, không chỉ thị
/elicit nap             # dán câu trả lời → RTM + log có timestamp
/elicit luot 2          # … gửi liên tục theo nhịp, không chờ câu trước
/elicit tu-choi 3       # khi nhận "⚠ Bị từ chối": sửa đúng lỗi, gửi lại ngay (không mất token)
```

Mỗi lượt đi qua **cổng 8 kiểm tra**: một dấu `?` · đúng một ý · tự chứa · **không một chữ mệnh lệnh** · không viện dẫn tài liệu · không nhắc bộ máy chấm · không dẫn dắt · câu trả lời một từ vẫn dùng được.

**Phân vai bắt buộc:** một người chỉ bấm gửi đúng nhịp và dán câu trả lời; người còn lại **viết spec từ lượt thứ 3**. Ngồi chờ nhau là cách mất giờ lớn nhất trong ngày.

### 10:20–11:05 — Viết spec

```bash
/spec-write             # battle/spec.md, đích 9.000 token (sàn 7.500)
                        # mọi ô không hỏi được: điền MẶC ĐỊNH NGÀNH, mở dòng G-xx, xếp hạng rủi ro
                        # in bảng xếp hạng rủi ro giả định → nguyên liệu cho lượt xác nhận
```

Spec theo **cấu trúc 10 mục BTC** (tài liệu "Spec Battle Anatomy", 11/09): `1` Tổng quan & phạm vi · `2` Item màn hình · `3` Event · `4` Validation & message lỗi · `5` Wireframe · `6` Flow & quy tắc xử lý · `7` Ràng buộc/bất thường/chưa chốt · `8` Xác thực & phân quyền · `9` Luồng dữ liệu & API · `10` Data model, perf, security. Chi tiết: [`knowledge/33-cau-truc-spec-btc.md`](knowledge/33-cau-truc-spec-btc.md).

**Lưu đồ bắt buộc dạng Mermaid** (không có Figma trong phòng thi, bản nộp không có ảnh). BTC yêu cầu tường minh **ba** sơ đồ — thiếu là mất điểm hình thức:

| Sơ đồ | Mục | Vai trò |
|---|---|---|
| `block-beta` wireframe | 5 | bố cục màn hình + bảng 3 trạng thái (mặc định/lỗi/thành công) |
| `sequenceDiagram` | 6 (cuối) | flow end-to-end nhiều bên |
| `flowchart LR` sơ đồ hệ thống | 9 | ai nói chuyện với ai |
| `flowchart TD` **lưu đồ quyết định** | 6.x | nên có cho logic nhiều nhánh nhất — làm lộ **nhánh cụt** và chốt **thứ tự kiểm** |
| `stateDiagram-v2` | 6.3 | vòng đời trạng thái |
| `erDiagram` | 10 | khi quan hệ dữ liệu không hiển nhiên |

Cú pháp đã kiểm + **lint 10 lỗi làm gãy render**: [`knowledge/33` §7](knowledge/33-cau-truc-spec-btc.md). Ba luật không đổi: **sơ đồ không thay bảng** (ô `Từ chối 0.5`/`KHL` chỉ có trong bảng) · mỗi sơ đồ kèm 1–2 câu chữ tóm tắt · có sơ đồ thì catch-all phải có dòng 0.15 "bảng và luật có mã thắng". Mẹo rẻ nhất: viết nhãn sơ đồ **không dấu**, phần có dấu để ở câu tóm tắt.

### 11:05–11:30 — Lượt xác nhận, rồi vá

```bash
/elicit xac-nhan        # 8–10 lượt RỜI, mỗi lượt một phát biểu, ưu tiên dạng nhị phân
                        # (gộp 10 phát biểu vào một lượt như bản cũ sẽ BỊ TỪ CHỐI)
/elicit nap             # mọi câu trả lời khác giả định → một việc sửa BR cụ thể
/frame muc-tieu-luat    # bảng Mục tiêu ↔ Luật: mục tiêu nào chưa có luật, luật nào phá mục tiêu
```

### 11:38–11:52 — Review & nộp

```bash
/spec-review battle/spec.md 20        # cổng F · Mục tiêu↔Luật · lạm dụng · giá trị tự nghĩ ra (G-7)
                                      # eval qua HAI executor mù (đa nghĩa = hai reader lệch nhau)
                                      # đếm token bản nộp
/spec-review battle/spec.md 20 sửa    # áp các mục loại SỬA vào spec.md
```

Ba loại phát hiện: `SỬA` (áp được ngay) · `HỎI` (cắt theo số lượt còn kịp trước đường cắt, mỗi mục một lượt nhị phân) · `RỦI RO ĐÃ BIẾT` (hết giờ hỏi — không sửa, ghi lại để buổi chiều tự bắn trước).

## Quy trình thi — buổi chiều (CÔNG)

```bash
/attack cheo A.md B.md C.md      # 13:00 — BẢNG ĐỒNG THUẬN CHÉO 3 SPEC, chạy MỘT LẦN
                                 # → hai spec chỏi nhau · một spec im lặng (#21) · cả ba im lặng
/attack battle/doi-thu/B.md B    # rồi soi từng spec: cổng F bằng grep, 24 loại lỗ hổng,
                                 # gói bằng chứng phạm vi 3 mức, điểm kỳ vọng EV = 2·P(TRÚNG) − P(VÔ HIỆU)
```

Nộp **tối đa** 5 test/spec: ứng viên `EV ≤ 0` thì bỏ slot (0 điểm) thay vì bắn bừa (−1 điểm). Test không sửa được sau khi nộp.

## Kháng nghị (16:00–17:00)

```bash
/appeal                 # dán các ca test CÔNG bị chấm VÔ HIỆU
                        # → battle/appeal.md: ≤3 ca, text ≤150 từ/ca copy nguyên khối
```

Chỉ tranh **phạm vi**, không tranh đáp án chuẩn. Bằng chứng theo 3 mức: lời AI Khách hàng nguyên văn > câu brief tường minh > ≥2 spec đối thủ có luật.

## Diễn tập (bắt buộc trước ngày thi)

```bash
/drill <tên> [tính-năng] [tự-động|thủ-công]   # Chỉ chạy khi gõ lệnh trực tiếp
```

Trọn vòng theo đúng hạn mức 11/09: sinh specs thật ẩn → `/frame` → chạy hàng đợi lượt một-ý qua agent `customer` (agent **từ chối** câu chứa chỉ thị và câu gộp nhiều ý, đúng như hệ thật) → `/spec-write` song song → lượt xác nhận → `/spec-review` → `/attack` chính spec vừa viết → chấm bằng `executor` + `customer` → `drill/<tên>/ket-qua.md` với 10 chỉ số, gồm **điểm quy đổi +2/+1/−1**, **lỗ hổng giả định**, **chi phí bị từ chối** và **đúng nhịp hay không**.

## Ghi chú

- **Hai trục kiểm chất lượng.** *Hình thức* (mơ hồ, ô trống, truy vết, token) ở `knowledge/40`, `31`. *Nội dung* (hiểu bài toán, khả thi, vận hành, phục vụ mục tiêu, chịu được lạm dụng) ở `knowledge/05`, `32`. Một spec có thể đạt 100% trục hình thức mà vẫn sai bài toán — bằng chứng đo được ở `knowledge/32` §7.
- **Giả định là trạng thái bình thường năm nay.** Với 15–20 lượt kịp gửi, 55–75% luật trong spec vẫn có nguồn `G-xx` (mặc định ngành) thay vì `A-xx` (câu trả lời). Quy tắc: **điền mặc định ngành, không sáng tạo giá trị mới** — viết mặc định ra không xấu hơn im lặng ở bất kỳ ca nào, còn tự nghĩ ra một giá trị lạ là ca duy nhất tệ hơn im lặng (`knowledge/30` §1b).
- **Ranh giới sửa**: `SỬA` áp được ngay; `HỎI` bị chặn bởi số nhịp còn lại nên phải xếp hạng; hết giờ hỏi thì thành `RỦI RO ĐÃ BIẾT`, không sửa hộ bằng cách đoán lần thứ hai.
- **Executor** luôn được gọi mù và gọi hai lần (`/spec-review` khối D; reader 2 dùng model yếu hơn) — model Executor của BTC là thông tin bảo mật nên giả lập bảo toàn là lựa chọn duy nhất đúng.
- **Customer** chỉ dùng khi `/drill`; nó **từ chối** câu chứa chỉ thị hoặc câu gộp nhiều ý bằng đúng khuôn nhãn của hệ thật (`⚠ Bị từ chối · …` + `✓ Không trừ token`), để đội thấy ngay lượt nào sẽ mất nhịp ở phòng thi.
