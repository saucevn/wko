# Publishing — Soạn + đẩy 1 page lên Lark

> Workflow chi tiết để publish 1 page mới hoặc update page có sẵn.

## Prerequisites

- [ ] Đã onboard xong (xem [ONBOARDING.md](ONBOARDING.md))
- [ ] `company.config.yaml` validated (`python3 scripts/validate_config.py --strict`)
- [ ] `dist/` có rendered output (`python3 scripts/render.py`)
- [ ] `lark-cli auth status` shows logged in

## 6-step publish flow

### Bước 1 — Soạn draft (local)

```bash
mkdir -p drafts          # đã gitignored
nano drafts/new-page.md
```

Template chọn theo type (xem [`dist/skills/01-page-format.md`](../dist/skills/01-page-format.md)):

- General (SOP/PROC/CHK/...): copy "General page template"
- MASTER (MST): copy "MST bridge" hoặc "MST standalone" template
- HUB: copy "HUB template"

Title H1 format `<mã> <tên>`. Mã chưa đặt ngay — sẽ resolve Bước 2.

### Bước 2 — Resolve mã page

Mở Master Wiki Index trên Lark (URL trong `company.config.yaml` `lark.wiki_root_url`).

1. Filter cột **Space** → section đích (vd `OPS-CS`)
2. Filter cột **Type** → type của page mới (vd `SOP`)
3. Sort cột **Page Code** descending → lấy NUMBER lớn nhất
4. NUMBER mới = max + 1 (zero-padded 3 digits)

Ví dụ: section `OPS-CS`, type `SOP`, max hiện `003` → mã mới `OPS-CS-SOP-004`.

Update H1 trong draft: `# OPS-CS-SOP-004 Tiếp nhận và phân loại yêu cầu`.

**KHÔNG tự đặt số.** Phải tra Master Index để tránh conflict.

### Bước 3 — Tạo node Lark

```bash
# Tạo node con dưới Hub Parent
HUB_PARENT_NODE="<node_token của HUB-001 section>"   # tra trên Lark

lark-cli wiki node create \
  --space-id "<wiki_root_token>" \
  --title "OPS-CS-SOP-004 Tiếp nhận và phân loại yêu cầu" \
  --obj-type docx \
  --parent-node-token "$HUB_PARENT_NODE"
```

Output sẽ in `node_token` + `obj_token` mới. Lưu lại.

### Bước 4 — Render draft (preview)

Nếu draft có Jinja2 placeholder (vd `{{ company.short_name }}`):

```bash
python3 scripts/render.py --file drafts/new-page.md
# Output → dist/drafts/new-page.md (đã substitute)
```

Nếu draft pure markdown (không có placeholder), skip step này.

### Bước 5 — Push content lên Lark

```bash
lark-cli docs update "<obj_token>" \
  --content drafts/new-page.md \
  --api-version v2
```

Verify:

```bash
lark-cli docs fetch "<obj_token>" --api-version v2 | head -30
```

### Bước 6 — Update Master Index + Hub Parent

```bash
# Auto: rebuild Master Index từ Lark tree
python3 scripts/rebuild_master_index.py --confirm

# Auto: update HUB Mục lục của section
python3 scripts/rebuild_hub_toc.py --section OPS-CS --confirm \
  --hub-obj-token "<obj_token of HUB-001 page>"
```

Hoặc manual: open Master Index trên Lark, add row mới với 12 cột required V4.1:

| Field | Value |
|---|---|
| Page Code | `OPS-CS-SOP-004` |
| Page Name | Tiếp nhận và phân loại yêu cầu |
| Space | OPS |
| Section | OPS-CS |
| Type | SOP |
| **Hub Parent** | `OPS-CS-HUB-001` |
| Owner | (role name) |
| Reviewer | (role name) |
| Status | ⬜ Draft |
| Version | v0.1 |
| Security Level | Internal |
| Link | (Lark URL) |

## Workflow code change

Nếu publish có liên quan code change (rare):

```bash
git add <changed files>
git commit -m "feat(<section>): publish OPS-CS-SOP-004 Tiếp nhận yêu cầu

Node: <node_token>
Obj: <obj_token>
Master Index updated."
git push origin main
```

**KHÔNG commit `drafts/`** (đã gitignored).

## Update existing page

```bash
# Sửa draft trong drafts/
nano drafts/existing-page.md

# Render
python3 scripts/render.py --file drafts/existing-page.md

# Push (cùng obj_token cũ)
lark-cli docs update "<obj_token>" --content drafts/existing-page.md

# Update version trong Master Index (vd v1.0 → v1.1)
# Add row vào Change Log section của page
```

## Rollback

### Sai content

```bash
# Restore từ backup
lark-cli docs update "<obj_token>" --content drafts/page-backup.md
```

### Sai mã page

KHÔNG xóa. Mark Deprecated trong Master Index:

```bash
# Update status = 📋 Deprecated, ghi note "Replaced by [[<mã đúng>]]"
# Tạo page mới với mã đúng
```

Sau 30 ngày Deprecated → move sang ARC space.

## CI integration (opt-in)

Khi `vars.LARK_INTEGRATION_ENABLED == 'true'`:

- `lark-rebuild-index.yml` chạy weekly Monday 2h UTC + manual trigger
- `lark-kpi-monthly.yml` chạy 1st of month → KPI report + content audit

Setup secrets (Settings → Secrets and variables → Actions):

- `LARK_APP_ID`
- `LARK_APP_SECRET`
- `COMPANY_CONFIG_B64` = `base64 -i company.config.yaml`

## Troubleshooting

### `lark-cli wiki node create` lỗi 403

→ Scope thiếu. Thêm `wiki:node:write` trong Lark Developer Console.

### `lark-cli docs update` lỗi 404 obj_token

→ Sai obj_token. Verify với `lark-cli wiki node get --node-token <node>`.

### Master Index lệch

→ Master Index trên Lark là canonical. Sync về local:

```bash
python3 scripts/rebuild_master_index.py --pull
```

### Render fail `UndefinedError`

→ `company.config.yaml` thiếu field. Check error message file:line, bổ sung.

## 🔗 Related

- [skill 05 — Publish workflow](../dist/skills/05-publish-workflow.md)
- [skill 01 — Page format](../dist/skills/01-page-format.md)
- [skill 08 — INDEX & numbering](../dist/skills/08-index-and-numbering.md)
- [ONBOARDING.md](ONBOARDING.md)
- [ARCHITECTURE.md](ARCHITECTURE.md)
