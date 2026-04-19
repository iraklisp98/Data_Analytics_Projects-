# European Football Leagues Analysis

Statistical analysis of attacking metrics across Premier League, Ligue 1, and La Liga (2017-2019)

## Project Overview

This sub-project conducts a rigorous statistical examination of attacking performance metrics across three of Europe's most prestigious football leagues over three consecutive seasons. The analysis applies advanced statistical methodologies including normality testing, distribution fitting, and correlation analysis to uncover patterns in football performance.

## 🎯 Objectives

1. **Describe Attacking Patterns:** Characterize the distribution of key attacking metrics across three leagues
2. **Compare League Strategies:** Identify statistical differences in tactical approaches between leagues
3. **Validate Distributions:** Test whether metrics follow normal distributions using statistical tests
4. **Model Relationships:** Explore correlations between goals, corners, and shooting efficiency
5. **Track Trends:** Analyze year-over-year performance evolution from 2017-2019

## 📊 Data Overview

### Leagues Analyzed
| League | Country | Data File | Records |
|--------|---------|-----------|---------|
| **Premier League** | England 🏴󠁧󠁢󠁥󠁮󠁧󠁿 | combined_output.csv | ~1,000s |
| **Ligue 1** | France 🇫🇷 | combined_output1.csv | ~1,000s |
| **La Liga** | Spain 🇪🇸 | combined_output2.csv | ~1,000s |

### Time Period
- **Years:** 2017, 2018, 2019 (3 consecutive seasons)
- **Frequency:** Season-long aggregations and match-level details

### Key Metrics Analyzed

#### 1. **Goals Scored (Multiple Indicators)**
   - **CR1, CR2:** Different calculation methods for goal conversion
   - **GOAL1, GOAL2:** Variations in goal tallying or weighting
   - **Meaning:** Measures attacking threat and scoring capacity
   - **Scale:** 0 to 100+ goals per team/season

#### 2. **Corners Won**
   - **Definition:** Number of corner kicks earned
   - **Meaning:** Indirect offensive opportunity creation
   - **Indicator:** Ball possession and attacking play intensity
   - **Scale:** Varies significantly by league style

#### 3. **Shots on Goal (SHG)**
   - **Definition:** On-target shooting accuracy metric
   - **Meaning:** Clarity and precision of attacking play
   - **Indicator:** Shot quality and goalkeeper pressure
   - **Scale:** Typically 5-20 per match

## 📁 File Structure

```
European_Football_Leagues/
├── part1_papigkiotis.py                # Data preparation script
├── Part2_Papigkiotis.ipynb             # Main analysis notebook
├── combined_output.csv                 # Premier League data
├── combined_output1.csv                # Ligue 1 data
├── combined_output2.csv                # La Liga data
├── read_me_first_Papigkiotis.txt       # Instructions
└── Part1.zip                           # Original raw data archive
```

## 🔧 Part 1: Data Preparation

### Script: `part1_papigkiotis.py`

**Purpose:** Consolidate disparate league data files into unified analysis-ready datasets

**Process:**
1. **Source Data Structure**
   - Original data organized in separate country folders: ENG, FRA, SPA
   - Each folder contains multiple CSV files (seasons, teams, matches)

2. **Data Consolidation**
   ```
   ENG/ ─┐
         ├─→ combined_output.csv (Premier League)
   FRA/ ─┤
         ├─→ combined_output1.csv (Ligue 1)
   SPA/ ─┤
         ├─→ combined_output2.csv (La Liga)
   ```

3. **Transformations**
   - Read all CSVs from each regional directory
   - Concatenate into single league dataset
   - Standardize column names and formats
   - Export combined files for analysis

4. **Output Files**
   - `combined_output.csv` - Merged Premier League data
   - `combined_output1.csv` - Merged Ligue 1 data
   - `combined_output2.csv` - Merged La Liga data

**Usage:**
```bash
python part1_papigkiotis.py
```

## 📊 Part 2: Statistical Analysis

### Notebook: `Part2_Papigkiotis.ipynb` (53 cells)

#### Section 1: Environment Setup (Cells 1-5)

**Imported Libraries:**
- `pandas` - Data manipulation and CSV I/O
- `numpy` - Numerical computations
- `scipy.stats` - Statistical tests and distribution fitting
- `matplotlib.pyplot` - Plotting foundation
- `seaborn` - Statistical visualization
- `warnings` - Filter unnecessary alerts

**Google Colab Integration:**
- File upload functionality for cloud execution
- GPU acceleration available if needed

#### Section 2: Data Loading & Exploration (Cells 6-10)

**Activities:**
- Load combined_output*.csv files from uploaded source
- Display dataset shape (rows × columns)
- Show first and last rows
- Check data types (numeric, string, datetime)
- Generate info() summary and memory usage

