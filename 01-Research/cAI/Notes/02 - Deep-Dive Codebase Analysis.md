---
type: note
topic: cai
status: done
up: "[[Hub - cAI]]"
created: 2026-11-26
tags:
  - type/note
  - topic/cai
  - architecture
  - codebase-analysis
---

# 02 — Deep-Dive Codebase Analysis (Mausbot, OpenBot, Rakazo, Paperclip, Obot, Kortix)

> [!abstract] Tóm tắt nhanh (TL;DR)
> Tài liệu này tổng hợp kết quả đọc trực tiếp mã nguồn, tài liệu kiến trúc (`ARCHITECTURE.md`, `DESIGN.md`, `PRODUCT.md`, `VISION.md`, `adr/`), và Database Schema của 6 dự án tiêu biểu nhất — phân tích rõ **cái gì nên lấy, cái gì phải bỏ** cho **cAI**.

---

## 1. Rakazo (`elie222/rakazo`) — Apache-2.0 Full-Stack

### Kiến trúc thực tế trong mã nguồn
- **Monorepo (`pnpm` + `turbo`):**
  - `apps/`: `api` (backend core), `worker` (**Graphile Worker** chạy routines/cron/webhooks/background jobs), `web` (React/Vite), `desktop` (Electron), `mobile` (Expo iOS/Android), `www`.
  - `packages/`: `core`, `db` (Prisma + PostgreSQL), `auth` (Better Auth), `adapters`, `adapter-kit`, `memory`, `page-kit` (live dashboards/docs), `contracts`, `chat-ui`, `i18n`, `logging`.
- **Mô hình dữ liệu Enterprise có sẵn trong `packages/db/prisma/`:**
  - **Multi-tenancy & Isolation:** `Organization` → `Member` / `Invitation` → `Space` → `SpaceMember` + **PostgreSQL Row-Level Security (RLS)** thông qua role ứng dụng tách biệt (`rakazo_app` chạy runtime vs owner role chạy migration).
  - **Enterprise Identity:** Có sẵn **SCIM 2.0** (`ScimConnectionBinding`, `ScimSubject`, `ScimUser`, `ScimGroup`, `ScimGroupMember`), `Passkey`, `AiDataConsent`.
  - **Security, Vault & Audit:** `SpaceKey` (mã hóa nội dung theo Space), `VaultCredential` + `VaultCredentialBot` + `VaultCredentialLimit` (bot điền login/TOTP trên browser nhưng **không bao giờ thấy mật khẩu trong context/tool output**), `AuditEvent`, `AuditEventForwarding` (đẩy SIEM), `SecurityEvent`.
  - **Billing & Commercial:** `BillingAccount`, `ModelCreditAccount`, `ModelCreditEntry`, `SpendingLimit`, `PartnerSale`, `ComputerAllowanceGrant`.
  - **Agent Runtime & Work:** `Bot`, `BotSection`, `ChatGroup`, `Thread`, `Message`, `Task`, `Run`, `Attempt`, `SteeringMessage`, `Routine`, `Board`, `Ticket`, `Page`, `PageVersion`, `AgentSkill`, `TaughtSkill`, `McpServer`, `BotMcpServer`.

### Đánh giá theo 3 tiêu chí của cAI
1. **B2B & Enterprise (9/10):** Rất mạnh về nền tảng DB/RLS/SCIM/Vault/Billing/Audit và 100% Apache-2.0. Điểm cần bổ sung: RBAC phân quyền chi tiết cấp phòng ban (custom roles), SSO SAML/OIDC enterprise UI, và chính sách policy engine khai báo (như CEL của OpenBot).
2. **End-User tạo Agent không kéo-thả (9.5/10):**
   - Triết lý trùng khớp tuyệt đối (`VISION.md`): *"Routines remain scheduled prompts rather than becoming a visual workflow language. Rakazo is not a visual workflow builder."*
   - Cơ chế **Fleet Management (`docs/fleet-management.md`)**: Người dùng có thể sai một Bot tạo/sửa/nhóm/lên lịch các Bot khác bằng ngôn ngữ tự nhiên (`list_fleet`, `create_group`, `set_child_instructions`, `create_child_routine`) — mọi thay đổi quan trọng đều có **version history** và **Approval Card** bắt buộc duyệt!
   - **Pages (`page-kit`)**: Bot không chỉ trả lời text mà tạo ra dashboard/báo cáo tương tác share link được.
   - **Subagents không block chat**: Bot giao việc song song cho subagent, thừa kế đúng boundary quyền của hội thoại gốc.
