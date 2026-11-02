# Classifying Climate-Induced Disaster Impacts on Nepali Households Using Machine Learning Approaches

**Evidence from the National Climate Change Survey (NCCS) 2022**

| | |
|---|---|
| **Author** | Avin Amatya (Student ID 260072, CUID 17624321) |
| **Programme** | MSc Data Science and Computational Intelligence, Softwarica College of IT & E-Commerce (Coventry University) |
| **Module** | ST7072CEM Machine Learning |
| **Language** | Python 3 · Jupyter Notebook / Google Colab |

<!-- After pushing, replace <USERNAME> and <REPO> below to activate the badge -->
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/<USERNAME>/<REPO>/blob/main/NCCS2022_Climate_Impact_Classification.ipynb)

---

## Research question

Can household characteristics recorded in Nepal's National Climate Change Survey 2022 identify households that experienced a **high impact** from climate-induced disasters?

**Objectives**
1. Build a single, leakage-safe household-level dataset from the NCCS 2022 files and derive a justified binary target.
2. Train and compare Logistic Regression, a Decision Tree and an RBF-kernel SVM in a common pipeline, evaluated with accuracy, precision, recall, F1, ROC-AUC and confusion matrices.
3. Identify the most informative household factors and examine performance across ecological belts.

---

## Repository contents

```
├── NCCS2022_Climate_Impact_Classification.ipynb   # complete analysis (code + saved outputs)
├── results.json                                   # every number reported in the paper
├── requirements.txt                               # Python dependencies
├── figures/                                       # charts produced by the notebook
└── README.md
```

---

## Data access (not included in this repository)

The NCCS 2022 microdata are **not redistributed** here, in line with the National Statistics Office (NSO) data-access conditions.

1. Request the data from the NSO Microdata Catalogue: **National Climate Change Survey 2022**, survey ID `NPL-NSO-NCCS-2022-v01` — <https://microdata.nsonepal.gov.np/index.php/catalog/157>
2. The notebook uses the Stata files in `Climate Change Survey-2022/Data/stata/`: `S01`, `S02_2`, `S04`, `S05`, `S06_3` and `Weight`.

---

## How to run

### Option A — Google Colab (recommended)
1. Upload the `Climate Change Survey-2022` folder to **My Drive** (keep it private).
2. Open the notebook in Colab (badge above, or *File → Upload notebook*).
3. Run the first code cell and approve the Google Drive prompt.
4. If your folder is elsewhere, edit `DATA_DIR` in the setup cell so it points to `.../Data/stata`.
5. *Runtime → Run all* (about 7 minutes).

### Option B — local Jupyter
```bash
pip install -r requirements.txt
export NCCS_DATA="/path/to/Climate Change Survey-2022/Data/stata"   # Windows: set NCCS_DATA=...
jupyter notebook NCCS2022_Climate_Impact_Classification.ipynb
```

All random processes use `random_state = 42`. Figures are written to `figures/` and all metrics to `results.json`.

---

## Method summary

| Step | What is done |
|---|---|
| 1. Data | 18 linked NCCS files; 6,508 households in 326 primary sampling units (PSUs) |
| 2. Features | 56 leakage-safe features (33 numeric, 23 categorical → 115 after encoding) on location, housing, assets, income, land and services; impact/perception sections and caste excluded; land converted to hectares |
| 3. Target | `high_impact = 1` if the household rated any of 19 disasters as high or very high (severity ≥ 4): 2,536 households (39.0%) |
| 4. Split | `StratifiedGroupKFold` by PSU — train 5,209 households / 261 PSUs, test 1,299 / 65 PSUs (no PSU in both) |
| 5. Pipeline | median/mode imputation, standard scaling, one-hot encoding, `class_weight='balanced'` |
| 6. Models | Logistic Regression (transparent baseline), Decision Tree (rule-based screening), SVM-RBF (risk ranking), each tuned by grouped 5-fold `GridSearchCV` on F1 |
| 7. Imbalance | class weights vs. SMOTE vs. decision-threshold tuning on out-of-fold probabilities; three naive baselines |
| 8. Explanation & audit | permutation importance, LR coefficients, tree rules, performance by ecological belt, random-vs-grouped split comparison |

---

## Key results (held-out test set, 1,299 households)

| Model | Threshold | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Logistic Regression | 0.50 | 0.545 | 0.667 | 0.600 | 0.704 |
| Decision Tree | 0.50 | 0.524 | 0.747 | 0.616 | 0.719 |
| SVM (RBF) | 0.50 | 0.559 | 0.628 | 0.591 | 0.724 |
| Logistic Regression (tuned threshold) | 0.47 | 0.531 | 0.707 | 0.607 | 0.704 |
| **Decision Tree (tuned threshold)** | **0.37** | 0.515 | **0.846** | **0.640** | 0.719 |
| SVM (tuned threshold) | 0.29 | 0.540 | 0.770 | 0.635 | 0.724 |
| Baseline: flag every household | – | 0.389 | 1.000 | 0.560 | 0.500 |

- Threshold tuning improved every model; SMOTE did not (F1 0.537–0.598).
- Most informative factors: province, house type, agricultural engagement and household assets.
- A naive random split would have overstated F1 by up to 0.084, which is why the evaluation splits by PSU.

Full figures and discussion are in the accompanying IEEE-format report.

---

## Ethics and licence

- Data used only for academic purposes under the NSO student data-access procedure; no attempt was made to identify households, and no microdata are published in this repository.
- Caste/ethnicity was excluded as a predictor. The models are intended as a decision aid for prioritising outreach, never to deny support.
- Code © 2026 Avin Amatya, shared for academic assessment. The NCCS 2022 data remain the property of the National Statistics Office, Nepal.

## Citation

National Statistics Office (NSO), *National Climate Change Survey 2022* (NPL-NSO-NCCS-2022-v01), Microdata Catalogue, Kathmandu, Nepal.