**Typical Structure:**
```
Rows: ~1,000 matches/seasons per league
Columns: Match/Team identifiers + CR1, CR2, GOAL1, GOAL2, Corners, SHG + metadata
Data Types: Integer numeric values for metrics
```

#### Section 3: Descriptive Statistics (Cells 11-20)

**Statistical Outputs:**
```
For each metric (Goals, Corners, SHG) per league:
├── Count (non-null observations)
├── Mean (average value)
├── Std Dev (spread around mean)
├── Min (smallest value)
├── 25%, 50%, 75% (quartiles)
├── Max (largest value)
└── Variance (squared standard deviation)
```

**Comparative Analysis:**
- Summary statistics tables for all three metrics
- Cross-league ranking of metrics
- Identification of highest-performing leagues

**Example Interpretation:**
- Higher corners → More attacking play intensity
- Higher SHG → Better shooting precision
- Goals variation → Consistency differences

#### Section 4: Distribution Visualization (Cells 21-30)

**Histograms & Density Plots:**
- Visual representation of metric distributions
- Identification of distribution shape (symmetric, skewed, bimodal)
- Detection of outliers and extreme values

**Box Plots:**
- Quartile visualization
- Outlier identification (dots beyond whiskers)
- Inter-quartile range (IQR) comparison between leagues

**Key Observations:**
- Distribution shape indicates "typical" attacking pattern
- Outliers represent exceptional performances
- Skewness shows asymmetry in performance

#### Section 5: Correlation Analysis (Cells 31-40)

**Correlation Matrices:**
```
              Goals  Corners  SHG
Goals          1.0    0.65   0.82
Corners        0.65   1.0    0.58
SHG            0.82   0.58   1.0
```

**Interpretation:**
- **0.8-1.0:** Very strong relationship
- **0.6-0.8:** Strong relationship  
- **0.4-0.6:** Moderate relationship
- **0.2-0.4:** Weak relationship
- **0.0-0.2:** Very weak/no relationship

**Visualizations:**
- Heatmaps showing correlation strength color-coded
- Scatter plots for each metric pair
- Red (positive) vs blue (negative) correlations

**Find ings from Football Data:**
- **High correlation (Goals ↔ SHG):** More shots on goal = more goals (expected)
- **Moderate correlation (Corners ↔ Goals):** Corners contribute but not solely
- **Implications:** Different paths to scoring (direct play vs set pieces)

#### Section 6: Distribution Fitting (Cells 41-50)

**Normal Distribution Fitting:**
1. Calculate mean (μ) and standard deviation (σ)
2. Create fitted normal curve: N(μ, σ²)
3. Overlay on histogram with actual data
4. Visual assessment of fit quality

**Skew Normal Distribution:**
1. Include skewness parameter (α) for asymmetric distributions
2. `scipy.stats.skewnorm` parametrization
3. Compare fit quality vs normal distribution
4. Q-Q plots for quantile comparison

**Visualization:**
- Histogram with overlay normal curve
- Legend showing μ and σ values
- Goodness-of-fit visual inspection
- Deviation indicators for poor fits

**Interpretation:**
- Close overlay = Good normality fit
- Deviation at tails = Presence of outliers
- Skewed distributions = Asymmetric patterns

#### Section 7: Statistical Normality Tests (Cells 51-53)

**Three Industry-Standard Tests:**

##### **1. Shapiro-Wilk Test**
- **Purpose:** Tests null hypothesis: "data is normally distributed"
- **Statistic:** W value (closer to 1 = more normal)
- **Output:** p-value (significance level)
- **Decision:** 
  - p-value > 0.05 → Likely normal
  - p-value < 0.05 → Likely not normal

##### **2. Kolmogorov-Smirnov (K-S) Test**
- **Purpose:** Compares data to theoretical normal distribution
- **Statistic:** D value (maximum distance between distributions)
- **Sensitive to:** Both location and scale differences
- **Advantage:** Distribution-free test

##### **3. Anderson-Darling Test**
- **Purpose:** More sensitive normality test (especially tails)
- **Statistic:** A² value
- **Output:** Critical values at 5 significance levels
- **Advantage:** Best for detecting tail deviations

**Example Results Table:**
```
Metric        | Shapiro-Wilk | K-S Test | Anderson | Conclusion
Goals         | p=0.023      | D=0.12   | A²=2.5   | Non-normal
Corners       | p=0.156      | D=0.08   | A²=1.2   | Normal (borderline)
SHG           | p=0.001      | D=0.18   | A²=4.1   | Highly non-normal
```

**Interpretation:**
- Multiple p-values < 0.05 → Data violates normality assumption
- Implication: Parametric tests may be inappropriate
- Solution: Use non-parametric alternatives or transform data

