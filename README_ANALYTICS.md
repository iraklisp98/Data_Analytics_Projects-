# Data Analytics Projects Portfolio

Welcome to the comprehensive Data Analytics Projects repository! This collection showcases advanced analytics across diverse domains including sports, finance, e-commerce, and social media.

## Repository Overview

This repository contains **6 major analytical projects** with **5 Jupyter notebooks**, **2 Python scripts**, and **300+ data files** covering multiple analytical methodologies and industries.

---

## 📋 Projects Summary

### 🏆 1. Football Analytics
**Location:** `Football Analytics/`

A comprehensive sports analytics initiative analyzing European professional football leagues and ML-based betting prediction models.

**Sub-Projects:**
- **European Football Leagues Analysis** - Statistical analysis of Premier League, Ligue 1, and La Liga attacking metrics (2017-2019)
- **MLB Odds Prediction Models** - Comparative evaluation of two predictive models for sports betting profitability prediction (2020-2021)

**Key Technologies:** Pandas, SciPy (Distribution fitting), Matplotlib, Seaborn, Plotly
**Status:** ✅ Complete

[→ Detailed Football Analytics Documentation](Football%20Analytics/README_ANALYTICS.md)

---

### 📈 2. S&P 500 Stock Market Analysis  
**Location:** `S&P_resources/`

Five-year temporal analysis of major S&P 500 technology stocks with advanced time series techniques and correlation analysis.

**Focus:** Tech stocks (AAPL, MSFT, GOOG, AMZN) with 139-stock full S&P 500 dataset capability

**Key Analyses:**
- Moving average trend filtering and noise removal
- Multi-stock correlation analysis
- Technical pattern recognition
- Interactive 5-year historical visualizations

**Key Technologies:** Pandas, NumPy, Plotly (interactive charts), Matplotlib, Seaborn
**Status:** ✅ Complete

[→ Detailed S&P Resources Documentation](S%26P_resources/README_ANALYTICS.md)

---

### 💰 3. Sales Analytics  
**Location:** `Sales Analytics/`

Multi-faceted sales analysis covering alcohol distribution and retail commerce with geographic and temporal insights.

**Sub-Projects:**
- **Alcohol Sales Monitoring (US)** - Geographic and product analysis of US alcohol sales (2016-2019)
- **Retail Sales Analysis** - Temporal and spatial analysis of e-commerce transactions with seasonal trends

**Key Technologies:** Pandas, Matplotlib, Seaborn, Plotly
**Status:** ✅ Complete

[→ Detailed Sales Analytics Documentation](Sales%20Analytics/README_ANALYTICS.md)

---

### 🎬 4. YouTube Commercial Analytics
**Location:** `Youtube_Commercial/`

Comprehensive sentiment and engagement analysis of YouTube content with NLP techniques for social media intelligence.

**Key Analyses:**
- TextBlob-based sentiment analysis and polarity classification
- Word frequency and keyword extraction (positive/negative)
- Emoji usage and emotional expression patterns
- Title punctuation analysis for clickbait detection
- Category-based performance correlation

**Key Technologies:** TextBlob (NLP), WordCloud, Emoji extraction, Pandas, Plotly
**Status:** ✅ Complete

[→ Detailed YouTube Analytics Documentation](Youtube_Commercial/README_ANALYTICS.md)

---

## 🔧 Technical Stack

### Core Libraries
| Category | Libraries |
|----------|-----------|
| **Data Processing** | Pandas, NumPy |
| **Visualization** | Matplotlib, Seaborn, Plotly, WordCloud |
| **Statistics & ML** | SciPy, TextBlob |
| **Environment** | Jupyter Notebooks, Google Colab |

### Statistical Techniques Used
- **Normality Testing:** Shapiro-Wilk test, Kolmogorov-Smirnov test, Anderson-Darling test
- **Distribution Fitting:** Normal distribution, Skew Normal distribution
- **Time Series Analysis:** Moving averages, trend identification, seasonal analysis
- **Correlation Analysis:** Pearson correlation, heatmap visualization
- **Sentiment Analysis:** TextBlob polarity scoring, classification
- **NLP:** WordCloud generation, keyword extraction, emoji analysis

