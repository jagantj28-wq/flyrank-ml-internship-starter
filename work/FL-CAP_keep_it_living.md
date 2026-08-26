# FL-CAP: Keep It Living — Sustaining the Portfolio Platform

**Track:** AI Fluency · Capstone Milestone (`fl-cap`)  
**Student:** Jagan T. Jiju  
**Repository:** [github.com/jagantj28-wq/flyrank-ml-internship-starter](https://github.com/jagantj28-wq/flyrank-ml-internship-starter)  
**Reference Guide:** [https://aifluency.flyrank.ai/week-10.html#send-the-link](https://aifluency.flyrank.ai/week-10.html#send-the-link)

---

## 1. How to Add the Next Case Study (Step-by-Step System)

To ensure this portfolio remains an evolving career platform rather than a static course artifact, new work is added via an automated, low-friction 4-step loop:

* **Where it lives:** The new case study is added directly as a distinct project block within `docs/index.html` (under the Research & Case Studies section) and recorded as an executed notebook under `work/notebooks/`.
* **The 4-Step Addition Workflow:**
  1. **Re-engage the Preserved Claude Project:** Open the standing `FlyRank ML & Fluency Workspace` Claude Project. It already contains my Voice Card (*"Direct, plain, rigorous, curious, unpretentious, specific"*), target audience (applied AI leads), and technical constraints (zero data leakage, observational language).
  2. **Run the 3-Beat Interview:** Provide raw, unpolished experimental notes and instruct Claude to interview me with 3 sharp questions:
     - *Beat 1 (The Problem):* What was decaying, broken, or un-optimized in the search data?
     - *Beat 2 (What I Did & Decided):* What architecture was chosen, what was deliberately excluded, and what trade-offs were made?
     - *Beat 3 (What Came of It):* What was the measured out-of-sample result, what were the honest limits, and what would I do differently next time?
  3. **Drop into HTML & Commit Receipts:** Format the 3-beat narrative with a key metrics card and code links into `docs/index.html`, and save the validation metrics JSON into `work/outputs/`.
  4. **Deploy in 10 Seconds:** Run `git add .`, `git commit -m "Add new case study: ..."`, and `git push origin main`. GitHub Pages deploys automatically within 60 seconds with zero infrastructure maintenance.

---

## 2. The Named Next Piece of Real Work

* **Project Title:** **"Forward-Window Lag-Free Ranking Engine on the Full 79M Warehouse"**
* **Context & Scope:**  
  Our current capstone models 30,000 URLs over a trailing 90-day observation window. The next iteration scales this pipeline to DuckDB queries across the full 78.8M-row historical warehouse release on Hugging Face (`FlyRank/internship-warehouse`).
* **The Specific Technical Question:**  
  Does our Random Forest's 2.83x Precision@50 lift hold across rolling 30-day forward evaluation windows during broad Google core search updates, or do feature importance weights shift significantly between evergreen content and news-driven publishers?
* **Planned Addition Date:** **April 15, 2026** (aligned with Week 7/8 modeling milestones).

---

## 3. Evidence of Concrete Reminder Set

A recurring calendar reminder has been created to prevent the platform from going stale:

* **Platform:** Google Calendar / Mobile Alert
* **Event Title:** `Portfolio Update: Add 79M Warehouse Case Study`
* **Scheduled Date:** **Wednesday, April 15, 2026 at 10:00 AM IST**
* **Notifications:** 1 day before (email) + morning of (push notification)
* **Checklist in Description:**
  - [ ] Pull warehouse evaluation metrics from DuckDB queries on March/April 2026 data.
  - [ ] Open Claude Project `FlyRank ML & Fluency Workspace` and run 3-beat interview.
  - [ ] Add new case study block to `docs/index.html`.
  - [ ] Commit and push to GitHub (`git push origin main`).

---

## 4. Preserved AI Workspace Context

The Claude Project configured in Week 1 remains active and standing with the following permanent instructions:
* **Persona:** Senior Applied ML Advisor and tough editorial tutor.
* **Standing Voice Card:** *"Direct, plain, rigorous, curious, unpretentious, specific."*
* **Non-Negotiables:** Never use fluff ("passionate", "synergy", "spearheaded"); enforce observational language; demand concrete client-holdout metrics.

Because this context is stored permanently, drafting future case studies requires zero context re-initialization—turning a multi-hour writing chore into a 15-minute dialogue.
