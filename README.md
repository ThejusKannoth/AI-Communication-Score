# Self-Introduction Scorer

This project is a prototype tool for communication program.  
It takes a transcript of a short self-introduction as input and produces:

- An overall score (0–100)
- Per-criterion scores for:
  - Content & Structure (40%)
  - Speech Rate (10%)
  - Language & Grammar (20%)
  - Clarity (15%)
  - Engagement / Positivity (15%)
- Brief textual feedback for each criterion.

The tool is implemented as a Streamlit web app with rule-based, NLP-based, and rubric-driven logic.

---

## Scoring Formula
Let:
- **wᵢ** = weight of criterion i  
- **sᵢ** = normalized score of criterion i in range [0, 1]

Then the final score is:

Overall Score = ( Σ (wᵢ × sᵢ) / Σ wᵢ ) × 100

### Content & Structure (40)

Signals used:

- Rule-based:
  - Presence of key information groups: **name, age, class/school, family, hobbies**
  - Optional groups: **origin, fun fact, strengths/achievements**
  - Greeting and closing phrases
  - Length score based on target word range (e.g. 60–180 words)
- NLP-based:
  - Semantic similarity between the transcript and a rubric description using a sentence-embedding model.

Combination:

Content Score =
  0.60 × must_have_ratio
+ 0.15 × good_to_have_ratio
+ 0.15 × length_score
+ 0.10 × semantic_similarity
+ greeting/closing_bonus


### Speech Rate (10)

- Proxy: **word count** for a short introduction.
- Ideal range: **80–140 words**.
- `length_score(words, 80, 140)` returns 1 inside range, decreasing to 0 outside.
- Normalised score:

Speech Score = length_score(words, min=80, max=140)

### Language & Grammar (20)

- Rule-based:
  - **Type–Token Ratio (TTR)** as a vocabulary richness signal.
- NLP-based:
  - Semantic similarity with rubric description (“simple but correct sentences, reasonably rich vocabulary”).

Language Score = (0.70 × TTR) + (0.30 × semantic_similarity)


### Clarity (15)

- Rule-based:
  - Count filler words like *um, uh, you know, like, sort of, kind of*.
  - Compute fillers per 100 words and map to a score from 0.2 to 1.0.


### Engagement / Positivity (15)

- NLP-based:
  - Sentiment analysis (VADER).  
  - VADER compound ∈ [−1,1] is mapped to [0,1]:

Engagement Score = (VADER_compound + 1) / 2

---

## How to Run Locally


```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Add the path and run the app
cd file/path
streamline run app1.py
