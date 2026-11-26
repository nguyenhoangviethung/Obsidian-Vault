---
tags:
  - ccar-p
  - domain-1
  - lesson-1-1
---

# Lesson 1.1: From Business Problem to Claude Solution

> [!abstract] Learning Objectives (From Official Lesson)
> After this lesson, you can:
> 1. Write a **problem statement** with **baseline, target, constraints, and scope**.
> 2. Split a "solution" into **tasks** and decide who owns each: **Claude, code, a specialized system, or a person**.
> 3. Pick a **build path** by applying **hard constraints first**.
> 4. Write **success criteria that can actually fail**.

---

## 1. The Core Method: 5 Decisions in Strict Order

Never treat a stakeholder's request (e.g., *"Build an AI agent"*) as a technical spec. Treat it as a **hypothesis**, then walk through **5 sequential decisions**—each decision narrows the next:

| Step | Stage | Core Question | Key Elements |
| :-: | :--- | :--- | :--- |
| **1** | **Problem** *(WHY)* | What real problem are we solving and within what limits? | **Now (Baseline)** · **Target** · **Limits (Hard/Soft Constraints)** · **Scope (In/Out)** |
| **2** | **Tasks** *(WHAT)* | What smaller units of work make up this solution? | Break down until each task has **one clear, independent test** |
| **3** | **Owners** *(WHAT)* | Who is the most reliable owner for each task? | **Claude** · **Code** · **Specialized System** · **Person (HITL)** |
| **4** | **Build Path** *(HOW)* | Which implementation path survives non-negotiable limits? | Apply **Hard Constraints first** (compliance, SLA, data residency, budget) to eliminate invalid paths |
| **5** | **Success** *(PROOF)* | How will we objectively prove it worked—or failed? | **Falsifiable SMART criteria** anchored directly to Step 1's **Baseline** |

---

## 2. Worked End-to-End Example

> **Vague Stakeholder Ask:**
> *"Our customer support team is overwhelmed. Build an AI agent to automatically handle customer refund requests."*

### Step 1 — Reframe into a Problem Statement (`WHY`)
- **Baseline (Now):** Support agents handle 1,200 refund tickets/week; average handling time (AHT) is **14 minutes/ticket**; policy-error rate is **8%**.
- **Target:** Cut AHT to **$\le$ 5 minutes/ticket** and reduce policy-error rate to **$\le$ 2%** within 90 days.
- **Hard Constraints:**
  - Zero unauthorized refunds; refunds $> \$200$ require manager approval.
  - Exact refund amounts (including tax/shipping deductions) must have **0% math error**.
  - Customer payment/PII data must comply with corporate privacy rules; every decision needs an audit log.
  - Customer-facing draft reply latency must be **$< 5$ seconds (P95)**.
- **Scope:**
  - *In-Scope:* Standard domestic e-commerce orders within the 30-day return window.
  - *Out-of-Scope:* Fraud disputes, chargebacks, international customs claims (must escalate directly to human specialists).

### Step 2 & Step 3 — Split into Testable Tasks & Assign Owners (`WHAT`)

| # | Discrete Task | One Clear Test | Best Owner | Why? |
| :-: | :--- | :--- | :--- | :--- |
| 1 | Classify ticket intent & detect out-of-scope (fraud/chargeback) | Precision/Recall $\ge$ 95% on 200 labeled tickets | **Claude** | Unstructured customer emails with varied wording and emotion. |
| 2 | Fetch order details, purchase date, & item price | Returns exact DB record for order ID | **Specialized System** *(Order DB / ERP API)* | Source of truth lives in structured database. |
| 3 | Check if `today - purchase_date <= 30 days` & calculate exact refund + tax | 100% pass on unit tests (edge dates, decimals) | **Deterministic Code** | Exact date logic and math must be 100% deterministic. |
| 4 | Draft empathetic customer response explaining approval/denial + policy reason | Human/LLM-judge rubric score $\ge$ 4.5/5, no policy contradiction | **Claude** | Natural language drafting & tone adaptation. |
| 5 | Approve & trigger refund if amount $> \$200$ | Manager approval recorded in audit log before payout API fires | **Person (HITL)** | High-stakes irreversible financial action. |
| 6 | Execute payout API & write immutable audit trail | Payout matches calculated amount; log entry created | **Deterministic Code / Payment System** | Side-effecting financial action must be controlled in code. |