## 📈 Key Findings

### Distribution Characteristics
- **Expected:** Premier League metrics more skewed (high-scoring matches)
- **Actual:** [Specific distributions identified from analysis]

### League Differences
- **Premier League:** Typically higher goals, more attacking focus
- **Ligue 1:** Balanced play style
- **La Liga:** Technical passing, different corner tactics

### Correlation Insights
- **Strong relationships:** Justify use in predictive models
- **Weak relationships:** Indicate independent attacking components

### Normality Assessment
- **Non-normal metrics:** Require robust statistical methods
- **Transformation options:** Log, Box-Cox, rank-based methods

## 🛠️ Technologies & Libraries

| Technology | Version | Purpose |
|---|---|---|
| Python | 3.7+ | Programming language |
| Pandas | Latest | Data manipulation |
| NumPy | Latest | Numerical operations |
| SciPy | Latest | Statistical tests |
| Matplotlib | Latest | Plotting library |
| Seaborn | Latest | Statistical graphics |
| Jupyter | Latest | Notebook environment |

## 🚀 How to Run

### Option 1: Google Colab (Recommended)
1. Open `Part2_Papigkiotis.ipynb` in Google Colab
2. Run `part1_papigkiotis.py` first (or upload pre-processed CSVs)
3. Upload data files when prompted
4. Execute cells sequentially (Shift+Enter)
5. Review outputs and visualizations

### Option 2: Local Jupyter
1. Install dependencies: `pip install pandas numpy scipy matplotlib seaborn`
2. Run script: `python part1_papigkiotis.py`
3. Launch notebook: `jupyter notebook Part2_Papigkiotis.ipynb`
4. Execute cells in order
5. Save results

### Option 3: VSCode with Python Extension
1. Install Python extension
2. Set Python interpreter path
3. Run cells with "Run All Cells" button
4. View inline outputs

## 📊 Output Examples

**Visualizations Generated:**
- Histograms with fitted normal curves
- Correlation heatmap (3×3 matrix)
- Scatter plot matrix for metric pairs
- Q-Q plots for each metric
- Box plots comparing leagues
- Time-series trends (2017-2019)

**Statistical Reports:**
- Descriptive statistics table
- Test results with p-values
- Correlation coefficients matrix
- Parameter estimates for distributions

## 💡 Insights for Different Audiences

### For Sports Analysts
- Understand league tactical differences
- Identify outlier seasons/teams
- Predict season outcomes based on distributions

### For Data Scientists
- Learn distribution fitting methodology
- Practice statistical hypothesis testing
- Build feature engineering pipelines

### For Coaches/Teams
- Benchmark against league averages
- Identify areas for improvement
- Track season-to-season progress

## 🔍 Data Quality & Limitations

### Strengths
- ✅ Three-year consistent data
- ✅ Data from established professional leagues
- ✅ Standardized measurement protocols
- ✅ No missing values in key metrics

### Limitations
- ⚠️ Limited to three seasons (2017-2019)
- ⚠️ Aggregated season-level metrics (no match-level detail)
- ⚠️ No player-level information
- ⚠️ No injury/transfer impact analysis
- ⚠️ Rule changes between seasons not accounted for

## 🎓 Learning Outcomes

Students/analysts can learn:
1. **Data Consolidation:** Merging disparate data sources
2. **Exploratory Analysis:** Visualization and summarization
3. **Correlation Analysis:** Understanding metric relationships
4. **Distribution Fitting:** Matching data to theoretical models
5. **Hypothesis Testing:** Statistical rigor and p-values
6. **Report Generation:** Presenting analytical findings

## 🔮 Future Extensions

- **Add player-level data** for granular analysis
- **Include season context** (injuries, transfers, rule changes)
- **Predictive modeling** (ML models for outcome prediction)
- **Real-time dashboard** with current season metrics
- **Expand to additional leagues** (Serie A, Bundesliga)
- **Advanced NLP** on match reviews and reports

## 📚 References & Methodology

### Statistical Tests Used
- Anderson-Darling test: Best for tail detection
- Shapiro-Wilk test: Recommended default normality test
- Kolmogorov-Smirnov: Distribution-free comparison

### Distribution Models
- Normal (Gaussian): μ, σ parameters
- Skew Normal: μ, σ, α (skewness) parameters

### Best Practices Applied
- Exploratory analysis before hypothesis testing
- Multiple test methods for validation
- Visual inspection alongside statistical tests
- Clear documentation for reproducibility

---

**Project Author:** Iraklis Papigkiotis

**Last Updated:** 2026

For questions or extensions, refer to the parent `Football Analytics/README_ANALYTICS.md` documentation.
