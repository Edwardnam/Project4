# Predicting Recipe Ratings from Recipe Characteristics

## DSC 80 Final Project

This project analyzes the relationship between recipe characteristics and user ratings using the **Recipes and Ratings dataset from Food.com**.

The goal of the project is to investigate whether features such as **preparation time, number of ingredients, and number of steps** can help predict the average rating of a recipe.

---

# Introduction

This project analyzes the **Recipes and Ratings dataset from Food.com** to investigate how recipe characteristics relate to user ratings.

The dataset contains information about recipes such as preparation time, number of ingredients, cooking steps, and user ratings. By combining recipe features with rating data, the project explores whether structural properties of recipes influence how users evaluate them.

## Research Question

Can recipe characteristics such as **preparation time**, **number of ingredients**, and **number of steps** be used to predict the average rating of a recipe?

Understanding these relationships may provide insight into which types of recipes tend to receive higher ratings and whether complexity or preparation time influences user satisfaction.

---

# Data Cleaning and Exploratory Data Analysis

The Recipes dataset and Interactions dataset were merged using the recipe ID to combine recipe features with user ratings.

Several exploratory visualizations were created to understand the structure of the dataset.

---

## Distribution of Recipe Preparation Time

<iframe
src="assets/prep_time_distribution.html"
width="800"
height="600"
frameborder="0">
</iframe>

Recipe preparation times are **heavily right-skewed**, meaning that most recipes require relatively little time while a smaller number take much longer to prepare. Most recipes fall below approximately 100 minutes.

---

## Distribution of Recipe Ratings

<iframe
src="assets/rating_distribution.html"
width="800"
height="600"
frameborder="0">
</iframe>

Average ratings are **highly concentrated between 4 and 5**, indicating that most recipes on the platform receive favorable reviews from users.

---

## Ingredients vs Average Rating

<iframe
src="assets/ingredients_vs_rating.html"
width="800"
height="600"
frameborder="0">
</iframe>

The scatterplot shows **no strong relationship between the number of ingredients and average rating**. Recipes with both few and many ingredients tend to receive similar ratings, suggesting that complexity alone may not determine user satisfaction.

---

# Assessment of Missingness

The column **avg_rating** contains missing values because some recipes have not yet received any user ratings.

This missingness likely occurs when users choose not to rate a recipe. Since the probability of missingness may depend on user behavior rather than the rating value itself, this missingness is likely **Missing At Random (MAR)**.

A permutation test was conducted to determine whether preparation time is associated with rating missingness. The results suggest that recipes with longer preparation times may be slightly more likely to have missing ratings.

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

The permutation test produced a **p-value below 0.05**, suggesting that preparation time may be associated with whether a recipe receives ratings.

---

# Framing a Prediction Problem

The goal of the prediction task is to **predict the average rating (`avg_rating`) of a recipe** using features available before users rate the recipe.

This is a **regression problem** because the target variable is continuous and ranges from 1 to 5.

## Evaluation Metric

The model is evaluated using **Mean Squared Error (MSE)**, which measures the average squared difference between predicted ratings and actual ratings.

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

- minutes (quantitative)  
- n_ingredients (quantitative)  
- n_steps (quantitative)

## Performance

Baseline model results:

**RMSE ≈ 0.636**

This relatively high prediction error suggests that these basic recipe features alone are not strong predictors of recipe ratings.

---

# Final Model

The final model uses a **Random Forest Regressor**, which can capture nonlinear relationships between variables.

## Feature Engineering

Two additional features were created:

### log_minutes

The logarithm of preparation time. Preparation times are highly skewed, so this transformation reduces the influence of extreme values and helps the model capture patterns more effectively.

### steps_per_ingredient

The ratio of the number of steps to the number of ingredients. This feature captures the **complexity of the recipe instructions**, which may influence how users evaluate recipes.

## Hyperparameter Tuning

Hyperparameters were optimized using **GridSearchCV with 3-fold cross-validation**.

Best parameters:

- `n_estimators = 50`
- `max_depth = 5`

## Model Performance

Training RMSE ≈ 0.640  
Testing RMSE ≈ 0.636

The final model performs **very similarly to the baseline model**, suggesting that the available recipe features still have limited predictive power for predicting average ratings.

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

The resulting **p-value ≈ 0.195**.

Since the p-value is greater than the significance level of **α = 0.05**, we **fail to reject the null hypothesis**.

This suggests that there is **no statistically significant evidence that the model performs differently between recipes with many ingredients and those with fewer ingredients**.

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
