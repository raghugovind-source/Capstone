📦 S3 Object Staleness Predictor — With Savings & Priority Groups
Project Overview
This project trains a binary classification model to predict whether an S3 object is stale, then enriches each prediction with an estimated annual storage cost savings and a priority action group — turning raw ML output into a ranked remediation queue for infrastructure teams.
Dataset: inventory_updated.csv — 92,369 records, 10 columns.
Target: is_stale (Boolean)

🔍 Summary of Findings
ItemDetailBest ModelRandom Forest ClassifierPrimary MetricF1-Score (Macro)Top Predictorsdays_since_last_access, days_since_modified, access_to_modify_gap, is_latest, delete_markerTotal Stale Objects IdentifiedSee notebook outputEstimated Annual SavingsDerived from object size × S3 Standard pricing ($0.023/GB/month)
Priority Group Framework
GroupPriority ScoreRecommended Action🔴 Critical≥ 0.75Delete immediately🟠 High0.50 – 0.75Review and delete within 30 days🟡 Medium0.25 – 0.50Archive to Glacier / Intelligent-Tiering🟢 Low< 0.25Monitor quarterly
Priority Score Formula
priority_score = (0.50 × normalised_savings)
              + (0.30 × staleness_confidence)
              + (0.20 × normalised_age)

50% savings weight — financial impact drives urgency
30% confidence weight — model certainty reduces false-positive deletions
20% age weight — older-accessed objects are more likely truly abandoned

Key Findings

days_since_accessed is the single strongest staleness predictor — access recency alone separates most stale from active objects.
Never-accessed objects (last_accessed was null) are flagged as a binary feature and independently rank as high staleness signals.
Cumulative savings curve shows that deleting the top 20% of objects by priority score typically recovers the majority of available savings — enabling phased remediation.
Bucket-level analysis reveals that a small number of buckets account for a disproportionate share of stale storage.


📁 Project Structure
s3-staleness-predictor/
│
├── README.md                          ← This file
├── requirements.txt                   ← Python dependencies
├── data/
│   └── inventory_updated.csv          ← Raw dataset (user-supplied)
└── notebooks/
    └── staleness_predictor.ipynb      ← Main Jupyter notebook

🚀 How to Run
bashpip install -r requirements.txt
jupyter notebook notebooks/staleness_predictor.ipynb
Place inventory_updated.csv in the data/ folder before running.

📓 Notebook
👉 notebooks/staleness_predictor.ipynb

🛠 Dependencies
LibraryPurposepandasData loading, cleaning, feature engineeringnumpyNumerical operationsmatplotlibStatic plottingseabornStatistical visualisationsplotlyInteractive chartsscikit-learnModelling, preprocessing, evaluationimbalanced-learnSMOTE oversampling
