# Audience Rating Feature Engineering

## Objective

Prepare movie data for predicting audience ratings using `vote_average` as the target. The current notebook combines four datasets, creates release-date features, and keeps movies with at least 34 votes. Its saved output reports **11,508 remaining rows**. The current code produces **25 columns**.

## Data sources

| File | Information used |
| --- | --- |
| `data/processed/movies_metadata.csv` | Base movie IDs, release dates, budget, revenue, runtime, and popularity. |
| `data/raw/movies_metadata.csv` | Ratings, vote counts, genres, collection information, production companies and countries, spoken languages, titles, overview, original language, and status. |
| `data/processed/credits.csv` | Director, cast size, and crew size. |
| `data/raw/keywords.csv` | Movie keywords. |

The processed movie dataset is the base. All other datasets are matched to it using `id`.

## Cleaning and merging

1. Convert `id` in all four datasets with `pd.to_numeric(errors="coerce")`, followed by the nullable integer datatype `Int64`. Invalid numeric text becomes missing.
2. Remove repeated IDs with `drop_duplicates(subset="id", keep="first")`. The saved checks show zero duplicate IDs in each source afterward.
3. Select the needed columns and remove rows with missing IDs before merging.
4. Merge the datasets using `how="left"` and `validate="one_to_one"`.

The left merges preserve the cleaned base rows. Unmatched information remains missing. The one-to-one validation checks that each merge uses unique IDs on both sides. Runtime is selected from the processed base only, avoiding duplicate runtime columns.

## Features created

`release_date` is converted to a datetime with invalid dates set to missing. Three new features are then created:

| Feature | Calculation | Example for `1995-10-30` |
| --- | --- | --- |
| `release_year` | Extract the year. | `1995` |
| `release_month` | Extract the month. | `10` |
| `release_decade` | `(release_year // 10) * 10` | `1990` |

These features allow later analysis of ratings by release period. Missing dates remain missing in the derived features. `cast_size` and `crew_size` are existing features brought in from the processed credits dataset.

## Minimum vote-count decision

The `compare_vote_cutoffs()` function compares the 25th, 50th, and 75th percentiles of `vote_count` in the merged dataset. It reports the effect of keeping rows at or above each cutoff.

| Percentile | Minimum votes | Rows dropped | Rows kept |
| --- | ---: | ---: | ---: |
| 25th | 3 | 9,293 | 36,140 |
| 50th | 10 | 22,518 | 22,915 |
| 75th | 34 | 33,925 | 11,508 |

The selected minimum is **34 votes**, the observed 75th-percentile value. The filter keeps rows where `vote_count >= 34`, including movies with exactly 34 votes. Missing vote counts do not pass the filter.

This focuses the dataset on movies with more audience votes while reducing coverage of movies with fewer votes. The cutoff retains about **25.33%** of the 45,433 merged rows. Shared vote counts mean percentile cutoffs do not necessarily remove an exact percentage of rows.

For later modeling, `vote_average` is the target, `vote_count` is used for sample selection or weighting, and `id` identifies each movie. Vote count should not be treated as an ordinary prediction feature for this assignment.

## Output and reproducibility

The notebook exports `filtered_merged_df` to:

```text
data/features/07_rating_features.csv
```

The export uses `index=False`. The current code contains 22 merged columns plus the three date features, giving 25 columns. Row counts above come from the notebook's saved outputs.

The path setup uses `Path.cwd().parent.parent`, so the notebook's working directory must be the project's `notebooks/feature_engineering` folder. Run the cells from top to bottom to reload the source data and reproduce the cleaning, merging, filtering, and export.

## Limitations and remaining work

Keeping the first duplicate ID does not resolve differences between conflicting records. Other missing values are retained, and the vote-count filter does not explicitly remove missing target ratings. No prediction model is trained or evaluated in this notebook.

The row count matches the partner's screenshot, but the current notebook has not yet produced the reference's full 34-column set. These 14 columns remain to be added:

| Group | Missing columns |
| --- | --- |
| Log features | `log_budget`, `log_revenue`, `log_popularity` |
| List counts | `genre_count`, `company_count`, `country_count`, `spoken_language_count`, `keyword_count` |
| Boolean features | `has_collection`, `has_director`, `title_differs_from_original` |
| Text counts | `title_word_count`, `overview_word_count` |
| Cast information | `top_cast`, sourced from the processed credits column `top_five_cast` |

After deriving these features, matching the reference's 34-column export also requires excluding the five source columns `release_date`, `budget`, `revenue`, `popularity`, and `belongs_to_collection` from the final column selection.

Source reviewed: `07_rating_features(1).ipynb` and its saved outputs.
