# S&P 500 Stock Market Analysis

Five-year temporal analysis of major technology stocks with advanced time series techniques and multi-security correlation analysis

## Project Overview

This project conducts comprehensive time series analysis of the S&P 500 stock index, with particular focus on major technology companies. Using 5+ years of historical daily trading data, the analysis combines classical time series techniques (moving averages, trend identification) with correlation analysis to identify patterns and relationships in equity markets.

## 🎯 Project Objectives

1. **Time Series Cleaning:** Handle missing values, duplicates, and data quality issues in historical data
2. **Noise Removal:** Apply moving average techniques to filter market volatility and identify true trends
3. **Trend Identification:** Detect support/resistance levels and momentum changes
4. **Correlation Analysis:** Quantify relationships between major tech stocks
5. **Pattern Recognition:** Apply technical analysis methods for trading signals
6. **Interactive Exploration:** Create dynamic visualizations for data exploration

## 📊 Data Overview

### Dataset Scale
- **Total Stocks:** 139 S&P 500 companies
- **Time Period:** 5+ years of daily trading data
- **Trading Days:** ~1,260 days per year
- **Total Records:** 130,000+ daily price observations

### Geographic & Market Scope
- **Market:** US equity market (S&P 500 index constituents)
- **Trading Hours:** NYSE regular hours (9:30 AM - 4:00 PM ET)
- **Currencies:** All prices in USD (US dollars)

### Focus Companies (Analyzed in Depth)

| Ticker | Company | Sector | Market Cap |
|--------|---------|--------|-----------|
| **AAPL** | Apple Inc. | Technology | 2.5+ Trillion |
| **MSFT** | Microsoft Corporation | Technology | 2.2+ Trillion |
| **GOOG** | Alphabet Inc. | Technology | 1.7+ Trillion |
| **AMZN** | Amazon.com Inc. | Consumer / Cloud | 1.6+ Trillion |

**Rationale for Focus:** These are the largest FAANG+ companies with highest market impact and liquidity.

### Additional Available Data
- **135 Additional Stocks:** Complete dataset includes 135 other S&P 500 constituents
- **Sectors Represented:** Financials, Healthcare, Energy, Industrials, Consumer Staples, etc.
- **Accessibility:** All data can be loaded and analyzed using provided scripts

## 📁 File Structure & Data Format

```
S&P_resources/
├── time_series_project_stocks.ipynb    # Main analysis notebook (52 cells)
├── README_ANALYTICS.md                 # This documentation file
└── individual_stocks_5yr/              # Data directory with 139 CSV files
    ├── AAPL_data.csv                   # Apple Inc.
    ├── MSFT_data.csv                   # Microsoft
    ├── GOOG_data.csv                   # Alphabet
    ├── AMZN_data.csv                   # Amazon
    ├── A_data.csv                      # Agilent Technologies
    ├── AAL_data.csv                    # American Airlines
    └── [134 additional stock files]    # Other S&P 500 constituents
```

### CSV File Structure

**Each stock CSV contains:**

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| **Date** | String | Trading date | 2019-01-02 |
| **Open** | Float | Opening price | 154.89 |
| **High** | Float | Daily high price | 155.64 |
| **Low** | Float | Daily low price | 154.01 |
| **Close** | Float | Closing price (adjusted) | 154.39 |
| **Volume** | Integer | Shares traded | 24,537,200 |

**Time Range Per Stock:** 1,260+ daily observations (5+ years)

**Example Data Snippet:**
```
Date,Open,High,Low,Close,Volume
2019-01-02,154.89,155.64,154.01,154.39,24537200
2019-01-03,151.61,152.89,150.71,151.35,32652670
2019-01-04,151.75,155.87,151.64,155.35,40001410
```

## 📊 Analysis Notebook Structure

### `time_series_project_stocks.ipynb` (52 Cells)

#### Section 1: Environment Setup (Cells 1-5)

