---
type: source
topic: ccar-p
status: done
up: "[[Hub - CCAR-P]]"
tags:
  - type/source
  - topic/ccar-p
  - mock-exam
  - resources
---

# CCAR-P Mock Exam Resources

> [!info] Lưu ý quan trọng về đề thi thật
> Anthropic **không phát hành ngân hàng câu hỏi mẫu đầy đủ chính thức** cho **CCAR-P (Claude Certified Architect – Professional)**; tài liệu chính thức là **CCAR-P Exam Guide** trong *Anthropic Partner Academy*. Các nguồn dưới đây là tài nguyên cộng đồng và nền tảng luyện thi mô phỏng theo đúng blueprint 7 domain.

---

## 1. Thông số kỳ thi & Trọng số (để tự căn giờ & chấm điểm)

- **Mã thi:** `CCAR-P` (Claude Certified Architect – Professional)
- **Số câu hỏi:** 63–70 câu (Single-response & Multiple-response; mỗi câu chọn nhiều đáp án đều ghi rõ số lượng cần chọn)
- **Thời gian:** 120 phút (~1.7 phút/câu)
- **Điểm đạt:** 720 / 1,000 (scaled score)
- **Hình thức thi:** Pearson VUE (Online proctored hoặc tại trung tâm), lệ phí $175 USD, hiệu lực 12 tháng

| STT | Domain | Trọng số |
| :-: | :--- | :---: |
| 1 | Integration (MCP, Tool Design, RAG, AuthN/AuthZ, Observability) | **19%** |
| 2 | Solution Design & Architecture (Workflow vs Agent, Multi-agent, Trade-offs) | **17%** |
| 3 | Evaluation, Testing & Optimization (Golden datasets, CI/CD Evals, Cost/Latency) | **16%** |
| 4 | Governance, Safety & Risk Management (Guardrails, Compliance, HITL) | **14%** |
| 5 | Stakeholder Communication & Lifecycle Management (SLOs, Change Management) | **14%** |
| 6 | Claude Models, Prompting & Context Engineering (Model tiers, Prompt Caching) | **13%** |
| 7 | Developer Productivity & Operational Enablement | **7%** |

---

## 2. Website luyện đề Mock Exam (Có bản miễn phí)

### Dành riêng cho CCAR-P (Professional)
- **Timo Labs** — https://www.timolabs.dev
  - Có bài test miễn phí **20 câu / 40 phút** cho CCAR-P (không cần đăng ký tài khoản), trả về điểm theo từng domain kèm giải thích đáp án. Có gói trả phí cho bộ đề full-length.
- **StudyTech AI** — https://studytech.ai
  - Bộ câu hỏi tình huống miễn phí (dạng interactive quiz, không cần đăng ký) bám sát 7 domain của CCAR-P, có giải thích vì sao từng phương án đúng/sai.
- **Cert Made Easy** — https://certmadeeasy.com
  - Cung cấp ngân hàng câu hỏi tình huống miễn phí, giải thích chi tiết theo ràng buộc hệ thống và CCAR-P Cheat Sheet.
- **AI Cert Guides** — https://aicertguides.com
  - Có công cụ chẩn đoán nhanh (free diagnostic), cheat sheet và chế độ luyện tập theo từng domain trong 7 domain CCAR-P.
- **Szymon Paluch** — https://szymonpaluch.com
  - Có bài viết hướng dẫn và mock exam cho cả CCAR-P và CCAR-F, hỗ trợ **Study Mode** (xem đáp án ngay) và **Timed Exam Mode** (mô phỏng 120 phút).
- **Preporato** — https://preporato.com
  - Nền tảng luyện thi scenario-based cho CCAR-P có giải thích chi tiết cho từng phương án.
- **Tutorials Dojo** — https://tutorialsdojo.com
  - Có study guide, lộ trình ôn tập và bộ đề luyện thi CCAR-P bám sát 7 domain (Integration, Solution Design, Evaluation, Governance, Developer Productivity...).
- **AI Wisdom** — https://aiwisdom.dev
  - Cung cấp khóa học, kế hoạch ôn tập và bộ câu hỏi luyện tập miễn phí chia theo từng domain của CCAR-P kèm giải thích chi tiết đáp án đúng/sai.
- **CertSafari** — https://certsafari.com
  - Tổng hợp các câu hỏi tình huống chuyên sâu về LLM-as-a-Judge bias, Prompt Caching, ADR & kiểm soát rủi ro xác suất cho CCAR-P.
