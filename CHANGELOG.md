# Changelog

All notable changes documented here. Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

Versions follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html):

- **MAJOR** (v2.x.x): breaking change config schema, render API
- **MINOR** (v1.x.0): new feature (backward compatible)
- **PATCH** (v1.0.x): bug fix, doc, dependency bump

## [Unreleased]

(In development — see [ROADMAP.md](ROADMAP.md) for v1.1+ plans.)

## [v1.0.0] — 2026-05-19

🎉 **First public release.** Production-ready Lark Wiki Company OS template.

### Added

#### Bootstrap (M1)
- `LICENSE` (MIT, Copyright © 2026 saucevn)
- `README.md` với Quick start 10 phút
- `SECURITY.md`, `CONTRIBUTING.md`, `MAINTAINERS.md`, `ROADMAP.md`
- `.env.example`, `pyproject.toml`, `Makefile`
- `.gitignore` với secret protection (`.env`, `company.config.yaml`, `dist/`)

#### Config schema V4.1 Execution-First (M2)
- `company.config.yaml.example` — 12 sections, fully commented
- 7 SPACE default: `SYS`, `GEN`, `INT`, `OPS`, `BOD`, `TMP`, `ARC`
- 13 TYPE default: `MST`, `SOP`, `CHK`, `TMP`, `HUB`, `PBK`, `DBD`, `DIC`, `POL`, `LOG`, `IDX`, **`PROC`**, **`GDL`**
- 24 default sections (per V4.1 blueprint)
- `master_index_fields`: 12 required + 5 recommended (Hub Parent, Source Type/URL, Effective Date, Review Cadence, Impacted Pages)
- `pol_mst_rules`: POL external-only, primary owner table per section
- `hub_rules`: HUB-001 sticky, 4 branch patterns (A/B/C/D)
- `execution_first`: section formula + rejection rules
- `scripts/validate_config.py` — 10 unit tests

#### Render engine (M3)
- `scripts/render.py` — Jinja2 với `StrictUndefined` (fail-fast on missing placeholders)
- 6 modes: default, `--check`, `--file`, `--watch`, `--clean`, `--config`
- Custom filters: `upper_dashed`, `wiki_link`
- Skip meta subdirs: `superpowers/`, `drafts/`, `.git/`
- `scripts/generate_index.py` — auto skills/docs README
- `scripts/validate_structure.py` — folder + gitignore lint
- 11 unit tests + 1 watch smoke test (interactive marker)

#### Content templated (M4 + M5)
- **12 skills** (V4.1 placeholders, strip Thích Cay):
  - 01 page-format (3 templates: general/MASTER/HUB)
  - 02 writing-style (title format per type)
  - 03 linking-rules (Hub Parent + 4 link symbols)
  - 04 page-status (4 lifecycle states)
  - 05 publish-workflow (6 bước via lark-cli)
  - 06 excel-to-wiki
  - 07 source-protection
  - 08 index-and-numbering (V4.1 7 SPACE + 13 TYPE + HUB rules + POL primary owner)
  - 09 contributing-workflow (Phase 1 manual + Phase 2 bot)
  - 10 master-registry
  - 11 page-types (13 TYPE với execution-first questions)
  - 12 emergency-playbook (PBK generic template)
- **11 docs** mirror cho non-skill audience:
  - 00 company-overview (skeleton)
  - 01 org-structure (render từ org.departments)
  - 02 wiki-architecture (loop 7 SPACE)
  - 03 permissions, 04 glossary, 05 lark-base-connections, 08 contributing
  - 06 context-notes, 07 status-tracker (skeletons)
  - 09 master-registry, 10 page-types-taxonomy

#### Scripts migration (M6) — `lark-cli only`
**Drops `lark-oapi` Python SDK.** Mọi Lark API qua `subprocess` calls `lark-cli`.

