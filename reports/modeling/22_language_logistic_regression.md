# Original Language Classification

## Objective and Data

A multiclass Logistic Regression model predicts `target_original_language` using `data/features/11_language_features.csv`. The analysis is limited to the three most frequent classes: English (`en`, 32,251 movies), French (`fr`, 2,436), and Italian (`it`, 1,529), for 36,216 movies total.

The target is label-encoded. A stratified 70/15/15 split gives 25,351 training, 5,432 validation, and 5,433 test movies. Preprocessing is fitted on training data only. The validation set selects the model; the test set is held out for final evaluation.

## Features and Model

- **Numeric (median imputation, standard scaling):** `release_year`, `release_month`, `release_decade`, `log_budget`, `log_revenue`, `runtime`, `log_popularity`, `genre_count`, `has_collection`, `company_count`, `country_count`, `cast_size`, `crew_size`, `has_director`, `keyword_count`, `title_word_count`, `overview_word_count`, `title_differs_from_original`, `has_tagline`, `budget_known`, `revenue_known`.
- **Categorical:** `status`, imputed with the most frequent value and one-hot encoded.
- **Text:** `original_title` character n-grams, and `genres` and `production_countries` word n-grams, TF-IDF encoded.

The identifier `id` and target are excluded. High-cardinality fields not used in the model include `title`, `overview`, `director`, cast, companies, and keywords. The feature file has no separate raw `original_language` column.

Eight configurations compare `C`, solver, and class weighting: `lbfgs` at `C` 0.1, 1, and 10 with both no class weight and `balanced`; and `saga` at `C=1` with both class-weight options. All use `max_iter=1000`, `tol=0.001`, and random seed 42. Selection uses validation macro F1, with accuracy as a tie-breaker.

## Validation Results

| Configuration | Accuracy | Macro precision | Macro recall | Macro F1 |
|---|---:|---:|---:|---:|
| `C=0.1`, `lbfgs`, none | 0.9531 | 0.8577 | 0.7832 | 0.8169 |
| `C=1`, `lbfgs`, none | 0.9577 | 0.8720 | 0.8178 | 0.8431 |
| **`C=10`, `lbfgs`, none (selected)** | **0.9582** | **0.8754** | **0.8298** | **0.8514** |
| `C=0.1`, `lbfgs`, balanced | 0.9352 | 0.7476 | 0.8984 | 0.8075 |
| `C=1`, `lbfgs`, balanced | 0.9473 | 0.7936 | 0.8980 | 0.8394 |
| `C=10`, `lbfgs`, balanced | 0.9464 | 0.7953 | 0.8905 | 0.8376 |
| `C=1`, `saga`, none | 0.9558 | 0.8642 | 0.8059 | 0.8330 |
| `C=1`, `saga`, balanced | 0.9451 | 0.7835 | 0.8997 | 0.8334 |

The balanced models trade precision for stronger macro recall, but the unweighted `lbfgs` model at `C=10` has the best validation macro F1.

## Final Test Results

| Accuracy | Macro precision | Macro recall | Macro F1 |
|---:|---:|---:|---:|
| 0.9573 | 0.8686 | 0.8408 | 0.8542 |

| Language | Precision | Recall | F1 | Test movies |
|---|---:|---:|---:|---:|
| `en` | 0.9733 | 0.9808 | 0.9770 | 4,838 |
| `fr` | 0.8209 | 0.7514 | 0.7846 | 366 |
| `it` | 0.8117 | 0.7904 | 0.8009 | 229 |

English is predicted most reliably. French is the most difficult class, followed by Italian; both have much smaller support than English, and some French and Italian movies are misclassified as English. The confusion matrix and validation comparison are in the notebook.

## Inference Example

Input: `original_title="Mother Wore Tights"`, `production_countries="United States of America"`, `genres="Music"`, runtime 107 minutes.  
Actual language: `en`; predicted language: **`en`**.
