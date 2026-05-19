# Skill 02 — Quy tắc viết tiêu đề & nội dung (V{{ taxonomy.version | replace("v", "") }})

## 1. Tiêu đề — `<mã> <tên page>` theo type

Title H1 luôn có format `<mã> <tên>` (KHÔNG dấu chấm sau mã). Phần `<tên>` phụ thuộc **type** (xem [skill 11](11-page-types.md)):

| Type | `<tên>` rule | Ví dụ full title |
|---|---|---|
| **SOP** / **PBK** | Câu hỏi HOẶC noun phrase | `OPS-CS-SOP-001 Tiếp nhận và phân loại yêu cầu` · `OPS-LIVE-PBK-001 Livestream bùng đơn` |
| **MST** | "MASTER <tên chuẩn>" | `OPS-CS-MST-002 MASTER Chính sách đổi trả` · `OPS-PIM-MST-001 MASTER SKU List` |
| **PROC** | Câu mô tả luồng (mới V4.1) | `OPS-CS-PROC-001 Luồng xử lý khách hàng từ inbox đến đóng ticket` |
| **CHK** | "Checklist <chủ đề>" | `OPS-WH-CHK-001 Checklist kiểm kê kho` |
| **TMP** | "Template <tên>" | `TMP-GEN-TMP-001 Template họp nội bộ` |
| **HUB** | "<Tên section> — Tổng quan" | `OPS-CS-HUB-001 CSKH — Tổng quan` |
| **DBD** | "Dashboard <tên>" | `OPS-INV-DBD-001 Dashboard Settlement` |
| **DIC** | Noun phrase | `GEN-04-DIC-001 Thuật ngữ kinh doanh A–Z` |
| **POL** | Noun phrase (external) | `OPS-ECM-POL-001 TikTok Shop policy` |
| **GDL** | Noun phrase (luật mềm, mới V4.1) | `OPS-MKT-GDL-001 Brand voice guideline` |
| **LOG** | "<Tên log>" | `BOD-GOV-LOG-001 Decision Log` |
| **IDX** | "<Tên index>" | `SYS-00-IDX-001 Master Wiki Index` |

### Quy tắc chung

- **SOP** câu hỏi khi mô tả thao tác từ góc nhìn người dùng ("làm gì", "khi nào", "xử lý thế nào")
- **PROC** mô tả luồng đa vai trò có bàn giao — câu chứa "Luồng" hoặc "từ X đến Y"
- **MST** luôn có prefix "MASTER" để dễ nhận diện
- **CHK/TMP** luôn có prefix "Checklist"/"Template"
- **HUB** luôn có suffix "— Tổng quan"
- **POL** chỉ dành cho luật/policy NGOÀI (sàn, luật, NĐ) — internal là MST, không phải POL

### Lỗi sai phổ biến

❌ SAI title:

```
"OPS-CS-SOP-001. Tiếp nhận yêu cầu"      → thừa dấu chấm sau mã
"Tiếp nhận yêu cầu"                       → thiếu mã
"OPS-CS-SOP-001 — Tiếp nhận yêu cầu"     → dùng " — " thay khoảng trắng — KHÔNG nhất quán
```

❌ SAI prefix theo type:

```
SOP có noun phrase mơ hồ: "OPS-CS-SOP-001 Khiếu nại"
  → đổi: "OPS-CS-SOP-001 Xử lý khách khiếu nại"

MST không có "MASTER": "OPS-CS-MST-002 Chính sách đổi trả"
  → đổi: "OPS-CS-MST-002 MASTER Chính sách đổi trả / hoàn tiền"

HUB thiếu "— Tổng quan": "OPS-CS-HUB-001 CSKH"
  → đổi: "OPS-CS-HUB-001 CSKH — Tổng quan"

POL nội bộ: "INT-HR-POL-001 Nội quy lao động"
  → POL chỉ external. Đổi: "INT-HR-MST-001 MASTER Nội quy lao động" (derive từ POL Luật LĐ)
```

