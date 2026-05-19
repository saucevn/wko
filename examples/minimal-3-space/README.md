# Example: Minimal Co — 3-SPACE tối giản

Cấu hình **tối giản nhất** — phù hợp solo founder / freelancer / project nhỏ:

- **Ngành:** Generic (không specialize)
- **Quy mô:** 1-10 người
- **Phòng ban:** All-in-one (1 người làm hết)
- **Mục đích:** Get started fast, expand sau khi grow

## Đặc điểm config

### 3-SPACE (vs V4.1 default 7)

| SPACE | Mục đích |
|---|---|
| **SYS** | Wiki Operating System (rules + index) |
| **OPS** | Mọi vận hành (daily, client, finance gộp vào 1 space) |
| **ARC** | Archive |

**Bỏ:** GEN (gộp vào OPS), INT (gộp vào OPS), BOD (Owner = 1 người), TMP (dùng từ saucevn/wko template gốc).

### 4 Sections

- SYS-00 (Wiki OS)
- OPS-MAIN (vận hành chung)
- OPS-CLIENT (khách hàng / dự án)
- OPS-FIN (tài chính)
- ARC-OLD

### POL primary owner table — Vietnamese context
- Luật Thuế cá nhân / hộ kinh doanh → OPS-FIN
- Sàn / platform terms → OPS-CLIENT

### 4 MASTER Registry
- SYS Master Registry
- OPS-MAIN quy trình daily
- OPS-CLIENT onboarding
- OPS-FIN thu chi

### `publish_requires_review: false`

Solo mode — không cần review bước Active.

### 2 Lark Base
- Daily Log (công việc + thu chi)
- Client Tracker (active clients)

## Cách dùng

```bash
cp examples/minimal-3-space/company.config.yaml ../../company.config.yaml
nano ../../company.config.yaml          # update Lark tokens
cd ../..
python3 scripts/validate_config.py --strict
python3 scripts/render.py
```

## Khi nào fit?

✅ **Fit nếu:**
- Solo founder / freelancer
- Project nhỏ < 10 người
- Side project / startup pre-PMF
- Cần Wiki để remember workflow, không cần full company OS
- Sẵn sàng expand sang V4.1 default sau

❌ **Không fit:**
- Team đã có 10+ người (nên dùng V4.1 default hoặc `tech-startup-singapore`)
- Cần HR/Finance/Compliance docs riêng (xem `acme-foods-vietnam`)
- E-commerce nhiều kênh (xem `acme-foods-vietnam`)

## Expansion path

Khi team grow, migrate sang V4.1 default:

1. Add SPACE mới: GEN, INT, BOD
2. Move pages `OPS-MAIN` thường thường → SOP/PROC trong section phù hợp
3. Move `OPS-FIN` → INT-FIN
4. Move `OPS-CLIENT` → giữ trong OPS (vd OPS-CS hoặc OPS-ACCT)
5. Bổ sung `master_registry` đầy đủ
6. Add `org.departments`

Hoặc clean up: drop old 3-SPACE, copy `acme-foods-vietnam` hoặc `tech-startup-singapore` làm base.

## So với 2 examples khác

| | Minimal | Acme F&B | Tech SaaS |
|---|---|---|---|
| SPACE | 3 | 7 | 6 |
| Sections | 4 | 24 | 17 |
| MASTER | 4 | 12 | 10 |
| Lark Bases | 2 | 7 | 5 |
| Phòng ban | 1 (All) | 9 | 5 |
| Review quorum | 0 (solo) | 1 | 1 |

## 🔗 Related

- [docs-meta/ONBOARDING.md](../../docs-meta/ONBOARDING.md)
- [examples/acme-foods-vietnam/](../acme-foods-vietnam/) — F&B shape
- [examples/tech-startup-singapore/](../tech-startup-singapore/) — SaaS shape
