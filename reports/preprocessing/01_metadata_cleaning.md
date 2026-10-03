# Movie Metadata Cleaning

**Presentation length:** about 3 minutes

## Objective

Profile `movies_metadata.csv` and clean its six core identifier, date, and numeric fields while preserving all other columns and source rows. The raw CSV remains unchanged.

## Initial profile

The dataset contains **45,466 rows and 24 columns**. The six target fields were initially read as strings for `id`, `release_date`, `budget`, and `popularity`, and as floating-point values for `revenue` and `runtime`. There were **17 exact duplicate rows beyond their first copies** (33 rows belong to duplicate groups). These were inspected and retained because this task focuses on core-column conversion rather than record deduplication.

Before cleaning, target-field missing values were: `id` 0, `release_date` 87, `budget` 0, `revenue` 6, `runtime` 263, and `popularity` 5.

## Cleaning decisions and results

- `id` was converted to nullable integer. Three nonnumeric IDs were set to missing.
- `release_date` was parsed as a datetime. Three nonmissing values were invalid, resulting in **90 missing dates** after conversion.
- `budget`, `revenue`, and `runtime` were converted to nullable integers. Nonnumeric values were coerced to missing; zero values were treated as unavailable-value sentinels. Missing totals are **36,576**, **38,058**, and **1,821**, respectively.
- `popularity` was converted to floating point. One invalid nonmissing value was set to missing; its 66 zero values were retained. It has **6 missing values** after conversion.

The three bad IDs are date-shaped strings in structurally shifted records; their corresponding date fields contain `1`, `12`, and `22`. These records were not guessed or repaired. Their invalid values in the cleaned columns are missing, and the rows remain in the output.

## Output and validation

The cleaned dataset preserves the original **45,466 × 24** shape and all 17 duplicate rows. The notebook writes the local output to `data/processed/movies_metadata.csv`, reloads it, and checks the shape and target missing-value counts. The processed CSV is excluded from Git by `.gitignore`; CSV does not retain pandas dtypes, so downstream readers should parse `id`, `budget`, `revenue`, and `runtime` as nullable integers and `release_date` as a date.

## Caveats

Treating zero budget, revenue, and runtime as missing is a domain assumption based on these fields' frequent zero placeholders. If a downstream use case considers any of those zeros meaningful, revisit that policy. Exact duplicates and structurally shifted rows are documented but not removed.
