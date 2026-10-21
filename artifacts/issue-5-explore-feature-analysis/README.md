**Issue #5: Explore Feature Analysis**
---
This is a detailed completion report for [Issue 5](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/issues/5). **Explore feature analysis. Find information about the relationships between the features.** The evidence is tied to source commit `c6a25f8061e60982979c0b8d3112b144e4c09c88` and includes Git history for independently checking that relationship.

**Outcome**
---
The data was examined and it was found that columns were either numeric (int or float), or of string type. When missing values were searched for, it was found that it was very bimodal--either they were very low, or very high (in the tens of thousands). Initial analysis suggests that columns with high amounts of missing values can be removed, but this is not entirely necessary.

**Work Completed**
---
Integer and float columns include: `BMI_TCR`, `INIT_CPRA`, `IAG_KI`, `FUNC_STAT_TCR`, and `A2A2B_ELIGIBILITY` 
String columns include: `MULTIORG`
Some of these integer and float columns include missing values that will be handled.

A correlation matrix was plotted to display each features' correlation with the possible target label `event_adverse`
The feature with the closests correlation to `event_adverse` is `END_STAT`

**Files Delivered**
---
| Area | Exact file snapshot | Why is it included |
| --- | --- | --- |
| Feature analysis notebook | [feature-analysis.ipynb](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/blob/main/notebooks/feature-analysis.ipynb) | Dtype and correlation matrix |
