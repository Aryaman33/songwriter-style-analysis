# Enhanced Data Collection Plan - v2

## Current Situation Analysis

### Problem: Low Model Accuracy (40.5%)
- **Current data**: 316 songs collected → 208 songs after preprocessing (34% loss!)
- **Current model accuracy**: 40.5% (barely better than 38.1% baseline)
- **Root cause**: Insufficient training data per songwriter

### Data Loss Breakdown
```
Songwriter          Collected  →  After Preprocessing  (Loss %)
================================================================
Jack Antonoff            50   →   50                   (0%)
Max Martin               46   →   46                   (0%)
Dr. Luke                 41   →   25                   (39%)
Diplo                    22   →   22                   (0%)
Ryan Tedder              24   →   24                   (0%)
Sia Furler               20   →   20                   (0%)
Stargate                 22   →   21                   (5%)
Benny Blanco             25   →    0                   (100% - DROPPED)
Greg Kurstin             27   →    0                   (100% - DROPPED)
Julia Michaels           14   →    0                   (100% - DROPPED)
Shellback                14   →    0                   (100% - DROPPED)
The-Dream                11   →    0                   (100% - DROPPED)
================================================================
TOTAL                   316   →  208                   (34%)
```

**Why the losses?**
1. **Preprocessing filters** remove songwriters with <20 songs
2. **Word count threshold** filters out short songs
3. **Duplicate removal** eliminates redundant entries

---

## Solution: Enhanced Data Collection Strategy

### New Target: 80-100 Songs Per Songwriter

**Why 80-100 songs?**
- Machine learning models need **60-80+ samples per class** for reliable predictions
- Current 20-50 songs per songwriter is insufficient
- Industry research shows 70-100 samples yields 50-65% accuracy for text classification

### Focused Approach: Top 6 Songwriters

Instead of 12 songwriters with thin data, focus on **6 songwriters** with **deep data**:

1. **Jack Antonoff** (currently: 50 songs) → TARGET: 100 songs
   - 80s-inspired indie-pop, emotional storytelling
   - Artists: Taylor Swift, Lorde, Lana Del Rey, Bleachers, St. Vincent, Carly Rae Jepsen, Clairo, Pink, Troye Sivan, Sara Bareilles

2. **Max Martin** (currently: 46 songs) → TARGET: 100 songs
   - Pop perfection, catchy hooks, radio hits
   - Artists: Taylor Swift, The Weeknd, Ariana Grande, Katy Perry, Maroon 5, P!nk, Backstreet Boys, Britney Spears, Demi Lovato, Bon Jovi, Usher, Ellie Goulding

3. **Dr. Luke** (currently: 41 songs) → TARGET: 90 songs
   - Electronic pop, club bangers
   - Artists: Katy Perry, Kesha, Doja Cat, Kim Petras, P!nk, Miley Cyrus, Avril Lavigne, Taio Cruz, Flo Rida, B.o.B, Nicki Minaj, Pitbull

4. **Ryan Tedder** (currently: 24 songs) → TARGET: 85 songs
   - Anthemic choruses, piano-driven
   - Artists: Beyoncé, Adele, Taylor Swift, OneRepublic, Leona Lewis, Ed Sheeran, Jonas Brothers, U2, Colbie Caillat, James Blunt, Ariana Grande, Camila Cabello

5. **Sia Furler** (currently: 20 songs) → TARGET: 80 songs
   - Powerful vocals, emotional depth
   - Artists: Rihanna, Beyoncé, David Guetta, Sia, Flo Rida, Eminem, Christina Aguilera, Katy Perry, Britney Spears, Céline Dion, Rita Ora, Zayn

6. **Stargate** (currently: 21 songs) → TARGET: 85 songs
   - R&B grooves, pop-soul fusion
   - Artists: Rihanna, Beyoncé, Ne-Yo, Katy Perry, Sam Smith, Wiz Khalifa, Coldplay, Lionel Richie, Shakira, Mariah Carey, Jennifer Lopez, Usher

**Total Target: 540 songs**
**Expected after preprocessing (assuming 20% loss): ~430 songs**
**Expected accuracy: 50-65%**

---