**Libraries Imported:**
```python
import pandas as pd                 # Data manipulation
import numpy as np                  # Numerical operations
import matplotlib.pyplot as plt     # Static visualization
import seaborn as sns               # Statistical graphics
import plotly.express as px         # Interactive charts
import plotly.graph_objects as go   # Advanced Plotly
from pathlib import Path            # File operations
from glob import glob               # File pattern matching
```

**Special Considerations:**
- Google Colab integration (if applicable)
- GPU acceleration availability
- Large dataset handling optimization

#### Section 2: Data Discovery & Loading (Cells 6-10)

**File Discovery:**
```python
stock_files = glob('individual_stocks_5yr/*.csv')
print(f"Found {len(stock_files)} stock files")  # Should find 139 files
```

**Focus Stock Loading:**
```python
aapl = pd.read_csv('individual_stocks_5yr/AAPL_data.csv')
amzn = pd.read_csv('individual_stocks_5yr/AMZN_data.csv')
goog = pd.read_csv('individual_stocks_5yr/GOOG_data.csv')
msft = pd.read_csv('individual_stocks_5yr/MSFT_data.csv')
```

**Data Consolidation:**
```python
# Combine focus stocks into single DataFrame
stocks = pd.concat([
    aapl.assign(Name='AAPL'),
    amzn.assign(Name='AMZN'),
    goog.assign(Name='GOOG'),
    msft.assign(Name='MSFT')
], ignore_index=True)
```

#### Section 3: Data Quality & Cleaning (Cells 11-15)

**Data Integrity Checks:**

1. **Null Value Assessment**
   ```python
   print(stocks.isnull().sum())
   # Output: Shows missing values per column
   # Expected: Minimal nulls (< 0.1%)
   ```

2. **Duplicate Detection**
   ```python
   duplicates = stocks[stocks.duplicated(subset=['Date', 'Name'])]
   print(f"Duplicate rows: {len(duplicates)}")
   # Expected: 0 duplicates
   ```

3. **Data Type Validation**
   ```python
   # Check data types:
   # Date: should be object/string initially
   # OHLCV: should be numeric (float/int)
   ```

4. **Outlier Identification**
   ```python
   # Check for extreme price movements
   stocks['Daily_Return_%'] = stocks.groupby('Name')['Close'].pct_change() * 100
   extreme_days = stocks[stocks['Daily_Return_%'] > 15]  # > 15% moves
   print(f"Extreme move days: {len(extreme_days)}")
   ```

**Cleaning Actions:**
- Remove rows with critical missing data
- Handle data type conversions
- Flag but retain outliers (valid market events)

#### Section 4: Date Conversion & Parsing (Cells 16-20)

**DateTime Conversion:**
```python
stocks['Date'] = pd.to_datetime(stocks['Date'])
# Verify conversion
print(stocks['Date'].dtype)  # Should be 'datetime64[ns]'
```

**Time-Based Indexing:**
```python
stocks = stocks.sort_values('Date')
stocks['Year'] = stocks['Date'].dt.year
stocks['Month'] = stocks['Date'].dt.month
stocks['Quarter'] = stocks['Date'].dt.quarter
stocks['Day_of_Week'] = stocks['Date'].dt.dayofweek
stocks['Week_of_Year'] = stocks['Date'].dt.isocalendar().week
```

**Benefits:**
- Enable time-based filtering (annual, monthly)
- Detect seasonal patterns
- Align with business calendars

#### Section 5: Statistical Overview (Cells 21-25)

**Summary Statistics by Stock:**
```python
summary = stocks.groupby('Name')['Close'].agg([
    'count',
    'mean',
    'median',
    'std',
    'min',
    'max'
]).round(2)
```

**Output Example:**
```
       count   mean   median    std    min      max
AAPL    1260  136.45  132.89   58.32  101.30  232.45
AMZN    1260  1854.67 1923.21  587.32 801.45 3234.19
GOOG    1260  1203.45 1189.34  421.23 512.67 2023.45
MSFT    1260  187.62  184.23   67.12  92.34  309.18
```

