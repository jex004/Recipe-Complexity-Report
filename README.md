# An Analysis on the Relationship Between Recipe Complexity and Recipe Popularity
Report by Jenny Xu & Claire Lee

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
6. Removed the recipe "how to preserve a husband". This recipe was uploaded as a joke since preserving a husband is not actually a valid recipe.
7. Removed the recipe "napa dave s individual breakfast casseroles". When combing through our data for null values, we found that this particular recipe has null values in `'user_id'`, `'recipe_id'`, `'date'`, `'review'`, and `'avg_rating'`. This is likely due to the recipe being included in the recipes dataset, but not in the interactions dataset. In other words, this recipe has no reviews. Since there is only one recipe with zero reviews, we treated it as an outlier and removed it.

ask jenny abt this
<iframe src="assets/cleaned_df.html" width="100%" height="400px" frameborder="0"></iframe>

These are the columns we ended up with:

| Column                  | Description    |
| :---------------------- | :------------- |
| `'name'`                | object         |
| `'id'`                  | int64          |
| `'minutes'`             | int64          |
| `'contributor_id'`      | int64          |
| `'submitted'`           | datetime64[ns] |
| `'tags'`                | object         |
| `'nutrition'`           | object         |
| `'n_steps'`             | int64          |
| `'steps'`               | object         |
| `'description'`         | object         |
| `'ingredients'`         | object         |
| `'n_ingredients'`       | int64          |
| `'user_id'`             | float64        |
| `'recipe_id'`           | float64        |
| `'date'`                | datetime64[ns] |
| `'rating'`              | float64        |
| `'review'`              | object         |
| `'avg_rating'`          | object         |
| `n_reviews`             | float64        |
| `'is_easy'`             | bool           |
| `'is_kid_friendly'`     | bool           |

Below is our cleaned dataframe `cleaned_df`

*Some outputs have been truncated for nicer viewing

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                     | nutrition                                    |   n_steps | steps                                                                                                   | description                                                                                             | ingredients                                                                                             |   n_ingredients |          user_id |   recipe_id | date       |   rating | review                                                                                                  |   avg_rating |   n_interactions | is_easy   | is_kid_friendly   |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:-----------------------------------------------------------------------------------------|:---------------------------------------------|----------:|:--------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------|----------------:|-----------------:|------------:|:-----------|---------:|:--------------------------------------------------------------------------------------------------------|-------------:|-----------------:|:----------|:------------------|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less',  'time-to-make',  'course',  'main-ingredient',  'preparation'... | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]     |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish ... | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.... | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder',... |               9 | 386585           |      333281 | 2008-11-19 |        4 | These were pretty good, but took forever to bake.  I would send it ended up being almost an hour!  E... |            4 |                1 | False     | False             |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less',  'time-to-make',  'cuisine',  'preparation',  'north-american'... | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0] |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder',... | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the b... | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour',... |              11 | 424680           |      453467 | 2012-01-26 |        5 | Originally I was gonna cut the recipe in half (just the 2 of us here), but then we had a park-wide y... |            5 |                1 | False     | False             |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less',  'time-to-make',  'course',  'main-ingredient',  'preparation'... | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a ... | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this ... | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground b... |               9 |  29782           |      306168 | 2008-12-31 |        5 | This was one of the best broccoli casseroles that I have ever made.  I made my own chicken soup for ... |            5 |                4 | True      | False             |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less',  'time-to-make',  'course',  'main-ingredient',  'preparation'... | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a ... | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this ... | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground b... |               9 |      1.19628e+06 |      306168 | 2009-04-13 |        5 | I made this for my son's first birthday party this weekend. Our guests INHALED it! Everyone kept say... |            5 |                4 | True      | False             |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less',  'time-to-make',  'course',  'main-ingredient',  'preparation'... | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a ... | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this ... | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground b... |               9 | 768828           |      306168 | 2013-08-02 |        5 | Loved this.  Be sure to completely thaw the broccoli.  I didn&#039;t and it didn&#039;t get done in ... |            5 |                4 | True      | False             |




### Univariate Data

