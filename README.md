# 🎮 Press Start
### A Comparative Machine Learning Approach to Video Game Sales Prediction
**COMP 3608 - Machine Learning | B-Rank Mission**

| Member | Student ID |
|--------|-----------|
| Matthew Singh | 816035076 |
| Vinayak Maharaj | 816036176 |
| Jonathan La Borde | 816041435 |

---

## Files in this Repo

```
press-start/
├── press_start.ipynb   <- entire project, run this
├── .gitignore
└── README.md
```

---

## How to Run

### 1. Open the notebook
Go to [colab.research.google.com](https://colab.research.google.com) ->
`File -> Open notebook -> GitHub` -> paste this repo's URL -> open `press_start.ipynb`

### 2. Get a Kaggle API token
The notebook downloads the datasets automatically using the Kaggle API.
You need a free Kaggle account and an API token to do this.

1. Create a free account at [kaggle.com](https://kaggle.com) if you don't have one
2. Go to **kaggle.com -> profile picture -> Settings**
3. Scroll to the **API Tokens** section
4. Click **Generate New Token** - this gives you a username and key string
5. In the notebook, find Section 3 and fill in your credentials:

```python
os.environ['KAGGLE_USERNAME'] = 'your_kaggle_username'
os.environ['KAGGLE_KEY']      = 'your_key_string_here'
```

### 3. Run all cells
`Runtime -> Run all`

Everything else is automatic - datasets download, clean, EDA plots generate,
models train, price optimisation runs, and results tables populate.

---

## Datasets

All three datasets are from Kaggle and focus on the Steam platform,
chosen specifically because they include **price** as a feature - essential
for the price optimisation analysis.

| # | Kaggle Slug | Description |
|---|-------------|-------------|
| 1 | `nikdavis/steam-store-games` | ~8,500 Steam games after filtering (20,000+ owners). Price, owner range, release date, ratings. Used for **training**. |
| 2 | `fronkongames/steam-games-dataset` | ~25,500 Steam games after filtering. Most comprehensive. Price, estimated owners, DLC count, review scores. Used for **training**. |
| 3 | `artermiloff/steam-games-dataset` | ~21,900 Steam games (March 2025 snapshot). Used exclusively for **testing** - never seen during training. |

**Why three datasets?** The project uses a cross-dataset temporal design to eliminate data leakage.
Datasets 1 and 2 cover 1997-2022 and serve as the training set (~34,000 games combined).
Dataset 3 is a March 2025 snapshot and serves as the test set, simulating a real developer
using historical data to make a current pricing decision.

**Filtering:** Games with 20,000 or fewer estimated owners are excluded. This removes the
bottom SteamSpy bucket (which has no discriminating signal - ~73% of raw games report
identical owner counts) and focuses the model on games with measurable traction.

**Target variable:** `Estimated_Owners` - midpoint of owner range (e.g. "20,000 - 50,000" -> 35,000), log₁+ transformed.

**Features used:** `Price_Real`, `Is_Holiday`, `Positive_Ratio`, `DLC_Count`, `Release_Month`

**Note on Genre:** Genre was excluded from the final model due to incompatible encoding across
datasets. Dataset 1 used pipe-separated strings, Dataset 2's genre column was a shifted
Steam categories field, and Dataset 3 had only 27 unique values. A shared normalised genre
vocabulary would require significant manual mapping effort and is noted as a limitation.

**Note on Price:** All prices are CPI-adjusted to 2020 real dollars using US Bureau of Labor
Statistics deflators, removing inflation bias across the 27-year training window. The
conversion uses a dictionary of annual deflators (1998-2025).

---

## Models

| Model | Role | Test R² | Test RMSE |
|-------|------|---------|-----------|
| Linear Regression | Baseline - establishes a performance floor | 0.075 | 1.083 |
| Decision Tree | Captures non-linear price interactions | 0.109 | 1.063 |
| Random Forest | Ensemble - best generalisation to 2025 data | 0.183 | 1.018 |
| XGBoost | Boosted trees - close second to Random Forest | 0.171 | 1.026 |

All models are trained on Datasets 1 + 2 and evaluated on Dataset 3.
R² of 0.18 on a completely unseen 2025 dataset trained on 1997-2022 data is a
respectable result for temporal generalisation - Steam sales are heavily influenced
by marketing and visibility factors that no tabular model can capture.

**Metrics:** RMSE (primary), R² (secondary)

---

## Research Questions
1. How strongly does price correlate with sales volume?
2. Do holiday/seasonal releases sell more copies?
3. What is the optimal price point predicted by each model?
4. Are games currently overpriced or underpriced relative to that optimum?
5. What revenue gain would a developer have achieved by following the model's recommendation?

---

## Key Findings

**Optimal price:** Tree-based models recommend $21-$27 in 2020 real dollars, equivalent to
approximately $26-$34 in 2025 nominal dollars. Linear Regression's $70 optimum is an
artefact of its inability to model a curved demand relationship and is disregarded.

**Are games overpriced?** The opposite - the median Steam game with measurable traction
is priced at $7.99 nominal ($7.49 real), which is $18-$26 below the model optimum.
Games with traction appear to be significantly underpriced.

**Developer gain simulation:** A developer pricing at the market median of $7.99 with
~74,000 owners earns ~$591K gross. At the model-recommended $27, the same owner count
yields ~$2.0M - a 3.4x revenue gain. This is a conservative estimate since $27 is
also the predicted owner-maximising price, meaning reach would not decrease.

---

## Git Workflow
- Branches: `feature/preprocessing` (Matthew) -> `feature/eda` (Vinayak) -> `feature/models` (Jonathan)
- Commits are sequential - each member merges to main before the next begins
- Atomic commits - one logical change per commit
- Data files and plots are gitignored and never committed
