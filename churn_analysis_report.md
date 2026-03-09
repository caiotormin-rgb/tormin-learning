# Telco Customer Churn Analysis Report

**Dataset:** IBM Telco Customer Churn (Kaggle — `yeanzc/telco-customer-churn-ibm-dataset`)
**Scope:** 7,043 customers in California · 33 features · Q-end snapshot

---

## 1. Dataset Overview

The dataset captures one quarter of customer activity for a US telecom provider. Each row is one customer, with fields covering demographics, subscribed services, billing, and churn outcomes.

### Key Fields Used

| Category | Fields |
|----------|--------|
| Demographics | Gender, Senior Citizen, Partner, Dependents, Tenure Months |
| Location | City, Zip Code, Latitude, Longitude |
| Services | Phone Service, Multiple Lines, Internet Service, Online Security, Online Backup, Device Protection, Tech Support, Streaming TV/Movies |
| Billing | Contract, Paperless Billing, Payment Method, Monthly Charges, Total Charges |
| Target | **Churn Label** (`Yes` / `No`) |

### Excluded (leaky or derived)
- `Churn Score` — pre-computed IBM SPSS propensity score, not raw behaviour
- `Churn Value` — binary encoding of the target
- `Churn Reason` — only available post-churn
- `CLTV` — corporate forward-looking formula, not a causal feature

---

## 2. Exploratory Analysis

### Geographical Distribution
A scatter plot of customer locations (coloured by churn status) filtered to month-to-month contracts showed no strong regional clustering — churn is distributed across California rather than concentrated in specific cities.

### Churn by Contract Type
Month-to-month customers churn at a significantly higher rate than one-year or two-year contract holders. This made month-to-month the natural segment to focus modelling on.

### Churn by Internet Service
Fiber optic customers have the highest mean churn rate among all internet service types, despite (or because of) paying more. DSL customers churn at a lower rate; customers with no internet service churn least.

### Monthly Charges by Churn Status
A violin plot showed churned customers have a higher and wider distribution of monthly charges — they tend to be on more expensive plans, likely fiber optic bundles, and leave before recouping cost for the company.

---

## 3. Modelling

### Scope
Model was restricted to **month-to-month customers only** (≈ 3,900 rows, ≈ 55% of total). These are the highest-risk segment and the most actionable — contracted customers have exit penalties, making churn interventions less relevant there.

`Contract` was dropped as a feature since it is constant within this filtered dataset.

### Target
**Churn Label** (`Yes` / `No`) — binary classification.

### Preprocessing
- Categorical encoding: `pd.get_dummies` with `drop_first=True`
- Missing values: median imputation
- Train/test split: 80/20, stratified by target

### Class Imbalance
Even within month-to-month customers, churn rates are uneven. `class_weight='balanced'` was applied to the classifier, which scales each class's loss contribution inversely to its frequency:

$$w_c = \frac{n\_\text{samples}}{n\_\text{classes} \times n\_\text{samples}_c}$$

This up-weights churners during training, improving recall for the minority class at a small cost to overall accuracy — the right trade-off in a retention context where **missing a churner is more costly than a false alarm**.

### Model
`DecisionTreeClassifier` — `max_depth=5`, `min_samples_leaf=20`, `class_weight='balanced'`

### Evaluation

| Metric | Description |
|--------|-------------|
| Precision | Of predicted churners, how many actually churned |
| Recall | Of actual churners, how many were identified — prioritise for retention |
| F1 macro | Unweighted average F1 across both classes |
| CV Accuracy / F1 / Recall | 5-fold `StratifiedKFold` scores (mean ± std) |

---

## 4. Decision Tree Findings

### Root Split: Internet Service = Fiber Optic
The tree's first question is whether a customer has fiber optic internet. This is the single strongest churn signal in the segment — fiber customers churn at a much higher rate, likely due to higher prices and greater availability of competing fiber providers.

### Second Split: Tenure Months
On both branches, the next split is tenure. Very short-tenure customers (≤ 1.5–5.5 months) are at the highest risk regardless of service type. They are still in the comparison-shopping window and have not yet built switching inertia.

### Node Colors
- **Orange** → majority class **No** (retained) — deeper orange = more confident
- **Blue** → majority class **Yes** (churned) — deeper blue = more confident
- **Washed-out / white** → node is near 50/50 (high uncertainty)

The root node is near-white (Gini ≈ 0.5) because `class_weight='balanced'` equalises both classes going into training.

---

## 5. Path Examples

| # | Profile | Prediction | Risk Level |
|---|---------|------------|------------|
| 1 | Fiber optic · tenure ≤ 1.5 months · charges > $69.88 | **Yes** | Very high |
| 2 | No fiber · tenure ≤ 5.5 months · no Tech Support | **Yes** | High |
| 3 | Fiber optic · tenure 1.5–13.5 months · no dependents | **Yes** | Moderate |
| 4 | No fiber · tenure > 5.5 months · phone service · longitude > −116.58 | **Yes** | Moderate |
| 5 | No fiber · tenure > 5.5 months · phone service · longitude ≤ −116.58 | **No** | Low |
| 6 | Fiber optic · tenure > 13.5 months · has dependents | **No** | Very low |

**Notable pattern:** Examples 4 and 5 share the same service profile but differ only by geography. The longitude split (≈ −116.58) separates the LA metro area from the Inland Empire/Desert regions — areas with different competitive dynamics and ISP availability.

---

## 6. Key Takeaways

1. **Fiber optic + new customer = highest priority for retention.** The first 1–2 months are the most critical intervention window.
2. **Tech Support and Dependents act as stickiness anchors.** Customers without these add-ons and family ties have fewer reasons to stay.
3. **Geography matters.** Customers in more competitive areas (further east, more ISP options) churn more — geo-targeted competitive offers could help.
4. **Month-to-month is a behaviour, not just a contract.** Within this segment, tenure and service mix predict churn better than any single billing variable.
5. **Class balancing is essential.** Without it, a naive model would achieve ~73% accuracy by predicting "No" for everyone — useless for a retention team.
