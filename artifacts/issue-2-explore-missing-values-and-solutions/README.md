**Issue #2: Explore missing values and solutions**
---
This is a detailed completion report for [Issue 2](https://github.com/orgs/Break-Through-Tech/projects/200/views/1?pane=issue&itemId=246011664&issue=Break-Through-Tech%7CHealthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation%7C2). 

**Outcome**
---
Every column carrying missing values now has a documented handling strategy, chosen to match what the missingness actually means rather than applying one blanket fill. The cleaned output at `data/processed/cleaned_waitlist.csv` has zero nulls.

**Work Completed**
---
**Dates and target.** `INIT_DATE`, `TX_DATE`, and `END_DATE` are converted from stings to datetime values. Two columns are created from them:
- `TRANSPLANTED`: True if the patient has a transplant date, otherwise it is False.
- `DAYS_TO_EVENT`: days from listing to transplant. It is left blank for non_transplanted patients since filling with 0 would falsely mean "transplanted on day 0." Filter on `TRANSPLANTED` to get real wait times.

`TRANSPLANTED` and `DAYS_TO_EVENT` are labels and MUST NOT be used as model features. Non-transplanted patients are kept in the file so that the cohort can switch between "everyone" and "transplanted only" without needed to rerun the cleaning.

**Leakage columns dropped.** Columns that are known only after the listing are removed: `REM_CD`, `END_DATE`, `TX_DATE`, `COMPOSITE_DEATH_DATE`, `END_STAT`, `END_CPRA`, `DAYSWAIT_CHRON`, `DAYSWAIT_ALLOC`, `DON_TY`, `ORGAN`, `TRR_ID_CODE`, `DONOR_ID`, `PTIME`, `PSTATUS`.

**String columns.** `MULTIORG` filled with "N". All remaining string columns filled with "Unknown".

**Numeric columns.** `BMI_TCR` and `INIT_CPRA` filled with the column median, since they skew to the right and are continuous.`DIAG_KI`, `FUNC_STAT_TCR`, and `A2A2B_ELIGIBILITY` filled with a sentinel value of -1 and cast to string, so the sentinel is treated as its own category rather than as a number that would skew the distribution.

**Outliers.** `BMI_TCR` is winsorized using the IQR rule (values clipped to the bounds, no rows removed). `INIT_CPRA` (a percentage), `INIT_AGE` (IQR cut valid ages), and `DAYS_TO_EVENT` (long waits are real data) are intentionally not winsorized, as it would cut away valuable data.

**Dialysis.** `DIALYSIS_DATE` is not filled. A missing dialysis date generally means the candidate was not on dialysis; its absence provides valuable information. It is used to derive `DIALYSIS_DURATION` (days on dialysis before listing (0 where no date exists)) and then dropped. Negative durations mean the patient started dialysis after listing; these are clipped to 0 and flagged in `DIALYSIS_AFTER_LISTING`.

**Invalid records.** Records where the transplant date or end date falls before the listing date were removed. Missing dates are not treated as invalid.


**Files Delivered**
---
| Area | Exact file snapshot | Why is it included |
| --- | --- | --- |
| Cleaning notebook | [outliers.ipynb](<keep your existing link>) | Date parsing, transplant flag and wait-time target, null handling, imputation, outlier handling, and dialysis date derivation |
| Cleaned data | `data/processed/cleaned_waitlist.csv` | Output of the notebook, used for modeling |
