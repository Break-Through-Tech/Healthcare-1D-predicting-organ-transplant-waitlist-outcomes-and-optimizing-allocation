**Issue #4: Explore Feature Engineering**
---
This is a detailed completion report for [Issue 4](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/issues/4). **Observe what features relate to our 2 different labels (waitlist outcome vs waitlist duration) and then choose the best features for each label.** The evidence is tied to source commit `8529c8eb307b94ef95efadf8d5ddb269cb1430a4` and includes Git history for independently checking that relationship.

**Outcome**
---

**Work Completed**
---
`POLICY_ERA`: Categorical feature based on listing date relative to the March 2021 kidney allocation policy change. The notebook has a cell separating listing dates prior to March 3rd, then between 2015-2021, and then after 2021. This can be helpful for comparing features to this feature and observe outcomes.

`AGE_DIALYSIS_INTERACTION`: Multiplying age by years on dialysis. It separates patients that are older and have been on dialysis for a longer time.

`DIALYSIS_DURATION_LOG`: Log-transform to compress right-skew in DIALYSIS_DURATION. This is a feature made to test out in modeling to scale values proportionally.

**Files Delivered**
---
| Area | Exact file snapshot | Why is it included |
| --- | --- | --- |
| Encoding, feature engineering, and scaling notebook | [encoding-scaling-cells.ipynb](https://github.com/Break-Through-Tech/Healthcare-1D-predicting-organ-transplant-waitlist-outcomes-and-optimizing-allocation/blob/main/notebooks/encoding-scaling-cells.ipynb) | Decoding, feature engineering, encoding, scaling, and verification split into documented cells |
| Encoded, feature engineered, and scaled data | `data/processed/task3_encoded_scaled.csv` | Output of the notebook, used for modeling