---

## 📊 Data Summary

| Project | Data Type | Records | Time Period | Geographic Scope |
|---------|-----------|---------|-------------|------------------|
| Football Leagues | Sporting statistics | 1,000s | 2017-2019 | England, France, Spain |
| Stock Market | Financial time series | 1,260+ days each | 5-year history | US (S&P 500) |
| Alcohol Sales | Retail transactions | 100,000s | 2016-2019 | All US (zip code level) |
| Retail Sales | E-commerce | 1,000s | Monthly | Unspecified |
| Baseball Odds | Betting data | 2 seasons | 2020-2021 | US teams |
| YouTube | Social comments | 1,000s | Variable | US-focused |

---

## 🎯 Quick Start Guide

### For Each Project:
1. Navigate to the project folder (e.g., `Football Analytics/`)
2. Read the project-specific README.md for detailed documentation
3. Review the sub-project READMEs for methodology and results
4. Open notebooks in Jupyter or Google Colab
5. Follow the data sourcing instructions in each project's README

### Running Notebooks:
- Most notebooks were developed in **Google Colab**
- Can also run locally with Jupyter Notebook: `jupyter notebook filename.ipynb`
- Ensure all dependencies are installed: See individual project READMEs

---

## 🎓 Learning Outcomes & Applications

### Statistical Analysis Mastery
- Distribution fitting and normality testing
- Hypothesis testing and statistical inference
- Correlation and relationship analysis

### Time Series Analysis
- Trend identification and forecasting
- Seasonal decomposition
- Moving average smoothing techniques

### Data Visualization
- Interactive charts with Plotly
- Comparative multi-variable analysis
- Geographic and categorical breakdowns

### Machine Learning & Prediction
- Classification models for predictions
- Feature correlation analysis
- Model performance evaluation metrics

### Natural Language Processing
- Sentiment analysis and polarity scoring
- Keyword extraction and frequency analysis
- Text visualization with word clouds

---

## 📁 Repository Structure

```
Data-Analytics-Projects-/
│
├── Football Analytics/
│   ├── README_ANALYTICS.md
│   ├── European_Football_Leagues/
│   │   ├── README.md
│   │   ├── part1_papigkiotis.py
│   │   ├── Part2_Papigkiotis.ipynb
│   │   └── combined_output*.csv
│   └── odd_prediction_model/
│       ├── README.md
│       ├── odd_models_Papigkiotis.ipynb
│       └── mlb-odds-*.csv
│
├── S&P_resources/
│   ├── README_ANALYTICS.md
│   ├── time_series_project_stocks.ipynb
│   └── individual_stocks_5yr/
│       └── [139 stock CSV files]
│
├── Sales Analytics/
│   ├── README_ANALYTICS.md
│   ├── Alcohol_Sold_US/
│   │   ├── README.md
│   │   └── final_assignment_papigkiotis.py
│   └── Sales+Analysis/
│       ├── README.md
│       ├── sales_analysis.ipynb
│       └── Sales_data.ftr
│
└── Youtube_Commercial/
    ├── README_ANALYTICS.md
    ├── project_Youtube_analysis.ipynb
    ├── UScomments.csv
    └── additional_data/
```

---

## 🔮 Upcoming Projects

- **Zomato Analytics** - Restaurant performance and ratings analysis
- **European Football Dangerous Plays Assessment** - Injury-risk analysis in professional football

---

## 💡 Key Insights Across Projects

1. **Sports Analytics:** Statistical rigor in sports betting can identify profitable prediction models
2. **Finance:** Multi-year stock correlations reveal sector dynamics and investment patterns
3. **Retail:** Geographic and temporal patterns in sales data guide inventory and marketing strategy
4. **Social Media:** Sentiment analysis of social data provides brand perception and engagement insights
5. **Data Quality:** Proper data cleaning and transformation is foundational to all analyses

---

## 🙋 Project Author

**Analyst:** Iraklis Papigkiotis

---

**Last Updated:** 2026

For detailed information on any specific project, navigate to its subfolder and read the dedicated README.md files.
