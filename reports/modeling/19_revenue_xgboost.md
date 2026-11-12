# XGBoost Revenue Prediction Report

## 1. Project Objective

This notebook builds XGBoost regression models to predict a movie's `log_revenue`. The workflow compares several model configurations, evaluates them with validation data, selects the best configuration, and then measures its performance on an unseen test set.

The notebook also includes two additional analyses:

- A genre-only model that uses movie genre indicators as predictors.
- A cleaned-data model that removes rows where `log_revenue` is missing or equal to zero.

## 2. Libraries and Data

The notebook imports pandas and NumPy for data processing, Matplotlib and Seaborn for visualization, XGBoost for modeling, and scikit-learn for splitting the data and calculating evaluation metrics.

The first dataset is loaded from `09_popularity_features.csv`. The target variable is `log_revenue`. The notebook finds 38,032 missing values in this target column.

## 3. Feature Preparation

The following columns are removed because they are identifiers, text fields, categorical list fields, or other fields not used in this numerical XGBoost model:

`id`, `log_revenue`, `revenue_reported`, `target_log_popularity`, `genres`, `production_companies`, `production_countries`, `spoken_languages`, `director`, `status`, `title`, `original_title`, `overview`, `top_cast`, `keywords`, and `original_language`.

After these columns are removed, the remaining columns are assigned to `x`, while `log_revenue` is assigned to `y`. A check confirms that no object or string columns remain. Missing feature values and missing target values are filled with zero for the first modeling approach.

The resulting feature matrix contains 18 numerical features.

## 4. Training, Validation, and Test Splits

The data is divided into three groups using `train_test_split` with `random_state=67`:

| Dataset | Rows | Purpose |
|---|---:|---|
| Training | 36,344 | Fit the XGBoost models |
| Validation | 4,543 | Compare hyperparameter configurations |
| Test | 4,543 | Final evaluation of the selected model |

The split is approximately 80% training, 10% validation, and 10% testing. The test set is not used while selecting the model configuration.

## 5. XGBoost Configuration

The initial model uses the `XGBRegressor` class with:

- `objective="reg:squarederror"`
- `random_state=67`
- `n_estimators=100`
- `max_depth=3`
- `learning_rate=0.1`

The main hyperparameter comparison tests 35 configurations. The tested ranges are:

| Hyperparameter | Values tested |
|---|---|
| `n_estimators` | 100, 200, 300, 400, 500 |
| `max_depth` | 2, 3, 4, 5, 6, 7, 8 |
| `learning_rate` | 0.01, 0.02, 0.03, 0.04, 0.05 |

Each configuration is trained on the training set and evaluated on the validation set using MAE, RMSE, and R².

## 6. Full-Dataset Model Results

For the first experiment, the best configuration is selected by the lowest validation RMSE. The lowest RMSE occurs for model 18:

| Setting | Value |
|---|---:|
| `n_estimators` | 300 |
| `max_depth` | 5 |
| `learning_rate` | 0.03 |
| Validation MAE | 2.027446 |
| Validation RMSE | 3.867541 |
| Validation R² | 0.603582 |

The selected model is then evaluated once on the test set:

| Test metric | Result |
|---|---:|
| MAE | 2.086006 |
| RMSE | 3.994792 |
| R² | 0.550910 |

The notebook visualizes validation MAE and RMSE, validation R², predicted versus actual revenue, and the residuals. It also creates a feature-importance plot. The notebook identifies `log_budget` as the most important feature in the full-dataset model.

An inference example uses the first row of the test set and predicts a `log_revenue` of approximately `0.08295529`.

## 7. Genre-Only Model

The notebook next loads `10_genre_features.csv` and uses 19 binary genre columns as predictors:

`Drama`, `Crime`, `Comedy`, `Action`, `Thriller`, `Adventure`, `Science Fiction`, `Animation`, `Family`, `Romance`, `Mystery`, `Music`, `Horror`, `Fantasy`, `Documentary`, `War`, `Western`, `History`, and `Foreign`.

The genre data is split into training, validation, and test sets. A preliminary genre model uses 200 trees, a maximum depth of 3, and a learning rate of 0.10. Its validation performance is:

| Metric | Result |
|---|---:|
| MAE | 3.942552 |
| RMSE | 5.650168 |
| R² | 0.080655 |

The notebook then tests the same 35 hyperparameter configurations. The best genre configuration, based on the lowest validation RMSE, is model 14:

- `n_estimators=400`
- `max_depth=4`
- `learning_rate=0.04`
- Validation MAE: `3.925638`
- Validation RMSE: `5.645629`
- Validation R²: `0.082131`

The genre feature-importance results identify Adventure as the most important genre feature, followed by Thriller, Foreign, Action, and Comedy. The genre revenue visualization also shows that Adventure has the highest average log revenue in this dataset, while Foreign has the lowest. These are associations in the data and do not mean that genre alone determines a movie's revenue.

## 8. Cleaned-Data Model

Because many rows have missing or zero revenue values, the notebook creates `clean_df` by keeping only rows where `log_revenue` is present and not equal to zero. This leaves 7,398 rows.

The cleaned data is split as follows:

| Dataset | Rows |
|---|---:|
| Training | 5,918 |
| Validation | 740 |
| Test | 740 |

The same 35 hyperparameter configurations are trained and compared again. The best cleaned-data model is model 12:

| Setting | Value |
|---|---:|
| `n_estimators` | 200 |
| `max_depth` | 4 |
| `learning_rate` | 0.02 |
| Validation MAE | 1.398446 |
| Validation RMSE | 2.149314 |
| Validation R² | 0.500575 |

The selected model is evaluated on the cleaned test set:

| Test metric | Result |
|---|---:|
| MAE | 1.415268 |
| RMSE | 2.080804 |
| R² | 0.520761 |

The notebook creates three final visualizations for this model:

1. Validation MAE and RMSE across all configurations.
2. Validation R² across all configurations.
3. Predicted versus actual log revenue, including a diagonal line representing perfect predictions.

It also creates a residual plot. Residuals are calculated as actual log revenue minus predicted log revenue. Values above zero indicate underprediction, while values below zero indicate overprediction.

## 9. Final Conclusion

The cleaned-data model is the final selected model in the notebook because it avoids treating missing or zero revenue values as ordinary observations and produces the best final error values among the completed experiments. Its test results are an MAE of `1.415268`, an RMSE of `2.080804`, and an R² of `0.520761`.

The full-dataset model achieved a higher test R² of `0.550910`, but it was trained after replacing missing target values with zero. Therefore, the two experiments measure performance on different target populations and should not be compared as if they were evaluated on identical data. The cleaned-data results provide the more focused assessment for movies with recorded, nonzero revenue.

Overall, the models show that numerical movie attributes, especially budget-related information, provide more predictive value than genre indicators alone. The genre-only model has a much lower R², demonstrating that genre by itself is not sufficient to accurately predict movie revenue.
