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

---

## Pipeline

---

## Installation


---

## Acknowledgements
