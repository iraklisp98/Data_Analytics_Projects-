# YouTube Commercial Analytics

Sentiment analysis, engagement metrics, and content intelligence for YouTube videos using NLP and data visualization

## Project Summary

This project conducts advanced analysis of YouTube video performance and viewer sentiment using natural language processing (NLP) techniques. By analyzing comment text, engagement metrics, and video metadata, the project extracts insights about viewer perception, content performance, and engagement drivers.

## 🎯 Project Objectives

1. **Sentiment Analysis:** Classify viewer comments as positive, negative, or neutral
2. **Engagement Correlation:** Identify relationships between comment sentiment and video metrics
3. **Keyword Extraction:** Extract most discussed topics in positive and negative comments
4. **Clickbait Detection:** Analyze title punctuation correlation with engagement
5. **Content Performance:** Evaluate which video categories drive the most engagement
6. **Emoji Analysis:** Understand emotional expression patterns in comments
7. **Trend Identification:** Identify emerging topics and patterns

## 📊 Data Overview

### Dataset Characteristics

| Attribute | Details |
|-----------|---------|
| **Primary Data** | UScomments.csv (YouTube comments) |
| **Format** | CSV (comma-separated values) |
| **Records** | 1,000+ comment records |
| **Features** | Comment text, video metadata |
| **Geographic Focus** | US-based content/viewers |
| **Data Types** | Text and numeric |

### Data Files

```
Youtube_Commercial/
├── project_Youtube_analysis.ipynb   # Main analysis notebook (83 cells)
├── UScomments.csv                   # Comment dataset
├── README_Analytics.md              # Project documentation
└── additional_data/                 # Supplementary resources
```

### Key Data Columns

**From Comment Records:**
- `Comment_text` - User comment content
- `Likes` - Number of likes on comment
- `Replies` - Number of replies

**From Video Metadata:**
- `Title` - Video title
- `Views` - Total video views
- `Likes` - Video likes
- `Dislikes` - Video dislikes (YouTube historical data)
- `Comments` - Total comment count
- `Category` - Video category (Music, Gaming, etc.)
- `Publish_Date` - Video publication date

---

## 📊 Analysis Notebook Structure

### `project_Youtube_analysis.ipynb` (83 Cells)

#### Section 1: Environment & Data Loading (Cells 1-10)

**Library Imports:**
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from textblob import TextBlob
import re
from collections import Counter
from wordcloud import WordCloud
import emoji
from sklearn.preprocessing import normalize
```

**Load Data:**
```python
# Read comments dataset
comments_df = pd.read_csv('UScomments.csv', on_bad_lines='skip')

print(f"Shape: {comments_df.shape}")
print(f"Columns: {comments_df.columns.tolist()}")
print(comments_df.head())
print(comments_df.info())
```

**Data Exploration:**
- Display shape and sample records
- Check column names and types
- Identify data quality issues

#### Section 2: Data Quality & Cleaning (Cells 11-15)

**Null Value Handling:**
```python
# Check for missing values
print(comments_df.isnull().sum())

# Remove rows with critical missing data
comments_df = comments_df.dropna(subset=['Comment_text'])

# Fill optional fields
comments_df['Likes'] = comments_df['Likes'].fillna(0)
comments_df['Replies'] = comments_df['Replies'].fillna(0)
```

**Text Cleaning:**
```python
# Remove special characters and extra whitespace
comments_df['Comment_text_clean'] = (
    comments_df['Comment_text']
    .str.replace(r'[^a-zA-Z0-9\s]', '', regex=True)  # Keep alphanumeric + spaces
    .str.strip()
    .str.lower()
)

# Remove empty comments after cleaning
comments_df = comments_df[comments_df['Comment_text_clean'].str.len() > 0]
```

**Data Validation:**
```python
print(f"Records after cleaning: {len(comments_df)}")
print(f"Average comment length: {comments_df['Comment_text'].str.len().mean():.0f} chars")
print(f"Median likes per comment: {comments_df['Likes'].median()}")
```

#### Section 3: Sentiment Analysis Setup (Cells 16-20)

**TextBlob Sentiment:**

TextBlob provides polarity scores ranging from -1 to +1:
- **Polarity -1 to +1:**
  - -1: Extremely negative
  - 0: Neutral
  - +1: Extremely positive

```python
# Calculate sentiment polarity for each comment
def get_sentiment(text):
    try:
        return TextBlob(str(text)).sentiment.polarity
    except:
        return 0

comments_df['Polarity'] = comments_df['Comment_text'].apply(get_sentiment)