## 2. Quy tắc viết nội dung

```
✅ Viết cho người đọc — không phải cho người viết
✅ Mỗi bước = 1 hành động cụ thể — phải có động từ
✅ Có ví dụ thực tế nếu bước phức tạp
✅ Deadline phải có con số cụ thể (24h, 3 ngày, ngày 26 hàng tháng…)
✅ Ai đọc cũng tự làm được — không cần hỏi thêm

❌ Không dùng từ mơ hồ: "nhanh chóng", "kịp thời", "phù hợp"
❌ Không viết quá 2 trang A4 (ngoại trừ MASTER có nội dung chuẩn)
❌ Không gộp nhiều hành động vào 1 bước
❌ Không dùng giọng văn quan liêu, văn bản hành chính
```

## 3. Thuật ngữ chuyên ngành — phải link về DIC lần đầu

Khi nhắc đến thuật ngữ chuyên ngành **lần đầu** trong trang:

- **In đậm** từ đó
- Link inline đến `GEN-04-DIC-*` tương ứng HOẶC giải thích ngắn trong ngoặc đơn

### Ví dụ

```
Chỉ số **eNPS** *(Employee Net Promoter Score — đo hài lòng nhân viên)*
cần đạt ≥ 50.
```

```
**Phiếu BG** *(Bàn giao — xem [[GEN-04-DIC-002]] Thuật ngữ vận hành)*
được KTT chuyển cho HCNS trước ngày 03 hàng tháng.
```

## 4. Số liệu — phải cụ thể

```
✅ "Hoàn thành trước 17h ngày 03 hàng tháng"
✅ "Tối đa 30 phút response"
✅ "≥ 50 (theo thang điểm chuẩn eNPS)"

❌ "Hoàn thành sớm"
❌ "Response nhanh"
❌ "Đạt mức tốt"
```

## 5. Định dạng

- **Bold** cho từ khoá, deadline, tên file, tên người
- *Italic* cho thuật ngữ định nghĩa
- `code` cho lệnh, file path, ID, mã page (`OPS-CS-SOP-001`)
- Block quote (`>`) cho cảnh báo hoặc trích dẫn chính sách
- `[[mã page]]` cho link nội bộ wiki (sẽ tự resolve qua `scripts/build_backlink_graph.py`)

## 6. Tránh

- Emoji ngoài bộ chuẩn (xem [skill 01](01-page-format.md)) — giảm dùng emoji inline
- Viết tắt không có trong DIC. Lần đầu xuất hiện phải có dạng đầy đủ
- Câu mở đầu kiểu "Trong bối cảnh…", "Nhằm mục đích…" — vào thẳng vấn đề

## 7. Format mã page trong văn bản

Khi nhắc mã page trong văn bản, dùng 1 trong 3 format:

| Khi nào | Format | Ví dụ |
|---|---|---|
| Link nội bộ | `[[mã]]` | `Xem [[OPS-CS-SOP-001]]` |
| Inline reference | `code style` | "Mã `OPS-CS-SOP-001` đang Active" |
| Tham chiếu trong bảng | `mã` trực tiếp | `\| OPS-CS-SOP-001 \| Tiếp nhận yêu cầu \|` |

## 🔗 Tài liệu liên quan

→ ↑ {{ lark.wiki_root_url | wiki_link("Master Wiki Index") }} — canonical
→ [Skill 11 — Page Types](11-page-types.md) — {{ taxonomy.page_types|length }} type quyết định format title
→ [Skill 08 — INDEX & Numbering](08-index-and-numbering.md) — V{{ taxonomy.version | replace("v", "") }} code format
→ [Skill 01 — Page Format](01-page-format.md) — template general/MASTER/HUB
→ [docs/04 — Glossary](../docs/04-glossary.md) — thuật ngữ + DIC mapping
