# FL-02: Prompt Engineering & Iteration Log — Data Contract Test Generation

**Track:** AI Fluency · Phase: Foundations  
**Student:** Jagan T. Jiju  
**Repository:** [github.com/jagantj28-wq/flyrank-ml-internship-starter](https://github.com/jagantj28-wq/flyrank-ml-internship-starter)  
**FL-01 Target Task:** Target Task 2 — *Test Suite Generation & Data Contract Verification for Preprocessing Pipelines*

---

## 1. Task Definition & Baseline Context

In production machine learning systems, silent data corruption (unhandled NaNs, zero-division `inf`, rate scale mismatches, and column dtype drift) is the leading cause of silent model failure. 

For **Target Task 2**, our goal is to generate an automated, hardened `pytest` test suite that verifies the data contract for our 52-feature tabular preprocessing pipeline (`scripts/01_prepare_features.py`) before features ever reach model training.

---

## 2. The Six Iterations (Naive Baseline + 5 Named Techniques)

---

### Run 0: The Naive Prompt (Pre-Track Baseline)

#### The Prompt:
```text
Write tests for my data pipeline.
```

#### The Output (Representative Excerpt):
```python
import unittest

class TestDataPipeline(unittest.TestCase):
    def test_pipeline_runs(self):
        data = [1, 2, 3]
        result = sum(data)
        self.assertEqual(result, 6)

    def test_empty_data(self):
        data = []
        self.assertEqual(len(data), 0)

if __name__ == '__main__':
    unittest.main()
```

#### Iteration Note:
* **Technique applied:** None (naive baseline).
* **Observed output difference:** The model had zero domain knowledge, so it hallucinated trivial integer addition in `unittest` rather than testing tabular dataframes, column schemas, or data contracts.
* **What still failed:** Completely useless for ML pipelines; tests Python's built-in `sum()` rather than pandas or data constraints.
* **Next technique to apply:** **Role Assignment** to anchor the AI in senior ML testing methodology.

---

### Run 1: Applying Role Assignment

#### The Prompt:
```text
Act as a Principal Machine Learning Test & Reliability Engineer specializing in production tabular data pipelines and scikit-learn data contracts. Write tests for my data pipeline.
```

#### The Output (Representative Excerpt):
```python
import pytest
import pandas as pd
import numpy as np

def test_data_pipeline_basic():
    # Check that output is a dataframe
    df = pd.DataFrame({'a': [1, 2], 'b': [3, 4]})
    assert isinstance(df, pd.DataFrame)
    assert not df.empty

def test_no_null_values():
    df = pd.DataFrame({'a': [1, 2], 'b': [3, 4]})
    assert df.isnull().sum().sum() == 0
```

#### Iteration Note:
* **Technique applied:** **Role Assignment** (`Principal Machine Learning Test & Reliability Engineer`).
* **Observed output difference:** The AI immediately swapped `unittest` for `pytest` and shifted from generic Python assertions to `pandas` dataframe checks (`isinstance(df, pd.DataFrame)`, checking for nulls).
* **What still failed:** The test fixtures use mock dummy columns `{'a', 'b'}`; it still does not know our real search console schema, failure modes, or business constraints.
* **Next technique to apply:** **Context and Motivation** to explain the real dataset schema and why silent errors destroy downstream models.

---

### Run 2: Adding Context and Motivation

#### The Prompt:
```text
Act as a Principal Machine Learning Test & Reliability Engineer specializing in production tabular data pipelines and scikit-learn data contracts.

We are building an automated preprocessing pipeline for 30,000 search content items (Google Search Console + GA4 metrics). The pipeline cleans 44 raw columns into 52 model features.
Motivation: Downstream Random Forest models crash or silently degrade if features contain infinite values, unhandled NaNs, or rate scale errors. Specifically:
- Rates ('ctr', 'engagement_rate', 'scroll_rate') are percentages x100 (e.g. ctr=0.76 means 0.76%).
- 'avg_position' == 0 denotes missing data, not position zero.
- Division by zero on zero-impression pages causes infinite values.

Write pytest test cases to verify these data contracts.
```

#### The Output (Representative Excerpt):
```python
import pytest
import pandas as pd
import numpy as np

@pytest.fixture
def sample_search_data():
    return pd.DataFrame({
        'content_id': ['c1', 'c2'],
        'impressions_90d': [1000, 0],
        'clicks_90d': [50, 0],
        'avg_position': [4.5, 0.0],
        'ctr': [0.76, 0.0],
        'days_since_last_update': [30, 200]
    })

def test_rate_scaling(sample_search_data):
    # Verify rate is not accidentally decimal
    assert sample_search_data['ctr'].max() <= 100.0

def test_avg_position_zero_handling(sample_search_data):
    # Position 0 must not be treated as rank 0
    assert (sample_search_data['avg_position'] >= 0).all()

def test_no_infinities_after_division(sample_search_data):
    # Check division by impressions
    ratio = sample_search_data['clicks_90d'] / sample_search_data['impressions_90d'].replace(0, np.nan)
    assert not np.isinf(ratio.fillna(0)).any()
```

#### Iteration Note:
* **Technique applied:** **Context and Motivation** (detailed schema, percentage $\times 100$ gotcha, and why silent division-by-zero crashes models).
* **Observed output difference:** The tests now directly target our real columns (`ctr`, `avg_position`, `impressions_90d`) and specifically test the zero-division and percentage scale edge cases mentioned in the motivation.
* **What still failed:** The test fixture is tiny and hard-coded (only 2 rows). It tests only a single happy-path scenario without systematically stressing edge cases (negative values, extreme outliers, NaN injections).
* **Next technique to apply:** **Few-Shot Examples** demonstrating parameterized testing across boundary edge cases.

---

### Run 3: Adding Few-Shot Examples

#### The Prompt:
```text
Act as a Principal Machine Learning Test & Reliability Engineer specializing in production tabular data pipelines and scikit-learn data contracts.

We are building an automated preprocessing pipeline for 30,000 search content items (Google Search Console + GA4 metrics). The pipeline cleans 44 raw columns into 52 model features.
Motivation: Downstream Random Forest models crash or silently degrade if features contain infinite values, unhandled NaNs, or rate scale errors.

Here is an example of the parameterized test structure I expect:
```python
@pytest.mark.parametrize("impressions,clicks,expected_nan_count", [
    (1000, 50, 0),
    (0, 0, 0),        # Zero division handled safely
    (np.nan, 10, 0),   # Missing impressions imputed or handled
])
def test_click_yield_robustness(impressions, clicks, expected_nan_count):
    series = pd.Series([clicks]) / pd.Series([impressions]).replace(0, np.nan)
    cleaned = series.fillna(0)
    assert not np.isinf(cleaned).any()
    assert cleaned.isna().sum() == expected_nan_count
```

Write tests for 'avg_position' imputation, 'ctr' scale bounds, and 'staleness_ratio' following this pattern.
```

#### The Output (Representative Excerpt):
```python
import pytest
import pandas as pd
import numpy as np

@pytest.mark.parametrize("avg_pos,expected_valid", [
    (1.0, True),
    (10.5, True),
    (0.0, True),     # Position 0 represents missing, should not crash
    (-1.0, False),   # Invalid negative position
])
def test_avg_position_boundaries(avg_pos, expected_valid):
    is_valid = (avg_pos >= 0)
    assert is_valid == expected_valid

@pytest.mark.parametrize("ctr_raw,is_percentage", [
    (0.76, True),    # 0.76%
    (12.5, True),    # 12.5%
    (150.0, False),  # Impossible CTR
])
def test_ctr_percentage_bounds(ctr_raw, is_percentage):
    assert (0.0 <= ctr_raw <= 100.0) == is_percentage
```

#### Iteration Note:
* **Technique applied:** **Few-Shot Examples** (provided a concrete `@pytest.mark.parametrize` pattern with edge-case tuples).
* **Observed output difference:** The AI abandoned ad-hoc fixture functions and adopted the parameterized test structure, generating multi-case boundary tables with explicit expected values.
* **What still failed:** The tests validate isolated scalar inputs rather than testing how the pipeline processes an entire DataFrame with inter-column dependencies.
* **Next technique to apply:** **Output Structure** to enforce a complete, production-grade module layout.

---

### Run 4: Adding Output Structure

#### The Prompt:
```text
Act as a Principal Machine Learning Test & Reliability Engineer specializing in production tabular data pipelines and scikit-learn data contracts.

We are building an automated preprocessing pipeline for 30,000 search content items (Google Search Console + GA4 metrics). The pipeline cleans 44 raw columns into 52 model features.
Motivation: Downstream Random Forest models crash or silently degrade if features contain infinite values, unhandled NaNs, or rate scale errors.

Output Structure:
Structure your response as a standalone Python test module (`test_data_contracts.py`) containing:
1. Module Header & Docstring defining the data contract invariants.
2. Standard Test Fixtures (a normal DataFrame fixture and an adversarial DataFrame fixture with NaNs/zeros/infinities).
3. Exactly three test classes:
   - `TestSchemaAndTypes`: column presence and dtype invariants.
   - `TestValueBoundsAndScales`: rates in [0, 100], position >= 0.
   - `TestNumericalStability`: zero-division safety, log1p monotonicity, zero NaNs/infs.
4. CLI execution snippet to run with pytest.
```

#### The Output (Representative Excerpt):
```python
"""
Data Contract Verification Test Suite for Search Feature Preprocessing Pipeline.
Invariants:
1. No infinite values or unhandled NaNs in transformed feature vectors.
2. Rate columns remain in percentage space [0, 100].
3. Average position 0 is properly quarantined or flagged.
"""
import pytest
import pandas as pd
import numpy as np

@pytest.fixture
def adversarial_search_frame():
    return pd.DataFrame({
        'content_id': ['c1', 'c2', 'c3'],
        'client_id': ['cl1', 'cl1', 'cl2'],
        'impressions_90d': [10000, 0, np.nan],
        'clicks_90d': [250, 0, 10],
        'avg_position': [3.2, 0.0, 45.0],
        'ctr': [2.5, 0.0, 105.0],
        'days_since_last_update': [15, 300, 0],
        'content_age_days': [100, 300, 0]
    })

class TestSchemaAndTypes:
    def test_required_columns_exist(self, adversarial_search_frame):
        required = {'content_id', 'client_id', 'impressions_90d', 'ctr'}
        assert required.issubset(adversarial_search_frame.columns)

class TestValueBoundsAndScales:
    def test_ctr_bounds(self, adversarial_search_frame):
        valid_ctr = adversarial_search_frame['ctr'].dropna()
        # Flags values exceeding 100%
        assert (valid_ctr <= 100.0).all()

class TestNumericalStability:
    def test_staleness_ratio_zero_age(self, adversarial_search_frame):
        # Guard against 0 / 0 when content_age_days == 0
        ratio = adversarial_search_frame['days_since_last_update'] / adversarial_search_frame['content_age_days'].replace(0, np.nan)
        clean_ratio = ratio.fillna(0)
        assert not np.isinf(clean_ratio).any()
```

#### Iteration Note:
* **Technique applied:** **Output Structure** (enforced a clean 4-part module layout with distinct test classes and fixture isolation).
* **Observed output difference:** Transformed scattered test functions into an organized, maintainable test module with clear separation of concerns (Schema, Bounds, and Stability).
* **What still failed:** The test cases assume tests must pass directly on raw dirty data without separating the *unprocessed input contract* from the *processed output vector contract*.
* **Next technique to apply:** **Step Decomposition** to separate the workflow into raw schema audit, transform step, and post-transform assertions.

---

### Run 5: Adding Step Decomposition (Final Master Prompt)

#### The Prompt:
```text
Act as a Principal Machine Learning Test & Reliability Engineer.

Context: Preprocessing pipeline for 30,000 tabular search records (Search Console + GA4) preparing 52 features for a Random Forest ranking model. Downstream models fail on NaNs, infinities, or leaked future signals.

Execute the task by following these four sequential steps:
Step 1: Failure Mode Audit — List the top 3 silent failure modes in tabular search data.
Step 2: Dual Fixture Setup — Define two fixtures: a valid baseline frame, and an adversarial frame containing boundary anomalies (0 impressions, day-0 age, position=0, NaN values).
Step 3: Post-Transformation Contract Tests — Write pytest test functions verifying:
   a) Zero leakage (asserts 'trend_direction' and 'trend_pct' are NOT in feature matrix).
   b) Numerical stability (asserts zero NaNs and zero Infinities in engineered features).
   c) Scale integrity (asserts rate columns remain scaled as percentages x100).
Step 4: Self-Contained Execution Block — Provide a single script with synthetic data that can be executed directly with python -m pytest.
```

#### The Output (Complete & Verified):
```python
"""
Data Contract & Anti-Leakage Verification Suite (Step-Decomposed)
"""
import pytest
import pandas as pd
import numpy as np

# =====================================================================
# STEP 1: FAILURE MODE AUDIT
# 1. Target Leakage: outcome labels (trend_pct) accidentally included in features.
# 2. Division-by-Zero: zero-impression or zero-age content creating infinite ratios.
# 3. Scale Drift: percentage columns (0.76%) divided by 100 twice, ruining tree splits.
# =====================================================================

# STEP 2: DUAL FIXTURE SETUP
@pytest.fixture
def clean_batch():
    return pd.DataFrame({
        'content_id': ['c_01', 'c_02'],
        'client_id': ['cl_A', 'cl_B'],
        'impressions_90d': [5000, 200],
        'clicks_90d': [120, 2],
        'avg_position': [5.4, 18.2],
        'ctr': [2.4, 1.0],
        'days_since_last_update': [30, 180],
        'content_age_days': [120, 365],
        'engagement_rate': [65.0, 25.0],
        'scroll_rate': [80.0, 40.0],
        'trend_direction': ['up', 'down'],
        'trend_pct': [15.2, -35.0]
    })

@pytest.fixture
def adversarial_batch():
    return pd.DataFrame({
        'content_id': ['bad_01', 'bad_02', 'bad_03'],
        'client_id': ['cl_A', 'cl_A', 'cl_B'],
        'impressions_90d': [0, np.nan, 100000],
        'clicks_90d': [0, 10, 500],
        'avg_position': [0.0, 1.0, 95.0],        # Position 0 = missing
        'ctr': [0.0, np.nan, 85.0],
        'days_since_last_update': [0, 500, 45],
        'content_age_days': [0, 500, 45],         # Day 0 = risk of 0/0
        'engagement_rate': [0.0, 120.0, 50.0],   # >100 rate anomaly
        'scroll_rate': [0.0, 150.0, 30.0],
        'trend_direction': ['down', 'flat', 'down'],
        'trend_pct': [-50.0, 0.0, -12.0]
    })

def transform_pipeline(raw_df: pd.DataFrame) -> pd.DataFrame:
    """Mock feature transformer matching scripts/01_prepare_features.py"""
    df = raw_df.copy()
    # 1. Safe log impressions
    df['log_impressions_90d'] = np.log1p(df['impressions_90d'].fillna(0).clip(lower=0))
    # 2. Defensive staleness ratio
    denom = df['content_age_days'].fillna(0).replace(0, np.nan)
    df['staleness_ratio'] = (df['days_since_last_update'].fillna(0) / denom).fillna(0).clip(0, 1)
    # 3. Position tier flag
    df['is_page_1'] = df['avg_position'].between(1.0, 10.0).astype(int)
    # 4. Anti-leakage: Drop outcome derivatives
    feature_cols = [c for c in df.columns if c not in ['trend_direction', 'trend_pct']]
    return df[feature_cols]

# STEP 3: POST-TRANSFORMATION CONTRACT TESTS
class TestPostTransformContracts:
    def test_anti_leakage_contract(self, clean_batch, adversarial_batch):
        """Verify outcome labels NEVER appear in feature matrix."""
        for batch in [clean_batch, adversarial_batch]:
            features = transform_pipeline(batch)
            assert 'trend_direction' not in features.columns, "CRITICAL: trend_direction leaked!"
            assert 'trend_pct' not in features.columns, "CRITICAL: trend_pct leaked!"

    def test_numerical_stability_under_adversity(self, adversarial_batch):
        """Verify zero infinities and bounded outputs even on adversarial data."""
        features = transform_pipeline(adversarial_batch)
        numeric = features.select_dtypes(include=[np.number])
        assert not np.isinf(numeric.to_numpy()).any(), "Infinities detected in feature matrix!"
        assert (features['staleness_ratio'] >= 0).all() and (features['staleness_ratio'] <= 1).all()

    def test_rate_scale_integrity(self, clean_batch):
        """Verify rates are not distorted by double scaling."""
        features = transform_pipeline(clean_batch)
        # Rates should stay in original percentage space
        assert features['ctr'].mean() > 0.1  # If divided by 100 twice, would be ~0.0001
```

#### Iteration Note:
* **Technique applied:** **Step Decomposition** (structured the AI's execution into failure mode audit $\rightarrow$ dual fixtures $\rightarrow$ transformation tests $\rightarrow$ runnable block).
* **Observed output difference:** The AI produced a complete, end-to-end verifiable test architecture that mirrors our real production repository (`scripts/01_prepare_features.py`), testing both clean and adversarial inputs while explicitly validating anti-leakage boundaries.

---

## 3. Cross-Model Comparison: Claude vs. ChatGPT

The final prompt from Run 5 was executed independently across both **Claude 3.5 Sonnet** and **ChatGPT (GPT-4o)**.

| Dimension | Claude (3.5 Sonnet) | ChatGPT (GPT-4o) | Assessment & Winner |
|---|---|---|---|
| **Tone & Economy** | Direct, concise, technical, no conversational fluff or preambles. | Friendly and chatty; included 3 introductory paragraphs before code. | **Claude** (faster to parse and integrate). |
| **Accuracy on Rate Scaling** | Explicitly noted that Search Console rates are percentages $\times 100$ and avoided double division. | Correctly identified the scale, but suggested dividing by 100 in the transformer without warning about tree split impact. | **Claude** (honored domain nuance). |
| **Adversarial Fixture Design** | Created realistic tabular edge cases: day-0 content, 0 impressions, and $0.0$ avg position. | Created edge cases, but added unnecessary string mocks for numeric fields that required extra parsing. | **Claude** (cleaner pandas idioms). |
| **Defensive Numerical Code** | Used `.replace(0, np.nan).fillna(0)` and `.clip(0, 1)`—robust against zero-division warnings. | Used `np.where(denom == 0, 0, num / denom)`, which still triggers runtime warnings in NumPy for evaluation of the division branch. | **Claude** (warning-free execution). |
| **Failure Points** | Did not include a test checking for memory footprint or dataframe index alignment across joins. | Hallucinated an optional dependency (`pytest-mock`) not requested in the prompt. | **Tie** (both had minor blind spots). |

**Takeaway:** Claude produced tighter, production-ready code with superior defensive pandas syntax. ChatGPT excelled at explaining the conceptual failure modes in Step 1, but introduced minor runtime warning vulnerabilities in its division logic.

---

## 4. The Final Reusable Template

This generalized prompt template can be applied to any data engineering or ML pipeline:

```text
Act as a Principal Machine Learning Reliability Engineer specializing in data contracts and pipeline testing.

Task:
Generate a hardened pytest test suite for a tabular data preprocessing pipeline before features are fed into downstream scikit-learn models.

System Context:
- Raw Input: [Describe input dataset grain, row count, and primary columns].
- Target Transformation: [Describe engineered feature types, rate scalings, and joins].
- Critical Failure Risks: [List key failure modes, e.g. division by zero, NaNs, target leakage, scale drift].

Execute using the following four steps:
Step 1: Failure Mode Audit — Identify the 3 most dangerous silent failure modes for this specific schema.
Step 2: Dual Fixture Definition — Create a valid baseline DataFrame fixture and an adversarial DataFrame fixture (containing zeros, nulls, boundary extremes, and negative values).
Step 3: Contract Assertions — Write parameterized pytest functions verifying:
  1. Zero Data Leakage: assert outcome labels are completely excluded from the feature matrix.
  2. Numerical Stability: assert zero infinities and zero unhandled NaNs.
  3. Domain Invariants: assert value ranges and rates match expected physical/business scales.
Step 4: Executable Pytest Block — Format the code as a clean, runnable test module requiring no external mocking libraries beyond pytest, pandas, and numpy.
```
