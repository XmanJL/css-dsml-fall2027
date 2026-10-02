# 05 – Text Cleaning

**Notebook:** `preprocessing/notebooks/05_text_cleaning.ipynb`
**Output:** `data/processed/movie_text.csv` (45,433 rows × 6 columns)
**Presentation time:** ≤ 5 minutes

---

## 1. Goal and scope

Clean the text fields of `movies_metadata.csv` so they can be joined to the other tables by `id` and used for NLP/feature work.

- Columns kept: `id`, `title`, `original_title`, `overview`, `original_language`, `status`
- `tagline` is dropped entirely. About 55% of it is blank, so it is excluded from all processing and from the output.
- Every column is read as a string (`dtype=str`, `keep_default_na=False`) so pandas can't silently change values.

## 2. Inspect the raw data

- Raw file: 45,466 rows × 24 columns.

## 3. Cleaning decisions

### 3.1 Invalid IDs
- Convert `id` to a number and print the rows that fail.
- Drop 3 rows with a non-numeric `id`. In those rows the columns are shifted and `id` holds a date, so there is nothing to recover.

### 3.2 Duplicate ids
- Found 30 extra rows that repeat an `id`. Their text is identical.
- Keep the copy with the most non-empty text columns. Ties go to the first row.
- Result: `id` is unique, 45,433 rows.

### 3.3 Whitespace
- Strip leading and trailing spaces on every text column.
- Remove zero-width and byte-order-mark characters, and collapse runs of whitespace or tabs to one space.
- Values changed: `overview` 1,439, `original_title` 79, `title` 22.

### 3.4 Placeholders become missing (NaN)
- Replace empty strings and generic tokens (`n/a`, `null`, `-`, `?`, `tbd`) with NaN in all columns.
- Replace description-style placeholders only in `overview` (`No overview found.`, `No overview`, and similar). This keeps a real film titled "Unknown" safe.
- Checked the most common values in each text column for hidden placeholders and found none beyond the list above.
- NaN counts: `overview` 1,104, `status` 84, `original_language` 11, `title` 3.

### 3.5 Language and status
- Lowercase `original_language`. All values are valid 2-letter codes.
- Check `status` against TMDB's six values (Released, Post Production, In Production, Planned, Rumored, Canceled). No unexpected values.

### 3.6 `title` vs `original_title`
- Keep both columns. They match in 74.9% of rows, and the differences are real:
  - 73% of non-English films have a different `title` (translations).
  - Only 5% of English films differ.
- Keep rows that share a title but have different ids. These are remakes and re-releases, for example 11 films titled "Cinderella".
  - 0 of them share both title and release date.
  - Only 20 rows share both title and overview. The matching pairs are different films, such as the 1971 and 1976 versions of *12 Chairs*.

## 4. Validation

- Assertions confirm `id` is unique, with no messy whitespace, blank strings or placeholders left.
- Before/after audit table of missing, blank and messy counts per column.

## 5. Result

| Step | Rows |
|---|---|
| Raw | 45,466 |
| After dropping bad ids | 45,463 |
| After removing duplicate ids | **45,433** |

Final missing values: `overview` 1,104, `status` 84, `original_language` 11, `title` 3, `original_title` 0.

## 6. Handoff notes

- Missing text is stored as NaN. Downstream steps should not treat NaN as an empty string.
- `release_date` was used only to investigate duplicates and is not in the output.
