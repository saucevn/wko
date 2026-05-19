# Architecture — wko template system

> Hiểu cách wko render template + publish lên Lark.

## 3 tầng

```
┌──────────────────────┐
│ Source (placeholder) │   skills/*.md, docs/*.md (có {{ company.name }})
└──────────┬───────────┘
           │ scripts/render.py
           ▼
┌──────────────────────┐
│ Dist (rendered)      │   dist/skills/, dist/docs/ (placeholder đã sub)
└──────────┬───────────┘
           │ scripts/rebuild_master_index.py + lark-cli docs update
           ▼
┌──────────────────────┐
│ Lark Wiki            │   live pages trên acme.sg.larksuite.com
└──────────────────────┘
```

## Components

### 1. `company.config.yaml`

Trái tim của template. **1 file** chứa toàn bộ config per client.

12 sections:

1. `company` — identity (name, short_name, industry)
2. `lark` — tenant URL, tokens, master_index
3. `taxonomy` — V4.1: 7 spaces, 13 page_types, sections
4. `hub_rules` — HUB-001 sticky, 4 branch patterns
5. `execution_first` — section formula, rejection rules
6. `pol_mst_rules` — POL external only, primary owner table
7. `org` — departments
8. `master_registry` — MASTER bắt buộc
9. `integrations` — Lark contributor group, bot webhook
10. `lark_bases` — Base connections
11. `master_index_fields` — 12 required + 5 recommended (V4.1)
12. `policies` — status values, defaults, ARC append-only

Validate: `python3 scripts/validate_config.py --strict`

### 2. Render engine — `scripts/render.py`

**Stack:** Python 3.11+ với Jinja2 (`StrictUndefined` — fail-fast).

Substitute placeholders:

```jinja2
{{ company.name }}                          → "Acme Foods"
{% for s in taxonomy.spaces %}- {{ s.code }} {{ s.icon }} {{ s.name }}
{% endfor %}                                → looped list
{{ lark.wiki_root_url | wiki_link("Wiki Root") }}  → "[Wiki Root](https://...)"
```

**Modes:**

| Flag | Mục đích |
|---|---|
| (default) | Render `skills/` + `docs/` → `dist/` |
| `--check` | CI dry-run, fail nếu placeholder thiếu config |
| `--file <path>` | Render 1 file (preview) |
| `--watch` | Re-render khi source thay đổi (dev) |
| `--clean` | Xóa `dist/` trước khi render |

**Skip subdirs:** `superpowers/`, `drafts/`, `.git/` (meta artifacts, không render).

**Custom filters:**

- `upper_dashed` — validate + uppercase page code
- `wiki_link(label)` — format `[label](url)`

### 3. Validators

| Script | Check |
|---|---|
| `validate_config.py` | Schema fields, Lark URL consistency, V4.1 core types, master_registry code format, POL primary owner refs |
| `validate_structure.py` | Required dirs (skills/docs/scripts/docs-meta), `.gitignore` chứa `dist/`/`.env`/`company.config.yaml` |
| `content_quality_audit.py` | V4.1 rules: POL-internal, missing PROC, missing Hub Parent, POL owner mismatch, invalid status |

### 4. Lark integration — `lark-cli only`

⚠️ **Repo này KHÔNG dùng `lark-oapi` (Python SDK).** Mọi Lark API qua `subprocess` gọi `lark-cli`.

Lý do:

- Single auth surface (`lark-cli auth login`)
- Match existing 20+ `lark-*` skills (lark-im, lark-doc, lark-wiki, lark-event, …)
- Không lock Python SDK version
- WebSocket event streaming có sẵn (`lark-cli event consume`)

**Pattern trong scripts:**

```python
from _common import require_lark_cli, require_lark_auth

def main():
    require_lark_cli()        # version >= 1.0.30
    require_lark_auth()       # logged in
    result = subprocess.run(
        ["lark-cli", "wiki", "node", "list", "--space-id", token, "--output", "json"],
        capture_output=True, text=True, check=True,
    )
    data = json.loads(result.stdout)
```

### 5. Config flow