## Step-by-Step Collection Plan

### Phase 1: Re-run Data Collection (Days 1-2)

1. **Update `01_data_collection.ipynb`:**
   - Modify target_writers dictionary (keep only top 6)
   - Expand artist lists for each songwriter (now 10-12 artists per songwriter)
   - Increase songs per artist from 15 to 20 in Genius search
   - Add more aggressive retry logic

2. **Collection Parameters:**
   ```python
   SONGS_PER_SONGWRITER_TARGET = 100
   MAX_SONGS_PER_ARTIST = 20  # increased from 15
   ARTISTS_TO_CHECK = 12       # increased from 8
   ```

3. **Run collection:**
   - Expected runtime: 3-4 hours (with rate limiting)
   - Monitor checkpoints for each songwriter
   - Target: 90-100 songs per songwriter before preprocessing

### Phase 2: Optimize Preprocessing (Day 3)

1. **Adjust `02_preprocessing.ipynb` filters:**
   - Lower word count threshold from 100 to 50 words (recover short songs)
   - Check duplicate detection logic (may be too aggressive)
   - Keep min 20 songs per songwriter filter (still valid)

2. **Expected outcome:**
   - 540 collected → 430-450 after preprocessing (~20% loss instead of 34%)
   - 6 songwriters with 70-80 songs each

### Phase 3: Re-train Models (Day 3)

1. **Run `03_exploratory_analysis.ipynb`** on new dataset
2. **Run `04_machine_learning.ipynb`** with new features:
   - Current: 2,010 features
   - With more data: Better feature importance, less overfitting

3. **Expected Results:**
   - Accuracy: 50-65% (vs current 40.5%)
   - Per-class precision: 45-70% (vs current 20-67%)
   - More balanced confusion matrix

---

## Alternative Quick Fix (If Time-Constrained)

If you don't have time for full re-collection, **Option B: Focus on 4 songwriters**:

Keep only the 4 with most data:
1. Jack Antonoff (50 songs) ← already good
2. Max Martin (46 songs) ← already good  
3. Dr. Luke (25 after preprocessing) ← collect 40 more
4. Diplo (22 songs) ← collect 30 more

**Advantages:**
- Less collection time (1-2 hours instead of 3-4)
- 4 classes = easier classification problem
- Expected accuracy: 55-70%

**Disadvantages:**
- Narrower scope (only 4 songwriting styles)
- Less impressive for academic presentation

---

## Implementation Commands

### To start enhanced collection:

```bash
# 1. Open 01_data_collection.ipynb
# 2. Run "Run All" to execute entire notebook
# 3. Monitor console for progress (will take 3-4 hours)
# 4. Check checkpoint files in data/ folder during collection
# 5. When complete, verify songs_data_final.csv has 500+ rows
```

### After collection:

```bash
# 1. Run 02_preprocessing.ipynb (Run All) - 5-10 minutes
# 2. Verify songs_preprocessed.csv has 400+ rows
# 3. Run 03_exploratory_analysis.ipynb (Run All) - 3-5 minutes  
# 4. Run 04_machine_learning.ipynb (Run All) - 15-20 minutes
# 5. Check classification_report.txt for new accuracy
```

---

## Expected Timeline

- **Day 1 (Today)**: Update notebooks + Start collection (3-4 hours)
- **Day 2**: Collection completes + Run preprocessing
- **Day 3**: Run EDA + ML training + Evaluate results

**Total time investment: ~5-6 hours** (mostly automated waiting)

---

## Questions?

**Q: Why not just keep all 12 songwriters?**  
A: Quality over quantity. 6 songwriters with 80 songs each >> 12 songwriters with 30 songs each

**Q: Can I add more songwriters later?**  
A: Yes! Once you hit 50-60% accuracy with 6, you can gradually add more (one at a time) as long as you maintain 70+ songs per songwriter.

**Q: What if collection fails partway?**  
A: Checkpoints save progress! You can resume from the last successful songwriter.

---

## Next Steps

1. Review this plan
2. Decide: Full collection (Option A) or Quick fix (Option B)
3. I'll update the notebooks accordingly
4. You run the collection overnight

Which option would you like to proceed with?
