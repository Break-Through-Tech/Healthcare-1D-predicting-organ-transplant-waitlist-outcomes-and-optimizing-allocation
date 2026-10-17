# [Issue #13](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/issues/13): Break Up the Encoding Analysis into Multiple Cells

This is a detailed completion report for Issue 13. Break up the encoding analysis into multiple cells with proper documentation.

## Outcome
We split the single-cell encoding analysis into a sectioned, documented notebook, so the team can inspect the output of each step and easily refer back to it as needed.

## Work Completed
All encoding and scaling work is now combined into one file, `encoding-scaling-cells.ipynb`, organized under five headers: Decoding, Data Handling, Feature Engineering, Encoding and Scaling, and Verification.

The notebook combines decoding code from Abdullah and encoding and scaling code from Afra. Afra's work adds a new `DIALYSIS_DURATION_LOG` feature for model testing. It also includes two new features engineered by James: `LISTED_POST_2021` and `AGE_DIALYSIS_INTERACTION`.

The `days_to_event` filter was removed because Catherine's outliers notebook already handles it. The fallback logic for `DIALYSIS_DURATION`, `INIT_DATE`, and `DIALYSIS_DATE` was also removed, since the outliers notebook creates `DIALYSIS_DURATION`.

Comments were adjusted throughout to describe each cell more thoroughly.

## Files Delivered
| Area | Exact file snapshot | Why is it included |
|---|---|---|
| Encoding and scaling notebook | encoding-scaling-cells.ipynb | Decoding, feature engineering, encoding, scaling, and verification split into documented cells |
