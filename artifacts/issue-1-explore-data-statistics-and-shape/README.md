**Issue #1: Explore data statistics and shape**
---
This is a detailed completion report for [Issue 1](https://github.com/orgs/Break-Through-Tech/projects/200/views/1?pane=issue&itemId=246011493&issue=Break-Through-Tech%7CHealthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation%7C1). **Explore the dataset's statistics and shape.** The evidence is tied in source commit `b2b4973dcecf8acf5829cc09daa20f3b226e7e6b` and includes Git history for independently checking that relationship.

**Outcome**
---
We profiled the OPTN extract and established its shape, column types, and missingness pattern, giving the team a shared baseline before any cleaning decisions.

**Work Completed**
---
The extract has 494,862 rows and 38 columns, one row per `WL_ID_CODE`.

Every column is either string or numeric. No boolean columns.

Missingness is bimodal: a column either has zero nulls or nulls in the tens of thousands. Six columns share an identical count of 264,107 (`PREV_TX`, `TX_DATE`, `DON_TY`, `ORGAN`, `TRR_ID_CODE`, `DONOR_ID`), which indicates they are only populated for transplanted candidates. That has leakage implications for feature selection.

**Files Delivered**
---
| Area | Exact file snapshot | Why is it included |
| --- | --- | --- |
| Data profiling notebook | [outliers.ipynb](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/blob/main/notebooks/outliers.ipynb) | Shape, dtype, and null-count checks |