- `_common.py` — `load_config`, `require_lark_cli`, `require_lark_auth`, `version_lt` (13 tests)
- `init_company.py` — Interactive wizard 12 questions (6 tests)
- `render.py` — Jinja2 engine (7 tests + 1 watch)
- `validate_config.py` — V4.1 schema (10 tests)
- `validate_structure.py` — folder lint (4 tests)
- `generate_index.py` — auto README (3 tests)
- `wiki_navigator.py` — recursive wiki tree walk
- `pull_from_lark.py` — snapshot tree + content
- `rebuild_master_index.py` — V4.1 Hub Parent column (4 tests)
- `rebuild_hub_toc.py` — HUB-001 sticky, 4 branch patterns (6 tests)
- `sync_index_contributed_column.py` — git log → Contributed By
- `wiki_kpi_report.py` — execution-first compliance metric (9 tests)
- `content_quality_audit.py` — POL/MST + missing-PROC rules (7 tests)
- `wiki_reviewer_bot.py` — **REWRITE** với `lark-cli event consume` NDJSON stream + Anthropic API (5 tests)
- `migrate_v4_to_v41.py` — COM→GEN, EMG→ARC-OLD mapping (8 tests)
- `build_backlink_graph.py` — parse `[[mã]]` refs, JSON + summary + SVG

#### CI workflows (M7)
- **3 generic** (no Lark deps): `ci-lint.yml`, `ci-validate.yml`, `ci-render.yml`
- **2 automation**: `auto-index.yml` (push main), `release.yml` (tag v*.*.*)
- **2 Lark-dependent** (opt-in via `vars.LARK_INTEGRATION_ENABLED`):
  - `lark-rebuild-index.yml` (weekly + manual)
  - `lark-kpi-monthly.yml` (1st of month)
- **Composite action** `.github/actions/setup-lark-cli` (install binary + min-version check)
- **Issue templates** (3): bug_report, feature_request, company_onboarding
- **PR template** với anti-leak checks
- **Dependabot** weekly (pip + github-actions)

#### Examples (M8)
- `examples/acme-foods-vietnam/` — F&B Việt, 1000 đơn/ngày, 7 SPACE + 12 MASTER
- `examples/tech-startup-singapore/` — SaaS B2B, 6 custom SPACE (ENG + GTM)
- `examples/minimal-3-space/` — Solo/freelance, 3 SPACE tối giản
- `examples/README.md` — decision flow + comparison table

#### Meta docs (M8)
- `docs-meta/ONBOARDING.md` — Setup 30 phút 8 bước
- `docs-meta/ARCHITECTURE.md` — 3-tier system, design decisions, extending
- `docs-meta/PUBLISHING.md` — 6-step publish workflow
- `docs-meta/UPGRADING.md` — sync upstream + conflict resolution + semver
- `CLAUDE.md` — AI agent rules với placeholder cho downstream clients

#### Sources (M8)
- `sources/schemas/lark_wiki_schema.json` (generic, V4.1 fields)
- `sources/schemas/lark_wiki_erd.svg`
- `sources/schemas/wiki_nodes_response.json` (sample API response)

#### E2E tests (M9)
- `tests/test_e2e.py` — full pipeline với 3 examples (7 tests)

### Stats v1.0.0

- **47 commits** trên `main`
- **94 tests** PASS (87 unit + 7 E2E + 1 interactive deselected in CI)
- **43% coverage** trên `scripts/`
- **23 source `.md`** files (12 skills + 11 docs)
- **17 Python scripts**
- **7 CI workflows + 1 composite action**
- **3 release tags**: `v0.1.0-alpha`, `v0.5.0-beta`, `v0.9.0-rc`, **`v1.0.0`**

### Migrated from

- Private repo "Thích Cay Company OS" V4 → public template V4.1
- `lark-oapi` (Python SDK) → `lark-cli` (system binary via subprocess)
- Hardcoded Lark tokens → `company.config.yaml` config-driven
- COM space (V4) → GEN space (V4.1)
- EMG space (V4) → ARC-OLD archive (V4.1)
- 11 TYPE → 13 TYPE (thêm PROC + GDL execution-first)

### Notes

- Repo public MIT — Copyright © 2026 [saucevn](https://github.com/saucevn)
- Maintained at <https://github.com/saucevn/wko>
- Distilled from production Thích Cay private repo. Mọi nội dung công ty cụ thể đã được strip.
- Default taxonomy V4.1 Execution-First — adapt per client qua `company.config.yaml`.

## [v0.9.0-rc] — 2026-05-19

Release candidate. Feature complete (M1-M7). Awaiting examples + meta docs.

## [v0.5.0-beta] — 2026-05-19

Beta. Content templated (M1-M5). No CI yet.

## [v0.1.0-alpha] — 2026-05-19

Alpha. Render engine working (M1-M3). No content yet.
