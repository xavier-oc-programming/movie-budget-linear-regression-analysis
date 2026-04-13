# Day 78 — Course Notes: Linear Regression and Data Visualisation with Seaborn

**Course:** 100 Days of Code: The Complete Python Pro Bootcamp  
**Day:** 78  
**Topics:** Exploratory data analysis, Seaborn visualisation, linear regression with scikit-learn

---

## Exercise Brief

Analyse a dataset of movie production budgets and worldwide box-office revenue to determine whether higher budgets tend to lead to higher earnings. Apply data cleaning, filtering, visualisation, and regression modelling in a single coherent workflow.

---

## Key Concepts Covered

### Data Cleaning
- Inspect dataset shape, missing values, duplicates, and data types
- Use nested loops to strip currency symbols (`$`) and thousands separators (`,`) from multiple columns simultaneously
- Convert cleaned string columns to numeric types with `.astype(float)`

### Filtering and Subsetting
- Identify films with `USD_Worldwide_Gross == 0` and investigate whether they are unreleased or simply had no revenue reported
- Filter on multiple conditions simultaneously using boolean masks and `.loc[]`
- Distinguish domestic-only releases from international films using column comparisons

### Visualisation with Seaborn
- Import Seaborn (`import seaborn as sns`) as a high-level wrapper around Matplotlib
- Create scatter plots with `sns.scatterplot()`
- Encode a third variable (domestic gross) as bubble size to produce a bubble chart
- Plot linear regression lines with confidence intervals using `sns.regplot()`
- Compare budget-to-revenue trends across decades by sub-setting the data and overlaying two `regplot()` calls on one Axes

### Floor Division for Decade Bucketing
- Use Python's floor division operator (`//`) to convert a 4-digit release year into a decade:  
  `df['Decade'] = (df['Release_Date'].dt.year // 10) * 10`

### Linear Regression with scikit-learn
- Import and instantiate `LinearRegression` from `sklearn.linear_model`
- Reshape the feature array to 2-D: `X = df[['USD_Production_Budget']]`
- Fit the model: `reg.fit(X, y)`
- Inspect coefficients: `reg.intercept_`, `reg.coef_`
- Evaluate fit: `reg.score(X, y)` → R²
- Make a point prediction: `reg.predict([[budget]])`

---

## Analysis Questions

1. Do higher film production budgets lead to higher worldwide box-office revenue?
2. What proportion of films in the dataset reported zero worldwide revenue?
3. Are zero-revenue films unreleased titles or films with missing data?
4. How does the budget-to-revenue relationship differ for international films vs. domestic-only releases?
5. How does that relationship change across decades (older vs. newer films)?
6. What is the linear regression model's intercept, slope, and R²?
7. Given a specific budget, what worldwide revenue does the model predict?
