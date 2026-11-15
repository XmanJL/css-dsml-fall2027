# Random Forest Movie Rating Prediction

## Issue Overview

The goal of this issue was to build a Random Forest model that predicted movie raings ('vote_average') using movie metadata and engineered features.

The dataset contained 11, 508 movies. 5 different Random Forest configurations were trained and compared to find the best-performing model.

## Feature Selection and Preprocessing

A total of 18 features were selected to train the model.

**Numerical Features (17):**
- `release_year`
- `release_month`
- `release_decade`
- `log_budget`
- `runtime`
- `log_popularity`
- `genre_count`
- `has_collection`
- `company_count`
- `country_count`
- `spoken_language_count`
- `cast_size`
- `crew_size`
- `keyword_count`
- `title_word_count`
- `overview_word_count`
- `title_differs_from_original`

**Categorical Feature (1):**
- `original_language`

The following features were excluded:
- `id`: Used only to identify movies.
- `vote_count`: Excluded due to potential target leakage.
- `log_revenue`: Excluded because revenue is only known after a movie is released.
- Raw text and high-cardinality columns were excluded because they didn't fit for this model.

**Preprocessing:**
- Missing numerical values were filled using the median.
- Missing categorical values were filled using the most frequent value.
- `original_language` was converted into numerical columns using one-hot encoding.

## Data Splitting

The dataset was divided into 3 sets:

- Training set: 8,055 movies (70%)
- Validation set: 1,726 movies (15%)
- Test set: 1,727 movies (15%)

The training set was used to train models, the validation set was used to compare their performance, and the test set was used for the final evaluation.

## Random Forest Configurations

5 different Random Forest configurations were tested:

- **Configuration 1:** 100 trees, maximum depth of 10, minimum samples split of 2.
- **Configuration 2:** 100 trees, maximum depth of 20, minimum samples split of 2.
- **Configuration 3:** 200 trees, maximum depth of 10, minimum samples split of 2.
- **Configuration 4:** 200 trees, maximum depth of 20, minimum samples split of 5.
- **Configuration 5:** 300 trees, no maximum depth, minimum samples split of 2.

**Validation Results (Using MAE, RMSE, R2):**

- Configuration 1: MAE = 0.5726, RMSE = 0.7512, R2 = 0.3581.
- Configuration 2: MAE = 0.5684, RMSE = 0.7465, R2 = 0.3660.
- Configuration 3: MAE = 0.5724, RMSE = 0.7513, R2 = 0.3579.
- Configuration 4: MAE = 0.5659, RMSE = 0.7442, R2 = 0.3699.
- Configuration 5: MAE = 0.5664, RMSE = 0.7460, R2 = 0.3668.

Configuration 4 performed best across all 3 validation metrics.

**Selected Configuration:**
- `n_estimators`: 200
- `max_depth`: 20
- `min_samples_split`: 5

## Final Test Evaluation

The selected model was evaluated on the test set.

**Final Test Results:** 
- MAE: 0.5648
- RMSE: 0.7234
- R2: 0.3897

The MAE means that the model's predictions were off by approximately 0.56 rating points on average. The R2 indicates that the model explains approximately 39% of the variation in movie ratings.

## One-Movie Prediction Example

One movie from the test set was selected to demonstrate how the model makes a prediction:

**Movie Features:**
- Release year: 2014
- Release month:
- Runtime:
- Genre count:
- Original language: English (`en`)

**Prediction Results:**
- Predicted rating: 5.71
- Actual rating: 5.50
- Difference: 0.21 rating points

As a result, the movie predicted a rating close to the movies actual rating.

## Evaluation Visualizations

3 visualizations were created to evaluate the model:

- **Validation Performance Comparison:** Compared MAE, RMSE, and R2 of all 5 configurations.
- **Predicted vs. Actual Ratings:** Showed how close the predicted ratings were to the actual ratings. 
- **Residual Distribution:** Showed the differences between actual and predicted ratings, helping identify overpredictions and underpredictions.

Most prediction errors were concentrated near 0, although some movies had larger prediction errors.

## Main Findings 

Configuration 4 achieved the best validation results among the 5 configurations tested.

The final model achieved an MAE of 0.5648 and R2 of 0.3897 on the test set. The model was able to learn patterns related to movie ratings but some variation in ratings remains unexplained.

The inference example also showed that the model could produce a prediction close to an actual movie rating with a difference of about 0.21 points. 

