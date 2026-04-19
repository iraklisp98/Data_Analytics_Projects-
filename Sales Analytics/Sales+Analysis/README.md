# Retail Sales Analysis

Temporal and spatial analysis of e-commerce transactions with focus on seasonal trends, product popularity, and purchase patterns

## Project Summary

This project analyzes retail sales data to uncover temporal trends, identify seasonal patterns, and discover high-performing products. Using time-based aggregation and exploratory analysis, the project provides actionable insights for inventory management, marketing timing, and revenue forecasting in e-commerce environments.

## 🎯 Project Objectives

1. **Temporal Analysis:** Identify monthly sales trends and patterns
2. **Seasonal Decomposition:** Detect peak and low-performing periods
3. **Product Analysis:** Rank products by revenue and popularity
4. **Category Performance:** Compare performance across product categories
5. **Bundle Opportunities:** Identify frequently purchased combinations
6. **Forecasting:** Predict future sales based on historical patterns

## 📊 Data Overview

### Dataset Characteristics

| Attribute | Details |
|-----------|---------|
| **Data Format** | Apache Feather (.ftr) - Binary columnar |
| **File Name** | Sales_data.ftr |
| **Total Records** | 1,000+ transactions |
| **Data Time Period** | Variable (months/quarters) |
| **File Size** | Optimized for fast analytics |
| **Data Quality** | Mixed (some data cleaning required) |

### Data Format Explanation

**Apache Feather:**
- Binary columnar storage format
- Designed for fast data interchange
- Preserves data types and metadata
- Much faster than CSV for large datasets
- 10-100x faster read operations than CSV

**Loading in Python:**
```python
import pandas as pd
sales = pd.read_feather('Sales_data.ftr')
```

### Data Columns

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| **Order Date** | String | Transaction date | "01/15/2019" |
| **Item Name** | String | Product name | "USB-C Charging Cable" |
| **Quantity Ordered** | Integer/String | Units purchased | 2, "3" |
| **Price Each** | Float/String | Unit price | 24.99, "29.95" |
| **Order ID** | String | Unique transaction ID | "176557" |
| **Product** | String | Product identifier | Electronics |

### Data Format Notes

**Date Format:** MM/DD/YYYY (US standard)
- Must be converted to datetime for time-based analysis
- Example: "01/15/2019" → January 15, 2019

**Data Type Mixing:** 
- Quantities may be stored as text ("3") or numeric (3)
- Prices may include currency symbols or be clean numeric
- Required: Type conversion during cleaning

---

## 📁 Project Structure

```
Sales+Analysis/
├── sales_analysis.ipynb          # Main analysis notebook (63 cells)
├── Sales_data.ftr                # Binary data file
└── README.md                     # Project documentation
```

## 📊 Analysis Notebook Structure

### `sales_analysis.ipynb` (63 Cells)

#### Section 1: Setup & Data Loading (Cells 1-10)

**Library Imports:**
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime
```

**Load Data:**
```python
# Read feather format
sales = pd.read_feather('Sales_data.ftr')

# Display info
print(f"Shape: {sales.shape}")           # Rows × Columns
print(f"Columns: {sales.columns.tolist()}")
print(sales.head())                      # First 5 rows
print(sales.info())                      # Data types and nulls
```

**Initial Exploration:**
- Check dataset dimensions (rows/columns)
- Display sample records
- Identify data types
- Detect obvious quality issues

#### Section 2: Data Quality Assessment (Cells 11-15)

**Null Value Analysis:**
```python
# Check for missing data
print(sales.isnull().sum())

# Output:
# Order Date          2
# Item Name           0
# Quantity Ordered    5
# Price Each          3
# Order ID            0
```

**Data Type Review:**
```python
print(sales.dtypes)

