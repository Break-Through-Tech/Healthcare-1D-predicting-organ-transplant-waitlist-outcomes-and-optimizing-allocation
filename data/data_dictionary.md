# OPTN Kidney Waitlist Analytic Extract: Fellows' Data Dictionary

**Project version:** 1.0  
**Last verified:** August 27, 2026  
**Applies to:** `data/restricted/kidney_waitlist_analytic.csv.gz` (494,862 rows,
38 columns; listings from January 1, 2015 through June 30, 2026)

This is the working data dictionary for the project. It was built from the
delivered file's column names and aggregate values, official OPTN/UNOS forms and
instructions, and internal consistency checks. It is sufficient for the proposed
classification and survival analyses. It is not a replacement for a
delivery-matched STAR data dictionary if one later becomes available.

No row-level restricted data is reproduced here.

## How to read this dictionary

The default prediction point is **the time of initial listing**. The modeling
guidance below assumes the question is: “What could we have known when this
candidate entered the waitlist?” A field that is useful for describing an outcome
is not automatically a valid model feature.

| Mark | Meaning |
| -- | -- |
| **Baseline** | Available at or by initial listing; eligible for consideration as a feature, subject to missingness and fairness review. |
| **Sensitive** | Available at listing but represents a protected attribute, geography, or institutional practice; use deliberately and audit subgroup effects. |
| **Outcome** | Created or updated after listing, or directly reveals the endpoint; never use as a listing-time feature. |
| **Identifier** | Record, patient, center, donor, or transplant identifier; do not use as a clinical feature. |

Every decoded value also has an evidence level:

- **Official:** supported by an OPTN, UNOS, HRSA, OMB, or SRTR source.
- **Project-verified:** established by recomputing aggregate relationships in the
  delivered extract.
- **Corroborated:** supported by a public historical STAR dictionary copy or
  published research, but not verified against the exact delivery version.
- **Unresolved:** the precise legacy/version-specific label has not been verified.
  Keep the raw code or use only the broader project grouping; do not invent a
  label.

## Complete field catalog

