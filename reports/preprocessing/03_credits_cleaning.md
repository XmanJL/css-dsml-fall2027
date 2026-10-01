# Release Notes & Issue Resolution Summary
## Executive Summary
This document provides a log on how I went about solving different issues that were assignedn for Isssue 3: Clean Cast and Crew Information.

### 1. Inspect the dataset dimensions, data types, missing values, and duplicate rows 
* **Resolution Strategy:** 
  Checked shape, dtypes, missing values, and duplicate rows (excluding `cast`/`crew`,
since list-type columns can't be checked with `.duplicated()`).
### 2. Parse `cast` and `crew`
* **Resolution Strategy:** 
  `cast`/`crew` were stringified lists of dictionaries. Converted to real Python lists with
`ast.literal_eval`, applied only where the value is a string.
### 3. Check for invalid or duplicated movie ID's
* **Resolution Strategy:** 
  44 movie IDs had duplicate rows (149 total rows involved — some movies appeared 3-4
times). Manually inspected the duplicates; title and release date matched exactly across
copies, confirming these are genuine repeated entries, not different movies incorrectly
sharing an ID. Removed with `drop_duplicates(subset='id', keep='first')`. Verified 0
duplicate IDs remain after cleanup.
### 4. Extracting: Director, Top five cast members, Cast and Crew Size 
* **Resolution Strategy:** 
  From `crew`/`cast`, added:
- `director` — crew member with job == 'Director'
- `top_five_cast` — first five cast members' names (billing order)
- `cast_size` / `crew_size` — count of cast/crew members
- `cast_names` — full list of cast member names, consistent format (every row a list,
  empty list `[]` for the 4 rows with no cast data)
### 5. Store cast memebers in a consistent format 
* **Resolution Strategy:** 
  Cast data was originally stored as a list of dictionaries per movie, with each dictionary
containing extra fields (`character`, `credit_id`, `gender`, `order`, etc.) beyond just
the actor's name. A `cast_names` column was created to hold just a plain list of names,
dropping the extra fields. Every row is guaranteed to be a list — verified with
`df['cast_names'].apply(type).value_counts()`, which returned a single type
(`<class 'list'>`) across all rows, confirming no mixed types remain.
### 6. Handle movies with missing cast or crew information 
* **Resolution Strategy:** 
  4 movies had no matching entry in `credits.csv` after the merge, leaving `cast`/`crew` as
`NaN`. Rather than dropping these rows, each extraction function (`get_director`,
`get_top_five_cast`, `get_cast_size`, `get_crew_size`, `get_all_cast_names`) checks
`isinstance(x, list)` before processing, and returns a safe default (`None`, `[]`, or `0`)
for rows with missing data. This preserves all original movies in the dataset while
avoiding errors from trying to iterate over a missing (NaN/float) value.
### 7. Keep one row per movie 
* **Resolution Strategy:** 
  44 movie IDs had duplicate rows (149 total rows involved, with some movies appearing 3-4
times). The duplicates were inspected directly — title and release date matched exactly
across each set of duplicate rows, confirming these were genuine repeated entries rather
than different movies incorrectly sharing an ID. Removed with
`drop_duplicates(subset='id', keep='first')`, keeping the first occurrence of each movie.
Verified afterward with `df['id'].duplicated().sum()`, which returned 0 — confirming every
movie ID now appears exactly once.
