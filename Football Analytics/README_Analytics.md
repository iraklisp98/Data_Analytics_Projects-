# Football Analytics Projects

Advanced statistical analysis of professional football leagues and predictive modeling for sports betting.

## Project Overview

This directory contains two distinct analytical projects in football analytics:

1. **European Football Leagues Analysis** - Comprehensive statistical study of attacking metrics across three major European leagues
2. **MLB Odds Prediction Models** - Comparative evaluation of predictive betting models

---

## 📊 Project 1: European Football Leagues Analysis

### Purpose & Scope
Comprehensive statistical analysis examining attacking metrics across three major European professional football leagues over consecutive seasons (2017-2019). This project applies rigorous statistical methodologies to identify patterns, trends, and strategic differences in football performance.

### Leagues Analyzed
- **Premier League** (England) - English top division
- **Ligue 1** (France) - French top division  
- **La Liga** (Spain) - Spanish top division

### Key Metrics
The analysis focuses on three critical attacking performance indicators:
- **Goals (CR1, CR2, GOAL1, GOAL2)** - Scoring efficiency metrics
- **Corners** - Tactical offensive opportunities
- **Shots on Goal (SHG)** - Shooting accuracy and conversion rates

### Analyses Performed

#### 1. Descriptive Statistics
- Mean, median, standard deviation, and variance
- Quartile analysis and range calculations
- Distribution characteristics for each metric

#### 2. Comparative League Analysis
- Performance differences between Premier League, Ligue 1, and La Liga
- Cross-league ranking and benchmarking
- Strategic pattern identification

#### 3. Correlation Analysis
- Relationships between goals, corners, and shots on goal
- Correlation heatmaps showing metric dependencies
- Scatter plot visualization of relationships

#### 4. Distribution Fitting
- Normal distribution fitting with visualization
- Skew Normal distribution modeling
- Quantile-quantile plots for validation

#### 5. Statistical Normality Tests
- **Shapiro-Wilk Test** - Normality assessment with p-values
- **Kolmogorov-Smirnov Test** - Goodness-of-fit evaluation
- **Anderson-Darling Test** - Distribution tail analysis

#### 6. Team Performance Tracking
- Season-to-season performance comparison
- Individual team trend analysis
- Year-over-year metric evolution

### Data Source & Files

```
European_Football_Leagues/
├── part1_papigkiotis.py           # Data preparation script
├── Part2_Papigkiotis.ipynb        # Main analysis notebook (53 cells)
├── combined_output.csv            # Premier League processed data
├── combined_output1.csv           # Ligue 1 processed data
├── combined_output2.csv           # La Liga processed data
└── read_me_first_Papigkiotis.txt  # Project documentation
```

### Data Processing Pipeline

**Part 1: Data Merging Script** (`part1_papigkiotis.py`)
- Consolidates multiple CSV files from regional league data directories (ENG, FRA, SPA)
- Concatenates season data for each league
- Outputs unified datasets for analysis in combined_output*.csv files

### Notebook Structure

**Part 2: Main Analysis** (53 cells) includes:

1. **Setup & Import** (Cells 1-5)
   - Load required libraries (Pandas, NumPy, SciPy, Matplotlib, Seaborn)
   - Import preprocessed data from Google Colab

2. **Data Overview** (Cells 6-10)
   - Display dataset structure and dimensions
   - Examine first/last rows and data types
   - Review summary statistics

3. **Descriptive Analysis** (Cells 11-20)
   - Calculate mean, median, std dev for metrics
   - Generate statistical summaries per league
   - Compare distributions across leagues

4. **Visualization** (Cells 21-30)
   - Histogram distributions for each metric
   - Density plots showing distribution shape
   - Box plots for comparative analysis

5. **Correlation Study** (Cells 31-40)
   - Compute Pearson correlation matrices
   - Create correlation heatmaps
   - Generate scatter plots for variable pairs

6. **Distribution Fitting** (Cells 41-50)
   - Normal distribution fitting with parameters
   - Skew Normal distribution modeling
   - Overlay fitted curves on histograms

7. **Statistical Tests** (Cells 51-53)
   - Shapiro-Wilk normality tests
   - K-S and Anderson-Darling test results
   - Interpretation of normality findings

### Key Findings & Outputs

- **Distribution Characteristics:** Identifies which attacking metrics follow normal distributions
- **Correlation Strengths:** Reveals which metrics are highly interdependent
- **League Differences:** Quantifies strategic/performance variations across leagues
- **Statistical Evidence:** Provides p-values and test statistics for rigorous conclusions
- **Trend Patterns:** Shows evolution of metrics across 2017-2019 seasons

### Technologies Used

| Library | Purpose |
|---------|---------|
| Pandas | Data loading, manipulation, and transformation |
| NumPy | Numerical computations and array operations |
| SciPy | Distribution fitting, statistical tests |
| Matplotlib | Foundation for all charts and visualizations |
| Seaborn | Statistical visualization (heatmaps, distributions) |
| Plotly | Interactive comparative visualizations |

### How to Use

1. **Prepare Data:** Run `part1_papigkiotis.py` to consolidate raw league data
2. **Run Analysis:** Open `Part2_Papigkiotis.ipynb` in Jupyter/Google Colab
3. **Review Results:** Examine generated charts and statistical outputs
4. **Interpret Findings:** Use p-values and visualizations for conclusions

