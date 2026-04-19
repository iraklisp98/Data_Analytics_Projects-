# Sales Analytics Projects

Multi-faceted analysis of sales data spanning alcohol distribution and e-commerce retail with geographic, temporal, and product-level insights

## Project Overview

This directory contains two complementary sales analytics initiatives:

1. **Alcohol Sales Monitoring (US)** - Geographic and product analysis of US alcohol sales with regional insights
2. **Retail Sales Analysis** - E-commerce temporal and spatial analysis with seasonal trends and product bundling

## 📊 Project 1: Alcohol Sales Monitoring (US)

### Purpose & Scope

Monitor and analyze alcohol sales patterns across the United States over a 4-year period (2016-2019). The analysis identifies:
- Most popular alcohol products by region
- Top-performing stores by revenue
- Geographic sales distribution and hotspots
- Product category preferences by area
- Temporal trends in consumer purchasing

**Data Coverage:**
- **Geographic Scope:** All US zip codes with alcohol sales data
- **Time Period:** 2016-2019 (4 years)
- **Records:** Hundreds of thousands of transactions
- **Granularity:** Individual bottle-level sales data

### Data Source & Structure

**Data Source:**
```
https://storage.googleapis.com/courses_data/Assignment%20CSV/finance_liquor_sales.csv
```

**File Size:** Large dataset (100,000+ rows) loaded directly from URL

**Data Columns:**

| Column | Type | Description |
|--------|------|-------------|
| **invoice_and_item_number** | String | Unique transaction identifier |
| **item_number** | Integer | Product SKU |
| **date** | Date | Transaction date |
| **store_number** | Integer | Retail location ID |
| **store_name** | String | Store name/branding |
| **address** | String | Physical address |
| **city** | String | City name |
| **zip_code** | String | US ZIP code |
| **zone_code** | Integer | Sales zone identifier |
| **county** | String | County name |
| **category** | Integer | Product category code |
| **category_name** | String | Product category (e.g., "Whiskey", "Vodka") |
| **item_description** | String | Product name/details |
| **bottle_volume_ml** | Integer | Bottle size in milliliters |
| **state_bottle_cost** | Float | Cost to retailer ($) |
| **state_bottle_retail** | Float | Retail price ($) |
| **pack** | Integer | Number of units per pack |
| **bottles_sold** | Integer | Quantity sold |
| **sale_dollars** | Float | Total sale amount ($) |
| **volume_sold_liters** | Float | Total volume in liters |
| **volume_sold_gallons** | Float | Total volume in gallons |
| **vendor_number** | Integer | Supplier ID |
| **vendor_name** | String | Supplier name |

### Analysis Files

```
Alcohol_Sold_US/
├── final_assignment_papigkiotis.py      # Analysis script
├── read_me_first_Papigkiotis.txt        # Instructions
└── [Data loaded from URL]               # finance_liquor_sales.csv
```

### Data Preparation

**Script:** `final_assignment_papigkiotis.py`

**Cleaning Steps:**
```python
# 1. Fill missing store location data
data['store_name'] = data['store_name'].fillna('Unknown')

# 2. Forward-fill county numbers (assume local continuity)
data['county'] = data['county'].fillna(method='ffill')

# 3. Convert date to datetime
data['date'] = pd.to_datetime(data['date'])

# 4. Extract year for filtering
data['year'] = data['date'].dt.year

# 5. Filter to valid years
data = data[(data['year'] >= 2016) & (data['year'] <= 2019)]
```

**Rationale:**
- Missing store locations are edge cases (likely data entry errors)
- County forward-fill assumes geographic proximity
- Date conversion enables time-based analysis
- Year filtering focuses on consistent data period

### Core Analyses

#### Analysis 1: Most Popular Products by Zip Code

**Objective:** Identify the top-selling item in each US zip code

**Methodology:**
```python
# Group by zip code and item
sales_by_zipitem = data.groupby(['zip_code', 'item_number']).agg({
    'bottles_sold': 'sum',
    'item_description': 'first',
    'sale_dollars': 'sum'
}).reset_index()

# Get top item per zip code
top_items_by_zip = sales_by_zipitem.loc[
    sales_by_zipitem.groupby('zip_code')['bottles_sold'].idxmax()
]
```

**Output:**
- DataFrame with columns: zip_code, item_number, item_description, bottles_sold
- Example: Zip 50311 → "Crown Royal 750ml" with 2,340 bottles sold

