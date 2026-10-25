# Movie Feature Visualization Report

**Notebook:** `15_product_visualization.ipynb`  
**Dataset loaded by the code:** `data/features/09_popularity_features.csv`

## Objective

The purpose of this notebook is to explore how movie characteristics relate to log popularity. The analysis examines genre count, production company count, production country count, spoken language count, and whether a movie belongs to a collection.

The notebook contains four scatterplots, one box plot, and five count heatmaps. Together, these charts show individual movie values, differences between groups, and where movie records are concentrated.

## Data and preparation

The notebook loads the CSV into a pandas DataFrame named `popularity_dataset`. It selects individual columns as Series and uses NumPy arrays for the scatterplots. Matplotlib displays the charts, while `ceil()` from Python's `math` module assigns values to heatmap groups.

The project path is defined using `Path.cwd().parent.parent`. This assumes the notebook's working directory is two levels below the project root.

| Column | Meaning used in this analysis |
| --- | --- |
| `genre_count` | Number of genres listed for a movie. |
| `company_count` | Number of production companies listed for a movie. |
| `country_count` | Number of production countries listed for a movie, rather than countries where it is available to watch. |
| `spoken_language_count` | Number of spoken languages listed for a movie, rather than the number of translations available. |
| `has_collection` | Collection membership: 0 means no and 1 means yes. |
| `target_log_popularity` | The movie's log-transformed popularity value. |

These features are already present in the CSV. The notebook does not show their original calculation or the exact formula used to transform popularity.

This report uses the notebook's code and saved chart outputs. The source CSV was not attached, so the notebook was not rerun and the underlying movie records were not independently checked.

## Scatterplot and box plot findings

### Genre count and log popularity

Each point represents one movie. The x-axis shows genre count, and the y-axis shows log popularity.

The saved scatterplot shows many overlapping points at genre counts from 1 to 5, with a wide range of popularity values at each count. Some high-popularity movies appear at counts from 2 to 5. The plot does not establish that adding genres consistently increases popularity or that three genres is an optimal number.

### Collection membership and log popularity

The notebook uses a **box plot** to compare movies that belong to a collection with those that do not.

The collection group has a visibly higher median and a higher middle range of log popularity. From the saved figure, the medians are approximately **1.5 for collection movies** and **0.7 for movies outside a collection**. These are visual estimates, not statistics recalculated from the CSV.

Both groups contain high-value outliers. The chart suggests an association between collection membership and higher typical log popularity, but it does not show that collection membership causes popularity.

### Production company count and log popularity

The x-axis shows the number of production companies, and the y-axis shows log popularity.

Most of the visible points are concentrated at lower company counts. Movies with high log popularity appear among those with relatively few companies, while movies with large company counts are less common. The chart does not show a consistent increase in popularity as company count increases.

The notebook's saved maximum company count is **26**.

### Production country count and log popularity

The x-axis shows production country count, and the y-axis shows log popularity.

Many movies are concentrated at one to a few production countries. Popularity varies substantially within these groups, and large country counts are uncommon. The plot does not show that involving more production countries consistently leads to higher popularity.

The notebook's saved maximum country count is **25**.

### Spoken language count and log popularity

The plotted x-values represent spoken language count, and the y-values represent log popularity.

Most visible points occur at relatively low language counts, with many below a log popularity of 3. Several of the highest popularity values occur among movies with one to a few spoken languages. Movies with many listed languages are uncommon, and the plot does not show a consistent increase in popularity as language count increases.

The notebook's saved maximum spoken language count is **18**.

## Heatmap method

Each heatmap groups movies by a feature on the x-axis and log popularity on the y-axis. A grid starts with zeros. The counting loop reads each movie's two values, selects the matching row and column, and adds 1 to that cell.

The number inside a cell is a **movie record count**. Yellow indicates fewer records, and red indicates more records. Red does not itself mean higher popularity or stronger correlation; the row identifies the popularity range.

| Feature | Groups used by the counting code | Grid size: rows × columns |
| --- | --- | --- |
| Genre count | 0; 1–4; 5–8 | 4 × 3 |
| Company count | 0; 1–10; 11–20; 21–26 | 4 × 4 |
| Country count | 0; 1–10; 11–20; 21–25 | 4 × 4 |
| Spoken language count | 0; 1–6; 7–12; 13–18 | 4 × 4 |
| Collection membership | No (0); Yes (1) | 4 × 2 |

