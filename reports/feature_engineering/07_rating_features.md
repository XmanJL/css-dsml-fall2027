# Issue #7: Audience Rating Feature Engineering

## 1. Goal
Create features for predicting `vote_average`.  
Use `data/processed/movies_metadata.csv` as the base dataset.  
Retrieve additional useful columns from `data/raw/` or `data/processed/` using `id`.  
Inspect `vote_average` and select a minimum `vote_count`.  

## 2. Data
Our main source file was `data\processed\movies_metadata.csv`. 
- 45,433 rows × 6 columns 
- Columns: `id`, `release_date`, `budget`, `revenue`, `runtime`, `popularity`

We pulled additional relevant columns from `data\raw\movies_metadata.csv`, `data\processed\credits.csv` and `data\processed\keywords_clean.csv` such as:
- `vote_average`, `vote_count` are the target and target selection.
- `belongs_to_collection`, `cast_size`, `crew_size`, `genres`, `production_companies`, `production_countries`, `spoken_languages`, `director`, `top_cast`, `keywords`, `title`, `original_title`, `overview`, `original_language`, `status`


## 3. Key Decisions
Pull from both raw and preprocessed data files. 
- We fetched data from the raw `movie_metadata.csv` file to get back the columns that were previously dropped.
- We fetched data from the processed `credits.csv` and `keywords_clean.csv` to gather relevant columns that were already cleaned.

Why we picked these columns/features.
- These columns/features can be used to infer the rating a person might give the movie.

Columns that were created based on other existing columns:
| Created column | Source column |
|---|---|
| `release_year` | `release_date` |
| `release_month` | `release_date` |
| `release_decade` | `release_date` |
| `log_budget` | `budget` |
| `log_revenue` | `revenue` |
| `log_popularity` | `popularity` |
| `genre_count` | `genres` |
| `has_collection` | `belongs_to_collection` |
| `company_count` | `production_companies` |
| `country_count` | `production_countries` |
| `spoken_language_count` | `spoken_languages` |
| `has_director` | `director` |
| `keyword_count` | `keywords` |
| `title_word_count` | `title` |
| `overview_word_count` | `overview` |
| `title_differs_from_original` | `title`, `original_title` |

Dropping rows that don't meet the minimum vote count.
- `MIN_VOTE_COUNT` = 34
- Based on the vote count stats:

| Statistic | Vote Count |
|---|---:|
| Mean | 109.935989 |
| Standard deviation | 491.466335 |
| Minimum | 0.0 |
| 25th percentile | 3.0 |
| 50th percentile (median) | 10.0 |
| 75th percentile | 34.0 |
| Maximum | 14,075.0 |

75% of movies have 34 or less `vote_count`, so we tried to balance keeping enough rows while making sure there are enough votes to make the `vote_average` meaningful.   
At 50%, 10 votes is too little, and choosing too small of a minimum creates noisy data.

## 4. Results
Resulting file is: `data\features\07_rating_features.csv`  
Dimensions: 11,508 rows x 34 columns  
Final columns:  
`['id', 'vote_average', 'vote_count', 'release_year', 'release_month', 'release_decade', 'log_budget', 'log_revenue', 'runtime', 'log_popularity', 'genre_count', 'has_collection', 'company_count', 'country_count', 'spoken_language_count', 'cast_size', 'crew_size', 'has_director', 'keyword_count', 'title_word_count', 'overview_word_count', 'title_differs_from_original', 'genres', 'production_companies', 'production_countries', 'spoken_languages', 'director', 'top_cast', 'keywords', 'title', 'original_title', 'overview', 'original_language', 'status']`  
Dropped 75% of rows, going from 45,433 to 11,508 because of the `MIN_VOTE_COUNT`

## 5. Additional Notes
We edited the 01_metadata_cleaning model and preprocessed `data\raw\movies_metadata.csv` again because the original cleaned metadata csv file still had duplicate rows. 

`MIN_VOTE_COUNT` can be edited to produce csv files with more or less rows. Keep in mind, there should be a minimum for how many votes a movie should have for its average score to be accurate or meaningful. Otherwise, it would create noisy data for training and testing.