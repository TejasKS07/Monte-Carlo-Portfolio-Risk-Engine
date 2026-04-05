# Monte Carlo VaR & CVaR Portfolio Risk Engine

A robust quantitative finance tool built in Python that simulates 10,000 potential future market days to calculate the true risk of an equity portfolio. 

Instead of relying purely on historical backtesting, this engine uses a **Monte Carlo Simulation** powered by **Cholesky Decomposition** to generate realistic, correlated future return paths for multiple assets. It outputs institutional-grade risk metrics including **Value at Risk (VaR)** and **Conditional Value at Risk (CVaR / Expected Shortfall)**.

## Key Features
* **Live Financial Data Integration:** Automatically pulls historical tick data via the `yfinance` API.
* **Dynamic Market Cap Weighting:** After initially constructing and validating the model using a standard Equal Weighting baseline ($1/N$), the engine was upgraded to fetch live market capitalizations. It now dynamically assigns portfolio weights to each asset based on their real-world scale relative to the total portfolio.
* **Correlated Asset Paths:** Implements Cholesky Decomposition on the historical correlation matrix to ensure that simulated random numbers respect the real-world statistical relationships between assets.
* **Massive Scale Simulation:** Simulates 10,000 independent trading days across the portfolio using `numpy`'s vectorized matrix multiplication for extreme performance.
* **Institutional Risk Metrics:**
  * **VaR (Value at Risk):** Calculates the maximum expected loss at a given confidence interval (e.g., 95%).
  * **CVaR (Expected Shortfall):** Averages the absolute worst-case scenarios to measure "fat-tail" black swan risks that VaR ignores.
* **Visual Risk Distribution:** Generates a probability density histogram plotting the full distribution of simulated returns alongside distinct risk thresholds.

## Technology Stack
* **Python 3.x**
* **Pandas:** Data manipulation, cleaning, and time-series pct_change generation.
* **NumPy:** Linear algebra operations, standard normal random number generation, and matrix multiplication.
* **yfinance:** Asset data extraction.
* **Matplotlib:** Data visualization and histogram generation.

## How It Works
1. **Historical Extraction:** The engine extracts historical Returns, Drift (`mu`), Risk (`sigma`), and the Pearson Correlation Matrix (`corr`) for the chosen assets.
2. **Decomposition:** It mathematically breaks down the correlation matrix into a lower-triangular matrix $L$.
3. **Simulation:** It generates a massive matrix of pure, independent random variables $Z$ and mathematically "injects" the historical correlation via a dot product ($Z \cdot L^{T}$).
4. **Scaling:** The correlated random numbers are scaled by historical volatility and shifted by historical drift.
5. **Portfolio Aggregation:** The individual asset simulations are combined using defined portfolio weights. While initially modeled with Equal Weights as a baseline, the aggregation is now driven by live Market Capitalization data, ensuring that larger companies command a proportionately larger impact on the simulated portfolio.
6. **Risk Analysis:** The engine sorts the 10,000 portfolio outcomes to isolate the worst 5% of days, deriving the VaR and CVaR metrics.

## Getting Started

### Prerequisites
Make sure you have the required libraries installed:
```bash
pip install numpy pandas yfinance matplotlib
```

### Running the Engine
1. Open the `Code.ipynb` Jupyter Notebook.
2. Under the `DATA` section, modify the `tickers` array to include your desired stock tickers (e.g., `['RELIANCE.NS', 'TCS.NS']`).
3. Run all cells from top to bottom.
4. The final cell will output the **95% VaR**, **95% CVaR**, and a histogram visualizing your portfolio's risk profile!

## Example Output Interpretation
* **VaR -2.5% :** We are 95% confident that the portfolio will not lose more than 2.5% of its value on any given normal trading day.
* **CVaR -3.8% :** In the 5% chance of a market crash event, we expect our average losses to be roughly 3.8%.
