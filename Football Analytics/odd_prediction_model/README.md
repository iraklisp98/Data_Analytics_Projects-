# MLB Odds Prediction Models Comparison

Comparative evaluation and profitability analysis of two predictive models for Major League Baseball game outcomes (2020-2021)

## Project Overview

This sub-project conducts a rigorous financial analysis of two predictive betting models for MLB games. Rather than merely evaluating prediction accuracy, the analysis focuses on **practical profitability** - calculating real returns on investment assuming $1 bets placed according to each model's predictions.

## 🎯 Objectives

1. **Evaluate Model Predictions:** Compare how accurately each model predicts MLB game outcomes
2. **Convert Odds Formats:** Transform American betting odds to European decimal format
3. **Calculate Profitability:** Determine cumulative profit/loss and ROI for each model
4. **Identify Superior Model:** Recommend the better-performing model for real-world deployment
5. **Analyze Consistency:** Track performance across multiple seasons (2020-2021)

## ⚾ Project Context

### Why Compare Betting Models?

In the era of advanced analytics, ML models predict sports outcomes with increasing accuracy. However, **prediction accuracy ≠ profitability**:

- A 52% accurate model can be highly profitable with proper odds
- A 58% accurate model can lose money with unfavorable odds
- ROI calculation incorporates both accuracy AND odds value

This project bridges that critical gap through financial analysis.

## 📊 Data Overview

### Datasets

| File | Season | Records | Data Source |
|------|--------|---------|-------------|
| `mlb-odds-2020.csv` | 2020 (Shortened) | ~160 games | MLB official data |
| `mlb-odds-2021.csv` | 2021 (Full) | ~162 games | MLB official data |

### Data Time Period
- **Years:** 2020 and 2021 MLB seasons
- **Note:** 2020 was shortened due to COVID-19 (60 games)
- **2021:** Full regular season (162 games)
- **Total Coverage:** ~322 games across both seasons

### Data Structure

**CSV Columns:**

| Column | Description | Format | Example |
|--------|-------------|--------|---------|
| **Date** | Game date | YYYY-MM-DD | 2020-07-23 |
| **Home Team** | Home team identifier | String | NYY |
| **Away Team** | Away team identifier | String | BOS |
| **Final** | Actual outcome | 0 or 1 | 1 (home win) |
| **Model 1** | Model 1's odds prediction | American odds | -110, +140 |
| **Model 2** | Model 2's odds prediction | American odds | -105, +135 |
| **Venue Info** | Stadium/location | String | Yankee Stadium |
| [Other metadata columns] | Game details | Various | - |

### Key Variables Explained

#### American Odds Format
- **Negative number** (e.g., -110): Favorite (requires bet to win $100)
  - Interpretation: Bet -110 to win $100
  - ROI calculation: $100 / 110 = 0.909

- **Positive number** (e.g., +140): Underdog (win for bet)
  - Interpretation: Bet $100 to win 140
  - ROI calculation: 140 / 100 = 1.40

#### Final (Outcome)
- **1** = Home team won
- **0** = Away team won (or could be inverted in actual data)
- **Purpose:** Ground truth for model evaluation

#### Model Predictions
- **Model 1 & Model 2:** Each provides American odds
- **Interpretation:** Higher odds = Model thinks outcome less likely
- **Conversion needed:** Transform to probability for comparison

## 📁 File Structure

```
odd_prediction_model/
├── odd_models_Papigkiotis.ipynb        # Main analysis notebook (28 cells)
├── mlb-odds-2020.csv                   # 2020 season data
├── mlb-odds-2021.csv                   # 2021 season data
└── read_me_first_Papigkiotis.txt       # Project instructions
```

## 📊 Analysis: Notebook Structure

### `odd_models_Papigkiotis.ipynb` (28 cells)

#### Section 1: Setup & Data Loading (Cells 1-3)

**Libraries Imported:**
```python
import pandas as pd          # Data manipulation
import numpy as np           # Numerical operations
import matplotlib.pyplot     # Visualization
import seaborn              # Statistical graphics
```

**Data Loading:**
```python
df_2020 = pd.read_csv('mlb-odds-2020.csv')
df_2021 = pd.read_csv('mlb-odds-2021.csv')
df_combined = pd.concat([df_2020, df_2021])  # Merge seasons
```

