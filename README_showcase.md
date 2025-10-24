# 📊 Modular Trading Backtest Engine

A **fully modular, production-grade trading backtesting and visualization engine** built for flexibility, extensibility, and professional freelancing use cases.

It supports **multi-source market data**, **indicator-based signal generation**, and **portfolio-level performance tracking** — all with **interactive Plotly charts** and **auto-generated reports**.

Built for scalability — add new indicators, signals, or data sources without touching core logic.

## 🚀 Key Features

- 📦 **Modular Architecture** — Clean separation between data loading, signal generation, trade simulation, and visualization.  
- 🔍 **Multi-Source Data** — Fetch data from Yahoo Finance, Binance, or CSV files seamlessly.  
- 📈 **Indicator-Based Scoring** — Automatically computes only the indicators required by each strategy and applies rule-driven logic to evaluate RSI, MACD, SMA, EMA, Bollinger Bands, and Volume with configurable thresholds for entry and exit signals.  

- 💰 **Realistic Trade Simulation** — Executes candle-by-candle portfolio updates using configurable parameters for true-to-market behavior. Each run supports:  
  - ⚡ **Initial capital allocation** for portfolio base.  
  - ⚡ **Stop-loss**, **take-profit**, and **trailing-stop** controls for trade exits.  
  - ⚡ **Risk-per-trade** and **position-size** percentages for dynamic exposure management.  
  - ⚡ **Slippage** and **commission** adjustments for execution realism.  
  - ⚡ **Trade-on-close** toggle to control entry timing logic.  
  - ⚡ **Buy link generation** for traceable entry tracking.  
  - ⚡ **Annualized performance scaling** via `periods_per_year`.  
  Designed to simulate real-world trading conditions while remaining fully configurable for testing different capital and execution models.  

- 🧠 **Multi-Strategy Portfolios** — Combine multiple strategies into one portfolio equity curve.  
- 🧾 **Automatic Reporting** — Generates metrics summary, PDF report, and HTML chart outputs.  
- 🌐 **Timezone-Normalized** — Standardizes timestamps to **UTC** for Binance/Kaggle and to **America/New_York** for Yahoo data, ensuring consistent alignment across sources without persistent timezone metadata.
- 📊 **Interactive Plotly Charts** — Hover to explore OHLC, signals, entries, exits, and equity with dynamic tooltips and zooming. Supports **range breaks** to skip non-trading hours and **optional volume overlays** based on configuration for cleaner analysis. 
- 💡 **Multi-Layer Indicator Visualization** — Overlay multiple indicators (RSI, MACD, SMA, EMA, Bollinger Bands, Volume, etc.) on a single interactive chart with independent scaling and synchronized interactivity — a level of integration not natively available in TradingView.  
- 🪶 **Lightweight Yet Powerful** — Lightweight core designed for fast iteration and client-ready delivery.  
  


## 🗂️ Project Structure

```
project_root/
│
├── main.py # Runs backtest based on config.json
├── config.json # Central configuration file
│
├── modules/ # Core modules for data, indicators, signals, and trades
├── visualization/ # Plotly-based charting and reporting utilities
├── results/ # Auto-generated outputs (charts, reports, metrics)
│
└── sample_outputs/ # Contains example chart.html and sample_report.pdf for clients
```

### 🧩 Module Overview (Simplified)

| Module | Description |
|---------|-------------|
| **Engine Core** | Runs the full backtest cycle: configuration parsing, strategy execution, and portfolio aggregation. |
| **Data Module** | Handles multi-source OHLCV data loading (Yahoo, Binance, CSV) with buffering for realistic backtests. |
| **Indicators Module** | Computes all supported indicators (RSI, MACD, SMA, EMA, Bollinger Bands, Volume). |
| **Signals & Rules** | Applies indicator-based conditions to generate buy/sell decisions dynamically. |
| **Trade Simulator** | Executes trades candle-by-candle with stop-loss, take-profit, and capital allocation controls. |
| **Metrics & Reporting** | Computes performance analytics and generates structured outputs (CSV, HTML, PDF). |
| **Visualization** | Builds interactive Plotly charts combining price, signals, and equity progression. |

💼 Trading Direction — The engine currently supports long-only trading (buy → sell).
The modular design allows easy extension to short-side logic through inverse signal conditions and mirrored trade execution rules.

This modular structure supports **rapid extension** — new indicators, data sources, or rule types can be added without modifying existing components.


## 🧩 Installation & Environment Setup

You only need **Python 3.10+** to run this engine.

### 🐍 Quick Setup (via pip)

Open a terminal in the project folder and run:

```
pip install -r requirements.txt
```

This installs all required dependencies, including:
- pandas  
- numpy  
- plotly  
- ta  
- reportlab  
- python-binance  
- yfinance  

After installation, the engine is ready to execute from `main.py` using your chosen `config.json`.

⚙️ **Note:**  
If using Binance as a data source, ensure your `.env` file contains valid API keys.  
Standard libraries (os, json, math, datetime, etc.) are built into Python — no separate installation needed.



## ▶️ Running the Backtest

Once your environment is ready, running a backtest is straightforward.  
Everything is controlled through a single configuration file — `config.json`.

---

### ⚙️ Step 1 — Configure Your Backtest

Open **`config.json`** in the project root.  
Here’s an example structure:

#### ⚙️ Example Configuration

The engine is fully configurable via a single `config.json` file.  
Each layer (data, indicators, strategies, and simulation) can be adjusted without touching code.

