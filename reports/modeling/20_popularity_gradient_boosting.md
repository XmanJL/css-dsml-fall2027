# Issue #20: Movie Popularity Prediction with Gradient Boosting

## Goal
Build a Gradient Boosting regression model to predict movie popularity using the feature-engineered popularity dataset.  
Use `data\features\09_popularity_features.csv` as the base dataset.  
The target is `target_log_popularity`.  
Model: Gradient Boosting Regressor.  

## Data
Our dataset was `data\features\09_popularity_features.csv`.  
- 45,430 rows x 34 columns.  
- Columns: `id`, `target_log_popularity`, `release_year`, `release_month`, `release_decade`, `log_budget`, `log_revenue`, `runtime`, `genre_count`, `has_collection`, `company_count`, `country_count`, `spoken_language_count`, `cast_size`, `crew_size`, `has_director`, `keyword_count`, `title_word_count`, `overview_word_count`, `title_differs_from_original`, `genres`, `production_companies`, `production_countries`, `spoken_languages`, `director`, `top_cast`, `keywords`, `title`, `original_title`, `overview`, `original_language`, `status`, `budget_reported`, `revenue_reported`

## Key Decisions
### Features Used 
Our model used numeric movie metadata and two compact categorical variables. Features like identifiers, raw text/list fields, and revenue fields were excluded because they were irrelevant or could introduce target leaks.  
- The 20 features included were: `release_year`, `release_month`, `release_decade`, `log_budget`, `runtime`, `genre_count`, `has_collection`, `company_count`, `country_count`, `spoken_language_count`, `cast_size`, `crew_size`, `has_director`, `keyword_count`, `title_word_count`, `overview_word_count`, `title_differs_from_original`, `original_language`, `status, budget_reported`

### Model Configuration and Hyperparameters Tested
Eight Gradient Boosting Regressor configurations were tested:  
| Model | n_estimators | learning_rate | max_depth |
|----------|----------|----------|----------|
| M1 | 100 | 0.05 | 2 |
| M2 | 200 | 0.05 | 2 |
| M3 | 100 | 0.10 | 2 |
| M4 | 200 | 0.10 | 2 |
| M5 | 100 | 0.05 | 3 |
| M6 | 200 | 0.05 | 3 |
| M7 | 100 | 0.10 | 3 |
| M8 | 200 | 0.10 | 3 |

## Results

### Validation Results
Validation performance was compared using MAE, RMSE, and R².  
Lower MAE and RMSE are better; higher R² is better.    
| n_estimators | learning_rate | max_depth | MAE | RMSE | R² |
|----------|----------|----------|----------|----------|----------|
| 100 | 0.05 | 2 | 0.420900 | 0.537301 | 0.545987 |
| 200 | 0.05 | 2 | 0.404541 | 0.522061 | 0.571377 |
| 100 | 0.10 | 2 | 0.404120 | 0.522254 | 0.571060 |
| 200 | 0.10 | 2 | 0.397683 | 0.515366 | 0.582301 |
| 100 | 0.05 | 3 | 0.405573 | 0.523286 | 0.569365 |
| 200 | 0.05 | 3 | 0.394614 | 0.512611 | 0.586755 |
| 100 | 0.10 | 3 | 0.394878 | 0.512889 | 0.586307 |
| 200 | 0.10 | 3 | 0.390236 | 0.507570 | 0.594842 | 

### Optimal Hyperparameter Configuration
M8 was selected using only validation results: 200 estimators, learning rate 0.10, and maximum depth 3. It achieved the lowest validation RMSE (0.5076), the lowest validation MAE (0.3902), and the highest validation R² (0.5948).  

### Final Test Results
After selecting M8, the model was retrained using the combined training and validation data and evaluated once on the untouched test set.  

| Metric | Result |
|----------|----------|
| MAE | 0.3856 |
| RMSE | 0.5015 |
| R² | 0.6136 |

The test R² of 0.6136 indicates that the model explains approximately 61% of the variation in log popularity. Because the target is logarithmic, MAE and RMSE are measured in log-popularity units.  

### Inference Example  
The script selected the first movie sample from the held-out test set and passed its selected feature vector through the fitted preprocessing pipeline.  
- Input: First sample from X_test with the 20 selected features  
- Output: Predicted target_log_popularity = 0.6103  