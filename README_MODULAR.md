# Songwriter Style Analysis - Modular Notebooks

## Project Overview

This project uses Machine Learning and Natural Language Processing to analyze and identify distinctive writing styles of professional songwriters. The analysis has been divided into 4 focused notebooks for better organization and reproducibility.

---

## Notebook Structure

### 01_data_collection.ipynb
**Purpose**: Enhanced data collection from Genius and Last.fm APIs

**Key Features**:
- Collects 50+ songs per songwriter (increased from 25)
- Supports 12 songwriters with expanded artist lists
- Includes checkpoint saving for long collection sessions
- Rate-limit friendly with delays and error handling
- Extracts lyrics, writer credits, and popularity metrics

**Runtime**: 2-3 hours (depending on API response times)

**Output**: 
- `data/songs_data_final.csv` (600+ songs)
- `data/collection_metadata.json`
- Checkpoint files for each songwriter

---

### 02_preprocessing.ipynb
**Purpose**: Clean and prepare lyrics for analysis

**Key Features**:
- Remove duplicates and noise
- Text cleaning (lowercase, remove special characters)
- Tokenization and lemmatization
- Stopword removal
- Filter by minimum sample size
- Remove too-short lyrics

**Runtime**: 5-10 minutes

**Output**:
- `data/songs_preprocessed.csv` (cleaned dataset)

---

### 03_exploratory_analysis.ipynb
**Purpose**: Visualize and analyze songwriter characteristics

**Key Features**:
- Calculate stylometric features (8 metrics)
- Sentiment analysis (polarity, subjectivity)
- Generate word clouds per songwriter
- Create sentiment visualizations
- Produce stylometric profile charts
- Statistical summaries

**Runtime**: 10-15 minutes

**Outputs**:
- `data/songs_with_features.csv` (dataset with all features)
- `data/wordclouds.png`
- `data/sentiment_visualization.png`
- `data/stylometric_profiles.png`
- `data/vocabulary_analysis.csv`
- `data/sentiment_analysis.csv`
- `data/eda_summary.csv`

---

### 04_machine_learning.ipynb
**Purpose**: Train classification models for songwriter attribution

**Key Features**:
- TF-IDF vectorization (word + character n-grams)
- Combined feature engineering (2012 features total)
- Train multiple models (Logistic Regression, SVC, Random Forest)
- Hyperparameter comparison
- Feature importance analysis
- Confusion matrix visualization
- Interactive prediction tool

**Runtime**: 15-20 minutes

**Outputs**:
- `data/best_classifier.pkl` (trained model)
- `data/tfidf_word_vectorizer.pkl`
- `data/tfidf_char_vectorizer.pkl`
- `data/label_encoder.pkl`
- `data/confusion_matrix.png`
- `data/feature_importance.png`
- `data/classification_report.txt`
- `data/model_metadata.json`

**Expected Accuracy**: 50-60% (with 600+ songs, improved from 38.1%)

---

## Quick Start

### 1. Install Dependencies
```bash
pip install lyricsgenius pylast pandas numpy matplotlib seaborn wordcloud textblob scikit-learn nltk
```

### 2. Configure API Credentials
Edit `config.py` with your API tokens:
```python
GENIUS_TOKEN = "your_genius_token_here"
LASTFM_API_KEY = "your_lastfm_key_here"
LASTFM_API_SECRET = "your_lastfm_secret_here"
```

### 3. Run Notebooks in Order
1. **01_data_collection.ipynb** - Collect song data (2-3 hours)
2. **02_preprocessing.ipynb** - Clean and prepare data (5-10 min)
3. **03_exploratory_analysis.ipynb** - Analyze and visualize (10-15 min)
4. **04_machine_learning.ipynb** - Train models and predict (15-20 min)

---

## Improvements Over Original

### Enhanced Data Collection
- **50+ songs per songwriter** (up from 25)
- **8 artists per songwriter** (up from 4)
- Better error handling and rate limiting
- Checkpoint system for recovery
- More comprehensive metadata extraction

### Clean Code
- No emojis or unnecessary formatting
- Professional comments and documentation
- Clear function names and structure
- Consistent naming conventions

### Better Organization
- Modular design (4 focused notebooks vs 1 monolithic)
- Each notebook has clear inputs/outputs
- Easy to re-run individual stages
- Better for debugging and iteration

### Expected Performance
With 600+ songs (vs 102 in original):
- **Accuracy**: 50-60% (up from 38.1%)
- **Better class balance**: 50+ songs per class (vs 12-25)
- **More robust features**: Larger vocabulary, better patterns
- **Reduced overfitting**: More diverse training examples

---

## Target Songwriters (12 Total)