**Activity:**
- Load both season CSVs
- Display shape information (rows, columns)
- Show sample rows for data structure inspection

#### Section 2: Data Cleaning & Validation (Cells 4-7)

**Data Quality Checks:**
- Check for null/missing values
  ```
  Model 1 nulls: X%
  Model 2 nulls: X%
  Final outcomes: X%
  ```
- Verify data types
  - Odds: Numeric (int/float)
  - Dates: Datetime format
  - Teams: String/categorical
  
- Remove problematic rows
- Handle edge cases (missing outcomes, invalid odds)

**Data Type Conversion:**
```python
df['Date'] = pd.to_datetime(df['Date'])
df['Model_1_Odds'] = df['Model_1_Odds'].astype(float)
df['Model_2_Odds'] = df['Model_2_Odds'].astype(float)
df['Final'] = df['Final'].astype(int)
```

#### Section 3: American to European Odds Conversion (Cells 8-15)

**Conversion Formula:**

For **negative American odds** (favorites):
$$\text{Decimal Odds} = 1 + \frac{100}{|\text{American Odds}|}$$

Example: -110 → 1 + (100/110) = 1.909

For **positive American odds** (underdogs):
$$\text{Decimal Odds} = 1 + \frac{\text{American Odds}}{100}$$

Example: +140 → 1 + (140/100) = 2.40

**Implementation:**
```python
def american_to_decimal(american_odds):
    if american_odds < 0:
        return 1 + (100 / abs(american_odds))
    else:
        return 1 + (american_odds / 100)

df['Model_1_Decimal'] = df['Model_1_Odds'].apply(american_to_decimal)
df['Model_2_Decimal'] = df['Model_2_Odds'].apply(american_to_decimal)
```

**Purpose of Conversion:**
- Decimal odds directly indicate total return ($1 bet × decimal odds)
- European format easier for mathematical operations
- Standardizes comparison across different odds formats

**Verification:**
- Display converted odds samples
- Verify conversions: All decimal > 1.0?
- Check for conversion errors or outliers

#### Section 4: Model Prediction Evaluation (Cells 16-20)

**Prediction Accuracy Assessment:**

For each model:

1. **Extract Predictions**
   - Odds imply implied probability (inverse of odds)
   - Decimal Odds 2.0 → Implied probability 50%
   - Formula: Probability = 1 / Decimal Odds

2. **Create Binary Predictions**
   - If Model-implied probability > 50% → Predict outcome = 1
   - Else → Predict outcome = 0

3. **Compare to Actual Outcomes**
   - For each game: Predicted outcome vs Actual outcome
   - Count correct predictions (match) and incorrect (mismatch)

4. **Calculate Accuracy Metrics**
   ```
   Model 1:
   ├── Correct predictions: X
   ├── Wrong predictions: Y  
   ├── Accuracy: X / (X+Y) × 100%
   └── Example: 165/322 = 51.2% accurate
   
   Model 2:
   ├── Correct predictions: A
   ├── Wrong predictions: B
   ├── Accuracy: A / (A+B) × 100%
   └── Example: 168/322 = 52.2% accurate
   ```

5. **Visualization**
   - Bar chart: Accuracy comparison
   - Confusion matrices: Detailed hit/miss breakdown
   - Trend line: Accuracy by month (if applicable)

**Example Output Table:**
```
Metric            | Model 1  | Model 2
------------------|----------|----------
Predictions Made  | 322      | 322
Correct           | 165      | 168
Incorrect         | 157      | 154
Accuracy %        | 51.2%    | 52.2%
```

#### Section 5: Profitability Analysis - Core Calculations (Cells 21-25)

**Betting Simulation Framework:**

Assume: 10 bet on each game according to each model's prediction

**For Each Game:**

1. **Model's Prediction**
   - Calculate implied probability from decimal odds
   - Make binary prediction (1 or 0)

2. **Betting Outcome**
   - IF prediction correct: Win bet of $10
     - Return: $10 × decimal_odds
     - Profit: ($10 × decimal_odds) - $10
   
   - IF prediction incorrect: Lose bet of $10
     - Return: $0
     - Loss: -$10

