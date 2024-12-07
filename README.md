# What Affects How Long a Recipe Takes to Complete?
By Shweta Kumar  

## Introduction

The dataset used for this analysis is the **Recipes and Ratings Dataset**, sourced from **food.com**. It consists of two key files:
- **`RAW_recipes.csv`**: Contains recipe details like name, tags, preparation time, ingredients, and nutritional values.
- **`RAW_interactions.csv`**: Contains user reviews and ratings for recipes.

This dataset is significant because it offers insights into recipe preparation time, ingredient complexity, and user preferences, making it valuable for food enthusiasts, recipe developers, and even restaurants looking to optimize their menus.

### Question of Focus
**"What factors affect how long a recipe takes to complete?"**

Understanding this relationship could reveal whether simpler recipes (in terms of time) are more favored by users or if other factors play a stronger role in recipe ratings.

### Why This Matters
Readers should care about this dataset and question because it provides actionable insights into:
- User preferences for recipe complexity.
- The potential to optimize recipe content for better ratings.
- Broader implications for food content creators aiming to appeal to their audience.

### Dataset Overview
- **Number of Rows:** `234428` rows.

### Relevant Columns
| Column Name       | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| `name`            | The name of the recipe.                                                    |
| `minutes`         | The time required to prepare the recipe, in minutes.                       |
| `tags`            | Categories or labels associated with the recipe (e.g., 'quick', 'vegan').  |
| `avg_rating`      | The average user rating for the recipe, on a scale of 1-5.                 |
| `num_ratings`     | The total number of ratings submitted for the recipe.                      |
| `n_steps`         | The number of steps required to prepare the recipe.                        |
| `n_ingredients`   | The number of ingredients needed for the recipe.                           |

By focusing on these columns, we aim to analyze how recipe preparation time correlates with user satisfaction as measured by average ratings.

---

## Step 2: Data Cleaning and Exploratory Data Analysis

### Cleaning Steps:
1. Converted `rating = 0` to `NaN` to avoid skewing averages.
2. Dropped irrelevant columns like `steps`, `description`, and `review`.
3. Dropped rows with missing values in `avg_rating`.
4. Extracted numeric features from the `nutrition` column and renamed them for clarity (`calories`, `protein`, etc.).

### Filtered Tags:
To simplify the analysis, I limited the `tags` column to four categories:
- **'15-minutes-or-less', '30-minutes-or-less', '60-minutes-or-less', '4-hours-or-less'**

