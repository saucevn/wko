# Skill 04 — Trạng thái trang (V{{ taxonomy.version | replace("v", "") }})

Mỗi trang Wiki phải gán **1 trong {{ policies.page_status_values|length }} status** trong **Master Wiki Index** (`SYS-00-IDX-001`, canonical).

## 1. {{ policies.page_status_values|length }} trạng thái

{% for s in policies.page_status_values %}
- **{{ s }}**
{% endfor %}

Default cho page mới: **{{ policies.default_status }}**

### Ý nghĩa

| Status | Ý nghĩa |
|---|---|
| **⬜ Draft** | Đang viết, chưa hoàn thiện. Có thể có nội dung chưa đầy đủ, chưa qua review. |
| **🔄 Active** | Đã review, đang được dùng. Bắt buộc có Owner + Reviewer + đầy đủ metadata. |
| **📋 Deprecated** | Không còn dùng nhưng vẫn truy cập được. Có cảnh báo "Đã deprecated, dùng [[mã page mới]] thay". Sau 30 ngày → Archived. |
| **✅ Archived** | Đã move sang Space `ARC`. Chỉ giữ để tra cứu lịch sử. Mã page reserved — không tái sử dụng. |

## 2. Lifecycle

```
       Draft
         ↓ (review pass)
       Active
         ↓ (không dùng nữa)
      Deprecated
         ↓ (30 ngày)
       Archived
```

**Không skip state.** Active không nhảy thẳng Archived — phải qua Deprecated 30 ngày để team biết.

{% if policies.arc_append_only %}
> ⚠️ ARC append-only — không tái dùng mã page đã Archived.
{% endif %}

## 3. Tiêu chí chuyển status

### 3.1. Draft → Active

Page chỉ được Active khi đạt 18 tiêu chí:

```
☐ Có mã page đúng chuẩn [SPACE]-[SECTION]-[TYPE]-[NUMBER]
☐ Đã đăng ký trong Master Wiki Index
☐ Có Owner
☐ Có Backup Owner
☐ Có Reviewer
☐ Có Hub Parent (V4.1 — page phải nằm dưới HUB rõ ràng)
☐ Có Version
☐ Có Status
☐ Có Ngày hiệu lực
☐ Có Mức bảo mật
☐ Có TL;DR
☐ Có Input/Output rõ
☐ Có Checklist (nếu là SOP)
☐ Có Lỗi thường gặp (nếu là SOP vận hành)
☐ Có Cách xử lý khi sai
☐ Có Link về MASTER (nếu derive từ MASTER)
☐ Có Link template/form (nếu cần)
☐ Có Change Log
```

Thiếu bất kỳ tiêu chí nào → page giữ status Draft.

### 3.2. Active → Deprecated

Khi:
- Có page mới thay thế (link rõ trong cell "Note" của Master Wiki Index)
- Hoặc nội dung không còn áp dụng (vd: chính sách đã đổi)
- Hoặc Owner / Reviewer quyết định ngừng dùng

Action:
1. Đổi Status trong Master Wiki Index → `📋 Deprecated`
2. Thêm callout đầu trang Lark:

   ```
   > ⚠️ Trang này đã deprecated. Dùng [[<mã page mới>]] thay.
   > Trang sẽ Archived sau ngày <DD/MM/YYYY> (30 ngày kể từ hôm nay).
   ```

3. Notify nhóm bắt buộc đọc qua group Lark
4. Update Last Reviewed + Next Review = ngày archive

### 3.3. Deprecated → Archived

Sau 30 ngày deprecated:

1. Move trang Lark sang Space `ARC` (rename mã: `ARC-OLD-<TYPE>-<NUMBER>` hoặc giữ mã cũ + status Archived)
2. Đổi Status trong Master Wiki Index → `✅ Archived`
3. Mã page reserved — không tái sử dụng

## 4. Cách gán status

Status được set ở **2 chỗ** (đồng bộ thủ công):

1. **Master Wiki Index** (`SYS-00-IDX-001`, obj `{{ lark.master_index.obj_token }}`) — canonical, cột Status
2. **`docs/07-status-tracker.md`** (repo mirror) — cập nhật cùng lúc khi đổi status

**KHÔNG đặt status vào title trang Lark.** Title chỉ có `<mã> <tên>` (xem [skill 01](01-page-format.md)).

Nếu lệch giữa 2 nơi → Master Wiki Index thắng, sửa docs/07.

## 5. Hỏi ai khi chưa rõ trạng thái

| Space | Người duyệt Active |
|---|---|
{% for s in taxonomy.spaces %}| **{{ s.code }}** | {{ s.owner }} |
{% endfor %}

## 6. Review cycle

Active page phải có **Next Review** ≤ 1 năm kể từ Last Reviewed:

| Loại page | Tần suất review đề xuất |
|---|---|
| Dashboard/KPI (DBD) | Hàng tuần hoặc hàng tháng |
| SOP vận hành hằng ngày | Hàng tháng |
| Process (PROC) đa vai trò | Hàng quý |
| Chính sách (POL external) | Khi luật/sàn thay đổi |
| MST nội bộ | Hàng tháng/quý |
| Tài liệu onboarding (GEN) | Hàng quý |
| Văn hóa/giới thiệu công ty | 6 tháng/lần |
| Emergency Playbook (PBK) | Hàng quý hoặc sau mỗi sự cố |

Quá hạn Next Review → cảnh báo trong KPI Wiki dashboard. Xem [scripts/wiki_kpi_report.py](../scripts/wiki_kpi_report.py).

## 7. Quy trình review hàng tháng

```
Ngày 1–3:   Wiki Admin xuất danh sách trang cần review tháng này
Ngày 4–7:   Owner kiểm tra nội dung
Ngày 8–10:  Update version/change log nếu có sửa
Ngày 11–12: Reviewer kiểm tra
Ngày 13:    Active bản mới (nếu có thay đổi)
Ngày 14–15: Thông báo các thay đổi quan trọng
```

## 8. Kiểm tra trước Active

Owner + Reviewer xác nhận:

```
☐ Page đã có đủ 18 tiêu chí (§3.1)
☐ Backlink chiều ngược đã được cập nhật (nếu là MASTER) — xem skill 03
☐ Nội dung đã được Owner / Reviewer duyệt (nếu là MASTER/POL)
☐ Hub Parent đã set trong Master Index (V4.1)
☐ Last Updated + Next Review đã set
☐ Change Log có entry mới
```

## 🔗 Tài liệu liên quan

→ ↑ {{ lark.wiki_root_url | wiki_link("Master Wiki Index") }} — canonical
→ [Skill 01 — Page Format](01-page-format.md) — 18 tiêu chí Active
→ [Skill 03 — Linking Rules](03-linking-rules.md) — backlink chiều ngược cần update khi đổi status
→ [Skill 05 — Publish Workflow](05-publish-workflow.md) — sync Master Wiki Index + docs/07
→ [Skill 08 — INDEX & Numbering](08-index-and-numbering.md) — mã page V{{ taxonomy.version | replace("v", "") }}
→ [docs/07 — Status Tracker](../docs/07-status-tracker.md) — mirror trạng thái
