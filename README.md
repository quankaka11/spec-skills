# Spec Battle Toolkit

Bộ công cụ Claude Code cho đội thi **HBLAB AI Hackathon #02 — Spec Battle** (12/09/2026): hỏi AI Khách hàng trong hạn mức 5 câu, viết & review spec ≤6.000 token, tấn công đội khác, kháng nghị. Chi tiết kiến trúc skill/agent xem [`.claude/README.md`](.claude/README.md); tiến độ chuẩn bị xem [`PROGRESS.md`](PROGRESS.md).

**Sau khi pull: mở phiên Claude Code MỚI** trong thư mục repo — agent tùy chỉnh (`executor`, `customer`) chỉ được nạp lúc khởi động phiên.

## Tham số đã chốt (họp BTC 09/09)

| Tham số | Giá trị |
|---|---|
| Nộp spec | **Markdown**, **≤ 6.000 token** (đích 5.400), sơ đồ chỉ dạng mermaid, **không ảnh**, khóa 12:00 |
| Hỏi AI Khách hàng | **5 câu · mỗi lượt 1 câu · 5.000 token (hỏi + trả lời) · KHÔNG có memory** |
| Ảnh gửi AI Khách hàng | 3 lần (mặc định không dùng — xem `knowledge/20` §1-9) |
| Điểm | **CÔNG trúng +2 · THỦ đỡ được +1 · CÔNG bị VÔ HIỆU −1** |
| Spec đối thủ | Markdown, tải về được |
| Kháng nghị | Text gửi AI, BTC review — **chỉ khi test CÔNG của mình bị VÔ HIỆU** |
| Sửa test sau khi nộp | Không |
| Hội thoại AI Khách hàng | Được xem lại |
| Câu hỏi về vận hành hệ thống thi | BTC không trả lời (bảo mật) |

Bảng đầy đủ + cách đo token + 7 ô còn hở: [`knowledge/00-luat-choi.md`](knowledge/00-luat-choi.md) §A.

**Bốn thay đổi đổi cả chiến thuật, không chỉ đổi con số:**
1. **5 câu hỏi, không memory** — question bank 158 câu không còn là danh sách để hỏi; nó là nguồn **mặc định ngành để tự điền** cho ~70% spec (`knowledge/30` §1b) và kho phát biểu cho câu restate.
2. **Công +2 / thủ +1** — trần điểm công 30, trần điểm thủ 15. Cùng một giờ bỏ vào buổi chiều sinh điểm gấp đôi; lời khuyên cũ "cạn giờ thì ưu tiên phòng thủ" đã bị sửa (`knowledge/00` §D1).
3. **Kháng nghị chỉ cho ca VÔ HIỆU** — rủi ro vai THỦ không có đường lùi, rủi ro vai CÔNG cứu được một phần. Bằng chứng phạm vi phải thu xong lúc 14:30, không phải 16:00.
4. **Cấu trúc spec = 10 mục BTC** (tài liệu "Spec Battle Anatomy" 11/09) — template 11 mục luật-nghiệp-vụ không còn là cấu trúc nộp. Bốn vùng hoàn toàn mới (item màn hình, event, validation & message lỗi nguyên văn, API) mà kế hoạch 5 câu gần như không phủ được: xem `knowledge/20` §3b để chọn phương án trước 9:30.

## Cấu trúc thư mục

| Đường dẫn | Nội dung |
|---|---|
| `knowledge/00…50-*.md` | 10 file tri thức chưng cất (luật chơi & tham số, hiểu bài toán, domain, 5 câu hỏi + ngân hàng, viết spec, bảng quyết định, khả thi & vận hành, **cấu trúc spec BTC 10 mục**, từ mơ hồ, tấn công) — skill đọc trực tiếp |
| `.claude/skills/<tên>/SKILL.md` | 7 skill: `frame`, `elicit`, `spec-write`, `spec-review`, `attack`, `appeal`, `drill` |
| `.claude/agents/{executor,customer}.md` | 2 agent giả lập, mù bối cảnh thật |
| `data/btc/` | Tài liệu BTC gửi, bản trích nguyên văn (đối chiếu khi tranh luận). 16 PDF nguồn đã chưng cất hết vào `knowledge/` và bỏ khỏi repo — xem PROGRESS 11/09 |
| `hackathon_descriptions.md` | Tài liệu nền hợp nhất (số liệu tiền-09/09; tham số hiện hành ở `knowledge/00` §A) |
| `battle/` | Tạo khi vào thi thật: brief, log, RTM, spec, test |
| `drill/` | Fixture & kết quả diễn tập |

## Quy trình thi — buổi sáng (THỦ)

### 9:00–9:30 — Hiểu bài toán, và chốt 5 câu sẽ hỏi

```bash
/frame                  # brief.md → battle/mo-hinh-bai-toan.md
                        # 6 khối mô hình + mâu thuẫn nội tại của brief
                        # → chia mọi ô chưa biết thành 3 nhóm:
                        #   (1) vào C1–C4  (2) hàng đợi câu C5  (3) tự điền mặc định ngành
/elicit ke-hoach        # gọt 4 câu hỏi theo brief + in danh sách ô sẽ KHÔNG hỏi
```

Hỏi BTC miệng trước 9:30 (không tốn token): **ảnh có tính vào 5.000 token không** · **AI Khách hàng còn mở sau 12:00 không**. Hai câu này đổi kế hoạch (`knowledge/00` §A2).

### 9:30–10:20 — Bốn câu hỏi dữ liệu