## Additional Hyperparameter Tuning

Following the review feedback, more hyperparameter combinations were tested to try to improve the model's performance. The 5 original configurations were kept, and 11 more were added, bringing the total to 16. Each new configuration changed one setting from the 300 tree, no maximum depth, minimum samples split of 2 configuration (except the first three, which changed the number of trees, maximum depth, and minimum samples split). The validation results table in the notebook now also shows `min_samples_leaf`, `max_features`, and `bootstrap` for every configuration.

**Additional Random Forest Configurations:**

- **Configuration 6:** 500 trees, no maximum depth, minimum samples split of 2.
- **Configuration 7:** 300 trees, maximum depth of 30, minimum samples split of 10.
- **Configuration 8:** 300 trees, maximum depth of 50, minimum samples split of 20.
- **Configuration 9:** 300 trees, no maximum depth, minimum samples split of 2, minimum samples per leaf of 2.
- **Configuration 10:** 300 trees, no maximum depth, minimum samples split of 2, minimum samples per leaf of 4.
- **Configuration 11:** 300 trees, no maximum depth, minimum samples split of 2, minimum samples per leaf of 8.
- **Configuration 12:** 300 trees, no maximum depth, minimum samples split of 2, `max_features` of "sqrt".
- **Configuration 13:** 300 trees, no maximum depth, minimum samples split of 2, `max_features` of "log2".
- **Configuration 14:** 300 trees, no maximum depth, minimum samples split of 2, `max_features` of 0.5.
- **Configuration 15:** 300 trees, no maximum depth, minimum samples split of 2, `max_features` of 0.8.
- **Configuration 16:** 300 trees, no maximum depth, minimum samples split of 2, bootstrap sampling turned off.

`class_weight` was not tested because it only applies to classification models and is not available in `RandomForestRegressor`.

**Validation Results for the Additional Configurations (Using MAE, RMSE, R2):**

- Configuration 6: MAE = 0.5664, RMSE = 0.7455, R2 = 0.3677.
- Configuration 7: MAE = 0.5666, RMSE = 0.7460, R2 = 0.3668.
- Configuration 8: MAE = 0.5665, RMSE = 0.7455, R2 = 0.3677.
- Configuration 9: MAE = 0.5636, RMSE = 0.7439, R2 = 0.3704.
- Configuration 10: MAE = 0.5634, RMSE = 0.7440, R2 = 0.3704.
- Configuration 11: MAE = 0.5641, RMSE = 0.7448, R2 = 0.3690.
- Configuration 12: MAE = 0.5688, RMSE = 0.7473, R2 = 0.3647.
- Configuration 13: MAE = 0.5710, RMSE = 0.7495, R2 = 0.3610.
- Configuration 14: MAE = 0.5617, RMSE = 0.7399, R2 = 0.3772.
- Configuration 15: MAE = 0.5655, RMSE = 0.7436, R2 = 0.3710.
- Configuration 16: MAE = 0.7494, RMSE = 0.9908, R2 = -0.1168.

Configuration 14 (`max_features` of 0.5) had the lowest validation MAE and RMSE and the highest validation R2 of all 16 configurations. Turning off bootstrap sampling (Configuration 16) performed much worse than the others, and limiting `max_features` to "sqrt" or "log2" performed slightly worse than using all features. Increasing the number of trees to 500 and using stronger depth and split limits made little difference.

**Updated Selected Configuration:**
- `n_estimators`: 300
- `max_depth`: None
- `min_samples_split`: 2
- `max_features`: 0.5

**Updated Final Test Results:**
- MAE: 0.5660
- RMSE: 0.7233
- R2: 0.3898

The additional combinations improved validation performance only slightly, and the test performance was essentially unchanged (MAE of 0.5642 and R2 of 0.3897 before the additional combinations, compared with MAE of 0.5660 and R2 of 0.3898 after). This suggests that tuning these hyperparameters further does not meaningfully improve the model, and that the remaining unexplained variation in ratings is likely limited by the available features rather than the model settings.

**Updated One-Movie Prediction Example:**
- Predicted rating: 5.72
- Actual rating: 5.50
- Difference: 0.22 rating points

**Updated Visualizations:** The Validation Performance Comparison charts now compare all 16 configurations. The notebook labels them Config 0 to Config 15, so Config 0 is Configuration 1 and Config 15 is Configuration 16.
