# Skill 05 — Quy trình đẩy lên Lark Wiki (V{{ taxonomy.version | replace("v", "") }})

6 bước publish 1 page lên Lark.

## Bước 0 — Chuẩn bị

Trước khi publish:

```bash
# 1. Pre-flight (theo skill 07)
git status                              # clean
git pull origin main
git fetch upstream                      # check saucevn/wko updates

# 2. Render template với config hiện tại
python3 scripts/render.py

# 3. Verify lark-cli + auth
lark-cli --version                      # ≥ 1.0.30
lark-cli auth status                    # logged in
```

## Bước 1 — Soạn draft

Soạn page trong `drafts/<page-name>.md` (gitignored, KHÔNG commit):

```bash
mkdir -p drafts
# Copy template từ skill 01 (general/MASTER/HUB) → drafts/<page-name>.md
# Sửa theo nội dung
```

Verify draft theo format:

```bash
# Title format: <mã> <tên>
head -1 drafts/foo.md

# Metadata block
sed -n '/^---/,/^---/p' drafts/foo.md
```

## Bước 2 — Resolve mã page

Mở Master Wiki Index trên Lark: {{ lark.wiki_root_url }}/wiki/{{ lark.master_index.node_token }}

1. Filter cột Space → Section đích
2. Filter cột Type → đếm số NUMBER lớn nhất hiện có
3. NUMBER mới = max + 1 (3 digits, leading zero)

Ví dụ: section `OPS-CS`, type `SOP`, max hiện 003 → mã mới `OPS-CS-SOP-004`.

**KHÔNG tự đặt số.** Phải tra Master Index.

## Bước 3 — Tạo node Lark

```bash
# Tạo node con dưới wiki_root_token
lark-cli wiki node create \
  --space-id "{{ lark.wiki_root_token }}" \
  --title "<mã> <tên>" \
  --obj-type docx \
  --parent-node-token "<hub_parent_node_token>"
```

Output sẽ in `node_token` + `obj_token` mới. Lưu lại.

## Bước 4 — Push content lên Lark

```bash
# Render draft với placeholder substitute (nếu draft chứa Jinja2)
python3 scripts/render.py --file drafts/foo.md

# Push content
lark-cli docs update "<obj_token>" --content drafts/foo.md --api-version v2
```

Verify:

```bash
lark-cli docs fetch "<obj_token>" --api-version v2 | head -50
```

## Bước 5 — Update Master Wiki Index

Add row mới vào Master Index với 12 cột bắt buộc V{{ taxonomy.version | replace("v", "") }}:

{% for f in master_index_fields.required %}
- {{ f }}
{% endfor %}

(+ optional recommended fields V{{ taxonomy.version | replace("v", "") }}:)

{% for f in master_index_fields.recommended %}
- {{ f }}
{% endfor %}

Cách:

```bash
# Tự động (recommend)
python3 scripts/rebuild_master_index.py --confirm

# Hoặc thủ công: mở Master Index trên Lark, add row
```

Master Index obj: `{{ lark.master_index.obj_token }}`

## Bước 6 — Backlink + notify

Update:

1. **Hub Parent** — thêm link mới vào HUB-{{ hub_rules.master_hub_number }} của section
2. **MASTER liên quan** — nếu page mới link tới MASTER nào, update "Trang đang dùng" trong MASTER đó
3. **Notify** — thông báo trong group Lark `{{ integrations.contributor_group_email }}`

```bash
# Auto-update HUB TOC
python3 scripts/rebuild_hub_toc.py --section <SPACE>-<SECTION> --confirm
```

## Commit log

Sau publish, commit local nếu có thay đổi code/config:

```bash
git add <changed files>
git commit -m "feat(<section>): publish <mã page>

Node: <node_token>
Master Index updated."
```

**KHÔNG commit `drafts/`** (đã gitignored).

## Rollback nếu sai

```bash
# Nếu publish nhầm content
lark-cli docs update "<obj_token>" --content drafts/foo-correct.md

# Nếu mã page sai (rất hiếm) — KHÔNG xóa, mark Deprecated trong Master Index
# Tạo page mới với mã đúng + note "Replaces [[<mã sai>]]"
```

## Troubleshooting

### `lark-cli` lỗi 403 / scope

→ Thêm scope qua Lark Developer Console + `lark-cli auth login --as user` lại.

### Master Index conflict (lệch)

→ Master Index trên Lark là canonical. Sửa Lark trước, sau đó `python3 scripts/rebuild_master_index.py --pull` để sync về local `docs/07-status-tracker.md`.

### Render fail với `UndefinedError`

→ `company.config.yaml` thiếu field. Check error message file:line, bổ sung config.

## 🔗 Tài liệu liên quan

→ ↑ {{ lark.wiki_root_url | wiki_link("Master Wiki Index") }} — canonical
→ [Skill 01 — Page Format](01-page-format.md) — template
→ [Skill 04 — Page Status](04-page-status.md) — trạng thái sau publish
→ [Skill 07 — Source Protection](07-source-protection.md) — pre-flight checklist
→ [Skill 08 — INDEX & Numbering](08-index-and-numbering.md) — V{{ taxonomy.version | replace("v", "") }} code
→ [scripts/rebuild_master_index.py](../scripts/rebuild_master_index.py)
→ [scripts/rebuild_hub_toc.py](../scripts/rebuild_hub_toc.py)
