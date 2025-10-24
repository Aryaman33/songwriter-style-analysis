# COMPLETE METHOD & CELL SUMMARY
## Songwriter Style Analysis Project

---

## NOTEBOOK STRUCTURE OVERVIEW

The project consists of **40 cells** organized into 6 major phases:

1. **Setup & Configuration** (Cells 1-7)
2. **Data Collection** (Cells 8-12)
3. **Data Preprocessing** (Cells 13-17)
4. **Exploratory Data Analysis** (Cells 18-23)
5. **Feature Engineering** (Cells 24-28)
6. **Machine Learning** (Cells 29-40)

---

# PHASE 1: SETUP & CONFIGURATION

## Cell 1: Project Overview (Markdown)
**Purpose**: Documentation header explaining project objectives, methods, and expected outcomes

**Content**:
- Project description
- 12 target songwriters
- Technical stack summary
- Dataset statistics
- Feature engineering overview

---

## Cell 2: Import Essential Libraries
**Purpose**: Load core Python libraries for data handling

**Libraries**:
- `lyricsgenius` - Genius API client
- `pandas` - Data manipulation
- `numpy` - Numerical operations
- `warnings` - Suppress non-critical warnings

**Output**: Confirmation message

---

## Cell 3: Configure Genius API
**Purpose**: Initialize Genius API client with credentials

**Configuration**:
- Import `GENIUS_TOKEN` from config.py
- Create `genius` client object
- Set parameters: `skip_non_songs=True`, `remove_section_headers=True`, `verbose=False`

**Output**: Success confirmation

---

## Cell 4: Force Config Reload and Token Verification
**Purpose**: Handle config updates without restarting kernel

**Methods Used**:
- `sys.modules` - Clear cached config module
- `importlib` - Reload fresh configuration

**Output**: Token verification (first 30 characters + length)

---

## Cell 5: Test Genius API Connection
**Purpose**: Verify API credentials work correctly

**Test Process**:
1. Search for "Blinding Lights" by The Weeknd
2. Extract metadata (title, artist, release date, URL)
3. Extract writer and producer credits from `_body` metadata
4. Display lyrics preview (first 200 characters)

**Methods**:
- `genius.search_song(title, artist)` - API search
- `hasattr()` - Check for metadata attributes
- `song._body` - Access detailed metadata

**Output**: Song details and lyrics preview

---

## Cell 6: Last.fm API Configuration
**Purpose**: Initialize Last.fm API for popularity metrics

**Libraries**:
- `pylast` - Last.fm API client
- `requests`, `json` - HTTP handling

**Configuration**:
- Import `LASTFM_API_KEY`, `LASTFM_API_SECRET`
- Create `lastfm_network` object

**Output**: Success confirmation

---

## Cell 7: Test Last.fm API Connection
**Purpose**: Verify Last.fm API functionality

**Test Process**:
1. Get track info for "Blinding Lights"
2. Retrieve playcount and listener count
3. Get top tags/genres (limit 5)
4. Get artist info and similar artists

**Methods**:
- `lastfm_network.get_track(artist, title)`
- `track.get_playcount()`
- `track.get_listener_count()`
- `track.get_top_tags(limit=5)`
- `artist.get_similar(limit=3)`

**Output**: Track and artist statistics

---

# PHASE 2: DATA COLLECTION

## Cell 8: Define Target Songwriters
**Purpose**: Configure list of 12 songwriters to analyze

**Data Structure**:
```python
target_writers = {
    'Songwriter Name': {
        'known_for': 'Style description',
        'notable_artists': ['Artist 1', 'Artist 2', ...]
    }
}
```

**Songwriters**: Max Martin, Sia Furler, Ryan Tedder, Julia Michaels, The-Dream, Jack Antonoff, Shellback, Benny Blanco, Stargate, Dr. Luke, Greg Kurstin, Diplo

**Output**: List of 12 songwriters with their characteristics

---

## Cell 9: Test Data Collection with Writer Credits
**Purpose**: Verify songwriter credit extraction works

**Test Songs**: "Blinding Lights", "Umbrella", "Counting Stars"

