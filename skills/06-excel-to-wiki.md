# Skill 06 — Chuyển nội dung từ Excel sang Wiki

File Excel nguồn (HR, KPI, Lương, Hoá đơn, ...) chứa nội dung thô. Khi viết trang Wiki tương ứng, **không copy nguyên file** — tóm tắt + link.

## Quy tắc

1. **Không copy nguyên bảng Excel vào Wiki.** Wiki tối đa 2 trang A4.
2. **Tóm tắt nội dung quan trọng** thành step-by-step theo [skill 01](01-page-format.md).
3. **Đính kèm link file Excel** trong section `🔗 Tài liệu liên quan`:

   ```
   → 📥 [QuyTrinhLuong_2026.xlsx](../sources/excel/QuyTrinhLuong_2026.xlsx) — file chi tiết 18 bước
   ```

4. **Link đến Lark Base** (nếu có tracking dữ liệu real-time).

## Quy trình

### Bước 1 — Đọc file Excel

```bash
python3 -c "
import openpyxl
wb = openpyxl.load_workbook('sources/excel/<file>.xlsx', data_only=True)
for sheet in wb.sheetnames:
    print(f'=== {sheet} ===')
    ws = wb[sheet]
    for row in ws.iter_rows(max_row=10, values_only=True):
        print(row)
"
```

### Bước 2 — Identify nội dung cần đưa lên Wiki

Phân loại:

- **A. Quy trình step-by-step** → đưa vào "📋 Chi tiết từng bước" (`type: SOP`) hoặc luồng đa vai trò (`type: PROC`, mới V4.1)
- **B. Bảng tham chiếu (mapping, list)** → đưa vào trang Wiki nếu < 20 dòng (`type: MST`). > 20 dòng → đẩy lên Lark Base + link
- **C. Số liệu mẫu, ví dụ** → đưa vào ⚠️ Lưu ý quan trọng
- **D. Form / Template Excel** → KHÔNG đưa lên Wiki. Để ở `sources/excel/` + link
- **E. Dashboard / KPI metrics** → tạo Lark Base + link, page Wiki chỉ là `type: DBD` mô tả

### Bước 3 — Xác định SPACE → SECTION → TYPE → NUMBER

Mở `SYS-00-IDX-001 Master Wiki Index` → tra Section đích → lấy NUMBER kế tiếp ([skill 05 §Bước 0](05-publish-workflow.md), [skill 08](08-index-and-numbering.md)).

### Bước 4 — Viết draft theo template general

Tạo draft tại `drafts/<page-name>.md`. Title H1 format `<mã> <tên>` (xem [skill 01](01-page-format.md)):

```markdown
# OPS-INV-SOP-004 Xuất hóa đơn hàng ngày

Loại tài liệu: SOP
Owner: KTTH (Kế toán Tổng hợp)
Backup Owner: KTT
Reviewer: KTT
Hub Parent: OPS-INV-HUB-001
Status: ⬜ Draft
Version: v0.1
Last updated: __/2026
Next review: __/__/2026
Mức bảo mật: Internal

---

## 1. TL;DR
Quy trình xuất HĐ hàng ngày — 3 cụm việc: (1) nhận file đơn từ sàn, (2) mapping qua MISA theo `OPS-INV-MST-001`, (3) đối soát cuối ngày.

## 4. Input cần có
- File đơn CSV từ các sàn
- Mapping rule trong [[OPS-INV-MST-001]]

## 5. Output bắt buộc
- HĐ xuất trong MISA ≤ 18:00 cùng ngày
- Báo cáo lệch (nếu có) gửi KTT

## 8. Hướng dẫn chi tiết từng bước

### Bước 1: Nhận file đơn từng sàn (08:00-09:00)
- **Tool:** Sàn dashboard
- **Output:** N file CSV trong folder `MISA/đơn-ngày-DD-MM`
- **Chuẩn đúng:** mỗi sàn 1 file, đặt tên `<sàn>_<DD-MM>.csv`

## 11. Link hệ thống

### MASTER liên quan
→ [[OPS-INV-MST-001]] MASTER Settlement Definition
→ [[OPS-INV-MST-002]] MASTER Fee Dictionary

### Template/Form
→ 📥 [QuyTrinh_HoaDon_2026.xlsx](../sources/excel/QuyTrinh_HoaDon_2026.xlsx) — chi tiết mapping

### Dashboard liên quan
→ [[OPS-INV-DBD-001]] Dashboard Settlement
→ 📊 [Lark Base — Tracking hóa đơn ngày](...) — track real-time

### Trang điều hướng
→ ↑ [[OPS-INV-HUB-001]] Hóa đơn & đối soát — Tổng quan
→ ↑ {{ lark.wiki_root_url | wiki_link("Master Wiki Index") }}

## 12. Change Log
| Version | Ngày | Người sửa | Thay đổi |
|---|---|---|---|
| v0.1 | __/2026 | __ | Tạo mới từ Excel |
```

### Bước 5 — Publish theo [skill 05](05-publish-workflow.md)

## Mapping Excel ↔ trang Wiki (template guide)

Khi mapping file Excel sang page Wiki, sử dụng bảng template sau:

| File Excel | Type Wiki phù hợp | SPACE/SECTION đích | Note |
|---|---|---|---|
| Quy trình step-by-step | SOP / PROC | OPS-* tương ứng | < 2 trang A4 |
| Bảng JD nhân sự | MST | INT-HR | + link Excel gốc |
| Bảng KPI / OKR | DBD | INT-O3K | + Lark Base link |
| Quy trình lương | MST + SOP con | INT-FIN | MASTER lương + SOP từng bước |
| Phối hợp liên phòng | PROC | INT-XFN | Luồng đa vai trò |
| Template form | TMP | TMP-GEN | Mẫu chung |
| File data lịch sử | (không Wiki) | giữ `sources/excel/` | Tham chiếu, không Wiki |

## 🔗 Tài liệu liên quan

→ ↑ {{ lark.wiki_root_url | wiki_link("Master Wiki Index") }} — canonical
→ [Skill 01 — Page Format](01-page-format.md) — template chuẩn
→ [Skill 05 — Publish Workflow](05-publish-workflow.md) — đẩy draft lên Lark
→ [Skill 08 — INDEX & Numbering](08-index-and-numbering.md) — quy tắc đánh số
→ [docs/07 — Status Tracker](../docs/07-status-tracker.md) — track trạng thái trang
