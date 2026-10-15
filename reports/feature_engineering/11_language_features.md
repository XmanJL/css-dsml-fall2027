# 11 – Language Features

**Notebook:** `notebooks/feature_engineering/11_language_features.ipynb`
**Output:** `data/features/11_language_features.csv` (45,422 rows × 32 columns)
**Presentation time:** ≤ 5 minutes

---

## 1. Goal and scope

Build one row per movie `id` with a target (`target_original_language`) and predictor columns for predicting a movie's original language.

- Base dataset: `data/processed/movies_metadata.csv`
- Everything else is joined to the base by `id` (left joins).

## 2. Feature sources

| Source | Columns used |
|---|---|
| `processed/movies_metadata.csv` (base) | id, release_date, budget, revenue, runtime, popularity |
| `processed/movie_text.csv` | original_language (target source), title, original_title, overview, status |
| `processed/nested_metadata.csv` | genres, production_companies, production_countries, belongs_to_collection (already `A\|B\|C` text) |
| `processed/credits.csv` | director, top_five_cast (renamed `top_cast`), cast_size, crew_size |
| `processed/keywords_clean.csv` | keyword_names_clean (renamed `keywords`) |
| `raw/movies_metadata.csv` | tagline (used only to build `has_tagline`) |

The preprocessing notebooks 01–05 were run first to produce the `processed/` files. Processed files were chosen over raw because they are already cleaned and have no duplicate ids.

**Deliberately not used:** `spoken_languages`. For most films its first value equals `original_language`, so it would leak the answer.

## 3. Cleaning the base and merging

### 3.1 Base cleaning
- Dropped 3 rows with a null `id`. In those rows the columns are shifted, so a poster filename sits in `budget`.
- Found 30 extra rows that repeat an `id`. 17 are exact copies and the other 13 differ only in `popularity`. Kept the row with the highest `popularity` per `id`.
- Converted `id`, `budget`, `revenue` and `runtime` from float to nullable integer (`Int64`), because `revenue` and `runtime` contain missing values. `popularity` stays a float because it has real decimals.

### 3.2 Merge
- Left-joined `movie_text`, `nested_metadata`, `credits` and `keywords_clean` onto the base. All four have 0 duplicate ids, so the merge adds columns only.
- Asserted the row count did not change and `id` stayed unique.
- 11 movies have no `original_language`. They cannot be used for a target, so they were dropped.

| Step | Rows |
|---|---|
| Raw base | 45,466 |
| After dropping null ids | 45,463 |
| After removing duplicate ids | 45,433 |
| After dropping missing language | **45,422** |

## 4. Target: `target_original_language`

- 89 distinct language codes. `en` is 71% of movies, then `fr` (2,436), `it` (1,529), `ja` (1,347) and `de` (1,079). The rest drop off steeply.
- **Cutoff: keep languages with at least 50 movies; group the rest as `Other`.**
  - Very small classes are not learnable and cannot be evaluated reliably.
  - 28 languages are kept and 61 are grouped. That gives 29 classes.
  - `Other` is 562 movies (1.2%). The smallest kept language has 57 movies and the largest dropped one has 45, so no language sits right at the cutoff.

## 5. Feature engineering decisions

### 5.1 Date: `release_year`, `release_month`, `release_decade`
- Parsed `release_date` with `errors="coerce"`. All dates parse; 87 movies have no date and stay missing.
- Years run from 1874 to 2020. Decades are lopsided: the 2000s and 2010s hold about half the data.

### 5.2 Financial and numeric: `log_budget`, `log_revenue`, `runtime`, `log_popularity`
- Logs use `log1p` (`log(1 + x)`) to compress very large ranges.
- **A budget or revenue of 0 means "unknown", not "free".** 80.5% of budgets and 83.7% of revenues are 0. These were converted to missing before taking the log, so unknown does not look like a tiny budget.
- A runtime of 0 is also treated as missing (1,557 movies).

### 5.3 Production: `genre_count`, `has_collection`, `company_count`, `country_count`
- Counts come from splitting the `A|B|C` text and counting the pieces. Missing text counts as 0.
- `has_collection` is 1 when `belongs_to_collection` is present. Only about 10% of movies belong to a collection.
- A 0 in a count can mean "none listed" and not "none exist" (for example, 26% of movies have no company listed).

### 5.4 Credits: `cast_size`, `crew_size`, `has_director`
- `cast_size` and `crew_size` come from `credits.csv`. One movie (id 401840) has no credits row, so its sizes were filled with 0.
- `has_director` is 1 when a director name exists. 888 movies (2%) have none listed.

### 5.5 Keywords and text: `keyword_count`, `title_word_count`, `overview_word_count`, `title_differs_from_original`
- `keyword_count` parses each keyword list with `ast.literal_eval`. 14,337 movies (32%) have no keywords and get 0.
- Word counts split on whitespace. Missing text counts as 0 words (1,103 missing overviews, 3 missing titles).
- `title_differs_from_original` is 1 when `title` differs from `original_title`. A missing title is not flagged.
- **This is the clearest signal in the table:** the titles differ for 73.5% of non-English movies but only 5.3% of English movies.

### 5.6 Modeling inputs (readable text)
- Columns: `genres`, `production_companies`, `production_countries`, `director`, `top_cast`, `keywords`, `title`, `original_title`, `overview`, `status`.
- `top_cast` and `keywords` were converted from list-strings to the same `A|B|C` format the other columns use. No name contains `|`, so the separator is safe.
- Missing text is filled with an empty string in this table. Note that reading the CSV back with pandas turns empty strings into NaN again.
- `status` is almost constant (44,975 of 45,422 are "Released"), so expect little signal from it.

### 5.7 Additional features
- **`has_tagline`** (from the raw file): 55.6% of English movies have a tagline versus 18.8% of non-English. Correlation with "is English" is 0.34, the strongest of the candidates tested. It mostly measures how well-documented a movie is, not its content.
- **`budget_known`**: 1 when a budget was recorded. 23% of English movies have one versus 11% of non-English.
- **Considered and rejected: `adult`.** Only 9 of 45,422 movies are flagged, so it carries almost no signal. `vote_count` (correlation 0.10) and `has_homepage` (0.08) were weaker than the two chosen features.

## 6. Final table and validation

- 45,422 rows × 32 columns. Checks: `id` is unique, the target has no missing values, and `original_language` is not in the output (it would leak the target).
- Columns, in order: identifier, target, date, financial/numeric, production, credits, keywords/text, extras, then the ten modeling inputs.
- Raw columns replaced by engineered ones (`budget`, `revenue`, `popularity`, `release_date`, `belongs_to_collection`) are not in the output.

| Column | Missing |
|---|---|
| `log_revenue` | 38,024 |
| `log_budget` | 36,542 |
| `runtime` | 1,817 |
| `release_year`, `release_month`, `release_decade` | 87 each |
| `log_popularity` | 3 |

All other columns have no missing values.