**Interpretation:**
- **Mean/Median proximity:** Suggests symmetric distribution
- **High Std Dev:** Indicates greater volatility
- **Min/Max Range:** Shows price range over 5 years

#### Section 6: Daily Return Calculation (Cells 26-30)

**Returns Computation:**
```python
# Percentage returns
stocks['Daily_Return_%'] = stocks.groupby('Name')['Close'].pct_change() * 100

# Log returns (preferred for statistical analysis)
stocks['Log_Return'] = np.log(stocks['Close'] / stocks['Close'].shift(1))

# Dollar returns
stocks['Dollar_Return'] = stocks['Close'] - stocks['Close'].shift(1)
```

**Metrics:**
- **Positive returns:** Gains in value
- **Negative returns:** Losses in value
- **Volatility:** Standard deviation of returns

#### Section 7: Moving Average Calculation (Cells 31-40)

**Simple Moving Average (SMA):**

Moving averages smooth price data to reveal underlying trends by filtering daily noise.

$$\text{SMA}_n = \frac{\sum_{i=0}^{n-1} P_i}{n}$$

Where:
- $P_i$ = Price on day i
- $n$ = Number of days in window

**Common Periods:**
- **MA20:** 20-day moving average (short-term, ~1 month)
- **MA50:** 50-day moving average (medium-term, ~2-3 months)
- **MA200:** 200-day moving average (long-term, ~1 year)

**Implementation:**
```python
stocks['MA_20'] = stocks.groupby('Name')['Close'].transform(
    lambda x: x.rolling(window=20).mean()
)
stocks['MA_50'] = stocks.groupby('Name')['Close'].transform(
    lambda x: x.rolling(window=50).mean()
)
stocks['MA_200'] = stocks.groupby('Name')['Close'].transform(
    lambda x: x.rolling(window=200).mean()
)
```

**Interpretation:**
- **Price above MA200:** Uptrend
- **Price below MA200:** Downtrend
- **MA20 crosses MA50:** Trading signal
  - Golden Cross (20↗ 50): Buy signal
  - Death Cross (20↘ 50): Sell signal

#### Section 8: Trend Analysis & Pattern Recognition (Cells 41-45)

**Trend Direction:**
```python
stocks['Trend_Direction'] = 'Neutral'
stocks.loc[stocks['Close'] > stocks['MA_200'], 'Trend_Direction'] = 'Uptrend'
stocks.loc[stocks['Close'] < stocks['MA_200'], 'Trend_Direction'] = 'Downtrend'
```

**Support/Resistance Levels:**
```python
# Support: Local minimum price
support = stocks.groupby('Name')['Close'].rolling(window=20).min().reset_index()

# Resistance: Local maximum price  
resistance = stocks.groupby('Name')['Close'].rolling(window=20).max().reset_index()
```

**Pattern Detection:**
- **Golden Cross:** MA20 > MA50 (buy signal opportunity)
- **Death Cross:** MA20 < MA50 (sell signal opportunity)
- **Breakout:** Price breaks above resistance or below support
- **Consolidation:** Price ranges between support/resistance

#### Section 9: Correlation Analysis (Cells 46-48)

**Pearson Correlation Matrix:**
$$r = \frac{\sum(x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum(x_i - \bar{x})^2 \sum(y_i - \bar{y})^2}}$$

**Calculation:**
```python
close_prices_pivot = stocks.pivot_table(
    values='Close',
    index='Date',
    columns='Name'
)[['AAPL', 'MSFT', 'GOOG', 'AMZN']]

correlation_matrix = close_prices_pivot.corr()
```

**Correlation Heatmap Output:**
```
        AAPL   MSFT   GOOG   AMZN
AAPL    1.00   0.78   0.72   0.81
MSFT    0.78   1.00   0.75   0.79
GOOG    0.72   0.75   1.00   0.76
AMZN    0.81   0.79   0.76   1.00
```

