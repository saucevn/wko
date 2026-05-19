# Example: Acme Cloud — SaaS B2B Singapore

Tech startup hư cấu mô phỏng SaaS B2B / DevOps:

- **Ngành:** SaaS B2B / DevOps platform
- **Quy mô:** ~20 nhân viên
- **HQ:** Singapore
- **Business model:** Subscription (ARR), không e-commerce, không inventory
- **Tech stack:** Cloud-native (AWS), SOC 2 / ISO 27001 compliance

## Đặc điểm config

### Custom 6-SPACE taxonomy (thay vì V4.1 default 7)

| SPACE | Mục đích | Thay đổi |
|---|---|---|
| SYS | Wiki OS | Standard |
| GEN | Company general + HR + Finance + Onboarding | Gộp INT vào GEN |
| **ENG** 💻 | Engineering (architecture/infra/security/QA/incident) | Mới, thay OPS |
| **GTM** 🚀 | Go-to-market (marketing/sales/CS/partnerships) | Mới, thay OPS commerce |
| BOD | Founders + governance | Standard |
| ARC | Archive | Standard |

**Bỏ:** OPS-CS, OPS-WH, OPS-INV, OPS-PIM, OPS-MFG, OPS-LIVE (commerce-heavy).

**Thêm:** ENG-ARCH, ENG-INFRA, ENG-SEC, ENG-INCIDENT, GTM-SALES, GTM-CS, ...

### POL primary owner table
6 compliance/legal:
- GDPR / PDPA → BOD-GOV
- SOC 2 / ISO 27001 → ENG-SEC
- AWS/GCP terms → ENG-INFRA
- Singapore employment law → GEN-HR
- GST/Tax → GEN-FIN
- Stripe terms → GEN-FIN

### 10 MASTER Registry
- ENG: product architecture, infra, security policies, on-call rotation
- GTM: pricing & discount matrix, SLA & support tiers
- GEN: role ladder & compensation bands, chart of accounts
- BOD: 3-year strategy
- SYS: Master Registry itself

### 5 Lark Base
- Engineering OKRs, Customer Pipeline, Incident Log
- Compliance Tracker, Hiring Pipeline

### 5 phòng ban (flat structure)
- Engineering, Growth, Customer Success, Ops (HR+Finance+Legal), Founders

## Cách dùng

```bash
cp examples/tech-startup-singapore/company.config.yaml ../../company.config.yaml
nano ../../company.config.yaml          # update Lark tokens
cd ../..
python3 scripts/validate_config.py --strict
python3 scripts/render.py
```

## Khi nào fit công ty bạn?

✅ **Fit nếu:**
- SaaS / DevOps / Platform startup
- ~10-50 nhân viên
- B2B subscription model
- Cloud-native (AWS/GCP/Azure)
- Cần compliance docs (SOC 2 / ISO / GDPR)
- Singapore / Vietnam / international

❌ **Không fit:**
- E-commerce với inventory (xem `acme-foods-vietnam`)
- Quy mô > 100 nhân viên (cần OPS sections phức tạp hơn)
- B2C consumer brand (cần Marketing/Live nặng)

## Customization gợi ý

1. Adjust `taxonomy.spaces` — nếu công ty bạn có Customer Support team lớn, tách ra space riêng
2. Adjust `master_registry` — bỏ "on-call rotation" nếu không có 24/7 SLA
3. Update `pol_mst_rules.primary_owner_table` theo compliance regimes thực tế
4. Add Lark Base cho stack riêng (vd PagerDuty integration, Datadog dashboards)

## So với V4.1 default

| Aspect | V4.1 default | Tech Startup |
|---|---|---|
| SPACE count | 7 | 6 |
| Section heavy | OPS (8 sections) | ENG (6) + GTM (4) |
| MASTER focus | F&B (BOM, SKU, settlement) | SaaS (architecture, SLA, pricing) |
| POL table | Sàn + ATTP + lao động VN | Compliance + cloud terms + SG law |
| Lark Base | Inventory + tickets + channel | OKRs + pipeline + incident + compliance |

## 🔗 Related

- [docs-meta/ONBOARDING.md](../../docs-meta/ONBOARDING.md)
- [examples/acme-foods-vietnam/](../acme-foods-vietnam/) — F&B shape
- [examples/minimal-3-space/](../minimal-3-space/) — tối giản
