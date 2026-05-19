# Skill 08 — INDEX & Mã page (V{{ taxonomy.version | replace("v", "") }})

Quy tắc đánh mã page + cấu trúc Master Wiki Index.

## 1. Format mã

```
{{ taxonomy.page_code_format }}
```

Thành phần:

- **SPACE** — 1 trong {{ taxonomy.spaces|length }} space
- **SECTION** — phần sau dấu `-` của section.code (vd `OPS-CS` → suffix `CS`)
- **TYPE** — 1 trong {{ taxonomy.page_types|length }} type
- **NUMBER** — 3 chữ số, append-only theo Master Index

## 2. {{ taxonomy.spaces|length }} SPACE

{% for s in taxonomy.spaces %}
### {{ s.order }}_{{ s.code }} — {{ s.name }} {{ s.icon }}

**Owner:** {{ s.owner }}
**Mục đích:** {{ s.get("purpose") | default("") }}
{% if s.get("append_only") %}**Append-only:** không tái dùng mã đã archived.{% endif %}

**Sections ({{ taxonomy.sections.get(s.code, []) | length }}):**

{% for sec in taxonomy.sections.get(s.code, []) %}- `{{ sec.code }}` — {{ sec.name }}
{% endfor %}

{% endfor %}

## 3. {{ taxonomy.page_types|length }} TYPE

| Code | Tên | Câu hỏi thực thi |
|---|---|---|
{% for t in taxonomy.page_types %}| `{{ t.code }}` | {{ t.name }} | {{ t.get("question") | default("—") }} |
{% endfor %}

## 4. Master Index fields (V{{ taxonomy.version | replace("v", "") }})

**Required ({{ master_index_fields.required|length }} cột):**

{% for f in master_index_fields.required %}
{{ loop.index }}. {{ f }}
{% endfor %}

**Recommended (V{{ taxonomy.version | replace("v", "") }} mới):**

{% for f in master_index_fields.recommended %}
- {{ f }}
{% endfor %}

## 5. HUB rules (V{{ taxonomy.version | replace("v", "") }})

### HUB-{{ hub_rules.master_hub_number }} = Master entry (sticky)

Mỗi section có 1 HUB-{{ hub_rules.master_hub_number }} là cửa vào. **KHÔNG đổi mã.**

### HUB-002, 003, ... = nhánh

Tạo HUB nhánh chỉ khi:

- Mỗi nhánh có ≥ {{ hub_rules.branch_hub_min_pages }} page con
- Có owner / use-case khác nhau giữa nhánh
- Người dùng cần menu shortcut riêng cho từng nhóm

### 4 Branch patterns

{% for p in hub_rules.branch_patterns %}
#### {{ p.name }}

Ví dụ: {{ p.example }}
{% endfor %}

### Hub Parent rule

{% if hub_rules.hub_parent_required %}
Mọi page MUST có **Hub Parent** trong Master Index:

- Page là source of truth chung (Pattern B): `Hub Parent = HUB-{{ hub_rules.master_hub_number }}` Master
- Page thuộc 1 nhánh (Pattern A/C/D): `Hub Parent = HUB-002+` nhánh tương ứng
- Page cross-nhánh trong section: `Hub Parent = HUB-{{ hub_rules.master_hub_number }}` Master
{% endif %}

## 6. Quy trình tạo page mới

```
Vấn đề / nhu cầu thực tế
  → Xác định SPACE
  → Xác định SECTION
  → Xác định TYPE (1 trong {{ taxonomy.page_types|length }})
  → Xác định Hub Parent
  → Tra Master Index lấy NUMBER tiếp theo
  → Ghi row Master Index
  → Tạo page
  → Link hai chiều
```

Chi tiết: [skill 05 — Publish Workflow](05-publish-workflow.md).

## 7. POL vs MST (V{{ taxonomy.version | replace("v", "") }})

| Aspect | POL | MST |
|---|---|---|
| **Scope** | External only ({{ pol_mst_rules.pol_scope }}) | Internal |
| **Số trang section** | 1 owner duy nhất | Nhiều section dùng |
| **Required sections** | {{ pol_mst_rules.pol_required_sections }} mục | {{ pol_mst_rules.mst_bridge_required_sections }} mục (bridge) |
| **Cross-section** | {{ pol_mst_rules.cross_section_rule }} | {{ pol_mst_rules.cross_section_rule }} |

### POL primary owner table

| External Policy | Section primary owner |
|---|---|
{% for policy, section in pol_mst_rules.primary_owner_table.items() %}| {{ policy }} | `{{ section }}` |
{% endfor %}

## 8. Quy tắc KHÔNG được làm

```
❌ Không tự đặt số theo ý thích
❌ Không dùng số để thể hiện thứ tự menu
❌ Không tái dùng mã đã archived
❌ Không tạo page nếu chưa có Hub Parent
❌ Không tạo page nếu chưa ghi Master Index
❌ Không đổi mã page khi đổi tên
```

## 9. Khi vấn đề ngoài thực tế → Update Wiki

Bảng phân loại:

| Vấn đề thực tế | Update vào type |
|---|---|
| Sai / thiếu luật gốc | MST |
| Chính sách bắt buộc thay đổi (external) | POL |
| Chưa có luồng ai làm trước sau | PROC |
| Nhân viên không biết từng bước làm | SOP |
| Hay quên bước kiểm tra | CHK |
| Thiếu mẫu để điền | TMP |
| Có ngoại lệ, lỗi | PBK |
| Sai do không hiểu thuật ngữ | DIC |
| Sai do tone / style | GDL |
| Không nhìn được số | DBD |
| Cần ghi nhận quyết định | LOG |

## 🔗 Tài liệu liên quan

→ ↑ {{ lark.wiki_root_url | wiki_link("Master Wiki Index") }} — canonical
→ [Skill 01 — Page Format](01-page-format.md) — templates
→ [Skill 05 — Publish Workflow](05-publish-workflow.md) — 6 bước publish
→ [Skill 11 — Page Types](11-page-types.md) — {{ taxonomy.page_types|length }} type
→ [docs/02 — Wiki Architecture](../docs/02-wiki-architecture.md) — {{ taxonomy.spaces|length }} space chi tiết
→ [scripts/rebuild_master_index.py](../scripts/rebuild_master_index.py)