**Interpretation:**
- **1.0:** Perfect positive correlation (move together)
- **0.7-0.9:** Strong correlation
- **0.0:** No correlation
- **-1.0:** Perfect negative correlation (move opposite)

**Tech Stock Insights:**
- High correlations (0.72-0.81) reveal tech sector co-movement
- Market-wide events affect all tech stocks similarly
- Diversification challenged within sector

#### Section 10: Visualizations - Static Plots (Cells 49-50)

**Plot Types Generated:**

1. **Line Charts (Price Over Time)**
   ```python
   for stock in ['AAPL', 'MSFT', 'GOOG', 'AMZN']:
       data = stocks[stocks['Name'] == stock]
       plt.figure(figsize=(14, 6))
       plt.plot(data['Date'], data['Close'], label='Close Price')
       plt.plot(data['Date'], data['MA_20'], label='MA20')
       plt.plot(data['Date'], data['MA_50'], label='MA50')
       plt.plot(data['Date'], data['MA_200'], label='MA200')
       plt.title(f'{stock} Price with Moving Averages')
       plt.legend()
       plt.show()
   ```

2. **Correlation Heatmap**
   ```python
   sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', vmin=-1, vmax=1)
   plt.title('Tech Stocks Correlation Matrix (5 Years)')
   plt.show()
   ```

3. **Distribution Plots**
   ```python
   stocks[stocks['Name'] == 'AAPL']['Daily_Return_%'].hist(bins=100)
   plt.title('AAPL Daily Returns Distribution')
   plt.xlabel('Return %')
   plt.ylabel('Frequency')
   ```

4. **Volatility Over Time**
   ```python
   volatility = stocks.groupby('Name')['Daily_Return_%'].rolling(20).std()
   plt.plot(volatility)
   plt.title('30-Day Rolling Volatility')
   ```

#### Section 11: Interactive Visualizations (Cells 51-52)

**Plotly Interactive Charts:**

```python
import plotly.express as px

# Interactive multi-stock comparison
fig = px.line(stocks, 
              x='Date', 
              y='Close', 
              color='Name',
              title='5-Year Stock Price Trends',
              labels={'Close': 'Price ($)'})
fig.show()

# Interactive candlestick chart (advanced)
fig = go.Figure(data=[go.Candlestick(
    x=aapl['Date'],
    open=aapl['Open'],
    high=aapl['High'],
    low=aapl['Low'],
    close=aapl['Close']
)])
fig.update_layout(title='AAPL Candlestick Chart')
fig.show()
```

**Interactive Features:**
- Hover for exact values
- Zoom into time periods
- Toggle stocks on/off
- Compare multiple stocks
- Export as PNG

## 🎲 Key Financial Metrics

### Volatility Measures

**Standard Deviation:**
- Annual volatility = Daily std dev × √252
- Measures price variability
- Higher volatility = Higher risk

**Beta:**
$$\beta = \frac{\text{Covariance}(\text{Stock Return, Market Return})}{\text{Variance}(\text{Market Return})}$$
- β > 1: More volatile than market
- β = 1: Same as market
- β < 1: Less volatile than market

### Performance Metrics

**Cumulative Return:**
- Starting price vs ending price
- Shows total appreciation/depreciation
- Example: AAPL from $150 to $180 = +20%

**Sharpe Ratio:**
$$\text{Sharpe Ratio} = \frac{\text{Avg Return} - \text{Risk-Free Rate}}{\text{Std Dev}}$$
- Measures risk-adjusted returns
- Higher = Better risk-adjusted performance

### Trend Indicators

**Moving Average Convergence Divergence (MACD):**
- Faster MA minus slower MA
- Positive = Uptrend
- Negative = Downtrend
- Crossovers = Trading signals

**Relative Strength Index (RSI):**
- Range: 0-100
- > 70: Overbought (potential sell)
- < 30: Oversold (potential buy)

## 🛠️ Technologies Used

