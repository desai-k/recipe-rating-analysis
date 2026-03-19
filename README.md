# recipe-rating-analysis

## Introduction
Food plays an important role in daily life, and understanding what makes a recipe appealing can help users make better cooking decisions. In this project, I analyze a large dataset of online recipes and user ratings to investigate how nutritional characteristics influence user preferences.

The dataset is composed of two sources: a recipes dataset containing 83,782 recipes and an interactions dataset containing 731,927 user ratings. After combining and cleaning these datasets, the resulting dataset used for this analysis contains **83,782 recipes**, each with aggregated rating information.

For this project, I focus on the following columns that are most relevant to understanding user preferences:

* `nutrition`: a list-like column containing nutritional values for each recipe, including sugar, calories, fat, protein, and other components
* `minutes`: the total preparation time required for the recipe
* `n_steps`: the number of steps in the recipe, representing its complexity
* `avg_rating`: the average rating given to the recipe by users

These variables capture both the nutritional content of recipes and aspects of user experience, such as effort and perceived quality.

### Central Question

**Do recipes with higher sugar content receive higher ratings?**

This question is important because sugar is often associated with taste and enjoyment. If recipes with higher sugar content consistently receive better ratings, it may indicate that users favor sweeter foods. Understanding this relationship can provide insight into user preferences and help inform the design of recommendation systems, as well as guide individuals in choosing recipes that align with their tastes.

---

## Data Cleaning and Exploratory Data Analysis
Before conducting analysis, the dataset required several preprocessing steps to ensure accuracy and usability. These steps were designed with the data generating process in mind and to ensure that the variables used in analysis meaningfully reflect user behavior and recipe characteristics.

### 1. Merging Datasets

The recipes dataset was merged with the interactions dataset to incorporate user ratings. Since each recipe can receive multiple ratings from different users, we aggregated ratings at the recipe level so that each recipe is represented by a single observation. This aligns with the data generating process, where multiple independent users contribute ratings for the same recipe.

**Impact:** This allowed us to analyze relationships at the recipe level (e.g., sugar vs. average rating) rather than at the individual rating level.

---

### 2. Handling Missing Ratings

Ratings of `0` were treated as missing values and replaced with `NaN`, since a rating of zero does not represent a valid user score but instead indicates missing or invalid data.

**Impact:** This prevents artificially lowering average ratings and ensures that computed averages reflect only valid user opinions.

---

### 3. Creating Average Rating

We computed the average rating (`avg_rating`) for each recipe by grouping ratings by recipe ID. This provides a single response variable that summarizes overall user preference.

**Impact:** This transformation creates a stable and interpretable target variable for both exploratory analysis and predictive modeling.

---

### 4. Expanding Nutritional Information

The `nutrition` column originally contained lists stored as strings. These were parsed and converted into separate numerical columns:

* calories
* total fat
* sugar
* sodium
* protein
* saturated fat
* carbohydrates

This step makes the data usable for quantitative analysis and reflects how nutritional information is inherently structured.

**Impact:** This allows us to isolate sugar as a variable of interest and directly analyze its relationship with ratings.

---

### 5. Handling Outliers

Recipes with preparation times greater than 300 minutes were removed, as these likely represent extreme or atypical cases that do not reflect typical user behavior.

**Impact:** This reduces the influence of outliers that could distort distributions and relationships in exploratory analysis.

---

### 6. Creating Sugar Categories

To better analyze how sugar content relates to ratings, we created a categorical variable `sugar_bin` by dividing sugar values into quartiles (Low, Medium-Low, Medium-High, High).

**Impact:** This enables clearer comparisons across groups and supports aggregation and visualization in later analysis.

---

### Summary
After cleaning, the dataset contains structured numerical features and a reliable average rating for each recipe. These steps ensure that the dataset accurately reflects both the nutritional properties of recipes and user-generated ratings, making it suitable for exploratory data analysis and modeling.

### Univariate Analysis
#### Plot 1: Distribution of Ratings
<iframe
  src="assets/plot1.html"
  width="600"
  height="600"
  frameborder="0"
></iframe>

The histogram shows the distribution of average recipe ratings, with the percentage of recipes on the y-axis and rating values on the x-axis. The distribution is heavily skewed toward higher ratings, with a large concentration of recipes clustered between 4.5 and 5.0. In particular, ratings near 5.0 make up the largest share, indicating that most recipes receive very positive feedback.

Lower ratings (below 3.5) are relatively rare, appearing only as small percentages across a few bins. There are also very few extremely low ratings (around 1–2), suggesting that poorly rated recipes are uncommon in the dataset.

