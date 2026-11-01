# 17 - Text visualization

## 1. Objective
Explore relationship between `target_log_popularity` and `key_word_count`, `title_word_count`, and `overview_word_count` to determine if there is correlation by using scatter plots, correlation heatmaps, and distribution plots.

## 2. Method
In our graphs `key_word_count` vs `target_log_popularity`, `title_word_count` vs `target_log_popularity`, and `overview_word_count` vs `target_log_popularity`, a sample of `10,000` values from the data is used, rather than every value of `45,430` to create a clearer scatter plot. A trendline is placed onto each of these graphs using the sample of data as well. However, using a sample of the data does not make a significant difference when looking at the trendline for results.

## 3. Findings
Through the three graphs, we can see that there is a high positive correlation in the graphs containing `key_word_count` versus ``target_log_popularity`` and a slight positive correlation in the `title_word_count` versus `target_log_popularity` graph. There is also a slight negative correlation in the `overview_word_count` versus the `target_log_popularity` graph. These correlations were determined by referring to the red trendline.


## 4. Limitations
The trendline considers extreme outliers such as a movie with nearly 100 keywords, a moviee with a 12+ word title, and a movie with a 175 word overview. This may have an affect on the trendline, but it is unknown if this is significant.\
Our data is also significantly overplotted, as there are many movies with similar word counts in each column. This makes sense considering titles are generally not that long, a movie only really has a few specific niches/keywords while the rest are usually general keywords nearly every movie would have, and an overview is probably not longer than a paragraph.

## 5. Results
We can see that the most popular movies can be found in the lower word counts for all of the graphs by referring to our overlapped graph.\
As there are more keywords for a movie, they are less common, but also generally more popular. We can infer that as a movie targets more specific niches, most likely with related topics, (ex. marriage, divorce, hardship, love), the movie is more popular because the story is more focused and supported around a central idea. However, for less popular movies with more keywords, it may be due to a movie trying too many different topics and ideas all at once, resulting in a less focused story, and becoming less popular.\
The correlation for `title_word_count` is slight, indicating that the amount of words in the title does not have a significant affect on the overall popularity of a movie. However, we can find some of our most popular movies at a 2 and 3 word title, which may be referring to movies with a title starting with `The`. Examples could include `The Matrix`, `The Godfather`, and `The Lion King`.\
Lastly, the `overview_word_count` is also slight, but is negative in contrast to the other two columns. Continuing the idea that a less focused movie may mean less popular, an overview that is too long may mean the movie is less focused overall, as it tries to mention too many things in what should be a short summary meant to catch a potential watcher's attention. This idea is supported by referring to how there is a drop off in the amount of movies there are past ~75 words compared to under ~75 words, and popularity continues declining. However, an overview likely does not have a large affect on a movie's popularity at all, suppported by the correlation being slight, as the movie itself would determine its popularity, not the overview.