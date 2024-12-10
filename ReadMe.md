# Hangman Project
---

## How the Guess Function Works

The guess function integrates two components:

### 1. Statistical Model
- **Method**: Computes letter frequencies based on a filtered dictionary of possible words matching the current game state.
- **How It Works**:
  - Filters the full dictionary using a regex pattern derived from the partially revealed word.
  - If too few words remain after strict filtering, a **partial matching** mechanism ranks potential candidates by how well they align with the known pattern.
  - Calculates letter frequencies from the filtered dictionary and normalizes them to probabilities.
- **Improvements**:
  - We integrated an **n-gram re-weighting mechanism**, boosting letter probabilities based on their co-occurrence in bigrams or trigrams containing known letters. The influence of this adjustment is controlled by a parameter `beta`.
    #### N-gram Weighting
    - **Purpose**: Adds weight that prioritizes letters that frequently co-occur in ngrams.
    - **How It Works**:
      - Computes bigram frequencies from the filtered dictionary.
      - Boosts the probabilities of letters appearing in n-grams that match the known pattern.
      - The influence is proportional to `beta`.
  - **Origin**: The statistical model was inspired by the implementation found in the following github repository:
    - [https://github.com/ShashwatKartikey/Hangman-Challenge/blob/main/hangman_api_user%20(1).ipynb]
    - I then modified the partial matching and added the ngram weighting to add "context"

---

### 2. Fine-Tuned BERT Model
- **Method**: Predicts missing letters using contextual understanding, particularly effective in late-game scenarios with fewer blanks.
- **How It Works**:
  - BERT is fine-tuned on a custom dataset of late-game examples where 1–3 letters are masked. This ensures the model is trained specifically to infer missing letters based on limited context.
  - When there are 3 or fewer unknown letters, BERT predicts the probabilities of missing letters at `[MASK]` positions.
- **Combination**:
  - The outputs of BERT and the statistical model are combined in the late game using a weighted parameter, `alpha`.



## Development Process

1. **Exploratory Data Analysis**:
   - We started by analyzing n-gram and letter distributions in the provided word dataset to identify patterns and trends.
   - This analysis guided the development of both the statistical and BERT-based models.

2. **Initial Models**:
   - A basic **n-gram model** was implemented to rank letters based on their frequencies and co-occurrence patterns.
   - A pre-trained **BERT model** was fine-tuned on masked word prediction tasks to learn contextual relationships.
   - Statistical model was then improved with repo model and then upgraded

3. **Combining Statistical and BERT Models**:
   - After exploring individual approaches, we decided to combine their strengths:
     - Statistical heuristics excel in the early game with many unknowns.
     - BERT can be fine-tuned when few letters remain as then the dictionary becomes less useful.

---

## Next Steps
If more time were available, focus on:

1. **Parameter Optimization**:
   - Fine-tune `alpha` and `beta` to optimize the balance between the statistical model, BERT predictions, and n-gram weighting.
2. **Enhanced Dataset for BERT**:
   - Improve the BERT dataset by incorporating scenarios with mixed revealed and hidden letters (`[HID]`) to better represent mid-game conditions.
3. **Advanced Statistical Approaches**:
   - Refine substring matching further with fuzzy matching or contextual weighting for partial matches.