```
.env (LARK_APP_ID/SECRET)
    │
    └─→ lark-cli auth login --as bot
            │
            └─→ subprocess lark-cli <subcommand>

company.config.yaml
    │
    └─→ scripts/_common.py load_config()
            │
            └─→ scripts/render.py (Jinja2)
            └─→ scripts/validate_config.py
            └─→ scripts/rebuild_master_index.py
            └─→ scripts/wiki_kpi_report.py
            └─→ ... (most scripts)
```

### 6. Authentication

| Scenario | Auth method |
|---|---|
| Local dev | `lark-cli auth login --as user` (browser flow, token trong OS keychain) |
| CI / bot | `LARK_APP_ID/SECRET` từ `.env` → `lark-cli auth login --as bot --app-id ... --app-secret ...` |
| Multi-tenant (v1.2+) | `LARK_PROFILE=acme` env var để switch profile |

### 7. Skills system

Repo có 2 lớp documentation:

| Lớp | Audience | Location |
|---|---|---|
| **skills/** | AI agents soạn Wiki | 12 file `01-page-format.md`, `02-writing-style.md`, ... |
| **docs/** | Người đọc cuối (manager, employee) | 11 file `00-company-overview.md`, ... |
| **docs-meta/** | Contributors (you!) | File này + ONBOARDING/PUBLISHING/UPGRADING |

AI agents đọc `CLAUDE.md` để biết khi nào dùng skill/doc nào.

### 8. CI workflows

7 workflows + 1 composite action. Xem chi tiết: [`.github/workflows/`](../.github/workflows/).

| Workflow | Trigger | Lark API? |
|---|---|---|
| ci-lint | PR + push | ❌ |
| ci-validate | PR + push | ❌ |
| ci-render | PR + push | ❌ |
| auto-index | push main (skills/docs) | ❌ |
| release | tag push `v*.*.*` | ❌ |
| lark-rebuild-index | weekly + manual | ✅ opt-in |
| lark-kpi-monthly | monthly | ✅ opt-in |

Opt-in flag: `vars.LARK_INTEGRATION_ENABLED = true` (default off để PR từ fork không fail).

## Design decisions

### Why placeholders visible in source?

Source `skills/01-page-format.md` chứa `{{ company.name }}` literal — không pre-rendered. Lý do:

- Easier sync upstream từ `saucevn/wko` (no merge conflicts về company-specific text)
- Contributors thấy ngay đâu là customizable
- 1 source serves N clients

### Why `StrictUndefined`?

Jinja2 raise nếu placeholder không tồn tại trong config. Lý do: bug catch sớm — placeholder typo `{{ comapny.name }}` không silent render thành empty.

### Why V4.1 default vs flexible?

Default đầy đủ V4.1 (7 SPACE, 13 TYPE, ...) để client mới có working setup ngay. Hoàn toàn customizable qua `company.config.yaml`.

### Why no `lark-oapi`?

Xem section "Lark integration" trên. Tóm tắt: single auth, version-stable, skill-compatible.

## Extending

### Thêm placeholder mới

1. Update `company.config.yaml.example` thêm field
2. Update `scripts/validate_config.py` thêm validation (optional)
3. Update `tests/conftest.py` `sample_config` fixture
4. Dùng `{{ new_field }}` trong skills/ hoặc docs/

### Thêm script mới

1. Tạo `scripts/foo.py`
2. Import từ `_common`: `from _common import load_config, require_lark_cli`
3. Add tests trong `tests/test_foo.py`
4. Update `Makefile` nếu muốn shortcut

### Thêm CI workflow

1. Tạo `.github/workflows/new-flow.yml`
2. Nếu cần Lark: dùng composite action `.github/actions/setup-lark-cli`
3. Nếu opt-in: guard với `if: vars.LARK_INTEGRATION_ENABLED == 'true'`

## 🔗 Related

- [README.md](../README.md)
- [docs-meta/ONBOARDING.md](ONBOARDING.md)
- [docs-meta/PUBLISHING.md](PUBLISHING.md)
- [docs-meta/UPGRADING.md](UPGRADING.md)
- [CONTRIBUTING.md](../CONTRIBUTING.md)