Overall, this pattern indicates a strong positive bias in ratings, where most recipes are rated highly. This could reflect genuinely good recipe quality, user rating tendencies (e.g., people are more likely to rate recipes they like), or platform bias toward higher scores.

#### Plot 2: Distribution of Sugar Content
<iframe
  src="assets/plot2.html"
  width="600"
  height="600"
  frameborder="0"
></iframe>

The distribution of sugar ratings is right-skewed, with most recipes concentrated at lower values and a smaller number extending into much higher ranges.

This suggests that higher sugar ratings are less common but may still represent a distinct subset of recipes that could influence overall trends or user preferences.

### Bivariate Analysis
#### Plot 3: Sugar vs Rating
<iframe
  src="assets/plot3.html"
  width="600"
  height="600"
  frameborder="0"
></iframe>

The boxplot illustrates how average recipe ratings are distributed across different sugar levels. Median ratings are fairly consistent across all categories, generally clustering between 4.5 and 5.0. While higher sugar groups show a slightly tighter concentration of high ratings, there is substantial overlap in the interquartile ranges across all bins. Lower-rated outliers are present in every category, indicating variability regardless of sugar content. Overall, this suggests that sugar level alone does not strongly differentiate recipe ratings, and other factors likely play a more significant role in determining user satisfaction.

#### Plot 4: Preparation Time vs Rating
<iframe
  src="assets/plot4.html"
  width="600"
  height="600"
  frameborder="0"
></iframe>

The scatter plot illustrates the relationship between sugar content and average recipe rating. The points are widely dispersed across the chart, and although there is a slight upward tendency, it is very minimal. This suggests a **weak positive relationship**, meaning recipes with more sugar might receive slightly higher ratings on average. However, because the points are not tightly clustered around a clear line, the association is **not strong or consistent**, indicating that sugar content is not a major factor influencing recipe ratings.

### Aggregation Table

| sugar_bin   | mean | count |
|------------|------|-------|
| Low        | 4.63 | 19827 |
| Medium-Low | 4.63 | 18269 |
| Medium-High| 4.62 | 18705 |
| High       | 4.63 | 18646 |

The average ratings across sugar content categories are nearly identical.
This suggests that sugar content does not strongly influence how users rate recipes.

## Assessment of Missingness
### Missing Not at Random (MNAR) Analysis

The `description` column is likely **Missing Not at Random (MNAR)**. The presence of a description appears to depend on unobserved factors related to the recipe or the contributor, rather than solely on variables available in the dataset.

Recipe descriptions are typically written manually, so whether a description is included may depend on factors such as the effort a contributor is willing to invest, their perception of the recipe’s importance, or how detailed they want their submission to be. For example, more complex or unique recipes may be more likely to include descriptions, while simpler recipes may not.

Because these influencing factors are not directly observed in the dataset, the missingness of `description` depends on unobserved variables, which is characteristic of **MNAR**.

Additional data—such as contributor activity levels, submission history, or engagement metrics—could help explain this missingness. If such variables account for the missingness, the mechanism could instead be considered Missing At Random (MAR).

---

### Missingness Dependency

The dependency of `description` missingness on other variables was evaluated using permutation tests.

#### Dependency on Number of Steps (`n_steps`)

- **Null Hypothesis (H₀):** Missingness of `description` does not depend on `n_steps`  
- **Alternative Hypothesis (H₁):** Missingness of `description` does depend on `n_steps`  
- **Test Statistic:** Difference in mean `n_steps` between recipes with and without descriptions  

The resulting p-value is approximately **0.041**, which is below the 0.05 significance level. Therefore, the null hypothesis is rejected.

This indicates that the missingness of `description` **depends on `n_steps`**. Recipes with missing descriptions have a different average number of steps compared to those with descriptions, suggesting that recipe complexity is associated with whether a description is provided.

<iframe src="assets/plot5.html" width="100%" height="400" frameborder="0"></iframe>

---

#### Independence from Calories (`calories`)

- **Null Hypothesis (H₀):** Missingness of `description` does not depend on `calories`  
- **Alternative Hypothesis (H₁):** Missingness of `description` does depend on `calories`  
- **Test Statistic:** Difference in mean calories between recipes with and without descriptions  

The resulting p-value is approximately **0.143**, which is greater than 0.05. Therefore, the null hypothesis is not rejected.

This suggests that the missingness of `description` **does not depend on `calories`**, indicating independence with respect to this variable.

<iframe src="assets/plot6.html" width="100%" height="400" frameborder="0"></iframe>

