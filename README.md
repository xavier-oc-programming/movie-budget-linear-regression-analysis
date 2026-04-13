# Movie Budget Linear Regression Analysis

Applies linear regression to 5,000+ films to quantify how production budget predicts worldwide box-office revenue, with Seaborn visualisation across decades.

The analysis investigates a fundamental question in the film industry: does spending more money on production reliably translate to higher box-office returns? A scikit-learn `LinearRegression` model is fitted on budget vs. worldwide gross to extract the slope, intercept, and R², giving a concrete numerical answer to that question. Before modelling, the project works through data cleaning, exploratory analysis, and statistical visualisation — including `regplot()` overlays that compare the regression line for older films against newer ones to show how the relationship has shifted over time.

The dataset (`cost_revenue_dirty.csv`) contains 5,391 films spanning over a century of cinema, with each row recording a film's production budget, worldwide gross, and domestic gross in raw currency-string format (e.g. `"$110,000"`). The cleaning pipeline strips currency symbols and commas, converts columns to float, and removes duplicates before any analysis begins. Release dates are parsed to extract decade buckets for temporal comparison.

No external APIs or credentials are required. All data is committed directly to the repository and loaded via relative paths.

---

## Table of Contents

1. [Quick start](#quick-start)
2. [Analysis flow](#analysis-flow)
3. [Features](#features)
4. [Dataset schema](#dataset-schema)
5. [Architecture](#architecture)
6. [Notebook reference](#notebook-reference)
7. [Configuration reference](#configuration-reference)
8. [Course context](#course-context)
9. [Dependencies](#dependencies)

---

## 1. Quick start

```bash
git clone https://github.com/xavier-oc-programming/movie-budget-linear-regression-analysis.git
cd movie-budget-revenue-analysis
pip install -r requirements.txt
jupyter notebook
```

Open `theory/00__Overview.ipynb` to read the lesson goals, then work through notebooks `01` to `08` in order. To run the full end-to-end analysis immediately, open `practice/A_01_Full_Analysis.ipynb`.

---

## 2. Analysis flow

```
data/cost_revenue_dirty.csv
        │
        ▼
pd.read_csv()  →  raw DataFrame (currency strings, mixed types)
        │
        │  ── Cleaning ───────────────────────────────────────────
        ├── nested loop strip "$" and ","
        ├── .astype(float)             →  numeric budget / revenue columns
        ├── .dropna() / .drop_duplicates()
        │
        │  ── Subsetting ─────────────────────────────────────────
        ├── boolean filter == 0        →  zero-revenue films investigation
        ├── .loc[] multi-condition     →  international vs domestic subset
        ├── .dt.year // 10 * 10        →  decade column
        │
        │  ── Visualisation ──────────────────────────────────────
        ├── sns.scatterplot()          →  budget vs worldwide gross scatter
        ├── bubble chart (size=domestic gross)
        ├── sns.regplot() ×2           →  old films / new films regression lines
        │
        │  ── Regression Modelling ───────────────────────────────
        └── sklearn LinearRegression
              .fit(X, y)
              .intercept_, .coef_      →  model equation
              .score(X, y)             →  R²
              .predict([[budget]])     →  revenue estimate
```

---

## 3. Features

- Cleans a real-world currency-formatted dataset using nested loops across multiple columns
- Identifies and investigates films that reported zero worldwide revenue
- Filters for international releases (films that earned revenue outside the domestic market)
- Creates bubble charts encoding three variables simultaneously (budget, worldwide gross, domestic gross)
- Converts release years to decade buckets with floor division
- Plots and compares linear regression lines for older vs. newer films
- Fits a scikit-learn `LinearRegression` model and reports intercept, slope, R², and point predictions

---

## 4. Dataset schema

### `data/cost_revenue_dirty.csv`

| Column | Type | Description |
|---|---|---|
| `Rank` | int | Film ranking in the source dataset |
| `Release_Date` | string → datetime | Original theatrical release date |
| `Movie_Title` | string | Film title |
| `USD_Production_Budget` | string → float | Production budget in USD (raw: `"$1,000,000"`) |
| `USD_Worldwide_Gross` | string → float | Total worldwide box-office revenue in USD |
| `USD_Domestic_Gross` | string → float | North American box-office revenue in USD |

**Computed columns added at runtime:**

| Column | Type | Description |
|---|---|---|
| `Decade` | int | Release decade derived via `(year // 10) * 10` |

---

## 5. Architecture

```
movie-budget-revenue-analysis/
│
├── theory/                          # Lesson notebooks — concepts and methods
│   ├── 00__Overview.ipynb           # Day goals and lesson overview
│   ├── 01__Data_Exploration_and_Cleaning.ipynb   # .shape, .dropna, type conversion
│   ├── 02__Zero_Revenue_Films.ipynb              # Films with $0 worldwide gross
│   ├── 03__International_Film_Filtering.ipynb    # Multi-condition boolean filtering
│   ├── 04__Bubble_Charts_with_Seaborn.ipynb      # scatterplot + bubble encoding
│   ├── 05__Decade_Conversion.ipynb               # Floor division for decade buckets
│   ├── 06__Linear_Regression_Plots.ipynb         # regplot() old vs new films
│   ├── 07__scikit_learn_Regression.ipynb         # LinearRegression fit + predict
│   └── 08__Summary.ipynb                         # Key learning points
│
├── practice/                        # Student code — exercises and outputs
│   └── A_01_Full_Analysis.ipynb     # End-to-end analysis notebook
│
├── data/
│   └── cost_revenue_dirty.csv       # Source dataset (5,391 films, raw currency format)
│
├── docs/
│   └── COURSE_NOTES.md              # Exercise brief and key concepts
│
├── requirements.txt                 # Pinned package versions
├── .gitignore
└── README.md
```

---

## 6. Notebook reference

### theory/

| Notebook | Key methods covered | Question answered |
|---|---|---|
| `00__Overview.ipynb` | — | What will this lesson build? |
| `01__Data_Exploration_and_Cleaning.ipynb` | `.shape`, `.head()`, `.tail()`, `.dtypes`, `.isna()`, `.duplicated()`, nested loop strip, `.astype()` | What is the structure and quality of the raw data? |
| `02__Zero_Revenue_Films.ipynb` | boolean filter `== 0`, `.loc[]`, `.sort_values()` | Which films reported zero worldwide revenue, and are they unreleased? |
| `03__International_Film_Filtering.ipynb` | `.loc[]` multi-condition, column subtraction | How many films earned revenue outside their domestic market? |
| `04__Bubble_Charts_with_Seaborn.ipynb` | `sns.scatterplot()`, `size=`, `alpha=`, `plt.title/xlabel/ylabel` | How does domestic vs worldwide revenue look when plotted as a bubble chart? |
| `05__Decade_Conversion.ipynb` | `pd.to_datetime()`, `.dt.year`, `//` floor division | How can release years be grouped into decade buckets? |
| `06__Linear_Regression_Plots.ipynb` | `sns.regplot()`, overlay on shared Axes, `.set()` | Does the budget-to-revenue regression line differ between older and newer films? |
| `07__scikit_learn_Regression.ipynb` | `LinearRegression`, `.fit()`, `.intercept_`, `.coef_`, `.score()`, `.predict()` | What is the quantified linear relationship between budget and revenue? |
| `08__Summary.ipynb` | — | What were the key learning points from this lesson? |

### practice/

| Notebook | Key methods covered | Question answered |
|---|---|---|
| `A_01_Full_Analysis.ipynb` | Full pipeline from cleaning to regression | Does a higher production budget lead to higher worldwide revenue? |

---

## 7. Configuration reference

| Value | Location | Description |
|---|---|---|
| `"../data/cost_revenue_dirty.csv"` | `practice/A_01_Full_Analysis.ipynb` | Relative path to the dataset from inside practice/ |
| `figsize=(8, 4)` | Theory notebooks | Default Matplotlib figure size |
| `alpha=0.3` | Scatter/bubble charts | Point transparency to reduce overplotting |

---

## 8. Course context

100 Days of Code: The Complete Python Pro Bootcamp — Day 78: Linear Regression and Data Visualisation with Seaborn.  
See [docs/COURSE_NOTES.md](docs/COURSE_NOTES.md) for the full exercise brief and key concepts.

---

## 9. Dependencies

| Module | Used in | Purpose |
|---|---|---|
| `pandas` | All notebooks | Data loading, cleaning, filtering, and transformation |
| `numpy` | Theory/practice notebooks | Numerical operations supporting pandas |
| `matplotlib` | Theory/practice notebooks | Low-level chart customisation (titles, labels, figure size) |
| `seaborn` | `04`, `06`, `A_01` | High-level statistical visualisation (scatter, bubble, regplot) |
| `scikit-learn` | `07`, `A_01` | `LinearRegression` model: fit, score, predict |
| `notebook` | All | Jupyter notebook server |