All five heatmaps use `ceil(popularity / 3)` for the row index. Because log popularity can contain decimals, the displayed row labels are shorthand for the following actual intervals:

| Displayed row label | Actual values counted |
| --- | --- |
| 0 | Exactly 0 |
| 1–3 | Greater than 0 and up to 3 |
| 4–6 | Greater than 3 and up to 6 |
| 7–9 | Greater than 6 and up to 9 |

For example, 0.7 is counted in the row labeled “1–3,” and 6.3 is counted in the row labeled “7–9.”

## Heatmap results

The displayed cells in **each of the five heatmaps sum to 45,430 movie records**. This is the number represented in those saved heatmaps, not an independently verified count of all rows in the source CSV.

Across all five heatmaps, the popularity groups have the same totals:

| Actual log popularity range | Movie records |
| --- | ---: |
| Exactly 0 | 66 |
| Greater than 0 through 3 | 45,045 |
| Greater than 3 through 6 | 318 |
| Greater than 6 through 9 | 1 |
| **Total** | **45,430** |

Approximately **99.15% of the counted records** fall above 0 and at or below 3. This explains why the strongest color concentration appears in that row.

The largest cell in each heatmap is:

| Heatmap | Feature group | Actual log popularity range | Movie records |
| --- | --- | --- | ---: |
| Genre count | 1–4 genres | Greater than 0 through 3 | 41,649 |
| Company count | 1–10 companies | Greater than 0 through 3 | 33,068 |
| Country count | 1–10 countries | Greater than 0 through 3 | 38,808 |
| Spoken language count | 1–6 languages | Greater than 0 through 3 | 41,104 |
| Collection membership | No collection | Greater than 0 through 3 | 40,717 |

The collection heatmap contains **40,943 records without a collection** and **4,487 with a collection**. This difference in group size explains why the “No” column can contain more movies even though the “Yes” group has a higher median in the box plot.

## Interpretation and limitations

- The scatterplots show substantial variation in popularity within the same feature count. A simple “more means more popular” pattern is not established by these figures.
- Heatmaps show raw counts. A large cell can reflect a large group, so counts alone do not establish which group is more likely to be popular.
- Each heatmap has its own color scale. Compare the printed counts or colorbars when comparing different heatmaps.
- The counting conditions exclude values outside the selected ranges. The notebook does not report how many records, if any, were excluded.
- A zero metadata count may mean no entries were recorded. It should not automatically be interpreted as proof that no companies, countries, or languages were involved.
- No correlation coefficients, statistical tests, or predictive models are calculated in this notebook.

## Notebook documentation and labeling fixes

The following changes would make the notebook's explanations and labels consistent with its code:

1. **Input filename:** The setup code loads `09_popularity_features.csv`, while the introductory Markdown names `08_popularity_features.csv`.
2. **Genre axes:** The genre scatterplot's Markdown reverses the axes. The code uses genre count on x and log popularity on y. Its “three genres is the sweet spot” claim is not established by the plotted points.
3. **Collection chart:** Describe this as a box plot. The individual dots are outliers, so their concentration does not show where most movies fall.
4. **Country axis:** The country scatterplot's Markdown should name country count on the x-axis, rather than company count.
5. **Spoken language scatterplot:** Add x- and y-axis labels for spoken language count and log popularity.
6. **Spoken language heatmap:** With `ceil(spoken / 6)`, the last two column labels should be `7–12` and `13–18`, rather than `7–11` and `12–18`.
7. **Popularity intervals:** Explain the decimal boundaries shown in the table above, or use labels such as `>0–3`, `>3–6`, and `>6–9`.
8. **Company plot display:** Change `plt.show` to `plt.show()` to explicitly call the display function. The saved chart is visible because the notebook environment displayed it automatically.

## Conclusion

The notebook shows that most counted movie records have log popularity greater than 0 and no higher than 3. The scatterplots do not establish that larger genre, company, country, or spoken language counts consistently correspond to higher popularity.

The collection box plot shows a clearer difference in typical popularity: movies belonging to a collection have a higher median than movies outside a collection. The heatmaps complement these charts by showing how many movie records fall into each combination of feature and popularity groups.