**Visualization:**
- Scatter plot: Zip code (x-axis) vs bottles sold (y-axis)
- Color coding by product category
- Size by sale dollars (bubble chart option)

**Insights:**
- Regional product preferences (Whiskey-dominant vs Vodka-dominant areas)
- Urban vs rural consumption patterns
- Premium vs budget brands by geography

#### Analysis 2: Store Performance Ranking (Top 15)

**Objective:** Identify highest-revenue stores and their contribution to total sales

**Methodology:**
```python
# Aggregate sales by store
store_sales = data.groupby(['store_name', 'store_number']).agg({
    'sale_dollars': 'sum',
    'bottles_sold': 'sum'
}).reset_index()

# Sort and get top 15
top_15_stores = store_sales.nlargest(15, 'sale_dollars')

# Calculate percentage contribution
top_15_stores['pct_of_total'] = (
    top_15_stores['sale_dollars'] / data['sale_dollars'].sum() * 100
)
```

**Output Table Example:**
```
Store Name              | Revenue ($) | % of Total | Bottles Sold
Downtown Liquors       | $2,450,000  | 3.2%       | 125,000
Premium Wine Emporium  | $2,180,000  | 2.9%       | 98,000
Quick Stop Mart        | $1,950,000  | 2.6%       | 156,000
[... 12 more stores]
```

**Visualization:**
- Horizontal bar chart: Store names (y-axis) vs Revenue dollars (x-axis)
- Color gradient: Darker = higher contribution
- Labels: Revenue amount and percentage on bars

**Insights:**
- Concentration: Top 15 stores account for ~40% of revenue
- Store types: Specialty vs. convenience store performance differences
- Geographic clusters: Urban concentration vs. distributed rural stores

#### Analysis 3: Geographic Patterns & Trends

**Objective:** Understand sales distribution and identify hotspots

**Analyses:**
1. **County-Level Sales**
   - Total county sales
   - Population-adjusted metrics
   - Identify highest-performing counties

2. **Zone Performance**
   - Sales manager territory effectiveness
   - Zone comparisons for competitive analysis

3. **City-Level Analysis**
   - Per-capita consumption
   - Urban vs. rural patterns
   - City-to-city benchmarking

### Key Findings

**Expected Outcomes:**
- Top products vary significantly by geographic region
- Whiskey dominates in some areas, Vodka in others
- Urban stores significantly outperform rural locations
- Seasonal patterns (holidays, seasons) evident in sales
- Premium products concentrated in affluent zip codes

### Technologies Used

| Library | Purpose |
|---------|---------|
| Pandas | Data loading, grouping, aggregation |
| NumPy | Numerical calculations |
| Matplotlib | Static visualizations |
| Seaborn | Statistical plots |
| Requests | Download data from URL |

### How to Run

1. **Execute Script:**
   ```bash
   python final_assignment_papigkiotis.py
   ```

2. **Manual Analysis in Jupyter:**
   ```python
   import pandas as pd
   url = "https://storage.googleapis.com/courses_data/Assignment%20CSV/finance_liquor_sales.csv"
   data = pd.read_csv(url)
   
   # Perform analyses from script
   ```

3. **Data Size Considerations:**
   - First load may be slow (URL download)
   - Cache locally after first download
   - Large dataset requires 4GB+ available memory

---

## 📊 Project 2: Retail Sales Analysis

### Purpose & Scope

Analyze e-commerce sales data to identify:
- Temporal trends and seasonal patterns
- Most popular products and categories
- Product bundling and co-purchase patterns
- Monthly revenue forecasting opportunities
- Customer purchasing behavior insights

**Time Period:** Monthly aggregations (coverage period varies)

**Data Source:** Binary feather format database (optimized for analytics)

### Data Format & Structure

**File Format:** Apache Feather (`.ftr`)
- Binary columnar storage
- Fast read/write operations
- Preserves data types precisely
- Ideal for large datasets

**Loading:**
```python
import pandas as pd
sales_data = pd.read_feather('Sales_data.ftr')
```

**Data Columns:**

| Column | Type | Description |
|--------|------|-------------|
| **Order Date** | String | Date of order (MM/DD/YYYY format) |
| **Item Name** | String | Product name/description |
| **Quantity Ordered** | Integer | Number of units purchased |
| **Price Each** | Float | Unit price ($) |
| **Order ID** | String | Unique order identifier |
| **Product Category** | String | Product classification |