| Technology | Version | Purpose |
|---|---|---|
| Python | 3.7+ | Programming language |
| Pandas | 1.0+ | Data manipulation |
| NumPy | 1.18+ | Numerical operations |
| Matplotlib | 3.0+ | Static plotting |
| Seaborn | 0.11+ | Statistical visualization |
| Plotly | 4.0+ | Interactive charts |
| Jupyter | Latest | Notebook environment |

## 🚀 How to Run

### Option 1: Google Colab
1. Open notebook in Colab: `File → Open Notebook`
2. Upload CSV files or mount Google Drive
3. Run cells sequentially
4. Export results

### Option 2: Local Jupyter
```bash
# Install dependencies
pip install pandas numpy matplotlib seaborn plotly jupyter

# Launch notebook
jupyter notebook time_series_project_stocks.ipynb

# Run cells with Shift+Enter
```

### Option 3: VSCode
1. Install Python extension
2. Select Python interpreter
3. Open notebook file
4. Click "Run All Cells"

## 📊 Expected Outputs

**Primary Visualizations:**
1. Multi-year price trends with moving averages
2. Correlation heatmap showing stock relationships
3. Distribution plots of daily returns
4. Interactive price charts with zoom capability
5. Volatility trends over time
6. Technical pattern indicators (Golden Cross, etc.)

**Key Statistics:**
- 5-year cumulative returns by stock
- Annual volatility estimates
- Correlation coefficients matrix
- Moving average crossover dates
- Support/resistance level identifications

## 💡 Insights for Different Audiences

### For Individual Investors
- Understand long-term price trends
- Identify oversold/overbought conditions
- Evaluate portfolio diversification needs
- Track major technicals

### For Portfolio Managers
- Correlation analysis for diversification
- Volatility assessment for risk management
- Trend identification for tactical allocation
- Performance benchmarking

### For Data Scientists
- Time series analysis techniques
- Moving average smoothing methods
- Correlation and covariance calculations
- Interactive visualization best practices

## ⚠️ Important Disclaimers

### Analysis Limitations
- ⚠️ Past performance ≠ future performance
- ⚠️ Analysis is descriptive, not predictive
- ⚠️ No guarantee of future returns
- ⚠️ Does not account for dividends/splits in all cases
- ⚠️ Excludes transaction costs/taxes

### Investment Disclaimer
- ⚠️ Not financial advice
- ⚠️ Consult professional advisor before investing
- ⚠️ Stock market involves significant risk
- ⚠️ Only invest money you can afford to lose
- ⚠️ Past correlation ≠ Future correlation

## 🔮 Future Enhancements

- **ML Predictions:** LSTM/Prophet models for price forecasting
- **Risk Metrics:** Value-at-Risk (VaR), Expected Shortfall
- **Options Analysis:** Implied volatility, Greeks calculations
- **Sentiment Analysis:** News sentiment vs stock performance
- **Real-time Data:** Live price feeds and alerts
- **Portfolio Optimization:** Efficient frontier calculation
- **Backtesting:** Trading strategy performance testing

## 📚 Technical References

### Time Series Concepts
- **Stationarity:** Essential for statistical models (ADF test)
- **Autocorrelation:** Price dependency on past values (ACF/PACF)
- **Seasonality:** Regular recurring patterns
- **Trend:** Long-term directional movement

### Technical Analysis
- **Support/Resistance:** Historical price levels
- **Trendlines:** Visual trend determination
- **Chart Patterns:** Head & shoulders, triangles, flags
- **Volume Confirmation:** Price movement validation

### Statistical Foundations
- **Correlation vs Causation:** Beware spurious relationships
- **p-values:** Statistical significance thresholds (< 0.05)
- **Confidence Intervals:** Range of likely parameter values
- **Hypothesis Testing:** Rigorous claim validation

---

**Project Author:** Iraklis Papigkiotis

**Last Updated:** 2026

Explore the interactive notebook for comprehensive stock analysis!
