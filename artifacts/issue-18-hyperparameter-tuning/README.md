**\*\*Issue #__: Hyperparameter Tuning\*\***  
**---**  
This is a detailed completion report for the hyperparameter tuning analysis. **\*\*Test different model settings to identify the best-performing model and evaluate its performance on the test data.\*\*** The analysis compares Logistic Regression, Random Forest, and XGBoost models using cross-validation and several performance metrics.

**\*\*Outcome\*\***  
**---**  
Three different models were tested using GridSearchCV to find the best hyperparameter settings for each model. The models were evaluated using PR-AUC during the tuning process, since the goal is to predict the \`event_adverse\` outcome.

A smaller, stratified sample of 40,000 training examples was used for the initial hyperparameter search. This kept the proportion of adverse events similar to the full training data while making the search more manageable.

The best version of each model was then retrained using the full training dataset and evaluated on the test dataset using ROC-AUC, PR-AUC, Brier score, and top-decile lift.

Calibration plots were also created to examine whether predicted probabilities accurately represented the actual risk of an adverse event. SHAP analysis was used on the winning model to identify which features had the largest influence on its predictions.

**\*\*Work Completed\*\***  
**---**  
The following models were compared:

- \`Logistic Regression\`
- \`Random Forest\`
- \`XGBoost\`

The hyperparameters tested for each model include:

- **Logistic Regression:** \`C\` values of 0.1, 1, and 10
- **Random Forest:** \`max_depth\` values of 6 and 12, and \`min_samples_leaf\` values of 10 and 50
- **XGBoost:** \`max_depth\` values of 3 and 6, and \`min_child_weight\` values of 1 and 10

GridSearchCV with 3-fold cross-validation was used to compare the different settings. The best hyperparameter configuration for each model was selected based on PR-AUC.

The selected models were evaluated using:

- **ROC-AUC** — measures how well the model separates patients with and without an adverse event.
- **PR-AUC** — measures performance when identifying the positive/adverse-event class.
- **Brier score** — measures how accurate the predicted probabilities are, with lower values being better.
- **Top-decile lift** — measures how much higher the adverse-event rate is among the 10% of patients predicted to be at highest risk compared with the overall population.

A calibration plot was created to determine whether predicted risks corresponded to observed adverse-event rates.

SHAP analysis was also performed on the winning model using a sample of 1,000 test patients. This identifies which features had the largest effect on the model's predictions and provides a ranked list of feature importance.

**\*\*Files Delivered\*\***  
**---**

| Area | Exact file snapshot | Why is it included |
| --- | --- | --- |
| Hyperparameter tuning notebook | `hyperparameter-tuning.ipynb` | Model comparison, GridSearchCV, hyperparameter selection, and evaluation metrics |
| Model evaluation | `hyperparameter-tuning.ipynb` | ROC-AUC, PR-AUC, Brier score, and top-decile lift |
| Calibration analysis | `hyperparameter-tuning.ipynb` | Evaluates whether predicted risk probabilities are well calibrated |
| SHAP analysis | `hyperparameter-tuning.ipynb` | Identifies the features with the largest influence on the winning model's predictions |