# Skill 07 — Bảo vệ source code & Clean-slate trước soạn thảo

Đây là **nguyên tắc tối thượng** — mọi AI agent và người dùng phải tuân thủ.

## A. Pre-flight checklist (mỗi phiên soạn thảo)

Trước khi bắt đầu **bất kỳ** phiên soạn thảo Wiki:

```
1. git status              → working tree phải SẠCH
2. git pull origin main    → đồng bộ rules mới nhất
3. git fetch upstream      → check có update từ saucevn/wko không
4. Đọc CLAUDE.md           → nắm 2 nguyên tắc + index
5. Đọc skill liên quan     → theo loại task
6. python3 scripts/render.py → render template tươi
7. Xác định đích Lark      → Space → Section → Page (xem dist/docs/02)
8. Confirm: draft viết ở   → drafts/ (gitignored) HOẶC /tmp/
                             KHÔNG commit draft vào repo
```

**Nếu working tree không sạch** → `git stash` hoặc commit trước, KHÔNG bắt đầu phiên mới.

## B. Files PROTECTED — không sửa/xóa

| Path | Lý do | Khi nào được sửa |
|---|---|---|
| `company.config.yaml` | Chứa Lark token, gitignored | Local edit, không commit |
| `.env` | Chứa secrets, gitignored | Local edit, không commit |
| `sources/lark-exports/*.xml` | Snapshot Lark, sinh từ script | Chạy `scripts/pull_from_lark.py` |
| `sources/schemas/*.json` | Schema sinh tự động | Pull mới qua script |
| `sources/schemas/*.svg` | Sinh từ schema | Re-generate |
| `.github/workflows/*.yml` | CI rule | Qua PR có review |
| `scripts/*.py` | Tooling | Qua PR có review |
| `CLAUDE.md` | Quy tắc gốc | Qua PR có review (ảnh hưởng mọi agent) |
| `AGENTS.md` | Symlink → CLAUDE.md | Không sửa trực tiếp |
| `skills/*.md` (source) | Template gốc từ saucevn/wko upstream | Override trong section "Custom rules" thay vì sửa source |
| `dist/*` | Output render.py | **KHÔNG sửa** — sửa source rồi re-render |

## C. Quy tắc khi gọi `lark-cli`

```
✅ ĐƯỢC:
- Publish file .md đã review từ drafts/ hoặc dist/
- Publish content trực tiếp qua --content "..."
- Đọc/fetch nội dung từ Lark về local

❌ KHÔNG:
- lark-cli ... publish sources/schemas/*.json
- lark-cli ... publish scripts/*.py
- lark-cli ... publish company.config.yaml
- lark-cli ... publish *.xml
- Auto-publish trong CI mà không có review gate (workflow YAML)
```

## D. Quy tắc khi tổ chức folder

```
Root chỉ chứa:
✅ README.md, CLAUDE.md, AGENTS.md (symlink)
✅ LICENSE, SECURITY.md, CONTRIBUTING.md, MAINTAINERS.md, ROADMAP.md
✅ .gitignore, .env.example, company.config.yaml.example
✅ pyproject.toml, Makefile

KHÔNG được ở root:
❌ *.xlsx → phải ở sources/excel/ (gitignored hoặc trong fork)
❌ *_content.xml → phải ở sources/lark-exports/
❌ *.py → phải ở scripts/ hoặc tests/
❌ Draft *.md → phải ở drafts/ (gitignored)
❌ company.config.yaml, .env → gitignored, không commit
```

CI sẽ tự động kiểm tra qua `.github/workflows/ci-validate.yml`.

## E. .gitignore (đã setup)

Xem `.gitignore` ở root. Quan trọng nhất:

- `.env`, `company.config.yaml` — chứa secret, **NEVER commit**
- `dist/` — output render.py, regenerable
- `drafts/` — folder cho draft local, KHÔNG commit
- `.venv/`, `__pycache__/` — Python artifacts

## F. Khi nào được tạo file mới ở root

- Khi tạo skill/docs/source/script mới — nhưng phải đặt ĐÚNG folder
- Khi tạo `CHANGELOG.md` — OK (community file)
- Tạo file mới cấp root khác — cần PR review

## G. Khi nào được xóa file

- Xóa nội dung trong `drafts/` — luôn được
- Xóa file `*.tmp`, `*.bak` — luôn được
- Xóa file `dist/` — được, sẽ regenerate
- Xóa file trong `sources/`, `scripts/`, `skills/`, `docs/` — **CẤM** trừ khi có lệnh rõ ràng từ owner
- Xóa file `.github/workflows/*` — **CẤM** trừ qua PR có review

## H. Verify checklist sau mỗi commit

```bash
# 1. Root chỉ có file whitelist
ls -p / | grep -v /
# Phải thấy: README.md, CLAUDE.md, AGENTS.md, LICENSE, SECURITY.md,
#            CONTRIBUTING.md, MAINTAINERS.md, ROADMAP.md, .gitignore,
#            .env.example, company.config.yaml.example, pyproject.toml, Makefile

# 2. Không có draft trong skills/, docs/, sources/
find skills docs sources -name "*.draft.md" -o -name "*.tmp" -o -name "*.bak"
# Phải rỗng

# 3. Validate cấu trúc
python3 scripts/validate_structure.py
# Exit 0

# 4. Config valid
python3 scripts/validate_config.py --strict
# Exit 0

# 5. Render OK
python3 scripts/render.py --check
# Exit 0

# 6. Markdown lint
npx markdownlint-cli2 "skills/**/*.md" "docs/**/*.md" "*.md"
# Exit 0
```

## I. Khi cần phá quy tắc

Nếu **buộc phải** vi phạm 1 trong các quy tắc trên (ví dụ: thay file Excel với cùng tên), agent phải:

1. Hỏi owner xác nhận (xem `MAINTAINERS.md`)
2. Ghi rõ lý do trong commit message với prefix `BREAK-RULE:`
3. Update [docs/07-status-tracker.md](../docs/07-status-tracker.md) ghi nhận sự kiện

## J. Anti-leak: nếu lỡ commit secret

1. **Revoke token ngay lập tức** tại Lark Developer Console
2. Xoá khỏi git history qua `git-filter-repo`
3. Notify maintainers (security@saucevn.dev)
4. Tạo token mới + update local `.env`

Chi tiết: [SECURITY.md](../SECURITY.md).

---

*Nguyên tắc này quan trọng hơn mọi nguyên tắc khác. Khi conflict, lấy file này làm chuẩn.*
