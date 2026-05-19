# Skill 12 — Emergency Playbook (PBK)

PBK = page xử lý **sự cố / ngoại lệ / nhánh rẽ**. Trong V{{ taxonomy.version | replace("v", "") }} Execution-First, PBK nằm trong từng section thay vì space EMG riêng.

## Khi nào tạo PBK

- Tình huống ngoài luồng SOP bình thường
- Lỗi hệ thống / lệch dữ liệu / phản ánh khách hàng nghiêm trọng
- Khủng hoảng (truyền thông, bảo mật, vận hành)
- Cross-team escalation

KHÔNG dùng PBK cho:

- Lỗi nhỏ thuộc 1 bước SOP (gộp vào section "Lỗi thường gặp" của SOP)
- Tình huống chưa từng xảy ra trên thực tế (chờ event để document)

## Generic PBK template

```markdown
# {{ '{{' }} space }}-{{ '{{' }} section }}-PBK-{{ '{{' }} number }} {{ '{{' }} title }}

Loại tài liệu: PBK
Owner: {{ '{{' }} owner_role }}
Backup Owner: {{ '{{' }} backup_role }}
Reviewer: {{ '{{' }} reviewer_role }}
Hub Parent: {{ '{{' }} hub_code }}
Status: {{ policies.default_status }}
Version: v0.1
Last incident: __/2026 (nếu đã xảy ra)
Mức bảo mật: Internal

---

## 1. Triệu chứng (Symptoms)

Cách nhận biết sự cố đang xảy ra:

- ...
- ...

## 2. Mức độ nghiêm trọng

| Mức | Tiêu chí |
|---|---|
| 🟢 **Low** | Ảnh hưởng < 1 người / không lan rộng |
| 🟡 **Medium** | Ảnh hưởng 1 team / dưới 1 ngày |
| 🔴 **High** | Ảnh hưởng đa team / 1-7 ngày |
| ⛔ **Critical** | Toàn công ty / vận hành dừng |

## 3. Xử lý ngay (Immediate response, < 15 phút)

```
1. ...
2. ...
3. Notify: ...
```

## 4. Xử lý sâu (Deep fix, < 24h)

```
1. ...
2. ...
3. Update Master Index: status liên quan
```

## 5. Stakeholders & escalation

| Vai trò | Hành động | Khi nào |
|---|---|---|
| Owner | First response | Phát hiện |
| Backup Owner | Take over | Owner busy / không online |
| Section lead | Decision | Mức Medium+ |
| BOD | Sign-off | Mức Critical |

## 6. Tools cần

- Lark group: `<group name>`
- Lark Base: ...
- External: ...

## 7. Sau sự cố (Post-mortem)

```
☐ Ghi nhận timeline trong LOG
☐ Update MST/SOP nếu cần (luật / luồng đổi)
☐ Update PBK nếu phát hiện trigger mới
☐ Notify all impacted
☐ Add to backlink graph
```

## 8. Lịch sử sự cố

| Ngày | Mức | Nguyên nhân | Hành động đã làm | Cải thiện |
|---|---|---|---|---|
| __/__/2026 | 🟡 | ... | ... | Update [[<page>]] |

## 9. Link liên quan

### MASTER / POL liên quan
→ [[<mã>]] ...

### Process / SOP liên quan
→ [[<mã>]] PROC khi không có sự cố
→ [[<mã>]] SOP từng bước thông thường

### Trang điều hướng
→ ↑ [[{{ '{{' }} hub_code }}]] Hub Parent
→ ↑ {{ lark.wiki_root_url | wiki_link("Master Wiki Index") }}

## 10. Change Log
| Version | Ngày | Người sửa | Thay đổi |
|---|---|---|---|
| v0.1 | __/2026 | __ | Tạo mới |
```

## Ví dụ PBK generic (có thể adapt)

### Downtime hệ thống

```
Title: <SPACE>-<SECTION>-PBK-001 Downtime hệ thống <tên>
Triệu chứng:
- User không truy cập được
- Error 5xx tăng đột biến
Mức: tùy thời gian downtime
Xử lý ngay:
1. Notify channel #incident (< 5 phút)
2. Identify root cause: deploy mới? infra? DB?
3. Rollback nếu deploy
4. Escalate vendor nếu infra
```

### Data leak / breach

```
Title: BOD-GOV-PBK-001 Data leak / breach
Triệu chứng:
- Phát hiện token public, credentials commit nhầm
- Bên ngoài report thấy data
Mức: ⛔ Critical
Xử lý ngay:
1. Revoke tất cả token compromised (< 15 phút)
2. Notify BOD + Legal
3. Xác định scope leak
4. Notify users impacted (theo luật NĐ 13)
```

### Financial error

```
Title: INT-FIN-PBK-001 Settlement lệch lớn
Triệu chứng:
- Số đối soát chênh > X% / N triệu
Mức: 🔴 High
Xử lý ngay:
1. Freeze chuyển khoản
2. Reconcile theo POL sàn
3. Notify KTT + BOD
```

## Cross-team PBK

Khi sự cố ảnh hưởng ≥ 3 section:

- **PROC** đặt ở `INT-XFN-PROC-*` (Phối hợp liên phòng)
- **PBK** đặt ở section primary owner, nhưng link cross-section
- Notify channel `#incident` hoặc tương đương

## 🔗 Tài liệu liên quan

→ ↑ {{ lark.wiki_root_url | wiki_link("Master Wiki Index") }}
→ [Skill 01 — Page Format](01-page-format.md) — general template (PBK kế thừa)
→ [Skill 03 — Linking Rules](03-linking-rules.md) — cross-team escalation link
→ [Skill 11 — Page Types](11-page-types.md) — PBK type definition

<!-- TODO {{ company.short_name }}: thêm playbook đặc thù công ty (vd: livestream bùng đơn, sàn lỗi sync, khủng hoảng truyền thông) -->
