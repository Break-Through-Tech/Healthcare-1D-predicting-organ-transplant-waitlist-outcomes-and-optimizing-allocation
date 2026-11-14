# Issue #16: Create model

This is a detailed completion report for [Issue 16](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/issues/16). Try out classification models for waitlist outcome and waitlist duration, starting with a logistic regression baseline.

## Outcome
We built a logistic regression baseline predicting adverse events (death or removal as too sick) within 1,200 days of listing, aligned with the XGBoost runs so all models share the same test set. The model captures real but modest signal: it meets the Brier score target on the main cohort and falls short of the ROC-AUC, PR-AUC, and top-decile lift targets. XGBoost beats it by only a small, consistent margin.

## Work Completed
**Target.** A fixed 1,200-day landmark window: 1 if an adverse event happened within 1,200 days of listing, 0 otherwise. A fixed window avoids labeling still-waiting candidates as safe while keeping every candidate whose outcome over the window is known.

**Cohorts.** Two cohorts were run, as agreed on 11/7/2026. The main (corrected) cohort keeps candidates with a known non-adverse exit (transplanted, transplanted elsewhere, removed administratively) and drops only those still waiting or `unknown` before 1,200 days. The sensitivity (original) cohort also drops candidates transplanted or removed for other reasons before 1,200 days, which raises the adverse rate to 36%.

**Features.** All five target columns (`outcome`, `event_adverse`, `event_transplant`, `censored`, `days_to_event`) were dropped to prevent leakage. `DIALYSIS_DURATION_LOG` was dropped because it is redundant with `DIALYSIS_DURATION`, and keeping both would split credit between their coefficients.

**Model.** Stratified 80/20 train/test split with `random_state=42`. Numeric features were re-scaled after the split, with the scaler fit on the training set only. Patient counts match the XGBoost runs exactly (408,167 and 138,611), so all models share the same test set.

**Results.**

| Cohort | Patients | Adverse rate | ROC-AUC | PR-AUC | Brier | Lift |
|---|---|---|---|---|---|---|
| Main (corrected) | 408,167 | 12.2% | 0.678 | 0.208 | 0.103 | 2.04× |
| Sensitivity (original) | 138,611 | 36.0% | 0.753 | 0.634 | 0.188 | 2.14× |
| *Target* | | | ≥ 0.78 | ≥ 0.65 | ≤ 0.18 | ≥ 2.5× |

On the main cohort, PR-AUC is about 1.7× the no-skill value (0.122), and Brier only slightly beats no-skill (0.107). The sensitivity cohort's metrics look stronger, but PR-AUC relative to no-skill is nearly the same (~1.8×), so the gain comes from its higher adverse rate rather than better prediction.

**Interpretation.** Coefficients were converted to odds ratios on the main cohort. The strongest risk factors are age (1.87), dialysis at listing (1.57), inactive status at listing (1.35), and blood type B (1.34). The strongest protective factors are A2/A2B eligibility (0.33), the pediatric functional scale (0.45), and listing before starting dialysis (0.49). Blood type and regional effects likely reflect allocation patterns rather than patient health and are flagged for the fairness audit. `POLICY_ERA` (0.86) is not interpreted as a policy effect because it also reflects how recently candidates were listed.

**Scope changes.** KNN and decision trees were replaced by Random Forest and XGBoost, per the project plan. KNN is slow at this data size and not interpretable, and a single decision tree is outperformed by tree ensembles. 


## Files Delivered
| Area | Exact file snapshot | Why is it included |
|---|---|---|
| Logistic regression notebook | logistic-regression.ipynb | Landmark baseline on both cohorts, evaluation against project targets, and odds ratio interpretation |
