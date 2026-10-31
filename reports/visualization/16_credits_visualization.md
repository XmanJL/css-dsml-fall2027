# Movie Feature Visualization Report 

### Objective:

Explore how cast and crew characteristics relate to movie popularity 

### Data set imported
Source: `data\features\09_popularity_features.csv`  
Rows Columns (45430, 34) 

`target_log_popularity`, `cast_size` and `crew_size` has a data type of float64 

### Scatter plot findings

| View | `cast_size` vs. `target_log_popularity` | `crew_size` vs. `target_log_popularity` |
|---|---|---|
| Full range | Most movies sit in a cluster at cast sizes of roughly 0 to 30, with `target_log_popularity` between 0 and 3. Beyond a cast of about 50 the points start to thin out, up to a maximum of 313. | Most movies sit in a dense block at crew sizes of roughly 0 to 40, with popularity between 0 and 3. The tail runs out to a maximum of 435, with a few movies scattered in between. The movies with the largest crews mostly sit between 1.5 and 4. |
| Zoomed (99th percentile) | Vertical stripes are due to cast size being a whole number. The upper edge rises slightly as cast size grows. Movies with tiny casts don't go much above about 2.5, but movies with larger casts reach 3 or more. | Vertical stripes appear because `crew_size` is a whole number. Below around 30, movies span the whole popularity range, from 0 to about 3. Past about 40, the bottom of the cloud thins out and the points concentrate around 2 to 3.5. Popularity is widely spread at every crew size. |
| Overall finding | There does not appear to be a strong relationship between cast size and `target_log_popularity`. Cast size alone does not seem to predict popularity, because the cluster at low cast sizes covers nearly the whole popularity range. | 


### Limitations
 
Outliers: `cast_size` with a max of 313 `crew_size` with a max of 435 indicate there are outliers 

Inspecting the outliers:
| Column | 95% | 99% | 99.9% | Max |
|---|---|---|---|---|
| `cast_size` | 32 | 60 | 117.6 | 313 |
| `crew_size` | 33 | 85 | 155 | 435 |

95% of movies have a cast under 32 and crew under 33 which means the data sits in a small range and only a few movies are stretching the axis 


Decision: I chose not to delete the outlier rows but will limit the axis zooming into the 99th percentile. I created two scatter plots (Full range and Zoomed) to compare  

### Director Box Plot Findings

### Cast and Crew Size Scatter Plot Findings

### Correlation Heatmap Findings

### Conclusion
Larger crews are associated with higher popularity, and the low-populairty movies largely disappear above a crew of about 40. Crew size looks more informative than cast size. A large crew size indicates an overall bigger production. Movies with higher production can indicate a movie may be of a franchise, may be highly anticipated or may be an overall good quality movie. A movie with a smaller crew may indicate budget limitations and might not have the same resources as a bigger production movie. 