| Field | Plain-language meaning | Observed format | Role at listing | Project guidance |
| -- | -- | -- | -- | -- |
| `ON_DIALYSIS` | Whether the candidate had received regularly administered dialysis for end-stage renal disease | `Y`, `N` | Baseline | Decode as yes/no. All rows are populated; the field does not necessarily mean “on dialysis today.” |
| `A2A2B_ELIGIBILITY` | Whether a blood type B candidate is eligible to receive an A2 or A2B kidney under the applicable allocation pathway | `1`, `0`, missing | Baseline | `1` = eligible and `0` = not eligible. Missing is **unknown/not applicable**, not automatically no. |
| `GENDER` | Candidate sex recorded by OPTN | `F`, `M` | Sensitive | Preserve the source label `GENDER`; do not relabel it as gender identity. Audit performance by group. |
| `ABO` | Candidate blood group, including A subtyping where recorded | `O`, `A`, `B`, `AB`, `A1`, `A2`, `A1B`, `A2B` | Baseline | Treat as categorical. Do not coerce the subtype values to missing. |
| `BMI_TCR` | Body mass index reported on the Transplant Candidate Registration form | Numeric kg/m²; 1,558 missing | Baseline | Check implausible values and document any clipping or imputation. |
| `FUNC_STAT_TCR` | Functional status at candidate registration: Karnofsky scale for adults or Lansky scale for children | Coded integer; 3,975 missing | Baseline | Decode with the functional-status table below. Retain age/scale context. |
| `INIT_STAT` | Waitlist medical/status code at the start of the observed listing | Coded integer | Baseline | Decode with the status table below. Keep unresolved code `4020` separate. |
| `INIT_CPRA` | Calculated panel-reactive antibody (cPRA) at the start of the listing | Percentage, normally 0–100; 142,741 missing | Baseline | High missingness. Add a missingness indicator if imputing and report missingness by demographic group. |
| `END_CPRA` | cPRA at the end of follow-up or latest recorded endpoint | Percentage; 142,448 missing | Outcome | Exclude from listing-time features because it can incorporate information recorded after listing. |
| `REM_CD` | Reason the candidate was removed from the waitlist | Coded integer; missing for candidates still waiting | Outcome | Directly determines the project outcome. Decode only for outcome construction and reporting. |
| `DAYSWAIT_CHRON` | Chronological days accrued on the waitlist through the endpoint | Numeric days | Outcome | Exclude. In this extract it is effectively a direct proxy for `days_to_event`. |
| `END_STAT` | Waitlist medical/status code at the end of the observed listing | Coded integer | Outcome | Exclude from listing-time features. |
| `INIT_AGE` | Candidate age at initial listing | Integer years, observed 0–91 | Baseline | Consider nonlinear effects and clinically meaningful age bands; assess fairness implications. |
| `DIALYSIS_DATE` | Date dialysis began, when recorded | Date; missing for 134,245 rows | Baseline | Derive dialysis duration as `INIT_DATE - DIALYSIS_DATE`, never from an outcome date. Missing generally aligns with `ON_DIALYSIS = N`, but validate exceptions. |
| `END_DATE` | End of the observed waitlist episode or administrative follow-up date | Date | Outcome | Exclude from features; use for follow-up calculations and validation only. |
| `INIT_DATE` | Initial listing date for the observed episode | Date | Baseline | Use for cohort definition, calendar-period adjustment, and temporal train/test splits. |
| `ETHCAT` | OPTN race/ethnicity category | Coded integer | Sensitive | Decode with the table below. Use for fairness evaluation; if used as a feature, explain why and compare models with and without it. |
| `PT_CODE` | De-identified patient code | Identifier | Identifier | Use only for linkage and duplicate checks. Never use as a feature or publish values. |
| `DAYSWAIT_ALLOC` | Allocation waiting-time measure through the endpoint | Numeric days | Outcome | Exclude from listing-time features; use only when the precise allocation-time definition fits the analysis. |
| `COMPOSITE_DEATH_DATE` | Composite date of death assembled from available OPTN death information | Date, often missing | Outcome | Exclude from features. Use only for endpoint validation under the data-use rules. |
| `REGION` | OPTN geographic region of the listing center | Integer 1–11 | Sensitive | The number is the region label, not an ordinal score. Treat categorically and audit geographic differences. |
| `WL_ID_CODE` | De-identified waitlist-registration identifier; one row per value in this extract | Identifier | Identifier | Use as the row key. Never use as a feature or publish values. |
| `PREV_TX` | Prior-transplant indicator carried on the transplant-related record | `Y`, `N`, missing | Outcome in this extract | Although prior-transplant history can conceptually predate listing, this delivered field is populated almost only for transplanted candidates. Its missingness therefore reveals the outcome; exclude as delivered. |
| `TX_DATE` | Transplant date | Date, populated for transplant records | Outcome | Direct outcome leakage. Use only to validate transplant endpoints. |
| `DON_TY` | Donor type for the transplant | `C`, `L`, `F`, missing | Outcome | Decode below. Populated only after a transplant; exclude from features. |
| `DIAG_KI` | Kidney diagnosis code on the transplant-related record | Coded integer, missing for 265,540 rows | Outcome in this extract | The clinical concept may predate transplant, but the field is populated almost exclusively for transplanted candidates here. Its missingness leaks the outcome; exclude as delivered. |
| `MULTIORG` | Whether the transplant involved multiple organs | `Y`, missing | Outcome | Missing is not a verified “no.” Populated from transplant records; exclude from features. |
| `ORGAN` | Organ recorded for the transplant | `KI`, `KP`, missing | Outcome | `KI` = kidney; `KP` = kidney-pancreas. Exclude from listing-time features. |
| `PSTATUS` | Recipient patient survival status on the transplant follow-up record | `0`, `1`, missing | Outcome | `0` = living and `1` = dead (corroborated). Exclude from features. |
| `PTIME` | Post-transplant patient follow-up/survival time | Numeric, often missing | Outcome | Not waitlist time. Exclude from features and from the proposed waitlist survival endpoint. |
| `TRR_ID_CODE` | De-identified Transplant Recipient Registration identifier | Identifier, missing without transplant | Identifier / outcome | Use only for linkage and validation. Its presence also reveals transplant. |
| `DONOR_ID` | De-identified donor identifier | Identifier, missing without transplant | Identifier / outcome | Never use as a feature or publish values. |
| `LISTING_CTR_CODE` | De-identified listing-center code | 256 observed categories | Identifier / sensitive | Use for grouped evaluation or hierarchical sensitivity analyses, not as an unconstrained patient-risk feature. Do not publish small center-level cells. |
| `outcome` | Project-created seven-category observed outcome | Text category | Outcome | Primary source for descriptive outcome groups; definitions are below. Never use as a feature. |
| `event_adverse` | Project-created binary adverse-event flag | `1`, `0` | Outcome | `1` for `died` or `removed_too_sick`; otherwise `0`. Candidate classification target. |
| `event_transplant` | Project-created binary transplant-event flag | `1`, `0` | Outcome | `1` only for `outcome = transplanted`; otherwise `0`. Event indicator for the proposed transplant survival analysis. |
| `censored` | Project-created right-censoring flag | `1`, `0` | Outcome | `1` only for `outcome = still_waiting`. Competing outcomes require an explicit analysis decision; they are not automatically equivalent to censoring. |
| `days_to_event` | Project-created follow-up time from `INIT_DATE` to the selected endpoint | Numeric days; 59 missing | Outcome | Duration for time-to-event work. Investigate the 59 negative-date cases and the 790 same-day records before modeling. |

