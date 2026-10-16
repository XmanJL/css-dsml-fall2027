# Feature Engineering: Revenue Features

**Author:** Aaron (`aaronstrong05`) | **Branch:** `Aaron-4` | **Notebook:** `notebooks/feature_engineering/08_revenue_features.ipynb`  
**Deliverable:** `data/features/08_revenue_features.csv`

---

## 1. Goal

The goal of this notebook is to clean up the movie data, combine all helper tables into one big dataset, and create new features (columns) that will help predict a movie's box office revenue.

---

## 2. Steps Taken

### Step 1: Clean Up 0s and Missing Values
In this dataset, a `0` for revenue, budget, or runtime doesn't mean the movie cost \$0 or made \$0—it just means the real number was never recorded.

If we leave `0`s in the data, it messes up math like averages and formulas. So we changed all `0`s to `NaN` (blank/missing):
- **Revenue:** 38,052 movies had a `0`. Changed to `NaN` (total missing now: 38,058).
- **Budget:** 36,573 movies had a `0`. Changed to `NaN` (total missing now: 36,576).
- **Runtime:** 1,558 movies had a `0`. Changed to `NaN` (total missing now: 1,821).

### Step 2: Remove Bad and Duplicate IDs
To make sure our movies line up cleanly when merging:
- Dropped **3 movies** that were missing an ID.
- Dropped **32 duplicate movies**, keeping only the first copy.

### Step 3: Merge All Datasets Together
We started with the main metadata file and merged in 4 other cleaned files using the movie `id`:
1. **`nested_metadata.csv`**: genres, collections, production companies, countries, and languages.
2. **`movie_text.csv`**: title, overview, language, and status.
3. **`keywords_clean.csv`**: movie keywords.
4. **`credits.csv`**: director, top cast, cast size, and crew size.

We also dropped duplicate columns created during the merge (like `release_date_y`) and renamed columns to match the project requirements:
- `keyword_names_clean` → `keywords`
- `top_five_cast` → `top_cast`

---

## 3. New Features Created

We created 15 new columns from the data to help predict revenue:

### Target (What we want to predict)
- **`target_log_revenue`**: The log of revenue (`np.log1p(revenue)`). Box office numbers can range from a few thousand dollars to billions. Taking the log shrinks the numbers down to a more balanced scale so machine learning models can learn better.

### Dates & Time
- **`release_year`**: The year the movie came out (e.g. `1995`).
- **`release_month`**: The month it was released (1 through 12) to capture seasonal trends like summer movies or holiday releases.
- **`release_decade`**: The decade (e.g. `1990`, `2000`), calculated by dividing the year by 10 and multiplying by 10.

### Log Numbers (Scaling down big numbers)
- **`log_budget`**: Log of the movie's budget (`np.log1p(budget)`).
- **`log_popularity`**: Log of the popularity score (`np.log1p(popularity)`).

### Counts & Yes/No Flags
- **`genre_count`**: How many genres the movie has (e.g. Animation + Comedy + Family = 3).
- **`country_count`**: How many countries helped make the movie.
- **`company_count`**: How many production studios worked on it.
- **`language_count`**: How many languages are spoken in the movie.
- **`has_collection`**: `True` if the movie belongs to a franchise/series (like Toy Story), `False` if standalone.
- **`has_director`**: `True` if a director is listed, `False` if missing.
- **`keyword_count`**: Total number of keywords listed for the movie.

### Words & Text
- **`title_word_count`**: Number of words in the movie title.
- **`overview_word_count`**: Number of words in the movie summary.
- **`title_differs_from_original`**: Checks if the English title is different from the original foreign title.

---

## 4. Final Output

- Saved the finished table to: **`data/features/08_revenue_features.csv`**
- It contains all merged movie details plus all the newly created features.

---

## 5. Things to Keep in Mind

- **A lot of movies are missing revenue:** About 83% of the movies in this dataset do not have reported revenue. When training a model, we will only train on the movies that have a real revenue number.
- **Reproducing the file:** The `data/` folder is ignored by Git. Running `08_revenue_features.ipynb` from start to finish will regenerate this file at any time.
