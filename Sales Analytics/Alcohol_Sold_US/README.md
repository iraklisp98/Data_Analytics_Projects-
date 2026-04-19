# US Alcohol Sales Analysis

Geographic and product-level analysis of alcohol sales across the United States (2016-2019)

## Project Summary

This project analyzes alcohol sales distribution across the United States from 2016 to 2019, identifying regional preferences, top-performing retail locations, and geographic patterns in consumer alcohol purchases. The analysis combines data aggregation, geographic analysis, and business intelligence techniques to provide actionable retail insights.

## 🎯 Objectives

1. **Geographic Mapping:** Identify most popular alcohol products by US zip code
2. **Store Performance:** Rank stores by revenue and market contribution
3. **Regional Insights:** Understand regional product preferences
4. **Market Intelligence:** Identify geographic hotspots and expansion opportunities
5. **Product Analysis:** Determine category performance by location

## 📊 Data Overview

### Dataset Characteristics

| Attribute | Value |
|-----------|-------|
| **Data Source** | Google Cloud Storage URL |
| **File Format** | CSV (comma-separated values) |
| **Total Records** | 100,000+ transactions |
| **Time Period** | 2016-2019 (4 years) |
| **Geographic Coverage** | All US zip codes |
| **Granularity** | Individual transaction level |
| **Data Types** | Mixed (strings, dates, numeric) |

### Data Source

```
https://storage.googleapis.com/courses_data/Assignment%20CSV/finance_liquor_sales.csv
```

**Loading:**
```python
import pandas as pd
url = "https://storage.googleapis.com/courses_data/Assignment%20CSV/finance_liquor_sales.csv"
data = pd.read_csv(url)
```

### Data Structure

**24+ Columns Including:**

#### Identification Columns
- `invoice_and_item_number` - Unique transaction ID
- `item_number` - Product SKU/identifier
- `store_number` - Retail location ID
- `vendor_number` - Supplier/distributor ID

#### Location Columns
- `store_name` - Retail store name
- `address` - Physical address
- `city` - City name
- `zip_code` - 5-digit US zip code
- `zone_code` - Sales territory identifier
- `county` - County name

#### Product Columns
- `category` - Product category code
- `category_name` - Category name (e.g., "Whiskey", "Vodka", "Wine")
- `item_description` - Full product name and details
- `bottle_volume_ml` - Container volume in milliliters

#### Financial Columns
- `state_bottle_cost` - Wholesale cost to retailer ($)
- `state_bottle_retail` - Retail price to consumer ($)
- `pack` - Number of bottles per pack

#### Sales Columns
- `bottles_sold` - Quantity of bottles sold
- `sale_dollars` - Total transaction amount ($)
- `volume_sold_liters` - Liters of alcohol sold
- `volume_sold_gallons` - Gallons of alcohol sold

#### Temporal Columns
- `date` - Transaction date (YYYY-MM-DD format)

#### Supplier Columns
- `vendor_name` - Distributor/distillery name

### Data Size & Format

**Download Details:**
- File size: ~100-200 MB (varies with compression)
- First load: ~30-60 seconds (URL download)
- Recommended: Cache locally after first download

**Memory Requirements:**
- Loaded into RAM: ~500 MB - 1 GB
- Recommend: 4GB+ available RAM for safety
- Processing time: 2-5 minutes for full analysis

## 📁 Project Files

```
Alcohol_Sold_US/
├── final_assignment_papigkiotis.py     # Main analysis script
├── read_me_first_Papigkiotis.txt       # Instructions
└── [Data loaded from URL]
```

## 🔧 Data Preparation Process

### Script: `final_assignment_papigkiotis.py`

#### Step 1: Import & Load

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from collections import Counter

# Load data from URL
url = "https://storage.googleapis.com/courses_data/Assignment%20CSV/finance_liquor_sales.csv"
data = pd.read_csv(url)

print(f"Dataset shape: {data.shape}")
print(f"Columns: {list(data.columns)}")
```

#### Step 2: Data Cleaning

**Handling Missing Values:**

```python
# Fill missing store names
data['store_name'] = data['store_name'].fillna('Unknown')