## Categorical value lookups

### Yes/no-style fields

| Field | Code | Meaning | Evidence |
| -- | -- | -- | -- |
| `ON_DIALYSIS` | `Y` / `N` | Yes / no | Official field definition; project-verified values |
| `A2A2B_ELIGIBILITY` | `1` / `0` | Eligible / not eligible | Official policy concept; project-verified values |
| `PREV_TX` | `Y` / `N` | Yes / no | Corroborated field definition; unsafe as delivered |
| `MULTIORG` | `Y` | Yes | Corroborated field definition; missing is unresolved, not a verified no |

### Blood group and donor/organ codes

| Field | Code | Meaning | Evidence |
| -- | -- | -- | -- |
| `ABO` | `O`, `A`, `B`, `AB` | Standard candidate ABO blood group | Official |
| `ABO` | `A1`, `A2`, `A1B`, `A2B` | A-antigen subtype recorded in addition to ABO group | Official |
| `DON_TY` | `C` | Deceased/cadaveric donor | Corroborated |
| `DON_TY` | `L` | Living donor | Corroborated |
| `DON_TY` | `F` | Unknown donor type; a secondary source suggests foreign donor | Unresolved (one row) |
| `ORGAN` | `KI` | Kidney | Corroborated; project-verified context |
| `ORGAN` | `KP` | Kidney-pancreas | Corroborated; project-verified context |
| `PSTATUS` | `0` | Living | Corroborated |
| `PSTATUS` | `1` | Dead | Corroborated |

### Race/ethnicity (`ETHCAT`)

These are source-system categories, not biological variables. Preserve the
categories for subgroup auditing and report the source terminology.

| Code | OPTN category | Evidence |
| --: | -- | -- |
| `1` | White | Official form; corroborated code mapping |
| `2` | Black or African American | Official form; corroborated code mapping |
| `4` | Hispanic/Latino | Official form; corroborated code mapping |
| `5` | Asian | Official form; corroborated code mapping |
| `6` | American Indian or Alaska Native | Official form; corroborated code mapping |
| `7` | Native Hawaiian or other Pacific Islander | Official form; corroborated code mapping |
| `9` | Multiracial | Official form; corroborated code mapping |
| `998` | Unknown | Corroborated code mapping |

### Functional status (`FUNC_STAT_TCR`)

For adults, codes `2010` through `2100` represent the Karnofsky performance scale
from 10% through 100% in 10-point increments. For pediatric candidates, codes
`4010` through `4100` represent the Lansky performance scale over the same
percentage range. Higher percentages mean greater functional independence.

| Codes | Decode rule | Evidence |
| -- | -- | -- |
| `2010`, `2020`, …, `2100` | Adult Karnofsky 10%, 20%, …, 100% | Official scale on the UNOS candidate-registration worksheet; corroborated code pattern |
| `4010`, `4020`, …, `4100` | Pediatric Lansky 10%, 20%, …, 100% | Official scale on the UNOS candidate-registration worksheet; corroborated code pattern |
| `996` | Not applicable: candidate under one year old | High; retain as a separate category |
| `998` | Unknown | Corroborated |
| `1` | Performs activities of daily living without assistance | High; broad ADL category, not a 1% score |

Do not combine adult and pediatric codes as raw numbers. Decode them into
`functional_scale` and `functional_percent`, retaining an unknown/anomalous flag.

### Waitlist status (`INIT_STAT`, `END_STAT`)

