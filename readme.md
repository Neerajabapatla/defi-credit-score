# 📈 DeFi Credit Scoring Using Aave V2 Transactions

## 📘 Project Overview
This project assigns a credit score (0–1000) to DeFi wallets based on their historical transaction behavior using data from the Aave V2 protocol. A higher score indicates responsible and safe usage (e.g., repaying loans, regular deposits), while lower scores suggest risky, exploitative, or bot-like behavior (e.g., frequent liquidations, no repayments).


## 🚀 Objective
- Build a robust machine learning model that predicts a wallet's DeFi creditworthiness.
- Output a credit score for each wallet using features extracted from raw transaction logs.


## ⚖️ Method Chosen
To train a model without labeled creditworthiness data, we first constructed a **proxy credit score** based on logical behavioral indicators using transaction history (e.g., deposits, repayments, liquidations).

Key behavioral signals:
- Reliable users frequently repay borrowed amounts, deposit regularly, and avoid liquidations.
- Risky users tend to borrow heavily without repayment, get liquidated, or withdraw more than they contribute.

We trained a **Random Forest Regressor** to learn patterns from these behaviors and predict credit scores on a scale of 0–1000. This approach balances interpretability, robustness, and accuracy, while avoiding overfitting on noisy DeFi data.

This proxy score serves as the target for training a supervised machine learning model. We use a **Random Forest Regressor** due to its robustness, interpretability, and ability to handle non-linearities.


## 📇 Architecture & Processing Flow

### 1. **Data Ingestion**
- Load `user-wallet-transactions.json` (sample of 100K Aave V2 logs)
- Flatten nested fields using `json_normalize`

### 2. **Feature Engineering**
Aggregate wallet-level statistics:
- Number of deposits, borrows, repays
- Total deposit, borrow, repay, withdraw in USD
- Liquidations, repay-to-borrow ratio, net contribution

### 3. **Score Label Generation**
- Compute `proxy_score` using domain-driven logic (see `score_logic_explained.md`)
- Score is clipped to [0, 1000]

### 4. **Model Training**
- Train `RandomForestRegressor` using engineered features
- Validate using train/test split

### 5. **Scoring & Output**
- Predict credit scores for each wallet
- Export predictions to `wallet_scores.json`


## 📊 Output Format
```json
[
  {
    "userWallet": "0x00000000001accfa9cef68cf5371a23025b6d4b6",
    "credit_score": 500
  }
]
```


## 🔧 Tech Stack
- Python (pandas, numpy, scikit-learn)
- Jupyter Notebooks
- Matplotlib / Seaborn (for visualizations)


## 📅 File Structure
```
├── main.py                    # Main script to run scoring pipeline
├── user-wallet-transactions.json         # Input transaction dataset
├── wallet_scores.json        # Final scored wallets output
├── wallet_features_with_scores.csv  # Engineered dataset
├── README.md                 # Project summary and usage
├── score_logic_explained.md # Full scoring methodology
├── analysis.md               # Score distribution analysis
```


## ✅ Usage
```bash
pip install -r requirements.txt
python main.py --input user-wallet-transactions.json --output wallet_scores.json
```


## 💪 Extensibility
This framework can be adapted to:
- Other DeFi protocols (e.g., Compound, MakerDAO)
- Real label integration (if repayment defaults data becomes available)
- More complex models (e.g., XGBoost, ensemble methods)




