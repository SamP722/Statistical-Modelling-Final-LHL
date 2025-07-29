# Final-Project-Statistical-Modelling-with-Python

## Project Goals

This project explores whether the availability of free bikes at a station can be predicted using nearby points of interest (POIs) from **Foursquare** and **Yelp**. The goal is to assess whether real-time bike availability correlates with venue density, ratings, or other location features — and to build a simple regression model from these results.

---

## Process

### 1. Data Collection

- Queried the **CityBikes API** for real-time bike station data in Montreal (1,004 stations)
- Collected venue data from **Foursquare** and **Yelp** APIs for each station using latitude and longitude
- For each POI dataset, gathered features such as:
  - Venue count within 1 km
  - Average distance from station
  - Average rating and review count (Yelp only)
  - Maximized Foursquare API usage by querying all 1,004 stations (one call per station) to build the most complete dataset possible within rate limits.


### 2. Data Processing

- Normalized nested JSON results using `pandas.json_normalize`
- Aggregated venue data per station
- Joined CityBikes, Yelp, and Foursquare datasets into a unified DataFrame
- Saved all cleaned datasets and the joined data to CSV and an **SQLite database**

### 3. Exploratory Data Analysis

- Visualized relationships between POI features and bike availability
- Created scatterplots and correlation matrices to identify trends
- Found that **Yelp review volume** showed a mild correlation with free bike counts

### 4. Modeling

- Built an **Ordinary Least Squares (OLS) regression** with:
  - `venue_count_fsq`, `avg_distance`, `venue_count_yelp`, `avg_rating`, `avg_reviews`
- Target variable: `free_bikes`
- Evaluated model fit and statistical significance of predictors

---

## Results

The regression model achieved an R² value of 0.145, meaning that about 14.5% of the variation in free bike availability was explained by the POI-based features. Among the predictors, `avg_reviews` — the average number of Yelp reviews for nearby venues — was the only statistically significant variable. This suggests that stations located near more frequently reviewed businesses tend to have slightly more bikes available.

Other variables, including `venue_count_fsq`, `avg_rating`, and `avg_distance`, were not statistically significant. Yelp provided richer metadata than Foursquare (which lacked review information in the free API tier), so review-based insights were only drawn from Yelp data. The model was also limited by the Yelp API rate cap, which allowed querying only 100 stations.

The real-time nature of the CityBikes API added variability to the dataset. By the time data was collected across all stations, time zone differences and late-night collection windows meant that most stations showed lower bike demand, possibly affecting the outcome.

---

## Challenges

One of the most significant challenges was navigating the Yelp API’s rate limit, which restricted queries to 300 per day and effectively capped full dataset coverage to only 100 stations. This resulted in missing data for many stations and affected the consistency of the analysis.

Inconsistent POI data availability between Yelp and Foursquare meant that not all stations had comparable metadata. Multicollinearity was also observed between certain features, particularly between `venue_count_yelp` and `avg_rating`, which may have influenced the reliability of regression outputs.

Lastly, working with live, real-time bike station data introduced additional complexity. Since data collection spanned several hours and was impacted by time zone differences, the availability of bikes at the time of each query may not reflect typical usage patterns.

---

## Future Goals

With more time and expanded access to API resources, this project could be extended to include a larger, more representative sample of stations. This would help improve model reliability and reduce sparsity in Yelp coverage.

Another logical next step would be to reframe the problem as a classification task — for example, predicting whether a given station will have low bike availability (e.g., fewer than 3 bikes). This may produce more actionable insights for system operators.

Incorporating temporal features like time of day, day of week, and weather conditions would likely improve model performance and help uncover more accurate patterns in station usage.
