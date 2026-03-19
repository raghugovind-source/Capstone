# 📦 S3 Object Staleness Predictor

## Project Overview

This project trains a **binary classification model** to predict whether an S3 inventory object is **stale** (`is_stale = True`) using object metadata from `inventory_updated.csv` (92,369 records, 10 columns). Early identification of stale objects supports automated lifecycle management, cost reduction, and storage optimization.

---

## 🔍 Summary of Findings

| Item | Detail |
|---|---|
| **Dataset** | 92,369 S3 object records with 10 metadata features |
| **Target** | `is_stale` (Boolean — binary classification) |
| **Primary Metric** | **F1-Score (Macro)** |
| **Metric Rationale** | Class imbalance makes accuracy misleading. Macro F1 penalizes poor performance on either class equally, ensuring the model handles both stale and active objects reliably |
| **Baseline Model** | Logistic Regression |
| **Best Model** | Random Forest Classifier |
| **Top Predictors** | `days_since_last_access`, `days_since_modified`, `access_to_modify_gap`, `is_latest`, `delete_marker` |

### Key Findings

- **Recency of access is the strongest signal**: The new `last_accessed` column provides critical information — objects not accessed recently are overwhelmingly more likely to be stale. `days_since_last_access` ranked as the top feature.
- **Access-to-modification gap matters**: Objects whose last access date closely trails their modification date (i.e., written but never revisited) show high staleness rates.
- **Version and deletion state are strong predictors**: Non-latest versions (`is_latest = False`) and objects with a `delete_marker` show significantly higher staleness rates.
- **Encryption type provides signal**: Unencrypted objects correlate with older storage practices and higher staleness.
- **SMOTE** oversampling on training data only was used to address class imbalance without leaking information into the test set.
- Random Forest outperformed the Logistic Regression baseline on both F1-Macro and ROC-AUC with stable cross-validation scores.

---

## 📁 Project Structure

```
│
├── README.md                          ← Project overview and findings (this file)
├── requirements.txt                   ← Python dependencies
├── inventory_updated.csv              ← Raw dataset (user-supplied)
└── staleness_predictor.ipynb          ← Main Jupyter notebook
```

---

## 🚀 How to Run

### 1. Install dependencies
```bash
pip install -r requirements.txt
```

### 2. Place your dataset
```
inventory_updated.csv
```

### 3. Launch the notebook
```bash
jupyter notebook staleness_predictor.ipynb
```

---

## 📓 Notebook

👉 **[staleness_predictor.ipynb](staleness_predictor.ipynb)**

---

## 🛠 Dependencies

| Library | Purpose |
|---|---|
| `pandas` | Data loading, cleaning, feature engineering |
| `numpy` | Numerical operations |
| `matplotlib` | Base static plotting |
| `seaborn` | Statistical visualizations |
| `plotly` | Interactive visualizations |
| `scikit-learn` | Modeling, preprocessing, evaluation |
| `imbalanced-learn` | SMOTE oversampling |
