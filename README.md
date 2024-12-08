# What Affects How Long a Recipe Takes to Complete?
By Shweta Kumar  

## Introduction

The dataset used for this analysis is the **Recipes and Ratings Dataset**, sourced from **food.com**. It consists of two key files:
- **`RAW_recipes.csv`**: Contains recipe details like name, tags, preparation time, ingredients, and nutritional values.
- **`RAW_interactions.csv`**: Contains user reviews and ratings for recipes.

This dataset is significant because it offers insights into recipe preparation time, ingredient complexity, and user preferences, making it valuable for food enthusiasts, recipe developers, and even restaurants looking to optimize their menus.

### Question of Focus
**"What factors affect how long a recipe takes to complete?"**

Understanding the factors that affect recipe preparation time helps home cooks plan meals more efficiently and avoid overly complex recipes. For recipe developers and businesses, these insights enable the creation of time-friendly content that appeals to busy individuals, boosting user satisfaction and engagement. Ultimately, this analysis benefits anyone looking to optimize their cooking experience or tailor recipes to specific audience needs.

### Dataset Overview
- **Number of Rows:** `234428` rows.

### Relevant Columns
| Variable      | Description                                                               |
|:--------------|--------------------------------------------------------------------------:|
| name          | The name of the recipe.                                                   |
| minutes       | The time required to prepare the recipe, in minutes.                      |
| tags          | Categories or labels associated with the recipe (e.g., 'quick', 'vegan'). |
| avg_rating    | The average user rating for the recipe, on a scale of 1-5.                |
| num_ratings   | The total number of ratings submitted for the recipe.                     |
| n_steps       | The number of steps required to prepare the recipe.                       |
| n_ingredients | The number of ingredients needed for the recipe.                          |
| calories      | The number of calories in the recipe.                                     |

By focusing on these columns, we aim to analyze which factors play a role in recipe completion time.

---

## Data Cleaning and Exploratory Data Analysis

### Cleaning Steps:
1. Left merge the recipes and interactions datasets together.
2. In the merged dataset, fill all ratings of 0 with np.nan.
3. Find the average rating per recipe and add this Series back to the recipes dataset.

*These first 3 steps were done as recommened in the Recipes and Ratings Dataset Download Page.*

4. Dropped all rows with an average rating of NaN or 0 to avoid skewing the dataset.
5. Dropped irrelevant columns like `steps`, `description`, and `review` which provided more information for each recipe but were not necessarily helpful in the actual model building process. These columns tended to be text heavy and not generalizable to the whole dataset.
6. Extracted numeric features from the `nutrition` column and renamed them for clarity (`calories`, `protein`, etc.) so that these features could be explored in the model building process.
7. Identified specific tags I would like to use in the model. I decided to focus on time related tags such as `30-minutes-or-less` and `4-hours-or-less` which I felt would be helpful in predicting the completion time of the recipe.
8. Filtered out the dataset on these specific tags to reduce the size of the dataset and make it easier to work with.

### Final Dataset
| name                              | minutes | tags               | n_steps | n_ingredients | avg_rating | num_ratings | calories | total_fat | sugar | sodium | protein | saturated_fat | carbohydrates |
|-----------------------------------|---------|--------------------|---------|---------------|------------|-------------|----------|-----------|-------|--------|---------|----------------|----------------|
| 1 brownies in the world best ever | 40      | 60-minutes-or-less | 10      | 9             | 4          | 1           | 138.4    | 10        | 50    | 3      | 3       | 19             | 6              |
| 1 in canada chocolate chip cookies| 45      | 60-minutes-or-less | 12      | 11            | 5          | 1           | 595.1    | 46        | 211   | 22     | 13      | 51             | 26             |
| 412 broccoli casserole            | 40      | 60-minutes-or-less | 6       | 9             | 5          | 4           | 194.8    | 20        | 6     | 32     | 22      | 36             | 3              |
| millionaire pound cake            | 120     | 4-hours-or-less    | 7       | 7             | 5          | 1           | 878.3    | 63        | 326   | 13     | 20      | 123            | 39             |
| 2000 meatloaf                     | 90      | 4-hours-or-less    | 17      | 13            | 5          | 2           | 267      | 30        | 12    | 12     | 29      | 48             | 2              |


### Univariate Analysis
<iframe
  src="assets/uni_analysis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot shows the distribution of the number of steps required to complete recipes. Most recipes involve a moderate number of steps, with a peak around 6-7 steps, indicating that they are designed to be manageable for home cooks. This helps answer the initial question by highlighting that recipes with fewer steps are likely to require less time to prepare, making step count a key factor in predicting preparation time.

### Bivariate Analysis
<iframe
  src="assets/bi_analysis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot "Average Rating vs Recipe Duration (Minutes)" plots the average rating for a recipe against the amount of time it takes to complete. I noticed that there didn't appear to be any apparent trend in this plot, so I decided not to use average rating as a feature in my model building.


### Interesting Aggregaties

