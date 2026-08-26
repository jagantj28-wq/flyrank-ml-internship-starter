# Week 1 · Draw the Path: Portfolio Sitemap & AI Toolkit

**Track:** AI Fluency · Week 1 Assignment  
**Student:** Jagan T. Jiju  
**Repository:** [github.com/jagantj28-wq/flyrank-ml-internship-starter](https://github.com/jagantj28-wq/flyrank-ml-internship-starter)  
**Reference Guide:** [https://aifluency.flyrank.ai/week-01.html#draw-the-path](https://aifluency.flyrank.ai/week-01.html#draw-the-path)

---

## 1. The Proof Statement

### The Three Core Questions:
* **The ONE Claim:** I build machine learning ranking models that work on messy, real-world search data and evaluate them honestly on held-out client domains.
* **The ONE Person:** An engineering lead or hiring manager at an applied AI / search intelligence company who needs practical decision-support tools rather than vanity demos.
* **The ONE Action:** Email me to discuss an applied ML role or set up an exploratory technical interview.

### The Statement (One Paragraph):
> "I build machine learning ranking and decision-support models that work on messy, real-world search data, evaluated honestly under client-holdout validation with zero data leakage. I am proving this to an engineering lead at an applied AI or search intelligence company who needs reliable, production-ready prototypes, so they will email me to schedule an interview."

### The One Honest "Why":
*A resume simply lists machine learning keywords and course titles. Owning this portfolio proves I can take 79M rows of messy, pseudonymized search data, formulate honest baselines, hunt data leakage, and deliver a transparent decision-support system that beats human rules by nearly 3x.*

---

## 2. The Portfolio Sitemap (Minimalist & Action-Focused)

A tight, 3-page structure where every element earns its place:

```text
[ 1. LANDING PAGE / HERO ]
    │
    ├── Value Proposition (States the single claim in 5 seconds)
    ├── Key Proof Metrics (e.g. ~2.8x Precision@50 lift over rule baseline; 79M-row warehouse scale)
    └── Direct CTA ("Review the Capstone Case Study" / "Get in Touch")
    │
    ▼
[ 2. WORK: SEARCH REFRESH CASE STUDY (The Proof) ]
    │
    ├── The Business Problem (Editorial triage for 30k+ decaying URLs)
    ├── The Methodology (Data contract, client-holdout split, zero-leakage discipline)
    ├── The Evidence (Rule baseline 24% vs Random Forest 68% Precision@50)
    ├── Interactive Deliverables (GitHub repository, live notebook links, PDF report)
    └── In-line Action Call ("Discuss this build with Jagan")
    │
    ▼
[ 3. ABOUT & CONTACT (The Person + The One Action) ]
    │
    ├── Engineering Philosophy (First-principles ML, anti-leakage rigor, observational claims)
    ├── Technical Stack (Python, pandas, scikit-learn, DuckDB, Git CI)
    └── The One Action: Direct email form / mailto link + GitHub & LinkedIn links
```

### Why every page earns its place:
1. **Landing (Hero):** Captures the engineering lead's attention in under 10 seconds and establishes credibility with verified metrics.
2. **Work (Search Refresh Case Study):** Provides the definitive technical proof of the claim with reproducible code, honest metrics, and architecture decisions.
3. **About & Contact:** Answers "who built this," articulates engineering maturity/ethics, and removes all friction to executing the one action (emailing for a role).
4. **Resisted Pages:** No generic multi-page blog, no filler "skills tag clouds," and no fabricated client testimonials.

---

## 3. Claude Project Configuration & Custom Instructions

* **Project Name:** `FlyRank Portfolio Build - AI Enablement`
* **Role Requested:** Act as a demanding technical tutor and editorial critic who explains reasoning before proposing solutions.

### Custom Instructions:

```text
You are a senior technical advisor, applied ML mentor, and portfolio tutor working with Jagan T. Jiju throughout his 10-week FlyRank AI Enablement build.

Proof Statement to Enforce:
"I build machine learning ranking and decision-support models that work on messy, real-world search data, evaluated honestly under client-holdout validation with zero data leakage. I am proving this to an engineering lead at an applied AI or search intelligence company who needs reliable, production-ready prototypes, so they will email me to schedule an interview."

Your Role & Persona:
1. Act as a demanding, constructive tutor. Explain the 'why' behind architectural and copywriting choices before drafting text.
2. Pressure-test all portfolio decisions against the One Claim, One Person, and One Action.
3. Push back aggressively when text sounds generic, fluffy, or boastful. Enforce disciplined scientific language: 'observed', 'directional', and 'decision-support'.
4. Ensure zero target leakage and strict data safety in all code recommendations.
```

---

## 4. The Pressure-Test: Real Prompt & AI Critique

### The Prompt Sent to Claude:

```text
This is my proof statement:
"I build machine learning ranking and decision-support models that work on messy, real-world search data, evaluated honestly under client-holdout validation with zero data leakage. I am proving this to an engineering lead at an applied AI or search intelligence company who needs reliable, production-ready prototypes, so they will email me to schedule an interview."

This is my sitemap:
1. Landing Page (Hero): One clear claim, key metric highlight (2.8x precision lift over rule baseline), and primary CTA ("Explore Case Study").
2. Case Study Page: Deep dive on the Search Content Refresh Opportunity model—showing data contract, leakage audit, baseline vs random forest results (Precision@50: 0.68 vs 0.24), and repo link.
3. About & Philosophy: Engineering philosophy (first-principles ML, client-holdout validation, human-in-the-loop decision support).
4. Contact: Direct email link and GitHub/LinkedIn profiles with a single clear CTA ("Let's talk").

Does this sitemap walk my one person from landing, to believing me, to taking my one action, and does it actually prove my claim? Tell me which pages earn their place, which are wasted, and what is missing. Be specific, not encouraging.
```

### The AI's Unfiltered Critique:

> 1. **What Earns Its Place:**
>    * **The Case Study is the core engine.** An engineering lead does not hire based on claims; they hire based on evidence. Showing the data contract, the baseline comparison (24% vs 68%), and the client-holdout split directly proves your claim.
>
> 2. **What Is Wasted / Friction Points:**
>    * **A standalone Contact Page is unnecessary friction.** Engineering leads are busy. If someone finishes reading your technical case study and is impressed, forcing them to click to a separate "Contact" page creates drop-off.
>    * **"About & Philosophy" risks sounding preachy if isolated.** Philosophy is only convincing when attached to code decisions. Having a standalone philosophy page tempts you to write fluff.
>
> 3. **What Is Missing:**
>    * **A direct CTA at the bottom of the Case Study.** The moment of highest conviction happens right when they see the holdout validation results and the GitHub repo. That is where the email trigger belongs.
>    * **A visible Code/Reproducibility badge on the Landing page.** An engineering lead wants to see code quality immediately.

### The Concrete Changes Made (Refinement):
Based on the pressure test, **two specific revisions were adopted**:
1. **Eliminated the separate Contact page:** The contact form / direct email button is placed directly in the footer of the Landing Page and prominently at the conclusion of the Case Study. When conviction is highest, the action is immediate.
2. **Integrated Philosophy into the Case Study:** Instead of a detached "About Me" philosophy page, the engineering principles (anti-leakage discipline, client-holdout rationale, and observational claim rules) are embedded directly as technical sidebars within the Case Study itself, where they serve as evidence rather than opinion.
