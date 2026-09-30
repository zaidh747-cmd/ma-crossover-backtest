# MA Crossover Strategy — Single Stock vs Panel

A Python backtest of a moving-average crossover strategy on US equities (2020–2024), with an honest out-of-sample evaluation against a like-for-like benchmark.

## What it does

1. Grid-searches MA (fast, slow) pairs on AAPL (2020–2023) and tests on 2024.
2. Repeats the grid on a panel of 21 US large-caps, selecting parameters on 2020–2023 only.
3. Tests the selected pair on 2024 against two benchmarks:
   - **Equal-weight buy-and-hold of the same 21 stocks** (the fair benchmark)
   - **SPY buy-and-hold** (the market benchmark)
4. Measures whether parameter rankings persist out-of-sample (Spearman rank correlation).

## Key findings

- **MA parameter rankings do not persist out-of-sample.** Spearman rank correlation between train and test Sharpe was **-0.12** on the 21-stock panel. The best pair on train (50/100) was not the best on test (10/100).
- **The signal did not add value in 2024.** Against the fair benchmark (equal-weight buy-and-hold of the same 21 stocks), the strategy returned 24.1% net of 10bp costs vs 32.1% for buy-and-hold (Sharpe 2.39 vs 2.54).
- **Most risk reduction comes from diversification, not the signal.** Average single-stock vol ≈ 22%; equal-weight portfolio ≈ 11%; strategy (net) ≈ 9%. The signal contributes the last 11% → 9% step, and it costs return.
- **The strategy does beat SPY** (24.1% vs 24.9% return, but Sharpe 2.39 vs 1.83 and max drawdown -5.7% vs -8.4%), which is consistent with what trend-following is designed to do: reduce downside exposure, not maximise return.

## Method

- Daily close prices from Yahoo Finance, auto-adjusted.
- Train: 2020-01-03 → 2023-12-29. Test: 2024-01-02 → 2024-12-31.
- Signals lagged by one day (`shift(1)`) to avoid look-ahead.
- Equal-weight portfolio across the 21 tradable stocks (SPY excluded from the tradable set).
- Transaction costs: 10 basis points per position change, applied on the net-of-cost results.

## What the strategy is actually for

Trend-following is a **risk-reduction** tool, not a return-maximisation tool. It goes to cash during downtrends, which lowers volatility and drawdown, at the cost of missing some upside. In a strong bull market like 2024, this means it will underperform a pure buy-and-hold — which is exactly what happened.

## Limitations

- **Hindsight ticker selection.** The 21 names (NVDA, META, TSLA, etc.) are today's large-caps. A 2020 investor would not have known to pick them. This is a diversification demo, not a clean strategy backtest.
- **One test year.** A single year of Sharpe has a standard error of roughly 1. Differences of a few tenths are indistinguishable from noise.
- **No risk-free rate subtracted.** Sharpe is reported as `(mean / std) × √252`. Subtract a risk-free rate to compare with published Sharpes.
- **Flat cost assumption.** 10bp per position change. Real fills on some names would be worse.
- **In-sample caveat.** Any metric computed on the train window is in-sample and is not presented as strategy performance.

## Reproducing

```bash
pip install numpy pandas yfinance matplotlib seaborn
jupyter notebook ma_crossover_clean.ipynb
