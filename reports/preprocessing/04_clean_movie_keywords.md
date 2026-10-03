# Issue #4: Clean Movie Keywords

## 1. Goal
Run `models\cleanKeywords.py` to read `data\raw\keywords.csv` and output `data\processed\cleaned_keywords.csv`
Preprocess `keywords.csv` dataset by cleaning and normalizing the keywords and rows. Output a new, cleaned version for feature engineering.

## 2. Raw Data
- Dataset dimensions: 46,419 rows x 2 columns (one row per movie).
- Columns: [`id`, `keywords`] Ex:

    | row | id | keywords |
    |---|---|---|
    |73| 28387 | [{'id': 3616, 'name': 'college'}] |

- Duplicate rows: 987
- Rows with no keywords are set as empty lists instead of null.

## 3. Decisions 
- Drop rows with empty keyword lists. Ex:

    | row | id | keywords |
    |---|---|---|
    |9| 45325 | [] |
 
- Drop rows with duplicate movie IDs and identical keywords. Ex:

    | row | id | keywords |
    |---|---|---|
    |35784| 55949 | [{'id': 293, 'name': 'female nudity'}, {'id': 3133, 'name': 'vampire'}, {'id': 9717, 'name': 'based on comic'}, {'id': 9951, 'name': 'alien'}] |
    |36741| 55949 | [{'id': 293, 'name': 'female nudity'}, {'id': 3133, 'name': 'vampire'}, {'id': 9717, 'name': 'based on comic'}, {'id': 9951, 'name': 'alien'}] |
- Combine rows with duplicate movie IDs but different keywords. Ex: 

    | row | id | keywords |
    |---|---|---|
    |35784| 55949 | [{'id': 293, 'name': 'female nudity'}, {'id': 3133, 'name': 'vampire'}] |
    |36741| 55949 | [{'id': 9717, 'name': 'based on comic'}, {'id': 9951, 'name': 'alien'}] |
    
    They would combine into 1 row:
    | row | id | keywords |
    |---|---|---|
    |35784| 55949 | [{'id': 293, 'name': 'female nudity'}, {'id': 3133, 'name': 'vampire'}, {'id': 9717, 'name': 'based on comic'}, {'id': 9951, 'name': 'alien'}] |
- Convert all keywords to lowercase
- Remove only unnecessary whitespaces in keywords
    
    ex.  `keyword: "high school"` remains as `"high school"`
- Remove duplicate keywords within a movie
- Leave hyphens in the keywords
    
    ex.  `keyword: "dog-sledding race"`

## 4. Results
`data\processed\cleaned_keywords.csv`
- Total: 31,092 rows, 2 columns
- Removed 15,327 rows (14,795 empty lists, 532 identical duplicate rows) from the original 46,419
- Each row has 1 movie and a clean, consistent format

    ex. `15602,"["fishing", "best friend", "duringcreditsstinger", "old men"]"`