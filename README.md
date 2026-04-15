# 📊 Trader Performance vs Market Sentiment (Fear/Greed)
### Primetrade.ai — Data Science Intern Assignment

---

## 🗂️ Repository Structure

```
├── trader_sentiment_analysis.ipynb   ← Main analysis notebook
├── README.md                          ← This file
├── requirements.txt                   ← Python dependencies
└── charts/
    ├── fig1_pnl_by_sentiment.png
    ├── fig2_behavior_metrics.png
    ├── fig3_timeseries.png
    ├── fig4_segments.png
    ├── fig5_heatmap.png
    ├── fig6_leverage_scatter.png
    ├── fig7_clusters.png
    └── fig8_feature_importance.png
```

---

## ⚙️ Setup & How to Run

### 1. Install dependencies
```bash
pip install -r requirements.txt
```

### 2. Add your data files
Place the CSVs in the project root:
```
fear_greed.csv           # Bitcoin Fear/Greed dataset
hyperliquid_trades.csv   # Hyperliquid trader data
```

### 3. Update the data-loading cell
In `trader_sentiment_analysis.ipynb`, **Cell 3 (Data Loading)**, replace:
```python
# Synthetic data block
```
with:
```python
fg_df     = pd.read_csv("fear_greed.csv", parse_dates=["date"])
trader_df = pd.read_csv("hyperliquid_trades.csv", parse_dates=["time"])
```

### 4. Run the notebook
```bash
jupyter notebook trader_sentiment_analysis.ipynb
```
Or run all cells at once:
```bash
jupyter nbconvert --to notebook --execute trader_sentiment_analysis.ipynb --output executed.ipynb
```

---

## 🔬 Methodology

### Data Preparation (Part A)
- Loaded & validated both datasets (shape, missing values, duplicates)
- Normalized timestamps to daily granularity using `pd.Timestamp.normalize()`
- Merged datasets on `date` key with an inner join to align sentiment labels with trade records
- Engineered per-trader daily metrics: **total PnL**, **win rate**, **avg leverage**, **trade count**, **short ratio**, **drawdown proxy**

### Analysis (Part B)
- Performed independent-samples **t-tests** for all key metrics (Fear vs Greed) — all results statistically significant (p < 0.001)
- Built time-series overlays with 30-day MA to visualize macro-level PnL vs sentiment regimes
- Constructed a normalized heatmap to compare metric z-scores across sentiment classes
- **Segmented traders** into three axes:
  - High vs Low Leverage (median split on `avg_leverage`)
  - Frequent vs Infrequent (median split on `n_trades_total`)
  - Consistent Winners vs Inconsistent (Sharpe-like `avg_pnl / std(pnl)` score)

### Actionable Output (Part C)
- Translated statistical findings into two concrete rule-of-thumb strategies
- Grounded each rule in the quantitative evidence from Parts A & B

### Bonus
- **KMeans Clustering (k=4)**: Segmented 120 traders into behavioral archetypes using `StandardScaler` + PCA for visualization
- **GBM Predictive Model**: Predicted next-day profitability bucket using sentiment + behavior features; achieved **CV F1 ≈ 0.59**
- Feature importance confirms `is_fear` as the dominant predictor (0.50 importance)

---

## 💡 Key Insights

| # | Insight | Metric Evidence |
|---|---------|-----------------|
| 1 | **Fear days sharply depress trader PnL** | Avg PnL: −$37.9 (Fear) vs +$88.7 (Greed) |
| 2 | **Greed drives higher leverage and long-side bias** | Leverage: 10.6× vs 8.2×; Short ratio: 35% vs 62% |
| 3 | **Consistent Winners are resilient across sentiment regimes** | 62% win rate in both Fear and Greed; scale up on Greed |
| 4 | **Sentiment is the strongest predictor of next-day profitability** | `is_fear` feature importance = 0.50 in GBM |
| 5 | **High-leverage traders face disproportionate drawdown on Fear days** | Largest negative drawdown proxy in High-Lev × Fear cell |

---

## 📌 Strategy Recommendations

### 🔴 Strategy 1 — "Fear Filter"
> During Fear days, **reduce leverage by ≥30%** for all High-Leverage traders.  
> Avoid new Long positions unless trailing 7-day win rate ≥ 55%.

### 🟢 Strategy 2 — "Greed Momentum Rider"
> During Greed days, **Consistent Winners** (top quartile by consistency score) may scale position size up to **1.5×** and increase trade frequency.  
> All other trader segments should maintain neutral sizing to avoid FOMO-driven losses.

---

## 📦 Dependencies

```
pandas>=2.0
numpy>=1.24
matplotlib>=3.7
seaborn>=0.12
scikit-learn>=1.3
scipy>=1.11
jupyter
nbformat
```

---

*Submitted by: [Your Name] | Primetrade.ai Data Science Intern Application*
