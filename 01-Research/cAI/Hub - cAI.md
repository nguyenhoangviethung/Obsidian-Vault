---
type: hub
topic: cai
status: in-progress
up: "[[00-Home]]"
created: 2026-11-26
tags:
  - type/hub
  - topic/cai
  - architecture
  - ai-agents
  - b2b
---

# Hub — Dự Án cAI (Enterprise Multi-Domain AI Agent Platform)

> [!abstract] Mục tiêu Research & Kick-off
> - **Dự án:** **cAI** — Nền tảng AI Agent cho doanh nghiệp (B2B), cho phép người dùng cuối tự tạo & tùy biến Agent linh hoạt mà **hạn chế kéo-thả / dựng workflow phức tạp**, có khả năng **scale mạnh cho `n` domain**.
> - **Brief gốc (Claude Artifact):** `https://claude.ai/artifact/CzTERjiqtCast5Wpoxw1Qq`
> - **3 Tiêu chí kiến trúc cốt lõi:**
>   1. **Enterprise & B2B Sellable:** Multi-tenant, RBAC/SSO/SCIM, Audit Log, Guardrails/Approval, Billing/Budgets, hỗ trợ SaaS + Self-hosted/VPC, **License sạch (MIT/Apache-2.0)** để bán thương mại / white-label không vướng bản quyền.
>   2. **End-User Agent Creation (No/Low Drag-and-Drop):** Người dùng cuối (không phải dev) tự tạo Agent bằng hội thoại + khai báo vai trò/routine/skill, đủ linh hoạt (flexible) nhưng không biến thành một công cụ kéo-thả node như n8n/Dify/Flowise.
>   3. **Scale mạnh cho `n` Domain:** Kiến trúc đóng gói Domain Pack (Skills + MCP/OpenAPI Tools + Knowledge + Policy), Tool Search chống tràn context, cô lập dữ liệu theo tenant/space/domain, và plug-in đa framework/model.

> [!warning] Lưu ý về Claude Artifact Brief
> Link `https://claude.ai/artifact/CzTERjiqtCast5Wpoxw1Qq` render nội dung động phía client (iframe sandbox của Claude.ai), nên bot không đọc trực tiếp được nội dung bên trong qua HTTP tĩnh.
> **Hành động đề xuất:** Export hoặc copy nội dung từ Artifact đó vào một file mới `Sources/cAI - Product Brief (Claude Artifact).md` để đối chiếu và hiệu chỉnh kiến trúc 100% theo vision cụ thể của dự án.

---

## 🎯 Kết Luận Nhanh (Executive Verdict)

Sau khi **clone và kiểm tra trực tiếp mã nguồn + LICENSE + DB Schema** của các dự án mã nguồn mở tiêu biểu nhất hiện nay (**OpenMausBot / Mausbot**, **CopilotKit OpenBot**, **Nightly-Labs OpenBot**, **Rakazo**, **Paperclip**, **Obot**, **Kortix CompanyOS/Suna**, cùng đối chiếu **OpenClaw**, **Dify**, **Onyx**):

- **Không có 1 repo đơn lẻ nào đạt 10/10 cả 3 tiêu chí**, nhưng **bộ ba kiến trúc tối ưu nhất cho cAI** là:
  1. **Base Foundation (Application + UX + Multi-tenant Core):** **`elie222/rakazo`** *(100% Apache-2.0)* — Full-stack TypeScript monorepo, **không dùng kéo-thả workflow**, đã có sẵn `Organization → Space → Bot`, PostgreSQL Row-Level Security (RLS), SCIM 2.0, Per-Space Vault, Audit Log Forwarding, Billing/Credits, Subagents, Fleet Management (Bot tạo/quản lý Bot), Pages (Dashboard), Web + Desktop + Mobile.
  2. **Domain Scaling & Governance Plane (Scale `n` domain):** **`obot-platform/obot`** *(100% MIT, Go/K8s)* — Virtual MCP Gateway (**vMCP**), **Dynamic Tool Search** (chống tràn context khi có hàng trăm tool ở `n` domain), **OpenAPI → MCP auto-hosting**, Git-backed MCP & Skills Registry, Unified LLM + MCP Guardrails.
  3. **Org Control Plane & Enterprise Patterns (Bổ sung):** **`paperclipai/paperclip`** *(100% MIT)* + các pattern thiết kế từ **`CopilotKit/OpenBot`** (CEL Action Policy, 8 Primitives, AG-UI Protocol) & **`OpenMausBot`** (Conversational `/setup`, Portable `team.md` Domain Pack, Customer-run Self-hosted Connector).
- **Cảnh báo bẫy License thương mại (B2B Deal-breakers):**
  - `nightly-labs/openbot` → **PolyForm Noncommercial 1.0.0** (Cấm dùng thương mại).
  - `CopilotKit/OpenBot` → Template MIT nhưng **Server binary & CopilotKit Intelligence là mã đóng/trả phí production**.
  - `milind-soni/OpenMausBot` → Core Apache-2.0, nhưng toàn bộ thư mục `enterprise/` (SSO, White-label, Budgets, Billing) là **proprietary source-available** (cấm resell/host cho bên thứ 3) và kiến trúc vốn là **local-first single-tenant**.
  - `kortix-ai/suna` & `langgenius/dify` → **Elastic License 2.0 / Modified Apache-2.0** (cấm bán Multi-tenant SaaS nếu không mua commercial license).

---

## 🗺️ Mục Lục Research (`01-Research/cAI/`)

### Ghi chú kiến trúc (`Notes/`)
- [[01 - Open-Source Landscape & License Audit]] — Bảng chấm điểm toàn cảnh 10 dự án OSS & kiểm toán bẫy bản quyền B2B.
- [[02 - Deep-Dive Codebase Analysis]] — Giải phẫu mã nguồn thật (DB schema, auth, runtime, UX tạo agent, sandbox) của Mausbot, OpenBot, Rakazo, Paperclip, Obot, Kortix.
- [[03 - Recommended Reference Architecture for cAI]] — Đề xuất kiến trúc mục tiêu cho cAI, sơ đồ hệ thống, cơ chế tạo Agent không kéo-thả, chiến lược scale `n` domain & lộ trình kick-off.
- [[04 - Áp dụng Reference Architecture vào đề bài cAI]] — Đối chiếu kiến trúc 03 với brief, thêm Case & Process Plane, áp dụng thử cho Domain Pack Tuyển dụng, lộ trình 12 tuần.

### Tài liệu nguồn (`Sources/`)
- [x] [[cAI - Product Brief (Claude Artifact)]] *(tóm tắt do Claude đọc ngày 18/11/2026; cần đối chiếu lại với artifact gốc)*

---

## 📊 Tất Cả Ghi Chú Thuộc Chủ Đề cAI (Auto Base)

```base
filters:
  and:
    - 'file.hasTag("topic/cai")'
    - 'type != "hub"'
views:
  - type: table
    name: "cAI Research Notes"
    groupBy:
      property: type
      direction: ASC
    order:
      - file.name
      - type
      - status
      - up
```
