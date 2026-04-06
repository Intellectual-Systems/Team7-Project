# 🎮 Press Start
### A Comparative Machine Learning Approach to Video Game Sales Prediction
**COMP 3608 — Machine Learning | B-Rank Mission**

| Member | Student ID |
|--------|-----------|
| Matthew Singh | 816035076 |
| Vinayak Maharaj | 816036176 |
| Jonathan La Borde | 816041435 |

---

## Files in this Repo

```
press-start/
├── press_start.ipynb   ← entire project, run this
├── .gitignore
└── README.md
```

---

## How to Run

### 1. Open the notebook
Go to [colab.research.google.com](https://colab.research.google.com) →
`File → Open notebook → GitHub` → paste this repo's URL → open `press_start.ipynb`

### 2. Get a Kaggle API token
The notebook downloads the datasets automatically using the Kaggle API.
You need a free Kaggle account and an API token to do this.

1. Create a free account at [kaggle.com](https://kaggle.com) if you don't have one
2. Go to **kaggle.com → profile picture → Settings**
3. Scroll to the **API Tokens** section
4. Click **Generate New Token** — this gives you a username and key string
5. In the notebook, find Section 3 and fill in your credentials:

```python
os.environ['KAGGLE_USERNAME'] = 'your_kaggle_username'
os.environ['KAGGLE_KEY']      = 'your_key_string_here'
```

### 3. Run all cells
`Runtime → Run all`

Everything else is automatic — datasets download, clean, EDA plots generate,
models train, price optimisation runs, and results tables populate.

---

## Datasets

All three datasets are from Kaggle and focus on the Steam platform,
chosen specifically because they include **price** as a feature — essential
for the price optimisation analysis.

| # | Kaggle Slug | Description |
|---|-------------|-------------|
| 1 | `nikdavis/steam-store-games` | ~27,000 Steam games. Price, owner range, release date, ratings. |
| 2 | `fronkongames/steam-games-dataset` | 110,000+ Steam games. Most comprehensive. Price, estimated owners, genres, DLC count. |
| 3 | `trolukovich/steam-games-complete-dataset` | ~40,000 games. Original and discount price, genre, developer, publisher. |

**Target variable:** `Estimated_Owners` — midpoint of owner range (e.g. "20000 - 50000" → 35000), log₁⁺ transformed.

**Features used:** `Price`, `Genre`, `Is_Holiday`, `Positive_Ratio`, `DLC_Count`, `Release_Month`

---

## Models

| Model | Role |
|-------|------|
| Linear Regression | Baseline — establishes a performance floor |
| Decision Tree | Captures non-linear price interactions |
| Random Forest | Ensemble — expected strongest performer |

**Metrics:** RMSE (primary), R² (secondary)

---

## Research Questions
1. How strongly does price correlate with sales volume?
2. Do holiday/seasonal releases sell more copies?
3. What is the optimal price point predicted by each model?
4. Are games currently overpriced relative to that optimum?

---

## Git Workflow
- Branches: `feature/preprocessing` (Matthew) → `feature/eda` (Vinayak) → `feature/models` (Jonathan)
- Commits are sequential — each member merges to main before the next begins
- Atomic commits — one logical change per commit
- Data files and plots are gitignored and never committed