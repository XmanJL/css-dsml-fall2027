# Issue #13: Popularity Feature Engineering

**Current: Requirement 7 — Document all feature sources and decisions.**

## Goal and Result

Create features for predicting movie popularity.

The output contains **45,430 movies and 34 columns**, with one row
per movie ID. It includes the 32 required columns and two additional
financial reporting flags.

Output: `data/features/09_popularity_features.csv`.

## Step 1: Load and Validate the Base
(Requirement 1: Use processed movies_metadata.csv.)

The prepared base contains 45,433 movies with unique integer IDs.
Before feature engineering, 30 duplicate-ID rows were removed,
keeping the first occurrence, and three rows without IDs were excluded.
The original processed file and excluded rows were backed up locally.

## Step 2: Inspect Popularity and Create the Target
(Requirements 3 and 4: Inspect popularity and create target_log_popularity.)

Three movies had missing popularity and were excluded from the feature
output. No nonnumeric, infinite, or negative popularity values were found.

Kept 66 zero values and retained unusually high values because
magnitude alone does not establish an error.

Created `target_log_popularity = log1p(popularity)`.
The maximum changed from approximately 547.49 to 6.31.

## Step 3: Join Additional Sources
(Requirement 2: Retrieve useful columns using id.)

Used left joins with one-to-one validation to preserve all 45,430 movies.

| Source in data/processed | Information used |
| --- | --- |
| movies_metadata.csv | Popularity, release date, budget, revenue, runtime |
| credits.csv | Director, top cast, cast size, crew size |
| nested_metadata.csv | Genres, collection, companies, countries, languages |
| movie_text.csv | Title, original title, overview, original language, status |
| keywords_clean.csv | Cleaned keyword lists |

One movie had no credits match; 14,341 had no cleaned keyword match.
All movies matched nested metadata and movie text.

Renamed `top_five_cast` to `top_cast` and
`keyword_names_clean` to `keywords`.

## Step 4: Create Features
(Requirement 5: Create the required feature columns.)

- **Dates:** extracted release year, month, and decade; kept 84 missing
  dates as unknown.
- **Financial:** applied log1p to positive budget and revenue.
  Treated zeros as unknown, an assumption because reporting status
  is unavailable. Added `budget_reported` and `revenue_reported`.
- **Runtime:** treated zero as unknown, giving 1,815 missing values.
  Retained large positive durations pending evidence of an error.
- **Production:** counted pipe-separated names and created
  `has_collection`. Blank fields mean zero listed entries, not proof
  that none exist.
- **Credits:** reused Task 1 counts and created `has_director`.
  Preserved missing values for the unmatched credits record.
- **Keywords:** parsed stored lists and counted nonblank names.
  Ignored blank entries found in four source lists. Preserved unknown
  keyword information as missing.
- **Text:** counted whitespace-separated title and overview words.
  Compared titles after normalizing capitalization and whitespace.
  Preserved 1,104 missing overviews as missing word counts.

Retained the requested text and categorical modeling inputs.

## Step 5: Validate and Export
(Requirement 6: Confirm one row per id.)

Verified required columns, present and unique integer IDs, retained
movie IDs, finite nonnegative targets, and the target formula.

Exported without a pandas row index, then read the CSV back using
the original column types. Verified values, column order, dimensions,
and missing-value counts.

The data folder is gitignored; the dataset stays local.
The notebook reproduces the feature-building steps from the prepared
processed inputs.

## Limitations and Modeling Decisions

Missing predictor values remain unknown; no mean or median imputation
was performed. Any learned imputation should be fitted on training
data only.

Very small positive financial amounts and unusually long runtimes
remain source-quality caveats.

Raw popularity is excluded from the exported table because it reveals
the target. Revenue features are included as requested, but must be
excluded from a model intended to predict popularity before release.

This task prepares features; it does not train or evaluate a model.