3. **Scale `n` Domain (7.5/10):**
   - Pluggable Sandbox (`Docker`, `E2B`, `Daytona`, `CreateOS`, `Boat`, `Railway`) giúp scale compute theo chiều ngang tốt.
   - Hỗ trợ MCP (HTTPS/stdio), OpenAPI JSON, Composio, Pipedream.
   - **Khoảng trống cần nâng cấp:** Chưa có **Virtual MCP Gateway + Tool Search** khi số lượng tool lên hàng trăm/nghìn ở `n` domain; chưa có **Domain Pack Marketplace/GitOps Registry** ở mức enterprise; chưa có **Eval/Benchmark** đo độ chính xác agent theo từng domain.

---

## 2. CopilotKit OpenBot (`CopilotKit/OpenBot`) — MIT Template / Closed Server & Intelligence

### Kiến trúc thực tế trong mã nguồn
- **Cấu trúc repo:** Chứa `app/` (React/Vite), `api/` (`openapi.yaml` 2MB + `asyncapi.yaml`), `shared/` (prompt rules, policy helpers, recorder), 15 thư mục `agent-*` (`agent-langgraph-agui`, `agent-claude-sdk`, `agent-adk`, `agent-crewai`, `agent-mastra`, `agent-pydantic-ai`, `agent-openai-agents`, `agent-strands`...).
- **Lõi đóng:** `backend-images.env` trỏ tới prebuilt images `ghcr.io/copilotkit/openbot` (`server` + `agent-computer`) và `ghcr.io/copilotkit/openbot-supervisor`, phụ thuộc dịch vụ `CopilotKit Intelligence` (cần license trả phí cho production).

### 5 Pattern Kiến Trúc Đỉnh Cao Nên Học Cho cAI
1. **Mô hình 8 Primitives × 3 Scopes (`docs/how-openbot-works.md`):**
   - Mọi tính năng trong sản phẩm chỉ xoay quanh đúng 8 khái niệm: `Conversations`, `Routines`, `Memory`, `Skills`, `Apps`, `Computer`, `Approvals`, `Activity` (+ `Vault`).
   - Áp dụng đồng nhất ở 3 tầng scope: **This Bot** (1 agent) → **Every Bot** (cá nhân) → **Everyone** (Admin/Doanh nghiệp). Giữ UX cực kỳ trong sáng dù tính năng phức tạp.
2. **CEL Action Policy Engine & `initiator_kind` (`docs/architecture.md`):**
   - Mọi hành động (browser, shell, file, MCP tool) đi qua Server Gateway, kiểm tra rule viết bằng **CEL expressions** (`deny` trước `allow`, **fail-closed**, hỗ trợ `dry-run`).
   - Ghi nhận `initiator_kind` (`person` vs `routine` vs `responsibility` vs `handoff` vs `memory`) trong **signed run assertion** để phân biệt rõ tác vụ có người giám sát hay **"Nobody watching"** (chạy ngầm)!
3. **AG-UI Protocol (Bring Your Own Agent Framework):**
   - Cho phép doanh nghiệp lớn kết nối agent tự viết bằng **LangGraph, Google ADK, Claude Agent SDK, CrewAI, Bedrock AgentCore, Mastra** vào chung một workspace cAI mà vẫn chịu chung quyền hạn, audit và approval!
