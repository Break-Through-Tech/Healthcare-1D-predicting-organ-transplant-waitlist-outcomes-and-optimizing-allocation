**Issue #7: Train/test split**
---
This is a detailed completion report for [Issue 7](https://github.com/orgs/Break-Through-Tech/projects/200/views/1?pane=issue&itemId=267923862&issue=Break-Through-Tech%7CHealthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation%7C17). **Split the encoded data into training and test sets without leaking test information into preprocessing.** The evidence is tied to source commit `[COMMIT HASH]` and includes Git history for independently checking that relationship.

**Outcome**
---
The encoded OPTN waitlist data is split 80/20 into training and test sets, stratified on `outcome`, with a fixed random seed so every teammate gets the identical split. Numeric scaling is now fit on the training rows only, so no test-set statistics influence the training data. Saved splits are in `data/processed/` for all modeling, survival, explainability, and fairness work.

**Work Completed**
---
**Moved scaling after the split.** The encoding notebook previously fit `StandardScaler` on all 494,803 rows. That let test-set means and standard deviations influence the training data (data leakage). The scaling cell was removed from the encoding notebook, which now saves an unscaled file (`task3_encoded_unscaled.csv`). Scaling happens in the split notebook instead.

**Features and targets separated.** `X` holds every encoded feature column. The five target columns (`outcome`, `event_adverse`, `event_transplant`, `censored`, `days_to_event`) are kept together in `y` so each sub-team can choose its label: classification uses `event_adverse`, and survival modeling uses `days_to_event` with an event column. Target columns are never in `X`.

**Leakage review.** `DIALYSIS_AFTER_LISTING` was [excluded from / kept in] the feature set because [reason, based on data_dictionary.md]. `DIALYSIS_DURATION` was confirmed to be measured [at listing / reason], so it and the features derived from it (`DIALYSIS_DURATION_LOG`, `AGE_DIALYSIS_INTERACTION`) were [kept / excluded].

**Split.** `train_test_split` with `test_size=0.2`, `stratify=y["outcome"]`, and `random_state=42`.
- Stratifying on `outcome` keeps every outcome type (died, removed too sick, transplanted, administrative removal, still waiting, and so on) in the same proportion in both sets. This preserves the adverse-event rate (about 14%) and the censored rate (about 21%).
- Resulting shapes: train `[395,842 x N]`, test `[98,961 x N]`. Verified that train and test rates for `event_adverse`, `event_transplant`, and `censored` match to about two decimal places: `[paste values]`.

**Scaling.** `StandardScaler` is fit on the training rows only and applied to both sets with `transform`. Only the continuous columns are scaled (`BMI_TCR`, `INIT_CPRA`, `INIT_AGE`, `functional_percent`, `DIALYSIS_DURATION`, `DIALYSIS_DURATION_LOG`, `AGE_DIALYSIS_INTERACTION`). One-hot and binary indicator columns are left as they are. The fitted scaler is saved so scaled values can be converted back to real units (for example, "age 62") in explainability work.

**Known limitations.**
- Censored patients (20.9%) are labeled `event_adverse = 0`, meaning "not observed yet," not "never happened." This is acceptable for a baseline classifier but must not be used for the time-to-transplant model, which should use `days_to_event` plus the event flag.
- The split is random. The data spans the 2021 allocation policy change, so models should also be evaluated separately on `POLICY_ERA_post_2021` rows of the test set to check for distribution shift.
- `REGION` and listing-related features may let a model re-learn allocation patterns rather than clinical risk. This is addressed in the fairness audit.

**Files Delivered**
---
| Area | Exact file snapshot | Why is it included |
| --- | --- | --- |
| Encoding notebook | [encoding-scaling-cells.ipynb]([COMMIT HASH]) | Updated to save unscaled encoded data; scaling cell removed |
| Split notebook | [[NOTEBOOK NAME].ipynb]([COMMIT HASH]) | Feature/target separation, stratified split, train-only scaling, saving outputs |
| Training and test data | `data/processed/X_train.csv`, `X_test.csv`, `y_train.csv`, `y_test.csv` | Shared splits used by all modeling sub-teams (not committed; derived from restricted data) |
| Fitted scaler | `data/processed/scaler.joblib` | Reuse on new data and reverse scaling for explainability |