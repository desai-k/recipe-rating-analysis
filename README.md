# recipe-rating-analysis

## Introduction
Here’s a polished version you can drop straight into your website’s **Introduction** section:

---

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
Before conducting analysis, the dataset required several preprocessing steps to ensure accuracy and usability. These steps were designed to reflect the way the data was generated and to prepare it for meaningful analysis.

### 1. Merging Datasets
The recipes dataset was merged with the interactions dataset to incorporate user ratings. Since each recipe can receive multiple ratings from different users, we aggregated ratings at the recipe level to ensure each recipe has a single representative value.

### 2. Handling Missing Ratings
Ratings of `0` were treated as missing values and replaced with `NaN`, since a rating of zero does not represent a valid user score but instead indicates missing or invalid data.

### 3. Creating Average Rating
We computed the average rating (`avg_rating`) for each recipe by grouping ratings by recipe ID. This provides a single response variable that summarizes overall user preference for each recipe.

### 4. Expanding Nutritional Information
The `nutrition` column originally contained lists stored as strings. These were converted into separate numerical columns:
- calories  
- total fat  
- sugar  
- sodium  
- protein  
- saturated fat  
- carbohydrates  

This transformation allows nutritional features to be analyzed individually and used in modeling.

### 5. Handling Outliers
Recipes with preparation times greater than 300 minutes were removed to reduce the influence of extreme values, which may represent errors or atypical recipes.

### 6. Creating Sugar Categories
To better analyze how sugar content relates to ratings, we created a new categorical variable `sugar_bin` by dividing sugar into quartiles (Low, Medium-Low, Medium-High, High). This allows for easier comparison across groups.

---

After cleaning, the dataset contains structured numerical features and a reliable average rating for each recipe, making it suitable for exploratory data analysis and modeling.



## Assessment of Missingness


## Hypothesis Testing

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis
