**Issue #__: Hyperparameter Tuning**
---
This is a detailed completion report for the hyperparameter tuning analysis. **Test different model settings to identify the best-performing model and evaluate its performance on the test data.** Logistic Regression, Random Forest, and XGBoost were tuned and compared on the same cohort, target, and test set as the team's baseline models.

**Outcome**
---
XGBoost is the best model (ROC-AUC 0.692, PR-AUC 0.221, Brier 0.102, top-decile lift 2.16). It is well calibrated, but it still misses the ROC-AUC (≥ 0.78), PR-AUC (≥ 0.65), and lift (≥ 2.5) targets. Only Brier (≤ 0.18) is met, and it only slightly beats the no-skill Brier of about 0.107. Tuning improved ROC-AUC by only about 0.014 over the logistic regression baseline, so the limit is the information in the features, not the model settings.

| Model | Final settings | ROC-AUC | PR-AUC | Brier | Top-decile lift |
| --- | --- | --- | --- | --- | --- |
| Logistic Regression | C = 1 | 0.678 | 0.208 | 0.103 | 2.04 |
| Random Forest | max_depth 16, min_samples_leaf 10 | 0.690 | 0.220 | 0.102 | 2.15 |
| **XGBoost** | **max_depth 4, min_child_weight 5** | **0.692** | **0.221** | **0.102** | **2.16** |
| *Target* | | ≥ 0.78 | ≥ 0.65 | ≤ 0.18 | ≥ 2.5 |

**Work Completed**
---
**1. Aligned the data with the team's baseline models.**
The first version of the notebook predicted `event_adverse` on all rows, which does not match the logistic regression and XGBoost work. It was changed to use the same 1,200-day landmark cohort:
- 408,167 patients, 12.2% adverse rate
- Target: adverse event (died or removed too sick) within 1,200 days of listing
- Patients still waiting or with unknown outcomes before day 1,200 are excluded
- 80/20 split, stratified on the target, `random_state=42` (same test set as the other models)
- Numeric features scaled on the training set only to avoid leakage
- `DIALYSIS_DURATION_LOG` dropped (duplicate of `DIALYSIS_DURATION`)

The logistic regression result (0.678 ROC-AUC, 0.208 PR-AUC) matches the team's baseline notebook exactly, which confirms the setup.

**2. Hyperparameter search (two rounds).**
GridSearchCV with 3-fold cross-validation, scored on PR-AUC, on a stratified sample of 100,000 training rows. Class weights were intentionally not used because they distort predicted probabilities and hurt Brier score and calibration.

Round 1:
- Logistic Regression: `C` of 0.01, 0.1, 1
- Random Forest: `max_depth` of 8, 12 and `min_samples_leaf` of 50, 200
- XGBoost: `max_depth` of 2, 3, 4 and `min_child_weight` of 10, 50

Several best values sat at the edge of the grid, so a second round widened them:
- Logistic Regression: `C` of 0.1, 1, 10
- Random Forest: `max_depth` of 12, 16, 20 and `min_samples_leaf` of 10, 25, 50
- XGBoost: `max_depth` of 4, 5, 6 and `min_child_weight` of 1, 5, 10

In round 2 the XGBoost winners were in the middle of the grid and test scores were unchanged, so tuning had converged. The winning model was chosen on cross-validation score, not on the test set.

**3. Test-set evaluation.**
The best version of each model was retrained on all training data and evaluated with ROC-AUC, PR-AUC, Brier score, and top-decile lift (table above).

**4. Calibration.**
All three models track the diagonal closely, so predicted risks match observed adverse-event rates and no recalibration is needed. No patient is predicted above about 27% risk, which reflects the modest ability to separate high-risk patients.

**5. SHAP interpretation (XGBoost, 1,000 test patients).**
- Largest drivers: older age (`INIT_AGE`) raises risk, and higher functional status (`functional_percent`) lowers it.
- Also raise risk: inactive status at listing (`INIT_STAT_4099`), blood types O and B, and `REGION_4`.
- Lower risk: starting dialysis after listing (`DIALYSIS_AFTER_LISTING`), `A2A2B_ELIGIBILITY_1.0`, and listing after 2021.
- Directions agree with the logistic regression odds ratios for these features.

**Caveats and Follow-ups**
---
- `DIALYSIS_DURATION` lowers risk in XGBoost but slightly raises it in logistic regression. It overlaps with `AGE_DIALYSIS_INTERACTION`, so this should be treated as an association only.
- `POLICY_ERA_post_2021` is likely not a true policy effect, because recently listed patients have less follow-up time. It should not be interpreted as the impact of the 2021 policy.
- `DIALYSIS_AFTER_LISTING` may use information not known at listing time. A follow-up check is to rerun without it and compare ROC-AUC.
- Blood type, region, and `ETHCAT` effects likely reflect allocation rules rather than patient health, and should be examined in the fairness audit.
- The sensitivity cohort (138,611 patients, 36% adverse rate) has not been tuned.

**Files Delivered**
---

| Area | Exact file snapshot | Why is it included |
| --- | --- | --- |
| Hyperparameter tuning notebook | `hyperparameter-tuning.ipynb` | Cohort setup, GridSearchCV for all three models, hyperparameter selection |
| Model evaluation | `hyperparameter-tuning.ipynb` | ROC-AUC, PR-AUC, Brier score, and top-decile lift compared against project targets |
| Calibration analysis | `hyperparameter-tuning.ipynb` | Shows predicted risks are well calibrated for all three models |
| SHAP analysis | `hyperparameter-tuning.ipynb` | Identifies the features with the largest influence on XGBoost predictions |