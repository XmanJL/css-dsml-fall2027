# Issue #4: Clean Movie Keywords

## 1. Goal
Preprocess `keywords.csv` dataset by cleaning and normalizing the keywords and rows. Output a new, cleaned version for feature engineering.

## 2. Raw Data
- Dataset dimensions: 46419 rows x 2 columns (one row per movie)
- Columns: [`id`, `keywords`]
- Duplicate rows: 987

## 3. Decisions 
- Drop rows with missing and empty keyword lists
- Drop rows with duplicate movie IDs and identical keywords
- Combine rows with duplicate movie IDs but different keywords
- Convert all keywords to lowercase
- Remove any unnecessary whitespaces in keywords
- Remove duplicate keywords within a movie

## 4. Results
`cleaned_keywords.csv`
- 31,092 rows, 2 columns