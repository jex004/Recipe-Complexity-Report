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
2. 

<iframe src="cleaned_df.html" width="100%" height="400"></iframe>

## Assessment of Missingness



## Hypothesis Testing



## Framing a Prediction Problem



## Baseline Model



## Final Model



## Fairness Analysis