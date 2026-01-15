---
name: backtesting-trading-strategies
description: |
  Backtest crypto and traditional trading strategies against historical data.
  Calculates performance metrics (Sharpe, Sortino, max drawdown), generates equity curves,
  and optimizes strategy parameters. Use when user wants to test a trading strategy,
  validate signals, or compare approaches. Trigger: "backtest", "test strategy",
  "historical performance", "simulate trades".
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
version: 2.0.0
author: Jeremy Longshore <jeremy@intentsolutions.io>
license: MIT
---

# Backtesting Trading Strategies

Validate trading strategies against historical data before risking real capital.

## Quick Start

```bash
# Install dependencies (one-time)
pip install pandas numpy yfinance ta-lib matplotlib

# Run a backtest
python {baseDir}/scripts/backtest.py --strategy sma_crossover --symbol BTC-USD --period 1y
```

## Supported Strategies

| Strategy | Description | Key Parameters |
|----------|-------------|----------------|
| `sma_crossover` | Simple moving average crossover | `fast_period`, `slow_period` |
| `ema_crossover` | Exponential MA crossover | `fast_period`, `slow_period` |
| `rsi_reversal` | RSI overbought/oversold | `period`, `overbought`, `oversold` |
| `macd` | MACD signal line crossover | `fast`, `slow`, `signal` |
| `bollinger_bands` | Mean reversion on bands | `period`, `std_dev` |
| `breakout` | Price breakout from range | `lookback`, `threshold` |
| `mean_reversion` | Return to moving average | `period`, `z_threshold` |
| `momentum` | Rate of change momentum | `period`, `threshold` |

## Workflow

### Step 1: Fetch Historical Data

```bash
python {baseDir}/scripts/fetch_data.py --symbol BTC-USD --period 2y --interval 1d
```

Saves to `{baseDir}/data/{symbol}_{interval}.csv`

### Step 2: Run Backtest

```bash
python {baseDir}/scripts/backtest.py \
  --strategy sma_crossover \
  --symbol BTC-USD \
  --start 2023-01-01 \
  --end 2024-01-01 \
  --capital 10000 \
  --params '{"fast_period": 20, "slow_period": 50}'
```

### Step 3: Analyze Results

Output includes:
- **Performance Metrics**: Total return, CAGR, Sharpe ratio, Sortino ratio
- **Risk Metrics**: Max drawdown, VaR, CVaR, volatility
- **Trade Stats**: Win rate, profit factor, avg win/loss, max consecutive losses
- **Equity Curve**: Visual chart saved to `{baseDir}/reports/`

### Step 4: Optimize Parameters

```bash
python {baseDir}/scripts/optimize.py \
  --strategy sma_crossover \
  --symbol BTC-USD \
  --param-grid '{"fast_period": [10,20,30], "slow_period": [50,100,200]}'
```

## Output Metrics

### Performance
| Metric | Description |
|--------|-------------|
| Total Return | Overall percentage gain/loss |
| CAGR | Compound annual growth rate |
| Sharpe Ratio | Risk-adjusted return (target: >1.5) |
| Sortino Ratio | Downside risk-adjusted return |
| Calmar Ratio | Return / max drawdown |

### Risk
| Metric | Description |
|--------|-------------|
| Max Drawdown | Largest peak-to-trough decline |
| VaR (95%) | Value at Risk at 95% confidence |
| CVaR (95%) | Expected loss beyond VaR |
| Volatility | Annualized standard deviation |
| Ulcer Index | Duration-weighted drawdown |

### Trade Statistics
| Metric | Description |
|--------|-------------|
| Total Trades | Number of round-trip trades |
| Win Rate | Percentage of profitable trades |
| Profit Factor | Gross profit / gross loss |
| Avg Win | Average winning trade |
| Avg Loss | Average losing trade |
| Expectancy | Expected value per trade |

## Configuration

Create `{baseDir}/config/settings.yaml`:

```yaml
data:
  provider: yfinance  # or: coingecko, binance, alpaca
  cache_dir: ./data

backtest:
  default_capital: 10000
  commission: 0.001  # 0.1% per trade
  slippage: 0.0005   # 0.05% slippage

risk:
  max_position_size: 0.25  # 25% of capital per trade
  stop_loss: 0.02          # 2% stop loss
  take_profit: 0.06        # 6% take profit (3:1 R/R)

reporting:
  output_dir: ./reports
  save_trades: true
  save_equity_curve: true
```

## Example Output

```
╔══════════════════════════════════════════════════════════════════════╗
║                    BACKTEST RESULTS: SMA CROSSOVER                   ║
║                    BTC-USD | 2023-01-01 to 2024-01-01                ║
╠══════════════════════════════════════════════════════════════════════╣
║ PERFORMANCE                          │ RISK                          ║
║ ─────────────────────────────────────┼─────────────────────────────  ║
║ Total Return:        +47.32%         │ Max Drawdown:      -18.45%    ║
║ CAGR:                +47.32%         │ VaR (95%):         -2.34%     ║
║ Sharpe Ratio:        1.87            │ Volatility:        42.1%      ║
║ Sortino Ratio:       2.41            │ Ulcer Index:       8.2        ║
╠══════════════════════════════════════════════════════════════════════╣
║ TRADE STATISTICS                                                     ║
║ ─────────────────────────────────────────────────────────────────────║
║ Total Trades:        24              │ Profit Factor:     2.34       ║
║ Win Rate:            58.3%           │ Expectancy:        $197.17    ║
║ Avg Win:             $892.45         │ Max Consec. Losses: 3         ║
║ Avg Loss:            -$381.22        │ Avg Trade Duration: 8.2 days  ║
╠══════════════════════════════════════════════════════════════════════╣
║ PARAMETERS: fast_period=20, slow_period=50                           ║
╚══════════════════════════════════════════════════════════════════════╝
```

## Files

| File | Purpose |
|------|---------|
| `scripts/backtest.py` | Main backtesting engine |
| `scripts/fetch_data.py` | Historical data fetcher |
| `scripts/strategies.py` | Strategy definitions |
| `scripts/metrics.py` | Performance/risk calculations |
| `scripts/optimize.py` | Parameter optimization |
| `config/settings.yaml` | Configuration |
| `data/` | Cached price data |
| `reports/` | Backtest results and charts |

## Error Handling

See `{baseDir}/references/errors.md` for common issues and solutions.

## Examples

See `{baseDir}/references/examples.md` for detailed usage examples.

## Resources

- [yfinance](https://github.com/ranaroussi/yfinance) - Yahoo Finance data
- [TA-Lib](https://ta-lib.org/) - Technical analysis library
- [Backtrader](https://www.backtrader.com/) - Alternative backtesting framework
- [QuantStats](https://github.com/ranaroussi/quantstats) - Portfolio analytics