4. **Typed Bot-to-Bot Handoff (`message_bot`) + `ask_person`:**
   - Handoff giữa 2 agent phải dùng **typed schema** (task + constraints + definition of done) thay vì văn bản tự do; có trần chống vòng lặp vô hạn (`BOT_HANDOFF_MAX_DEPTH`, `BOT_HANDOFF_MAX_PER_RUN`), chạy qua DB queue (`work_items` + `SELECT ... FOR UPDATE SKIP LOCKED` + `LISTEN/NOTIFY`).
5. **Security Boundary trên Sandbox Computer:**
   - Token mỗi máy tính được dẫn xuất bằng `HMAC-SHA256(COMPUTER_TOKEN, kind:id)` nên 1 container bị hack không mở được container khác; tách mạng Docker riêng cho PostgreSQL (`data` network); hỗ trợ chạy sandbox trên **gVisor (`runsc`)** + Egress Proxy chặn cloud metadata IP.

---

## 3. OpenMausBot / Mausbot (`milind-soni/OpenMausBot`) — Apache-2.0 Core / Proprietary Enterprise

### Kiến trúc thực tế trong mã nguồn
- **2 Process local loopback:** App Shell (`React 19 + Tailwind + Electron`) nói chuyện HTTP/SSE với Harness Server (`Node.js` tại `127.0.0.1:8799`), server spawn các tiến trình con (`claude`, `codex`, `grok` CLI) và chuẩn hóa output về **NDJSON** lưu trong `~/.openmausbot`.
- **Thư mục `enterprise/`:** Chứa license check Ed25519 offline (`omb1.<claims>.<sig>`) khóa các feature `whitelabel`, `sso`, `admin`, `budgets`, `billing` — **cấm resell/host cho bên thứ 3**.
- **Mô hình B2B (`docs/design/self-hosted-design.md`):** Không làm multi-tenant SaaS mà bán theo kiểu **"1 Server Docker cho mỗi khách hàng + Outbound Connector"** (`customer-run` mode: kết nối WebSocket chiều ra về Control Plane, chỉ cho chạy 7 job ký số cố định: `start/stop/restart/update/status/backup/host-health`, có rollback tự động).

### 4 Pattern Xuất Sắc Nên Học Cho cAI
1. **Conversational Onboarding (`/setup`):** *"Bots set themselves up. Don't add form fields for things the bot can learn by asking."* Người dùng chỉ cần chat 3–4 câu với Bot mới tạo, Bot tự cấu hình role, routine và hỏi kết nối app cần thiết.
2. **Portable Domain Team Package (`team.md`):** Đóng gói cả một phòng ban (nhiều bots + Chief of Staff + channels + skills + suggested routines + danh sách connectors) trong **1 file Markdown có YAML frontmatter** — tự động strip sạch secrets/memory/OAuth → cực kỳ lý tưởng để làm **Domain Pack Marketplace** cho `n` domain!
3. **Fast Decision Router (Jev-style):** Dùng model siêu nhẹ/nhanh (~200–300ms) để tự định tuyến tin nhắn chưa `@mention` trong phòng nhóm đến đúng chuyên gia domain.
4. **Connected Self-Hosted Connector (Cho khách Enterprise yêu cầu On-Prem/VPC):** Một outbound WebSocket connector có whitelist job ký bằng Ed25519 + offline entitlement key + hash-chained audit log (`/var/log/mausbot/audit.jsonl`) — rất hay nếu cAI có gói bán On-Premise cho ngân hàng/tập đoàn!

---

## 4. Paperclip (`paperclipai/paperclip`) — MIT Full-Stack

### Kiến trúc thực tế trong mã nguồn
- **Stack:** Node.js/TypeScript server + React UI + PostgreSQL (`packages/db/src/schema/` với hơn 100 bảng chuẩn enterprise: `budget_policies`, `automatic_run_brakes`, `agent_instruction_revisions`, `agent_config_revisions`, `approvals`, `cases`, `chat_slack_*`, `chat_teams_*`).
- **Packages đáng chú ý:**
  - `packages/adapters`: BYOA adapters (Claude Code, Codex, OpenClaw, Cursor, Gemini CLI, Hermes, HTTP).
  - `packages/paperclip-eval-kernel`: Hệ thống **Eval & Saved Test Runs** để test skill/agent trước khi đưa lên production!
  - `packages/skills-catalog` & `packages/teams-catalog`: Kho chuẩn hóa Skills & Ready-made Teams.

