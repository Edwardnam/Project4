# Predicting Recipe Ratings from Recipe Characteristics

## DSC 80 Final Project

This project analyzes the relationship between recipe characteristics and user ratings using the **Recipes and Ratings dataset from Food.com**.

The goal of the project is to investigate whether features such as **preparation time, number of ingredients, and number of steps** can help predict the average rating of a recipe.

--- 

# Introduction

Online recipe platforms allow users to share recipes and evaluate them through ratings and reviews. These ratings reflect overall user satisfaction, which may depend on factors such as recipe difficulty, preparation time, and structural complexity.

This project uses the **Recipes and Ratings dataset from Food.com** to investigate whether recipe characteristics can help explain and predict recipe ratings. By analyzing preparation time, number of ingredients, and number of cooking steps, this project explores whether simple structural features provide useful information about how users evaluate recipes.

The dataset contains 83,782 rows information about recipes such as preparation time, number of ingredients, cooking steps, and user ratings. By combining recipe features with rating data since 2008, the project examines whether structural properties of recipes influence how users rate them.

## Dataset Description

This project uses two datasets from **Food.com**:

- **Recipes dataset**: contains recipe-level features such as preparation time, ingredients, and cooking steps
- **Interactions dataset**: contains user interactions with recipes, including ratings

The datasets were merged using the recipe ID to combine recipe features with rating information.

The cleaned dataset contains recipe-level information with the following variables being most relevant to this analysis:

| Variable | Description |
|----------|-------------|
| `minutes` | Total preparation time required for the recipe |
| `n_ingredients` | Number of ingredients used in the recipe |
| `n_steps` | Number of steps in the recipe instructions |
| `avg_rating` | Average user rating for the recipe |
| `rating` | Individual user rating values prior to aggregation |

## Research Question

Can recipe characteristics such as **preparation time**, **number of ingredients**, and **number of steps** be used to predict the average rating of a recipe?

Understanding these relationships may provide insight into which types of recipes tend to receive higher ratings and whether complexity or preparation time influences user satisfaction.

---

# Data Cleaning and Exploratory Data Analysis

The Recipes dataset and Interactions dataset were merged using the recipe ID to combine recipe features with user ratings.

Before conducting the analysis, the data was cleaned by:

- merging the Recipes and Interactions datasets
- replacing invalid rating values with missing values
- computing the **average rating** for each recipe
- ensuring recipe-level features were in the correct format for analysis

These cleaning steps made it possible to analyze relationships between recipe structure and average user ratings.

Several exploratory visualizations were created to understand the structure of the dataset and the relationships between key variables.

---

## Univariate Analysis

### Distribution of Recipe Preparation Time

<iframe src="assets/prep_time_distribution.html" width="800" height="600" frameborder="0"></iframe>

Recipe preparation times are **heavily right-skewed**, meaning that most recipes require relatively little time while a smaller number take much longer to prepare. Most recipes fall below approximately 100 minutes.

This pattern suggests that while most recipes are relatively accessible in terms of time commitment, there is substantial variation in recipe length, with a small number of recipes requiring extremely long preparation times.

---

### Distribution of Recipe Ratings

<iframe src="assets/rating_distribution.html" width="800" height="600" frameborder="0"></iframe>

Average ratings are **highly concentrated between 4 and 5**, indicating that most recipes on the platform receive favorable reviews from users.

This concentration near the high end of the scale suggests that recipe ratings may be difficult to predict precisely, since many recipes cluster near similar rating values.

---

## Bivariate Analysis

### Ingredients vs Average Rating

<iframe src="assets/ingredients_vs_rating.html" width="800" height="600" frameborder="0"></iframe>

This scatterplot shows the relationship between the **number of ingredients** and the **average recipe rating**.

The relationship appears weak. Recipes with both few and many ingredients tend to receive similar ratings, suggesting that ingredient count alone does not strongly determine how users evaluate recipes.

---

## Key Observations

The exploratory analysis suggests several important patterns:

- Preparation times are **heavily right-skewed**
- Most recipes receive **ratings between 4 and 5**
- Ingredient count has **little visible relationship with average rating**

These observations suggest that while recipe structure may provide some information about ratings, simple structural variables alone may not strongly determine user satisfaction.

---

# Assessment of Missingness

The column **avg_rating** contains missing values because some recipes have not yet received any user ratings.

This missingness likely occurs when users choose not to rate a recipe. Since the probability of missingness may depend on user behavior rather than the rating value itself, this missingness is likely **Missing At Random (MAR)**.

A permutation-based missingness analysis was used to explore whether recipe preparation time is associated with whether a recipe receives a rating.

### Preparation Time Distribution by Rating Missingness

<iframe src="assets/missingness_boxplot.html" width="800" height="600" frameborder="0"></iframe>

This box plot compares recipe preparation times for recipes with observed ratings and recipes with missing ratings.

Recipes with missing ratings tend to have somewhat higher preparation times on average. This suggests that longer or more time-intensive recipes may be less likely to receive ratings, possibly because fewer users attempt them or finish them.

Understanding this missingness mechanism is important because it affects how the dataset should be interpreted and used in modeling.

---

# Hypothesis Testing

A permutation test was conducted to determine whether **preparation time differs between recipes with missing ratings and those with observed ratings**.

## Test Statistic

Difference in mean preparation time between recipes with missing ratings and recipes with observed ratings.

## Null Hypothesis (H₀)

Preparation time is independent of whether a recipe's average rating is missing.