```bash
/elicit cau 1           # C1 phạm vi NGOÀI + điều PHẢI ngăn
/elicit nap             # dán câu trả lời → RTM + log có timestamp
/elicit cau 2           # C2 bảng chuyển trạng thái đầy đủ  ← câu lãi nhất, không bỏ
/elicit cau 3           # C3 bảng tham số (gồm đơn vị neo hạn mức, ngày tiền về tay khách)
/elicit cau 4           # C4 tám kịch bản suy biến & thất bại phụ thuộc ngoài
```

Mỗi câu đi qua **cổng 5 kiểm tra** trước khi in ra: đúng một dấu `?` · không tham chiếu lượt trước · có ép format và cap dòng/từ · câu trả lời tệ nhất vẫn dùng được · chỉ dùng từ của brief. AI không có memory nên **không có lần thử thứ hai**.

### 10:20–11:05 — Viết spec

```bash
/spec-write             # battle/spec.md, đích ≤5.400 token
                        # mọi ô không hỏi được: điền MẶC ĐỊNH NGÀNH, mở dòng G-xx, xếp hạng rủi ro
                        # in khối "→ C5": 10 phát biểu Đúng/Sai đã viết sẵn
```

Spec theo **cấu trúc 10 mục BTC** (tài liệu "Spec Battle Anatomy", 11/09): `1` Tổng quan & phạm vi · `2` Item màn hình · `3` Event · `4` Validation & message lỗi · `5` Wireframe · `6` Flow & quy tắc xử lý · `7` Ràng buộc/bất thường/chưa chốt · `8` Xác thực & phân quyền · `9` Luồng dữ liệu & API · `10` Data model, perf, security. Chi tiết: [`knowledge/33-cau-truc-spec-btc.md`](knowledge/33-cau-truc-spec-btc.md).

Sơ đồ vẽ bằng **Mermaid** (không có Figma trong phòng thi): `block-beta` wireframe · `stateDiagram-v2` state machine · `sequenceDiagram` flow end-to-end · `flowchart LR` sơ đồ hệ thống. Cú pháp mẫu ở [`knowledge/33` §7](knowledge/33-cau-truc-spec-btc.md). **Sơ đồ không thay bảng** — sơ đồ chỉ vẽ chuyển hợp lệ, ô `Từ chối 0.5`/`KHL` chỉ có trong bảng; hết chỗ thì bỏ sơ đồ, giữ bảng.

### 11:05–11:30 — Câu hỏi cuối, rồi vá

```bash
/elicit restate         # = câu 5, lấy 10 phát biểu từ bảng xếp hạng rủi ro
/elicit nap             # mọi ý "Sai" → một việc sửa BR cụ thể
/frame muc-tieu-luat    # bảng Mục tiêu ↔ Luật: mục tiêu nào chưa có luật, luật nào phá mục tiêu
```

### 11:38–11:52 — Review & nộp

```bash
/spec-review battle/spec.md 20        # cổng F · Mục tiêu↔Luật · lạm dụng · giá trị tự nghĩ ra (G-7)
                                      # eval qua HAI executor mù (đa nghĩa = hai reader lệch nhau)
                                      # đếm token bản nộp
/spec-review battle/spec.md 20 sửa    # áp các mục loại SỬA vào spec.md
```

Ba loại phát hiện: `SỬA` (áp được ngay) · `HỎI` (chỉ khi còn câu hỏi, xuất thành phát biểu cho C5) · `RỦI RO ĐÃ BIẾT` (hết câu hỏi — không sửa, ghi lại để buổi chiều tự bắn trước).

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

Trọn vòng theo đúng hạn mức 09/09: sinh specs thật ẩn → `/frame` → 4 câu hỏi qua agent `customer` (mỗi lần một câu, không memory) → `/spec-write` → câu 5 restate → `/spec-review` → `/attack` chính spec vừa viết → chấm bằng `executor` + `customer` → `drill/<tên>/ket-qua.md` với 8 chỉ số, gồm **điểm quy đổi +2/+1/−1**, **lỗ hổng giả định** và **hiệu quả câu restate**.

## Ghi chú

- **Hai trục kiểm chất lượng.** *Hình thức* (mơ hồ, ô trống, truy vết, token) ở `knowledge/40`, `31`. *Nội dung* (hiểu bài toán, khả thi, vận hành, phục vụ mục tiêu, chịu được lạm dụng) ở `knowledge/05`, `32`. Một spec có thể đạt 100% trục hình thức mà vẫn sai bài toán — bằng chứng đo được ở `knowledge/32` §7.
- **Giả định là trạng thái bình thường năm nay.** Với 5 câu hỏi, 60–80% luật trong spec sẽ có nguồn `G-xx` (mặc định ngành) thay vì `A-xx` (câu trả lời). Quy tắc: **điền mặc định ngành, không sáng tạo giá trị mới** — viết mặc định ra không xấu hơn im lặng ở bất kỳ ca nào, còn tự nghĩ ra một giá trị lạ là ca duy nhất tệ hơn im lặng (`knowledge/30` §1b).
- **Ranh giới sửa**: `SỬA` áp được ngay; `HỎI` chỉ còn một câu để hỏi nên phải xếp hạng; hết câu thì thành `RỦI RO ĐÃ BIẾT`, không sửa hộ bằng cách đoán lần thứ hai.
- **Executor** luôn được gọi mù và gọi hai lần (`/spec-review` khối D; reader 2 dùng model yếu hơn) — model Executor của BTC là thông tin bảo mật nên giả lập bảo toàn là lựa chọn duy nhất đúng.
- **Customer** chỉ dùng khi `/drill`; nó cố tình chỉ trả lời một câu hỏi mỗi lượt và đánh dấu `[BỎ QUA: …]` để đội thấy ngay câu nào bị nhồi quá.