⚠️ Note: The configuration below is a simplified showcase version. The full implementation includes extended parameters and modular controls used in client projects.
```
{
  "data": {
    "source": "binance",
    "symbol": "ZECUSDT",
    "timeframe": "30m",
    "start": "2025-10-01",
    "end": "2025-10-10",
    "buffer_candles": 1000,
    "force_refresh": true
  },

  "plotting": {
    "show_volume": true,
    "show_equity_curve": true,
    "rangebreaks": true
  },

  "indicators": {
    "sma_fast": 50,
    "sma_slow": 200,
    "ema_fast": 10,
    "ema_slow": 40,
    "rsi_period": 14,
    "macd_fast": 6,
    "macd_slow": 13,
    "macd_signal": 5,
    "bb_window": 10,
    "bb_std": 1.5
  },

  "signals": {
    "rsi_buy_level": 30,
    "rsi_sell_level": 70
  },

  "strategies": [
    {
      "name": "Bollinger Bands",
      "entry_conditions": ["bb_signal"],
      "exit_conditions": ["bb_sell_signal"],
      "capital_fraction": 0.34
    },
    {
      "name": "EMA_Strategy",
      "entry_conditions": ["ema_signal"],
      "exit_conditions": ["ema_sell_signal"],
      "capital_fraction": 0.33
    },
    {
      "name": "RSI_BB_MACD_Combo_SMA Combo",
      "entry_conditions": ["rsi_signal", "bb_signal", "macd_signal", "sma_signal"],
      "exit_conditions": ["rsi_sell_signal"],
      "capital_fraction": 0.33
    }
  ],

  "trade_simulation": {
    "initial_capital": 10000,
    "slippage_pct": 0.00,
    "commission_pct": 0.00,
    "trade_on_close": false,
    "enable_buy_link": true
  }
}
```
💡 Tip:
Each strategy runs independently with its own capital fraction — you can plug in new indicators or strategies simply by adding new entries here.

### 🧠 Step 2 — Run the Main Script
Once configured, execute the main file from the terminal:
```
python main.py
```
The engine will:

Load market data (from Binance, Yahoo, or CSV).

Apply all selected indicators and generate entry/exit signals.

Simulate trades based on the provided risk parameters.

Save full reports, charts, and performance metrics in the /results folder.

### 📈 Step 3 — View Interactive Results

Once the run completes, all outputs are automatically organized in the `/results` directory.

Each **strategy** gets its own dedicated folder containing all related reports and charts — making it easy to compare multiple strategies side by side.

---

#### 📂 Example Output Tree

Each strategy automatically generates its own result folder with visualizations, trade logs, metrics, and reports — plus a consolidated portfolio summary.

```bash
results/
├── Bollinger_Bands/
│   ├── chart_Bollinger_Bands.html
│   ├── trades_Bollinger_Bands.csv
│   ├── metrics_Bollinger_Bands.json
│   ├── report_Bollinger_Bands.pdf
│   └── equity_Bollinger_Bands.csv
│
├── RSI_Strategy/
│   ├── chart_RSI_Strategy.html
│   ├── trades_RSI_Strategy.csv
│   ├── metrics_RSI_Strategy.json
│   ├── report_RSI_Strategy.pdf
│   └── equity_RSI_Strategy.csv
│
├── portfolio_equity.csv
├── portfolio_metrics.json
├── portfolio_equity_chart.html
└── portfolio_summary_report.pdf
```

## 🪄 Deliverables

| 📁 File | 💡 Description |
|----------|----------------|
| `chart_*.html` | 📊 Interactive candlestick chart with optional volume, equity overlay, and range breaks |
| `trades_*.csv` | 📈 Trade log including PnL, entry/exit prices, and timestamps |
| `metrics_*.json` | 📉 KPIs such as Sharpe ratio, drawdown, and win rate |
| `report_*.pdf` | 🧾 Professional report for sharing and client review |
| `portfolio_*` | 💼 Aggregated multi-strategy results summary and equity chart|

💼 *All outputs are lightweight and client-presentable.*  
Each file demonstrates part of a complete analytics pipeline — from signal generation to visualization and reporting.

💡 Showcase Highlights

Each chart supports zoom, hover, and range breaks

Volume visibility toggled via config

Built-in data caching for faster re-runs

All deliverables auto-organized by strategy

🕒 Timezone Handling — Standardized timestamp normalization:

Binance/Kaggle: Processed entirely in UTC (no persistent timezone metadata).

Yahoo Finance: Converted to America/New_York during import (also stripped of timezone awareness afterward).
Ensures consistent alignment across all sources without retaining timezone objects.

🎯 Ideal Use Cases

Freelancers offering strategy backtesting services

Traders testing indicator-based systems

Analysts preparing client-ready performance reports

### 🖼️ Sample Chart Preview

Here’s an example snapshot of the interactive candlestick chart, metrics, trade log, and report generated by the engine.  

> 💡 **Note**  
> The sample chart demonstrates the engine’s visualization and signal precision capabilities.  
> This example uses a simplified trade simulation (no stop-loss or risk controls) to emphasize chart clarity and multi-indicator integration.  
> All sample outputs can be customized based on client requirements.  
> Full trade management logic is available in full builds.

> 🧩 **Consistency Note**  
> All sample outputs (chart, trades CSV, metrics JSON, and PDF report) come from the **same backtest run**, enabling full cross-verification of signals, trade entries/exits, and PnL accuracy.

🔗 **[View Interactive Sample Chart](trading_engine_showcase/sample_chart.html)**


### ✅ Summary

This engine provides:

Modular, scalable design

Indicator-driven trade logic

Interactive visualization and analytics

Ready-to-deliver client reports

All built for practical, professional-grade freelancing work.