---

### Summary

The missingness of the `description` column exhibits mixed behavior:

- It **depends on `n_steps`**, indicating a relationship with an observed variable  
- It **does not depend on `calories`**  
- It remains plausibly **MNAR overall**, as unobserved factors likely influence whether descriptions are included  

This suggests that while some aspects of missingness may be explained by observed variables, unobserved factors still play an important role.

## Hypothesis Testing
### Hypotheses

- **Null Hypothesis (H₀):** There is no association between sugar content and average recipe rating. Any observed relationship is due to random chance.  
- **Alternative Hypothesis (H₁):** Recipes with higher sugar content tend to receive higher ratings (positive association).  

---

### Test Statistic

The test statistic used is the **difference in mean average ratings** between recipes with **high sugar content** and those with **low sugar content** (High − Low).

---

### Significance Level

A significance level of **α = 0.05** is used.

---

### Method

A permutation test is conducted by randomly shuffling the `sugar_bin` labels across recipes many times (10,000 iterations) and recalculating the difference in mean ratings for each shuffle. This generates a null distribution of the test statistic under the assumption that there is no relationship between sugar content and ratings.

---

### Results

The observed difference in mean ratings between high-sugar and low-sugar recipes is approximately **−0.0021**, indicating that high-sugar recipes received slightly lower ratings on average.

The permutation test produces a p-value of approximately **0.6255**, which is substantially greater than the significance level of 0.05.

Therefore, the null hypothesis is **not rejected**.

---

### Visualization

<iframe src="assets/plot7.html" width="100%" height="400" frameborder="0"></iframe>

---

### Conclusion

There is no statistically significant evidence of a relationship between sugar content and average recipe rating.

Although sugar is often associated with taste preference, this analysis does not support the claim that recipes with higher sugar content receive higher ratings. The small observed difference is likely due to random variation rather than a true underlying effect.

This suggests that other factors—such as preparation method, ingredients, or cuisine type—may play a more important role in determining recipe ratings.

## Framing a Prediction Problem
### Prediction Problem

The goal is to predict the **average rating (`avg_rating`)** of a recipe based on its characteristics.

This is a **regression problem**, since the response variable is continuous and takes values between 1 and 5.

---

### Response Variable

- **`avg_rating`**: the average user rating of a recipe  

This variable is chosen because it reflects overall user satisfaction and is commonly used by recipe platforms to rank and recommend recipes.

---

### Features and Time of Prediction

At the time of prediction, only information available when a recipe is created is used. Variables that depend on future user interaction (such as ratings themselves) are excluded to prevent data leakage.

The features used include:
- preparation time (`minutes`)  
- number of ingredients (`n_ingredients`)  
- nutritional information (e.g., `calories`, `sugar`, `protein`, `carbs`, `total_fat`)  

These features are available prior to any user ratings and capture aspects of recipe complexity and nutritional content that may influence user preferences.

---

### Evaluation Metric

Model performance is evaluated using **Root Mean Squared Error (RMSE)**.

RMSE is appropriate because:
- the response variable is continuous  
- it penalizes larger errors more heavily, making it sensitive to poor predictions  

In addition, **R² (coefficient of determination)** is reported as a secondary metric to measure how well the model explains variability in recipe ratings.

---

### Summary
- **Problem Type:** Regression  
- **Response Variable:** `avg_rating`  
- **Features:** Recipe characteristics available at creation time  
- **Evaluation Metrics:** RMSE (primary), R² (secondary)  
- **Data Leakage Avoidance:** Only pre-rating features are used  

## Baseline Model
### Model Description

A **linear regression model** is used as the baseline model to predict recipe ratings. The model is implemented using a single **scikit-learn Pipeline**, which includes both preprocessing and model training steps to ensure a clean and reproducible workflow.

---

### Features

The model incorporates the following features:

- **`minutes`** — quantitative: preparation time  
- **`n_ingredients`** — quantitative: number of ingredients  

Both features are numeric and used directly in the model.

---

### Encoding and Preprocessing

Since both features are quantitative, no categorical encoding is required.

However, both features are standardized using a **StandardScaler** to place them on comparable scales before fitting the model.

---

### Model Performance

The model is evaluated using **RMSE** and **R²** on both training and test data:

- **Training RMSE:** 0.639  
- **Test RMSE:** 0.630  
- **Training R²:** 0.0004  
- **Test R²:** -0.0002  

The training and test RMSE values are very similar, indicating that the model generalizes well to unseen data and is not overfitting.

However, the R² values are very close to zero (and slightly negative on the test set), indicating that the model explains almost none of the variability in recipe ratings.

