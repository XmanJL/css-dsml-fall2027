# 13 – Financial and Numeric Visualization

**Notebook:** `notebooks/visualization/13_financial_numeric_visualization.ipynb`
**Dataset loaded by the code:** `data/features/09_popularity_features.csv` (45,430 movies)
**Presentation time:** ≤ 5 minutes

---

## 1. Objective

Explore how a movie's budget, revenue and runtime relate to its popularity. Popularity is measured by `target_log_popularity`, which is `log(1 + popularity)`.

The notebook has five required charts and one extra chart: three scatter plots, a correlation heatmap, a distribution plot, and a budget-vs-revenue scatter coloured by popularity.

## 2. Data

| Column | Meaning | Movies with a value |
|---|---|---|
| `target_log_popularity` | Log of the movie's popularity score | 45,430 (all) |
| `log_budget` | Log of the budget in dollars | 8,880 (20%) |
| `log_revenue` | Log of the revenue in dollars | 7,398 (16%) |
| `runtime` | Length in minutes | 43,615 (96%) |

- Most movies have no recorded budget or revenue, so those charts show only the movies that have a value. The `n` is in each chart title.
- Each scatter plot has a red trend line and its correlation `r` in the title.
- The `runtime` chart is cut off at 300 minutes so the main group stays readable. 108 movies run longer (the longest is 1,256 minutes).

## 3. Findings

### 3.1 `log_budget` vs. `target_log_popularity` (r = 0.54)
Bigger budgets go with higher popularity. Movies with a budget below about $160,000 (log value 12) average 0.86 on the popularity scale, while movies at about $24 million and above (log value 17) average 2.41. The spread is wide at every budget level, so budget alone cannot predict popularity. The vertical stripes come from round budget numbers that many movies share.

### 3.2 `log_revenue` vs. `target_log_popularity` (r = 0.55)
The pattern is the same as for budget and slightly stronger. Movies that earn more are more popular. Most points sit between log revenue 15 and 20, and the cloud tilts upward. The most popular movies, with values above 4, have high revenue.

### 3.3 `runtime` vs. `target_log_popularity` (r = 0.17)
Runtime is the weakest of the three. 83% of movies run 80 to 150 minutes, and they cover almost the whole popularity range. Movies under 60 minutes are less popular on average (0.50 versus 1.08 for 80 to 150 minutes). The straight trend line understates this because the relationship is a hump around feature-film length, not a line.

### 3.4 Correlation heatmap
| | log_budget | log_revenue | runtime | target_log_popularity |
|---|---|---|---|---|
| **log_budget** | 1.00 | 0.70 | 0.25 | 0.54 |
| **log_revenue** | 0.70 | 1.00 | 0.22 | 0.55 |
| **runtime** | 0.25 | 0.22 | 1.00 | 0.17 |

- Budget and revenue are the two strongest predictors of popularity (0.54 and 0.55) and are strongly related to each other (0.70). A model would get much of the same information from either one.
- Runtime is only weakly related to anything.
- Each correlation uses only the movies that have both values.

### 3.5 Distribution of `target_log_popularity`
The distribution is skewed to the right. The median is 0.75 and the mean is 0.98, pulled up by a small group of very popular movies. About 11% of movies are near zero, and there is a second, smaller bump around 2. Only 0.7% of movies are above 3. Taking the log made the data much easier to read: in raw terms the median popularity is 1.13 but the maximum is 547.

### 3.6 Extra: budget vs. revenue, coloured by popularity
Budget and revenue rise together (r = 0.70, 5,375 movies with both). The brightest points, the most popular movies, sit in the top right where both are high. The dark points in the middle of the cloud are movies with mid-size budgets and revenue that are less popular. A small group in the bottom left has very small values for both, which are probably data errors or values recorded in the wrong unit.

## 4. Limitations

- Only about 1 in 5 movies has a budget or revenue, and these are likely larger, better-documented films. The budget and revenue findings may not apply to the other 80%.
- Correlation is not cause. Popular movies may attract more spending, or big budgets may drive popularity, and these charts can't tell which.
- Popularity is a snapshot taken at one point in time, so recent movies can score high for reasons unrelated to budget.
- The correlations are straight-line measures and miss curved patterns such as the runtime hump.
- Some budget and revenue values look like data errors (for example, a log value near 1 means a dollar or two).

## 5. Takeaways

1. Budget and revenue are the strongest financial signals of popularity (r about 0.55).
2. Runtime matters little (r = 0.17), apart from very short films being less popular.
3. Popularity is right-skewed, so the log scale is the right choice.
4. Missing budget and revenue data is the main caveat on these results.
