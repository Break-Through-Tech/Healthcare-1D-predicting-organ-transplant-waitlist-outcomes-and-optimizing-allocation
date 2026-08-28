# Predicting Organ Transplant Waitlist Outcomes & Optimizing Allocation

**Company / Org:** Break Through Tech AI Studio  
**Challenge Advisor:** Beth Parnell elizabeth.parnell@breakthroughtech.org   
**Program:** Break Through Tech AI Studio - Fall 2026

---

## 🏢 About MediMate Foundation

MediMate Foundation is a California 501(c)(3) nonprofit dedicated to improving
healthcare outcomes through advanced analytics and predictive modeling. Our focus
is on transparency and risk assessment in the organ allocation process.

We build AI-powered navigation and decision-support tools for people living with
chronic kidney disease and for kidney transplant candidates. Our work is grounded
in lived patient experience — our founder is a kidney transplant recipient — and
everything we publish is aggregate, explainable, and written to be understood by
the people it describes.

---

## 🎯 The Challenge

### Project Summary
This project builds predictive models and analytical tools to identify risk and
improve transparency in organ allocation patterns. The goal is to provide insights
into which patients are at highest risk while waiting, expected wait times, and
geographic or systemic factors influencing transplant success.

Your team will answer three linked questions:

1. **Who is at risk?** Predict which waitlist candidates are most likely to die or
   be removed as too sick before receiving a transplant.
2. **How long will they wait?** Model time-to-transplant using survival analysis,
   accounting for candidates still waiting when the observation window ends.
3. **Is the model fair, and can we explain it?** Audit performance across
   demographic and geographic subgroups, and translate the model's drivers into
   plain language.

### Success Criteria
Model performance targets: ROC-AUC >= 0.78, PR-AUC >= 0.65, Brier score <= 0.18,
Lift in top decile >= 2.5x, and C-index >= 0.72. Success is also measured by
fairness (subgroup performance within 0.05 AUC) and deliverable quality.

> **A note on these numbers:** These are targets to aim at, not a pass/fail bar. A
> well-documented model that misses a threshold and explains *why* is a stronger
> result than one that hits it without understanding.

### Project Milestones

Use these milestones to guide your work. Your team will create a **GitHub Projects
board** to track tasks within each milestone.

| Month | Milestone | Key Activities |
| -- | --- | --- |
| **September** | Data Understanding | Explore dataset, handle missing values, document findings |
| **October** | Model Development | Train baseline model, experiment with approaches, iterate |
| **November** | Evaluation & Presentation | Finalize model, prepare presentation, document results |

> **Note for the team:** Please create a GitHub Projects board in this repository
> to break these milestones into weekly tasks. Go to the **Projects** tab →
> **New project** → Choose **Board** → Add columns for each month.

**Suggested sub-teams (6–7 fellows):** EDA & data dictionary · baseline
classification · survival modeling · explainability (SHAP) · fairness audit ·
dashboard & final presentation

---

## 📊 Dataset

### Primary dataset: restricted OPTN kidney waitlist extract

- **Name and source:** OPTN national transplant data (Organ Procurement and
  Transplantation Network, administered by UNOS under contract to HRSA)
- **File:** `data/restricted/kidney_waitlist_analytic.csv.gz`
- **Format:** One compressed CSV containing structured,
  candidate-listing-level records. It has no free-text fields.
- **Current snapshot:** 494,862 rows and 38 columns (about 21.2 MB compressed).
  Initial listing dates run from January 1, 2015 through June 30, 2026, with
  follow-up recorded through July 3, 2026.
- **Access:** Fellows have been sent an email containing the Google Drive link for
  the restricted files. Download them to `data/restricted/`; this directory is
  gitignored and its contents must not be committed. See
  [`data/README.md`](data/README.md) for setup, schema, and handling guidance.

### Fallback dataset: Brazilian kidney waitlist data

If the restricted OPTN extract is temporarily unavailable, use
`data/waitlist_kidney_brazil 2.csv` as the fallback. It contains 48,153 rows and
53 columns covering 2000–2017 and must be read with a Windows-1252-compatible
encoding such as `cp1252`. It is a separate population with a different schema
and outcome coding, so do not combine it with the OPTN data without an explicit
harmonization plan.

### Key Details

- The analytic extract includes demographics, blood type, cPRA, dialysis status,
  BMI, functional status, diagnosis, OPTN region, listing center, transplant and
  death dates, and wait-time fields.