### Điểm mạnh cần ghép vào cAI
- **Budget Policies + Automatic Run Brakes:** Đặt trần ngân sách theo Company / Department / Project / Agent, tự động phanh run bị lặp vô hạn (bài toán sống còn khi bán B2B).
- **Skill Studio + Eval Kernel + Revision Rollback:** Cho phép Power User / Domain Owner tạo skill, chạy test với bộ input mẫu, kiểm tra chất lượng và rollback phiên bản lỗi.
- **Goal Alignment & Atomic Task Checkout:** Khi doanh nghiệp chạy quy trình đa agent (VD: Sales → Legal → Finance), mỗi task gắn với mục tiêu chung và có lock nguyên tử tránh 2 agent làm trùng việc.

---

## 5. Obot (`obot-platform/obot`) — MIT Full-Stack (Go + Kubernetes)

### Kiến trúc thực tế trong mã nguồn (`adr/`)
- **Virtual MCP Servers (`adr/2026-09-01-virtual-mcps.md`):**
  - Hợp nhất nhiều MCP server (từ catalog hoặc tự host) sau **1 endpoint vMCP duy nhất** cho từng domain/phòng ban.
  - Mỗi component pin theo **snapshot** (catalog thay đổi không làm sập production bất ngờ), phân loại cấu hình (`fixed`, `user-allowed`, `prohibited`), tự động quyết định dùng chung runtime hay tách riêng theo user.
  - **Tool Search trên vMCP lớn:** Khi doanh nghiệp kết nối 500–2.000 tools ở `n` domain, agent không phải nhồi toàn bộ schema vào prompt mà gọi tool search để nạp đúng tool cần thiết!
- **OpenAPI → Hosted MCP Server:** Biến bất kỳ REST API nội bộ nào (SAP, ERP, Jira nội bộ, HRM, CRM) có file OpenAPI spec thành MCP server có quản trị ngay lập tức.
- **Unified Guardrails Engine (`adr/2026-09-26-guardrails.md`):**
  - Một engine `pkg/guardrails` dùng chung cho cả **LLM Gateway** và **MCP Gateway** (kiểm tra cả `tools/call` input/output, `resources/read`, lẫn LLM prompt/response): phát hiện/che bí mật (secrets), PII/regex, LLM judge, tích hợp Bedrock Guardrails / Azure Content Safety / Model Armor / Lakera.
- **Git-Backed Registries + Okta SCIM:** Quản trị vòng đời Skills và MCP Catalogs bằng Git (GitHub/GitLab/Bitbucket), đồng bộ user/group tự động qua SCIM.

---

## 6. Kortix CompanyOS / Suna (`kortix-ai/suna`) — Elastic License 2.0

### 3 Ý Tưởng Kiến Trúc Giá Trị (Dù Không Fork Do License)
1. **Deterministic Code Filter trước Trigger:** Khi webhook/email/app event bắn vào, chạy **bộ lọc bằng code** để loại nhiễu trước khi đánh thức LLM → tiết kiệm 80–90% chi phí token ở quy mô B2B.
2. **Outcomes Loop (`Outcome → Plan → Work → Verify → Learn`):** Cho phép người dùng khai báo "mục tiêu cần đạt + tiêu chí hoàn thành (Definition of Done)", dùng một **Verifier Agent độc lập** nghiệm thu thay vì để agent tự chấm mình.
3. **Compute & State Separation:** Sandbox computer tự ngủ sau 15–20 phút nhàn rỗi (tốn 0 compute), đánh thức lại vẫn giữ nguyên `/workspace` và lịch sử hội thoại.

---

## 7. Liên Kết Liên Quan
- Hub chủ đề: [[Hub - cAI]]
- Tổng quan & License Audit: [[01 - Open-Source Landscape & License Audit]]
- Kiến trúc mục tiêu cho cAI: [[03 - Recommended Reference Architecture for cAI]]
