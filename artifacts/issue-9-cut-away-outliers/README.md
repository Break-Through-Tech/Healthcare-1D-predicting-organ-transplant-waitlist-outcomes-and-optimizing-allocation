# Issue #9: Cut away outliers

This is a detailed completion report for [Issue 9](https://github.com/orgs/Break-Through-Tech/projects/200/views/1?pane=issue&itemId=250604774&issue=Break-Through-Tech%7CHealthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation%7C9). **Outliers were cut away.** The evidence is tied to source commit 62faf2b9ea0715cd23e74b4bc1426f3a79989b8e and includes Git history for independently checking that relationship.

## Outcome
Only one column has outliers that needed to be cut away--BMI_TCR. Work was done in `outliers.ipynb`. It was winscoized using IQR, cutting away the 25th and 75th percentiles.

## Work Completed
**Structure.** The outliers column is under one notebook cell.

**Documentation.** There were three columns 
that seemed like good fits to be winscorized:
INIT_CPRA, INIT_AGE, and BMI_TCR. However, as they were tested, INIT_CPRA was a bad ft to winscorization because all of its values are percentages--in a range from 0-100%, these valeus cannot necessarily be outliers. INIT_AGE also ended up being a poor candidate, because pediatric and geriatric ages ended up being cut away. 

## Files Delivered
| Area | Exact file snapshot | Why is it included |
|---|---|---|
| Outliers | outliers.ipynb | Cutting away outliers |