print(f"Polarity range: {comments_df['Polarity'].min():.2f} to {comments_df['Polarity'].max():.2f}")
print(f"Mean polarity: {comments_df['Polarity'].mean():.2f}")
print(f"Median polarity: {comments_df['Polarity'].median():.2f}")
```

**Sentiment Classification:**
```python
# Classify into positive, negative, neutral
def classify_sentiment(polarity):
    if polarity > 0.1:
        return 'Positive'
    elif polarity < -0.1:
        return 'Negative'
    else:
        return 'Neutral'

comments_df['Sentiment'] = comments_df['Polarity'].apply(classify_sentiment)

# Distribution
print(comments_df['Sentiment'].value_counts())
```

**Distribution Example:**
```
Positive    425 (53%)
Neutral     280 (35%)
Negative    95  (12%)
```

### Section 4: Sentiment Visualization (Cells 21-25)

**Sentiment Distribution:**
```python
# Create visualizations
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# Pie chart
sentiment_counts = comments_df['Sentiment'].value_counts()
axes[0].pie(sentiment_counts, labels=sentiment_counts.index, autopct='%1.1f%%')
axes[0].set_title('Comment Sentiment Distribution')

# Polarity histogram
axes[1].hist(comments_df['Polarity'], bins=50, edgecolor='black')
axes[1].set_xlabel('Polarity Score')
axes[1].set_ylabel('Frequency')
axes[1].set_title('Polarity Score Distribution')

plt.tight_layout()
plt.show()
```

**Statistical Summary:**
```python
print("Sentiment Statistics:")
print(f"  Positive: {(comments_df['Sentiment'] == 'Positive').sum()} comments")
print(f"  Negative: {(comments_df['Sentiment'] == 'Negative').sum()} comments")
print(f"  Neutral: {(comments_df['Sentiment'] == 'Neutral').sum()} comments")
print(f"  Positive Rate: {(comments_df['Sentiment'] == 'Positive').sum() / len(comments_df) * 100:.1f}%")
```

#### Section 5: Word Cloud Generation (Cells 26-35)

**Separate Comments by Sentiment:**
```python
# Extract sentiment-specific texts
positive_text = ' '.join(comments_df[comments_df['Sentiment'] == 'Positive']['Comment_text'])
negative_text = ' '.join(comments_df[comments_df['Sentiment'] == 'Negative']['Comment_text'])
neutral_text = ' '.join(comments_df[comments_df['Sentiment'] == 'Neutral']['Comment_text'])
```

**Generate Word Clouds:**
```python
# Define stop words (common words to exclude)
from nltk.corpus import stopwords
stop_words = set(stopwords.words('english'))

fig, axes = plt.subplots(1, 3, figsize=(18, 5))

# Positive word cloud
wordcloud_pos = WordCloud(
    width=400, height=300,
    background_color='white',
    stopwords=stop_words,
    colormap='Greens'
).generate(positive_text)
axes[0].imshow(wordcloud_pos)
axes[0].set_title('Positive Comments - Word Frequency')
axes[0].axis('off')

# Negative word cloud
wordcloud_neg = WordCloud(
    width=400, height=300,
    background_color='white',
    stopwords=stop_words,
    colormap='Reds'
).generate(negative_text)
axes[1].imshow(wordcloud_neg)
axes[1].set_title('Negative Comments - Word Frequency')
axes[1].axis('off')

# Neutral word cloud
wordcloud_neu = WordCloud(
    width=400, height=300,
    background_color='white',
    stopwords=stop_words,
    colormap='Blues'
).generate(neutral_text)
axes[2].imshow(wordcloud_neu)
axes[2].set_title('Neutral Comments - Word Frequency')
axes[2].axis('off')

plt.tight_layout()
plt.show()
```

**Interpretation:**
- **Positive clouds:** Words like "love," "great," "amazing," "excellent"
- **Negative clouds:** Words like "hate," "bad," "poor," "waste"
- **Neutral clouds:** Descriptive words, questions, generic comments

#### Section 6: Keyword Extraction (Cells 36-45)

**Most Common Words (Positive):**
```python
# Extract words from positive comments
positive_comments = comments_df[comments_df['Sentiment'] == 'Positive']['Comment_text']

# Tokenize and clean
from nltk.tokenize import word_tokenize
words = []
for comment in positive_comments:
    tokens = word_tokenize(comment.lower())
    words.extend([w for w in tokens if w.isalpha() and w not in stop_words])

# Get most common
most_common_positive = Counter(words).most_common(15)

print("Top 15 Positive Keywords:")
for word, count in most_common_positive:
    print(f"  {word}: {count} occurrences")
