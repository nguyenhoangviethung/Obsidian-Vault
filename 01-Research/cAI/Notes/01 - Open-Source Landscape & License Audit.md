---
type: note
topic: cai
status: done
up: "[[Hub - cAI]]"
created: 2026-11-26
tags:
  - type/note
  - topic/cai
  - oss-audit
  - licensing
---

# 01 — Open-Source Landscape & License Audit (cAI)

> [!abstract] Tóm tắt nhanh (TL;DR)
> Đánh giá **10 nền tảng AI Agent mã nguồn mở** tiêu biểu nhất giai đoạn 2025–2026 theo 3 tiêu chí của **cAI**: **(1) Bán B2B / Enterprise**, **(2) End-user tạo Agent hạn chế kéo-thả**, **(3) Scale cho `n` domain**.
> Phần lớn các dự án "hot" như Mausbot, OpenBot (CopilotKit & Nightly), Kortix Suna, Dify đều có **bẫy bản quyền (license trap)** hoặc **kiến trúc local-first** không phù hợp để fork trực tiếp bán B2B SaaS. **Chỉ có 3 dự án thật sự sạch license (Apache-2.0 / MIT full-stack) và đủ tầm kiến trúc enterprise:** **Rakazo**, **Paperclip**, và **Obot** (kèm **Onyx** nếu cần Enterprise RAG).

---

## 1. Bảng So Sánh Tổng Quan & Kiểm Toán License

