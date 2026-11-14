# My notes for Issue #37: Genre Prediction with MLP

These are my notes for the first five parts. I still need to add the final model choice, test results, charts, and an example prediction later.

## 1. Pick the inputs and answers

I loaded `10_genre_features.csv` from the shared Week 5 Features folder. It had 45,433 movies. I removed 2,442 rows with no recorded genre, so I used 42,991 movies.

In the code, I made `X` for the information the model gets and `y` for the 20 genre answers. A movie can have more than one genre, so a row in `y` can have several 1s. I used 16 input features: 15 number or true/false columns and `original_language`.

I left out `id` and the genre columns because those should not be inputs. I also left out revenue and popularity since they are known after release. I did not use full text or names yet because they need more work to turn into model inputs.

## 2. Prepare the data

I copied `X` into `X_model` so I could prepare it without changing the original. I treated 34,169 zero budgets as missing. This is an assumption: many zero budgets probably mean the real budget was not recorded.

I set up a pipeline to fill missing numbers with the median and scale them. For `original_language`, I set it up to fill missing values and make one column per language. I only **defined** the pipeline here; fitted it after splitting the data.

## 3. Split the movies

I used `train_test_split` with `random_state=42`, so I can get the same split again:

- Training: 34,392 movies (80%)
- Validation: 4,299 movies (10%)
- Test: 4,300 movies (10%)

I checked that the groups did not overlap. Then I used `fit_transform` on training data and only `transform` on validation and test data. This matters because the pipeline should learn its median, scale, and language categories from training movies only. After that, each movie had 101 model inputs.

## 4. Train four MLPs

I tried four settings to see how the number and size of hidden layers and the learning rate affect the model:

| Name | Hidden layers | Learning rate |
| --- | --- | ---: |
| `one_layer_32` | 32 | 0.0010 |
| `one_layer_64` | 64 | 0.0010 |
| `two_layers` | 64, then 32 | 0.0010 |
| `slower_rate` | 64, then 32 | 0.0003 |

In the code, `model.fit(X_train_ready, y_train)` trains each version. I used the same seed and other settings for all four so the comparison is fair. All four hit the 80-iteration limit and gave a convergence warning. They still ran, but more training might help.

## 5. Compare on validation data

I used `model.predict(X_validation_ready)` for each model, then compared its predictions with `y_validation`. Precision is how often a predicted genre is right. Recall is how many of the real genres it finds. F1 combines both.

| Model | Precision | Recall | Micro F1 | Macro F1 |
| --- | ---: | ---: | ---: | ---: |
| `two_layers` | 0.647 | 0.229 | 0.338 | 0.148 |
| `one_layer_32` | 0.641 | 0.218 | 0.326 | 0.124 |
| `slower_rate` | 0.658 | 0.215 | 0.324 | 0.130 |
| `one_layer_64` | 0.645 | 0.215 | 0.323 | 0.135 |

`two_layers` has the highest validation micro F1 so far. Its recall is still low, and it predicted no genre for 1,492 validation movies. I have not used the test set to pick a model.

## Next parts

Pick a model from validation results, test it once, make the required charts, show one prediction, and explain which genres it does well or poorly on.
