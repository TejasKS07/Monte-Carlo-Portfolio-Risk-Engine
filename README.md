# Monte Carlo VaR & CVaR Portfolio Risk Engine

A quantitative finance tool built in Python that simulates 10,000 potential future market days to calculate the tail risk of an equity portfolio.

Instead of relying purely on historical backtesting, this engine uses a **Monte Carlo Simulation** powered by **Cholesky Decomposition** to generate realistic, correlated future return paths for multiple assets. It outputs institutional-grade risk metrics including **Value at Risk (VaR)** and **Conditional Value at Risk (CVaR / Expected Shortfall)**.

## Results

Applied to a market-cap weighted portfolio of 5 Nifty equities: **Reliance, TCS, HDFC Bank, Infosys, ICICI Bank.**

| Metric | MC Simulation | Historical |
|--------|--------------|------------|
| VaR (95%, 1-day) | -2.04% | — |
| CVaR (95%, 1-day) | -2.60% | — |
| VaR on ₹10,00,000 portfolio | ₹20,400 | — |
| CVaR on ₹10,00,000 portfolio | ₹26,000 | — |

> **Interpretation:** On 95% of trading days, the portfolio is not expected to lose more than **2.04%**. In the worst 5% of scenarios (tail events / black swans), the average loss is **2.60%**.

![Risk Distribution](risk_analysis.png)

## Key Features

- **Correlated Asset Paths:** Implements Cholesky Decomposition on the historical correlation matrix to ensure simulated random numbers respect real-world statistical dependencies between assets.
- **Dynamic Market Cap Weighting:** After validating the model using an Equal Weighting ($1/N$) baseline, the engine was upgraded to fetch live market capitalizations and dynamically assign portfolio weights based on each asset's real-world scale.
- **Institutional Risk Metrics:**
  - **VaR (Value at Risk):** Maximum expected loss at a given confidence level (95%).
  - **CVaR (Expected Shortfall):** Average loss across the worst-case tail scenarios — captures black swan risk that VaR ignores.
- **Live Data Integration:** Automatically pulls historical price data via the `yfinance` API.
- **Vectorized Simulation:** 10,000 independent scenarios computed via NumPy matrix operations.

## How It Works

1. **Historical Extraction:** Extracts historical returns, drift (`mu`), volatility (`sigma`), and the Pearson Correlation Matrix for each asset.
2. **Cholesky Decomposition:** Decomposes the correlation matrix into a lower-triangular matrix $L$ such that $LL^T = \Sigma$.
3. **Correlated Simulation:** Generates a matrix of independent standard normal random variables $Z$ and injects correlation via $Z \cdot L^T$.
4. **Scaling:** Correlated draws are scaled by historical volatility and shifted by historical drift to produce realistic return scenarios.
5. **Portfolio Aggregation:** Individual asset simulations are combined using market-cap derived weights.
6. **Risk Analysis:** Sorts 10,000 portfolio outcomes to isolate the worst 5%, deriving VaR and CVaR.

## Running the Engine

Open `Code.ipynb` and run all cells top to bottom. Modify the `tickers` array under the `DATA` section to use your own stock universe.

## Technology Stack

Python · NumPy · Pandas · yfinance · Matplotlib
