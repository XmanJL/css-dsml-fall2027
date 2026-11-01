# Text Features and Popularity Report

## Objective

Examine how title differences and text-derived features relate to movie popularity using a box plot and a Pearson correlation heatmap.

## Data

Source: `data/features/09_popularity_features.csv`  
Shape: 45,430 movies and 34 features. The `title_differs_from_original` flag is 0 when a movie's title matches its original title and 1 when it differs.

## Log Popularity by Title Difference

| Title comparison | Movies | Median log popularity | Mean log popularity |
|---|---:|---:|---:|
| Matches original title | 34,124 | 0.805 | 1.042 |
| Differs from original title | 11,306 | 0.650 | 0.786 |

Movies with a title that differs from the original have lower median and mean log popularity in this dataset. The box plot also shows substantial spread and high-popularity outliers in both groups. This is an unadjusted group comparison; it does not show that title differences cause lower popularity.

## Text Feature Correlations

| Feature | Correlation with `target_log_popularity` |
|---|---:|
| `keyword_count` | 0.406 |
| `title_word_count` | -0.081 |
| `overview_word_count` | -0.060 |

Keyword count has the strongest relationship with log popularity among the three text features, with a moderate positive correlation. Title and overview word counts have weak negative correlations. The heatmap also shows that the three text features are only weakly correlated with one another.

## Limitations

These are descriptive results. Correlation measures pairwise linear association and does not account for confounding factors or imply causation. The box plot compares groups without controlling for other movie characteristics.
