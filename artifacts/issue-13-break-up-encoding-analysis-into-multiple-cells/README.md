# Issue #13: Break up the encoding analysis into multiple cells

This is a detailed completion report for [Issue 13](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/issues/13). Break up the single-cell encoding analysis into multiple documented cells.

## Outcome
The encoding analysis no longer runs as one large cell. All encoding and scaling work now lives in a single sectioned notebook, `encoding-scaling-cells.ipynb`, where each step runs in its own cell. The team can inspect the result of each stage and easily refer back to it as needed.

## Work Completed
**Structure.** The notebook is organized under five headers: Decoding, Data Handling, Feature Engineering, Encoding and Scaling, and Verification.

**Combined code.** It brings together decoding code from Abdullah and encoding and scaling code from Afra. Afra's work also adds a new `DIALYSIS_DURATION_LOG` feature for model testing. Two new features engineered by James are included as well: `LISTED_POST_2021` and `AGE_DIALYSIS_INTERACTION`.

**Removed duplicate logic.** The `days_to_event` filter was removed, since Catherine's outliers notebook already handles it. The fallback logic for `DIALYSIS_DURATION`, `INIT_DATE`, and `DIALYSIS_DATE` was also removed, because the outliers notebook creates `DIALYSIS_DURATION`.

**Documentation.** Comments were adjusted throughout to describe each cell more thoroughly.

## Files Delivered
| Area | Exact file snapshot | Why is it included |
|---|---|---|
| Encoding and scaling notebook | encoding-scaling-cells.ipynb | Decoding, feature engineering, encoding, scaling, and verification split into documented cells |
