# My notes for Issue #37: Genre Prediction with MLP

These are my notes as I worked through the model, from preparing the data to checking the final results.

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

`two_layers` had the highest validation micro F1 at the default 0.50 cutoff. Its recall was still low, and it predicted no genre for 1,492 validation movies. I used these validation results to pick the model before checking the test set.

## 6. Choose the model and cutoff

I took `validation_results.loc[0, "model"]`, which gave me `two_layers` because it had the highest micro F1 in step 5. Then I used `predict_proba` to get its probability for each genre on validation movies. I tried cutoffs from 0.20 to 0.50 and turned each probability into 1 or 0 with `>= threshold`.

The best validation micro F1 in that list was **0.463 at 0.25**, so I chose 0.25. Its validation precision was 0.435 and recall was 0.495. Lowering the cutoff helped the model find more of the real genres. It also made more wrong predictions, which is why precision went down. This setting left only 3 validation movies with no predicted genre.

## 7. Check the final test results

After choosing the model and cutoff, I used them on the held-out 4,300 test movies. I did not change the settings based on the test scores.

| Test metric | Score |
| --- | ---: |
| Micro precision | 0.437 |
| Micro recall | 0.491 |
| Micro F1 | 0.463 |
| Macro precision | 0.343 |
| Macro recall | 0.276 |
| Macro F1 | 0.270 |

Only 9 test movies had no predicted genre. Micro F1 combines all genre decisions, while macro F1 averages the 20 genre scores equally. The lower macro score tells me the model is uneven across genres.

## 8. Compare the four model settings in a chart

I made a bar chart using the validation micro and macro F1 values from step 5. I used `barh` so all four model names fit. These are the scores at the original 0.50 cutoff, before I adjusted the chosen model's cutoff. The `two_layers` version was highest in micro F1, although the four scores were close.

## 9. See which genres were easier or harder

I used `precision_score`, `recall_score`, and `f1_score` with `average=None` to get a separate test score for each genre. I also counted the actual test movies for each genre and made a horizontal F1 chart.

Drama was highest at **0.693 F1** (2,029 actual test movies). Documentary was **0.603** (426) and Animation was **0.580** (187). TV Movie and Music were both **0.000** F1, so this model did not correctly predict those genres in the test set. Mystery was also low at **0.053**. The number of examples may be part of the problem, but the scores alone do not prove the cause.

## 10. Try one movie prediction

I picked a test movie with at least one predicted genre so the example is easy to read. I passed its input row through the same `preprocessor.transform` step, called `best_model.predict_proba`, and used the 0.25 cutoff. The movie shown was *Back to Stay*. The model predicted Drama and Comedy, and those were its recorded genres too. This is one example only; all 4,300 test movies are included in the test scores.

## 11. Show what the cutoff changed

I plotted validation micro and macro F1 against the cutoffs I tried. The chart marks 0.25 with a dashed line. It helps show why I used that cutoff: its micro F1 was a little higher than at 0.20 and much higher than at 0.50. Macro F1 was actually higher at 0.20, so choosing micro F1 as the main goal was a real tradeoff.

## 12. What I learned from the results

The model can find common genres like Drama fairly well, but its performance changes a lot by genre. The overall test micro F1 was **0.463** and macro F1 was **0.270**. I would explain the gap as a sign that some genres are much harder for this model. I also need to mention that all four MLPs reached the 80-iteration limit and showed convergence warnings, so more training or different settings might change the results.