The delivered extract contains codes `4010`, `4020`, `4050`, `4060`, and `4099`.
The labels below are reconstructed from a public SRTR format-codebook mirror and
are consistent with official OPTN forms. Code `4020` is not present in that
public lookup, so it must remain unknown. Do not treat the numbers as ordered.

| Code | `INIT_STAT` count | `END_STAT` count | Reconstructed label | Evidence |
| --: | --: | --: | -- | -- |
| `4010` | 348,947 | 337,478 | Active | High, reconstructed |
| `4020` | 19 | 66 | Unknown/legacy kidney status | Unresolved |
| `4050` | 196 | 49 | Active — medically urgent | High, reconstructed |
| `4060` | 30 | 14 | Active — critical status | High, reconstructed |
| `4099` | 145,670 | 157,255 | Temporarily inactive | High, reconstructed |

`END_STAT` is outcome-time information regardless of how its values are decoded.

### Removal reason (`REM_CD`)

The stable project grouping below was reproduced from the delivered file: every
observed nonmissing removal code maps to exactly one project `outcome`. This is
enough to construct the project's endpoints even where the finer historical label
is version-sensitive.

| Code | Project `outcome` | Finer interpretation when supported | Evidence level |
| --: | -- | -- | -- |
| missing | `still_waiting` | No recorded removal by the follow-up cutoff | Project-verified |
| `4` | `transplanted` | Deceased-donor transplant; removed by transplanting center | Official |
| `6` | `unknown` | Candidate refused transplant | Corroborated historical label |
| `7` | `removed_administrative` | Transferred to another center | Corroborated historical label |
| `8` | `died` | Candidate died while listed | Official |
| `9` | `removed_administrative` | Other reason | Official current instruction / corroborated |
| `12` | `unknown` | Condition improved; transplant no longer needed | Corroborated historical label |
| `13` | `removed_too_sick` | Too sick for transplant | Official concept; corroborated code |
| `14` | `transplanted_elsewhere` | Transplanted at another center | Corroborated historical label |
| `15` | `transplanted` | Living-donor transplant; removed by transplanting center | Official |
| `16` | `removed_administrative` | Candidate removed in error | High, reconstructed |
| `17` | `removed_administrative` | Changed to kidney-pancreas (`KP`) by the system | High, reconstructed |
| `18` | `transplanted` | Emergency deceased-donor transplant | Corroborated historical label |
| `21` | `died` | Died during the transplant procedure | Official |
| `22` | `transplanted_elsewhere` | Transplant outside the United States / elsewhere | Official concept; project-verified grouping |
| `24` | `removed_administrative` | Unable to contact candidate | Official/high |
| `40` | `removed_administrative` | Precise administrative label unresolved | Project-verified grouping |
| `43` | `transplanted` | Transplant subtype/removing-center detail unresolved | Project-verified grouping |
| `45` | `transplanted` | Transplant subtype/removing-center detail unresolved | Project-verified grouping |

Do not use `REM_CD` to predict `outcome`, `event_adverse`, or transplant. It is a
source variable from which those outcomes were derived.

### Kidney diagnosis (`DIAG_KI`)

The extract contains 69 diagnosis codes, mostly in the `3000`–`3074` family plus
`999`. The public STAR definition describes `DIAG_KI` as the kidney recipient's
primary diagnosis at transplant, with transplant-recipient data taking
precedence over candidate-registration data. The exact labels below come from a
public SRTR format-codebook mirror. They are **high-confidence reconstructed
labels**, not delivery-matched official labels.

