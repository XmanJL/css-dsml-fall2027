# Issue #4: Keywords Preprocessing

**Author:** Aaron (`aaronstrong05`) | **Branch:** `Aaron-4` | **Notebook:** `notebooks/preprocessing/04_keywords_cleaning.ipynb`
**Dataset:** The Movies Dataset, `keywords.csv`

## 1. Goal
`keywords.csv` stores each movie's keywords as a JSON-like *string*. The goal is a clean table with **one row per movie** and a **list of normalised keyword names**, ready for feature engineering.

## 2. Pipeline
`keywords` (string) → `keywords_parsed` (list of dicts) → `keyword_names` (list of str) → `keyword_names_clean` (lowercase, stripped, de-duplicated list)

Rule followed throughout: **never overwrite a source column**. Each step writes a new one, so "before" and "after" can always be compared. The only overwrites are the two row-dropping steps, and each prints how many rows it removed.

## 3. The raw data
- 46,419 rows × 2 columns (`id`, `keywords`), no `NaN` values.
- 987 fully duplicated rows.
- Movies with no keywords are stored as the string `"[]"`, not as a null, which is why the null check reports zero.

## 4. Key decisions

| Decision | Why |
|---|---|
| Parse with `ast.literal_eval`, not `json.loads` | The data uses single quotes, so it is not valid JSON. `literal_eval` parses Python literals safely (it cannot execute code, unlike `eval`). |
| Strip whitespace and lowercase every keyword | 18 keywords were not lowercase and 180 had extra whitespace. 196 movies had a keyword list changed, so variants like `"Jealousy"` and `"jealousy "` now match. |
| De-duplicate with `dict.fromkeys`, not `set` | Keeps the original order and stays a plain list. Sets are unordered and not JSON-serialisable. |
| **Drop** movies with empty keyword lists | They carry no keyword information. They are not lost from the project: `movies_metadata.csv` still has them, and a join on `id` can treat a missing list as empty. |
| Keep the **first** row for each repeated `id` | Verified first that every repeat is an exact copy (see below), so nothing is lost. |

## 5. Validation
Each step is backed by a check in the notebook, not just an assumption.

- **Within-movie repeats:** checked with `len(x) != len(set(x))`. Result: 0 before and 0 after cleaning. (`Series.duplicated()` is the wrong tool here because it compares whole rows.)
- **Movie IDs:** 0 missing, 0 non-positive.
- **Conflicting duplicates:** 532 IDs appeared twice. Counting distinct raw `keywords` strings per repeated ID gave **0 conflicts**, so the repeats were exact copies and keeping the first is safe. An `assert` stops the notebook if this ever changes.
- **Final integrity:** `id` is retained, unique, and no empty keyword lists remain (all asserted).

## 6. Results

| Step | Rows |
|---|---|
| Raw file | 46,419 |
| After dropping empty keyword lists (14,795 removed) | 31,624 |
| After dropping repeated IDs (532 removed) | **31,092** |

Of the 987 duplicated rows in the raw file, 532 remained after the empty-list drop, so the other 455 were empty-keyword rows.

## 7. Checklist for issue #4

| # | Task | Status |
|---|---|---|
| 1 | Inspect dimensions, types, missing values, duplicates | Done |
| 2 | Parse the JSON-like column | Done |
| 3 | Extract keyword names | Done |
| 4 | Consistent format | Done |
| 5 | Duplicate keywords within a movie | Done |
| 6 | Handle empty lists | Done (dropped) |
| 7 | Check missing / invalid / duplicated IDs | Done |
| 8 | Retain movie `id` | Done |
| 9 | One row per movie | Done |
| 10 | Document and justify decisions | This report and the notebook |

## 8. Notes and limitations
- **Coverage trade-off:** about a third of the movies (14,795 of 46,419) have no keywords and are excluded from this table. Anything that needs every movie should join back to `movies_metadata.csv` on `id`.
- **Titles:** `keywords.csv` has only `id`, not titles. Titles live in `movies_metadata.csv`.
- **Data is not committed:** `data/` is gitignored per `CONTRIBUTING.md`. Run the notebook top to bottom to regenerate `data/processed/keywords_clean.json` (columns `id`, `keyword_names_clean`).
