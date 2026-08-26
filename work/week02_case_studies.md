# Week 2 · Frame It as Cases: Work That Speaks for Itself

**Track:** AI Fluency · Week 2 Assignment  
**Student:** Jagan T. Jiju  
**Repository:** [github.com/jagantj28-wq/flyrank-ml-internship-starter](https://github.com/jagantj28-wq/flyrank-ml-internship-starter)  
**Reference Guide:** [https://aifluency.flyrank.ai/week-02.html#frame-it-as-cases](https://aifluency.flyrank.ai/week-02.html#frame-it-as-cases)

---

## 1. Voice Card (Standing Instruction)

> **"Direct, plain, rigorous, curious, unpretentious, specific."**

* **Rule of Thumb:** Short sentences. No corporate filler ("passionate," "spearheaded," "results-driven," "synergy"). Say exactly what happened, what broke, what worked, and what the numbers actually showed. If it sounds like a LinkedIn bot wrote it, throw it away.

---

## 2. The Cases (The Three Beats)

### Case 1: Search Content Refresh Ranking Engine (Primary Proof)
*The flagship project proving I can build machine learning systems on real, messy search data and evaluate them honestly.*

* **The Problem:**  
  In large-scale search publishing, content quietly decays over time: rankings slip, impressions drop, and revenue erodes. In FlyRank's 30,000-page starter dataset, **54.2% of pages (accounting for 51.3% of all search impressions)** were actively declining. Editorial teams can only review 20 to 50 URLs in a weekly sprint. Manual auditing is impossible, and rigid hand-written rules (`stale >= 180 days AND impressions >= 500`) either fail on coverage (catching only 17 pages) or flood the queue with noise (Precision@50 of only 24%).

* **What I Did & Decided:**  
  I built an end-to-end decision-support ranking pipeline:
  1. *Signal Architecture:* Cleaned 44 raw search dimensions and engineered 52 observable features (position tiers, velocity logs, freshness ratios).
  2. *Leakage Quarantine:* Explicitly banned target derivatives (`trend_direction`, `trend_pct`) and product decision flags (`health_score`) from feature matrices.
  3. *Honest Validation:* Implemented a strict `client_holdout` cross-validation split—holding out ~20% of entire client domains so no client's pages ever appeared in both training and test sets.
  4. *Model Exploration:* Trained and compared logistic regression, decision trees, and random forests against the hand-crafted baseline rule.

* **What Came of It:**  
  On completely unseen client domains, the Random Forest model achieved **Precision@50 = 0.680** (34 of the top 50 pages were verified declining) compared to **0.240** for the baseline rule (12 of top 50). That is a **~2.83x precision lift**, directly sparing editors from wasting dozens of hours on healthy content.  
  *What I would do differently next time:* The starter dataset uses a 90-day trailing slice; next time I will define forward-window outcome labels over the 79M-row warehouse to evaluate seasonal shift resilience over a 12-month horizon.

---

### Case 2: Leakage Audit & Anti-Leakage CI Harness (Supporting Proof)
*A technical audit demonstrating why evaluation honesty matters more than high test scores.*

* **The Problem:**  
  Beginner ML models often boast near-perfect accuracy (e.g. 98–100%) because target outcomes leak into the training features. In search, feeding in percentage change or product review flags creates circular models that look spectacular in Jupyter notebooks but fail completely when deployed to production.

* **What I Did & Decided:**  
  1. *The Trap Demonstration:* In Notebook 02, I deliberately trained a "leaky" decision tree by injecting `trend_pct`. The tree achieved a counterfeit 100% precision by splitting directly on the outcome derivative.
  2. *The Remediation:* Removed all outcome-derived features and rebuilt the feature pipeline exclusively on pre-decision observable signals (clicks, impressions, average position, content age).
  3. *CI Guardrails:* Configured GitHub Actions CI workflows and `.gitignore` data guards to enforce that raw datasets never leak into version control and that training pipelines run reproducibly in headless environments.

* **What Came of It:**  
  Proved that genuine, un-leaked models operate at an honest, reliable 68% Precision@50 rather than an artificial 100%. The resulting pipeline is robust, reproducible, and gives human reviewers understandable reason codes for every recommendation.  
  *What I would do differently next time:* Write automated pytest unit tests checking feature column names against an explicit blocklist during preprocessing, catching leakage before training code even starts.

---

## 3. Bio and Contact Copy

### Bio (Two Sentences):
> "I build machine learning ranking tools that help content and SEO teams find decaying pages before traffic drops. I focus on honest validation on messy search data, keeping models simple, readable, and free of data leakage."

### Contact Prompt / Call to Action (One Line):
> **"Have a messy search or tabular data problem? Email me at jagantj28@gmail.com and let's talk through an approach."**

---

## 4. The Before / After (Generic AI vs. Edited Human Voice)

### ❌ The Generic AI Version:
> *"As a passionate and results-driven machine learning practitioner, I leveraged cutting-edge artificial intelligence algorithms and advanced data preprocessing methodologies to spearhead a paradigm-shifting content optimization framework that maximizes synergies between organic search visibility and digital engagement KPIs."*

### ✅ My Edited Version:
> *"I built a ranking model on 30,000 pages of real search data to tell editors which decaying articles to fix first. Under an honest test on clients the model had never seen, it got 34 of its top 50 recommendations right—nearly three times better than the hand-written rule it replaced."*

### Why the difference matters:
* The generic AI version is full of vague corporate filler ("leveraged," "spearheaded," "synergies," "results-driven") that says nothing about what was actually built.
* My edited version gives real numbers (30,000 pages, 34 of top 50 right, ~3x lift), names the exact task (triage decaying articles for editors), and tells an engineering lead exactly what was accomplished.