> [!warning] Notice What Just Happened!
> The stakeholder asked for an *"AI agent"*, yet decomposing the problem proved that **only 2 of 6 tasks belong to Claude**—and the overall flow is a **fixed SOP (deterministic workflow with HITL)**, NOT an autonomous agent!

### Step 4 — Pick a Build Path Using Hard Constraints First (`HOW`)
- **Hard Constraint 1 (0% math error & strict rules):** Eliminates letting Claude compute totals or decide policy thresholds via prompt alone $\rightarrow$ deterministic workflow orchestrating code + APIs.
- **Hard Constraint 2 (Refunds $> \$200$ need human approval):** Eliminates full end-to-end autonomous payout $\rightarrow$ add programmatic HITL gate.
- **Hard Constraint 3 (P95 $< 5$ seconds for draft):** Eliminates heavy multi-agent loops $\rightarrow$ lightweight routing + single drafting call with prompt caching on refund policy text.

### Step 5 — Write Falsifiable Success Criteria (`PROOF`)
- **Falsifiable SMART Criteria (tied to Step 1 Baseline):**
  1. Reduce average handling time on in-scope refund tickets from **14 min (baseline)** to **$\le$ 5 min** over a 30-day pilot.
  2. Reduce refund policy-error rate from **8% (baseline)** to **$\le$ 2%**, with **0%** math/payout discrepancies.
  3. Achieve **$\ge$ 95%** recall on routing out-of-scope fraud/chargeback tickets to humans.
- *Why this works:* Every single criterion **can fail** during pilot testing, giving an objective Go / No-Go signal.

---

## 3. Mini Practice Scenarios (Test Yourself!)

### Practice Q1 — Ownership Split
A legal ops team wants Claude to review incoming vendor NDAs: extract non-standard clauses, check whether the vendor exists on the company's sanctions blocklist, calculate total contract liability across 15 line items, and flag high-risk exceptions for General Counsel review.

Which task ownership split follows CCAR-P best practices?
- **A.** Let Claude handle all four tasks in a single prompt with strict instructions to double-check arithmetic and blocklist names.
- **B.** Use Claude to extract non-standard clauses; use a specialized sanctions DB/code lookup for blocklist checks; use deterministic code to sum liability across line items; route flagged high-risk exceptions to a human (General Counsel).
- **C.** Deploy four autonomous Claude agents—one per task—communicating in an open-ended loop until all four agree.
- **D.** Build a custom fine-tuned Claude model trained on past NDAs so it memorizes the sanctions blocklist and addition rules.

> [!success]- Answer & Rationale (Click to reveal)
> **Correct Answer: B**
> - **B** assigns each task to its rightful owner: Claude handles unstructured legal clause extraction; specialized DB/code handles exact blocklist matching; deterministic code handles arithmetic; a human (HITL) owns high-stakes legal risk approval.
> - **A & D** misuse probabilistic LLMs for deterministic lookup/math (and fine-tuning goes stale as sanctions lists change).
> - **C** over-engineers a fixed SOP with unnecessary multi-agent latency, cost, and non-determinism.

### Practice Q2 — Falsifiable Success Criteria
An enterprise sponsor asks to *"deploy Claude to make internal HR policy Q&A much better for employees."* Which success criterion is properly designed for Step 5?
- **A.** "Employees report feeling generally positive about the new AI assistant after launch."
- **B.** "Claude answers HR policy questions accurately and helpfully."
- **C.** "Reduce HR helpdesk repeat policy tickets from 450/month (baseline) to $\le$ 225/month within 60 days while maintaining $\ge$ 97% citation-verified factual accuracy on a 300-question HR golden set."
- **D.** "Ensure the system uses the newest flagship Claude model with an autonomous multi-agent architecture."

> [!success]- Answer & Rationale (Click to reveal)
> **Correct Answer: C**
> - **C** is **SMART**, **falsifiable** (can clearly pass or fail), and anchored directly to a **Step 1 baseline** (450 tickets/month) plus a quality guardrail ($\ge$ 97% factual accuracy).
> - **A & B** are vague and unfalsifiable.
> - **D** measures implementation choices (model/agent), not business or quality outcomes.

---

## 4. Linked Concepts
- [[Domain-1 Solution design & architecture]]
- [[CCAR-P Glossary - Core Terms]]
- [[MOC - CCAR-P 4-Day Sprint]]