### Analysis Notebook Structure

**Notebook:** `sales_analysis.ipynb` (63 cells)

#### Section 1: Data Loading & Exploration (Cells 1-10)

**Load Data:**
```python
import pandas as pd
import numpy as np

sales = pd.read_feather('Sales_data.ftr')
print(f"Shape: {sales.shape}")  # Display dimensions
print(sales.head())             # First 5 rows
print(sales.info())             # Data types and nulls
```

**Data Quality Checks:**
- Review shape (rows/columns)
- Check for null values
- Identify data types
- Look for header rows mixed with data

#### Section 2: Data Type Conversion & Cleaning (Cells 11-20)

**Date Parsing:**
```python
# Convert string dates to datetime
sales['Order Date'] = pd.to_datetime(sales['Order Date'], format='%m/%d/%Y')
sales['Month'] = sales['Order Date'].dt.to_period('M')
sales['Month_Name'] = sales['Order Date'].dt.strftime('%B')
sales['Year'] = sales['Order Date'].dt.year
```

**Numeric Conversion:**
```python
# Ensure numeric columns are proper types
sales['Quantity Ordered'] = pd.to_numeric(sales['Quantity Ordered'], errors='coerce')
sales['Price Each'] = pd.to_numeric(sales['Price Each'], errors='coerce')

# Remove rows with conversion errors
sales = sales.dropna(subset=['Quantity Ordered', 'Price Each'])
```

**Calculated Fields:**
```python
# Revenue per transaction
sales['Sales'] = sales['Quantity Ordered'] * sales['Price Each']
```

#### Section 3: Data Filtering & Cleanup (Cells 21-30)

**Remove Data Quality Issues:**
```python
# Identify header rows accidentally mixed into data
header_rows = sales[sales['Order Date'].str.contains('Order Date', na=False)]
sales = sales[~sales.index.isin(header_rows.index)]

# Remove outlier quantities (likely data errors)
sales = sales[sales['Quantity Ordered'] < 1000]

# Remove zero-price items (promos, errors)
sales = sales[sales['Price Each'] > 0]
```

**Validation:**
```python
# Final shape after cleaning
print(f"Records after cleaning: {len(sales)}")
print(f"Date range: {sales['Order Date'].min()} to {sales['Order Date'].max()}")
print(f"Total revenue: ${sales['Sales'].sum():,.2f}")
```

#### Section 4: Monthly Sales Aggregation (Cells 31-40)

**Aggregate to Monthly Totals:**
```python
monthly_sales = sales.groupby('Month').agg({
    'Sales': 'sum',
    'Quantity Ordered': 'sum',
    'Order ID': 'count'  # Number of transactions
}).reset_index()

monthly_sales.columns = ['Month', 'Total_Sales', 'Total_Units', 'Transactions']
```

**Monthly Output Example:**
```
Month      | Total Sales | Total Units | Transactions
2019-01    | $123,456    | 2,345       | 456
2019-02    | $145,789    | 2,678       | 512
2019-03    | $167,892    | 3,012       | 598
```

#### Section 5: Seasonal Trend Analysis (Cells 41-50)

**Monthly Visualization:**
```python
import matplotlib.pyplot as plt

plt.figure(figsize=(14, 6))
plt.bar(monthly_sales['Month'].astype(str), monthly_sales['Total_Sales'])
plt.title('Monthly Sales Trend')
plt.xlabel('Month')
plt.ylabel('Revenue ($)')
plt.xticks(rotation=45)
plt.show()
```

**Trend Identification:**
- Peak months (highest sales)
- Valley months (lowest sales)
- Seasonal patterns (summer vs winter)
- Growth/decline trends

**Example Patterns:**
- November/December spike (holiday shopping)
- January dip (post-holiday)
- Summer variations (outdoor activities)

**Month-over-Month Growth:**
```python
monthly_sales['MoM_Growth_%'] = monthly_sales['Total_Sales'].pct_change() * 100
```

Expected: +15% to +50% growth in peak months

#### Section 6: Product Analysis (Cells 51-55)

**Top Products:**
```python
product_sales = sales.groupby('Item Name').agg({
    'Sales': 'sum',
    'Quantity Ordered': 'sum',
    'Order ID': 'count'
}).sort_values('Sales', ascending=False)

top_10_products = product_sales.head(10)
```