| time_bin   |   avg_rating |
|:-----------|-------------:|
| 0-30       |      4.64439 |
| 30-60      |      4.60647 |
| 60-120     |      4.62727 |
| 120-180    |      4.63081 |
| 180-300    |      4.63865 |

This pivot table shows the average rating for recipes grouped by preparation time bins. It reveals that recipes with very short preparation times (0-30 minutes) have the highest average rating, but the ratings remain consistently high across all time bins, indicating that preparation time has minimal impact on user satisfaction. This further empahsizes that the average rating column would not be a useful feature in predicting the recipe duration time.

### Imputation

| Column      |   Missing Values |
|:------------|-----------------:|
| description |              110 |
| review      |               57 |


This analysis is performed on the initial unmodified but merged dataframe that was created. Here we can see that only the review and description columns had missing values. These columns were both dropped so imputation was not needed in this scenario.


## Framing a Prediction Problem

### Prediction Problem

The prediction problem I chose to focus on in this report is: **Predict the number of minutes to prepare recipes**.

### Problem Type
This is a **regression problem** because the response variable, "number of minutes to prepare recipes," is continuous.

### Response Variable
The response variable is **`minutes`**, representing the total time required to prepare a recipe. This variable was chosen because understanding the factors influencing preparation time can help optimize recipes for convenience and efficiency, catering to user preferences.

### Evaluation Metric
The primary evaluation metric for the model is the **Mean Squared Error (MSE)**. MSE was selected because:
1. It penalizes larger errors more heavily, encouraging the model to predict preparation times as accurately as possible.
2. As a continuous target variable, MSE is a standard and interpretable metric for assessing the performance of regression models.
   
Other metrics, such as Mean Absolute Error (MAE), were considered, but MSE's sensitivity to larger deviations makes it more suitable for this problem, where precise predictions are critical for practical applications.


## Baseline Model

### Model Description and Features

The model used is a **Random Forest Regressor**, implemented within a pipeline that preprocesses the data before training. 

#### Features:
1. **`tags`** (Nominal): This categorical feature contains recipe tags (e.g., "60-minutes-or-less"). It was encoded using a **OneHotEncoder**, which creates binary columns for each unique tag.
2. **`calories`** (Quantitative): This numerical feature represents the calorie content of each recipe. It was scaled using a **StandardScaler** to normalize the values.

- **Quantitative Features:** 1 (`calories`)
- **Nominal Features:** 1 (`tags`)
- **Ordinal Features:** None

#### Model Performance:
- **Mean Squared Error (MSE):** 559.415
- **R² Score:** 0.6059

### Evaluation:
The MSE indicates the average squared error between the predicted and actual preparation times, and the R² score suggests that approximately 60.59% of the variance in preparation times is explained by the model. While the model shows reasonable performance, there is room for improvement. The MSE is relatively high, which suggests that some predictions may be significantly off. Additionally, incorporating more features or refining the existing ones may lead to better performance.

## Final Model

### Feature Selection and Justification

#### Features Added:
1. **`steps_per_ingredient`:** This feature represents the number of steps required per ingredient in a recipe. It captures the complexity of the recipe, as more steps per ingredient often indicate intricate preparation methods. This feature is highly relevant because more complex recipes are likely to take longer to prepare, making it a strong predictor for preparation time.
2. **`n_steps` with Quantile Transformation:** The total number of steps in a recipe is directly related to preparation time. Applying a **Quantile Transformer** normalizes its distribution, making it easier for the model to learn patterns, especially when the original distribution is skewed.

These features were chosen because they directly relate to the preparation process, offering insights into both the complexity and length of recipes.

---

### Modeling Algorithm and Hyperparameter Selection

#### Algorithm:
The final model is a **Random Forest Regressor**, chosen for its robustness to overfitting and ability to handle both numerical and categorical data. It also naturally captures interactions between features, which is valuable for a dataset with a mix of tags and numerical values.

#### Hyperparameters:
The hyperparameters that performed the best were:
- **`n_estimators`:** 100
- **`max_depth`:** 20
- **`min_samples_split`:** 5

These were selected through **GridSearchCV** using a 3-fold cross-validation on the training data, with the objective of minimizing the **Mean Squared Error (MSE)**. This method ensured that the selected hyperparameters generalized well across different subsets of the data.

---

### Model Performance and Comparison

#### Final Model Performance:
- **Mean Squared Error (MSE):** 381.04
- **R² Score:** 0.7315

#### Baseline Model Performance:
- **Mean Squared Error (MSE):** 559.41
- **R² Score:** 0.6059

#### Improvement:
The final model significantly improves over the baseline:
- The MSE is reduced by approximately 178.37, indicating a substantial decrease in the average squared error between predictions and actual values.
- The R² score increased from 0.6059 to 0.7315, meaning the final model explains approximately 12.56% more variance in preparation time than the baseline model.

This improvement is attributed to the addition of more meaningful features (`steps_per_ingredient` and transformed `n_steps`), which better capture the complexity of recipes, as well as careful hyperparameter tuning, which optimized the Random Forest algorithm's performance.

