# Genre Feature Engineering

## Overview

For our feature, we were tasked to apply one-hot encoding in order to take the genres of a movie from a string in a single column, into one genre for its own column per movie with a single value contained in each of those columns being a binary value of if the movie contained that genre. Furthermore, we created extra columns that provided more detail about certain existing columns, such as:

- `has_collection` pulled from `belongs_to_collection`
- `release_year` pulled from `release date`
- `has_director` pulled from `director`
- `company_count` pulled from `production_companies`

as examples.

## Predictor column implementation

For my part, I had worked with the `Production`, `Credits`, and `Keywords and text` categories of columns. The data for each of these columns was pulled from `nested_metadata.csv`, `credits.csv`, `keywords_clean.csv`, and `movie_text.csv`. 

`id` in `nested_metadata.csv` was first cleaned of duplicates, `nan` values, and converted into `int` for every value as they were originally stored as `float`. Every dataset was combined into one dataframe with unnecessary columns dropped, which would be my working dataset and additional predictor columns would be added to a copied final dataframe.

For `has_collection`, and `has_director`, I created a new column which pulled from the `belongs_to_collection` and `director` columns respectively and checked if that column was `nan`. The result of which would be placed into the new predictor columns.

`company_count`, `country_count`, and `spoken_languages_count` would use `.str.split("|").str.len()` to split the cell's string over `|`, then count the elements in that list. Then, using `pd.to_numeric(df[column], errors='coerce').fillna(0).astype(int)`, the `float` values would be converted to `int` while also filling in any `nan` values with 0 for clarity, as logically, their count is 0.

`keyword_count` first had to convert the `string` values into `list` using `ast.literal_eval()` and getting the length of that list, before using a similar method to the other count columns to ensure the data is stored as an `int` and `nan` values are stored as 0.

`title_word_count` and `overview_word_count` were similar to the above method, but instead split across whitespace as the values are regular sentences or titles. They again were ensured to be stored as `int` and `nan` values stored as 0.

`title_differs_from_original` uses `np.where()` to compare the `title` and `original_title` columns, and return true if they are not equivalent, and false if they are equivalent.

## Genre feature implementation

A list of all unique genres was created by using `df['genres'].dropna().str.split("|").explode().unique()`. By looping through all the genres in this list, a new column could be created for each of them by checking if the string in `genres` column for each movie contained something from this list, and converting that result into an `int`.

## Final format

The final dataset contains all original columns that data was pulled from unless it was made absolete by a new column, as well a renaming columns for clarity. This includes dropping `genres`, and renaming `keyword_names_clean` to `keywords` and `top_five_cast` to `top_cast`.

# Grecia's Report
# Date and Numerical Feature Engineering

## Overview

For my part of the feature engineering task, I created additional date and numerical features using the existing movie metadata dataset. The goal was to provide more useful information about each movie by extracting information from its release date and transforming numerical values such as budget, revenue, and popularity. These features can be used as inputs for future data analysis and machine learning models.

## Date Feature Implementation

The `release_year`, `release_month`, and `release_decade` columns were derived from the existing `release_date` column in `movies_metadata.csv`.

First, the release dates were converted into datetime values using `pd.to_datetime()` with `errors="coerce"` so that invalid dates would be handled as missing values.

The `release_year` column stores the year a movie was released, while `release_month` stores its release month. The `release_decade` column groups movies by decade by rounding the release year down to the nearest multiple of ten. For example, a movie released in 1995 would have a `release_decade` value of 1990.

These features make it easier to analyze patterns in movie releases across different years, months, and decades.

## Numerical Feature Implementation

The `log_budget`, `log_revenue`, and `log_popularity` columns were created from the existing `budget`, `revenue`, and `popularity` columns. These source columns were converted to numeric values using `pd.to_numeric()` with `errors="coerce"` to handle values that could not be converted.

The budget, revenue, and popularity features were transformed using `np.log1p()`, which calculates the natural logarithm of one plus the original value. This transformation can help reduce the effect of very large values and make highly skewed numerical data more manageable for analysis. Negative values were treated as missing before applying the transformation.

The `runtime` column was retained as a numerical feature representing the movie's duration in minutes.

## Final Format

The new features were merged into the existing feature-engineered dataset using the movie `id` column. Before merging, duplicate IDs and records with missing IDs were removed from the metadata used to create these features. The merge was validated as one-to-one to help ensure that the additional features did not introduce duplicate movie records.

The resulting dataset contained 45,433 rows and 51 columns, with zero duplicate IDs after the merge. The final dataset was saved as `data/features/10_genre_features.csv`.