For univariate data analysis, we examined the distribution of the the number of steps each recipe has. In the plot below, it is clear that the distribution of number of steps is skewed right, indicating that most of the recipes on [food.com](https://www.food.com/) have lower numbers of steps. The right skew of the distribution also suggests that as the number of steps increases, the number of recipes with that many steps on [food.com](https://www.food.com/) decreases.




### Bivariate Data

For bivariate data analysis, we examined the relationship between the distribution of the number of steps each recipe has and the number of reviews each recipe has. In the plot below, as per the trendline, we can see that as the number of steps a recipe takes increases, the number of reviews it has also increases, suggesting a positive trend between number of steps and number of reviews. Later, we can see if this relationship affects our predictions of number of reviews at all.



## Assessment of Missingness
When investigating NaN values in our dataframe, we discovered that the columns `'description'`, `'rating'`, and `'review'` contained a significant amount of null values.

### NMAR Analysis

We believe that the missingness in the `'review'` column is NMAR because whether not a review is left is the user's choice. They may leave a rating but not a review because they did not feel strongly enough about a recipe to warrant a full blown review, or perhaps they just prefer not to leave review in general. In order to confirm that this missingness is indeed NMAR and not MAR though, we would need data on either how the user felt about the recipe, which could be indicated by rating, or their general preferences when it comes to leaving reviews. So, we may want to obtain a numerical rating and a boolean value about their usual review preferences that could explain NMAR missingness for the `'review'` column.

### Missingness Dependency

Unlike `'review'`, we believe that the missingness in the `'description'` column could potentially be MAR and dependent on what year the recipe was submitted to [food.com](https://www.food.com/). In order to investigate this, we created a new column `'year'` containing only the year each recipe was submitted and then ran a permutation test to examine the missingness dependency of `'description'` on `'year'`.

**Null Hypothesis:** The missingness of recipe descriptions does not depend on the year the recipe was submitted.

**Alternative Hypothesis:** The missingness of recipe descriptions does depend on the year the recipe was submitted.

**Test Statistic:** TVD(total variation distiance) between the observed missingness distribution for each year and the distribution obtained after shuffling the `'year'` column.

**Significance Level:** 0.05

**Hypothesis Test Conclusion:** We obtained a p-value of 0.4529. Since this is significantly greater than our significance level of 0.05, we fail to reject the null hypothesis. We do not have sufficient evidence to suggest that the missingness of recipe descriptions depends on the year the the recipe was submitted.


## Hypothesis Testing

**Null Hypothesis (H₀):** Tagging a recipe as easy does not significantly affect the number of reviews that the recipe receives.

**Alternative Hypothesis (H₁):** Tagging a recipe as easy significantly increases the number of reviews that the recipe receives.

**Test Statistics:** We will use the difference of means in number of reviews between recipes that were tagged as easy and those that were not.

**Significance Level:** 0.05

### Running a Permutation Test

We chose a permutation test because we are trying to see whether tagging a recipe as easy and not tagging a recipe as easy comje from different population distributions, or that they come from different data generating processes. Our test statistic needs to measure how different two numerical distributions are, which is why we chose to use the difference of means.

**p-value:** 0.0

### Permutation Test Conclusion

Since our p-value of 0.0 is less than our significane level of 0.05, we reject our null hypothesis. We have sufficient evidence to suggest that tagging a recipe as easy significantly increases the number of reviews that the recipe receives.


## Framing a Prediction Problem

**Prediction Problem:** Can we predict the number of reviews a recipe has based on how complex the recipe is?

We chose a regression problem, with our response variable being the `'n_interactions'` column. We chose this variable because we want to predict the number of interactions each recipe receives based on other features that might tell us how complex the reicpe is. We also chose R<sup>2</sup> as our metric for evaluation. We chose to use R<sup>2</sup> as our metric instead of MSE, because MSE gives us a measure of the magnitude of error between the predicted and actual values. Additionally,MSE would not be able to offer a direct comparison between different models, so we would not be able to evaluate whether a certain model is better at predicting the number of reviews a recipe has. R<sup>2</sup>, on the other hand, is able to tell us how well the model's predictions fit the data, and provides an easier way of understanding how much of the variability in n_interactions is explained by the model.

We first created a new dataframe `filtered_df` containing only the columns we need for the remainder of this report.

We believe that each of the following columns either contribute or indicate the complexity of a recipe:

- `'minutes'` - The longer a recipe takes to complete, the more complex it may be
- `'n_steps'` - The more steps a recipe takes, the more complex it may be
- `'n_ingredients'` - Recipes that require more ingredients can be considered more complex
- `'is_easy'` - Recipes that contain the easy|beginner tag may be less complex than recipes that do not contain this tag
- `'is_kid_friendly'` - Recipes that contain the kid-friendly tag may be less complex than recipes that do not contain this tag

We also kept the `'n_interactions'` column as it is what we are trying to predict.


## Baseline Model

Our baseline model uses a Random Forest Regressor, which reduces the risk of overfitting because it aggregates the results of multiple decision trees. The subset of features we chose from our `filtered_df` dataframe are: `'minutes'`, `'n_steps'`, and `'n_ingredients'`, which are all discrete quantitative features. We did not need to perform any encodings because all of our features are quantitative, and can all directly be handled by decision trees. 

Our model has an R<sup>2</sup> value of 0.8343, meaning around 83.43% of the variance in `'n_interactions'` can be explained by the model. We believe this shows our current model is "good" as the R<sup>2</sup> value is quite high and suggests that our model can capture the significant patterns in the data.

## Final Model



## Fairness Analysis

**Group X:** Recipes from earlier years (before 2011)

**Group Y:** Recipes from later years (after 2011)

We chose the year 2011 as our threshold because it was the median of the years retrieved from `'date'` column of the cleaned dataframe. 

**Evaluation Metric:** R<sup>2</sup>

**Null Hypothesis:** The regressor's accuracy is the same for both short and long recipes, and any differences observed are due to random chance.

**Alternative Hypothesis:** The regressor's accuracy is higher for long recipes.

**Test Statistic:** absolute difference in R<sup>2</sup> values

**Significance Level:** 0.05

**Resulting P-Value:** 0.00

### Permutation Test Conclusion
Since our p-value is 0.00, we reject our null hypothesis. There is sufficient evidence to suggest that the regressor's accuracy is higher for 

__
Clearly state your choice of Group X and Group Y, your evaluation metric, your null and alternative hypotheses, your choice of test statistic and significance level, the resulting 
p-value, and your conclusion.
__
