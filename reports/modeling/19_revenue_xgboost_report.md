# Movie Revenue Prediction with XGBoost

## Overview

This notebook develops an XGBoost regression model to predict movie revenue. The prediction target is `log_revenue`. Multiple XGBoost configurations were trained and compared using a validation set. The best configuration was then evaluated on a separate test set. Additional analysis examined feature importance and the relationship between movie genres and revenue.

## Data Preparation

The dataset was loaded from `09_popularity_features.csv`. The original dataset contained movie identifiers, numerical features, categorical/text fields, engineered features, and revenue-related columns.

The following columns were excluded from the main model:

- `id`, because it is only an identifier.
- `log_revenue`, because it is the target being predicted.
- `revenue_reported`, because it directly reveals the target and would create target leakage.
- `target_log_popularity`, because it was created for a different prediction task.
- Raw text and categorical columns such as titles, descriptions, directors, cast, keywords, and production information.

The remaining 18 features were numerical or binary features, including release information, budget, runtime, movie counts, cast and crew counts, and indicators such as `has_collection` and `has_director`.

Missing feature values were replaced with zero. This was appropriate for some count and indicator features, but zero imputation should be interpreted carefully for variables such as budget or runtime. The notebook also replaced missing `log_revenue` values with zero. Because `log_revenue` is the target, a stronger approach would be to remove rows with missing target values rather than treating missing revenue as a true zero target.

## Train, Validation, and Test Split

The data was split into three subsets:

| Set | Rows | Purpose |
|---|---:|---|
| Training | 36,344 | Train the XGBoost models |
| Validation | 4,543 | Compare hyperparameter configurations |
| Test | 4,543 | Evaluate the selected model once |

The split used `random_state=67` so the same rows could be reproduced in later runs.

## Model Training

Thirty-five XGBoost regression configurations were tested. The hyperparameters varied across:

- `n_estimators`: 100 to 500 trees
- `max_depth`: 2 to 8
- `learning_rate`: 0.01 to 0.05

Each model was trained only on the training set. Predictions were made on the validation set, and each configuration was evaluated using:

- Mean Absolute Error (MAE)
- Root Mean Squared Error (RMSE)
- R-squared (R²)

Lower MAE and RMSE indicate smaller prediction errors. Higher R² indicates that the model explains more variation in the target.

## Validation Results

The best configuration was selected using the lowest validation RMSE:

| Hyperparameter | Selected value |
|---|---:|
| `n_estimators` | 300 |
| `max_depth` | 5 |
| `learning_rate` | 0.03 |

The selected model had the following validation results:

| Metric | Validation result |
|---|---:|
| MAE | 2.0274 |
| RMSE | 3.8675 |
| R² | 0.6036 |

The notebook includes two visualizations comparing the tested configurations. The first compares validation MAE and RMSE, where lower values are better. The second compares validation R², where higher values are better.

## Final Test Evaluation

After selecting the configuration using only the validation results, the selected model was evaluated on the test set:

| Metric | Test result |
|---|---:|
| MAE | 2.0860 |
| RMSE | 3.9948 |
| R² | 0.5509 |

The test results are slightly worse than the validation results, which is normal because the test set contains unseen data. The test R² of approximately 0.55 indicates that the final model explained about 55% of the variation in `log_revenue` for the test movies.

## Predicted-versus-Actual Visualization

The notebook includes a predicted-versus-actual scatter plot for the selected model on the test set. The red dashed line represents perfect predictions. Points closer to this line indicate more accurate predictions, while points farther away represent larger errors.

Because the model predicts `log_revenue`, the axes should ideally be labeled `Actual Log Revenue` and `Predicted Log Revenue` unless the predictions are converted back to the original revenue scale using the inverse of the logarithm used during feature engineering.

## Inference Example

The notebook selects one movie sample from the test set and passes it to the selected model:

```python
movie_sample = x_test.iloc[[0]]
predicted_log_revenue = best_model.predict(movie_sample)[0]
```

The example produced a predicted log revenue of approximately `0.0830`. This demonstrates how the trained model can accept one movie's features and produce a prediction.

## Optional Residual and Feature-Importance Analysis

The residual plot displays the difference between the actual and predicted log revenue values. Residuals were calculated as:

```python
residuals = y_test - y_test_pred
```

The notebook also displays the ten most important features according to the selected XGBoost model. These values describe how useful each feature was for the model's tree decisions; they should not be interpreted as proof that a feature causes revenue to increase or decrease.

## Genre Analysis

A separate genre-only XGBoost model was trained using 20 binary genre features. Each genre was represented by a column containing `0` or `1`, indicating whether a movie belonged to that genre.

Thirty-five genre-model configurations were compared using validation results. The best genre-only configuration was:

| Hyperparameter | Selected value |
|---|---:|
| `n_estimators` | 300 |
| `max_depth` | 4 |
| `learning_rate` | 0.03 |

Its validation results were:

| Metric | Genre-model validation result |
|---|---:|
| MAE | 3.9386 |
| RMSE | 5.6362 |
| R² | 0.0852 |

The genre-only model performed substantially worse than the full-feature model. This suggests that genres alone do not provide enough information to predict movie revenue accurately. Budget, popularity-related information, release information, runtime, and other engineered features add important predictive information.

The genre feature-importance results ranked the genres as follows at the top:

1. Adventure
2. Thriller
3. Foreign
4. Romance
5. Action

The genre revenue visualization compared the average `log_revenue` for movies associated with each genre. Adventure had the highest average log revenue in this dataset, while TV Movie, Foreign, and Documentary had much lower averages. These are associations in the dataset, not causal effects. Also, a movie with multiple genres contributes to the average for each of its genres.

## Main Findings

The full-feature XGBoost model performed better than the genre-only model. The selected full-feature model achieved a test RMSE of approximately 3.99 and a test R² of approximately 0.55 on the log-revenue scale. The genre-only model achieved a validation R² of only approximately 0.085, showing that genre information by itself explains relatively little of the variation in movie revenue.

Overall, revenue prediction benefits from combining multiple types of information rather than relying on genre alone. The most important limitation in the current workflow is the treatment of missing target values. Future versions should remove rows with missing `log_revenue` before splitting instead of filling the target with zero.
