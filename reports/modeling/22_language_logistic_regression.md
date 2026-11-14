# 18 â€“ Original Language Prediction with Logistic Regression

**Notebook:** `notebooks/modeling/18_language_logistic_regression.ipynb`
**Input:** `data/features/11_language_features.csv`
**Presentation time:** â‰¤ 5 minutes

---

## 1. Goal and scope

Predict `target_original_language` (29 classes) from movie features using multinomial logistic regression.

## 2. Data and predictors

- **Numbers (15):** release year/month/decade, log budget/revenue/popularity, runtime, genre/company/country/cast/crew/keyword counts, title and overview word counts.
- **Flags (5):** `has_collection`, `has_director`, `has_tagline`, `budget_known`, `revenue_known`.
- **Lists (7, `A|B|C` text):** genres, production companies, production countries, director, top cast, keywords, status.
- **Free text (2):** overview, title.
- **Excluded to avoid leakage:** `original_title` and `title_differs_from_original` (a title in the original script gives the answer away).

## 3. Method

- Stratified three-way split (`random_state=42`): train 27,252 (60%), validation 9,085 (20%), test 9,085 (20%).
- Numbers: median fill + standard scaling. Lists: one 0/1 column per value (values in fewer than 5 movies ignored). Text: TF-IDF, 5,000 words max, English stop words removed.
- Model: multinomial `LogisticRegression`.
- Tuning: grid over `C` {0.1, 1, 10, 30, 100} × `class_weight` {None, balanced} × `solver` {lbfgs, saga} (20 fits). Each is fit on train and scored on validation by macro-F1.
- Final: best combination refit on train + validation, scored once on test.

## 4. Results

Top of the validation grid:

| C | class_weight | solver | Val accuracy | Val macro-F1 |
|---|---|---|---|---|
| **1** | **None** | **saga** | 0.893 | **0.759** |
| 1 | None | lbfgs | 0.893 | 0.756 |
| 10 | None | saga | 0.890 | 0.748 |
| 10 | balanced | lbfgs | 0.883 | 0.745 |

Final test result:

| Model | Accuracy | Macro-F1 |
|---|---|---|
| Majority baseline (`en`) | 0.710 | n/a |
| Logistic regression (`C=1`, no weights, `saga`) | **0.897** | **0.772** |

## 5. Key findings

- Production country, cast names, keywords and title words are the strongest signals (e.g. `Japan` for `ja`, `India` and `bollywood` for `hi`, `le`/`les` for `fr`).
- Best languages: `th`, `en`, `ja`, `fa`, `sr`, `ko` (F1 0.91 to 0.94). Worst: `he`, `ro`, `nl`, `Other`, `ta` (F1 0.55 to 0.64), mostly small classes.
- `C=1` was best. `C=0.1` underfits (macro-F1 about 0.63) and larger values overfit slightly.
- `class_weight="balanced"` never beat unweighted at the same `C`.
- The solver barely changes the score (0.759 vs 0.756 at the best setting). `saga` is about 7 times slower than `lbfgs`.
- Most common error: foreign films predicted as `en`, and the reverse.

## 6. Limitations and next steps

- One split and a coarse grid. The `saga` vs `lbfgs` gap is within noise.
- Cast names are memorised, which will not generalise to new actors.
- Possible next steps: tune `C` between 0.3 and 3, add character n-grams of titles, try other models.