| Code | Reconstructed kidney diagnosis | Rows |
| --: | -- | --: |
| `999` | Other, specified separately | 16,911 |
| `3000` | Idiopathic/post-infectious crescentic glomerulonephritis | 296 |
| `3001` | Membranous glomerulonephritis | 1,704 |
| `3002` | Mesangiocapillary type 1 glomerulonephritis | 157 |
| `3003` | Mesangiocapillary type 2 glomerulonephritis | 34 |
| `3004` | IgA nephropathy | 12,520 |
| `3005` | Anti-GBM disease | 307 |
| `3006` | Focal glomerular sclerosis / focal segmental glomerulosclerosis | 13,544 |
| `3007` | Chronic pyelonephritis / reflux nephropathy | 1,404 |
| `3008` | Polycystic kidneys | 17,962 |
| `3009` | Nephritis | 973 |
| `3010` | Nephronophthisis | 395 |
| `3013` | Oxalate nephropathy, including hereditary oxalosis | 296 |
| `3014` | Cystinosis | 154 |
| `3015` | Fabry disease | 126 |
| `3016` | Amyloidosis | 579 |
| `3017` | Gout | 44 |
| `3018` | Systemic lupus erythematosus | 6,227 |
| `3019` | Progressive systemic sclerosis | 17 |
| `3020` | Wilms tumor | 149 |
| `3021` | Renal cell carcinoma | 620 |
| `3022` | Incidental carcinoma | 3 |
| `3023` | Myeloma | 301 |
| `3024` | Hemolytic uremic syndrome | 588 |
| `3025` | Hypoplasia / dysplasia / dysgenesis / agenesis | 2,193 |
| `3026` | Cortical necrosis | 179 |
| `3027` | Acute tubular necrosis | 1,070 |
| `3028` | Medullary cystic disease | 310 |
| `3029` | Sickle cell anemia | 146 |
| `3030` | Acquired obstructive nephropathy | 957 |
| `3031` | Alport syndrome | 2,013 |
| `3032` | Familial nephropathy | 151 |
| `3033` | Goodpasture syndrome | 433 |
| `3034` | Malignant hypertension | 1,509 |
| `3035` | Henoch-Schönlein purpura | 162 |
| `3036` | Prune belly syndrome | 240 |
| `3037` | Retransplant / graft failure | 15,859 |
| `3040` | Hypertensive nephrosclerosis | 43,325 |
| `3041` | Chronic glomerulonephritis, unspecified | 3,711 |
| `3042` | Membranous nephropathy | 1,006 |
| `3043` | Chronic glomerulosclerosis, unspecified | 640 |
| `3044` | Analgesic nephropathy | 411 |
| `3045` | Radiation nephritis | 30 |
| `3046` | Antibiotic-induced nephritis | 63 |
| `3047` | Cancer chemotherapy-induced nephritis | 275 |
| `3048` | Calcineurin inhibitor nephrotoxicity | 1,757 |
| `3049` | Heroin nephrotoxicity | 15 |
| `3050` | Renal artery thrombosis | 65 |
| `3051` | Chronic nephrosclerosis, unspecified | 325 |
| `3052` | Congenital obstructive uropathy | 2,226 |
| `3053` | Scleroderma | 101 |
| `3054` | Wegener granulomatosis (now generally called granulomatosis with polyangiitis) | 992 |
| `3055` | Polyarteritis | 140 |
| `3056` | Rheumatoid arthritis | 22 |
| `3057` | Sarcoidosis | 205 |
| `3058` | Lymphoma | 21 |
| `3059` | Nephrolithiasis | 574 |
| `3060` | Urolithiasis | 20 |
| `3062` | Pre-bone-marrow-transplant total-body irradiation | 9 |
| `3063` | Drug-related interstitial nephritis | 497 |
| `3064` | Thin basement membrane disease | 223 |
| `3066` | Cholesterol embolization | 6 |
| `3068` | Rapidly progressive glomerulonephritis | 358 |
| `3069` | Diabetes mellitus type I | 5,600 |
| `3070` | Diabetes mellitus type II | 58,345 |
| `3071` | Diabetes mellitus type other/unknown | 694 |
| `3072` | Hepatorenal syndrome | 5,123 |
| `3073` | Lithium toxicity | 987 |
| `3074` | HIV nephropathy | 1,023 |
| missing | No `DIAG_KI` value in this extract | 265,540 |

These labels are useful for audit and descriptive work, but the field remains
unsafe for the initial listing-time model: it is populated almost exclusively for
transplanted candidates in this assembled extract, so its missingness leaks the
outcome. If a later extract supplies diagnosis consistently for all candidates,
verify this lookup against its era-matched documentation before modeling.

## Project-created outcomes

| `outcome` value | Rows | Endpoint interpretation |
| -- | --: | -- |
| `transplanted` | 234,429 | Transplant event for `event_transplant` |
| `still_waiting` | 103,300 | Administratively right-censored at follow-up cutoff |
| `removed_administrative` | 45,332 | Competing removal; handling depends on the estimand |
| `transplanted_elsewhere` | 37,243 | Competing/alternate transplant outcome; not counted in the current `event_transplant` flag |
| `removed_too_sick` | 35,219 | Adverse event |
| `died` | 34,524 | Adverse event |
| `unknown` | 4,815 | Unresolved outcome; exclude or use a documented sensitivity analysis |

