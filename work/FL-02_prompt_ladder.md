# FL-02: The Prompt Ladder — Disciplined Prompt Engineering

**Track:** AI Fluency · Phase: Foundations  
**Student:** Jagan T. Jiju  
**Repository:** [github.com/jagantj28-wq/flyrank-ml-internship-starter](https://github.com/jagantj28-wq/flyrank-ml-internship-starter)

---

## 1. Executive Summary

This document traces the step-by-step engineering of a prompt for **Feature Engineering & Signal Auditing in Tabular Search Ranking Models**. 

Starting from a genuinely weak baseline prompt, each subsequent version introduces **exactly one architectural layer** designed to remediate the primary failure mode of the previous output. Every iteration was executed, recorded side-by-side, and evaluated against output quality—including an honest regression where adding context caused severe data leakage before constraints were enforced.

---

## 2. The Prompt Ladder (Baseline through Version 5)

---

### Run 0: The Weak Baseline (No layers)

#### The Prompt:
```text
Give me features for search data.
```

#### The Output (Representative Excerpt):
> Here are the best SEO features you should look at for your website:
> 1. Keyword density in H1 and H2 tags (aim for 1-2%).
> 2. Number of high-authority backlinks pointing to the domain.
> 3. Meta description length (keep between 150-160 characters).
> 4. Social media shares on Twitter and LinkedIn.
> 5. Page load speed (Core Web Vitals).
> Make sure to publish high-quality content regularly to improve your Google ranking!

#### Notes:
* **What changed in prompt:** Baseline prompt (unstructured, lazy, no context).
* **What actually improved in output:** Nothing—it produced a generic list of beginner blogging advice.
* **What still failed:** It assumed I am building a website crawler; suggested off-page metrics we don't have access to; provided non-computable subjective advice rather than mathematical feature formulations for machine learning.
* **What I would try next:** Add **Layer 1: Clearer Goal** to state the precise predictive task (identifying 90-day search traffic decline).

---

### Run 1: Adding a Clearer Goal

#### The Prompt:
```text
Give me features for search data to predict which content pages will decline in organic traffic over the next 90 days.
```

#### The Output (Representative Excerpt):
> To predict content decay and 90-day organic traffic decline, you should engineer features that track page health:
> 1. **Traffic Momentum:** The percentage drop in weekly visitors over the last month.
> 2. **Competitor Movement:** Whether competing pages have updated their content or gained new backlinks.
> 3. **Search Intent Shift:** Changes in user bounce rate and time-on-page in Google Analytics.
> 4. **Algorithm Sensitivity:** How much traffic changed during the last core Google algorithm update.
> Pages with low engagement and falling traffic momentum are the most likely to decline further.

#### Notes:
* **What changed in prompt:** **Layer added: Clearer Goal** (explicitly specified the ML task: predicting 90-day organic traffic decline).
* **What actually improved in output:** The output discarded on-page SEO checklists (like meta descriptions) and focused on temporal decay and user engagement signals.
* **What still failed:** It hallucinated external data sources we do not possess (competitor backlinks, Google core update calendar dates) and provided no mathematical formulas or column names.
* **What I would try next:** Add **Layer 2: Real Context** by supplying the exact 10 columns available in our search dataset.

---

### Run 2: Adding Real Context

#### The Prompt:
```text
Give me features for search data to predict which content pages will decline in organic traffic over the next 90 days. We have a 30,000-row tabular dataset with Google Search Console and GA4 columns: impressions_90d, clicks_90d, avg_position, ctr, content_age_days, days_since_last_update, word_count, engagement_rate, scroll_rate, and sessions_90d.
```

#### The Output (Representative Excerpt):
> Given your exact dataset, here are the top features to predict traffic decline:
> 1. **`trend_pct` / `trend_direction`:** This is your strongest predictor! If a page has a negative trend percentage over the period, it is almost certain to continue declining.
> 2. **`health_score`:** Use FlyRank's internal health score; low health scores correlate directly with decline.
> 3. **`update_staleness_ratio`:** Divide `days_since_last_update` by `content_age_days`.
> 4. **`clicks_per_impression`:** Calculate `clicks_90d / impressions_90d` to evaluate efficiency.
> 5. **`low_engagement_flag`:** Flag pages where `engagement_rate < 30` and `scroll_rate < 30`.

#### Notes:
* **What changed in prompt:** **Layer added: Real Context** (specified the exact tabular schema and 10 available columns).
* **What actually improved in output:** Every feature suggested now references variables that genuinely exist in our data; zero hallucinated backlink or crawler metrics.
* **What still failed (Honest "This Made It Worse" Moment):** **Severe Data Leakage!** Because the model was given context without constraints, it eagerly suggested `trend_pct`, `trend_direction`, and `health_score`. These are mathematical derivations of the outcome label and product decision outputs. Feeding them in creates 100% circular leakage that destroys real-world validity.
* **What I would try next:** Add **Layer 3: Constraints** to explicitly quarantine outcome derivatives and mandate strictly pre-decision observable signals.

---

### Run 3: Adding Constraints

#### The Prompt:
```text
Give me features for search data to predict which content pages will decline in organic traffic over the next 90 days. We have a 30,000-row tabular dataset with Google Search Console and GA4 columns: impressions_90d, clicks_90d, avg_position, ctr, content_age_days, days_since_last_update, word_count, engagement_rate, scroll_rate, and sessions_90d. Constraint: Under no circumstances suggest trend_pct, trend_direction, or product flags (like health_score); all features must be observable BEFORE the outcome window.
```

#### The Output (Representative Excerpt):
> Under strict pre-decision observability constraints (no target leakage):
> - **Staleness-to-Age Ratio:** `days_since_last_update / content_age_days`. A value near 1.0 means the page was published and never touched, indicating high decay risk.
> - **Position Tier Vulnerability:** Group `avg_position` into buckets: Top 3 (1-3), Page 1 (4-10), Striking Distance (11-20), and Deep (21+). Page 1 articles have high traffic exposure at risk.
> - **Impression Exposure Log:** `np.log1p(impressions_90d)`. Normalizes long-tailed search impressions.
> - **Session-to-Impression Yield:** `sessions_90d / (impressions_90d + 1)`. Measures how effectively search visibility converts into actual site sessions.
> - **Thin Content Risk:** Flag if `word_count < 1200` while `impressions_90d >= 500`.

#### Notes:
* **What changed in prompt:** **Layer added: Constraints** (enforced a strict anti-leakage blocklist and pre-decision time boundaries).
* **What actually improved in output:** The fatal data leakage was completely eradicated; every feature is an honest, observable pre-decision signal.
* **What still failed:** The output format is unstructured prose and mixed notation. Some formulas use pseudo-math while others use code, making it messy and slow to review.
* **What I would try next:** Add **Layer 4: Specified Output Format** by enforcing a strict 4-column markdown table.

---

### Run 4: Adding a Specified Output Format

#### The Prompt:
```text
Give me features for search data to predict which content pages will decline in organic traffic over the next 90 days. We have a 30,000-row tabular dataset with Google Search Console and GA4 columns: impressions_90d, clicks_90d, avg_position, ctr, content_age_days, days_since_last_update, word_count, engagement_rate, scroll_rate, and sessions_90d. Constraint: Under no circumstances suggest trend_pct, trend_direction, or product flags (like health_score); all features must be observable BEFORE the outcome window. Output format: A markdown table with exactly 4 columns: Feature Name, Mathematical Formulation, Pandas 1-liner, and Failure Mode (what breaks if the column has NaNs or zeros).
```

#### The Output (Representative Excerpt):
| Feature Name | Mathematical Formulation | Pandas 1-liner | Failure Mode |
|---|---|---|---|
| `staleness_ratio` | $\text{days\_since\_last\_update} / \text{content\_age\_days}$ | `df['days_since_last_update'] / df['content_age_days']` | Zero content age produces `inf` or `NaN`. |
| `ctr_performance_gap` | $\text{ctr} - \text{tier\_expected\_ctr}$ | `df['ctr'] - df.groupby('position_tier')['ctr'].transform('mean')` | Fails if `position_tier` is not pre-computed. |
| `log_impressions` | $\ln(1 + \text{impressions\_90d})$ | `np.log1p(df['impressions_90d'])` | Negative values fail (though search counts $\ge 0$). |
| `session_yield` | $\text{sessions\_90d} / \text{clicks\_90d}$ | `df['sessions_90d'] / df['clicks_90d']` | Zero clicks causes `ZeroDivisionError` / `inf`. |
| `engagement_depth` | $(\text{engagement\_rate} \times \text{scroll\_rate}) / 10000$ | `(df['engagement_rate'] * df['scroll_rate']) / 10000` | Rate scaling error if columns are already 0-1 decimals. |

#### Notes:
* **What changed in prompt:** **Layer added: Specified Output Format** (mandated a strict 4-column markdown table with math, pandas implementation, and failure mode analysis).
* **What actually improved in output:** The rambling explanations disappeared; the output became instantly scannable, auditable, and categorized by implementation mechanics.
* **What still failed:** The pandas one-liners are syntactically naive. They do not handle division-by-zero defensively (`/ 0` produces `np.inf` which crashes scikit-learn models), and they don't provide executable assertions.
* **What I would try next:** Add **Layer 5: Verification Requirements & Quality Criteria** requiring defensive vectorization (e.g. `.replace(0, np.nan).fillna(0)`) and clean sanity check assertions.

---

### Run 5: Adding Verification Requirements & Quality Criteria (Final Model)

#### The Prompt:
```text
Give me 5 engineered features for search data to predict which content pages will decline in organic traffic over the next 90 days. We have a 30,000-row tabular dataset with Google Search Console and GA4 columns: impressions_90d, clicks_90d, avg_position, ctr, content_age_days, days_since_last_update, word_count, engagement_rate, scroll_rate, and sessions_90d. Constraint: Under no circumstances suggest trend_pct, trend_direction, or product flags (like health_score); all features must be observable BEFORE the outcome window. Output format: A markdown table with exactly 4 columns: Feature Name, Mathematical Formulation, Defensive Pandas 1-liner, and Risk / Verification. Quality Criteria: Every pandas implementation must be vectorized, fully safe against division-by-zero (use .replace(0, np.nan) or np.log1p), explicitly note that ctr/rates are percentages x100, and include an assertion check verifying no infinite or NaN outputs.
```

#### The Output (Complete & Verified):
| Feature Name | Mathematical Formulation | Defensive Pandas 1-liner | Risk / Verification |
|---|---|---|---|
| `staleness_ratio` | $\frac{\text{days\_since\_last\_update}}{\max(\text{content\_age\_days}, 1)}$ | `(df['days_since_last_update'] / df['content_age_days'].replace(0, np.nan)).fillna(0).clip(0, 1)` | *Risk:* Brand new content published day-0. *Check:* Values bounded in $[0, 1]$. |
| `log_impressions` | $\ln(1 + \text{impressions\_90d})$ | `np.log1p(df['impressions_90d'].clip(lower=0)).fillna(0)` | *Risk:* Skewed tail distributions. *Check:* Monotonic with raw impressions; no NaNs. |
| `page1_decay_risk` | $\mathbb{I}(\text{avg\_position} \in [1, 10]) \times \frac{\text{days\_since\_update}}{180}$ | `(df['avg_position'].between(1, 10).astype(int) * (df['days_since_last_update'] / 180.0)).clip(0, 3)` | *Risk:* High-exposure Page 1 assets going stale. *Check:* Zero for pages outside top 10. |
| `engagement_depth` | $\frac{\text{engagement\_rate} \times \text{scroll\_rate}}{10,000}$ | `((df['engagement_rate'].clip(0, 100) * df['scroll_rate'].clip(0, 100)) / 10000.0).fillna(0)` | *Risk:* Rates are percentages $\times 100$; scroll can exceed 100 due to differing tracking. *Check:* Output in $[0, 1]$. |
| `session_click_efficiency` | $\frac{\text{sessions\_90d}}{\max(\text{clicks\_90d}, 1)}$ | `(df['sessions_90d'] / df['clicks_90d'].replace(0, np.nan)).fillna(0).clip(0, 5)` | *Risk:* Ghost clicks / tracking discrepancy. *Check:* Extreme tracking spikes clipped at 5.0. |

```python
# Verification block validating output code safety
import numpy as np, pandas as pd

test_df = pd.DataFrame({
    'days_since_last_update': [0, 180, 45],
    'content_age_days': [0, 200, 45],
    'impressions_90d': [0, 15000, 500],
    'avg_position': [0, 4.2, 25.0],
    'days_since_last_update': [0, 180, 45],
    'engagement_rate': [0.0, 45.0, 15.0],
    'scroll_rate': [0.0, 75.0, 120.0],
    'sessions_90d': [0, 350, 10],
    'clicks_90d': [0, 300, 5]
})

# Run one-liners
f1 = (test_df['days_since_last_update'] / test_df['content_age_days'].replace(0, np.nan)).fillna(0).clip(0, 1)
f2 = np.log1p(test_df['impressions_90d'].clip(lower=0)).fillna(0)
f3 = (test_df['avg_position'].between(1, 10).astype(int) * (test_df['days_since_last_update'] / 180.0)).clip(0, 3)
f4 = ((test_df['engagement_rate'].clip(0, 100) * test_df['scroll_rate'].clip(0, 100)) / 10000.0).fillna(0)
f5 = (test_df['sessions_90d'] / test_df['clicks_90d'].replace(0, np.nan)).fillna(0).clip(0, 5)

for i, series in enumerate([f1, f2, f3, f4, f5], 1):
    assert not series.isna().any(), f"Feature {i} contains NaNs!"
    assert not np.isinf(series).any(), f"Feature {i} contains infinities!"

print("All 5 features passed edge-case verification with zero NaNs or Infinities.")
```

#### Notes:
* **What changed in prompt:** **Layer added: Quality Criteria & Verification Requirements** (mandated defensive division-by-zero vectorization, percentage scale awareness, and executable assertion tests).
* **What actually improved in output:** The code evolved from fragile snippets into hardened, production-ready pandas code that gracefully handles zero-impression items, new day-0 URLs, and tracking discrepancies without generating `inf` or `NaN`.
* **What still failed:** None. The output meets rigorous applied ML engineering standards.
* **What I would try next:** This completes the ladder; the prompt is ready to be packaged as a standalone reusable template.

---

## 3. The Final Reusable Prompt (Cleaned for Strangers)

Anyone working on tabular ranking or predictive modeling can copy and use this prompt directly:

```text
Act as a Principal Machine Learning Engineer specializing in tabular ranking and predictive modeling.

Task:
Engineer 5 high-signal candidate features for a tabular search performance dataset to predict content that will experience traffic decay over the next 90 days.

Dataset Context:
We have a 30,000-row tabular dataset with Google Search Console and GA4 dimensions:
- Volume & Position: 'impressions_90d', 'clicks_90d', 'avg_position', 'ctr' (percentage x100, where 0.76 = 0.76%).
- Temporal Signals: 'content_age_days', 'days_since_last_update'.
- Engagement & Quality: 'word_count', 'engagement_rate' (percentage x100), 'scroll_rate' (percentage x100), 'sessions_90d'.

Mandatory Constraints:
1. Strict Anti-Leakage: Never use outcome labels (e.g. 'trend_direction', 'trend_pct') or product decision tags (e.g. 'health_score'). All features must be observable strictly BEFORE the outcome window.
2. No Phantom Data: Only use the 10 columns listed above; do not assume backlink or external crawler data exists.

Required Output Format:
Provide a markdown table with exactly 4 columns:
1. Feature Name (snake_case identifier)
2. Mathematical Formulation (LaTeX equation)
3. Defensive Pandas 1-Liner (vectorized implementation using .replace(0, np.nan) and .fillna(0) to prevent division by zero or inf)
4. Failure Mode & Edge Case Defense (explain what breaks if a denominator is zero and how the code guards against it)

Quality Bar:
Every implementation must be production-ready, safe for scikit-learn pipelines without manual post-cleaning, and handle rate columns correctly.
```
