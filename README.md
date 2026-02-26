# Portfolio Risk Management & Value at Risk (VaR) Analysis

## Overview
Comprehensive risk management system for equity portfolios implementing multiple Value at Risk (VaR) methodologies, Monte Carlo simulation, backtesting, and crisis scenario analysis. Built to demonstrate quantitative risk assessment techniques used in institutional portfolio management.

## Key Results
- **Portfolio:** 10-stock diversified equity portfolio ($100,000 initial investment)
- **Period Analyzed:** 5 years (2019-2024) including COVID-19 crash
- **VaR Methods:** Historical, Parametric, and Monte Carlo (95% confidence)
- **Backtesting:** VaR model validation with violation rate analysis
- **Crisis Analysis:** Portfolio behavior during March 2020 market crash

## Portfolio Composition
Equal-weighted portfolio across sectors:
- **Technology:** AAPL, MSFT, GOOGL, META, TSLA
- **Financials:** JPM, V
- **Healthcare:** JNJ
- **Consumer Staples:** WMT, PG

## Risk Metrics Implemented

### 1. Value at Risk (VaR) - Three Methods
**Historical VaR:**
- Non-parametric approach using empirical distribution
- 95% confidence: Maximum 1-day loss prediction
- Based on actual historical returns (no distributional assumptions)

**Parametric VaR:**
- Assumes normal distribution of returns
- Uses mean and standard deviation
- Formula: `VaR = μ - z * σ` where z = 1.645 for 95% confidence

**Monte Carlo VaR:**
- 10,000 simulated return scenarios
- Captures tail risk through simulation
- More flexible than parametric methods

### 2. Conditional VaR (CVaR / Expected Shortfall)
- Average loss **beyond** VaR threshold
- Answers: "If VaR is breached, how bad will it be?"
- More informative than VaR for tail risk assessment

### 3. Risk-Adjusted Performance Metrics
**Sharpe Ratio:**
- Measures excess return per unit of total risk
- Formula: `(Portfolio Return - Risk-Free Rate) / Portfolio Volatility`

**Sortino Ratio:**
- Similar to Sharpe but uses only downside deviation
- Penalizes only negative volatility (more relevant for investors)
- Higher values indicate better risk-adjusted returns

### 4. Drawdown Analysis
**Maximum Drawdown:**
- Largest peak-to-trough decline during period
- Measures worst-case historical loss
- Critical for understanding portfolio pain points

### 5. Higher-Order Moments
**Skewness:**
- Measures asymmetry of return distribution
- Negative skew = more frequent small gains, occasional large losses
- Important for risk assessment beyond volatility

**Kurtosis:**
- Measures "fat tails" (extreme events)
- Higher kurtosis = more tail risk
- Critical for understanding crash probability

## Technical Implementation

### Data Collection (Cell 1)
- **Data Source:** Yahoo Finance via `yfinance` library
- **Historical Period:** 5 years (2019-2024)
- **Frequency:** Daily adjusted close prices
- **Preprocessing:** Automatic handling of splits, dividends

### Returns Calculation (Cell 2)
```python
returns = data.pct_change().dropna()
portfolio_returns = (returns * weights).sum(axis=1)
portfolio_value = initial_investment * (1 + portfolio_returns).cumprod()
```

### VaR Calculation Methods (Cell 4)

**Historical VaR:**
```python
historical_var = np.percentile(portfolio_returns, (1 - confidence_level) * 100)
```
- Directly takes 5th percentile of historical returns
- No distributional assumptions

**Parametric VaR:**
```python
parametric_var = mean_return - stats.norm.ppf(confidence_level) * std_return
```
- Uses normal distribution quantile function
- Fast but assumes normality (may underestimate tail risk)

**Monte Carlo VaR:**
```python
simulated_returns = np.random.normal(mean_return, std_return, 10000)
monte_carlo_var = np.percentile(simulated_returns, (1 - confidence_level) * 100)
```
- Generates 10,000 scenarios
- More robust for non-normal distributions

### Backtesting Framework (Cell 6)
**Rolling Window Validation:**
- 1-year (252-day) rolling window for VaR calculation
- Check if next day's return violates predicted VaR
- Calculate violation rate vs. expected rate (5% for 95% confidence)

**Pass/Fail Criteria:**
- **Pass:** Violation rate within 2% of expected (3%-7% range)
- **Fail:** Too many violations (underestimates risk) or too few (overestimates)

### Monte Carlo Simulation (Cell 5)
**30-Day Forward Projection:**
- 1,000 scenarios simulated
- Shows distribution of possible outcomes
- Provides 5th, 50th, and 95th percentile projections

### Crisis Scenario Analysis (Cell 7)
**COVID-19 Crash (Feb-Mar 2020):**
- Isolates specific crisis period
- Compares actual losses to VaR predictions
- Tests if VaR model would have held during extreme stress

### Risk Decomposition (Cell 8)
**Marginal Contribution to Risk:**
- Shows how much each stock contributes to portfolio volatility
- Formula: `MCR = (Σ * w) / σ_portfolio`
- Identifies concentration risk

**Component Contribution:**
- Weighted marginal contribution: `Component = w * MCR`
- Sum of all components equals total portfolio risk

## Technical Stack
- **Language:** Python 3.x
- **Data:** yfinance (Yahoo Finance API)
- **Numerical Computing:** NumPy, Pandas
- **Statistical Analysis:** SciPy (stats module)
- **Visualization:** Matplotlib, Seaborn
- **Risk Calculations:** Custom implementations of VaR methods

