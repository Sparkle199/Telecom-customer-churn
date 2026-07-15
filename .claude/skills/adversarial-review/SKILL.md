---
name: adversarial-review
description: Challenges assumptions and probes for edge cases, data leakage, and overfitting risk. Use for extra rigor on model validity before trusting results.
---

# Adversarial / Red-Team Review

## Procedure

1. Attempt to break the stated assumptions of the analysis or model.
2. Enumerate edge cases and adverse inputs (missing values, rare
   categories, distribution shift between train/test, class imbalance).
3. Probe specifically for overfitting and data leakage: does performance
   hold on a genuinely held-out split? Does any feature encode the target?
4. Report findings as a ranked list of concerns.

## Rules

- Findings only; do not silently "fix" what it finds — report and let the
  next step (developer/planner) decide.
- Be concrete: name the specific feature, split, or metric in question.
