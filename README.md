# Crypto Trader Sentiment Analysis

An analysis of cryptocurrency trading behavior in relation to the Bitcoin Fear & Greed Index, examining how market sentiment affects trader performance and decision-making.

---

## Overview

This project investigates whether market sentiment — as measured by the **Fear & Greed Index** — has a meaningful impact on crypto trader performance and behavioral patterns. By merging historical trade data with daily sentiment classifications, the analysis answers two core questions:

1. **Does trading performance differ across Fear vs. Greed market conditions?**
2. **Do traders actually change their behavior based on sentiment?**

---

## Datasets

| Dataset | Shape | Description |
|---|---|---|
| `fear_greed_index.csv` | 2,644 rows × 4 cols | Daily Fear & Greed Index scores and sentiment classifications |
| `historical_data.csv` | 211,224 rows × 16 cols | Historical trade records including PnL, trade size, direction, fees, and more |

Both datasets contained **no missing values** and **no duplicate records**.

### Dataset Columns (After Cleaning)

**Fear & Greed Index:**
- `value` — Numeric index score
- `classification` — Sentiment label: *Extreme Fear*, *Fear*, *Neutral*, *Greed*, *Extreme Greed*
- `date` — Derived from Unix timestamp

**Historical Trade Data (key columns retained):**
- `Account` — Trader identifier
- `date` — Normalized from `Timestamp IST`
- `Closed PnL` — Realized profit/loss per trade
- `Size USD` — Trade size in USD
- `Side` — BUY or SELL
- `Direction` — Long or Short
- `Fee` — Transaction fee

---

## Project Structure

```
.
├── Analysis.ipynb          # Main analysis notebook
└── Datasets/
    ├── fear_greed_index.csv
    └── historical_data.csv
```

---

## Dependencies

```
pandas
matplotlib
seaborn
```

Install dependencies:

```bash
pip install pandas matplotlib seaborn
```

---

## Methodology

### 1. Data Preparation

- **Fear & Greed Index:** The original `date` column was dropped in favor of a more reliable date derived from the Unix `timestamp` column.
- **Historical Data:** Unnecessary columns (`Transaction Hash`, `Order ID`, `Crossed`, `Trade ID`, `Timestamp`) were removed. The `Timestamp IST` column was normalized to date-only precision and renamed to `date`.
- **Merging:** Both datasets were joined on `date` using a **left join** on the historical data, preserving all trade records while appending the corresponding daily sentiment classification.

### 2. Key Metrics Created

| Metric | Description |
|---|---|
| `daily_pnl` | Total PnL per trader per day |
| `win_rate` | Percentage of winning trades per trader |
| `overall_win_rate` | Aggregate win rate across all traders |
| `avg_trade_size` | Average trade size (USD) per trader |
| `trades_per_day` | Total number of trades per day |
| `overall_long_short_ratio` | Ratio of BUY to SELL orders across all trades |

---

## Analysis & Key Findings

### Question 1: Does Performance Differ Between Fear vs. Greed Days?

Performance was aggregated by sentiment classification across **Total PnL**, **Average PnL**, **Drawdown Proxy** (minimum single-trade PnL), and **Win Rate**:

| Sentiment | Total PnL | Average PnL | Drawdown Proxy | Win Rate |
|---|---|---|---|---|
| Extreme Fear | $739,110 | $34.54 | -$31,037 | 37% |
| Fear | $3,357,155 | $54.29 | -$35,682 | 42% |
| Neutral | $1,292,921 | $34.31 | -$24,500 | 40% |
| Greed | $2,150,129 | $42.74 | -$117,990 | 38% |
| Extreme Greed | $2,715,171 | $67.89 | -$10,259 | 46% |
---

![Trade Performance](/Visuals/Performance.png)


**Key Insights:**

- **Extreme Greed** produces the highest Win Rate (46%) and Average PnL ($67.89), suggesting strong market momentum creates a tailwinds effect where trades are both easier to win and more profitable.
- **Greed** is the most dangerous phase — the Drawdown Proxy spikes to -$117,990, far exceeding any other sentiment period. Traders likely over-leverage or make outsized bets just before the market peaks.
- **Neutral** markets yield the lowest Average PnL ($34.31), indicating that directionless, choppy markets are harder to extract value from than even fearful, volatile ones.
- **Fear** outperforms Greed on Average PnL ($54.29 vs. $42.74) despite a lower win rate. Successful trades in fearful markets tend to be larger — likely due to high-volatility "bounce" plays.

---

### Question 2: Do Traders Change Behavior Based on Sentiment?

Behavioral metrics were computed per sentiment classification: trade count, average trade size, long/short bias, and average fee paid.

---
![](/Visuals/Behavior.png)
**Key Insights:**

- **Contrarian Long Bias in Fear:** Traders show their highest Long Bias (61.95%) during *Extreme Fear* — buying the dip while the market broadly sells off.
- **Larger Bets When Fearful:** Average position size peaks around $184K during *Extreme Fear* vs. ~$156K during *Extreme Greed*. This "doubling down" in falling markets explains the severe drawdowns observed in the performance analysis.
- **Panic Trading Costs More:** Average fees are highest during *Fear* ($1.50) and lowest during *Extreme Greed* ($0.68), suggesting traders use expensive market orders during fearful periods to enter/exit quickly.
- **Peak Activity During Transitions:** Trading volume is highest during *Fear* (618 trades) rather than the absolute extremes, suggesting traders are most hyperactive when anticipating a directional shift.

---

## Running the Analysis

1. Clone the repository and ensure the `Datasets/` folder contains both CSV files.
2. Open `Analysis.ipynb` in Jupyter Notebook or JupyterLab.
3. Run all cells in order (Kernel → Restart & Run All).

---

## Visualizations

The notebook generates the following charts:

- **Average PnL by Sentiment** — Bar chart comparing per-trade profitability across sentiment categories
- **Win Rate by Sentiment** — Bar chart showing success rate across market conditions
- **Behavioral Shift in Trade Bias** — Bar chart of Long Trade Bias (%) by sentiment, with a 50% neutral reference line
- **Average Trade Size (USD) by Sentiment** — Horizontal bar chart of position sizing by market mood