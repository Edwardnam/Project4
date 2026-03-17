# Data Cleaning and Exploratory Data Analysis

The Recipes dataset and Interactions dataset were merged using the recipe ID to combine recipe features with user ratings.

Several exploratory visualizations were created to understand the structure of the dataset and the relationships between recipe characteristics and ratings.

---

## Univariate Analysis

### Distribution of Recipe Preparation Time

<iframe src="assets/prep_time_distribution.html" width="800" height="600" frameborder="0"></iframe>

Recipe preparation times are heavily right-skewed, meaning that most recipes require relatively little time while a smaller number take much longer to prepare. Most recipes fall below approximately 100 minutes.

---

### Distribution of Recipe Ratings

<iframe src="assets/rating_distribution.html" width="800" height="600" frameborder="0"></iframe>

Average ratings are highly concentrated between 4 and 5, indicating that most recipes on the platform receive favorable reviews from users.

---

## Bivariate Analysis

### Ingredients vs Average Rating

<iframe src="assets/ingredients_vs_rating.html" width="800" height="600" frameborder="0"></iframe>

This scatterplot shows the relationship between the number of ingredients and the average recipe rating. The relationship appears weak, suggesting that recipes with more ingredients do not necessarily receive higher ratings.

---

## Key Observations

- Recipe preparation times are heavily right-skewed.
- Most recipes receive ratings between 4 and 5.
- There is no strong relationship between the number of ingredients and average ratings.

These observations suggest that simple structural recipe features alone may not strongly determine how users rate recipes.
