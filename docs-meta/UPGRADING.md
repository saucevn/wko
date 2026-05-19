# Upgrading — Sync upstream `saucevn/wko`

> Khi upstream release version mới, pull về fork private để hưởng improvements.

## Setup upstream remote (lần đầu)

```bash
cd <your-fork>
git remote add upstream https://github.com/saucevn/wko.git
git remote -v
# origin    https://github.com/<your-org>/wko-acme.git (fetch/push)
# upstream  https://github.com/saucevn/wko.git           (fetch)
```

## Check upstream updates

```bash
git fetch upstream
git log HEAD..upstream/main --oneline
```

Hoặc xem release tags:

```bash
git fetch --tags upstream
git tag -l | tail -5
```

Compare với current:

```bash
git describe --tags --abbrev=0       # current tag
```

## Pull update

### Strategy 1: Merge (recommended)

```bash
git fetch upstream
git checkout main
git merge upstream/main
# Resolve conflicts (xem section dưới)
git push origin main
```

### Strategy 2: Rebase (cleaner history)

```bash
git fetch upstream
git checkout main
git rebase upstream/main
# Resolve conflicts per-commit
git push origin main --force-with-lease    # cảnh báo: force push
```

### Strategy 3: Cherry-pick (selective)

```bash
# Chỉ pick commit cụ thể
git fetch upstream
git cherry-pick <commit-hash>
git push origin main
```

## Resolve conflicts

Khi conflict, ưu tiên theo file:

| File pattern | Strategy |
|---|---|
| `company.config.yaml` | **giữ local** (không commit nên không conflict thường) |
| `skills/*.md` | **ưu tiên upstream** (template gốc), trừ khi bạn custom |
| `docs/*.md` | **mix**: section "Custom rules" giữ local, các phần khác lấy upstream |
| `scripts/*.py` | **ưu tiên upstream** (logic chuẩn) |
| `.github/workflows/*` | **ưu tiên upstream** |
| `CLAUDE.md` | **ưu tiên upstream** trừ section "Custom rules" (cuối file) |
| `examples/` | **giữ local** nếu bạn thêm example riêng |
| `dist/` | gitignored, không cần resolve |

### Conflict tool

```bash
# Visualize conflict
git diff --diff-filter=U

# Resolve manually trong editor
nano skills/01-page-format.md

# Or accept upstream cho toàn file
git checkout --theirs skills/01-page-format.md

# Or keep local
git checkout --ours company.config.yaml

# Commit resolved
git add <files>
git commit
```

## Khi taxonomy thay đổi (vd v4.1 → v4.2)

### 1. Đọc CHANGELOG

```bash
git fetch upstream
git show upstream/main:CHANGELOG.md | head -50
```

Tìm section `## [v4.x.0]` — đặc biệt section **Breaking changes** + **Migration guide**.

### 2. Chạy migration tool (nếu có)

Upstream thường ship migration script:

```bash
python3 scripts/migrate_v41_to_v42.py --dry-run
python3 scripts/migrate_v41_to_v42.py --confirm
```

(Hiện tại v1.0 chỉ có `migrate_v4_to_v41.py`.)

### 3. Update `company.config.yaml`

Compare với new example:

```bash
diff company.config.yaml company.config.yaml.example | less
```

Thêm field mới required (theo CHANGELOG), giữ value local cho field có sẵn.

### 4. Re-validate + re-render

```bash
python3 scripts/validate_config.py --strict
python3 scripts/render.py --clean
```

### 5. Test publish 1 page sample

Trước khi rebuild Master Index, test với 1 page:

```bash
lark-cli docs fetch "<a sample obj_token>" --api-version v2  # snapshot trước
lark-cli docs update "<sample obj_token>" --content dist/skills/01-page-format.md  # test push
# Verify trên Lark UI
```

Nếu OK → proceed full rebuild:

```bash
python3 scripts/rebuild_master_index.py --confirm
python3 scripts/rebuild_hub_toc.py --confirm   # all sections
```

## Semver promise

| Version bump | Means |
|---|---|
| **PATCH** (v1.0.0 → v1.0.1) | Bug fix, doc clarification, dependency bump. No action needed. |
| **MINOR** (v1.0.0 → v1.1.0) | New feature (backward compatible). Optional adopt — config + render OK as-is. |
| **MAJOR** (v1.0.0 → v2.0.0) | Breaking change in config schema, render API, hoặc CLI behavior. **Migration required.** Read CHANGELOG + RFC discussion. |

## Avoid divergence

Pull upstream **regularly** (weekly recommended) thay vì gộp 6 tháng — conflicts dễ resolve hơn.

```bash
# Cron-style reminder (manual)
# Every Monday morning:
git fetch upstream
git log HEAD..upstream/main --oneline | head -10
```

## Rolling back upgrade

Nếu upstream upgrade gây issue:

```bash
# Find tag trước upgrade
git tag -l
git log --oneline | head -20

# Revert merge (nếu là merge commit)
git revert -m 1 <merge-commit-hash>

# Hoặc reset (nếu chưa push) — destructive
git reset --hard <last-good-commit>
```

Sau rollback, report issue lên `saucevn/wko`:

```bash
gh issue create --repo saucevn/wko \
  --title "[bug] v1.x.y upgrade fails với <symptom>" \
  --body "Migrated from v1.x.x. Error: ..."
```

## Sync example từ upstream

Nếu upstream thêm `examples/<new-shape>/`:

```bash
git fetch upstream
git checkout upstream/main -- examples/<new-shape>/
git add examples/<new-shape>/
git commit -m "chore: pull example <new-shape> from upstream"
```

## Contribute back

Nếu trong quá trình adapt bạn fix bug / cải tiến generic feature, contribute upstream:

1. Cherry-pick commit relevant ra branch fork
2. PR lên `saucevn/wko`

Xem [CONTRIBUTING.md](../CONTRIBUTING.md).

## 🔗 Related

- [README.md](../README.md)
- [ARCHITECTURE.md](ARCHITECTURE.md)
- [CONTRIBUTING.md](../CONTRIBUTING.md)
- [ROADMAP.md](../ROADMAP.md)
