# When AUROC Lies: Bidirectional Cross-Hospital Validation and Recalibration of Routine-CBC Dengue Classifiers

Reproducible code and analysis for a study on whether machine-learning dengue classifiers built from routine complete-blood-count (CBC) data actually transfer between hospitals — and how to fix them when they do not.

---

## Overview

Most published CBC-based dengue models are trained and tested on a **single hospital** and report near-perfect accuracy, but their portability to a new institution is rarely tested. Using two independent Bangladeshi cohorts, we perform the **first bidirectional cross-hospital external validation** of CBC-based dengue classification and show that:

- **Ranking transfers, decisions do not.** Cross-hospital AUROC stays near-perfect (0.991 and 1.000), yet at the default 0.5 threshold decision quality collapses **asymmetrically** — sensitivity drops to 0.465 in one direction (missed cases) and precision drops to 0.790 in the other (over-referral).
- **A cheap fix works.** A small target-hospital calibration subset + Youden's J threshold selection + isotonic recalibration restores sensitivity to **0.992**, precision to **1.000**, and expected calibration error (ECE) from **0.369 → 0.011**, with **no retraining**.
- **The cause is threshold/scale, not feature drift.** Cross-site SHAP feature rankings are identical (Spearman ρ = 1.000), so the failure is a mismatch in decision boundary and probability scale — exactly what recalibration corrects.

---

## Key results

**Internal 5-fold cross-validation (near-ceiling for every model):** AUROC 0.987–1.000.

**Bidirectional cross-hospital transfer (before vs. after target adaptation):**

| Metric | Joypurhat → Munshiganj | Munshiganj → Joypurhat |
|---|---|---|
| AUROC | 0.991 | 1.000 |
| Sensitivity (before) | 0.465 | 1.000 |
| Sensitivity (after) | 0.992 | 0.979 |
| Precision (before) | 0.996 | 0.790 |
| Precision (after) | 0.990 | 1.000 |
| ECE (before) | 0.369 | 0.165 |
| ECE (after) | 0.011 | 0.006 |

**Cross-site SHAP importance (normalized):** WBC > Platelet > Age > Sex on both hospitals; Spearman ρ = 1.000.

---

## Datasets

Two independent, publicly available Bangladeshi dengue datasets, harmonized to four common features: **age, sex, platelet count, WBC count**. Diagnostic label is binary (positive / negative).

| Cohort | Records used | Positive / Negative | Source |
|---|---|---|---|
| Joypurhat | 919 (after removing 35 "Child" rows and missing values) | 618 / 301 | Mendeley Data, DOI: [10.17632/xrsbyjs24t.1](https://doi.org/10.17632/xrsbyjs24t.1) |
| Munshiganj | 1,018 | 697 / 321 | Mendeley Data, DOI: [10.17632/673swz9tb4.1](https://doi.org/10.17632/673swz9tb4.1) |

> A third dataset (Jamalpur, DOI 10.17632/6fsrsk3mb8.2) was evaluated but **dropped**: its diagnostic labels are not separable from routine CBC (AUROC ≈ 0.68 even with all features), so it cannot anchor a meaningful model.

The datasets are **not redistributed** here — download them from the Mendeley links above and place them in a `data/` folder (or a Google Drive folder for Colab).

---

## Repository structure

```
.
├── data/                      # place the CSV datasets here (not tracked)
│   ├── Dengue_diseases_dataset_Joypurhat.csv
│   └── Dengue_clinical_dataset_Munshiganj.csv
├── notebook/
│   └── dengue_cross_hospital.ipynb   # 8-cell pipeline (Colab-ready)
├── results/
│   ├── table1_internal_5model.csv
│   ├── table2_bidirectional.csv
│   ├── table3_shap_stability.csv
│   ├── fig1_separability.(png|pdf)
│   ├── fig2_reliability.(png|pdf)
│   ├── fig3_before_after_bars.(png|pdf)
│   └── fig4_shap_stability.(png|pdf)
├── requirements.txt
├── LICENSE
└── README.md
```

---

## Pipeline

The notebook runs end-to-end in 8 cells:

1. **Setup & data load** — mount data, read both CSVs.
2. **Harmonize** — map to the common schema (age, sex, platelet, WBC + binary label); drop "Child" rows and missing values.
3. **Separability diagnostic + Figure 1** — class-wise distributions and single-feature AUCs.
4. **Five-model internal comparison (Table 1)** — Logistic Regression, Random Forest, Extra Trees, XGBoost, LightGBM via stratified 5-fold CV.
5. **Bidirectional frozen cross-hospital validation + adaptation (Table 2)** — train on source, freeze, test on target both ways; Youden's J threshold + isotonic recalibration.
6. **Reliability diagrams (Figure 2)** — before/after calibration.
7. **Sensitivity/precision bars (Figure 3)** — before/after adaptation.
8. **Cross-site SHAP stability (Figure 4 + Table 3)** — feature-importance comparison and Spearman correlation.

---

## Installation

```bash
git clone https://github.com/<your-username>/when-auroc-lies.git
cd when-auroc-lies
pip install -r requirements.txt
```

`requirements.txt`:

```
pandas
numpy
scikit-learn
xgboost
lightgbm
shap
matplotlib
scipy
```

## Usage

- **Google Colab:** open `notebook/dengue_cross_hospital.ipynb`, mount Drive, set `DATA_DIR` to your folder, and run the cells in order.
- **Local:** put the two CSVs in `data/`, adjust the paths in Cell 1, and run the notebook.

All tables and figures are written to `results/` (figures at 600 dpi PNG + vector PDF).

---

## Reproducibility notes

- Random seed fixed at `42`; results are deterministic across runs (minor ±0.001 variation possible in boosting models).
- No synthetic oversampling is used; class imbalance is handled with class weights only, so reported numbers reflect real records.
- Standardization statistics are fit on the training partition only (no leakage).
- The external model is **frozen** (trained on the source hospital only) before it is applied to the target hospital.

---

## Citation

If you use this code or analysis, please cite the paper (ICCIT 2026, under review):

```bibtex
@inproceedings{whenaurolies2026,
  title     = {When AUROC Lies: Bidirectional Cross-Hospital Validation and
               Recalibration of Routine-CBC Dengue Classifiers},
  author    = {<Author names>},
  booktitle = {Proc. Int. Conf. Computer and Information Technology (ICCIT)},
  year      = {2026},
  note      = {Under review}
}
```

Please also cite the dataset sources (Mendeley DOIs above).

---

## License

Released under the MIT License (see `LICENSE`). The datasets retain their own licenses from the original Mendeley records.

---

## Acknowledgements

Datasets courtesy of the respective Mendeley Data contributors (Joypurhat and Munshiganj cohorts). This work targets ICCIT 2026.

*Maintainer: `<your-name>` · Questions and issues welcome via the Issues tab.*
