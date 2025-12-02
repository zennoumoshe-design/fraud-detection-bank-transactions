# Fraud Detection Analysis on Banking Transactions

**Role:** Data Analyst / Fraud Analyst  
**Author:** Zennou Moshe  

---

## 1. Project Overview

This project analyzes a dataset of **10,000 banking transactions**, each labeled as *fraudulent* or *legitimate*.  
The goal is to understand fraud patterns, identify high-risk behaviors, evaluate key indicators, and provide actionable recommendations for fraud prevention.

This project reflects the real work of a **Fraud Analyst** in a fintech or banking environment:

- exploring transaction data
- detecting abnormal patterns
- engineering features
- validating insights with simple ML models
- proposing business-oriented fraud-prevention strategies

The full analysis is implemented in the notebook:

👉 **`fraud_detection_analysis.ipynb`**

---

## 2. Key Business & Analytical Questions

The analysis is structured around the essential questions a Fraud Analyst must answer:

1. **What does each variable represent, and how might it relate to fraud?**  
2. **Which variables show strong logical or statistical connections with fraud?**  
3. **Which variables are irrelevant or misleading for fraud detection?**  
4. **Which features contain extreme or abnormal values worth deeper investigation?**  
5. **What meaningful fraud patterns can be extracted from the dataset?**  
6. **What is the true fraud rate overall and across segments?**  
7. **Which features are the strongest predictors of fraud?**  
8. **What are the limitations of the dataset?**  
9. **What concrete fraud-prevention actions should the business take?**

---

## 3. Dataset Description

Each row represents a single financial transaction with the following fields:

### Transaction Attributes

- `transaction_id`
- `user_id`
- `amount`
- `transaction_type` (ATM, POS, Online, QR)
- `merchant_category` (Travel, Clothing, Grocery…)
- `country`
- `hour` (0–23)

### Risk Indicators

Simulated scores from 0 to 1:

- `device_risk_score`
- `ip_risk_score`

### Target Variable

- `is_fraud` (0 = legitimate, 1 = fraud)

---

## 4. Key Findings from Data Exploration

### 4.1 Fraud Rate

- Global fraud rate: **5%** (500 fraudulent transactions)  
This is realistic for fintech platforms.

---

### 4.2 Fraud by Country

- **Nigeria (NG): 100% fraud rate**  
- All other countries: **3–5%**

NG is a simulated "high-risk region" in this dataset.

---

### 4.3 Fraud by Transaction Type

Fraud rate (descending):

1. **ATM** – 5.5%  
2. **Online** – 5.2%  
3. **QR** – 4.8%  
4. **POS** – 4.5%

Fraud occurs across all channels → risk is diffuse.

---

### 4.4 Fraud by Merchant Category

Most fraud-prone categories:

- **Travel**
- **Clothing**
- **Electronics**

Typical for high-value or easily resold goods.

---

### 4.5 Fraud by Hour

- From **00:00 to 05:00**, almost all transactions are fraudulent,  
  but volumes are extremely low (not enough to define a robust rule).

During daytime, some country-specific peaks appear (e.g. US, UK, FR).

---

## 5. Visual Exploration

### Amount Distribution

- Most transactions have low amounts.
- Fraud also occurs at low amounts → “test frauds”.
- Larger fraudulent outliers exist → “cash-out frauds”.

### Risk Score Distributions

Clear separation:

- Non-fraud: scores between **0.0–0.3**
- Fraud: scores between **0.7–1.0**

### Boxplots

- Fraud amounts: highly variable with extreme values.
- Risk scores: no overlap between fraud and non-fraud.

➡ Risk scores are by far the strongest predictors in this synthetic dataset.

---

## 6. Correlation Analysis

Strongest correlations with fraud:

- `device_risk_score` → **0.87**
- `ip_risk_score` → **0.87**
- `amount` → **0.64**

Weak correlations:

- `hour`
- `transaction_type`
- `merchant_category`
- `country` (individually)

