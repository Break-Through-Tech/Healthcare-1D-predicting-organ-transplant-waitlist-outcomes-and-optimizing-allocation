# Issue #16: Create model

This is a detailed completion report for [Issue 16](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/issues/16). Try out classification models for waitlist outcome and waitlist duration, starting with a logistic regression baseline.

## Outcome
We built a logistic regression baseline predicting `event_adverse` (death or removal as too sick). The model captures real but modest signal: it meets the Brier score target and falls short of the ROC-AUC, PR-AUC, and top-decile lift targets. These results set the bar the tree ensemble models need to beat.

## Work Completed
**Data.** Still-waiting candidates and `unknown` outcomes were excluded because their final outcome is not observed. Labeling still-waiting candidates as 0 would teach the model that patients with short follow-up are low risk.

**Features.** All five target columns (`outcome`, `event_adverse`, `event_transplant`, `censored`, `days_to_event`) were dropped to prevent leakage. `DIALYSIS_DURATION_LOG` was dropped because it is redundant with `DIALYSIS_DURATION`, and keeping both would split credit between their coefficients.

**Model.** Stratified 80/20 train/test split with `random_state=42`, so later models can use the identical split.

**Results.** ROC-AUC 0.698 (target ≥ 0.78), PR-AUC 0.313 (target ≥ 0.65), Brier score 0.137 (target ≤ 0.18), and top-decile lift 2.08× (target ≥ 2.5×). The Brier target is met, but a no-skill model also scores below 0.18 on this data (0.148), so the improvement is small.

**Interpretation.** Coefficients were converted to odds ratios. The strongest risk factors are age (1.90), dialysis at listing (1.80), and blood type B (1.54). The strongest protective factors are A2/A2B eligibility (0.26) and the pediatric functional scale (0.42). Blood type and regional effects likely reflect allocation patterns rather than patient health and are flagged for the fairness audit. `POLICY_ERA` is not interpreted as a policy effect because it also reflects shorter follow-up for recent listings.

**Scope changes.** KNN and decision trees were replaced by Random Forest and XGBoost, per the project plan. KNN is slow at this data size and not interpretable, and a single decision tree is outperformed by tree ensembles. Waitlist duration moves to the survival modeling task (Cox and competing risks), which handles still-waiting candidates properly.

**Known follow-up.** Results will be rerun after merging main, since the encoding notebook now outputs unscaled data and scaling moves into this notebook.

## Files Delivered
| Area | Exact file snapshot | Why is it included |
|---|---|---|
| Logistic regression notebook | logistic-regression.ipynb | Baseline model, evaluation against project targets, and odds ratio interpretation |