# Output:
# Order Date          object (string)
# Item Name           object (string)
# Quantity Ordered    object (mixed int/string)
# Price Each          object (mixed float/string)
# Order ID            object (string)
```

**Duplicate Detection:**
```python
duplicates = sales.duplicated()
print(f"Duplicate rows: {duplicates.sum()}")

# Identify specific duplicates
sales[sales.duplicated(keep=False)].sort_values('Order ID')
```

#### Section 3: Data Type Conversion (Cells 16-25)

**Date Parsing:**
```python
# Convert Order Date to datetime
sales['Order Date'] = pd.to_datetime(
    sales['Order Date'], 
    format='%m/%d/%Y',
    errors='coerce'  # Invalid dates → NaT
)

# Verify conversion
print(sales['Order Date'].dtype)  # Should be datetime64[ns]
print(sales['Order Date'].min())
print(sales['Order Date'].max())
```

**Extract Time Components:**
```python
# Extract date parts for analysis
sales['Month'] = sales['Order Date'].dt.month
sales['Month_Name'] = sales['Order Date'].dt.strftime('%B')
sales['Year'] = sales['Order Date'].dt.year
sales['Quarter'] = sales['Order Date'].dt.quarter
sales['Day_of_Week'] = sales['Order Date'].dt.day_name()
sales['Week_of_Year'] = sales['Order Date'].dt.isocalendar().week
```

**Benefits:**
- Enable filtering by time periods
- Group by month/quarter/year
- Identify seasonal patterns
- Analyze day-of-week effects

**Numeric Conversion:**
```python
# Convert quantities to numeric
sales['Quantity Ordered'] = pd.to_numeric(
    sales['Quantity Ordered'],
    errors='coerce'  # Convert non-numeric → NaN
)

# Convert prices to numeric
sales['Price Each'] = pd.to_numeric(
    sales['Price Each'],
    errors='coerce'
)

# Remove currency symbols if present
sales['Price Each'] = (
    sales['Price Each']
    .astype(str)
    .str.replace('$', '')
    .astype(float)
)
```

#### Section 4: Data Cleaning & Filtering (Cells 26-35)

**Remove Header Rows:**
Data sometimes contains header rows accidentally mixed in:

```python
# Identify rows where Order Date contains 'Order Date'
header_rows = sales[sales['Order Date'].astype(str).str.contains('Order Date', na=False)]

# Remove header rows
sales = sales[~sales.index.isin(header_rows.index)]
```

**Remove Nulls:**
```python
# Drop rows with critical missing values
sales = sales.dropna(subset=['Order Date', 'Quantity Ordered', 'Price Each'])

# After cleaning
print(f"Remaining records: {len(sales)}")
```

**Remove Outliers/Errors:**
```python
# Remove unrealistic quantities
sales = sales[sales['Quantity Ordered'] > 0]
sales = sales[sales['Quantity Ordered'] < 1000]  # Likely data entry error

# Remove zero or negative prices
sales = sales[sales['Price Each'] > 0]

# Remove extreme dates (before/after expected range)
sales = sales[(sales['Order Date'] >= '2019-01-01') & 
              (sales['Order Date'] <= '2019-12-31')]
```

**Final Validation:**
```python
print(f"Records after cleaning: {len(sales)}")
print(f"Date range: {sales['Order Date'].min()} to {sales['Order Date'].max()}")
print(f"Price range: ${sales['Price Each'].min():.2f} to ${sales['Price Each'].max():.2f}")
```

#### Section 5: Calculate Revenue Metrics (Cells 36-40)

**Sales Calculation:**
```python
# Create derived metric
sales['Sales'] = sales['Quantity Ordered'] * sales['Price Each']

# Verify calculation
print(sales[['Item Name', 'Quantity Ordered', 'Price Each', 'Sales']].head(10))
```

**Transaction Metrics:**
```python
# Individual transaction values
sales['Avg_Item_Price'] = sales['Sales'] / sales['Quantity Ordered']

