---
type: hub
topic: <slug-chu-de>
status: in-progress
up: "[[00-Home]]"
created: {{date}}
tags:
  - type/hub
  - topic/<slug-chu-de>
---

# Hub — {{title}}

> [!abstract] Mục tiêu Research
> - **Câu hỏi trung tâm / Mục tiêu:** 
> - **Phạm vi (In-scope / Out-of-scope):** 
> - **Đầu ra kỳ vọng (Output):** *(VD: Báo cáo, Chứng chỉ, Prototype, Quyết định đầu tư...)*

---

## 🗺️ Mục Lục Chủ Đề

### Ghi chú chính (`Notes/`)
- [ ] 

### Khái niệm / Tổng hợp (`KeyNotes/`)
- 

### Thực hành / Thí nghiệm (`Practice/`)
- 

### Tài liệu nguồn (`Sources/` hoặc liên kết)
- 

---

## 📊 Tất Cả Ghi Chú Thuộc Chủ Đề Này (Auto Base)

> [!note] Đổi `<slug-chu-de>` bên dưới trùng với tag `topic/...` của chủ đề này.

```base
filters:
  and:
    - 'file.hasTag("topic/<slug-chu-de>")'
    - 'type != "hub"'
views:
  - type: table
    name: "Topic Notes"
    groupBy:
      property: type
      direction: ASC
    order:
      - file.name
      - type
      - status
      - up
```
