# Movie Metadata Cleaning

## Objective

Clean the core numeric and date columns in `movies_metadata.csv` and
produce a processed dataset containing only the required columns.

## Initial Dataset

The original dataset contained 45,466 rows and 24 columns.

There were 13 exact duplicate rows in the initial dataset.

## Preprocessing

### Movie ID

Three records contained dates instead of valid numeric movie IDs.
These records were removed. The remaining IDs were converted to an
integer type.

### Release Date

The `release_date` column was converted to a datetime type.
There were 87 missing or invalid dates**. These were retained as
missing values rather than removing the movie records.

### Budget

The `budget` column was converted to an integer numeric type.

There were 36,573 zero-budget values and no negative budgets.
Zero values were treated as unknown rather than removing
the movie records.

### Revenue

The `revenue` column was already numeric.

There were 3 missing/invalid values, 38,052 zero values, and
no negative revenue values. Zero values were treated as
unknown rather than removing the movie records.

### Runtime

The `runtime` column was already numeric.

There were 260 missing values and 1,558 zero values.
Zero runtimes were treated as missing values because a zero-minute
movie runtime is not meaningful.

### Popularity

The `popularity` column was converted to a numeric type.

Three values could not be converted to numbers and were treated as
missing. There were no negative popularity values.

### Duplicates

After preprocessing, 17 exact duplicate rows were removed.

There were also 13 duplicate movie ID. To keep one row per movie,
the first of each duplicated movie ID was retained.

## Final Dataset

The final dataset contains 45,433 rows and 6 columns.

The final columns are:

- `id`
- `release_date`
- `budget`
- `revenue`
- `runtime`
- `popularity`

Final checks:

- Exact duplicate rows: 0
- Duplicate movie IDs: 0

The processed dataset was saved as:

`data/processed/movies_metadata.csv`