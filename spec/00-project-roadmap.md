# Spec: Telecom customer churn project roadmap

Date: 2026-07-15

## Goal

Go from the raw Telco churn CSV to a documented, evaluated churn model with
insights that could inform a retention program.

## What

An end-to-end churn analysis and modelling project using
`WA_Fn-UseC_-Telco-Customer-Churn.csv` (see `dataset-details.txt` for the
source/context): EDA, cleaning, feature engineering, a baseline model,
one or two stronger models, honest evaluation, and a written summary of
findings.

## Why

Bootcamp side project — practice the full workflow (not just model fitting)
and produce something explainable end to end.

## Constraints

- Single public/synthetic dataset (IBM sample data via Kaggle), 7,043 rows,
  21 columns, target column `Churn`. No real customer PII — no compliance
  role needed.
- Python/pandas/scikit-learn stack assumed; adjust in `spec/` if that changes.
- Known data quirks to handle, not ignore:
  - `TotalCharges` is stored as a string and has 11 blank rows (all appear
    to be new customers with `tenure == 0`) — needs numeric conversion and
    an explicit decision on those rows.
  - `customerID` is a unique identifier — must be excluded as a feature.
  - Several "No internet service" / "No phone service" categorical values
    are functionally the same as "No" for modelling purposes — decide once,
    document it, apply consistently.

## Risks

- **Class imbalance**: churn is ~26.5% positive (1,869 Yes / 5,174 No).
  Accuracy alone will be misleading — plan metrics around this from the
  start, not after training a model that looks good and isn't.
- **Leakage**: `customerID` or any post-hoc feature that encodes the
  outcome must not enter the model. Flag via the `risk-assessor` skill
  before training.
- **Overfitting on categorical-heavy data**: many low-cardinality
  categorical columns after one-hot encoding — watch train/test gap,
  use the `adversarial-review` skill before trusting a result.
- **Small dataset for deep tuning**: 7k rows is enough for classic ML but
  not for elaborate model search — don't over-invest in marginal tuning
  gains at the expense of interpretation and writeup.

## Success / acceptance criteria

- A clean, reproducible pipeline from raw CSV to trained model (script or
  notebook, either is fine, but it must run top to bottom without manual
  patching).
- Evaluation reported with precision/recall/F1 and ROC-AUC (or PR-AUC)
  per class, not just accuracy, on a genuinely held-out split.
- A short written interpretation: which factors are associated with churn,
  and what that would mean for a retention program (ties back to the
  dataset's stated "Inspiration").
- Journal entries in `journal/agent-journal.md` for each phase below.

## Tasks

Each phase should get its own short spec in `spec/` before implementation
(use `spec/TEMPLATE.md`) if it's non-trivial — this file is the map, not a
substitute for a phase spec. Suggested skill to lead each phase in
parentheses.

- [ ] **1. Environment & structure** — confirm the Python environment,
      add `requirements.txt`, agree on notebook vs. script layout.
      (`planner`, `developer`)
- [ ] **2. EDA** — load the CSV, check dtypes/missing values, target
      balance, distributions of tenure/charges, relationship between each
      feature group (services, account info, demographics) and `Churn`.
      (`data`)
- [ ] **3. Cleaning** — fix `TotalCharges` dtype and blanks, decide and
      document the "No X service" → "No" collapsing, drop `customerID`.
      (`developer`, `risk-assessor` to sign off the leakage-relevant calls)
- [ ] **4. Feature engineering** — encode categoricals, consider tenure
      buckets/contract interactions, decide on scaling. (`developer`)
- [ ] **5. Train/test split** — stratified split on `Churn`; the test set
      is untouched until final evaluation. (`risk-assessor`)
- [ ] **6. Baseline model** — logistic regression (or similar) to set a
      floor before anything fancier. (`developer`)
- [ ] **7. Model iteration** — tree-based/ensemble model(s), handle
      imbalance explicitly (`class_weight`, threshold tuning, or
      resampling — pick one and justify it), cross-validate. (`developer`,
      `adversarial-review` to probe overfitting/leakage)
- [ ] **8. Evaluation** — confusion matrix, precision/recall/F1, ROC-AUC;
      state the metric that matters most for a retention use case and why.
      (`test`)
- [ ] **9. Interpretation** — feature importance (or coefficients/SHAP),
      translate top drivers into plain-language retention insights.
      (`data`)
- [ ] **10. Writeup** — update `README.md` with results and how to
      reproduce them; final `review` pass and `reflection` journal entry.
      (`review`, `reflection`)
- [ ] **11. Stretch (optional)** — simple predict script/API or dashboard
      demo. Only if time allows; not required for success criteria above.