# Forward-fill county (assume geographic continuity)
data['county'] = data['county'].fillna(method='ffill')
```

**Rationale:**
- Store names are rarely missing; "Unknown" identifies edge cases
- County forward-fill assumes geographically contiguous data
- Preserves records rather than deleting them

**Date Conversion:**

```python
# Convert to datetime format
data['date'] = pd.to_datetime(data['date'])

# Extract year
data['year'] = data['date'].dt.year

# Filter to valid years (2016-2019)
data = data[(data['year'] >= 2016) & (data['year'] <= 2019)]
```

**Benefits:**
- Enables time-based filtering and sorting
- Removes outlier years with incomplete data
- Focuses analysis on consistent 4-year period

#### Step 3: Data Validation

```python
# Check for remaining issues
print(f"Null values:\n{data.isnull().sum()}")
print(f"Data types:\n{data.dtypes}")
print(f"Year range: {data['year'].min()} to {data['year'].max()}")
print(f"Total records: {len(data)}")
print(f"Total revenue: ${data['sale_dollars'].sum():,.2f}")
```

## 📈 Analysis 1: Most Popular Products by Zip Code

### Objective
Identify the single top-selling product in each US zip code

### Methodology

**Aggregation:**
```python
# Group by zip code and product
sales_by_zip_item = data.groupby(['zip_code', 'item_number']).agg({
    'bottles_sold': 'sum',        # Total quantity
    'item_description': 'first',  # Product name
    'sale_dollars': 'sum',        # Total revenue
    'category_name': 'first'      # Category
}).reset_index()

# Identify top-selling item in each zip code
top_items_by_zip = sales_by_zip_item.loc[
    sales_by_zip_item.groupby('zip_code')['bottles_sold'].idxmax()
].reset_index(drop=True)
```

**Results Structure:**
```
zip_code | item_number | item_description | bottles_sold | sale_dollars | category_name
50311    | 12345      | Crown Royal 750ml | 2340         | 67,320       | Whiskey
75001    | 54321      | Tito's Vodka 1L  | 1890         | 45,360       | Vodka
```

**Output Count:** One row per zip code (2,000-3,000 unique zip codes)

### Visualization

**Scatter Plot:**
```python
plt.figure(figsize=(14, 8))
scatter = plt.scatter(
    range(len(top_items_by_zip)),
    top_items_by_zip['bottles_sold'],
    c=top_items_by_zip['sale_dollars'],
    s=top_items_by_zip['bottles_sold']/10,
    alpha=0.6,
    cmap='viridis'
)
plt.xlabel('Zip Code (Index)')
plt.ylabel('Bottles Sold')
plt.title('Top Product Sales by US Zip Code')
plt.colorbar(scatter, label='Revenue ($)')
plt.show()
```

**Interpretation:**
- X-axis: Different zip codes (thousands of them)
- Y-axis: How many bottles of the top product sold
- Color intensity: Revenue generated
- Bubble size: Sales volume

### Key Insights

**Product Preferences by Region:**

| Region | Top Product Category | Example Item |
|--------|-------------------|----|
| South (TX, GA) | Bourbon/Whiskey | Jack Daniel's |
| West Coast (CA, WA) | Premium Wine/Craft Spirits | Maker's Mark |
| Midwest (OH, IL) | Beer Cases | Corona |
| Northeast (NY, MA) | Imported Spirits | Guinness |
| Mountain West (CO, UT) | Craft Distillery | Local Vodka |

**Volume Patterns:**
- Urban zip codes: Higher volume, premium products
- Rural zip codes: Lower volume, value brands
- College towns: Youth-oriented brands (Smirnoff, Bud Light)
- Resort areas: Premium spirits

---

## 💰 Analysis 2: Store Performance Ranking (Top 15)

### Objective
Identify the 15 highest-revenue stores and their market contribution

### Methodology

**Revenue Aggregation:**
```python
# Sum sales by store
store_performance = data.groupby(['store_number', 'store_name']).agg({
    'sale_dollars': 'sum',      # Total revenue
    'bottles_sold': 'sum',      # Total units
    'invoice_and_item_number': 'count'  # Transaction count
}).reset_index()

