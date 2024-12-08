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
| `name`            | The name of the recipe.                                                    |
| `minutes`         | The time required to prepare the recipe, in minutes.                       |
| `tags`            | Categories or labels associated with the recipe (e.g., 'quick', 'vegan').  |
| `avg_rating`      | The average user rating for the recipe, on a scale of 1-5.                 |
| `num_ratings`     | The total number of ratings submitted for the recipe.                      |
| `n_steps`         | The number of steps required to prepare the recipe.                        |
| `n_ingredients`   | The number of ingredients needed for the recipe.                           |
| `calories`        | The number of calories in the recipe.                                      |

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
| name                                 |   minutes | tags               |   n_steps |   n_ingredients |   avg_rating |   num_ratings |   calories |   total_fat |   sugar |   sodium |   protein |   saturated_fat |   carbohydrates |
|:-------------------------------------|----------:|:-------------------|----------:|----------------:|-------------:|--------------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|
| 1 brownies in the world    best ever |        40 | 60-minutes-or-less |        10 |               9 |            4 |             1 |      138.4 |          10 |      50 |        3 |         3 |              19 |               6 |
| 1 in canada chocolate chip cookies   |        45 | 60-minutes-or-less |        12 |              11 |            5 |             1 |      595.1 |          46 |     211 |       22 |        13 |              51 |              26 |
| 412 broccoli casserole               |        40 | 60-minutes-or-less |         6 |               9 |            5 |             4 |      194.8 |          20 |       6 |       32 |        22 |              36 |               3 |
| millionaire pound cake               |       120 | 4-hours-or-less    |         7 |               7 |            5 |             1 |      878.3 |          63 |     326 |       13 |        20 |             123 |              39 |
| 2000 meatloaf                        |        90 | 4-hours-or-less    |        17 |              13 |            5 |             2 |      267   |          30 |      12 |       12 |        29 |              48 |               2 |