These variables are only useful when combined with others.

---

## 7. Feature Engineering

Engineered features:

- `is_high_amount` (flag for large transactions)
- `is_high_device_risk` (device_risk_score > 0.7)
- `is_high_ip_risk` (ip_risk_score > 0.7)
- `combined_risk_score` (average of device and IP risk)
- `is_night` (transactions between 00:00 and 05:00)

In this dataset, binary features such as `is_high_device_risk` and `is_high_ip_risk` are almost perfectly predictive of fraud.

---

## 8. Machine Learning (Lightweight Models)

Two simple models were trained:

### Logistic Regression

- **100% accuracy, precision, and recall**  
- Because risk scores are already perfectly separated between fraud and non-fraud.

### Random Forest

- Also **100% accuracy**
- Provides feature importances to validate key predictors.

### Top Predictive Features (after removing IDs)

1. `is_high_device_risk`
2. `is_high_ip_risk`
3. `combined_risk_score`
4. `device_risk_score`
5. `ip_risk_score`
6. `amount`

Least useful features:

- `transaction_type`
- `merchant_category`
- `hour`
- `user_id`
- Most `country` dummies (except NG)

---

## 9. Fraud Patterns Identified

### Pattern A — High-Risk Device/IP Signature

- High device risk  
- High IP risk  

➡ Almost guaranteed fraud in this dataset.

---

### Pattern B — Low-Amount Test Fraud

Fraudsters start with very small transactions to test card validity,  
then escalate to larger amounts.

---

### Pattern C — Large-Amount Cash-Out

High-value frauds in:

- Travel
- Electronics
- Clothing

➡ Likely “cash-out” or purchase of resellable goods.

---

### Pattern D — High-Risk Country

- Nigeria shows extreme concentration of fraud.  
- Simulates markets with poor regulation or higher fraud exposure.

---

### Pattern E — Nighttime Transactions

Night hours (00:00–05:00) are mostly fraudulent in the dataset,  
but the volume is too low to build a robust rule alone.

---

## 10. Dataset Limitations

- Synthetic dataset → too clean and too separable.
- Risk scores are unrealistically predictive.
- No user behavior history (no past transaction patterns).
- No device ID, card ID, or account-level features.
- No geographic consistency checks.
- No transaction velocity features (e.g. X transactions in Y minutes).

In real-world environments, fraud signals are much noisier.

---

## 11. Business Recommendations

### 1. Auto-block high-risk device/IP scores

Block or strongly challenge transactions where:

- `device_risk_score > 0.7` **OR**
- `ip_risk_score > 0.7`

### 2. Flag for manual review

Flag transactions for manual review when:

- amount is very high
- merchant category is Travel / Electronics / Clothing
- transaction occurs at night
- country is considered high-risk

### 3. Implement a combined fraud score

Example:

`fraud_score = 0.5 * combined_risk_score + 0.3 * normalized_amount + 0.2 * country_risk_score`

### 4. Deploy a lightweight ML model

A Logistic Regression using:

- `device_risk_score`
- `ip_risk_score`
- `amount`
- `country` (one-hot encoded)

is highly effective and easy to explain to business stakeholders.

---

## 12. Global Conclusion

The analysis shows that fraud in this dataset:

- is primarily predicted by **risk scores**,
- follows **typical fintech fraud patterns**,
- can be accurately detected using **simple business rules**,
- and can be enhanced with a **lightweight Machine Learning model**.

The dataset is intentionally “too perfect”, which is expected for a pedagogical dataset.  
Despite this, the methodology and conclusions reflect real **Fraud Analyst** thinking and workflows.

---

## 13. Repository Structure

- `fraud_detection_analysis.ipynb` → full end-to-end analysis (Python / pandas / scikit-learn)
- `README.md` → project description, findings, and business recommendations

Future improvements:

- Add requirements file
- Add scripts to train and evaluate the model
- Extend with more realistic, noisy data
