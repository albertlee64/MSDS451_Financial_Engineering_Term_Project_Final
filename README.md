# MSDS451 Financial Engineering Term Project Final

## Disclaimer

This repository is for research. Past performance (including backtests) does not guarantee future results. Nothing herein is investment advice or an offer to sell or the solicitation of an offer to buy any security. Strategy performance depends on data quality, transaction costs, taxes, market impact, and investor behavior. Use at your own risk.

## Overview

A research repo for a net, implementable systematic equity strategy that:

- Selects the top-20 live tickers each rebalance by a blended Sharpe + CAGR rank,

- Tilts expected returns with cross-sectional signals (momentum & mean-reversion),

- Solves a max-Sharpe optimization with diversification caps (∑w=1, 0 ≤ wᵢ ≤ W_MAX),

- Applies realistic frictions (expense ratio monthly, turnover × bps trading costs),

- Reports robust performance diagnostics (rolling Sharpe/alpha/beta, drawdowns, turnover, concentration),

- and includes log-return Monte Carlo for forward dispersion.

## Methodology

S&P 500 Index fund asset list & selection. At each rebalance, restrict to live tickers, compute rolling (e.g., 36-month) Sharpe and CAGR, combine ranks, and keep the top-20 (MAX_ACTIVE_ASSETS).

Since I did not want to “cherry pick” my assets, I decided to change my methodology from the previous models. This makes it as realistic as possible with fund managers selecting assets based on modeling data rather than having a pre-conceived list of assets in their minds. Because of the limited amount of resources and compute power, I just stuck to equity asset classes within the S&P 500. I downloaded the current list of S&P 500 stock tickers and created a csv for the model to pick up on. This was titled “universe.csv”. This creates a realistic model that would be used by fund managers. The only exception is that they would most likely have additional assets such as ETF’s, mutual funds, and assets on other exchanges.

To reduce the number of assets down to 20 (even number that I randomly picked), I then deployed a model to determine what 20 asset classes to include. To start, the model first limits the universe to tickers that are “live,” then ranks them on a rolling window (e.g., 36 months) by a blend of Sharpe and CAGR and keeps the top 20. For those 20, it estimates expected returns and the covariance matrix. From there, the model then uses tilt expected returns and  the cross-sectional signals (momentum and mean-reversion) so favored names get a slight boost. It then solves a max-Sharpe optimization with diversification constraints: total must sum to 1 and each weight is capped to ensure diversification. This ultimately produces the portfolio weights and asset classes chosen for the fund. For rebalances it applies the realized monthly returns; on rebalance months it deducts trading costs (bps × turnover), and every month it subtracts the configured expense ratio (management/other fees) to report net performance. The model output produced the following asset classes chosen for an optimized portfolio that meets the fund prospectus parameters:

<img width="468" height="185" alt="image" src="https://github.com/user-attachments/assets/d94d4de3-8cac-4b80-8c5b-5609001c7b7e" />

## Performance Evaluation

When comparing the models and performance metrics, the Max Sharpe (net) delivered ~18.6% CAGR at ~16.7% ann. vol, for a Sharpe of ~1.11. That’s a substantial risk-adjusted premium to SPY (~9.5% CAGR, 14.9% vol, Sharpe ~0.63). The model carries a defensive beta (~0.84) and produced annualized alpha ~10% versus SPY. Drawdowns remain equity-like: MaxDD ≈ −49% (SPY ~−51%), yielding a Calmar ~0.38. Among the alternative model, Min-Var trades a bit of return for lower vol (~15.4%) with a small Sharpe give-up, and Risk Parity sits between them. An equal-weight live universe (EW_LIVE) improves on SPY but still trails Max Sharpe on efficiency.

Robustness checks show the edge is regime-dependent. The regime-dependency shows that the strategy’s edge isn’t constant and relies heavily on the market which makes sense considering all the stocks are only based on one the S&P 500 index fund. The rolling 3-year excess Sharpe is strong in 2005–08 and 2018–19, middling in 2012–16, and mostly negative in 2022–24. Rolling beta trended below 1 in recent years, so the sleeve under-participated in mega-cap-led advances, while rolling alpha also faded—together explaining recent underperformance. The hit rate (share of months beating SPY) swings from ~40–50% in softer periods to 60–80% in strong ones.

<img width="468" height="185" alt="image" src="https://github.com/user-attachments/assets/753a5352-d18a-4f0e-8c08-c7e11025ea99" />

<img width="468" height="137" alt="image" src="https://github.com/user-attachments/assets/d100c7d2-92c4-48ca-a107-60040415ee41" />

<img width="468" height="138" alt="image" src="https://github.com/user-attachments/assets/7ecbd6d5-38e9-49a1-88fd-84f3a952de82" />

<img width="468" height="137" alt="image" src="https://github.com/user-attachments/assets/9a0f0030-fb2f-43ed-b623-96ca462ff646" />

Implementation frictions are material but manageable. With the simple bps × turnover set within the model, the cumulative trading-cost drag sums to ~22% over the backtest. Fees are applied as a monthly expense ratio; plotting fee scenarios shows the expected compounding drag, but the strategy’s net Sharpe remains >1. For forward-looking risk, the log-return Monte Carlo calibrated to Max Sharpe’s monthly series yields a skewed wealth distribution from a $1M start over 25 years (example: mean ≈ $101M, 5th ≈ $18M, 95th ≈ $279M) which shows that the outcome in future years are wide.

<img width="468" height="185" alt="image" src="https://github.com/user-attachments/assets/ad339999-1674-4dca-9ede-65012751534c" />

<img width="468" height="185" alt="image" src="https://github.com/user-attachments/assets/1ea9c0eb-489a-4f79-8ce4-68ce4f070c7c" />

Using backtesting and looking at stress testing, the portfolio still mostly outperforms benchmark S&P 500. For example, during the dot-com bubble, the CAGR on the portfolio was -9.4% vs. S&P of -26.9%. Similarly during the Great Financial Crisis, CAGR on the portfolio was -28.5% vs. S&P 500 of 33.7%. During the 2022 drawdown, the portfolio CAGR was -2.3% vs. S&P 500 of -20.9%. The COVID era was the exception where the portfolio performed poorly against the benchmark (-36.3% vs. S&P 500 -32%).

<img width="468" height="185" alt="image" src="https://github.com/user-attachments/assets/2a51f21d-f896-4054-a144-ce73b461de46" />

## AI Usage

Parts of this repository (research framing, code scaffolding, doc text, and refactoring) were assisted by an AI coding assistant.

## License

MIT License