**Methods**:
- `genius.search_song(title, artist)`
- Extract `writer_artists` from `song._body`
- Extract `producer_artists` from `song._body`
- `lastfm_network.get_track()` for playcount
- Create dictionary with all metadata

**Output**: DataFrame preview of 3 test songs

---

## Cell 10: Quick Diagnostic - Songwriter Detection
**Purpose**: Test if target songwriter names match Genius credits

**Test Case**: Max Martin on "Blinding Lights"

**Logic**:
1. Search song on Genius
2. Extract writer credits
3. Check if "Max Martin" in credits (case-insensitive)
4. Report match/mismatch

**Output**: Diagnostic report (success/warning)

---

## Cell 11: Full Data Collection
**Purpose**: Collect 25 songs per songwriter (12 songwriters = ~300 songs)

**Algorithm**:
```
FOR each songwriter:
    FOR each artist they've worked with:
        Search artist songs on Genius
        FOR each song:
            Get full metadata
            Check if target songwriter credited
            IF credited:
                Get Last.fm data
                Save song data
                BREAK if 25 songs collected
        Add delay between artists (rate limiting)
    Save checkpoint file
```

**Rate Limiting**:
- 1.5 seconds between songs
- 3 seconds between artists
- 60 seconds after rate limit error

**Methods**:
- `genius.search_artist(name, max_songs=30, sort="popularity")`
- `lastfm_network.get_track(artist, title)`
- `lastfm_track.get_playcount()`
- `lastfm_track.get_top_tags(limit=3)`

**Checkpoint System**:
- Saves `data/checkpoint_{songwriter}.csv` after each songwriter
- Allows recovery if collection interrupted

**Output**: 
- ~300 songs total
- Progress updates during collection
- Checkpoint files per songwriter

---

## Cell 12: Save Collected Dataset
**Purpose**: Save final dataset and verify quality

**Process**:
1. Create DataFrame from collected songs
2. Display summary statistics
3. Save to CSV: `data/songs_data_final.csv`
4. Save to JSON: `data/songs_data_final.json`
5. Create backup: `data/songs_data_backup.csv`

**Quality Checks**:
- Total songs collected
- Songs per songwriter distribution
- Missing values count
- Average lyrics length

**Output**: Dataset saved with statistics

---

# PHASE 3: DATA PREPROCESSING

## Cell 13: Load Collected Data
**Purpose**: Reload saved dataset for preprocessing

**Methods**:
- `pd.read_csv('data/songs_data_final.csv')`

**Validation**:
- Check shape and columns
- Display first few rows
- Show songwriter distribution

**Output**: Dataset overview

---

## Cell 14: Download NLTK Resources
**Purpose**: Ensure all required NLP resources available

**Function**: `ensure_nltk_resource(resource)`
- Checks if resource exists with `nltk.data.find()`
- Downloads if missing with `nltk.download()`

**Resources Downloaded**:
- `tokenizers/punkt` - Sentence/word tokenization
- `tokenizers/punkt_tab` - Updated tokenizer data
- `corpora/stopwords` - Common English stopwords
- `corpora/wordnet` - Lexical database
- `taggers/averaged_perceptron_tagger` - POS tagging
- `corpora/omw-1.4` - Multilingual wordnet

**Output**: Download confirmation for each resource

---

## Cell 15: Import NLP Libraries
**Purpose**: Load text processing and visualization tools

**Libraries**:
- `re`, `string` - Regular expressions and string operations
- `collections.Counter` - Frequency counting
- `nltk` modules:
  - `stopwords` - Common words to remove
  - `word_tokenize`, `sent_tokenize` - Tokenization
  - `WordNetLemmatizer` - Word normalization
- `textblob.TextBlob` - Sentiment analysis
- `matplotlib.pyplot`, `seaborn` - Visualization

**Plot Configuration**:
- Style: "whitegrid"
- Default figure size: (12, 6)

**Output**: Library confirmation

---

## Cell 16: Define Text Cleaning Functions
**Purpose**: Create reusable text preprocessing functions

