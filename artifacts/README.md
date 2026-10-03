***Issue #3: Encode and normalize data***

**Outcome**
BMI_TCR feature had a data entry error of a value of 430,226. This was capped at 80 using the median as the replacement. After fixing the data entry error from the BMI_TCR (Body Mass Index at listing) feature, the real range is 12.4 - 45.1 (as seen from the IQR bounds) with a mean ~28.8. This is worth noting as BMI can affect transplant eligibility outcomes.

There were many patients with a negative raw dialysis duration, which means they were listed before starting dialysis, not after. This shows a clinical pattern of preemptive listings. This was handled in the outliers notebook by clipping negative durations to 0 and creating a DIALYSIS_AFTER_LISTING flag.

Most patients had dialysis durations that were short, with an average of 582 days. One patient had been on dialysis for 15,356 days. This made an extreme case that stretched out the data, that after standardizing the feature, the longest duration was still very much far above the average. To fix this, we applied a log-transform on the DIALYSIS_DURATION feature, creating DIALYSIS_DURATION_LOG. This brough the extreme value closer to the average. We kept both the original and log-transformed versions of this feature (DIALYSIS_DURATION and DIALYSIS_DURATION_LOG) so we can test later for the modeling.