| Dự án (GitHub Repo) | License thực tế *(đã kiểm tra code)* | B2B SaaS / White-label? | Tech Stack | Kiểu tạo Agent (End-User) | Điểm B2B (1) | Điểm UX No-Drag (2) | Điểm Scale `n` Domain (3) | Kết luận cho cAI |
| :--- | :--- | :---: | :--- | :--- | :---: | :---: | :---: | :--- |
| **Rakazo**<br>`elie222/rakazo` | **Apache-2.0** *(100% full-stack)* | ✅ **Tự do bán & white-label** | TypeScript, React/Vite, Electron, Expo, Prisma, PostgreSQL (RLS), Graphile Worker, Docker/E2B/Daytona | Chat-first + Prompt + Skills + Routines + **Fleet Bot** (bot tạo bot), **cấm visual workflow** | **9/10** | **9.5/10** | **7.5/10** | 🏆 **Ứng viên #1 làm Core Base** |
| **Obot**<br>`obot-platform/obot` | **MIT** *(100% full-stack)* | ✅ **Tự do bán** *(bản Enterprise chỉ khác license key cho tính năng mở rộng)* | Go, PostgreSQL/SQLite, Kubernetes/Helm, Bifrost LLM GW | Không phải app end-user tạo bot; là **MCP & LLM Governance Platform** | **9.5/10** | **4/10** *(Admin/IT UI)* | **10/10** *(vMCP + Tool Search + Git Registry)* | 🏆 **Ứng viên #1 làm Domain & Governance Plane** |
| **Paperclip**<br>`paperclipai/paperclip` | **MIT** *(100% full-stack)* | ✅ **Tự do bán** | TypeScript (Node.js), React, PostgreSQL, BYOA Adapters | **Org Chart + Skill Studio + Eval Kernel + Team Templates** (Task-centric) | **9/10** | **7/10** *(hơi nặng chất manager/ops)* | **8.5/10** *(Multi-Org + Skills/Teams Catalog)* | 🥈 **Tốt nhất cho Multi-Agent Org & Budget/Eval** |
| **OpenBot (CopilotKit)**<br>`CopilotKit/OpenBot` | **MIT (Template)** + **Proprietary (Server Binary & Intelligence)** | ❌ **Bị khóa lõi** *(server & memory phải mua license CopilotKit)* | React/Vite, Hono API (closed img), Postgres, AG-UI, LangGraph/15 harnesses, K8s Helm | **8 Primitives** + Standing Role + Routines + Teach-a-Bot (rất chuẩn!) | **8.5/10** *(thiết kế)* / **2/10** *(license)* | **9/10** | **8.5/10** *(AG-UI + MCP + Tenant Pkg)* | 💡 **Học kiến trúc & protocol (AG-UI, CEL Policy), KHÔNG fork trực tiếp** |
| **Mausbot (OpenMausBot)**<br>`milind-soni/OpenMausBot` | **Apache-2.0 (Core)** + **Proprietary (`enterprise/`)** | ⚠️ **Bị giới hạn** *(`enterprise/` cấm resell/host cho bên thứ 3)* | Electron, React/Tailwind, Node.js harness (`127.0.0.1`), NDJSON local, `claude`/`codex`/`grok` CLI | **Chat-first + `/setup` tự hỏi đáp + `team.md` 1-click** | **5/10** *(local-first, 1 server/tenant)* | **9/10** | **6.5/10** *(gắn chặt local CLI)* | 💡 **Học UX `/setup`, `team.md`, On-Prem Connector; KHÔNG dùng làm core SaaS** |
| **OpenBot (Nightly-Labs)**<br>`nightly-labs/openbot` | **PolyForm Noncommercial 1.0.0** | ❌ **Cấm hoàn toàn thương mại** | Electron/Bun local desktop, ACP, local CLI runners, Cloudflare D1 auth | Prompt-driven local agents (`danger-full-access`, `approvalPolicy: never`) | **1/10** | **7/10** | **3/10** | ❌ **Loại ngay lập tức** |
| **Kortix CompanyOS (Suna)**<br>`kortix-ai/suna` | **Elastic License 2.0** + `WHITE-LABEL-LICENSE.md` | ❌ **Cấm bán SaaS/Managed Service** | TypeScript/Python, `pi` harness, GitOps (`kortix.yaml`), Cloud Sandboxes | Git-as-code + Outcomes (`Outcome→Plan→Work→Verify→Learn`) + My Kortix router | **8/10** *(kỹ thuật)* / **2/10** *(license)* | **6/10** *(hơi thiên dev/GitOps)* | **8.5/10** | 💡 **Học pattern Outcomes & Code Event Filter, KHÔNG fork bán SaaS** |
| **OpenClaw**<br>`openclaw/openclaw` | **MIT / OpenClaw Foundation** | ✅ **Tự do dùng** | TypeScript/Node, Hub-and-Spoke Gateway, `SKILL.md` + `SOUL.md` | Config/Markdown (`AGENTS.md`, `SKILL.md`), sống trong Slack/Telegram/WhatsApp | **6.5/10** *(OCE mới hình thành)* | **5/10** *(cấu hình file)* | **7.5/10** | 💡 **Chuẩn hóa định dạng `SKILL.md` & kênh chat** |
| **Onyx (Danswer)**<br>`onyx-dot-app/onyx` | **MIT (Community)** + EE | ✅ **Core MIT dùng thương mại được** | Python (FastAPI), Next.js, Postgres, Vespa/Vector DB | Custom Assistants (Prompt + Knowledge + Actions), không kéo-thả | **8/10** | **7.5/10** | **8/10** *(Enterprise RAG Connectors)* | 💡 **Tham khảo/tích hợp cho tầng Enterprise RAG & ACL Indexing** |
| **Dify**<br>`langgenius/dify` | **Modified Apache-2.0** | ❌ **Cấm Multi-tenant B2B SaaS & cấm xóa logo** | Python (Flask), Next.js, Postgres, Redis, Vector DB | **Kéo-thả Visual Workflow Canvas** (Node Graph) | **7/10** *(kỹ thuật)* / **1/10** *(license)* | **3/10** *(vi phạm tiêu chí #2)* | **7/10** | ❌ **Loại** *(vướng cả license lẫn triết lý kéo-thả)* |

---

## 2. Phân Loại Nhóm Kiến Trúc Trên Thị Trường 2026

Các dự án mã nguồn mở hiện nay chia làm **4 trường phái kiến trúc rõ rệt**:

1. **Nhóm Local-First CLI Harness (`OpenMausBot`, `nightly-labs/openbot`):**
   - Bản chất là ứng dụng Electron/Desktop bọc các CLI coding agent (`claude`, `codex`, `grok`, `gemini`) chạy trên máy người dùng.
   - **Ưu:** UX chat cực mượt, gần gũi như Telegram/Slack, khởi tạo nhanh.
   - **Nhược chí mạng cho B2B:** Không sinh ra để làm multi-tenant SaaS; lưu state kiểu file local (NDJSON); bảo mật & cô lập dữ liệu yếu; phụ thuộc subscription/CLI cài trên máy; bản Enterprise bị khóa license.

2. **Nhóm Persistent AI Coworker Workspace (`Rakazo`, `CopilotKit/OpenBot`, `Kortix CompanyOS`):**
   - Bản chất là **server-first platform** (API + Worker + PostgreSQL + Pluggable Sandboxes + Multi-surface Web/Desktop/Mobile/Slack/Teams).
   - **Ưu:** Đúng tinh thần "Agent là đồng nghiệp lâu dài có trí nhớ, routine, máy tính riêng, kết nối app và xin duyệt trước khi hành động", **loại bỏ hoàn toàn kéo-thả workflow**.
   - **Điểm phân hóa:** Trong nhóm này, **chỉ có `Rakazo` là 100% Apache-2.0 mở toàn bộ mã nguồn backend + frontend + worker + DB schema**.

3. **Nhóm Multi-Agent Organizational Control Plane (`Paperclip`):**
   - Không làm lại agent loop mà làm **tầng điều phối tổ chức**: Org Chart, Goal Ancestry, Atomic Task Checkout, Budget Enforcement (tự động phanh khi vượt ngân sách), Skill Studio + Evals.
   - **Ưu:** 100% MIT, thiết kế Multi-Organization từ gốc, giải bài toán kiểm soát chi phí & chất lượng ở quy mô doanh nghiệp.

4. **Nhóm Enterprise AI / MCP Governance Gateway (`Obot`):**
   - Đứng giữa mọi Agent/Client và toàn bộ hệ thống công cụ/LLM doanh nghiệp.
   - **Ưu:** 100% MIT, giải quyết triệt để bài toán **scale `n` domain** bằng **Virtual MCP (vMCP)**, **Dynamic Tool Search**, **OpenAPI → MCP**, **Git-backed Registry**, và **Unified Guardrails**.

---

## 3. Các Bẫy Bản Quyền (License Traps) Cần Tránh Khi Làm Sản Phẩm B2B

> [!danger] 4 Bẫy bản quyền đã xác minh trực tiếp trong repo
> 1. **Bẫy "Open Template / Closed Runtime" (`CopilotKit/OpenBot`):** File `LICENSE` ghi MIT, nhưng file `backend-images.env` kéo ảnh Docker đóng (`ghcr.io/copilotkit/openbot`) và yêu cầu Production License cho `CopilotKit Intelligence`.
> 2. **Bẫy "Open Core + Enterprise Folder Restriction" (`OpenMausBot`):** File `enterprise/LICENSE` ghi rõ: *"Hosting for third parties, reselling, and white-labelling the product for third parties are permitted only under a written partner agreement"*.
> 3. **Bẫy "Source-Available Anti-SaaS" (`Kortix/Suna` — Elastic 2.0; `Dify` — Modified Apache 2.0):** Không được phép dùng làm nền tảng multi-tenant SaaS bán cho khách hàng B2B nếu không ký hợp đồng thương mại.
> 4. **Bẫy "Noncommercial" (`nightly-labs/openbot` — PolyForm Noncommercial):** Cấm mọi mục đích thương mại kể cả dùng nội bộ doanh nghiệp.

---

## 4. Liên Kết Liên Quan
- Hub chủ đề: [[Hub - cAI]]
- Phân tích sâu mã nguồn: [[02 - Deep-Dive Codebase Analysis]]
- Kiến trúc đề xuất cho cAI: [[03 - Recommended Reference Architecture for cAI]]