### Function: `clean_lyrics(text)`
**Purpose**: Basic text cleaning
**Steps**:
1. Convert to lowercase
2. Remove URLs (`http`, `www`, `https`)
3. Fix unicode characters (smart quotes, ellipsis)
4. Remove song structure markers `[Verse]`, `[Chorus]`
5. Remove extra whitespace

### Function: `tokenize_text(text)`
**Purpose**: Split text into words
**Method**: `word_tokenize(text)` from NLTK

### Function: `remove_stopwords(tokens)`
**Purpose**: Remove common English words
**Method**: Filter tokens against `stopwords.words('english')`

### Function: `lemmatize_tokens(tokens)`
**Purpose**: Reduce words to base form (e.g., "running" → "run")
**Method**: `WordNetLemmatizer().lemmatize(word)`

### Function: `preprocess_lyrics(text)`
**Purpose**: Complete preprocessing pipeline
**Steps**:
1. Clean text with `clean_lyrics()`
2. Tokenize with `tokenize_text()`
3. Remove punctuation
4. Remove stopwords
5. Lemmatize tokens
6. Remove single-character tokens

**Output**: Function definitions confirmed

---

## Cell 17: Test Text Cleaning
**Purpose**: Verify preprocessing functions work correctly

**Test Process**:
1. Select first song from dataset
2. Display original lyrics (first 300 chars)
3. Apply `clean_lyrics()` and show result
4. Apply full `preprocess_lyrics()` and show tokens

**Output**: Side-by-side comparison of original vs processed

---

## Cell 18: Apply Preprocessing to All Songs
**Purpose**: Process entire dataset

**Process**:
1. Apply `clean_lyrics()` to all songs → `lyrics_cleaned` column
2. Apply `preprocess_lyrics()` to all → `lyrics_processed` column
3. Calculate metrics:
   - `word_count` - Total tokens after processing
   - `unique_words` - Count of unique tokens
   - `lexical_diversity` - unique_words / word_count
4. Filter out songs with < 10 words
5. Save to `data/songs_preprocessed.csv`

**Quality Check**:
- Songs before filtering
- Songs after filtering
- Songs per songwriter after filtering

**Output**: Processed dataset saved

---

# PHASE 4: EXPLORATORY DATA ANALYSIS (EDA)

## Cell 19: Reload Preprocessed Data
**Purpose**: Load cleaned dataset for analysis

**Methods**:
- `pd.read_csv('data/songs_preprocessed.csv')`
- Convert `lyrics_processed` from string to list
- Create `df_clean` DataFrame

**Output**: Dataset overview with shape and columns

---

## Cell 20: Generate Word Clouds
**Purpose**: Visualize most common words per songwriter

**Method**: `WordCloud` library

**Process**:
1. For each songwriter:
   - Combine all processed lyrics
   - Generate word cloud with 100 words
   - Plot in subplot grid
2. Save as `data/wordclouds.png`

**Configuration**:
- Width: 600px, Height: 400px
- Background: white
- Colormap: 'viridis'
- Max words: 100

**Output**: Multi-panel word cloud image

---

## Cell 21: Vocabulary Analysis
**Purpose**: Compare vocabulary usage across songwriters

**Metrics Calculated**:
- Total words per song (average)
- Unique words per song (average)
- Vocabulary richness (unique/total * 100)
- Average word length

**Methods**:
- `groupby('songwriter').agg()` - Group statistics
- Bar plots for comparison

**Visualization**: 3-panel figure
1. Average words per song
2. Vocabulary richness percentage
3. Average word length

**Output**: 
- `data/vocabulary_analysis.png`
- Statistical summary

---

## Cell 22: Sentiment Analysis
**Purpose**: Analyze emotional tone of lyrics

**Method**: TextBlob sentiment analysis

**Metrics**:
- `sentiment_polarity` - Positive/negative scale (-1 to +1)
- `sentiment_subjectivity` - Objective/subjective scale (0 to 1)

**Process**:
1. Apply TextBlob to each song's cleaned lyrics
2. Calculate average sentiment per songwriter
3. Categorize songs: negative, neutral, positive
4. Create visualizations

**Visualizations**: 2-panel figure
1. Average sentiment polarity by songwriter (bar chart)
2. Sentiment category distribution (stacked bar)