## Alternative Hypothesis (H₁)

Recipes with missing ratings have different preparation times than recipes with observed ratings.

### Permutation Distribution of the Test Statistic

<iframe src="assets/hypothesis_test_distribution.html" width="800" height="600" frameborder="0"></iframe>

This plot shows the permutation distribution of the difference in mean preparation time under the null hypothesis. The red dashed line marks the observed test statistic.

The observed statistic falls in the tail of the permutation distribution, and the resulting **p-value is below 0.05**, suggesting that preparation time may be associated with whether a recipe receives ratings.

This provides evidence that rating missingness is related to recipe characteristics rather than occurring completely at random.

---

# Framing a Prediction Problem

The goal of the prediction task is to **predict the average rating (`avg_rating`) of a recipe** using features available before users rate the recipe.

This is a **regression problem** because the target variable is continuous and ranges from 1 to 5.

## Evaluation Metric

The model is evaluated using **Mean Squared Error (MSE)** and **Root Mean Squared Error (RMSE)**. These metrics measure how close predicted ratings are to actual ratings, with RMSE providing a more interpretable measure in the original scale of the response variable.

## Features Used

- `minutes` – preparation time  
- `n_ingredients` – number of ingredients  
- `n_steps` – number of steps  

Features that would not be available at prediction time, such as user ratings themselves, were excluded from the model.

---

# Baseline Model

The baseline model is a **Linear Regression model** implemented using a Scikit-Learn Pipeline.

The dataset was split into **training and testing sets using an 80/20 split**.

## Features

- `minutes` (quantitative)  
- `n_ingredients` (quantitative)  
- `n_steps` (quantitative)

## Performance

Baseline model results:

**RMSE ≈ 0.636**

This relatively high prediction error suggests that these basic recipe features alone are not strong predictors of recipe ratings.

The baseline model captures some relationship between recipe structure and ratings, but the performance indicates that additional feature engineering or more flexible models may be needed to improve prediction quality.

---

# Final Model

The final model uses a **Random Forest Regressor**, which can capture nonlinear relationships between variables.

## Feature Engineering

Two additional features were created:

### `log_minutes`

The logarithm of preparation time. Preparation times are highly skewed, so this transformation reduces the influence of extreme values and helps the model capture patterns more effectively.

### `steps_per_ingredient`

The ratio of the number of steps to the number of ingredients. This feature captures the **complexity of the recipe instructions**, which may influence how users evaluate recipes.

## Hyperparameter Tuning

Hyperparameters were optimized using **GridSearchCV with 3-fold cross-validation**.

Best parameters:

- `n_estimators = 50`
- `max_depth = 5`

## Model Performance

Training RMSE ≈ 0.640  
Testing RMSE ≈ 0.636

### Actual vs Predicted Ratings

<iframe src="assets/final_model_predictions.html" width="800" height="600" frameborder="0"></iframe>

This scatterplot compares the actual recipe ratings to the ratings predicted by the final model. The red dashed line represents perfect prediction.

The points are spread around the diagonal rather than tightly concentrated on it, which indicates that the final model is only moderately effective at predicting ratings.

Overall, the final model performs **very similarly to the baseline model**, suggesting that the available recipe features still have limited predictive power for predicting average ratings.

---

# Fairness Analysis

A fairness analysis was conducted to determine whether the model performs differently across groups of recipes.

Recipes were divided into two groups based on the **median number of ingredients**:

- **Group 1:** recipes with a number of ingredients less than or equal to the median  
- **Group 2:** recipes with a number of ingredients greater than the median  

## Evaluation Metric

Root Mean Squared Error (**RMSE**) was used to measure prediction error for each group.

## Results

RMSE for recipes with fewer ingredients:

**≈ 0.644**

RMSE for recipes with many ingredients:

**≈ 0.624**

Observed RMSE difference:

**≈ −0.021**

A permutation test with **1000 simulations** was performed to determine whether this difference could occur by random chance.

### Permutation Distribution of RMSE Difference

<iframe src="assets/fairness_permutation_distribution.html" width="800" height="600" frameborder="0"></iframe>

This plot shows the permutation distribution of RMSE differences between the two ingredient-count groups. The red dashed line marks the observed difference.

The resulting **p-value ≈ 0.195**, which is greater than the significance level of **α = 0.05**.

Since the p-value is greater than the significance level, we **fail to reject the null hypothesis**. This suggests that there is **no statistically significant evidence that the model performs differently between recipes with many ingredients and those with fewer ingredients**.

---

# Conclusion

This project examined whether simple recipe characteristics can explain and predict average recipe ratings.

The exploratory analysis showed that recipe preparation times vary substantially and are strongly right-skewed, while ratings are concentrated near the top of the rating scale. Statistical testing suggested that preparation time is associated with whether a recipe receives ratings, but the predictive models showed that structural features alone have limited ability to predict average ratings accurately.

Although the final model introduced additional engineered features and a more flexible algorithm, its performance remained similar to the baseline model. This suggests that factors beyond simple structural recipe features, such as taste, presentation, or user preference, likely play a larger role in determining how users rate recipes.

---

# Project Overview

The analysis includes:

- Exploratory data analysis of recipe preparation time and ratings  
- Missingness analysis  
- Hypothesis testing using permutation tests  
- Building a baseline linear regression model  
- Improving the model using a Random Forest with hyperparameter tuning  
- A fairness analysis evaluating model performance across recipe groups  

---

# Author

**Nam Ngo**
