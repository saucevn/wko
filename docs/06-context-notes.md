# Ngữ cảnh vận hành — {{ company.name }}

<!-- TODO {{ company.short_name }} owner: điền theo template dưới. Mục đích: AI agent + nhân viên mới hiểu quy mô + nhịp vận hành để viết SOP/PROC phù hợp. -->

## Quy mô

- **Số đơn / ngày:** <TODO>
- **Số khách hàng active / tháng:** <TODO>
- **Tỷ lệ hoàn:** <TODO>
- **Lifetime value trung bình:** <TODO>

## Kênh bán

<!-- TODO: liệt kê kênh + tỷ trọng doanh thu

Format đề xuất:

| Kênh | Tỷ trọng | Đặc điểm vận hành |
|---|---|---|
| TikTok Shop | 40% | Live + short video, peak weekend |
| Shopee | 30% | Coupon-driven, batch shipping |
| Web | 15% | High-margin, custom orders |
| Facebook | 10% | KOC + community |
| Offline | 5% | Showroom + distributor |
-->

## Nhịp vận hành

### Daily

<!-- TODO:
- Buổi sáng: ... (vd nhập tồn, check hàng về)
- Trưa: ...
- Chiều: ... (vd đóng gói, bàn giao vận chuyển)
- Cuối ngày: ... (vd đối soát đơn)
-->

### Weekly

<!-- TODO: weekly cadence (vd Monday review, Friday wrap-up) -->

### Monthly

<!-- TODO: monthly cadence (vd settlement, payroll, KPI review) -->

## Mùa vụ / Cao điểm

<!-- TODO:
- Cao điểm: ... (vd Tết, 11.11, 12.12, mùa du lịch)
- Thấp điểm: ...
- Đặc điểm vận hành mùa cao điểm:
  - Tăng X% volume
  - Cần Y nhân lực bổ sung
  - Cần Z action chuẩn bị (mua hàng trước, mở kho 2, ...)
-->

## Constraints / Limitations

<!-- TODO: hạn chế cần lưu ý khi viết SOP/PROC

Ví dụ:
- Kho chỉ 1 location → không multi-warehouse
- Vận chuyển chỉ Giao Hàng Nhanh + Viettel Post
- Tax: VAT 8% (giảm 2%) → phải kiểm tra ngày hiệu lực mỗi quý
-->

## KPI cốt lõi (V{{ taxonomy.version | replace("v", "") }})

Mỗi space có KPI riêng. Tổng quan:

{% for s in taxonomy.spaces %}
{% if s.code != "ARC" and s.code != "TMP" %}
- **{{ s.code }}** ({{ s.name }}) → <!-- TODO: KPI chính -->
{% endif %}
{% endfor %}

Chi tiết KPI: xem `DBD` pages trong từng section + Lark Base "OKR Tracker" ([05-lark-base-connections](05-lark-base-connections.md)).

## External dependencies

<!-- TODO: nhà cung cấp / partner / vendor cần biết

Format:
| Loại | Tên | Liên hệ | Khi dùng |
|---|---|---|---|
| Vận chuyển | GHN, Viettel Post | ... | Mỗi đơn ship |
| Kế toán | MISA | support@... | Daily entries |
| Bank | VCB, TCB | ... | Settlement |
-->

## 🔗 Tài liệu liên quan

→ ↑ {{ lark.wiki_root_url | wiki_link("Master Wiki Index") }}
→ [Tổng quan công ty](00-company-overview.md)
→ [Cơ cấu tổ chức](01-org-structure.md)
→ [Lark Base connections](05-lark-base-connections.md)
→ [Status tracker](07-status-tracker.md)
