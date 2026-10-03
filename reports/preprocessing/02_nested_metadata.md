# Nested Metadata Preprocessing

## Overview

For this part of the preprocessing, we worked with the nested metadata columns in `movies_metadata.csv`. The main goal was to take the nested values and turn them into a cleaner format that would be easier to work with later.

The columns included in this part were:

- `id`
- `original_title`
- `release_date`
- `genres`
- `belongs_to_collection`
- `production_companies`
- `production_countries`
- `spoken_languages`

## Cleaning the Nested Columns

Some of the columns, such as `belongs_to_collection`, `production_companies`,  `genres`, `production_countries`, and `spoken_languages`, were stored as strings containing lists and dictionaries. We used `ast.literal_eval` to safely convert these strings back into their actual Python structures.

From there, we extracted the readable `name` values instead of keeping the full dictionaries.

For example:

`[{'id': 16, 'name': 'Animation'}, {'id': 35, 'name': 'Comedy'}]`

became:

`Animation|Comedy`

If a movie had multiple values, we separated them using `|`. We also removed repeated values within the same movie and stripped extra whitespace.

Empty lists and values that did not contain any usable names were treated as missing values. For `spoken_languages`, there were also some entries that had a language code but a blank name. We decided to skip the blank names instead of replacing them with the language code because we wanted to keep the final values readable.

## Problems We Found

While checking the `id` column, we found 3 rows where the IDs were actually dates instead of movie IDs. After looking at the full rows, we found that the data was shifted into the wrong columns, so these rows were treated as malformed and removed.

We also found 29 movie IDs that were repeated, involving 59 rows total. We compared the cleaned information for these repeated IDs and found that the values were the same. Because we only wanted one row per movie, we kept the first occurrence of each ID and removed the extra copies.

## Validation

After cleaning the data, we checked the results again to make sure:

- There were no duplicate movie IDs.
- There were no repeated values within the cleaned nested columns.
- There were no empty strings left in the cleaned nested columns.
- Each movie was represented by only one row.

After removing the malformed and duplicate records, our cleaned portion contained **45,433 movies**.

## Final Format

The final dataset keeps the movie ID and title while changing the nested metadata into a simpler and more readable format. This should make the data easier to use later for analysis and machine learning.
