![Python](https://img.shields.io/badge/Python-blue)
![pandas](https://img.shields.io/badge/pandas-blue)
![NumPy](https://img.shields.io/badge/NumPy-blue)
![Matplotlib](https://img.shields.io/badge/Matplotlib-orange)
![Seaborn](https://img.shields.io/badge/Seaborn-orange)
![scikit--learn](https://img.shields.io/badge/scikit--learn-orange)
![Jupyter](https://img.shields.io/badge/Jupyter-orange)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-black)

# Movie Budget Linear Regression Analysis

Does a bigger production budget reliably translate to higher box-office returns? Across 5,384 released films spanning over a century of cinema, a linear regression model fitted on budget versus worldwide gross reveals a slope of approximately 3.12 — meaning every additional dollar of production budget is associated with roughly $3.12 in worldwide revenue. The relationship explains 55.77% of the variance in revenue for films released from 1970 onwards.

The analysis begins with a currency-formatted dataset that requires cleaning before any modelling can begin. Raw columns like `"$110,000"` are stripped of symbols and converted to floats; release dates are parsed to datetime; and 7 unreleased titles (dated after the May 2018 data-collection cut-off) are removed from the working dataset. What remains is 5,384 films from 1915 to 2018.

From there the analysis investigates the 37.28% of films that failed to recoup their production costs, identifies 155 international releases that earned worldwide revenue while recording zero domestic gross, and compares the regression lines for films made before and after 1970 — showing that the budget-to-revenue relationship is both stronger and steeper in the modern era.

---

## Quick Start

```bash
git clone https://github.com/xavier-oc-programming/movie-budget-linear-regression-analysis.git
cd movie-budget-linear-regression-analysis
pip install -r requirements.txt
jupyter notebook
```

Open `notebooks/analysis/A_01_Full_Analysis.ipynb` to run the full end-to-end analysis. Concept notebooks are in `notebooks/concepts/` and step through each method in isolation.

---

## Analysis Flow

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
        ├── drop future releases (>= 2018-05-01)   →  data_clean (5,384 films)
        │
        │  ── Subsetting ─────────────────────────────────────────
        ├── boolean filter == 0        →  zero-revenue films (357 worldwide)
        ├── .loc[] multi-condition     →  international releases (155 films)
        ├── budget > worldwide gross   →  loss-making films (37.28%)
        ├── .dt.year // 10 * 10        →  decade column
        ├── Decade <= 1960             →  old_films (153 films)
        └── Decade > 1960             →  new_films (5,231 films)
        │
        │  ── Visualisation ──────────────────────────────────────
        ├── sns.scatterplot()          →  budget vs worldwide gross bubble chart
        ├── sns.regplot()              →  new films regression line
        │
        │  ── Regression Modelling ───────────────────────────────
        └── sklearn LinearRegression
              .fit(X, y)
              .intercept_   →  -8,650,768
              .coef_        →  3.1226
              .score(X, y)  →  R² = 55.77%
              .predict()    →  revenue estimate for any budget
```

---

## Key Findings

- **55.77% of revenue variance explained** by production budget alone for films from 1970 onwards (R² = 0.5577)
- **Slope of 3.12**: each additional dollar of budget is associated with ~$3.12 in worldwide gross
- **37.28% of films lost money** — production costs exceeded worldwide gross revenue
- **357 films reported zero worldwide gross**, with the highest-budget zero-earner carrying a $175M budget (Singularity)
- **155 international releases** earned worldwide revenue while recording zero domestic gross
- **153 old films (pre-1970)** vs 5,231 new films — the modern era dominates the dataset
- Budget range: $1,100 (My Date With Drew, 2005) to $425M (Avatar, 2009); Avatar grossed $2.78B worldwide

---

## Dataset Schema

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

Source: [the-numbers.com/movie/budgets](https://www.the-numbers.com/movie/budgets), scraped May 1, 2018.

---

## Architecture

```
movie-budget-linear-regression-analysis/
│
├── notebooks/
│   ├── analysis/
│   │   └── A_01_Full_Analysis.ipynb     # End-to-end analysis from cleaning to regression
│   └── concepts/
│       ├── 00__Overview.ipynb
│       ├── 01__Data_Exploration_and_Cleaning.ipynb
│       ├── 02__Zero_Revenue_Films.ipynb
│       ├── 03__International_Film_Filtering.ipynb
│       ├── 04__Bubble_Charts_with_Seaborn.ipynb
│       ├── 05__Decade_Conversion.ipynb
│       ├── 06__Linear_Regression_Plots.ipynb
│       ├── 07__scikit_learn_Regression.ipynb
│       └── 08__Summary.ipynb
│
├── data/
│   └── cost_revenue_dirty.csv           # Source dataset (5,391 rows, raw currency format)
│
├── plots/
│   ├── budget_vs_revenue_bubble.png     # Budget vs worldwide gross over time
│   └── regression_new_films.png         # Regression line for films 1970+
│
├── notebook/
│   └── index.html                       # Rendered notebook (GitHub Pages)
│
├── docs/
│   └── COURSE_NOTES.md
│
├── .github/
│   └── workflows/
│       └── publish_notebook.yml         # CI/CD: auto-render notebook to GitHub Pages
│
├── requirements.txt
├── .gitignore
└── README.md
```

---

## Visualisations

| File | Description |
|---|---|
| `plots/budget_vs_revenue_bubble.png` | Scatter plot of all cleaned films: release date on x-axis, production budget on y-axis, point size and colour encode worldwide gross |
| `plots/regression_new_films.png` | Regression plot for films from 1970 onwards showing the linear fit (R² = 55.77%) with formula annotation |

---

## Operations Reference

| Value | Location | Description |
|---|---|---|
| `'../../data/cost_revenue_dirty.csv'` | `notebooks/analysis/A_01_Full_Analysis.ipynb` | Relative path to dataset from analysis notebook |
| `scrape_date = pd.Timestamp('2018-5-1')` | Analysis notebook | Cut-off for filtering unreleased films |
| `figsize=(8, 4), dpi=200` | All chart cells | Standard figure dimensions |
| `alpha=0.3` | Scatter/bubble charts | Point transparency for overplotted areas |
| `dpi=150` | `savefig()` calls | Output resolution for saved plots |

---

## Background

100 Days of Code: The Complete Python Pro Bootcamp — Day 78: Linear Regression and Data Visualisation with Seaborn.  
See [docs/COURSE_NOTES.md](docs/COURSE_NOTES.md) for the full exercise brief and key concepts.

---

## Dependencies

| Module | Purpose |
|---|---|
| `pandas` | Data loading, cleaning, filtering, and transformation |
| `numpy` | Numerical operations |
| `matplotlib` | Low-level chart customisation (titles, labels, figure size) |
| `seaborn` | Statistical visualisation — scatter, bubble, regplot |
| `scikit-learn` | `LinearRegression` model: fit, score, predict |
| `notebook` | Jupyter notebook server |

```bash
pip install -r requirements.txt
```
