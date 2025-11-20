# Songwriter Style Analysis

> **Identifying musical authorship through computational stylometry and machine learning**

![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![Status](https://img.shields.io/badge/Status-Complete-success.svg)
![License](https://img.shields.io/badge/License-MIT-green.svg)

## Table of Contents

- [Overview](#overview)
- [Project Motivation](#project-motivation)
- [Dataset](#dataset)
- [Methodology](#methodology)
- [Key Features](#key-features)
- [Results](#results)
- [Project Structure](#project-structure)
- [Installation & Setup](#installation--setup)
- [Usage](#usage)
- [Technologies Used](#technologies-used)
- [Future Improvements](#future-improvements)
- [Author](#author)

---

## Overview

This project applies **Natural Language Processing (NLP)** and **Machine Learning** techniques to identify distinctive writing patterns of top music industry songwriters. By analyzing lyrics across multiple performing artists, the system can predict the songwriter behind a song with **57% accuracy (43% error rate)** - significantly better than random chance (25% accuracy baseline, 75% error rate).

**Domain Applications Project - M.Sc. Data Analytics**

---

## Project Motivation

The music industry has long recognized that successful songwriters have distinctive "fingerprints" in their writing. This project investigates:

- **Can computational methods detect these stylistic signatures?**
- **Do songwriters maintain consistent patterns across different performing artists?**
- **What linguistic features differentiate one songwriter from another?**

### Real-World Applications

- **Music Attribution**: Identifying uncredited songwriters
- **Copyright Analysis**: Detecting stylistic similarities
- **Music Production**: Understanding successful writing patterns
- **Academic Research**: Computational stylometry in creative writing

---

## Dataset

### Data Collection

**Source**: 413 songs collected via Genius API and Last.fm API

**Target Songwriters** (Top 4 analyzed):
- **Jack Antonoff** - 100 songs (indie-pop, emotional storytelling)
- **Max Martin** - 110 songs (pop perfection, catchy hooks)
- **Dr. Luke** - 129 songs (electronic pop, club bangers)
- **Ryan Tedder** - 64 songs (anthemic choruses, piano-driven)

*Note: Stargate was excluded due to insufficient data (only 10 songs)*

### Collection Strategy

- **24 artists per songwriter** (expanded artist lists)
- **Max 30 songs per artist** to ensure diversity
- **Strict credit verification** to ensure data quality
- **API rate limiting** to prevent throttling

### Data Quality Metrics

**Credit Verification**: 100% of songs have verified songwriter credits  
**No Duplicate Songs**: Strict deduplication process  
**Balanced Classes**: 64-129 songs per songwriter  
**Rich Metadata**: Popularity metrics, tags, release dates  

---

## Methodology

### Pipeline Overview

The project follows a **4-stage pipeline**:

```
Data Collection → Preprocessing → Exploratory Analysis → Machine Learning
```

### 1. Data Collection (`01_data_collection.ipynb`)

- **Genius API**: Lyrics and songwriter credits
- **Last.fm API**: Popularity metrics and tags
- **Checkpoint System**: Resume interrupted collections
- **Error Handling**: Rate limiting and API failure recovery

### 2. Preprocessing (`02_preprocessing.ipynb`)

Text cleaning pipeline:
- Remove duplicates and invalid entries
- Clean special characters and formatting
- Tokenization and normalization
- Stopword removal
- Lemmatization
- Filter by sample size (minimum 50 songs)

### 3. Exploratory Analysis (`03_exploratory_analysis.ipynb`)

**Stylometric Features**:
- Word count and vocabulary richness
- Lexical diversity (unique words / total words)
- Average word length
- Short/long word ratios
- Sentiment analysis (polarity & subjectivity)
- Repetition patterns

**Visualizations**:
- Word clouds per songwriter
- Sentiment distribution charts
- Stylometric profiles
- Vocabulary analysis
- Feature correlation heatmaps

### 4. Machine Learning (`04_machine_learning.ipynb`)

**Feature Engineering**:
- **TF-IDF Vectorization**: Word-level and character n-grams
- **Stylometric Features**: 10 quantitative metrics
- **Combined Feature Set**: 2,010 total features

**Models Tested**:
- Logistic Regression (Best: **57.1% accuracy**)
- Random Forest Classifier
- Linear SVC

**Validation**:
- Stratified K-Fold Cross-Validation
- Train/Test Split (80/20)
- Confusion Matrix Analysis
- Feature Importance Rankings

---

## Key Features

### Stylometric Analysis

Each songwriter exhibits distinct patterns:

| Songwriter | Avg Words | Lexical Diversity | Sentiment | Unique Style |
|------------|-----------|-------------------|-----------|--------------|
| **Dr. Luke** | 255 | 0.392 | +0.084 | Longest songs, repetitive |
| **Jack Antonoff** | 178 | 0.464 | +0.068 | Highest diversity, longer words |
| **Max Martin** | 185 | 0.385 | +0.098 | Most positive, structured |
| **Ryan Tedder** | 181 | 0.369 | +0.171 | Very positive, emotional |

### Model Performance

**Best Model**: Logistic Regression

```
Overall Accuracy: 57.1% (Error Rate: 42.9%)
Macro F1-Score:   0.578
Weighted F1:      0.569

Per-Class Performance:
  • Dr. Luke:       65% precision, 59% recall
  • Jack Antonoff:  53% precision, 50% recall
  • Max Martin:     52% precision, 50% recall
  • Ryan Tedder:    59% precision, 77% recall (best)

Confusion Analysis:
  • Dr. Luke ↔ Max Martin: 18% of Dr. Luke songs confused as Max Martin
  • Most distinctive: Ryan Tedder (77% recall, highest)
```

**Baseline**: Random guessing would achieve 25% accuracy / 75% error rate (4 classes)  
**Improvement**: 2.3x better accuracy, 1.7x lower error rate than random

---

## Project Structure

```
songwriter-style-analysis/
│
├── 01_data_collection.ipynb      # API data collection
├── 02_preprocessing.ipynb        # Text cleaning & preparation
├── 03_exploratory_analysis.ipynb # Visualizations & statistics
├── 04_machine_learning.ipynb     # Model training & evaluation
│
├── config_template.py            # API credential template
├── config.py                     # API credentials (not tracked)
├── README.md                     # Project documentation
│
└── data/                         # Generated datasets & models
    ├── songs_data_final.csv          # Raw collected data
    ├── songs_preprocessed.csv        # Cleaned lyrics
    ├── songs_with_features.csv       # With stylometric features
    ├── best_classifier.pkl           # Trained model
    ├── label_encoder.pkl             # Label encoder
    ├── tfidf_vectorizer.pkl          # TF-IDF vectorizer
    ├── classification_report.txt     # Model performance
    ├── confusion_matrix.png          # Confusion matrix plot
    ├── wordclouds.png                # Songwriter word clouds
    ├── sentiment_analysis.png        # Sentiment distributions
    ├── stylometric_profiles.png      # Feature comparisons
    ├── feature_importance.png        # Top predictive features
    └── ...                           # Additional outputs
```

---

## Installation & Setup

### Prerequisites

- Python 3.8+
- pip package manager
- Genius API token
- Last.fm API credentials

### Step 1: Clone Repository

```bash
git clone https://github.com/Aryaman33/songwriter-style-analysis.git
cd songwriter-style-analysis
```

### Step 2: Install Dependencies

```bash
pip install -r requirements.txt
```

**Required packages**:
```
pandas
numpy
matplotlib
seaborn
scikit-learn
nltk
lyricsgenius
pylast
textblob
wordcloud
```

### Step 3: Configure API Credentials

1. Copy `config_template.py` to `config.py`
2. Add your API credentials:

```python
# config.py
GENIUS_TOKEN = "your_genius_api_token_here"
LASTFM_API_KEY = "your_lastfm_api_key_here"
LASTFM_API_SECRET = "your_lastfm_api_secret_here"
```

**Get API Credentials**:
- Genius API: https://genius.com/api-clients
- Last.fm API: https://www.last.fm/api/account/create

### Step 4: Download NLTK Data

```python
import nltk
nltk.download('punkt')
nltk.download('stopwords')
nltk.download('wordnet')
```

---

## Usage

### Run Notebooks Sequentially

#### 1. Collect Data
```bash
jupyter notebook 01_data_collection.ipynb
```
- Collects lyrics from Genius API
- Gathers popularity metrics from Last.fm
- Outputs: `data/songs_data_final.csv`

#### 2. Preprocess Text
```bash
jupyter notebook 02_preprocessing.ipynb
```
- Cleans and normalizes lyrics
- Removes stopwords and lemmatizes
- Outputs: `data/songs_preprocessed.csv`

#### 3. Explore Data
```bash
jupyter notebook 03_exploratory_analysis.ipynb
```
- Generates visualizations
- Calculates stylometric features
- Outputs: Charts, word clouds, feature CSV

#### 4. Train Models
```bash
jupyter notebook 04_machine_learning.ipynb
```
- Trains multiple classifiers
- Evaluates performance
- Outputs: Trained models and metrics

### Quick Prediction (Using Saved Model)

```python
import pickle
import pandas as pd

# Load model and vectorizer
model = pickle.load(open('data/best_classifier.pkl', 'rb'))
vectorizer = pickle.load(open('data/tfidf_vectorizer.pkl', 'rb'))
encoder = pickle.load(open('data/label_encoder.pkl', 'rb'))

# Predict songwriter
def predict_songwriter(lyrics):
    lyrics_processed = preprocess_lyrics(lyrics)  # Use preprocessing function
    features = vectorizer.transform([lyrics_processed])
    prediction = model.predict(features)
    songwriter = encoder.inverse_transform(prediction)[0]
    confidence = model.predict_proba(features).max()
    return songwriter, confidence

# Example usage
songwriter, confidence = predict_songwriter("Your song lyrics here")
print(f"Predicted: {songwriter} ({confidence:.1%} confidence)")
```

---

## Technologies Used

### APIs & Data Sources
- **Genius API** - Lyrics and metadata
- **Last.fm API** - Popularity metrics

### Data Processing
- **Pandas** - Data manipulation
- **NumPy** - Numerical operations
- **NLTK** - Natural language processing
- **TextBlob** - Sentiment analysis

### Machine Learning
- **scikit-learn** - ML models and evaluation
- **TF-IDF Vectorization** - Text feature extraction
- **Logistic Regression** - Best performing model

### Visualization
- **Matplotlib** - Plotting
- **Seaborn** - Statistical visualizations
- **WordCloud** - Word cloud generation

---

## Future Improvements

### Data Enhancement
- [ ] Expand to 150+ songs per songwriter (current: 64-129)
- [ ] Include more songwriters (current: 4)
- [ ] Add temporal analysis (style evolution over time)
- [ ] Incorporate melody/chord progression data

### Feature Engineering
- [ ] Rhyme scheme patterns
- [ ] Part-of-speech (POS) tag distributions
- [ ] Named entity recognition
- [ ] Topic modeling (LDA)
- [ ] Phonetic features

### Model Improvements
- [ ] Deep learning models (LSTM, BERT)
- [ ] Ensemble methods
- [ ] Multi-label classification (co-writers)
- [ ] Confidence thresholds for predictions

### Application Development
- [ ] Web interface for predictions
- [ ] API endpoint for real-time classification
- [ ] Chrome extension for Genius/Spotify
- [ ] Batch processing tool

---

## Author

**Aryaman Patel**  
M.Sc. Data Analytics Student

**GitHub**: [@Aryaman33](https://github.com/Aryaman33)  
**Project**: Domain Applications - Computational Stylometry

---

## License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## Acknowledgments

- **Genius.com** for lyrics API
- **Last.fm** for music metadata API
- **scikit-learn** community for ML tools
- Music industry songwriters for their artistry

---

## References

- Computational Stylometry Research
- Natural Language Processing Techniques
- Music Information Retrieval (MIR)
- Text Classification Methods

---

**If you find this project interesting, please star the repository!**