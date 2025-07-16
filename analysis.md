# 📊 Score Distribution and Behavioral Analysis

## 🎯 Purpose

This analysis provides insights into the distribution of credit scores assigned to DeFi wallets using transaction-level behavior and highlights behavioral traits common to low-scoring and high-scoring wallets.

---

## 📉 Score Distribution Overview

The credit scores range from **0 to 1000**. To understand how wallets are distributed across this range, we divide them into score bands:

- 0–100
- 101–200
- 201–300
- 301–400
- 401–500
- 501–600
- 601–700
- 701–800
- 801–900
- 901–1000

A histogram was plotted using these bins to visualize the number of wallets in each band.

### ✅ Observations:

- Most wallets fall within the **400–800** range, suggesting moderate to good behavior.
- Wallets in the **0–200** range exhibit risky or exploitative behavior.
- Few wallets achieve scores above **900**, indicating exemplary usage.

---

## 🔍 Behavioral Differences: Low vs High Score Wallets

To compare behaviors, we analyze key features between wallets with scores:

- ⬆️ **High Score (≥ 800)**
- ⬇️ **Low Score (≤ 200)**

### 🔢 Feature Comparison Table

| Feature                  | Low Score | High Score |
| ------------------------ | --------- | ---------- |
| num\_deposits            | 34.75     | 36.07      |
| num\_borrows             | 23.68     | 16.76      |
| num\_repays              | 20.20     | 12.81      |
| num\_liquidations        | 0.37      | 0.12       |
| repay\_to\_borrow\_ratio | 0.58      | 0.55       |
| total\_deposit\_usd      | 1.34M     | 460K       |
| total\_borrow\_usd       | 884K      | 247K       |
| total\_repay\_usd        | 576K      | 173K       |
| net\_contribution\_usd   | -161K     | +123K      |

### 🔴 Insights:

- Low-score wallets **borrow more** and **get liquidated more frequently**.
- Despite high deposits, they contribute negatively to the protocol.
- High-score wallets exhibit **more conservative borrowing**, **fewer liquidations**, and a **positive net contribution**.

---

## 📊 Visualization Code for Distribution Plot

```python
import seaborn as sns
import matplotlib.pyplot as plt

plt.figure(figsize=(10,6))
sns.histplot(features['credit_score'], bins=10, kde=False)
plt.title('Distribution of Credit Scores')
plt.xlabel('Credit Score (0–1000)')
plt.ylabel('Number of Wallets')
plt.grid(True)
plt.show()
```

---

## 📆 Visualization Code for Behavioral Comparison (Log Scale)

```python
avg_features.replace(0, 0.01, inplace=True)
avg_features[avg_features < 0] = 0.01

avg_features.plot(kind='bar', figsize=(12, 6), logy=True)
plt.title("Behavioral Feature Comparison (Log Scale): Low vs High Score Wallets")
plt.ylabel("Log Scale: Average Value")
plt.xticks(rotation=45)
plt.grid(True, which='both', axis='y', linestyle='--', linewidth=0.5)
plt.tight_layout()
plt.show()
```

---

## 📁 Related Files

- `wallet_features_with_scores.csv` : Feature vectors with computed credit scores
- `wallet_scores.json` : Final wallet credit scores
- `score_distribution.png` : Histogram of score ranges
- `behavioral_comparison.png` : Log-scaled behavioral feature bar chart