- The 38 columns comprise 33 source fields and five project-ready fields:
  `outcome`, `event_adverse`, `event_transplant`, `censored`, and
  `days_to_event`.
- `event_adverse` marks death or removal as too sick. `event_transplant` marks the
  `transplanted` outcome, while `censored` marks candidates who were still waiting
  at the end of follow-up.
- Work from the restricted OPTN extract by default. Use the Brazilian dataset
  only when access to the primary data is blocked, and state clearly in every
  analysis which dataset was used.

### Known Limitations and Preprocessing Needed

- **Class imbalance.** The adverse endpoint occurs in 69,743 records (14.1%). Lead
  classification evaluation with PR-AUC and calibration (Brier score) rather than
  ROC-AUC alone — ROC-AUC can look deceptively good on imbalanced data.
- **Censoring.** The extract marks 103,300 candidates (20.9%) as still waiting.
  Treating "no transplant yet" as a negative label will bias the wait-time model.
- **Competing outcomes.** Transplant, death, removal as too sick, administrative
  removal, transplant elsewhere, and unknown outcomes need explicit treatment.
  For each survival endpoint, document which outcomes count as the event,
  censoring, or a competing event before fitting the model.
- **Missingness.** Document missingness patterns before choosing an imputation
  strategy, and record any rows you exclude. In particular, cPRA fields contain
  substantial missingness.
- **Date validation.** Fifty-nine records currently have an end date before their
  listing date and no `days_to_event`; validate or exclude them before time-to-event
  modeling. Also decide whether zero-day events are analytically valid.
- **Distribution shift.** The data begins after the 2014 Kidney Allocation System
  change and spans later allocation-policy changes, including changes in 2021.
  Encode meaningful policy eras and check performance over time.
- **Scale.** The compressed CSV is manageable with pandas or another dataframe
  library, including in Google Colab. Avoid editing the row-level file in a
  spreadsheet application.
- **Fairness and leakage.** Watch for the model simply re-learning historical
  allocation patterns rather than predicting clinical risk. That distinction is
  the heart of the fairness component. Identifier fields and fields created or
  updated after the prediction point—including outcome labels and event dates—must
  not be used as model features.

### Data Handling

⚠️ **This is a public repository, and the primary dataset is restricted.**

- **Never commit raw row-level records to this repo.** The `data/restricted/`
  folder is gitignored.
- Commit code, aggregate summaries, and figures only.
- Do not put the Google Drive link in this repository or redistribute the data
  outside the team.
- Follow all access and use terms included with the OPTN data delivery.
- If you are unsure whether something is safe to commit, ask before you push.

### Data Dictionary and Documentation

- The restricted delivery includes `column_manifest.csv`; however, its contents do
  not fully match the current analytic CSV header. Review the discrepancy noted in
  [`data/README.md`](data/README.md) when loading or validating the data.
- Use the project [`Fellows' Data Dictionary`](data/data_dictionary.md) for all 38
  fields, observed categorical codes, leakage guidance, and confidence levels.
  The restricted delivery does not include a version-matched STAR dictionary, but
  that is not a blocker for this project: the dictionary identifies which values
  are official, project-verified, corroborated, or still unresolved. Fellows must
  preserve raw codes rather than inventing finer labels for unresolved legacy
  subcodes.
- Official OPTN data information and request portal:
  https://optn.transplant.hrsa.gov/data/

---

## 🛠️ Suggested Approach

**ML Problem Type:** Classification (primary) + Survival Analysis (time-to-event)

> This project is deliberately two-part. Classification answers "who is at risk";
> survival analysis answers "how long until transplant." The C-index in the Success
> Criteria comes from the survival half, so don't skip it.

**Suggested Sequence:**

1. **EDA and data dictionary** — load the restricted OPTN file, validate dates and
   derived labels, then profile every field: distributions, missingness, and
   outcome base rates. If it is unavailable, load the Brazilian fallback with the
   encoding and field mapping in `data/README.md`. Maintain the shared
   [`Fellows' Data Dictionary`](data/DATA_DICTIONARY.md) as the extract and the
   team's decisions evolve.
2. **Baseline classification** — use `event_adverse` as the initial target. Start
   with logistic regression as an interpretable baseline, then move to tree
   ensembles (Random Forest, XGBoost). For the Brazilian fallback, first create the
   comparable adverse target described in `data/README.md`. Always report the
   baseline; a complex model that barely beats logistic regression is itself a
   finding worth stating.