For the binary classification target,
`event_adverse = 1` for `died` or `removed_too_sick` (69,743 rows). For the
transplant time-to-event target, `event_transplant = 1` only for
`transplanted` (234,429 rows). Administrative removal, transplant elsewhere,
death, and removal as too sick are competing outcomes, not automatically ordinary
right-censoring. State the chosen estimand before deciding how to handle them.

## Recommended listing-time feature set

A defensible starting set is:

```text
ON_DIALYSIS, A2A2B_ELIGIBILITY, GENDER, ABO, BMI_TCR,
FUNC_STAT_TCR, INIT_STAT, INIT_CPRA, INIT_AGE, DIALYSIS_DATE,
INIT_DATE, ETHCAT, REGION
```

`GENDER`, `ETHCAT`, `REGION`, and center effects require deliberate fairness and
policy analysis. `LISTING_CTR_CODE` is better used for grouped evaluation or a
hierarchical sensitivity analysis than as a direct patient-risk feature.

Explicitly exclude all derived targets and identifiers, plus `END_CPRA`,
`REM_CD`, `DAYSWAIT_CHRON`, `END_STAT`, `END_DATE`, `DAYSWAIT_ALLOC`,
`COMPOSITE_DEATH_DATE`, `PREV_TX`, `TX_DATE`, `DON_TY`, `DIAG_KI`, `MULTIORG`,
`ORGAN`, `PSTATUS`, and `PTIME` from the listing-time feature matrix in this
delivery.

## Known data-quality checks

- The supplied `column_manifest.csv` does not match the analytic CSV header. The
  CSV header is the source of truth for this dictionary.
- Fifty-nine records have an endpoint date earlier than `INIT_DATE` and are
  missing `days_to_event`; investigate or exclude them under a documented rule.
- There are 790 same-day records with `days_to_event == 0`; confirm that the
  selected model can represent them.
- Missingness is informative in several transplant-side fields. Never convert all
  missing values to “no” or “normal” without field-specific justification.
- Re-run the aggregate code inventory when the restricted extract changes. A new
  code is not proof that the dictionary is wrong; it is a prompt to research and
  version the lookup.

## Sources and provenance

1. [HRSA overview of the OPTN
   database](https://www.hrsa.gov/optn/data-calculators/about-optn-data/optn-database)
   — official scope and collection context.
2. [Adult Kidney Candidate Listing Registration
   worksheet](https://www.reginfo.gov/public/do/DownloadDocument?objectID=159330900)
   and [UNOS Adult Kidney Transplant Candidate Registration
   worksheet](https://unos.org/wp-content/uploads/Adult-TCR-Kidney.pdf) — official
   field labels and candidate-registration response scales.
3. [Federal Candidate Registration Listing Removal
   worksheet](https://www.reginfo.gov/public/do/DownloadDocument?objectID=159357301)
   — current OPTN/HRSA removal form and response definitions.
4. [OPTN kidney allocation
   FAQs](https://www.hrsa.gov/optn/patients/resources/kidney/kidney-allocation-faqs)
   — official explanation of A2/A2B eligibility and allocation.
5. [SRTR technical methods for program-specific
   reports](https://srtr.hrsa.gov/transplant-professionals/program-specific-report/technical-methods-for-the-program-specific-reports/)
   — official definitions for cPRA, BMI, diagnosis groupings, and demographic
   reporting.
6. [CDC/USRDS linked-data
   codebook](https://www.cdc.gov/nchs/data/datalinkage/patient-profile.pdf) — federal
   corroboration for many historical UNOS removal codes.
7. [Public SRTR format-codebook
   mirror](https://github.com/VagishHemmige/sRtr/blob/master/data-raw/SRTR_format_codebooks.xlsx)
   and [public historical STAR variable-definition
   copy](https://github.com/onetomapanalytics/Meta_Data/wiki/STAR---Data-dictionary)
   — corroborating, non-authoritative definitions for legacy field names and code
   values. Neither is treated as delivery-matched documentation.
8. [`docs/research/optn-star-code-research.md`](../docs/research/optn-star-code-research.md)
   — project research log with source-by-source findings and unresolved items.

## Maintenance rule

When changing a definition, record the source, source date/version, affected
field/code, and verification date. Prefer an official document that matches the
record era. If sources disagree, preserve the raw code, use the broader
project-verified grouping where available, and mark the fine label unresolved
rather than selecting the most convenient meaning.