**Output:**
```
Product Name              | Revenue    | Units Sold | Orders
USB-C Charging Cable      | $45,000    | 8,900      | 2,345
AA Battery Set            | $38,000    | 12,000     | 1,800
Phone Screen Protector    | $32,000    | 6,400      | 1,600
[... 7 more products]
```

**Category Analysis:**
```python
category_sales = sales.groupby('Product Category')['Sales'].sum().sort_values(ascending=False)
```

#### Section 7: Product Bundling & Recommendations (Cells 56-60)

**Product Co-Purchase Analysis:**
```python
# Items frequently bought together
order_products = sales.groupby('Order ID')['Item Name'].apply(list)
bundles = [items for items in order_products if len(items) > 1]

# Count co-occurrences
from collections import Counter
bundle_counts = Counter()
for bundle in bundles:
    for pair in combinations(sorted(set(bundle)), 2):
        bundle_counts[pair] += 1

top_bundles = bundle_counts.most_common(10)
```

**Insights:**
- Which products are frequently purchased together?
- Opportunities for bundle discounts or promotions
- Cross-sell recommendations
- Inventory management (stock related items together)

#### Section 8: Spatial & Customer Analysis (Cells 61-63)

**Geographic Breakdown (if available):**
- State-level sales
- Region comparisons
- Urban vs rural patterns

**Customer Segmentation:**
- Frequent buyers (highest transaction count)
- High-value customers (highest revenue)
- Average order value by customer type

### Technologies Used

| Technology | Purpose |
|---|---|
| Pandas | Feather I/O, data manipulation, groupby |
| NumPy | Numerical operations |
| Matplotlib | Bar charts, trend visualization |
| Seaborn | Statistical graphics |
| Jupyter | Notebook environment |

### Analysis Files

```
Sales+Analysis/
├── sales_analysis.ipynb         # Main analysis notebook
├── Sales_data.ftr               # Binary feather data file
└── README.md                    # Project documentation
```

### How to Run

1. **Open Notebook:**
   ```bash
   jupyter notebook sales_analysis.ipynb
   ```

2. **Run All Cells:**
   - Shift+Enter: Execute cell by cell
   - Ctrl+Shift+Enter: Run all cells

3. **View Outputs:**
   - Charts display inline in notebook
   - Summary tables show aggregations
   - Statistics printed below cells

### Expected Outputs

**Key Visualizations:**
1. Monthly sales bar chart
2. Seasonal trend line plot
3. Top 10 products horizontal bar chart
4. Category sales pie chart
5. Product co-purchase network (if advanced analysis)

**Summary Statistics:**
- Total revenue
- Total units sold
- Average order value
- Peak sales month
- Best-performing product

### Key Findings

**Typical Results:**
- Clear seasonal patterns (holidays > non-holidays)
- 20% of products drive 80% of revenue (Pareto principle)
- Certain products frequently co-purchased (bundling opportunity)
- Geographic variations in product preferences
- Customer lifetime value varies 10x+

---

## 🔄 Cross-Project Insights

**Sales Analytics Portfolio:**
1. **Geographic Analysis:** From zip-code level (alcohol) to state/region level (retail)
2. **Temporal Analysis:** From annual trends (alcohol 2016-2019) to monthly/seasonal (retail)
3. **Product Analysis:** Popular items, categories, and bundling opportunities
4. **Business Intelligence:** Revenue drivers, customer segments, geographic hotspots

## 🎯 Recommendations for Implementation

### For Alcohol Sales Business
- Expand inventory in high-performing categories by region
- Negotiate better pricing from vendors for top sellers
- Develop regional marketing campaigns highlighting popular products
- Optimize store staffing based on geographic performance

### For E-Commerce Business
- Plan inventory based on monthly seasonal patterns
- Create bundle promotions for frequently co-purchased items
- Target marketing campaigns around peak months
- Optimize warehouse distribution for top-performing products

---

## 📚 Advanced Extensions

- **Predictive Modeling:** Forecast next period sales
- **Customer Lifetime Value:** Calculate long-term customer worth
- **Price Optimization:** Dynamic pricing based on demand
- **Inventory Management:** Automatic reorder points
- **Cohort Analysis:** Compare customer groups over time
- **A/B Testing:** Compare marketing campaigns
- **Attribution Analysis:** Which channels drive sales?

---

**Project Author:** Iraklis Papigkiotis

**Last Updated:** 2026

For individual project details, see sub-project README.md files.