3. **Cumulative Tracking**
   - Running total profit/loss after each game
   - Track bankroll growth

**Detailed Calculations:**

```
Game 1:
├── Model 1 Decimal Odds: 1.91
├── Model 1 Prediction: Win (implied prob > 50%)
├── Actual Outcome: Win ✓
├── Profit: (10 × 1.91) - 10 = $9.10
└── Running Total: $9.10

Game 2:
├── Model 1 Decimal Odds: 2.05
├── Model 1 Prediction: Lose (implied prob < 50%)
├── Actual Outcome: Win ✗
├── Profit: -$10
└── Running Total: $9.10 - $10 = -$0.90

[Continue for all 322 games...]

Final:
├── Model 1 Net Profit: $X,XXX
├── Total Bets: $3,220 (322 × $10)
├── ROI: (Net Profit / Total Bets) × 100%
└── Example: ($425 / $3,220) × 100% = 13.2%
```

**ROI Interpretation:**
- **Positive ROI:** Model is profitable
- **Negative ROI:** Model loses money
- **Higher ROI:** Better performing model
- **Break-even (0%):** Model wins as much as it loses

**Example Results:**
```
Model 1:
├── Games Bet: 322
├── Win %: 51.2%
├── Net P/L: +$425
└── ROI: +13.2%

Model 2:
├── Games Bet: 322
├── Win %: 52.2%
├── Net P/L: +$680
└── ROI: +21.1%
```

**Profitability Outputs:**
- Net profit/loss dollars ($)
- Win/loss count visual
- Cumulative P/L chart over time
- ROI percentage comparison
- Break-even analysis (when did each model hit profitability?)

#### Section 6: Comparative Analysis & Visualization (Cells 26-28)

**Side-by-Side Comparison:**

1. **Accuracy Comparison Chart**
   - Bar chart: Model 1 vs Model 2 accuracy %
   - Label: Which model more accurate?

2. **Profitability Comparison Chart**
   - Bar chart: Net P/L in dollars
   - Label: Which model more profitable?

3. **ROI Comparison Chart**
   - Bar chart: ROI percentage
   - Key metric for investment decisions

4. **Cumulative Profit Chart**
   - Line chart: Both models' running totals over 322 games
   - Visual trend of profitability
   - Identify when each model "turned profitable"
   - Show volatility (drawdown periods)

5. **Performance Metrics Table**
   ```
   Metric                | Model 1    | Model 2    | Winner
   ----------------------|------------|------------|--------
   Accuracy %            | 51.2%      | 52.2%      | Model 2
   Total Bets Placed     | 322        | 322        | Tie
   Wins                  | 165        | 168        | Model 2
   Losses                | 157        | 154        | Model 2
   Net Profit ($)        | +425       | +680       | Model 2
   ROI (%)               | +13.2%     | +21.1%     | Model 2
   Avg Win ($)           | 8.23       | 8.41       | Model 2
   Avg Loss ($)          | -10.00     | -10.00     | Tie
   Max Drawdown ($)      | -45        | -35        | Model 2
   Win/Loss Ratio        | 1.050      | 1.091      | Model 2
   ```

6. **Monthly/Seasonal Breakdown**
   - If data available: Performance by month
   - Season 2020 vs 2021 differences
   - Identify if models perform differently in particular periods

**Final Recommendation:**
```
RECOMMENDATION: Model 2
Rationale: 
- 1% higher accuracy (52.2% vs 51.2%)
- 60% higher profitability ($680 vs $425)
- 1.6x better ROI (21.1% vs 13.2%)
- More consistent performance across games
- Lower maximum drawdown (volatility)

Implementation: 
Deploy Model 2 for live betting applications
```

## 🎲 Key Concepts

### Implied Probability vs Decimal Odds

**Relationship:**
$$\text{Implied Probability} = \frac{1}{\text{Decimal Odds}}$$

**Examples:**
| Decimal Odds | Implied Probability | Meaning |
|---|---|---|
| 1.50 | 1/1.50 = 66.7% | Heavy favorite |
| 2.00 | 1/2.00 = 50.0% | Even odds |
| 3.00 | 1/3.00 = 33.3% | Underdog |
| 10.00 | 1/10.00 = 10.0% | Heavy underdog |