**Output**: `data/sentiment_analysis.png`

---

## Cell 23: Rhyme and Repetition Analysis
**Purpose**: Measure how repetitive lyrics are

### Function: `calculate_repetition_rate(tokens)`
**Formula**: `1 - (unique_words / total_words)`
**Range**: 0 (no repetition) to 1 (high repetition)

### Function: `count_repeated_phrases(text, min_length=3)`
**Purpose**: Count phrases repeated 2+ times
**Method**: Create sliding window of 3-word phrases, count duplicates

**Visualization**: 2-panel figure
1. Average repetition rate by songwriter
2. Average repeated phrases by songwriter

**Output**: `data/repetition_analysis.png`

---

## Cell 24: EDA Summary Report
**Purpose**: Create comprehensive songwriter profiles

**Metrics per Songwriter**:
- Total songs
- Average words per song
- Vocabulary richness (%)
- Average sentiment
- Average subjectivity
- Average word length

**Output**: `data/eda_summary.csv`

---

## Cell 25: Signature Vocabulary Analysis
**Purpose**: Find unique words that define each songwriter

**Method**: TF-IDF (Term Frequency-Inverse Document Frequency)

**Process**:
1. Combine all songs per songwriter into one document
2. Apply TF-IDF vectorization (max 200 features)
3. Extract top 20 words per songwriter based on TF-IDF score
4. Display top 12 signature words per songwriter

**Algorithm**:
```python
TfidfVectorizer(max_features=200, min_df=1, max_df=0.9)
```

**Output**: 
- Signature words printed for each songwriter
- `data/signature_words.pkl` (saved for ML)

---

## Cell 26: Stylometric Fingerprinting
**Purpose**: Calculate writing style characteristics

### Function: `calculate_stylometric_features(lyrics_list)`
**Features Extracted**:
1. **Lexical diversity** - Unique words / total words (vocabulary richness)
2. **Average word length** - Mean characters per word
3. **Word length variance** - Variation in word lengths
4. **Short word ratio** - Words ≤ 3 characters
5. **Long word ratio** - Words ≥ 7 characters
6. **Very long word ratio** - Words ≥ 10 characters

**Visualization**: 6-panel heatmap
- Shows each metric per songwriter
- Color intensity indicates value

**Output**: 
- `data/stylometric_profiles.csv`
- Heatmap visualization

---

# PHASE 5: FEATURE ENGINEERING

## Cell 27: Prepare ML Dataset
**Purpose**: Filter dataset for machine learning

**Filtering Criteria**:
- Minimum 12 songs per songwriter (for balanced training)

**Process**:
1. Count songs per songwriter
2. Keep only songwriters with ≥12 songs
3. Create `df_ml` DataFrame

**Result**: Usually reduces to 5-7 songwriters with sufficient data

**Output**: Filtered dataset shape and distribution

---

## Cell 28: Extract TF-IDF Features (Word-Level)
**Purpose**: Convert lyrics to numerical features using word frequency

**Method**: `TfidfVectorizer` from scikit-learn

**Configuration**:
- `max_features=1000` - Top 1000 most important words
- `ngram_range=(1, 2)` - Unigrams and bigrams
- `min_df=2` - Word must appear in at least 2 songs
- `max_df=0.8` - Exclude words in >80% of songs
- `analyzer='word'` - Word-level analysis

**Formula**: 
```
TF-IDF = (word_frequency_in_document) × log(total_documents / documents_containing_word)
```

**Output**:
- Sparse matrix (rows=songs, columns=1000 word features)
- Feature names: `tfidf_w_[word]`
- Saved: `data/tfidf_word_vectorizer.pkl`

---

## Cell 29: Extract TF-IDF Features (Character-Level)
**Purpose**: Capture stylistic patterns beyond word choice

**Configuration**:
- `max_features=1000` - Top 1000 character patterns
- `ngram_range=(3, 5)` - 3, 4, and 5 character sequences
- `analyzer='char'` - Character-level analysis
- `min_df=2` - Pattern must appear in 2+ songs

**Why Character N-grams?**
- Captures rhyme schemes (e.g., "ight", "ove")
- Captures suffix patterns (e.g., "ing", "tion")
- More robust to vocabulary variations