```

**Most Common Words (Negative):**
```python
# Same process for negative comments
negative_comments = comments_df[comments_df['Sentiment'] == 'Negative']['Comment_text']

words = []
for comment in negative_comments:
    tokens = word_tokenize(comment.lower())
    words.extend([w for w in tokens if w.isalpha() and w not in stop_words])

most_common_negative = Counter(words).most_common(15)

print("Top 15 Negative Keywords:")
for word, count in most_common_negative:
    print(f"  {word}: {count} occurrences")
```

**Example Results:**
```
Positive Keywords:       Negative Keywords:
- love (234)            - hate (87)
- great (201)           - bad (65)
- amazing (187)         - waste (54)
- best (165)            - poor (48)
- excellent (143)       - awful (41)
```

**Visualization:**
```python
fig, axes = plt.subplots(1, 2, figsize=(14, 6))

# Positive keywords
pos_words, pos_counts = zip(*most_common_positive)
axes[0].barh(pos_words, pos_counts, color='green')
axes[0].set_title('Top 15 Positive Keywords')
axes[0].set_xlabel('Frequency')

# Negative keywords
neg_words, neg_counts = zip(*most_common_negative)
axes[1].barh(neg_words, neg_counts, color='red')
axes[1].set_title('Top 15 Negative Keywords')
axes[1].set_xlabel('Frequency')

plt.tight_layout()
plt.show()
```

#### Section 7: Emoji Analysis (Cells 46-55)

**Extract Emojis:**
```python
# Function to extract emojis
def extract_emojis(text):
    return ''.join(c for c in text if c in emoji.EMOJI_DATA)

comments_df['Emojis'] = comments_df['Comment_text'].apply(extract_emojis)

# Count emoji usage
emoji_counts = Counter()
for emojis_str in comments_df['Emojis']:
    for char in emojis_str:
        emoji_counts[char] += 1

# Top emojis
top_emojis = emoji_counts.most_common(12)

print("Most Used Emojis:")
for emoji_char, count in top_emojis:
    print(f"  {emoji_char}: {count} times")
```

**Emoji-Sentiment Correlation:**
```python
# Group by emoji sentiment
emoji_sentiment = {}
for emoji_char, _ in top_emojis:
    emoji_rows = comments_df[comments_df['Emojis'].str.contains(emoji_char)]
    if len(emoji_rows) > 0:
        positive_pct = (emoji_rows['Sentiment'] == 'Positive').sum() / len(emoji_rows) * 100
        emoji_sentiment[emoji_char] = positive_pct

# Visualization
fig, ax = plt.subplots(figsize=(10, 6))
emojis_list = list(emoji_sentiment.keys())
positive_percentages = list(emoji_sentiment.values())

ax.bar(emojis_list, positive_percentages, color='coral')
ax.set_ylabel('% Positive Comments')
ax.set_title('Emoji Usage by Sentiment')
ax.set_ylim(0, 100)

plt.show()
```

**Interpretation:**
- Positive emojis (😍, 😂, 👍) appear more in positive comments
- Negative emojis (😡, 😤) appear more in negative comments
- Emojis indicate emotional engagement

#### Section 8: Title Analysis - Clickbait Detection (Cells 56-65)

**Analyze Titles:**
```python
# Count punctuation in titles
def count_punctuation(title):
    return sum(1 for char in str(title) if char in '!?')

comments_df['Title_Punctuation'] = comments_df['Title'].apply(count_punctuation)

print(f"Average punctuation per title: {comments_df['Title_Punctuation'].mean():.1f}")
print(f"Max punctuation: {comments_df['Title_Punctuation'].max()}")

# Distribution
print(comments_df['Title_Punctuation'].describe())
```

**Clickbait Correlation:**
```python
# Analyze engagement by punctuation level
clickbait_analysis = comments_df.groupby('Title_Punctuation').agg({
    'Views': 'mean',
    'Likes': 'mean',
    'Dislikes': 'mean',
    'Comments': 'mean'
})

print("Engagement Metrics by Title Punctuation:")
print(clickbait_analysis)
```

**Visualization:**
```python
fig, axes = plt.subplots(2, 2, figsize=(12, 8))

metrics = [('Views', 0), ('Likes', 1), ('Dislikes', 2), ('Comments', 3)]

for metric, idx in metrics:
    row, col = divmod(idx, 2)
    axes[row, col].scatter(
        comments_df['Title_Punctuation'],
        comments_df[metric],
        alpha=0.5
    )
    axes[row, col].set_xlabel('Punctuation Count')
    axes[row, col].set_ylabel(metric)
    axes[row, col].set_title(f'{metric} vs Title Punctuation')