3. **Survival modeling** — use `days_to_event` only after defining the endpoint and
   treatment of every outcome. Start with Cox proportional hazards, evaluated with
   C-index, then compare against a competing-risks model. With the fallback, use
   `time` as the duration and its `event` codes as documented in `data/README.md`.
4. **Calibration and thresholds** — Brier score, calibration curves, decile lift.
   Choose an operating point tied to a stated use case, and justify it.
5. **Explainability** — SHAP global and local explanations. Then the harder part:
   translate the top drivers into language a patient or clinician would understand.
6. **Fairness audit** — subgroup performance across age, sex, race/ethnicity, blood
   type, and OPTN region. Report gaps honestly, including where you miss the 0.05
   AUC target.
7. **Deliverable** — stakeholder write-up plus a Streamlit dashboard or notebook
   walkthrough.

**Recommended Libraries:**
- XGBoost
- Logistic Regression (scikit-learn)
- Cox Proportional Hazards (Cox PH) — via `lifelines` or `scikit-survival`
- SHAP
- Streamlit
- GitHub
- Jupyter Notebooks
- Docker
- CI/CD
- Large Language Models (LLMs) such as Claude or GPT
- Google Colab

**Evaluation Metrics:**
- ROC-AUC
- PR-AUC
- Brier score
- Lift in top decile
- C-index
- Subgroup AUC gap (fairness)

---

## 📚 Resources to Get Started

The following resources will help your team understand the problem space and
potential technical approaches for this project:

**Background Reading:**
- OPTN — How organ allocation works:
  https://optn.transplant.hrsa.gov/patients/about-transplantation/how-organ-allocation-works/
- OPTN — National data reports: https://optn.transplant.hrsa.gov/data/
- SRTR — Annual data reports on U.S. transplant outcomes: https://www.srtr.org/reports/
- National Kidney Foundation — The transplant waitlist explained:
  https://www.kidney.org/atoz/content/transplant-waitlist

**Technical Tutorials:**
- lifelines — survival analysis in Python:
  https://lifelines.readthedocs.io/en/latest/Survival%20Analysis%20intro.html
- scikit-survival user guide:
  https://scikit-survival.readthedocs.io/en/stable/user_guide/index.html
- SHAP documentation: https://shap.readthedocs.io/en/latest/
- scikit-learn — probability calibration:
  https://scikit-learn.org/stable/modules/calibration.html
- XGBoost — Python introduction:
  https://xgboost.readthedocs.io/en/stable/python/python_intro.html
- Streamlit — get started: https://docs.streamlit.io/get-started

**Code Examples:**
- lifelines Cox PH worked example:
  https://lifelines.readthedocs.io/en/latest/Survival%20Regression.html
- scikit-survival worked examples:
  https://scikit-survival.readthedocs.io/en/stable/user_guide/00-introduction.html

**Other:**
- Fairlearn — fairness assessment in ML: https://fairlearn.org/
- If you find a good paper, notebook, or explainer, post it in Slack — I'd rather
  the team share sources than duplicate searching.

*Feel free to explore beyond these, and share anything interesting you find with me!*

---

## 🤝 How We'll Work Together

**Check-ins:** During our biweekly 60-min AI Studio Lab Section meeting block
(2nd and 4th week of every month)  
**Communication:** Slack (Break Through Tech workspace)  
**Response time:** Within 48 hours on weekdays  

**Recommended Tools:**
- **Coding:** Google Colab, VS Code
- **Collaboration:** GitHub, Notion
- **Virtual Meetings:** Zoom, Google Meet

**What I'm looking for from you:** Honest documentation over polished results. If
something doesn't work, that's data. If a metric target isn't reachable with this
dataset, I want to know why — that's a more interesting finding than hitting the
number.

---

## 🚀 Getting Started

1. **Review this overview document** and note any questions for our first meeting
2. **Begin reviewing the dataset** using the Google Drive link in the email sent to
   fellows — start with [`data/README.md`](data/README.md) for download, setup, and
   validation notes
3. **Read the GitHub Projects documentation**
   [here](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects)
4. **Set up your environment** — a Colab notebook with `pandas`, `scikit-learn`,
   `lifelines`, and `shap` is enough to start

I'm excited to work with you!