**Output**:
- Sparse matrix (rows=songs, columns=1000 char features)
- Feature names: `tfidf_c_[pattern]`
- Saved: `data/tfidf_char_vectorizer.pkl`

---

## Cell 30: Extract Stylometric Features
**Purpose**: Calculate writing style metrics for each song

### Function: `extract_stylometric_features(lyrics_tokens)`
**Returns Dictionary with 8 Features**:
1. `word_count` - Total words
2. `unique_word_count` - Unique words
3. `lexical_diversity` - unique/total ratio
4. `avg_word_length` - Mean characters per word
5. `std_word_length` - Standard deviation of word lengths
6. `short_word_ratio` - Proportion ≤3 chars
7. `long_word_ratio` - Proportion ≥7 chars
8. `very_long_word_ratio` - Proportion ≥10 chars

**Output**: DataFrame with 8 stylometric columns

---

## Cell 31: Extract Sentiment Features
**Purpose**: Calculate emotional characteristics

### Function: `extract_sentiment_features(cleaned_text)`
**Returns Dictionary with 4 Features**:
1. `sentiment_polarity` - Positive/negative (-1 to +1)
2. `sentiment_subjectivity` - Objective/subjective (0 to 1)
3. `positive_word_ratio` - Frequency of positive words
4. `negative_word_ratio` - Frequency of negative words

**Method**: TextBlob sentiment analysis

**Positive Words**: love, good, happy, beautiful, joy, wonderful, amazing, great, best, forever
**Negative Words**: hate, bad, sad, ugly, pain, terrible, awful, worst, hurt, cry

**Output**: DataFrame with 4 sentiment columns

---

## Cell 32: Combine All Features
**Purpose**: Create final feature matrix for ML

**Feature Combination**:
1. TF-IDF Word features (1000)
2. TF-IDF Character features (1000)
3. Stylometric features (8)
4. Sentiment features (4)

**Total Features**: 2,012

**Process**:
1. Reset all indices to ensure alignment
2. Concatenate horizontally with `pd.concat(axis=1)`
3. Encode target labels with `LabelEncoder`
4. Create X (features) and y (labels) arrays
5. Save to files

**Files Saved**:
- `data/X_features.npy` - Feature matrix (n_songs × 2012)
- `data/y_labels.npy` - Target labels (n_songs)
- `data/label_encoder.pkl` - Label mapping
- `data/song_metadata.csv` - Song info with labels

**Output**: Feature matrix shape and label mapping

---

# PHASE 6: MACHINE LEARNING

## Cell 33: Load Features and Split Data
**Purpose**: Prepare data for model training

**Process**:
1. Load X and y from .npy files
2. Perform stratified train-test split
   - Test size: 20%
   - Random state: 42 (reproducibility)
   - Stratify: Maintain class distribution

**Stratified Split**: Ensures each songwriter represented proportionally in train/test

**Output**:
- Training set: 80% of data
- Test set: 20% of data
- Class distribution in each set

---

## Cell 34: Train Multiple ML Models
**Purpose**: Compare different algorithms

**Models Trained**:

### 1. Logistic Regression
- Multi-class linear classifier
- Regularization: L2 (default)
- Max iterations: 1000
- Class weight: 'balanced' (handles imbalance)

### 2. Linear SVC (Support Vector Classifier)
- Linear SVM for multi-class
- Max iterations: 1000
- Class weight: 'balanced'
- Loss: hinge (default)

### 3. Random Forest Classifier
- Ensemble of 100 decision trees
- Class weight: 'balanced'
- Random state: 42
- n_jobs: -1 (parallel processing)

**Evaluation Metrics**:
- Accuracy: Correct predictions / total
- F1-score (macro): Average F1 across classes (unweighted)
- F1-score (weighted): Weighted by class frequency

**Output**: Performance comparison table

---

## Cell 35: Hyperparameter Tuning (Random Forest)
**Purpose**: Optimize best model

**Method**: `GridSearchCV` with stratified k-fold cross-validation

