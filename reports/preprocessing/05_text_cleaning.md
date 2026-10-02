# Issue #5: Clean Movie Text Information

**Author:** Hector (`futurectrowner`) | **Branch** `hector-5` | **Notebook:** `notebooks/processing/05_text_cleaning.ipynb` **Dataset:** `movies_metadata.csv`

## Goal

Clean `data/raw/movies_metadata.csv` and preserve it's original meaning.

## Columns retained

The processed dataset contains these six required columns:

- `id`
- `title`
- `original_title`
- `overview`
- `status`
- `original_language`

the `id` column was retained so every movie can be identified. I made a copy beforehand so the raw data wouldn't be affected when I was working through this.

## Whitespace cleanup

I removed unnecessary whitespace from the beginning and end text by using the method `.str.strip()` and iterated it through a for loop to go through the whole filtered dataframe.

It didn't remove any spaces between the strings on the column so it would still be able to be readable.

## Standardizing missing and placeholder values

I investigated the sum of placeholder values with `pd.NA`. I didn't choose to interpolate because we are working with strings and not integers. So I checked the sum of missing values which with `isna()` and filled in the all filtered set with `.fillna(pd.NA)` so they all represent the same value. I checked each of the columns by printing them out and they all have the same value of `NaN`.

## Investigating Duplicate titles

I investigated duplicate titles and didn't remove any rows based off of that.

## Comparing `title` and `original_title`

I compared `title` and `original_title`. There were about 79 rows that were listed as duplicates and I listed them all by using calling the `.loc[]` method and filtering out specifically `title` , `original_title` , and `id` to compare all the rows. It turns out that majority of the rows that were listed as duplicates had the same information along with the same movie `id`.

I specifically printed the whole row with the `.iloc[]` to show me all the information and it turned out I was right because it had the same information.

So I ended up dropping the rows and only keeping the first copy so there wouldn't be any duplicates with `.drop_duplicates()`.

After I dropped the duplicate rows I checked with `df.duplicated().sum()` and it printed out 0. Therefore, there wasn't any rows that were duplicates anymore.

## Result 

The processed file has **45,436 rows** and six columns: `id`, `title`, `original_title`, `overview`, `status` , `original_language`. It dropped about 30 ish duplicate rows. Each movies is only represented once using its movie `id`

The cleaned dataset was saved as:

`data/processed/movies_metadata.csv`