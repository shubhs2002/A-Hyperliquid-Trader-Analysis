# A-Hyperliquid-Trader-Analysis

## Setup & Run
```bash
pip install pandas numpy matplotlib scikit-learn
python analysis.py
```
Outputs: `sentiment_analysis.png` (6-chart figure)

---

## Datasets
| Dataset | Rows | Columns |
|---|---|---|
| Trade Data | 211,224 | 16 |
| Fear/Greed Index | 2,644 | 4 |
| **Merged (by date)** | **184,263** | — |

- Date range: 2023-03-28 → 2025-02-19
- No missing values; no duplicates in either dataset
- Timestamps converted from Unix ms; aligned at daily granularity

---

## Methodology
1. Normalize the Fear/Greed labels (Extreme Fear → Fear, Extreme Greed → Greed)
2. Merge both datasets on `date`
3. Aggregate to **daily level**: PnL, win rate, trade count, leverage, long ratio
4. Compare Fear vs Greed days across all metrics
5. Segment traders (32 unique accounts) into: High/Low Leverage, Frequent/Infrequent, Consistent Winners (win_rate ≥ 55%)
6. Train a Random Forest to predict next-day profitability bucket

---

## Key Findings

### Insight 1 — Fear days are paradoxically more profitable in raw PnL
- Avg daily PnL: **Fear = $6.7M**, Greed = $841K, Neutral = $159K
- However, this is driven by a **very small number of high-activity Fear days** with massive volume (avg 133K trades vs 10K on Greed days)
- Win rate is **highest on Fear days (41.5%)** vs Greed (30.4%), suggesting selective, higher-conviction trading

### Insight 2 — Greed days show stronger long bias
- Long ratio: **Greed = 59.4%** vs Fear = 49.4%
- Traders systematically tilt bullish when sentiment is greedy — classic momentum chasing

### Insight 3 — Leverage is higher during Fear
- Avg leverage: **Fear = 70,923** vs Greed = 51,360
- Counter-intuitive: sophisticated traders on Hyperliquid may increase leverage on dips, using Fear days as entry points

---

## Strategy Recommendations

**Rule 1 — Leverage-aware sentiment filter (High-Leverage Segment)**
> "During Fear periods, reduce max leverage to 50–60% of your Greed-day level. Fear days generate the most volume but also the widest dispersion of outcomes. High-leverage traders face liquidation risk even when aggregate PnL looks positive."

**Rule 2 — Long-bias discipline for Greed phases (Frequent Traders)**
> "When the Fear/Greed index reads Greed (≥ 60), cap long exposure at 55% of your book. Our data shows traders over-tilt to longs during Greed with a 59.4% long ratio — but win rate drops to 30%, meaning over half of those directional bets lose. Consistent Winners maintain near-50% long/short balance regardless of sentiment."

---

## Bonus Model
- **Model**: Random Forest (100 trees) predicting whether next day is profitable
- **Top features**: long_ratio (0.28), win_rate (0.27), n_trades (0.24), avg_size_usd (0.19)
- Sentiment value alone contributes only 0.03 importance — behavior metrics dominate
- **Takeaway**: Sentiment is a weak direct predictor; trader behavior patterns matter more

