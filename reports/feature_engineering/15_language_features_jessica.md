# Issue #15: Original Language Feature Engineering
Author: Jessica 
## Goal
Get the datatset down to one clean row per movie ID to build features 
## Before we start
`Id` dtype as a float64 and becuase `Id` is a label and not a quantity we had to convert into an integer 

Starting Shape: 45466 rows x 6 columns 

## Key Decisions 
| Step | What happened |
| ----------- | ----------- |
| Junk rows | 3 rows had no valid `id` and were dropped|
| Exact duplicates | 17 rows identical in every column were dropped|
| Conflicting dupes| 13 ids appeared twice and differed on in `popularity`; kept the higher value |
| Missing language | 11 movies had no `origianl_language` in the raw file and were dropped |
| Grouping langauges | grouped movies with <100 movies into `Other` |
| Rename `keywords_names_clean` | Issue wanted it to be under `keywords` |


## Features built 
| Category      | Columns | How they were built  |
| ----------- | ----------- | ------- |
| Date      | `release_year`, `release_month`, `release_decade`      |   Parsed from release_date; decade is the year rounded down to the nearest 10
| Financial and numeric   | `log_budget`, `log_revenue`, `log_popularity`, `runtime`        |log1p applied so zero values are handled
| Production   | `genre_count`, `company_count`, `country_count`, `has_collection`        | Items counted in each list-like column; `has_collection` is True when `belongs_to_collection` is filled
| Credits   | 	`cast_size`, `crew_size`, `has_director`        | Taken from credits.csv; `has_director` is True when `director` is filled
| Keywords and text   | `keyword_count`, `title_word_count`, `overview_word_count`, `title_differs_from_original`        | Item and word counts; title compared with original title
| Modeling inputs   | `genres`, `production_companies`, `production_countries`, `director`, `top_cast`, `keywords`, `title`, `original_title`, `overview`, `status`        | Carried through unchanged




## Notes and Limitations 

When inspecting `df['original_language'].value_counts()` there were notably 89 distinct laguages. Some of the smaller languages were: fr: 2,436 it: 1,529 ja: 1,347 de: 1,079 are each only 2 to 5%. After exapanding the list I found Javanese, Zulu, Samoan and Sinhala all only to have one movie in their language. Tamil sits at 78 movies but I decided to group it into `other` 

A minimum of 100 gives each class enough rows to split into train and test 
After the cutoff: I was left with 24 languages: 23 names langauges and 1 other 

`budget` and `release_date` were stored as text. The first line turns budget into numbers and the second turns release_date into real dates. With `errors='coerce'`, a value that can’t be converted becomes missing (NaN or NaT) and doesn’t crash the cell.

## Final Check 
One row per `id`: confirmed with `assert final['id'].is_unique`

Final shape: 4,422 rows x 30 columns 