1. **Max Martin** - Pop hits, catchy hooks
2. **Sia Furler** - Powerful vocals, emotional lyrics
3. **Ryan Tedder** - Anthemic choruses, piano-driven
4. **Julia Michaels** - Personal, confessional lyrics
5. **The-Dream** - R&B melodies, romantic themes
6. **Jack Antonoff** - 80s-inspired production, indie-pop
7. **Shellback** - Pop production, Max Martin collaborator
8. **Benny Blanco** - Pop and hip-hop fusion
9. **Stargate** - R&B and pop production
10. **Dr. Luke** - Pop hits, electronic production
11. **Greg Kurstin** - Alternative pop, diverse production
12. **Diplo** - Electronic, dancehall, pop fusion

---

## Feature Engineering

### Text Features (2000)
- **Word TF-IDF** (1000): Unigrams and bigrams, vocabulary patterns
- **Character TF-IDF** (1000): 3-5 character n-grams, stylistic patterns

### Stylometric Features (8)
- Word count and unique word count
- Lexical diversity (vocabulary richness)
- Average word length and variance
- Short/long/very-long word ratios

### Sentiment Features (4)
- Sentiment polarity (positive/negative)
- Sentiment subjectivity (objective/subjective)
- Positive/negative word ratios

**Total**: 2,012 features per song

---

## File Structure

```
songwriter-style-analysis/
├── 01_data_collection.ipynb      # Enhanced data collection
├── 02_preprocessing.ipynb         # Text cleaning
├── 03_exploratory_analysis.ipynb # Visualizations
├── 04_machine_learning.ipynb     # ML models
├── config.py                      # API credentials
├── config_template.py             # Template for config
├── README.md                      # This file
├── data/                          # All outputs
│   ├── songs_data_final.csv       # Raw collected data
│   ├── songs_preprocessed.csv     # Cleaned data
│   ├── songs_with_features.csv    # Data with features
│   ├── best_classifier.pkl        # Trained model
│   ├── tfidf_word_vectorizer.pkl  # Word vectorizer
│   ├── tfidf_char_vectorizer.pkl  # Char vectorizer
│   ├── label_encoder.pkl          # Label encoder
│   ├── wordclouds.png            # Visualizations
│   ├── sentiment_visualization.png
│   ├── stylometric_profiles.png
│   ├── confusion_matrix.png
│   ├── feature_importance.png
│   └── *.csv                      # Analysis summaries
└── songwriter_style_analysis.ipynb # Original monolithic notebook
```

---

## Using the Trained Model

After running all notebooks, you can use the interactive prediction tool:

```python
# In 04_machine_learning.ipynb
result = predict_songwriter("""
Your song lyrics here...
""")

print(f"Predicted: {result['predicted_songwriter']}")
print(f"Confidence: {result['confidence']:.1%}")
```

Or load the saved model separately:

```python
import pickle
import numpy as np
from scipy.sparse import hstack

# Load model and vectorizers
with open('data/best_classifier.pkl', 'rb') as f:
    model = pickle.load(f)

with open('data/tfidf_word_vectorizer.pkl', 'rb') as f:
    tfidf_word = pickle.load(f)

with open('data/tfidf_char_vectorizer.pkl', 'rb') as f:
    tfidf_char = pickle.load(f)

with open('data/label_encoder.pkl', 'rb') as f:
    label_encoder = pickle.load(f)

# Use for predictions
# (see 04_machine_learning.ipynb for full prediction function)
```

---

## Troubleshooting

### API Rate Limits
- **Genius**: 1.5s delay between songs, 3s between artists
- **Last.fm**: Usually no issues, but errors are handled gracefully
- If rate limited: Script waits 60s automatically

### Low Accuracy
- Collect more songs (aim for 50+ per songwriter)
- Focus on solo-written songs (fewer collaborators)
- Add more songwriters for balanced dataset
- Experiment with different features/models

### Memory Issues
- Process data in smaller batches
- Reduce `max_features` in TF-IDF vectorizers
- Use sparse matrices (already implemented)

---

## Next Steps for Improvement

1. **More Data**: Collect 100+ songs per songwriter
2. **Solo Songs**: Filter for sole-writer credits
3. **Deep Learning**: Implement LSTM/Transformer models
4. **Audio Features**: Add melody, rhythm, chord patterns
5. **Topic Modeling**: Extract thematic patterns
6. **Temporal Analysis**: Track style evolution over time

---

## Citation

If you use this code for research or academic purposes, please cite:

```
Songwriter Style Analysis using Machine Learning
Natural Language Processing and TF-IDF Feature Engineering
2025
```

---

## License

MIT License - Feel free to use and modify for your projects!

---

## Contact

For questions or suggestions about the modular notebooks, please open an issue on GitHub.