---

### Conclusion
The baseline model performs poorly as a predictor of recipe ratings. The selected features (`minutes` and `n_ingredients`) do not provide meaningful predictive power for the response variable.

While the model generalizes well, its lack of explanatory power suggests that more informative features are needed. Recipe ratings are likely influenced by more complex factors such as ingredients, nutritional content, or user preferences.

This baseline model serves as a reference point for evaluating improvements in more advanced models.

## Final Model
### Feature Engineering
To improve upon the baseline model, additional features were engineered to better capture meaningful aspects of the data:

- **`log_minutes`** (quantitative): a log-transformed version of preparation time. This transformation reduces skewness in cooking times and allows the model to better distinguish between relatively short and long recipes.  
- **`calories_per_ingredient`** (quantitative): calculated as `calories / n_ingredients`. This feature represents the density or richness of a recipe, which may better reflect how heavy or indulgent a recipe is compared to total calories alone.  

In addition to the baseline features (`minutes`, `n_ingredients`), the model also includes:

- **`calories`** (quantitative)  
- **`sugar`** (quantitative)  
- **`protein`** (quantitative)  
- **`carbs`** (quantitative)  

These features were included because nutritional content may directly influence taste and user satisfaction, which are likely drivers of recipe ratings.

---

### Model and Hyperparameter Selection

A **Random Forest Regressor** was chosen as the final model because it can capture **nonlinear relationships** and interactions between features, which a linear model cannot.

The following hyperparameters were tuned using **GridSearchCV**:

- **`n_estimators`**: number of trees in the forest  
- **`max_depth`**: maximum depth of each tree  

These hyperparameters were selected to control model complexity and reduce overfitting while maintaining predictive power.

The best-performing hyperparameters were:

- **`n_estimators = 100`**  
- **`max_depth = 5`**

Model selection and hyperparameter tuning were performed using 3-fold cross-validation on the training data, optimizing for **RMSE**.

---

### Model Performance

The final model was evaluated on both training and test data:

- **Training RMSE:** (computed in notebook)  
- **Test RMSE:** 0.62977  
- **Training R²:** (computed in notebook)  
- **Test R²:** 0.00043  

---

### Comparison to Baseline Model

Compared to the baseline linear regression model:

- Test RMSE decreased from **0.62997 → 0.62977**  
- Test R² increased from **−0.00019 → 0.00043**  

This indicates a small improvement in predictive performance.

---

### Conclusion
The final model improves upon the baseline by incorporating engineered features and using a more flexible algorithm capable of modeling nonlinear relationships.

The added features (`log_minutes` and `calories_per_ingredient`) provide additional information about recipe complexity and nutritional density, which are relevant to how users may perceive and rate recipes.

Despite this improvement, overall model performance remains modest. The very low R² suggests that most of the variation in recipe ratings is not explained by the available features. This is likely due to missing factors such as individual taste preferences, cooking quality, or presentation, which are not captured in the dataset.

Overall, the final model demonstrates a measurable but limited improvement over the baseline, highlighting both the value of feature engineering and the limitations of the available data.

## Fairness Analysis
## Step 8: Fairness Analysis

### Groups and Evaluation Metric

The fairness of the final model is evaluated by comparing performance across groups defined by recipe calorie content:

- **Group X:** Low-calorie recipes (below median calories)  
- **Group Y:** High-calorie recipes (above median calories)  

The evaluation metric used is **Root Mean Squared Error (RMSE)**, which is appropriate for this regression task.

---

### Hypotheses

- **Null Hypothesis (H₀):** The model is fair. The RMSE for low-calorie and high-calorie recipes is the same, and any observed difference is due to random chance.  
- **Alternative Hypothesis (H₁):** The model is unfair. The RMSE differs between low-calorie and high-calorie recipes.  

---

### Test Statistic and Significance Level

The test statistic is the **difference in RMSE** between the two groups (Group X − Group Y).

A significance level of **α = 0.05** is used.

---

### Results

A permutation test with 1000 simulations yields a p-value of approximately **0.907**.

Since this p-value is much greater than 0.05, the null hypothesis is not rejected.

---

### Visualization

<iframe src="assets/plot8.html" width="100%" height="400" frameborder="0"></iframe>

---

### Conclusion

There is no statistically significant evidence that the model performs differently between low-calorie and high-calorie recipes. The difference in RMSE between the two groups is very small and consistent with random variation.

This suggests that the model is **fair with respect to calorie-based groupings**, although fairness across other potential groupings may still require further investigation.
