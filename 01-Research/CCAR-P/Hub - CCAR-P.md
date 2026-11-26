---
type: hub
topic: ccar-p
status: in-progress
up: "[[00-Home]]"
created: 2026-11-25
aliases:
  - MOC - CCAR-P 4-Day Sprint
tags:
  - type/hub
  - topic/ccar-p
  - study-plan
  - 4-day-sprint
---

# MOC — CCAR-P 4-Day Intensive Study Sprint

> [!important] Sprint Strategy (7 Domains in 4 Days)
> With only 4 days, **do not copy textbooks word-for-word**. Spend **30% of time learning concepts & terminology**, **50% doing scenario drills & error analysis**, and **20% reviewing your Glossary + Exam Traps**.

---

## 4-Day Schedule (Weighted by Exam Blueprint)

| Day | Focus Domains | Combined Weight | Target Outcome |
| :-: | :--- | :---: | :--- |
| **Day 1** | **D1:** Solution Design & Architecture (17%)<br>**D2:** Models, Prompting & Context Engineering (13%) | **30%** | Master the 5-decision design method, Workflow vs. Agent ladder, model selection, and Prompt Caching. |
| **Day 2** | **D3:** Integration (19%) — *Largest Domain*<br>**D7:** Developer Productivity & Enablement (7%) | **26%** | Master MCP vs. direct API, tool schema design, RAG architecture, AuthN/AuthZ, and operational workflows. |
| **Day 3** | **D4:** Evaluation, Testing & Optimization (16%)<br>**D5:** Governance, Safety & Risk Management (14%) | **30%** | Master golden datasets, CI/CD evals, failure diagnosis (retrieval vs. generation), guardrails outside the model, compliance, and HITL. |
| **Day 4** | **D6:** Stakeholders & Lifecycle (14%)<br>**Full Mock Exam + Mistake Log Review** | **14% + Full Sim** | Master SLOs, stakeholder alignment, change management, plus 1–2 timed mock exams and full trap review. |

---

## Daily Study Loop (Repeat for Every Lesson)

1. **Skim & Decode Terms (10 min):** Read the lesson outline and check unfamiliar terms in [[CCAR-P Glossary - Core Terms]].
2. **Capture Only 3 Things (15 min):** Never rewrite full prose. Record only: *(a) Decision rule*, *(b) Keyword triggers*, *(c) Exam traps*.
3. **Active Drill (15 min):** Answer 2–3 scenario questions immediately without looking at notes; explain *why the wrong options fail*.
4. **Spaced Review (End of Day, 20 min):** Re-read only your [[Mock Exam Mistake Log Template|Mistake Log]] and KeyNotes. (Optional: use audio/TTS to review glossary & traps while commuting or walking—never rely on audio alone for architecture diagrams.)

---

## Vault Navigation

### Domain Notes (`01-Research/CCAR-P/Notes/`)
- [[Domain-1 Solution design & architecture]] (17%)
  - [[Lesson 1.1 - From Business Problem to Claude Solution]]
- [ ] Domain-2 Models, Prompting & Context Engineering (13%)
- [ ] Domain-3 Integration (19%)
- [ ] Domain-4 Evaluation, Testing & Optimization (16%)
- [ ] Domain-5 Governance, Safety & Risk Management (14%)
- [ ] Domain-6 Stakeholders & Lifecycle (14%)
- [ ] Domain-7 Developer Productivity & Enablement (7%)

### Core KeyNotes (`01-Research/CCAR-P/KeyNotes/`)
- [[CCAR-P Glossary - Core Terms]]

### Practice & Mock Exams (`01-Research/CCAR-P/Practice/`)
- [[CCAR-P Mock Exam Resources]]
- [[CCAR-P Exam Traps & Anti-Patterns Cheatsheet]]
- [[CCAR-P Sample Questions - Set 01]]
- [[CCAR-P Mock Exam - Full Mini-Test (Set 02 - 14 Câu theo 7 Domain)]]
- [[Mock Exam Mistake Log Template]]

---

## Auto-Tracked Notes (Obsidian Base)

```base
filters:
  and:
    - 'file.hasTag("topic/ccar-p")'
    - 'type != "hub"'
views:
  - type: table
    name: "All CCAR-P Notes"
    groupBy:
      property: type
      direction: ASC
    order:
      - file.name
      - type
      - status
      - up
```