- **ClaudeCertPrep** — https://claudecertprep.com
  - Tài liệu và bài tập tập trung vào các anti-pattern và bẫy kiến trúc thực chiến của chứng chỉ Claude Certified Architect.
- **YouTube Walkthroughs** — Tìm kiếm từ khóa `"CCAR-P practice exam walkthrough"`
  - Các video phân tích đề thi mẫu theo phương pháp loại trừ (*method of elimination*) cho các câu tình huống kiến trúc dài.

---

## 3. Kho GitHub mã nguồn mở (Open-Source Prep)

### Nhóm CCAR-P (Mục tiêu chính)
1. **`anuragsirish/claude-certified-architect-prep`**
   - Link: https://github.com/anuragsirish/claude-certified-architect-prep
   - Nội dung: Lộ trình video miễn phí, Exam Guide rút gọn, ghi chú học tập (167 trang) và **20 câu hỏi luyện tập theo tình huống** do tác giả đã thi đạt 860/1,000 tổng hợp.
2. **`shreyabaid007/Claude-Certified-Architect`**
   - Link: https://github.com/shreyabaid007/Claude-Certified-Architect
   - Nội dung: Ghi chú có cấu trúc cho cả CCAR-F và CCAR-P, kèm bảng so sánh, sơ đồ Mermaid, tổng hợp bẫy đề thi (*exam traps*).
3. **`sarveshtalele/claude-architect-exam-guide`**
   - Link: https://github.com/sarveshtalele/claude-architect-exam-guide
   - Nội dung: Phân tích từng domain của đề thi và prompt cấu hình biến Claude thành study coach / người gác thi thử.
4. **`ai-zero-to-hero` (CCAR-P section)**
   - Link: https://github.com/ai-zero-to-hero/tree/main/certifications/anthropic/claude-certified-architect-professional
   - Nội dung: Phân rã chi tiết từng domain khớp với blueprint chính thức của Anthropic.

### Nhóm CCAR-F (Foundations — Dùng để củng cố nền tảng, không thay thế đề CCAR-P)
> [!warning] Phân biệt CCAR-F và CCAR-P
> Các repo dưới đây tập trung vào **CCAR-F (Foundations)** (Agentic Architecture, Claude Code, Prompting, MCP, Context Management). Nhiều kiến thức kỹ thuật vẫn dùng chung cho CCAR-P, nhưng CCAR-P sẽ hỏi sâu hơn về Governance, Stakeholder Lifecycle, Evaluation CI/CD và kiến trúc Enterprise.
- `avidevelops/claude-architect-exam-prep` — https://github.com/avidevelops/claude-architect-exam-prep
- `ankitrahejagatech/claude-certified-architect-prep` — https://github.com/ankitrahejagatech/claude-certified-architect-prep
- `OlivierAlter/Claude-Certified-Architect-Foundations-Certification-Exam` — https://github.com/OlivierAlter/Claude-Certified-Architect-Foundations-Certification-Exam (có bộ câu hỏi theo 5 domain Foundations và terminal skill để thi thử).
- `utkarsh1agarwal/claude-architect-exam-guide` — https://github.com/utkarsh1agarwal/claude-architect-exam-guide (có Interactive Mock Exam Player bấm giờ và chấm điểm tự động cho 5 domain CCAR-F).
- `szymonpaluch/claude-certified-architect-foundations-practice-exam` — https://github.com/szymonpaluch/claude-certified-architect-foundations-practice-exam (ngân hàng câu hỏi markdown tình huống và trình thi thử trên trình duyệt).
- `dnacenta/claude-certified-architect` — https://github.com/dnacenta/claude-certified-architect (study guide theo từng domain kèm flashcard và đáp án ẩn).

---

## 4. Liên kết nội bộ
- Bộ câu hỏi mẫu Set 01 (6 câu khởi động): [[CCAR-P Sample Questions - Set 01]]
- Bộ đề Mini-Test Set 02 (14 câu đủ 7 domain): [[CCAR-P Mock Exam - Full Mini-Test (Set 02 - 14 Câu theo 7 Domain)]]
- Tổng hợp bẫy đề thi & Anti-patterns: [[CCAR-P Exam Traps & Anti-Patterns Cheatsheet]]
- Ghi chép lỗi sai khi làm bài: [[Mock Exam Mistake Log Template]]
- Tài liệu nguồn Domain 1: [[Domain-1 Solution design & architecture]]
