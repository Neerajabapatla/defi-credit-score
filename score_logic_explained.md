# 🧠 Score Logic Explained

## Why a Proxy Score?

In the absence of true labeled credit scores in DeFi, we define a **proxy score** based on measurable transaction behavior from Aave V2. This score mimics creditworthiness by rewarding responsible usage and penalizing risky or exploitative activity.

---

## 📊 Features Used in Scoring

| Feature                 | Description                                         |
| ----------------------- | --------------------------------------------------- |
| `total_deposit_usd`     | Total USD value deposited by the user               |
| `total_borrow_usd`      | Total USD borrowed                                  |
| `total_repay_usd`       | Total USD repaid                                    |
| `num_deposits`          | Count of deposit actions                            |
| `num_borrows`           | Count of borrow actions                             |
| `num_repays`            | Count of repay actions                              |
| `total_withdraw_usd`    | USD value withdrawn                                 |
| `num_liquidations`      | Times user was liquidated (a strong risk indicator) |
| `repay_to_borrow_ratio` | Ratio of repaid to borrowed amount                  |
| `net_contribution_usd`  | Deposits + Repays - Borrows - Withdraws             |

---

## ⚖️ Proxy Score Formula

We calculate a score between 0 and 1000 using the following logic:

```python
def proxy_score(row):
    score = 500  # Start from neutral base

    # Positive Contributions
    # Reward deposits (up to $10K)
    score += min(row['total_deposit_usd'], 10_000) * 0.04

    # Reward high repayment discipline
    score += min(row['repay_to_borrow_ratio'], 2.0) * 80

    # Reward net contribution (up to $5K)
    score += min(row['net_contribution_usd'], 5_000) * 0.05

    # Bonus for activity: frequent borrows and repays (but not too many)
    score += min(row['num_borrows'], 10) * 2
    score += min(row['num_repays'], 10) * 2

    # Penalties
    # Penalize high total borrow if not well repaid
    if row['repay_to_borrow_ratio'] < 0.5 and row['total_borrow_usd'] > 100:
        score -= 50

    # Penalize liquidations heavily
    score -= row['num_liquidations'] * 100

    # Penalize negative contribution
    if row['net_contribution_usd'] < 0:
        score += row['net_contribution_usd'] * 0.1  # subtracts

    # Final Bounds
    return int(np.clip(score, 0, 1000))
```

---

## 🧰 Why This Makes Sense

- **Higher deposits** show protocol trust
- **Consistent repayment** shows financial discipline
- **Liquidations** signal poor risk management
- **Net contribution** ensures users are not draining the protocol

This proxy mimics how lenders assess trustworthiness in traditional finance.

---

## 🎯 How It’s Used

- The `proxy_score` is computed for each wallet as a training label
- We train a `RandomForestRegressor` on the wallet-level features
- The model then generalizes this logic to score **new, unseen wallets**

This approach ensures scalability, transparency, and a clear behavioral foundation for credit risk modeling in DeFi.

