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

## Preprocessing Decisions

For my part of the preprocessing, I worked with `id`, `original_title`, `genres`, `production_countries`, and `spoken_languages`.

For the three nested columns, I used `ast.literal_eval` to safely turn the values into lists and dictionaries. I then extracted the `name` from each dictionary so that the final data would be easier to read and use. When a movie had multiple values, I separated them using `|`.

I also removed repeated values within the same movie and stripped extra whitespace from the names. Empty lists, missing values, or values that did not have any usable names were kept as missing values. Some of the language entries had a language code but a blank `name`, so I skipped those blank names instead of replacing them with the code since the goal was to keep readable language names.

While checking the `id` column, I found 3 rows with invalid IDs that were actually dates. Looking at the full rows showed that the data in those rows was shifted/malformed, so I removed those records.

I also found 29 movie IDs that appeared more than once, involving 59 rows total. I checked the cleaned values for these duplicate IDs and they were the same, so I kept the first occurrence of each ID to make sure there was only one row per movie.

Finally, I checked that there were no duplicate IDs, repeated values within the nested columns, or empty strings left in the cleaned data.

## Cleaning the Nested Columns

Some of the columns, such as `belongs_to_collection`, `production_companies`, `genres`, `production_countries`, and `spoken_languages`, were stored as strings containing lists and dictionaries. We used `ast.literal_eval` to safely convert these strings back into their actual Python structures.

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
