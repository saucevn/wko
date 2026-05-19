# Example: Acme Foods Vietnam

Công ty hư cấu F&B Việt Nam mô phỏng case study thực tế:

- **Ngành:** F&B / Gia vị
- **Quy mô:** ~1000 đơn/ngày
- **Kênh:** TikTok Shop + Shopee + Web/FB
- **Pháp nhân:** 2 (Acme Foods Co. Ltd + Acme Distribution JSC)
- **Phòng ban:** 9 (HCNS, KT, KD, Kho, MKT, SX, CS, Live, IT)

## Đặc điểm config

### Taxonomy: V4.1 default đầy đủ
- 7 SPACE (SYS / GEN / INT / OPS / BOD / TMP / ARC)
- 13 TYPE (V4.1 với PROC + GDL)
- 24 sections (4 sections OPS-PIM/MFG/MKT/LIVE đặc thù F&B)

### 12 MASTER Registry
- HR: ma trận vị trí & JD, quy trình lương 18 bước
- Sales: SKU List, Settlement Definition, Fee Dictionary
- Operations: BOM per SKU, QC tiêu chuẩn, vị trí kho, chính sách CSKH
- Marketing: campaign tiêu chí, luật chơi livestream

### 7 Lark Base
- HCNS Database, OKR Tracker, Finance Tracker
- Channel Performance, SKU Catalog, Inventory, Customer Tickets

### POL primary owner table
Mapping 10 external policies → sections:
- TikTok/Shopee/Web/FB → OPS-ECM
- Luật Lao động + BHXH → INT-HR
- Luật ATTP / QCVN → OPS-MFG (đặc thù F&B)
- NĐ 13/2023 → BOD-GOV

## Cách dùng

```bash
# Copy config vào root repo
cp examples/acme-foods-vietnam/company.config.yaml ../../company.config.yaml

# Sửa Lark token thật của bạn (thay EXAMPLE values)
nano ../../company.config.yaml

# Validate + render
cd ../..
python3 scripts/validate_config.py --strict
python3 scripts/render.py

# Preview
cat dist/docs/02-wiki-architecture.md | head -30
```

## Khi nào fit công ty bạn?

✅ **Fit nếu:**
- F&B, retail, e-commerce Việt Nam
- 100-1000 đơn/ngày
- Multi-channel (TikTok/Shopee/Web)
- Cần MASTER cho SKU/BOM/Settlement/Fee
- Có sản xuất riêng (factory + QC)

❌ **Không fit:**
- Pure tech / SaaS (xem `tech-startup-singapore`)
- Quy mô < 50 đơn/ngày (xem `minimal-3-space`)
- B2B services (không có e-commerce)

## Customization gợi ý

Adapt cho công ty F&B khác:

1. Đổi `company.name`, `company.short_name`
2. Đổi `org.departments` theo phòng ban thực
3. Adjust `master_registry` — bỏ MASTER không phù hợp, thêm MASTER riêng
4. Add/remove sections trong `taxonomy.sections.OPS` (vd: thêm `OPS-RND` nếu có R&D team)
5. Update `lark_bases` với Base token thật

## 🔗 Related

- [docs-meta/ONBOARDING.md](../../docs-meta/ONBOARDING.md) — setup 30 phút
- [docs-meta/ARCHITECTURE.md](../../docs-meta/ARCHITECTURE.md) — kiến trúc template
- [examples/tech-startup-singapore/](../tech-startup-singapore/) — shape khác (SaaS)
- [examples/minimal-3-space/](../minimal-3-space/) — shape tối giản
