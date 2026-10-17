**Issue #2: Explore missing values and solutions**
---
This is a detailed completion report for [Issue 2](https://github.com/orgs/Break-Through-Tech/projects/200/views/1?pane=issue&itemId=246011664&issue=Break-Through-Tech%7CHealthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation%7C2). **Explore the columns with missing values and devise a solution.**

**Outcome**
---
Every column carrying missing values now has a documented handling strategy, chosen to match what the missingness actually means rather than applying one blanket fill. The cleaned output at `data/processed/cleaned_waitlist.csv` has zero nulls.

**Work Completed**
---
**String columns.** `MULTIORG` filled with "N". All remaining string columns filled with "Unknown".

**Numeric columns.** `BMI_TCR` and `INIT_CPRA` filled with the column median, since both are continuous and right-skewed. `DIAG_KI`, `FUNC_STAT_TCR`, and `A2A2B_ELIGIBILITY` filled with a sentinel value of -1 and cast to string, so the sentinel is treated as its own category rather than as a number that would skew the distribution.

**Dates.** `DIALYSIS_DATE` (134,245 missing) is not filled. A missing dialysis date generally means the candidate was not on dialysis, which is information rather than absence. It is parsed as a date, used to derive `DIALYSIS_DURATION` where a date exists, and dropped afterward. Candidates with no dialysis date get a duration of 0, and `ON_DIALYSIS` carries the yes/no signal separately.

**Invalid records.** 59 records with an end date before their listing date were excluded. These are the same 59 missing `days_to_event`.

**Known follow-up.** `INIT_CPRA` is median-imputed on 142,741 rows (29%) and the median is 0, so imputed rows are currently indistinguishable from genuinely unsensitized candidates. The data dictionary asks for a missingness indicator here. Not yet added.

**Files Delivered**
---
| Area | Exact file snapshot | Why is it included |
| --- | --- | --- |
| Cleaning notebook | [outliers.ipynb](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/blob/main/notebooks/outliers.ipynb) | Null handling, imputation, and dialysis date derivation |