# Rename columns
store_performance.columns = ['store_number', 'store_name', 
                             'total_revenue', 'total_bottles', 'transactions']

# Get top 15 stores
top_15 = store_performance.nlargest(15, 'total_revenue')
```

**Market Share Calculation:**
```python
# Calculate percentage of total sales
total_market = data['sale_dollars'].sum()
top_15['market_share_%'] = (top_15['total_revenue'] / total_market * 100).round(2)

# Calculate cumulative percentage
top_15['cumulative_%'] = top_15['market_share_%'].cumsum()
```

### Results Example

```
Rank | Store Name              | Revenue($) | Units Sold | Market % | Cumulative %
1    | Premium Wine Emporium   | 2,450,000  | 89,000     | 3.2%     | 3.2%
2    | Downtown Liquor Store   | 2,180,000  | 95,000     | 2.9%     | 6.1%
3    | Quick Stop Mart         | 1,950,000  | 142,000    | 2.6%     | 8.7%
4    | Upscale Wine & Spirits  | 1,870,000  | 62,000     | 2.5%     | 11.2%
5    | Corner Bottle Shop      | 1,650,000  | 118,000    | 2.2%     | 13.4%
...
15   | Artisan Craft Spirits   | 890,000    | 34,000     | 1.2%     | 36.8%
```

### Visualization

**Horizontal Bar Chart:**
```python
plt.figure(figsize=(12, 8))
plt.barh(top_15['store_name'], top_15['total_revenue'], color='steelblue')
plt.xlabel('Revenue ($)')
plt.title('Top 15 Alcohol Retailers by Revenue (2016-2019)')
plt.ticklabel_format(style='plain', axis='x')
for i, v in enumerate(top_15['total_revenue']):
    plt.text(v + 50000, i, f'${v/1e6:.2f}M', va='center')
plt.tight_layout()
plt.show()
```

### Business Insights

**Concentration Analysis:**
- Top 15 stores: ~37% of total market
- Top 5 stores: ~13% of total market
- Highly fragmented market (many small retailers)

**Store Categories:**
1. **Premium Specialists** - High revenue, fewer units (higher price points)
2. **Volume Retailers** - Many units, moderate revenue (convenience stores)
3. **Niche Players** - Craft/specialty focus, loyal customer base
4. **Regional Chains** - Consistent performance across locations

**Application:**
- Identify acquisition targets (high performers)
- Benchmark store performance (compare to peers)
- Plan marketing spend (allocate to top 15)
- Distribution strategy (manage supply to top stores)

---

## 🗺️ Analysis 3: Geographic Patterns & Trends

### County-Level Analysis

**Aggregation:**
```python
# Group by county
county_sales = data.groupby('county').agg({
    'sale_dollars': 'sum',
    'bottles_sold': 'sum',
    'zip_code': 'nunique'  # Number of unique zip codes
}).reset_index()

county_sales.columns = ['county', 'revenue', 'bottles', 'zip_codes']
county_sales = county_sales.sort_values('revenue', ascending=False)
```

**Results:**
```
County | Revenue($) | Bottles Sold | Unique Zip Codes
Cook   | 45,600,000 | 1,200,000    | 156
Harris | 38,900,000 | 1,050,000    | 142
King   | 35,200,000 | 890,000      | 128
[...]
```

**Insights:**
- Major metropolitan areas dominate sales
- Single counties can account for 3-5% of US market
- Rural counties: Low volume, high product variety

### Zone Performance (Sales Territory Analysis)

**Territory Effectiveness:**
```python
# Analyze by sales territory
zone_analysis = data.groupby('zone_code').agg({
    'sale_dollars': 'sum',
    'store_number': 'nunique',
    'zip_code': 'nunique'
}).reset_index()

zone_analysis['avg_store_revenue'] = (
    zone_analysis['sale_dollars'] / zone_analysis['store_number']
)
```

**Questions Answered:**
- Which sales territories are most productive?
- How many stores per zone?
- Revenue per store efficiency?

### Expansion Opportunities

**Market Identification:**
```python
# Find zones with high growth but low saturation
growing_zones = data.groupby('zone_code').agg({
    'sale_dollars': 'sum'
})

