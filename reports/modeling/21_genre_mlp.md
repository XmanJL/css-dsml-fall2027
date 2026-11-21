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

## 4. Try more MLP settings

My teammate pointed out that four models were a small comparison. I kept the same data split and tried **32 settings** instead. The code uses `product(...)` to combine four layer layouts, two `alpha` values, two learning rates, and two batch sizes. Three layouts have two hidden layers, and one has three.

I used `MLPClassifier` with ReLU and Adam for every setting. `model.fit(X_train_ready, y_train)` trains each one. I saved the model and its settings so I could compare them later. All 32 reached the 120-iteration limit. That means the optimizer had not reported convergence yet, so I would not say the search is fully settled.

## 5. Compare on validation movies

I used `predict_proba` to get each model's genre probabilities on the 4,299 validation movies. For every model, the code tried cutoffs from 0.20 to 0.50. The line `predictions = (probabilities >= threshold).astype(int)` turns a probability into a yes or no for each genre.

That made **224 model-and-cutoff comparisons**. I calculated precision, recall, micro F1, and macro F1 for each one. I ranked the settings by micro F1 because I chose that as the main score. Precision tells me how many predicted genres were right; recall tells me how many real genres were found. F1 combines them.

## 6. Pick the model and cutoff

The best validation result was `mlp_10`: hidden layers `(128, 64)`, `alpha=0.0001`, learning rate `0.0005`, batch size `256`, and cutoff **0.25**. Its validation micro F1 was **0.469**, with precision **0.431** and recall **0.514**. Its macro F1 was **0.286**. Five validation movies had no predicted genre.

In the code, `validation_results.iloc[0]` picks the highest ranked row. I took both the model name and cutoff from that same row. At the default 0.50 cutoff, this model's validation micro F1 was only 0.368, so choosing the cutoff mattered.

## 7. Check the test movies

I then used the chosen model and 0.25 cutoff on the 4,300 test movies. I did not change the settings after looking at this result.

| Test metric | Score |
| --- | ---: |
| Micro precision | 0.430 |
| Micro recall | 0.507 |
| Micro F1 | **0.465** |
| Macro precision | 0.332 |
| Macro recall | 0.293 |
| Macro F1 | **0.282** |

Six test movies had no predicted genre. Micro F1 counts all genre decisions together. Macro F1 averages the 20 genre scores equally, so the lower macro score shows the results are still uneven by genre.

I also kept two checks from my teammate's draft: Hamming loss was **0.124**, which is the share of wrong yes/no genre decisions, and macro Jaccard was **0.181**, which measures overlap between predicted and recorded genres for each label.

One limit: we had already looked at this same test split in the earlier four-model version. I did not tune this update on the new test result, but it is no longer a completely untouched test set. I would describe these as updated results, not a fresh independent benchmark.

## 8. Compare the leading settings in a chart

I made a bar chart for the top ten models, using each model's best validation cutoff. It shows micro and macro F1 side by side. `mlp_10` was first for micro F1, but several settings were close. This chart is about validation performance, not the final test scores.

## 9. Check each genre

I used `precision_score`, `recall_score`, and `f1_score` with `average=None` to see a separate test score for each genre. I also counted how many test movies actually had each genre and plotted the F1 scores.

Drama was highest at **0.693 F1** (2,029 test movies). Documentary was **0.625** (426), and Animation was **0.586** (187). TV Movie was **0.000** (79), Music was **0.011** (166), and Mystery was **0.036** (243). The smaller groups may be harder for the model, but these scores alone do not prove the reason.

## 10. Try one movie prediction

I picked one test movie with at least one predicted genre. The code uses the same `preprocessor.transform` step, then `best_model.predict_proba`, then the 0.25 cutoff. The example was *Back to Stay*. The model predicted Drama and Comedy, and those were its recorded genres too. The title was just for showing the example; it was not an input. One correct movie does not represent the whole test set.

## 11. Show what the cutoff changed

I plotted the selected model's validation micro and macro F1 at each cutoff. The dashed line marks 0.25. At 0.20, recall was higher but precision was lower, and micro F1 was 0.462. At 0.25, micro F1 was 0.469. At 0.50, micro F1 fell to 0.368. Macro F1 was actually higher at 0.20, so picking micro F1 as the main goal affects which cutoff we choose.

## 12. What I learned

Trying more settings improved the test micro F1 a little, from **0.463** in our first version to **0.465** here. Macro F1 went from **0.270** to **0.282**. That is a small improvement, not a huge jump. The model finds common genres like Drama much better than some rare or difficult genres. All 32 models hit the iteration limit, and comparing 224 settings on one validation set can make the best validation score look a little optimistic. Those are the main limits I would mention when presenting.
