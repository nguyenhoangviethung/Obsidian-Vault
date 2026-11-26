---
type: system
status: active
up: "[[00-Home]]"
tags:
  - type/system
---

# 📘 Vault Guide — Kiến Trúc Research Đa Chủ Đề

Vault được thiết kế theo nguyên tắc **Trọn gói theo từng chủ đề (Topic-First)** kết hợp với các cơ chế liên kết gốc của Obsidian (**Properties, Wikilinks, Obsidian Bases, Templates, Nested Tags, Graph View**) để dễ kiểm soát trong thanh thư mục mà không bị phân tán tài liệu ra nhiều nơi.

---

## 1. Bản Đồ Thư Mục (Chỉ 4 Khu Vực Chính)

| Thư mục | Vai trò | Quy tắc đơn giản |
| :--- | :--- | :--- |
| **`00-Home.md`** | Trang chủ điều phối | Mở đầu tiên mỗi khi vào Vault; tự tổng hợp mọi chủ đề bằng Obsidian Bases. |
| **`00-Inbox/`** | Hộp thư đến (Nháp nhanh) | Ghi chú vội / chưa biết thuộc chủ đề nào *(note mới tạo mặc định vào đây)*. |
| **`01-Research/`** | **Trọn gói từng chủ đề độc lập** | Mỗi chủ đề = 1 thư mục riêng (VD: `CCAR-P/`, `Kubernetes/`, `Investing/`). Toàn bộ `Hub`, `Notes/` (chia thư mục con theo chương/domain nếu cần), `KeyNotes/`, `Practice/`, `Sources/` của chủ đề đó **đều nằm gọn bên trong**. |
| **`02-Shared-Knowledge/`** | **Kiến thức dùng chung (Tùy chọn)** | **Chỉ** chuyển note ra đây khi khái niệm đó được dùng chung cho **≥ 2 chủ đề khác nhau** (VD: `SLA vs SLO` dùng cho cả `CCAR-P` lẫn `System-Design`). |
| **`03-Archive/`** | Kho lưu trữ | Khi xong 1 chủ đề, kéo nguyên thư mục từ `01-Research/<Chủ-đề>/` sang đây. |
| **`99-System/`** | Hạ tầng Vault | `Assets/` (ảnh/PDF tự động lưu), `Bases/`, `Templates/`, `Vault-Guide.md`. |

---

## 2. Quy Tắc Tổ Chức Bên Trong Một Chủ Đề (`01-Research/<Chủ-đề>/`)

- **Cấp 1 (Thư mục chủ đề):** Chứa file `Hub - <Tên>.md` làm điểm vào duy nhất + các thư mục theo vai trò phù hợp với chủ đề đó (VD: `Notes/`, `KeyNotes/`, `Practice/` hoặc `Experiments/`, `Sources/`).
- **Cấp 2 (Phân cấp con trong `Notes/`):** Nếu chủ đề có nhiều chương/domain (như CCAR-P có 7 Domain, mỗi Domain có nhiều Lesson), tạo thư mục con theo từng chương/domain (VD: `Notes/Domain-1 Solution Design & Architecture/`) để file tổng quan và các bài học con nằm cùng một cụm, không bị trải phẳng ngang hàng.

---

## 3. Cơ Chế Liên Kết & Kiểm Soát Của Obsidian

- **Properties chuẩn hóa:** `type` (`hub`, `note`, `concept`, `source`, `practice`), `topic`, `status` (`inbox`, `in-progress`, `review`, `done`, `archived`), `up` (`"[[Hub - ...]]"` hoặc note cha).
- **Nested Tags:** `#topic/<chủ-đề>` và `#type/<loại>`. Nếu 1 note liên quan 2 chủ đề, chỉ cần gắn cả 2 tag `#topic/a` và `#topic/b` — note đó sẽ tự xuất hiện trong Base của cả 2 Hub!
- **Obsidian Bases:** [[00-Home]] dùng [[Research-Dashboard.base]] để quản lý toàn Vault; mỗi `Hub - <Tên>.md` nhúng 1 bảng `base` riêng để tự gom mọi note thuộc chủ đề đó.
- **Graph View:** Nhờ thuộc tính `up`, mỗi chủ đề tự tạo thành 1 cụm vệ tinh tách biệt; các note trong `02-Shared-Knowledge/` hoặc liên kết chéo `[[...]]` sẽ hiện thành cầu nối giữa các cụm.

---

## 4. Quy Trình 30 Giây Mở Một Chủ Đề Research Mới

1. Tạo thư mục: `01-Research/<Ten-Chu-De>/` (bên trong tạo `Notes/`, `KeyNotes/`, `Practice/`... tùy nhu cầu).
2. Tạo file `Hub - <Ten-Chu-De>.md` trong thư mục đó.
3. Bấm `Ctrl+P` → **Templates: Insert template** → chọn **`TPL - Research Hub`**.
4. Đổi `<slug-chu-de>` trong frontmatter và trong khối `base` cuối file.
5. Chủ đề mới sẽ **tự động hiện trên [[00-Home]]**!
