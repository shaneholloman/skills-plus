---
name: generating-trading-signals
description: |
  Generate trading signals using technical indicators (RSI, MACD, Bollinger Bands, etc.).
  Combines multiple indicators into composite signals with confidence scores.
  Use when asked to "get trading signals", "check indicators", "analyze for entry/exit".
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
version: 2.0.0
author: Jeremy Longshore <jeremy@intentsolutions.io>
license: MIT
---

# Generating Trading Signals

## Overview

Multi-indicator signal generation system that analyzes price action using 7 technical indicators and produces composite BUY/SELL signals with confidence scores and risk management levels.

**Indicators Used:**
- RSI (Relative Strength Index) - Overbought/oversold
- MACD (Moving Average Convergence Divergence) - Trend and momentum
- Bollinger Bands - Mean reversion and volatility
- Trend (SMA 20/50/200 crossovers) - Trend direction
- Volume - Confirmation of moves
- Stochastic Oscillator - Short-term momentum
- ADX (Average Directional Index) - Trend strength

## Prerequisites

```bash
pip install yfinance pandas numpy
```

## Quick Start

```bash
cd {baseDir}/scripts

# Scan top 10 crypto for signals
python scanner.py --watchlist crypto_top10

# Scan specific symbols
python scanner.py --symbols BTC-USD,ETH-USD,SOL-USD

# Filter for buy signals only with 60%+ confidence
python scanner.py --symbols BTC-USD,ETH-USD --filter buy --min-confidence 60

# Detailed breakdown of each signal
python scanner.py --symbols BTC-USD --detail
```

## Instructions

### 1. Quick Signal Scan

For a quick overview of multiple assets:

```bash
python {baseDir}/scripts/scanner.py --watchlist crypto_top10 --period 6m
```

Output shows signal type (STRONG_BUY/BUY/NEUTRAL/SELL/STRONG_SELL) and confidence for each asset.

### 2. Detailed Signal Analysis

For full indicator breakdown:

```bash
python {baseDir}/scripts/scanner.py --symbols BTC-USD --detail
```

Shows each indicator's contribution:
- Individual signal (BUY/SELL/NEUTRAL)
- Indicator value
- Reasoning (e.g., "RSI oversold at 28.5")

### 3. Filter and Rank Signals

Find the best opportunities:

```bash
# Only buy signals with 70%+ confidence
python scanner.py --filter buy --min-confidence 70 --rank confidence

# Rank by most bullish
python scanner.py --rank bullish

# Save results to JSON
python scanner.py --output signals.json
```

### 4. Custom Watchlists

Available watchlists:
- `crypto_top10` - Top 10 crypto by market cap
- `crypto_defi` - DeFi tokens
- `crypto_layer2` - Layer 2 solutions
- `stocks_tech` - Tech stocks
- `etfs_major` - Major ETFs

```bash
python scanner.py --list-watchlists
python scanner.py --watchlist crypto_defi
```

## Output

### Signal Summary Table

```
================================================================================
  SIGNAL SCANNER RESULTS
================================================================================

  Symbol       Signal         Confidence          Price    Stop Loss
--------------------------------------------------------------------------------
  BTC-USD      STRONG_BUY          78.5%     $67,234.00  $64,890.00
  ETH-USD      BUY                 62.3%      $3,456.00   $3,312.00
  SOL-USD      NEUTRAL             45.0%        $142.50         N/A
--------------------------------------------------------------------------------

  Summary: 2 Buy | 1 Neutral | 0 Sell
  Scanned: 3 assets | 2024-01-15 14:30
================================================================================
```

### Detailed Signal Output

```
======================================================================
  [EMOJI] BTC-USD - STRONG_BUY
  Confidence: 78.5% | Price: $67,234.00
======================================================================

  Risk Management:
    Stop Loss:   $64,890.00
    Take Profit: $71,922.00
    Risk/Reward: 1:2.0

  Signal Components:
----------------------------------------------------------------------
    RSI              | STRONG_BUY   | Oversold at 28.5 (< 30)
    MACD             | BUY          | MACD above signal, positive momentum
    Bollinger Bands  | BUY          | Price near lower band (%B = 0.15)
    Trend            | BUY          | Uptrend: price above key moving averages
    Volume           | STRONG_BUY   | High volume (2.3x) on up move
    Stochastic       | STRONG_BUY   | Oversold (%K=18.2, %D=21.5)
    ADX              | BUY          | Strong uptrend (ADX=32.1, +DI=28.5)
----------------------------------------------------------------------
```

## Signal Types

| Signal | Score | Meaning |
|--------|-------|---------|
| STRONG_BUY | +2 | Multiple strong buy signals aligned |
| BUY | +1 | Moderate buy signals |
| NEUTRAL | 0 | No clear direction |
| SELL | -1 | Moderate sell signals |
| STRONG_SELL | -2 | Multiple strong sell signals aligned |

## Confidence Score

Confidence (0-100%) based on:
- **Agreement**: How well indicators align (higher when all agree)
- **Strength**: How extreme the readings are

| Confidence | Interpretation |
|------------|----------------|
| 70-100% | High conviction, strong signal |
| 50-70% | Moderate conviction |
| 30-50% | Weak signal, mixed indicators |
| 0-30% | No clear direction, avoid trading |

## Configuration

Edit `{baseDir}/config/settings.yaml`:

```yaml
indicators:
  rsi:
    period: 14
    overbought: 70
    oversold: 30

signals:
  weights:
    rsi: 1.0       # Increase to emphasize RSI
    macd: 1.0
    bollinger: 1.0
    trend: 1.0
    volume: 0.5    # Lower weight for volume
```

## Error Handling

See `{baseDir}/references/errors.md` for:
- API rate limits
- Insufficient data handling
- Network errors

## Examples

See `{baseDir}/references/examples.md` for:
- Multi-timeframe analysis
- Custom indicator parameters
- Combining with backtester
- Automated scanning schedules

## Integration with Backtester

Test signals historically:

```bash
# Generate signal
python scanner.py --symbols BTC-USD --detail

# Backtest the strategy that generated the signal
cd ../../../trading-strategy-backtester/skills/backtesting-trading-strategies/scripts
python backtest.py --strategy rsi_reversal --symbol BTC-USD --period 1y
```

## Resources

- yfinance for price data
- pandas/numpy for calculations
- Compatible with trading-strategy-backtester plugin
