# Feature Engineering: Revenue Features

**Author:** Aaron (`aaronstrong05`) | **Branch:** `Aaron-4` | **Notebook:** `notebooks/feature_engineering/08_revenue_features.ipynb`  
**Deliverable:** `data/features/08_revenue_features.csv`

---

## 1. Goal

The goal of this notebook is to clean up the movie data, combine all helper tables into one dataset, and create new features (columns) that will help predict a movie's box office revenue.

---

## 2. Steps Taken

### Step 1: Check Zeros and Missing Values
In this dataset, a `0` for revenue, budget, or runtime doesn't mean the movie cost \$0 or made \$0. It means the real number was never recorded. The budget column was stored as text, so it was converted to numbers first.

Before cleaning, almost all of the "missing" values were stored as `0` rather than blank:

| Column | Blank | Zero |
|---|---|---|
| Revenue | 6 | 38,052 |
| Budget | 3 | 36,573 |
| Runtime | 263 | 1,558 |

All `0`s were changed to `NaN` (missing) so they don't distort the math:
- **Revenue:** total missing is now 38,058.
- **Budget:** total missing is now 36,576.
- **Runtime:** total missing is now 1,821.

### Step 2: Remove Bad and Duplicate IDs
- Dropped **3 rows** that had no ID.
- Dropped **30 duplicate rows**, keeping only the first copy of each ID.

### Step 3: Merge All Datasets Together
We started with the main metadata file and merged in 4 other cleaned files using the movie `id` (left joins, so no movies were lost):
1. **`nested_metadata.csv`**: genres, collections, production companies, countries, and languages.
2. **`movie_text.csv`**: title, overview, language, and status.
3. **`keywords_clean.csv`**: movie keywords.
4. **`credits.csv`**: director, top cast, cast size, and crew size.

The merge created duplicate columns (`release_date_y`, `original_title_y`). Those were dropped and the `_x` suffix was removed. Two columns were renamed to match the project requirements:
- `keyword_names_clean` → `keywords`
- `top_five_cast` → `top_cast`

### Step 4: Create Features
See Section 3.

### Step 5: Drop Columns and Fill Missing Values
- **Dropped:** `budget`, `revenue`, `runtime`, `release_date`, and `popularity`. Their information is now in the log and date features.
- **Numeric columns:** missing values filled with `0`.
- **Text columns:** missing values filled with an empty string.
- **Yes/No columns:** missing values filled with `False`.
- A final check confirmed no missing values remain.

---

## 3. New Features Created

We created 16 new columns from the data to help predict revenue:

### Target (What we want to predict)
- **`target_log_revenue`**: The log of revenue (`np.log1p(revenue)`). Box office numbers range from a few thousand dollars to billions. Taking the log shrinks them to a more balanced scale so models can learn better.

### Dates & Time
- **`release_year`**: The year the movie came out (e.g. `1995`).
- **`release_month`**: The month it was released (1 through 12), to capture seasonal trends.
- **`release_decade`**: The decade (e.g. `1990`, `2000`), found by dividing the year by 10 and multiplying by 10.

### Log Numbers
- **`log_budget`**: Log of the budget (`np.log1p(budget)`).
- **`log_popularity`**: Log of the popularity score (`np.log1p(popularity)`).

### Counts & Yes/No Flags
- **`genre_count`**: How many genres the movie has.
- **`country_count`**: How many countries helped make the movie.
- **`company_count`**: How many production studios worked on it.
- **`language_count`**: How many languages are spoken in the movie.
- **`has_collection`**: `True` if the movie belongs to a franchise/series, `False` if standalone.
- **`has_director`**: `True` if a director is listed, `False` if missing.
- **`keyword_count`**: Number of keywords listed for the movie.

### Words & Text
- **`title_word_count`**: Number of words in the movie title.
- **`overview_word_count`**: Number of words in the movie summary.
- **`title_differs_from_original`**: `True` if the title is different from the original title. Three movies had a missing `title`, so it was filled with their `original_title` first.

---

## 4. Final Output

- Saved the finished table to: **`data/features/08_revenue_features.csv`**
- It contains the merged movie details plus the new features. The original `budget`, `revenue`, `runtime`, `release_date`, and `popularity` columns are not included.

---

## 5. Things to Keep in Mind

- **A lot of movies are missing revenue:** About 84% of the movies do not have a reported revenue. When training a model, we will only train on the movies that have a real revenue number.
- **Missing values are filled with 0:** In the numeric columns, a filled-in `0` can't be told apart from a real `0`. This applies to `log_budget` in particular, since most budgets were missing.
- **Reproducing the file:** The `data/` folder is ignored by Git. Running `08_revenue_features.ipynb` from start to finish will regenerate this file at any time.