**Hyperparameters Tuned**:
- `n_estimators`: [50, 100, 200] - Number of trees
- `max_depth`: [None, 10, 20, 30] - Tree depth limit
- `min_samples_split`: [2, 5, 10] - Minimum samples to split node
- `min_samples_leaf`: [1, 2, 4] - Minimum samples in leaf

**Cross-Validation**: 5-fold stratified CV

**Process**:
1. Define parameter grid
2. Run GridSearchCV (tests all combinations)
3. Find best parameters
4. Retrain with best parameters
5. Evaluate on test set

**Output**: 
- Best parameters found
- Best cross-validation score
- Test set accuracy

---

## Cell 36: Feature Importance Analysis
**Purpose**: Understand which features matter most

**Method**: Random Forest `feature_importances_` attribute

**Process**:
1. Extract importance scores for all 2,012 features
2. Group by feature category:
   - Word TF-IDF (features 0-999)
   - Character TF-IDF (features 1000-1999)
   - Stylometric (features 2000-2007)
   - Sentiment (features 2008-2011)
3. Sum importance within each category
4. Visualize top 20 individual features

**Key Finding**: Character-level features typically most important (85%+)

**Output**:
- `data/feature_importance.csv`
- Bar chart visualization
- Category breakdown

---

## Cell 37: Generate Classification Report
**Purpose**: Detailed per-class performance metrics

**Metrics per Songwriter**:
- **Precision**: Of predicted X, how many actually X?
- **Recall**: Of actual X, how many predicted X?
- **F1-score**: Harmonic mean of precision & recall
- **Support**: Number of songs in test set

**Overall Metrics**:
- Macro average: Unweighted mean across classes
- Weighted average: Weighted by class frequency

**Output**: 
- Formatted classification report
- Saved to `data/classification_report.txt`

---

## Cell 38: Confusion Matrix Visualization
**Purpose**: See which songwriters are confused

**Matrix Structure**:
- Rows: True labels
- Columns: Predicted labels
- Diagonal: Correct predictions
- Off-diagonal: Misclassifications

**Method**: `confusion_matrix()` from scikit-learn

**Visualization**: Heatmap with counts

**Insights**:
- High diagonal values = good performance
- Off-diagonal clusters = similar styles confused
- Example: Max Martin vs Dr. Luke often confused (collaborators)

**Output**: `data/confusion_matrix.png`

---

## Cell 39: Save Trained Model
**Purpose**: Persist best model for future use

**Files Saved**:
- `data/best_classifier.pkl` - Trained Random Forest model
- `data/model_metadata.json` - Performance metrics and config

**Method**: `joblib.dump()` for serialization

**Metadata Includes**:
- Model type
- Accuracy, F1 scores
- Number of features
- Class names
- Training date

**Output**: Confirmation of saved files

---

## Cell 40: Interactive Prediction Tool
**Purpose**: Test model with custom lyrics

### Function: `predict_songwriter(lyrics)`
**Parameters**: Raw song lyrics (string)

**Process**:
1. **Preprocess lyrics**:
   - Clean with `clean_lyrics()`
   - Tokenize and lemmatize with `preprocess_lyrics()`

2. **Extract features**:
   - TF-IDF word features (using saved vectorizer)
   - TF-IDF character features
   - Stylometric features (8 metrics)
   - Sentiment features (4 metrics)

3. **Predict**:
   - Apply trained model
   - Get class probabilities (if available)
   - Return predicted songwriter

**Returns Dictionary**:
```python
{
    'predicted_songwriter': 'Name',
    'confidence': 0.XX,
    'all_probabilities': {
        'Songwriter 1': 0.XX,
        'Songwriter 2': 0.XX,
        ...
    }
}
```

**Usage Example**:
```python
result = predict_songwriter("""
    Your song lyrics here
""")
print(result['predicted_songwriter'])
print(result['confidence'])
```

**Interactive Cell**: Allows pasting lyrics for real-time prediction

**Output**: 
- Predicted songwriter
- Confidence score
- Probability distribution bar chart

---

# SUMMARY OF KEY METHODS

## Data Collection Methods
1. **Genius API**:
   - `genius.search_song(title, artist)` - Find song
   - `genius.search_artist(name, max_songs)` - Get artist discography
   - `song._body['writer_artists']` - Extract writers
   - `song._body['producer_artists']` - Extract producers