### Streak Analysis

**Tracking consecutive wins/losses:**
- Identifies when models "go cold" or "hot"
- Highlights volatility periods
- Useful for risk management

### Odds Value Assessment

**Is an odd "good" or "bad"?**
- Model accurately predicts 52% win rate
- If getting 2.0 odds (50% implied): GOOD VALUE
- If getting 1.8 odds (55% implied): BAD VALUE

This project evaluates both aspects simultaneously.

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Python 3.7+ | Programming language |
| Pandas | Data loading and manipulation |
| NumPy | Numerical calculations |
| Matplotlib | Static visualizations |
| Seaborn | Statistical charts |
| Jupyter Notebook | Interactive analysis environment |

## 🚀 How to Run

### Step 1: Setup Environment
```bash
pip install pandas numpy matplotlib seaborn jupyter
```

### Step 2: Prepare Data
- Ensure `mlb-odds-2020.csv` and `mlb-odds-2021.csv` are in project directory
- Verify CSV format matches expected structure

### Step 3: Run Notebook
```bash
jupyter notebook odd_models_Papigkiotis.ipynb
```

### Step 4: Execute Cells
- Run cells sequentially (Shift+Enter)
- Review outputs after each section
- Save final results

### Step 4 (Alternative): Google Colab
1. Upload notebook and CSV files to Google Colab
2. Run cells with "Play" button
3. Compare results in cloud environment

## 📊 Expected Outputs

**Primary Deliverables:**

1. **Accuracy Comparison**
   - Model 1: X%
   - Model 2: Y%

2. **Profitability Analysis**
   - Model 1 ROI: +A%
   - Model 2 ROI: +B%

3. **Visualizations**
   - Accuracy bar chart
   - ROI comparison chart
   - Cumulative profit line chart
   - Performance metrics table

4. **Conclusion**
   - Winner identification
   - Recommended model for deployment

## 💡 Insights for Stakeholders

### For Sports Bettors
- Understand which model to follow
- Calculate expected returns (ROI)
- Manage bankroll based on profitability metrics

### For Data Scientists
- Learn odds conversion mathematics
- Practice financial metric calculations
- Understand backtesting framework

### For Analysts
- Identify which model captures predictive edge
- Spot periods of strong/weak performance
- Calculate betting profitability beyond raw accuracy

## ⚠️ Important Disclaimers

### Limitations of Analysis
- ⚠️ Historical backtesting ≠ future performance guarantee
- ⚠️ Past odds may not reflect future market odds
- ⚠️ 322-game sample may not be statistically sufficient
- ⚠️ Model assumptions (home/away weighting) not documented
- ⚠️ 2020 shortened season may skew results

### Responsible Betting Reminders
- ⚠️ Sports betting involves financial risk
- ⚠️ No model is 100% accurate
- ⚠️ Only bet money you can afford to lose
- ⚠️ This analysis is educational, not financial advice
- ⚠️ Consider consulting professional bettors/analysts

## 🔮 Future Enhancements

- **Ensemble methods:** Combine both models intelligently
- **Kelly criterion:** Optimize bet sizing based on edge
- **Additional features:** Include team stats, injuries, weather
- **Real-time deployment:** Live betting implementation
- **Expand to other sports:** NBA, NFL, Soccer
- **Advanced models:** Neural networks, gradient boosting
- **Performance tracking:** Monitor live betting results

## 📚 References

### Betting Terminology
- **American Odds:** Moneyline format used in North America
- **Decimal Odds:** European format (total return including stake)
- **Implied Probability:** Likelihood embedded in odds
- **Edge:** Advantage over market-implied probability
- **ROI:** Return on investment metric for profitability

### Statistical Concepts
- **Backtesting:** Evaluating strategy on historical data
- **Accuracy:** Percentage of correct predictions
- **Profitability:** Financial return in dollars
- **Volatility:** Fluctuation in returns over time

---

**Project Author:** Iraklis Papigkiotis

**Last Updated:** 2026

For questions or extensions, refer to the parent `Football Analytics/README_ANALYTICS.md` documentation.
