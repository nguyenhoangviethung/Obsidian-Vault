---
type: concept
topic: ccar-p
status: in-progress
up: "[[Hub - CCAR-P]]"
tags:
  - type/concept
  - topic/ccar-p
  - glossary
  - terminology
---

# CCAR-P Glossary — Core Enterprise & AI Architecture Terms

> [!tip] How to Use This Glossary
> Whenever an exam question or lesson uses an unfamiliar enterprise or AI term, add it here with **1 plain-English definition** and **1 concrete exam example**.

---

## Domain 1 & Lesson 1.1 Foundations

### SLA (Service-Level Agreement)
- **Meaning:** A formal, contractual promise of system performance or availability made to users or customers (e.g., *"99.9% uptime"* or *"95% of responses must return in under 2 seconds"*).
- **Exam Signal:** Usually a **Hard Constraint**. If an SLA says `< 2 seconds`, any slow multi-agent loop or deep sequential chain is immediately disqualified.
- **Related:** **SLO (Service-Level Objective)** is the internal engineering target used to stay safely inside the SLA.

### Fixed SOP (Standard Operating Procedure)
- **Meaning:** A standardized, step-by-step checklist where the exact sequence of steps is already known and must be followed consistently every time (e.g., *Step 1: Verify order ID $\rightarrow$ Step 2: Check return window $\rightarrow$ Step 3: Calculate refund $\rightarrow$ Step 4: Send email*).
- **Exam Signal:** A **Fixed SOP** means you should build a **Deterministic Workflow** (code orchestrating steps), **NOT** an Autonomous Agent.

### Baseline (Current State / "Now")
- **Meaning:** A quantitative measurement of how the process performs *today* before introducing Claude (e.g., *"Currently takes 14 minutes per support ticket with a 12% error rate"*).
- **Exam Signal:** Without a baseline, you cannot prove your solution improved anything—making success criteria meaningless.

### Hard Constraints vs. Soft Preferences
- **Hard Constraints (Non-negotiable):** Rules that **immediately eliminate** any design violating them (e.g., GDPR/HIPAA data residency, zero PII leaving VPC, `< 2s` latency SLA, accuracy cannot drop, strict legal audit trail).
- **Soft Preferences (Optimizable trade-offs):** Nice-to-have goals balanced *after* hard constraints are met (e.g., lower token cost, faster developer velocity, cleaner UX).
- **Golden Rule:** **Hard constraints always veto soft preferences.**

### Scope (In-Scope vs. Out-of-Scope)
- **Meaning:** Explicit boundaries defining what the system **will** handle and what it **will NOT** handle (and must escalate or reject).
- **Exam Signal:** Prevents "scope creep" and uncontrolled AI behavior on edge cases (e.g., *In-scope: standard domestic returns under $200; Out-of-scope: fraud disputes and international customs claims*).

### Deterministic vs. Probabilistic
- **Deterministic:** Given the exact same input, the system **always** produces the exact same output (e.g., Python code, SQL queries, regex, schema validators, RBAC permission checks, arithmetic).
- **Probabilistic:** Output is generated based on statistical likelihood and can vary slightly or make rare mistakes (e.g., LLMs like Claude).
- **Golden Rule:** Never use a probabilistic system (prompting Claude) to enforce a deterministic requirement (math, security, permissions, exact policy rules).

### Falsifiable Success Criteria & SMART Metrics
- **Falsifiable:** A statement that **can be proven false** by a real measurement or test.
  - *Bad (Unfalsifiable):* "Improve customer support quality."
  - *Good (Falsifiable):* "Reduce average handle time from 14 min to $\le$ 6 min while keeping policy-compliance accuracy $\ge$ 98% on a 500-ticket test set."
- **SMART:** **S**pecific, **M**easurable, **A**chievable, **R**elevant, **T**ime-bound.

### HITL (Human-in-the-Loop)
- **Meaning:** Designing an explicit checkpoint where a human must review, edit, or approve Claude's output **before** an external action executes.
- **Exam Signal:** Required for **high-stakes or irreversible actions** (issuing large refunds, sending legal contracts, modifying production financial/medical records).

### Guardrails
- **Meaning:** Protective checks placed **around** the LLM (pre-processing inputs and post-processing outputs) to block unsafe, non-compliant, or malformed behavior (e.g., PII redaction middleware, JSON schema validation, output classifiers).
- **Exam Signal:** Effective enterprise guardrails live **in code/infrastructure outside the model**, not only inside the system prompt.

### PII (Personally Identifiable Information)
- **Meaning:** Any data that can identify a specific individual (full name, SSN/ID number, email, phone number, bank account, medical record).
- **Exam Signal:** Must be stripped, masked, or tokenized programmatically before sending to external endpoints when required by compliance.

### RBAC (Role-Based Access Control) & Least Privilege
- **RBAC:** Controlling what data or actions a user/service can access based on their assigned role.
- **Least Privilege:** Granting only the minimum permissions strictly needed for a task (e.g., read-only DB credentials for a search agent).
- **Exam Signal:** Access control must be enforced at the API/database/middleware layer, never by telling Claude in a prompt *"don't show admin data."*

### Latency, TTFT, and P95 Latency
- **Latency:** Total time from sending a request until receiving the complete response.
- **TTFT (Time-to-First-Token):** How long a user waits before seeing the first word appear (improved by **streaming**).
- **P95 Latency:** The response time under which 95% of requests complete (captures tail slowness much better than averages).

### Data Residency & VPC (Virtual Private Cloud)
- **Data Residency:** Legal or corporate requirement that data must be stored and processed only within a specific geographic region (e.g., EU only).
- **VPC:** An isolated private cloud network where enterprise workloads run; often requires hosting Claude via AWS Bedrock or Google Vertex AI within private network boundaries.
