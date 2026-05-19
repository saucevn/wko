# Examples — 3 company shapes

Mỗi example là một fully-filled `company.config.yaml` + README giải thích quyết định.

## Choose your shape

| Example | Best for | SPACE count | MASTER |
|---|---|---|---|
| [`acme-foods-vietnam`](acme-foods-vietnam/) | F&B / Retail / E-commerce VN ~1000 đơn/ngày | 7 (V4.1 default) | 12 |
| [`tech-startup-singapore`](tech-startup-singapore/) | SaaS B2B / DevOps / Cloud-native | 6 custom (ENG + GTM) | 10 |
| [`minimal-3-space`](minimal-3-space/) | Solo / freelance / project < 10 người | 3 minimal | 4 |

## Quick test

Render từng example để xem output:

```bash
for ex in examples/*/; do
  echo "=== $ex ==="
  cp "$ex/company.config.yaml" company.config.yaml
  python3 scripts/validate_config.py --strict && python3 scripts/render.py --check
  rm company.config.yaml
done
```

## Decision flow

```
Công ty bạn:

├─ < 10 người, simple workflow?
│     └─→ minimal-3-space
│
├─ SaaS / DevOps / Cloud-native B2B?
│     └─→ tech-startup-singapore
│         (Custom ENG + GTM spaces)
│
├─ E-commerce / Retail / F&B với inventory?
│     └─→ acme-foods-vietnam
│         (Full V4.1 với OPS commerce-heavy)
│
└─ Khác?
      └─→ Start với V4.1 default (company.config.yaml.example)
          + adapt sections theo nghiệp vụ
```

## Customization checklist

Cho bất kỳ example:

- [ ] Đổi `company.name`, `short_name`, `industry`, `hq_country`
- [ ] Update `lark.*` với Lark tokens thật
- [ ] Adjust `taxonomy.spaces` nếu cần thêm/bớt
- [ ] Adjust `taxonomy.sections` theo nghiệp vụ thực
- [ ] Update `org.departments`
- [ ] Adjust `master_registry` — bỏ MASTER không phù hợp, thêm MASTER cần
- [ ] Update `pol_mst_rules.primary_owner_table` theo compliance regimes
- [ ] Update `lark_bases` với Base token thật
- [ ] Update `integrations.contributor_group_email` + webhook

## Contributing a new example

Cộng đồng welcome PR thêm example shape mới. Yêu cầu:

1. Folder `examples/<name>/` với `company.config.yaml` + `README.md`
2. Config dùng placeholder tokens (vd `bascnEXAMPLE...`), KHÔNG token thật
3. README giải thích:
   - Industry / quy mô / use case
   - Đặc điểm config khác V4.1 default
   - Fit / not-fit criteria
4. Validate: `python3 scripts/validate_config.py --strict` PASS
5. PR theo [CONTRIBUTING.md](../CONTRIBUTING.md)

Ý tưởng examples cần community input:
- Marketing agency (creator-driven)
- Healthcare / clinic
- Edu-tech / school
- Non-profit NGO
- Manufacturing (heavy industry)
- Restaurant chain (multi-location)