## Project Structure
```
Project-3-Portfolio-Risk-Management/
├── notebook/
│   └── Portfolio_Risk_VaR.ipynb    # Complete implementation (10 cells)
└── results/
    └── (visualization screenshots to be added)
```

## How to Run
1. **Open notebook:** `notebook/Portfolio_Risk_VaR.ipynb`
2. **Install dependencies:**
```bash
   pip install yfinance numpy pandas matplotlib seaborn scipy
```
3. **Run cells sequentially (1-10):**
   - **Cell 1:** Data download (requires internet connection)
   - **Cell 2:** Returns calculation and portfolio construction
   - **Cell 3:** Risk metrics (Sharpe, Sortino, Max Drawdown)
   - **Cell 4:** VaR calculation (3 methods)
   - **Cell 5:** Monte Carlo simulation
   - **Cell 6:** VaR backtesting
   - **Cell 7:** COVID-19 crisis analysis
   - **Cell 8:** Risk decomposition by stock
   - **Cell 9:** Correlation analysis
   - **Cell 10:** Final summary report

**Expected Runtime:** 5-10 minutes total (data download is slowest part)

## Key Findings & Insights

### VaR Comparison (Typical Results)
For a $100,000 portfolio at 95% confidence:
- **Historical VaR:** ~$1,500-$2,000 daily loss threshold
- **Parametric VaR:** Similar to historical if returns are near-normal
- **Monte Carlo VaR:** Captures tail risk better than parametric

### Backtesting Results
- Historical VaR typically passes backtesting (violation rate ~5%)
- Parametric VaR may fail if returns are non-normal (fat tails)
- Demonstrates importance of model validation

### Crisis Performance
**COVID-19 Crash (March 2020):**
- VaR models often breached during unprecedented events
- Worst single day typically exceeds 1-day VaR
- Shows limitations of historical data during regime changes

### Risk Concentration
- Technology stocks typically contribute >50% of portfolio risk
- Despite equal weighting, high-volatility stocks dominate risk
- Demonstrates importance of risk-based allocation (not just equal weights)

## Visualizations Generated
1. **Portfolio Value Over Time:** Cumulative performance chart
2. **Drawdown Chart:** Peak-to-trough declines over period
3. **VaR Distribution:** Histogram with VaR thresholds overlaid
4. **Monte Carlo Paths:** Simulated future scenarios (spaghetti chart)
5. **Backtest Violations:** Scatter plot showing VaR breaches
6. **Crisis Analysis:** COVID-19 period deep dive (dual chart)
7. **Risk Contribution:** Horizontal bar chart by stock
8. **Correlation Heatmap:** Pairwise stock correlations

## Limitations & Assumptions

### Model Assumptions
- **Returns normality:** Parametric VaR assumes normal distribution (often violated)
- **Historical stability:** Past distributions predict future (may not hold during regime shifts)
- **No transaction costs:** Analysis ignores trading costs and slippage
- **Constant correlations:** Correlation matrix assumed stable (increases during crises)

### Data Limitations
- **5-year window:** Limited crisis events in sample period
- **Daily frequency:** Misses intraday risk (flash crashes)
- **Survivorship bias:** All stocks survived 2019-2024 (no bankruptcies)

### Practical Considerations
- **Liquidity risk:** Assumes ability to exit positions at observed prices
- **Model risk:** All VaR models can fail during unprecedented events
- **Regulatory capital:** Banks use 99% confidence, not 95%

## Real-World Applications

### Institutional Use Cases
- **Risk Limits:** Set maximum allowable VaR for trading desks
- **Capital Requirements:** Regulatory VaR for bank capital buffers
- **Performance Attribution:** Risk-adjusted return evaluation
- **Stress Testing:** Scenario analysis for regulatory compliance

### Portfolio Management
- **Position Sizing:** Allocate based on marginal risk contribution
- **Rebalancing Triggers:** Reduce exposure when VaR exceeds threshold
- **Hedging Decisions:** Identify concentrated risks to hedge

## Future Enhancements
- **Extreme Value Theory (EVT):** Better model tail risk beyond VaR
- **GARCH Models:** Time-varying volatility for more accurate VaR
- **Copula Methods:** Model complex dependency structures
- **Multi-period VaR:** 10-day or longer horizons (regulatory standard)
- **Real-time Dashboard:** Live VaR monitoring with alerts
- **Options Integration:** Include derivatives for hedging analysis

## Learnings & Insights
1. **VaR is not worst-case loss:** It's a threshold, not a maximum
2. **Backtesting is critical:** Models must be validated, not just calculated
3. **Multiple methods matter:** Different VaR approaches provide complementary views
4. **Correlations break down:** Crisis periods show dramatically different behavior
5. **CVaR > VaR:** Expected shortfall provides more information than simple VaR

## References
- **VaR Methodology:** J.P. Morgan RiskMetrics (1996)
- **Backtesting Framework:** Basel Committee on Banking Supervision
- **Risk Metrics:** "Active Portfolio Management" by Grinold & Kahn
- **Monte Carlo Methods:** "Risk Management and Financial Institutions" by John Hull

## Author
[Anagh Das]  
[LinkedIn](linkedin.com/in/dasanagh) | [Email](ddas948@gmail.com)

---

*This project demonstrates quantitative risk management techniques used in institutional finance, with emphasis on methodology validation and practical limitations.*