### Data Quality Notes

- Data spans 2017-2019 seasons (3 years of historical data)
- All three leagues standardized metrics for comparison
- Missing values handled according to statistical best practices

---

## ⚾ Project 2: MLB Odds Prediction Models Comparison

### Purpose & Scope
Comparative evaluation of two machine learning models trained to predict MLB game outcomes, with focus on profitability for sports betting applications. Tests predictive accuracy and calculates return on investment (ROI) for each model.

### Methodology

#### Odds Conversion System
- **Input Format:** American odds (used by US sportsbooks)
  - Example: -110 (favorites), +150 (underdogs)
- **Output Format:** European decimal odds (used internationally)
  - Formula: (American odds ÷ 100 + 1) for negative; (100 ÷ American odds + 1) for positive
- **Purpose:** Standardized probability conversion for comparison

#### Model Comparison Framework
- Evaluate each model's predictions against actual game outcomes
- Track prediction accuracy (win %)
- Calculate profit/loss assuming $1 bet per game
- Compute ROI metrics for investment return analysis

### Data Files

```
odd_prediction_model/
├── odd_models_Papigkiotis.ipynb    # Analysis notebook (28 cells)
├── mlb-odds-2020.csv               # 2020 season data
├── mlb-odds-2021.csv               # 2021 season data
└── read_me_first_Papigkiotis.txt   # Documentation
```

### Dataset Structure

Each CSV includes:
- **Date** - Game date
- **Final** - Actual game outcome (1/0 for winner/loser)
- **Model 1** - Prediction odds in American format
- **Model 2** - Prediction odds in American format
- **Venue/Team Info** - Game context data

### Notebook Analysis Structure (28 cells)

1. **Data Import** (Cells 1-3)
   - Load 2020 and 2021 season data
   - Display shape and sample rows

2. **Data Cleaning** (Cells 4-7)
   - Handle null/missing values
   - Verify data types and ranges
   - Remove invalid entries

3. **Odds Transformation** (Cells 8-15)
   - Create functions for American → European conversion
   - Apply to Model 1 predictions
   - Apply to Model 2 predictions

4. **Prediction Evaluation** (Cells 16-20)
   - Compare Model 1 predictions against actual outcomes
   - Compare Model 2 predictions against actual outcomes
   - Calculate prediction accuracy (%)

5. **Profitability Analysis** (Cells 21-25)
   - Simulate $1 bets on each model
   - Calculate cumulative profit/loss
   - Track ROI percentage

6. **Comparison & Visualization** (Cells 26-28)
   - Side-by-side model performance charts
   - ROI comparison graphs
   - Winner determination

### Key Metrics

| Metric | Definition |
|--------|-----------|
| **Accuracy** | % of correct predictions out of total bets |
| **Cumulative P/L** | Total profit or loss across all bets |
| **ROI** | (Final Bankroll - Initial Bankroll) ÷ Initial Bankroll × 100 |
| **Win Rate** | Consecutive and overall winning predictions |

### Analysis Outputs

- **Model 1 Accuracy:** Percentage correct predictions
- **Model 2 Accuracy:** Percentage correct predictions
- **Model 1 ROI:** Profitability ranking
- **Model 2 ROI:** Profitability ranking
- **Recommended Model:** Statistical winner for deployment

### Technologies Used

| Library | Purpose |
|---------|---------|
| Pandas | CSV data loading and manipulation |
| NumPy | Mathematical calculations for odds conversion |
| Matplotlib | Performance comparison charts |
| Seaborn | Statistical visualization |  
| Plotly | Interactive ROI trends |

### Betting Model Evaluation

This analysis applies rigorous statistical evaluation to sports betting models:
- **Backtesting:** Historical performance validation
- **Profitability Analysis:** Real-world financial outcomes
- **Risk Assessment:** Volatility and drawdown analysis
- **Model Selection:** Data-driven decision making

### How to Use

1. **Load Data:** Open `mlb-odds-2020.csv` and `mlb-odds-2021.csv`
2. **Run Notebook:** Execute `odd_models_Papigkiotis.ipynb` in Jupyter/Colab
3. **Review Results:** Compare accuracy and ROI metrics
4. **Select Model:** Choose higher-performing model for deployment

### Data Quality Notes

- Data covers 2020-2021 MLB seasons  
- American odds format standardized across datasets
- All predictions and outcomes validated for accuracy

---

## 🔍 Cross-Project Insights

**Statistical Rigor:** Both projects employ industry-standard statistical methodologies
**Data-Driven Decision Making:** Conclusions based on quantitative analysis, not intuition
**Practical Application:** Results directly applicable in sports strategy and betting
**Reproducibility:** Structured code and documentation enable verification and extension

---

## 📚 Further Exploration

- Extend European Football analysis to additional seasons or leagues
- Incorporate additional features (injuries, player transfers) into betting models
- Develop ensemble models combining both prediction approaches
- Expand to other sports using similar analytical frameworks

---

**Project Author:** Iraklis Papigkiotis

For individual project details, see sub-project README.md files.