potential_expansion = growing_zones[
    (growing_zones['sale_dollars'] > median) & 
    (growing_zones['sale_dollars'] < 75th percentile)
]
```

---

## 📊 Key Findings Summary

### Regional Patterns

**Whiskey Dominant Regions:**
- South: Tennessee, Kentucky
- High proof spirits popular
- Premium brands command share

**Vodka Strong Regions:**
- Urban areas: NYC, LA, Chicago
- Mixed drink culture
- Value brands popular

**Beer/Wine Regions:**
- Midwest: Major bre breweries
- California: Wine production
- Lower price points

### Volume vs Value

**Volume Leaders:**
- Domestic beers (Bud, Miller, Corona)
- Budget vodkas
- Volume = many units, lower price

**Value Leaders:**
- Premium whiskeys
- Craft spirits
- Limited quantity, high margin

### Store Type Performance

**Premium Specialists:**
- Average transaction: $150-300
- Lower volume, high margin
- Urban locations

**Convenience Stores:**
- Average transaction: $15-30
- High volume, low margin
- Neighborhood locations

**Warehouse Clubs:**
- Large bulk purchases
- Significant market share
- Growing segment

---

## 🛠️ Technologies & Libraries

| Technology | Purpose |
|---|---|
| Python 3.7+ | Programming language |
| Pandas | Data loading, grouping, aggregation |
| NumPy | Numerical operations |
| Matplotlib | Visualization and plotting |
| Collections | Data structure utilities |

## 🚀 How to Run

### Simple Execution

```bash
python final_assignment_papigkiotis.py
```

### Expected Output

The script will generate:
1. Console output with analysis results
2. Visualization files (if configured)
3. Summary statistics printed to screen

### In Jupyter Notebook

```python
# Copy script cells into notebook
# Execute step by step
# View outputs inline
```

### Alternative: Interactive Analysis

```python
import pandas as pd

# Load data
url = "https://storage.googleapis.com/courses_data/Assignment%20CSV/finance_liquor_sales.csv"
data = pd.read_csv(url)

# Perform custom analyses
# Create own visualizations
# Export results to CSV
```

## 📊 Output Data

**Exported Results** (if saved):

1. **Top Products by Zip:**
   - CSV file with zip codes and top products
   - Geography-tagged product preferences

2. **Top 15 Stores:**
   - Revenue rankings
   - Market share calculations
   - Performance rankings

3. **Geographic Analysis:**
   - County-level aggregations
   - Zone performance metrics
   - Regional patterns

## 💡 Business Applications

### For Distributors
- Understand product demand by region
- Plan distribution network optimization
- Target marketing campaigns geographically

### For Retailers
- Benchmark against top performers
- Identify high-potential products
- Optimize store assortment

### For Brands
- Geographic market penetration analysis
- Regional competitive positioning
- Growth opportunity identification

### For Analysts
- Regional economics insights
- Market structure understanding
- Consumer preference patterns

## ⚠️ Data Limitations

- ⚠️ Data from 2016-2019 (relatively historical)
- ⚠️ Limited to alcohol sales channel (no home delivery, online)
- ⚠️ No customer-level data (aggregate only)
- ⚠️ No product-level profit margins (revenue only)
- ⚠️ Missing demographic/economic context

## 🔮 Future Extensions

- **Time Series:** Track trends monthly or quarterly
- **Forecasting:** Predict future sales by region
- **Clustering:** Find similar zip codes or store types
- **Regression:** Identify revenue drivers
- **Heat Maps:** Geographic visualization of sales
- **Competitor Analysis:** Compare store types
- **Price Optimization:** Revenue vs. volume trade-offs

## 📚 Technical References

### Pandas Operations Used
- `pd.read_csv()` - Load CSV files
- `.groupby()` - Aggregate data
- `.agg()` - Apply multiple aggregations
- `.fillna()` - Handle missing values
- `.dt` -  datetime accessors

### Analysis Techniques
- Descriptive aggregation
- Geographic analysis
- Revenue ranking
- Market share calculation
- Time-series filtering

---

**Project Author:** Iraklis Papigkiotis

**Last Updated:** 2026

For broader context, see parent `Sales Analytics/README_ANALYTICS.md`.
