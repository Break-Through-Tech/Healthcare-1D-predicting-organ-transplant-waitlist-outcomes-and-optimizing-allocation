**Issue #3: Encode and normalize data**
---
This is a detailed completion report for [Issue 3](https://github.com/orgs/Break-Through-Tech/projects/200/views/1?pane=issue&itemId=246012223&issue=Break-Through-Tech%7CHealthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation%7C3). **Encode the categorical features and normalize numerical features by scaling.** The evidence is tied to source commit `eca5d5ad14e516b98c55cf2022c3785734542ff1` and includes Git history for independently checking that relationship.

**Outcome**
---
The cleaned waitlist data is now a fully numeric, model-ready matrix at `data/processed/task3_encoded_scaled.csv`: 494,803 rows by 51 columns with zero nulls across every feature column. Eight categorical features were one-hot encoded, seven numerical features were standardized, and the five outcome columns pass through unmodified so a modeler can select a target and drop the rest.

**Work Completed**
---
**Decoded `FUNC_STAT_TCR`.** The raw field mixes adult Karnofsky codes (2010-2100) with pediatric Lansky codes (4010-4100) plus three non-scale codes, so it could not be encoded or scaled directly. Decoded per `data_dictionary.md` into `functional_scale` (karnofsky, lansky, adl_independent, not_applicable_infant, unknown) and `functional_percent` (10-100, comparable across both scales). `functional_percent_missing` flags the 12,792 rows with no real score; those are median-imputed.

**Engineered three additional features.** `LISTED_POST_2021` marks listings on or after the March 15, 2021 allocation policy change. `AGE_DIALYSIS_INTERACTION` multiplies age by years on dialysis. `DIALYSIS_DURATION_LOG` compresses the right skew in dialysis duration.

**Encoded categoricals.** One-hot encoding with `drop_first=True` across `ON_DIALYSIS`, `GENDER`, `ABO`, `A2A2B_ELIGIBILITY`, `INIT_STAT`, `ETHCAT`, `REGION`, and `functional_scale`.

**Scaled numericals.** `StandardScaler` applied to `BMI_TCR`, `INIT_CPRA`, `INIT_AGE`, `functional_percent`, `DIALYSIS_DURATION`, `DIALYSIS_DURATION_LOG`, and `AGE_DIALYSIS_INTERACTION`. The three binary indicators are left unscaled since they are already 0/1.

**Excluded leakage and identifier fields.** The feature whitelist contains only fields the data dictionary marks Baseline or Sensitive. `PREV_TX`, `DIAG_KI`, and `MULTIORG` are excluded despite describing conditions that predate listing, because in this extract they are populated almost exclusively for transplanted candidates, so their missingness reveals the outcome.

**Known follow-up.** `INIT_CPRA` is median-imputed on 142,741 rows and the median is 0, so imputed rows are currently indistinguishable from genuinely unsensitized candidates. The data dictionary asks for a missingness indicator here. Not yet added.

**Files Delivered**
---
| Area | Exact file snapshot | Why is it included |
| --- | --- | --- |
| Encoding and scaling notebook | [encoding-scaling-cells.ipynb](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/blob/main/notebooks/encoding-scaling-cells.ipynb) | Code for encoding and scaling |