2. **Last.fm API**:
   - `lastfm_network.get_track(artist, title)` - Get track
   - `track.get_playcount()` - Popularity metric
   - `track.get_top_tags()` - Genre tags

## Text Processing Methods
1. **Cleaning**:
   - `str.lower()` - Normalize case
   - `re.sub()` - Regex substitution
   - Remove URLs, unicode artifacts, structure markers

2. **Tokenization**:
   - `word_tokenize(text)` - Split into words
   - `sent_tokenize(text)` - Split into sentences

3. **Normalization**:
   - `stopwords.words('english')` - Common word removal
   - `WordNetLemmatizer().lemmatize()` - Word base form
   - Filter by length, punctuation

4. **Sentiment**:
   - `TextBlob(text).sentiment.polarity` - Positive/negative
   - `TextBlob(text).sentiment.subjectivity` - Objective/subjective

## Feature Extraction Methods
1. **TF-IDF**:
   - `TfidfVectorizer(max_features, ngram_range, analyzer)`
   - Word-level: captures vocabulary
   - Character-level: captures style

2. **Stylometric**:
   - Lexical diversity = unique_words / total_words
   - Word length statistics (mean, std)
   - Word category ratios (short/long)

3. **Sentiment**:
   - Polarity and subjectivity from TextBlob
   - Positive/negative word ratios

## Machine Learning Methods
1. **Preprocessing**:
   - `LabelEncoder()` - Encode text labels to numbers
   - `train_test_split(stratify=y)` - Balanced split

2. **Models**:
   - `LogisticRegression()` - Linear probabilistic
   - `LinearSVC()` - Support vector machine
   - `RandomForestClassifier()` - Ensemble trees

3. **Hyperparameter Tuning**:
   - `GridSearchCV()` - Exhaustive search
   - `StratifiedKFold()` - Cross-validation

4. **Evaluation**:
   - `accuracy_score()` - Overall correctness
   - `f1_score()` - Balanced precision/recall
   - `confusion_matrix()` - Detailed errors
   - `classification_report()` - Per-class metrics

5. **Model Persistence**:
   - `joblib.dump(model, file)` - Save model
   - `joblib.load(file)` - Load model

---

# FILE OUTPUTS GENERATED

## Data Files
1. `songs_data_final.csv` - Raw collected songs (~300)
2. `songs_data_backup.csv` - Backup copy
3. `songs_preprocessed.csv` - Cleaned lyrics
4. `song_metadata.csv` - Song info with labels
5. `checkpoint_{songwriter}.csv` - Collection checkpoints

## Feature Files
6. `X_features.npy` - Feature matrix (2,012 features)
7. `y_labels.npy` - Target labels

## Model Files
8. `best_classifier.pkl` - Trained Random Forest
9. `tfidf_word_vectorizer.pkl` - Word vectorizer
10. `tfidf_char_vectorizer.pkl` - Character vectorizer
11. `label_encoder.pkl` - Label mapping
12. `signature_words.pkl` - Unique vocabulary

## Analysis Files
13. `eda_summary.csv` - Statistical summary
14. `stylometric_profiles.csv` - Style metrics
15. `feature_importance.csv` - Top features
16. `classification_report.txt` - Model performance
17. `model_metadata.json` - Model config

## Visualizations
18. `wordclouds.png` - Vocabulary clouds
19. `vocabulary_analysis.png` - Word usage charts
20. `sentiment_analysis.png` - Emotion charts
21. `repetition_analysis.png` - Repetition metrics
22. `confusion_matrix.png` - Prediction errors
23. `feature_importance.png` - Important features

---

# TOTAL STATISTICS

- **Total Cells**: 40
- **Code Cells**: 37
- **Markdown Cells**: 3
- **Total Features Extracted**: 2,012
- **Total Methods/Functions**: 25+
- **Total Files Generated**: 23
- **Expected Runtime**: 2-3 hours (mostly data collection)
- **Expected Accuracy**: 38-60% (depends on data quality)

---

**End of Summary**