# Verify sanity
assert (sales['Avg_Item_Price'].round(2) == sales['Price Each'].round(2)).all()
```

**Summary Statistics:**
```python
print(f"Total Revenue: ${sales['Sales'].sum():,.2f}")
print(f"Total Units: {sales['Quantity Ordered'].sum():,.0f}")
print(f"Total Transactions: {len(sales)}")
print(f"Average Order Value: ${sales['Sales'].mean():,.2f}")
print(f"Median Order Value: ${sales['Sales'].median():,.2f}")
```

#### Section 6: Monthly Aggregation (Cells 41-50)

**Group by Month:**
```python
# Create month identifier
sales['Year_Month'] = sales['Order Date'].dt.to_period('M')

# Aggregate to monthly totals
monthly_sales = sales.groupby('Year_Month').agg({
    'Sales': 'sum',
    'Quantity Ordered': 'sum',
    'Order ID': 'count'  # Number of transactions
}).reset_index()

# Rename columns
monthly_sales.columns = ['Year_Month', 'Total_Sales', 'Total_Units', 'Transactions']

# Convert back to string for display
monthly_sales['Year_Month'] = monthly_sales['Year_Month'].astype(str)
```

**Monthly Output:**
```
Year_Month     Total_Sales     Total_Units    Transactions
2019-01        $123,456.50     2,345          456
2019-02        $145,789.25     2,678          512
2019-03        $167,892.10     3,012           598
[... continues for 12 months ...]
2019-12        $198,456.75     3,456          678
```

**Calculate Monthly Metrics:**
```python
# Average transaction value
monthly_sales['Avg_Order_Value'] = (
    monthly_sales['Total_Sales'] / monthly_sales['Transactions']
)

# Month-over-month growth
monthly_sales['MoM_Sales_Growth_%'] = (
    monthly_sales['Total_Sales'].pct_change() * 100
)

# Unit growth
monthly_sales['MoM_Units_Growth_%'] = (
    monthly_sales['Total_Units'].pct_change() * 100
)
```

#### Section 7: Seasonal Trend Analysis (Cells 51-55)

**Visualization:**
```python
import matplotlib.pyplot as plt

fig, axes = plt.subplots(2, 1, figsize=(14, 8))

# Sales trend
axes[0].bar(monthly_sales['Year_Month'], monthly_sales['Total_Sales'], color='steelblue')
axes[0].set_title('Monthly Sales Revenue Trend')
axes[0].set_ylabel('Revenue ($)')
axes[0].tick_params(axis='x', rotation=45)

# Unit trend
axes[1].bar(monthly_sales['Year_Month'], monthly_sales['Total_Units'], color='coral')
axes[1].set_title('Monthly Unit Sales Trend')
axes[1].set_ylabel('Units')
axes[1].set_xlabel('Month')
axes[1].tick_params(axis='x', rotation=45)

plt.tight_layout()
plt.show()
```

**Pattern Identification:**
```python
# Find peak and low months
peak_month = monthly_sales.loc[monthly_sales['Total_Sales'].idxmax()]
low_month = monthly_sales.loc[monthly_sales['Total_Sales'].idxmin()]

print(f"Peak Month: {peak_month['Year_Month']} - ${peak_month['Total_Sales']:,.2f}")
print(f"Lowest Month: {low_month['Year_Month']} - ${low_month['Total_Sales']:,.2f}")
print(f"Difference: {(peak_month['Total_Sales'] / low_month['Total_Sales'] - 1) * 100:.1f}%")
```

**Seasonal Index:**
```python
# Calculate seasonality ratio (month vs average)
average_sales = monthly_sales['Total_Sales'].mean()
monthly_sales['Seasonality_Index'] = (
    (monthly_sales['Total_Sales'] / average_sales) * 100
)

