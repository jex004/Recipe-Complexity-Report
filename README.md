# An Analysis on the Relationship Between Recipe Complexity and Recipe Popularity
Report by Jenny Xu and Claire Lee

## Overview

This project explores the relationship between recipe complexity and the number of reviews a recipe receives.

## Introduction

Online recipe websites provide opportunities for chefs to share their recipes to larger audiences and build a following. For chefs that are trying to grow their influence, it is important to understand the ideal level of complexity that their recipes should be at in order to garner the most attention.

The first dataset is called `recipe`. It contains 83782 rows that each represent a unique recipe and 10 columns whose contents are described below:

| Column             | Description                                                                                                                                    |
| :----------------- | :--------------------------------------------------------------------------------------------------------------------------------------------- |
| `'name'`           | Recipe name                                                                                                                                    |
| `'id'`             | Recipe ID                                                                                                                                      |
| `'minutes'`        | Minutes to prepare recipe                                                                                                                      |
| `'contributor_id'` | User ID who submitted this recipe                                                                                                              |
| `'submitted'`      | Date recipe was submitted                                                                                                                      |
| `'tags'`           | Food.com tags for recipe                                                                                                                       |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe                                                                                                                      |
| `'steps'`          | Text for recipe steps, in order                                                                                                                |
| `'description'`    | User-provided description                                                                                                                      |
| `'ingredients'`    | Text for recipe ingredients                                                                                                                    |
| `'n_ingredients'`  | Number of ingredients in recipe                                                                                                                |

The second dataset is called `interactions`. It contains 731927 rows that each represent a review from a user for a specific recipe and 5 columns whose contents are described below:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

Before continuing with our analysis, it is important to clean the data to ensure our data is accurate and organized, and to better understand the contents of our data.
These are the steps we followed:

1. Left merged the `recipe` and `interactions` datasets together on `'id'` and `'recipe_id'`.
2. Filled all null values in `'rating'` with 0 values (insert explanation)(do i need to keep avg rating even tho i dont use it at all)
3. Created a column of integers `'n_reviews'` that contains the total amount of reviews for each recipe
4. Created a column of boolean values `'is_easy'` where it is True if the recipe's tags contain the 'easy' or 'beginner-cook' tags
5. Created a column of boolean values `'is_kid_friendly'` where it is True if the recipe's tags contain the 'kid-friendly' tag
6. Removed the recipe "_____". This recipe was uploaded as a joke since preserving a husband is not actually a valid recipe.
7. Removed the recipe "_____". When combing through our data for null values, we found that this particular recipe has null values in (insert col names). This is likely due to the recipe being included in the recipes dataset, but not in the interactions dataset. In other words, this recipe has no reviews. Since there is only one recipe with zero reviews, we treated it as an outlier and removed it.
<iframe src="assets/cleaned_df.html" width="100%" height="400px" frameborder="0"></iframe>

These are the columns we kept:

(insert table)

Below is our cleaned dataframe `cleaned_df`

(insert df)

### Univariate Data




### Bivariate Data



## Assessment of Missingness
When investigating NaN values in our dataframe, we discovered that the columns `'description'`, `'rating'`, and `'review'` contained a significant amount of null values.

### NMAR Analysis

We believe that the missingness in the `'review'` column is NMAR because whether not a review is left is the user's choice. They may leave a rating but not a review because they did not feel strongly enough about a recipe to warrant a full blown review, or perhaps they just prefer not to leave review in general. In order to confirm that this missingness is indeed NMAR and not MAR though, we would need data on either how the user felt about the recipe, which could be indicated by rating, or their general preferences when it comes to leaving reviews, which is data we do not have.

### Missingness Dependency

Unlike `'review'`, we believe that the missingness in the `'description'` column could potentially be MAR and dependent on what year the recipe was submitted to [food.com](https://www.food.com/). In order to investigate this, we created a new column `'year'` containing only the year each recipe was submitted and then ran a permutation test to examine the missingness dependency of `'description'` on `'year'`.

**Null Hypothesis:** The missingness of recipe descriptions does not depend on the year the recipe was submitted.

**Alternative Hypothesis:** The missingness of recipe descriptions does depend on the year the recipe was submitted.

**Test Statistic:** someone help me choose between abs diff in means and ks stat bc i ran tests for both.

**Significance Level:** 0.05

## Hypothesis Testing



## Framing a Prediction Problem

We first created a new dataframe `filtered_df` containing only the columns we need for the remainder of this report.

We believe that each of the following columns either contribute or indicate the complexity of a recipe:

- `'minutes'` - The longer a recipe takes to complete, the more complex it is
- `'n_steps'` - The more steps a recipe takes, the more complex it is
- `'n_ingredients'` - Recipes that require more recipes can be considered more complex
- `'is_easy'` - 
- `'is_kid_friendly'` - 

We also kept the `'n_reviews'` column as it is what we are trying to predict.

## Baseline Model



## Final Model



## Fairness Analysis