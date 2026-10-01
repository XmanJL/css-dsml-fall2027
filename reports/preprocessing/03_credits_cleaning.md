# Issue #3: Credits Data Cleaning

## Goal

Clean `data/raw/credits.csv` and create one usable credits record per movie. The output is saved locally as `data/processed/credits.csv`.

## What We Found

The source file has 45,476 rows and three columns: `cast`, `crew`, and `id`. The cast and crew values were text representations of lists. There were no missing CSV cells, but some lists were empty.

We found 37 exact duplicate rows. After removing them, seven movie IDs still had two different source records. Those pairs contained the same cast names and crew members, although some crew entries appeared in a different order.

## Cleaning Decisions

- Parsed `cast` and `crew` into Python lists.
- Selected the first crew member labeled `Director`.
- Sorted cast members by their `order` field and retained up to five names.
- Counted all cast and crew members, not only those displayed in the top-five list.
- Kept movies with incomplete credits: an empty cast becomes `[]` and size `0`; an empty crew has size `0` and no director.
- Removed exact duplicate rows, then kept the first source record for each repeated movie ID. This gives one repeatable choice per ID. For a movie with multiple credited directors, crew ordering can affect which director is selected.

## Result and Checks

The processed file has **45,432 rows** and these columns: `id`, `director`, `top_five_cast`, `cast_size`, and `crew_size`. Cast names are stored as JSON lists so another program can read them consistently.

We read the saved CSV back and verified that every ID is present and unique, every cast value is a readable JSON list, and the required columns are present. The final file includes 2,414 movies with no cast, 771 with no crew, and 887 with no listed director.

The processed CSV is kept locally because the repository ignores the `data/` folder. The notebook contains the steps needed to reproduce it.