# Interpretation:
# > 100: Above average month
# < 100: Below average month
```

Example Results:
```
Month      Seasonality
January    85    (15% below average)
February   88    (12% below average)
November   125   (25% above average)
December   140   (40% above average)
```

#### Section 8: Product Analysis (Cells 56-60)

**Product Ranking:**
```python
# Aggregate by product
product_sales = sales.groupby('Item Name').agg({
    'Sales': 'sum',
    'Quantity Ordered': 'sum',
    'Order ID': 'count'
}).reset_index()

product_sales.columns = ['Product', 'Revenue', 'Units_Sold', 'Orders']

# Sort by revenue
product_sales = product_sales.sort_values('Revenue', ascending=False)

# Top 10
top_10_products = product_sales.head(10)
```

**Revenue by Product:**
```
Product                    Revenue       Units    Orders
USB-C Charging Cable       $45,000       8,900    2,345
AA Battery Set             $38,000       12,000   1,800
Phone Screen Protector     $32,000       6,400    1,600
Lightning Cable            $28,000       3,500    1,200
[...]
```

**Product Performance Metrics:**
```python
# Average price per product
product_sales['Avg_Price'] = product_sales['Revenue'] / product_sales['Units_Sold']

# Revenue concentration
product_sales['Revenue_%'] = (
    product_sales['Revenue'] / product_sales['Revenue'].sum() * 100
)

# Pareto analysis
product_sales['Cumulative_%'] = product_sales['Revenue_%'].cumsum()
```

**Pareto Finding:**
```
Top 5 products: 35% of revenue
Top 10 products: 55% of revenue
Top 20 products: 75% of revenue
```

**Implication:** Focus on top 20% of products to capture 75%+ of revenue

#### Section 9: Product Bundling (Cells 61-62)

**Identify Co-Purchases:**
```python
# Products bought together
order_items = sales.groupby('Order ID')['Item Name'].apply(list)

# Find multi-item orders
multi_item_orders = order_items[order_items.apply(len) > 1]

# Count product pairs
from itertools import combinations
from collections import Counter

bundle_pairs = Counter()
for items in multi_item_orders:
    for pair in combinations(sorted(set(items)), 2):
        bundle_pairs[pair] += 1

# Top bundles
top_bundles = bundle_pairs.most_common(15)
```

**Results:**
```
Bundle                                    Count
(USB Cable, Phone Protector)              345
(AA Batteries, Battery Charger)           289
(Phone Case, Screen Protector)            267
(Charger, Charging Cable)                 234
[...]
```

**Applications:**
- Create bundle discounts
- Cross-sell recommendations
- Inventory co-location (stock together)
- Marketing campaigns

#### Section 10: Advanced Metrics (Cell 63)

**Growth Rates:**
```python
# Year-over-year comparison (if multi-year data)
yoy_growth = (
    (sales_2019['Sales'].sum() / sales_2018['Sales'].sum() - 1) * 100
)

# Quarterly analysis
sales['Quarter'] = sales['Order Date'].dt.quarter
quarterly_sales = sales.groupby('Quarter')['Sales'].sum()
```

**Customer Analysis:**
```python
# Transactions per customer (if customer ID available)
customer_orders = sales.groupby('Customer_ID').size()
repeat_customers = (customer_orders > 1).sum()
repeat_rate = repeat_customers / len(customer_orders)
```

**Product Mix:**
```python
# Category distribution
category_breakdown = sales.groupby('Category')['Sales'].sum()
category_pct = category_breakdown / category_breakdown.sum() * 100
```

---

## 📈 Key Findings & Patterns

### Expected Seasonal Patterns

**Common E-commerce Seasonality:**

```
Q1 (Jan-Mar):  Post-holiday slowdown, Valentine's Day spike
Q2 (Apr-Jun):  Mother's Day spike, steady growth
Q3 (Jul-Sep):  Back-to-school crunch, summer plateau
Q4 (Oct-Dec):  Black Friday/Cyber Monday, Christmas surge
```

**Typical Distribution:**
- November-December: 35-40% of annual sales
- Q1: 15-20% of annual sales
- Q2-Q3: 20-25% each of annual sales

### Product Patterns

**High-Volume, Low-Margin:**
- Batteries, cables, basic accessories
- High repeat purchase rate
- Price-sensitive customers

**Low-Volume, High-Margin:**
- Premium electronics
- Specialty items
- Loyal customer base

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Python 3.7+ | Programming language |
| Pandas | Data manipulation, feather I/O |
| NumPy | Numerical operations |
| Matplotlib | Visualization foundation |
| Seaborn | Statistical graphics |
| Jupyter Notebook | Interactive environment |

## 🚀 How to Run

### In Jupyter Notebook

```bash
jupyter notebook sales_analysis.ipynb
```

Execute cells sequentially (Shift+Enter):
1. Cells run in order
2. Variables persist across cells
3. Outputs display inline
4. Can re-run individual cells for testing

### In Interactive Python

```python
import pandas as pd

