# Trạng thái Wiki — {{ company.name }}

> Snapshot trạng thái page Wiki. **Master Wiki Index trên Lark là canonical.** File này là mirror, có thể stale.
>
> Auto-update qua `scripts/wiki_kpi_report.py` (chạy hàng tháng qua CI workflow `lark-kpi-monthly.yml`).

## Theo trạng thái

| Trạng thái | Count |
|---|---|
{% for s in policies.page_status_values %}| {{ s }} | (TBD — chạy `python3 scripts/wiki_kpi_report.py`) |
{% endfor %}

## Theo SPACE

| SPACE | Total pages | Active | Draft | Deprecated | Archived |
|---|---|---|---|---|---|
{% for s in taxonomy.spaces %}| `{{ s.code }}` | (TBD) | (TBD) | (TBD) | (TBD) | (TBD) |
{% endfor %}

## Execution-First Compliance (V{{ taxonomy.version | replace("v", "") }})

Theo công thức execution-first ({% for r in execution_first.section_formula.required %}{{ r }}{% if not loop.last %}, {% endif %}{% endfor %}):

| Section | HUB | MST | PROC | SOP | CHK | TMP | PBK | Compliance |
|---|---|---|---|---|---|---|---|---|
{% for space_code, sections in taxonomy.sections.items() %}{% for sec in sections %}| `{{ sec.code }}` | (TBD) | (TBD) | (TBD) | (TBD) | (TBD) | (TBD) | (TBD) | (TBD) |
{% endfor %}{% endfor %}

> Compliance = % section đạt đủ 7 type required execution-first.

## Pages quá hạn review

(TBD — chạy `python3 scripts/wiki_kpi_report.py --overdue-review`)

## Pages thiếu Hub Parent (V{{ taxonomy.version | replace("v", "") }})

(TBD — chạy `python3 scripts/content_quality_audit.py --check hub-parent`)

## Master Registry status

{{ master_registry | length }} MASTER bắt buộc cấu hình:

{% for m in master_registry %}
- `{{ m.code }}` — {{ m.name }} (owner: {{ m.owner }}) — (TBD: status thật trên Lark)
{% endfor %}

## Migration history

{% if taxonomy.version == "v4.1" %}
Hiện đang V4.1 Execution-First. Migration history (nếu có):

- (TBD) V3 → V4: <ngày>, <person>
- (TBD) V4 → V4.1: <ngày>, <person>
{% endif %}

## Cách update

### Tự động (recommended)

```bash
# Pull snapshot từ Lark
python3 scripts/pull_from_lark.py --all

# Generate report
python3 scripts/wiki_kpi_report.py --output docs/07-status-tracker.md
```

### Thủ công

Update bảng "Theo SPACE" + "Compliance" bằng cách:

1. Mở Master Index trên Lark
2. Group by Space + Status
3. Đếm rows
4. Cập nhật bảng

## 🔗 Tài liệu liên quan

→ ↑ {{ lark.wiki_root_url | wiki_link("Master Wiki Index") }} — canonical
→ [Skill 04 — Page Status](../skills/04-page-status.md)
→ [scripts/wiki_kpi_report.py](../scripts/wiki_kpi_report.py)
→ [scripts/content_quality_audit.py](../scripts/content_quality_audit.py)
→ [scripts/pull_from_lark.py](../scripts/pull_from_lark.py)
