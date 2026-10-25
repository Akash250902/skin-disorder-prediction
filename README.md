# Skin Disorder Prediction

Machine learning classifier for six erythemato-squamous skin diseases, built on the UCI Dermatology dataset. The whole project (analysis, modelling, doctor suggestions, model comparison and challenges report) lives in a single Jupyter notebook.

## Results at a glance

| Setup | Accuracy | Macro-F1 |
|---|---|---|
| Tuned Random Forest, hold-out set (74 patients) | **97.3%** (72 / 74) | **0.969** |
| Tuned Random Forest, repeated stratified CV | about 97 to 98% | about 0.977 |
| Clinical features only (no biopsy) | about 86% | 0.85 |
| Histopathology only | about 94% | 0.94 |
| All features | about 97% | 0.97 |

Four of the six diseases were classified perfectly on the hold-out set. The only two errors were one seborrheic dermatitis and one pityriasis rosea patient confused with each other.

## Problem

Predict the disease class (1 to 6) for a patient from clinical and histopathological findings:

| Code | Disease |
|---|---|
| 1 | Psoriasis |
| 2 | Seborrheic dermatitis |
| 3 | Lichen planus |
| 4 | Pityriasis rosea |
| 5 | Chronic dermatitis |
| 6 | Pityriasis rubra pilaris |

The brief asks for three tasks plus two reports: a data analysis report, a predictive model, suggestions to doctors for earliest identification, a model comparison report (best model for production) and a report on challenges faced. See `Problem statement.docx`.

## Dataset

* 366 patients, 34 attributes plus the class label
* 11 clinical features, 22 histopathological features (need a biopsy) and Age
* Ordinal scores 0 to 3, except `family_history` (0/1) and `Age`
* File: `DATASET/dataset_35_dermatology (1).csv`

## Approach

1. **Data analysis**: data quality checks, target distribution, age analysis, per-disease feature profiles, mutual information, correlation structure and clinical-vs-histopathology comparison.
2. **Modelling**: 8 models compared with repeated stratified cross-validation (including Logistic Regression, KNN, SVM, Decision Tree, Random Forest, Extra Trees, Gradient Boosting and Naive Bayes), then the top candidates were tuned with grid search. The final model was selected on cross-validated macro-F1, never on the test set.
3. **Interpretation**: permutation importance and Random Forest importances.
4. **Doctor suggestions**: how far clinical features alone get you, which diseases are hard to separate without a biopsy and which few biopsy findings add the most.
5. **Challenges report**: the data problems met and how each was handled.

## Key findings

* **Hidden missing values**: `Age` was stored as text because 8 patients had a `?` placeholder, even though `isna()` reported zero missing values. These are imputed inside the model pipeline so no information leaks from the test set.
* **Moderate class imbalance** (about 5.6 : 1), so stratified splits and macro-F1 were used alongside accuracy.
* **Top models are statistically close**: Random Forest, SVM and Logistic Regression are effectively tied after tuning, and the 74-patient hold-out set means one patient is worth about 1.35 accuracy points.
* **Histopathology drives the predictions**: clubbing of the rete ridges, fibrosis of the papillary dermis, thinning of the suprapapillary epidermis and elongation of the rete ridges rank highest.
* **Strong clinical pointers**: oral mucosal involvement appears in 67 of 72 lichen planus patients and in no other disease.
* **Without a biopsy**, seborrheic dermatitis, chronic dermatitis and pityriasis rosea are the hardest to tell apart, and a couple of targeted histology findings recover most of the gap.

These are statistical patterns in this dataset, not causal claims, and should be reviewed by dermatologists before any change in practice.

## Repository contents

```
DATASET/              UCI Dermatology data (CSV)
Problem statement.docx   Project brief
Skin dis.ipynb        Full analysis, models and reports
.gitignore
```

## How to run

```bash
git clone https://github.com/Akash250902/skin-disorder-prediction.git
cd skin-disorder-prediction
pip install numpy pandas matplotlib seaborn scikit-learn joblib jupyter
jupyter notebook "Skin dis.ipynb"
```

Run the notebook top to bottom. The last modelling cell saves the fitted pipeline as `skin_disorder_model.joblib`.

## Tech stack

Python, pandas, NumPy, scikit-learn, matplotlib, seaborn, joblib, Jupyter.
