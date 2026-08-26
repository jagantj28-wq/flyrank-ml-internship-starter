# FL-01: Personal Workflow Audit & AI Collaboration Setup

**Track:** AI Fluency · Phase: Setup  
**Student:** Jagan T. Jiju  
**Repository:** [github.com/jagantj28-wq/flyrank-ml-internship-starter](https://github.com/jagantj28-wq/flyrank-ml-internship-starter)

---

## 1. Personal Workflow Audit (12 Recurring Tasks)

Based on Ethan Mollick's framework (*On-boarding your AI Intern*), recurring tasks across coursework, applied machine learning projects, and daily engineering routines are classified below:

| # | Recurring Task | Category | One-Line Rationale |
|---|---|---|---|
| **1** | **Setting thesis/capstone research scope & ethical boundaries** | **Just me** | Core intellectual ownership and ethical responsibility cannot be delegated; human intent must drive the problem choice. |
| **2** | **Evaluating peer code & grading team member contributions** | **Just me** | Interpersonal accountability, qualitative performance assessment, and academic integrity require direct human judgment. |
| **3** | **Sitting for live exams & technical interview problem solving** | **Just me** | Demonstrating unassisted algorithmic reasoning, first-principles mastery, and spontaneous communication must be done solo. |
| **4** | **Drafting boilerplate unit tests for ML pipeline functions** | **Delegate to AI with review** | AI quickly generates standard edge-case assertions (NaNs, empty series), but I verify logic and data contracts manually. |
| **5** | **Summarizing lengthy technical documentation & research papers** | **Delegate to AI with review** | AI condenses methods and takeaways rapidly; I review against the original text to catch nuanced omissions or hallucinations. |
| **6** | **Generating Python docstrings, type annotations, & Markdown tables** | **Delegate to AI with review** | Structural reformatting is mechanical and fast for LLMs, requiring only a brief inspection before staging. |
| **7** | **Drafting professional email updates & project progress notes** | **Delegate to AI with review** | AI drafts polite, structured status updates from bullet points, which I personalize and edit before sending. |
| **8** | **Debugging obscure environment/runtime errors & stack traces** | **Collaborate with AI** | Iterative back-and-forth hypothesis testing pinpoints dependency, encoding, or CPython build conflicts faster than manual searching. |
| **9** | **Brainstorming feature engineering hypotheses for tabular models** | **Collaborate with AI** | AI acts as a creative sounding board for domain signals, while I evaluate feasibility and enforce anti-leakage guards. |
| **10** | **Red-teaming research questions & decision-support framing** | **Collaborate with AI** | Using AI as a devil's advocate exposes weak assumptions, circular logic, or unmeasured claims before formal review. |
| **11** | **Refactoring scripts into modular, readable functions** | **Collaborate with AI** | Pair-programming with AI refines code architecture, naming clarity, and idiomatic patterns while preserving behavior. |
| **12** | **Automated code formatting, linting, & CI test runs** | **Fully automate** | Deterministic tools (Ruff/Black/Prettier and GitHub Actions) execute syntax rules and test suites automatically on git push. |

---

## 2. Three Target Tasks for FL-02 through FL-04

These three high-leverage tasks will be reused in upcoming assignments to build systematic prompt templates, feedback loops, and automated workflows.

### Target Task 1: Feature Engineering Brainstorming & Signal Auditing
* **Category:** Collaborate with AI (FL-02: Prompt Engineering & Co-Intelligence)
* **Description:** Collaborating with an AI assistant to generate candidate feature ideas from raw search console/analytics dimensions, verify domain relevance, and check for leakage risks.
* **Measurable Definition of "Done Well":**
  1. Generates $\ge 5$ concrete, mathematically well-defined feature formulations from observable data.
  2. Passes a strict anti-leakage filter: explicitly confirms zero target leakage (no outcome derivatives or product decision flags).
  3. Translates directly into clean, runnable pandas/NumPy code that executes without error on the first pass.

### Target Task 2: Test Suite Generation & Data Contract Verification
* **Category:** Delegate to AI with Review (FL-03: Delegation, Error Handling & Review)
* **Description:** Providing data schemas and function signatures to AI to draft comprehensive test cases verifying schema integrity, value ranges, and null constraints.
* **Measurable Definition of "Done Well":**
  1. Coverage covers at least four distinct failure modes: missing values, out-of-bound ranges (e.g. rate columns), unexpected dtypes, and empty splits.
  2. 100% test pass rate on valid test fixtures, with immediate failing tests on deliberately broken mock data.
  3. Review time under 5 minutes with zero hallucinated module dependencies.

### Target Task 3: Translating Technical ML Results into Decision-Support Reports
* **Category:** Collaborate with AI (FL-04: Workflow Systems & Human-in-the-Loop)
* **Description:** Synthesizing model performance tables, feature importances, and baseline comparisons into a structured markdown report designed for human decision-makers.
* **Measurable Definition of "Done Well":**
  1. Clearly states the decision, targeted user, recommended action, and concrete cost of error.
  2. Strictly adheres to disciplined claim language: uses *observed*, *directional*, and *decision-support* without any causal claims or assertions of predicting proprietary algorithms.
  3. Includes executive callouts with real metrics (e.g., baseline vs model Precision@50, uplift ratios) and a prioritized action queue.

---

## 3. Claude Project Configuration & Custom Instructions

### Project Details
* **Project Name:** `FlyRank ML & Fluency Workspace`
* **Target Environment:** Claude (Web / Desktop)

### Custom Instructions (Configured in Project Settings)

```text
You are an expert AI pair-programmer and research colleague collaborating with Jagan T. Jiju on applied machine learning, search intelligence, and software engineering.

Core Persona & Background:
- Jagan is a Computer Science engineer and applied ML intern at FlyRank specializing in tabular ranking systems, search console data analytics, and production data workflows.
- Technical stack: Python, pandas, scikit-learn, DuckDB, Git, GitHub Actions, Windows/PowerShell.

Tone & Style Preferences:
- Be concise, direct, and technically rigorous. Avoid flattering filler, conversational preamble, and repetitive summaries.
- Deliver code in complete, modular blocks with idiomatic type annotations and clean error handling.
- Maintain strict scientific integrity: clearly distinguish between correlation and causation. Frame all findings as "observed," "directional," or "decision-support."

Anti-Leakage & Safety Discipline:
- Never use target labels (e.g. trend_direction, trend_pct) or product decision outputs (e.g. health_score) as input features.
- Never hardcode credentials, API tokens, client identities, or private URLs in code.
- Treat model outputs as prioritization aids for human experts, not infallible automated decisions.
```

---

## 4. Free Toolkit & Course Enrollment Verification

* **Claude Account:** Configured with the custom project instructions above.
* **ChatGPT Account:** Registered for cross-model validation and red-teaming.
* **Anthropic Academy:** Enrolled in *AI Fluency: Framework & Foundations* (`anthropic.skilljar.com/ai-fluency-framework-foundations`) and completed Module 1 (*Introduction to AI Fluency & The Co-Intelligence Mindset*).