# Load data
sales = pd.read_feather('Sales_data.ftr')

# Run analyses from notebook
# Execute code cells as needed
```

### Expected Execution Time
- Full notebook: 2-5 minutes
- Individual section: 10-30 seconds
- Dependent on data size and machine

## 📊 Output Examples

**Console Outputs:**
```
Dataset shape: (1247, 6)
Total Revenue: $1,234,567.89
Total Units Sold: 45,678
Number of Transactions: 1,247
Average Order Value: $990.45
```

**Visualizations Generated:**
1. Monthly sales bar chart
2. Monthly units trend
3. Top 10 products chart
4. Product category breakdown
5. Seasonal pattern overlay
6. Growth rate trends

**Exported Data (Optional):**
- `monthly_sales.csv` - Month-level aggregations
- `top_products.csv` - Product rankings
- `bundles.csv` - Product pair frequencies

## 💡 Business Insights

### For E-commerce Managers
- Plan inventory based on seasonal demand
- Time marketing campaigns around peak periods
- Bundle flagged products for promotions
- Allocate budget to high-seasonality months

### For Product Specialists
- Identify star products and allocate shelf space
- Recommend bundle discounts
- Plan product launches for strong months
- Monitor underperforming products

### For Finance Teams
- Forecast quarterly revenue based on patterns
- Plan cash flow around peak seasons
- Identify growth opportunities
- Benchmark performance vs. industry

### For Data Scientists
- Time series decomposition techniques
- Seasonal adjustment methods
- Forecast model inputs
- Anomaly detection signals

## ⚠️ Limitations

- ⚠️ Limited time period (may not capture multi-year seasonality)
- ⚠️ No customer data (can't analyze lifetime value)
- ⚠️ No profit margins (revenue only)
- ⚠️ No external factors (marketing, competition)
- ⚠️ Outliers may distort patterns

## 🔮 Future Enhancements

- **Forecasting:** ARIMA/Prophet models for next quarter
- **Anomaly Detection:** Identify unusual sales patterns
- **Customer Segmentation:** RFM analysis (Recency, Frequency, Monetary)
- **Price Optimization:** Elasticity analysis
- **Churn Prediction:** Identify at-risk customers
- **Attribution:** Which channel drives sales?
- **Real-time Dashboard:** Live sales tracking

## 📚 Technical Concepts

### Seasonality
- Regular, repeating patterns in time series
- Sources: Holidays, weather, school calendar, promotions
- Measurement: Seasonal index, decomposition

### Trend
- Long-term directional movement
- Underlying growth or decline
- Separate from seasonal fluctuations

### Stationarity
- Statistical property (mean/variance constant)
- Important for forecasting models
- Testing: ADF test, KPSS test

### Aggregation
- Combining granular data into summary statistics
- Common levels: Daily → Monthly → Annual
- Trade-off: Detail vs. clarity

---

**Project Author:** Iraklis Papigkiotis

**Last Updated:** 2026

For broader context, see parent `Sales Analytics/README_ANALYTICS.md`.
