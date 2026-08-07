# Capstone Report — <your lane>

- **Author:** Arpit Sharma 
- **Lane:** Content Trend Prediction using Machine Learning
- **Repo:** https://github.com/acecod3z/Flyrankinternship
- **Date:** August 2026

> Copy this file to `work/capstone_report.md` and fill it in as you build. The eight
> sections mirror the Pass / Needs-Work rubric axes, so nothing here is optional.

## 1. Problem framing

This project supports the decision of identifying which content pages are likely to decline in performance so that editors can prioritize updates.

The unit of analysis is an individual content page.

The output of the system is a predicted content trend category (down, stable, up, flat, or new).

A FlyRank editor can use these predictions to decide which pages should be reviewed or refreshed first.

A wrong prediction may result in unnecessary work on healthy pages or missed opportunities to refresh declining pages.

Machine learning is useful because content performance depends on multiple interacting signals such as impressions, clicks, CTR, search volume and page age. These relationships are difficult to capture with simple manual rules.

## 2. Data safety

The project used the anonymized dataset:

content_refresh_anonymized.csv

The following columns were intentionally excluded from the feature set:

content_id
client_id
trend_direction
trend_pct

content_id and client_id are pseudonymous identifiers and were only used for grouped train-test splitting.

trend_direction is the prediction target.

trend_pct was excluded because it is directly related to the target and would introduce label leakage.

No client-identifying information appears anywhere in the notebook, outputs or repository.

## 3. Baseline

The Week-4 baseline used a transparent rule-based scoring system.

The rule prioritized pages that had:

High search volume
Low CTR
Poor average search position

Each page received a score and a reason code explaining why it was ranked.

Example reason codes included:

HIGH_VOLUME_LOW_CTR_POOR_POSITION
HIGH_VOLUME_LOW_CTR
LOW_CTR
POOR_POSITION

The baseline produced an interpretable ranked queue for editors.

Unlike the machine learning model, the baseline generated rankings rather than class predictions, so direct classification metrics were not computed.

The baseline serves as an understandable decision-support system that later models should improve upon.
## 4. Model / analysis

This task is a multiclass classification problem.

Two models were trained:

Logistic Regression
Random Forest Classifier

Logistic Regression was selected because it provides an interpretable linear baseline.

Random Forest was selected because it can learn nonlinear relationships, handles mixed feature types well and provides feature importance.

The target variable was:

trend_direction

The following columns were excluded to prevent leakage:

content_id
client_id
trend_direction
trend_pct

The remaining numerical and categorical features were used after preprocessing.

Missing values were imputed.

Categorical variables were one-hot encoded.

Random seed:

42

## 5. Evaluation

A grouped train-test split was performed using GroupShuffleSplit.

Grouping was based on client_id to ensure pages from the same client did not appear in both training and testing datasets.

Training set:

80%

Testing set:

20%

Model performance:

Model	Accuracy	Precision	Recall	F1 Score
Logistic Regression	0.6351	0.6444	0.6351	0.6188
Random Forest	0.6956	0.6733	0.6956	0.6576

The Random Forest model outperformed Logistic Regression across all evaluation metrics.

Error analysis showed that most incorrect predictions occurred between the stable and up classes because their behavioural patterns overlap.

The down class achieved the highest recall, indicating stronger predictive signals.

## 6. Interpretation

The Random Forest identified the following features as the most important:

impressions_prev_30d
impressions_last_30d
impressions_90d
avg_position
days_with_impressions
content_age_days
sessions_last_30d
sessions_prev_30d
ctr
pageviews_90d

These features are consistent with expected SEO behaviour because they capture search visibility, engagement and historical performance.

No single feature dominated the model, suggesting that obvious feature leakage was avoided.

One interesting observation was that historical impression metrics contributed more than search volume alone.

## 7. Recommendation

The trained Random Forest model should be used to identify pages that are most likely to decline.

Editors can review pages predicted as down first and prioritise content updates based on the model's output.

The rule-based baseline remains useful because it provides transparent explanations for ranking pages.

Confidence in the Random Forest predictions is moderate because the model achieved approximately 69.6% accuracy on unseen grouped clients.

Future improvements may include:

Gradient Boosting models
Hyperparameter tuning
Time-aware validation
Additional historical behavioural features
Precision@K evaluation for ranking tasks

## 8. Reproducibility

Repository:

git clone https://github.com/acecod3z/Flyrankinternship.git

Install dependencies:

pip install -r requirements.txt

Run notebooks:

work/notebooks/w04_baseline_score.ipynb

work/notebooks/w05_model.ipynb

Random seed:

42

Main libraries used:

pandas
numpy
scikit-learn

The notebooks can be executed from top to bottom to reproduce the reported results.

---

> **Claims checklist before submitting:** observed / measured / directional / decision-support
> **Metrics vs. base rate:** report your task's base rate (majority-class %) next to any
> precision@K or accuracy — a high score can just be a high base rate. AUC / lift over
> baseline are the honest discrimination numbers.
> language everywhere · no causal claims without an experiment or causal design · no
> "predicted Google's algorithm" · no client-identifying details · numbers in this report
> match a fresh re-run.
