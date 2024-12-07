# What Affects How Long a Recipe Takes to Complete?
By Shweta Kumar  

## Step 1: Introduction

The dataset used for this analysis is the **Recipes and Ratings Dataset**, containing recipe information from **food.com**. The dataset includes two files:
- `RAW_recipes.csv`: Information about recipes (name, ID, time, tags, nutrition, etc.).
- `RAW_interactions.csv`: Reviews and ratings submitted for recipes.

Here are some of the key questions I could explore with this dataset:

### Key Questions:
1. Do shorter recipes have more reviews?  
2. Do specific tags correlate with higher calories?  
3. Does the number of minutes to complete a recipe affect the average rating?  

I chose to investigate question 3: **"Does the number of minutes to complete a recipe affect the average rating?"**

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