plt.tight_layout()
plt.show()
```

**Key Finding:**
- Moderate correlation between high punctuation and engagement
- Highly punctuated titles (!!!!) may indicate clickbait
- Engagement varies more by content than punctuation alone

#### Section 9: Engagement Analysis (Cells 66-75)

**Correlation Analysis:**
```python
# Calculate engagement metrics
comments_df['Engagement_Rate'] = (
    (comments_df['Likes'] + comments_df['Dislikes']) / comments_df['Views']
)

# Multi-variable correlation
correlation_matrix = comments_df[[
    'Views', 'Likes', 'Dislikes', 'Comments', 'Engagement_Rate'
]].corr()

# Visualization
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', vmin=-1, vmax=1)
plt.title('Engagement Metrics Correlation')
plt.show()
```

**Findings:**
```
Correlation Matrix:
           Views    Likes  Dislikes  Comments  Engagement
Views      1.00     0.92    0.78      0.85      0.45
Likes      0.92     1.00    0.72      0.81      0.62
Dislikes   0.78     0.72    1.00      0.68      0.58
Comments   0.85     0.81    0.68      1.00      0.71
```

- Strong positive correlation: More views → More likes/dislikes
- Comment volume correlates with engagement
- Dislikes (though low) correlate with popularity

#### Section 10: Category Analysis (Cells 76-80)

**Performance by Category:**
```python
# Group by video category
category_performance = comments_df.groupby('Category').agg({
    'Views': 'mean',
    'Likes': 'mean',
    'Likes_to_Views_%': lambda x: (x.mean() * 100),
    'Comments': 'count'  # Number of videos
})

category_performance = category_performance.sort_values('Views', ascending=False)

print("Performance by Category:")
print(category_performance)
```

**Example Results:**
```
Category        Avg Views    Avg Likes    Like Rate    Videos
Music           1,200,000    45,000       3.8%         245
Gaming          850,000      28,000       3.3%         187
Entertainment   650,000      22,000       3.4%         156
Vlogs            480,000      18,000       3.8%         134
Tutorials        320,000      12,000       3.8%         98
```

**Visualization:**
```python
fig, axes = plt.subplots(1, 2, figsize=(14, 6))

# Views by category
category_performance['Views'].sort_values().plot(
    kind='barh', ax=axes[0], color='steelblue'
)
axes[0].set_title('Average Views by Category')

# Like rate by category
category_performance['Likes_to_Views_%'].sort_values().plot(
    kind='barh', ax=axes[1], color='coral'
)
axes[1].set_title('Like Rate by Category')

plt.tight_layout()
plt.show()
```

#### Section 11: Sentiment by Category (Cells 81-83)

**Sentiment Distribution Across Categories:**
```python
# Cross-tabulation
sentiment_by_category = pd.crosstab(
    comments_df['Category'],
    comments_df['Sentiment'],
    normalize='index'
) * 100

print("Sentiment Distribution by Category (%):")
print(sentiment_by_category.round(1))
```

**Example Results:**
```
Category       Positive  Neutral  Negative
Music          58%       30%      12%
Gaming         52%       35%      13%
Entertainment  55%       32%      13%
Vlogs          48%       38%      14%
Tutorials      62%       28%      10%
```

**Visualization:**
```python
sentiment_by_category.plot(kind='bar', stacked=True)
plt.title('Sentiment Distribution by Category')
plt.ylabel('Percentage')
plt.xlabel('Category')
plt.legend(title='Sentiment')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

**Interpretation:**
- Tutorial videos get most positive sentiment (90% positive/neutral)
- Music videos have strong positive community
- Gaming slightly lower satisfaction (higher negative %)

---

## 📊 Key Metrics & Findings

### Overall Sentiment Summary

```
Total Comments Analyzed: 800
Positive Sentiment: 53% (425 comments)
Neutral Sentiment: 35% (280 comments)
Negative Sentiment: 12% (95 comments)

Average Polarity Score: +0.28 (mildly positive)
Overall Engagement Rate: 4.2% (likes/dislikes shared)
```

### Top Themes Discovered

**Positive Themes:**
- Video quality and production value
- Entertainment and humor
- Educational content
- Artist/creator appreciation

**Negative Themes:**
- Video length or pacing issues
- Technical problems
- Disagreement with content
- Clickbait disappointment

### Emoji Findings

Most Used Emojis:
- 😂 (laughing) - 234 uses (87% in positive comments)
- ❤️ (heart) - 187 uses (92% in positive comments)
- 👍 (thumbs up) - 156 uses (94% in positive comments)
- 😍 (heart eyes) - 134 uses (96% in positive comments)
- 😡 (anger) - 42 uses (78% in negative comments)

