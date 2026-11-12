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