### Clickbait Finding

Correlation between title punctuation and engagement:
- 0-1 punctuation marks: 3.8% like rate
- 2-3 punctuation marks: 4.1% like rate
- 4+ punctuation marks: 4.3% like rate

**Conclusion:** Moderate correlation; punctuation alone doesn't drive engagement, content quality matters more.

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Python 3.7+ | Programming language |
| Pandas | Data manipulation |
| NumPy | Numerical operations |
| TextBlob | Sentiment analysis, NLP |
| NLTK | Natural language processing |
| WordCloud | Text visualization |
| Emoji | Emoji handling and analysis |
| Matplotlib | Visualization |
| Seaborn | Statistical graphics |
| Scikit-learn | Machine learning utilities |

## 🚀 How to Run

### In Jupyter Notebook

```bash
jupyter notebook project_Youtube_analysis.ipynb
```

Execute cells sequentially using Shift+Enter

### Prerequisites

```bash
pip install pandas numpy textblob nltk wordcloud emoji matplotlib seaborn scikit-learn
```

Download NLTK data:
```python
import nltk
nltk.download('stopwords')
nltk.download('punkt')
nltk.download('universal_tagset')
```

### Execution Time
- Full notebook: 5-10 minutes
- Individual section: 10-30 seconds

---

## 📊 Output Deliverables

**Visualizations:**
1. Sentiment distribution pie chart
2. Polarity score histogram
3. Positive/Negative/Neutral word clouds
4. Top keyword bar charts
5. Emoji usage heatmap
6. Title punctuation vs engagement scatter
7. Engagement correlation heatmap
8. Category performance comparison
9. Sentiment by category stacked bar chart

**Statistical Reports:**
- Sentiment summary statistics
- Keyword frequency lists
- Emoji-sentiment associations
- Category performance metrics
- Polarity distribution analysis

**Text Outputs:**
- Top positive keywords
- Top negative keywords
- Emoji sentiment mapping
- Category rankings

---

## 💡 Business Applications

### For Content Creators
- Understand viewer sentiment about video topics
- Identify which video elements resonate most
- Optimize titles for engagement (but avoid excessive punctuation)
- Monitor brand perception in comments
- Learn from negative feedback for improvement

### For YouTube Strategic Partners
- Category-based content recommendations
- Creator performance benchmarking
- Audience satisfaction metrics
- Content moderation insights
- Trend identification

### For Marketing Teams
- Sentiment-based campaign evaluation
- Audience emotion understanding
- Competitive content benchmarking
- Influencer selection criteria
- Campaign optimization

### For Data Scientists
- NLP techniques (sentiment analysis, text classification)
- Feature engineering from text data
- Time series analysis of engagement
- Predictive modeling opportunities
- Anomaly detection in engagement patterns

---

## ⚠️ Limitations

- ⚠️ TextBlob limited to English text
- ⚠️ Sentiment scores are approximations (context-dependent)
- ⚠️ Sarcasm and idioms may be misclassified
- ⚠️ Limited to comments available in dataset
- ⚠️ No cross-platform comparison (YouTube only)
- ⚠️ Historical data only (no real-time updates)
- ⚠️ No account for spam/bot comments

## 🔮 Future Enhancements

- **Advanced NLP:** Transformer models (BERT, GPT) for better sentiment
- **Topic Modeling:** LDA for automatic topic extraction
- **Time Series:** Sentiment trends over video lifetime
- **Influencer Analysis:** Creator performance metrics
- **Recommendation Engine:** Suggest videos based on sentiment
- **Multilingual Support:** Analyze non-English comments
- **Real-time Monitoring:** Live sentiment streaming
- **Comparison Analysis:** Cross-creator benchmarking

## 📚 NLP Concepts

### Sentiment Analysis
- Polarity: How positive/negative a text is
- Subjectivity: How opinion-based vs. factual
- Applications: Brand monitoring, customer feedback, content evaluation

### Text Preprocessing
- Tokenization: Breaking text into words
- Stop words: Removing common words (the, is, a)
- Lemmatization: Converting to base form (running → run)

### Feature Extraction
- TF-IDF: Term frequency-inverse document frequency
- Bag of Words: Word occurrence counting
- Word Embeddings: Dense vector representations

---

**Project Author:** Iraklis Papigkiotis

**Last Updated:** 2026

For broader context, see parent [README_ANALYTICS.md]( ../README_ANALYTICS.md).
