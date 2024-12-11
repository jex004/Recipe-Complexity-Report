# An Analysis on the Relationship Between Recipe Complexity and Recipe Popularity
Report by Jenny Xu & Claire Lee

## Overview

This project explores the relationship between recipe complexity and the number of reviews a recipe receives.

## Introduction

Online recipe websites provide opportunities for chefs to share their recipes with larger audiences and build a following. For chefs that are trying to grow their influence, it is important to understand the ideal level of complexity that their recipes should be at in order to garner the most attention.

The first dataset is called `recipe`. It contains 83,782 rows that each represent a unique recipe and 10 columns whose contents are described below:

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

The second dataset is called `interactions`. It contains 731,927 rows that each represent a review from a user for a specific recipe and 5 columns whose contents are described below:

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
2. Filled all null values in `'rating'` with 0 values (we decided to treat missing ratings as zero)
3. Created a column of integers `'n_reviews'` that contains the total amount of reviews for each recipe
4. Created a column of boolean values `'is_easy'` where it is True if the recipe's tags contain the 'easy' or 'beginner-cook' tags
5. Created a column of boolean values `'is_kid_friendly'` where it is True if the recipe's tags contain the 'kid-friendly' tag
6. Removed the recipe "how to preserve a husband". This recipe was uploaded as a joke since preserving a husband is not actually a valid recipe.
7. Removed the recipe "napa dave s individual breakfast casseroles". When combing through our data for null values, we found that this particular recipe has null values in `'user_id'`, `'recipe_id'`, `'date'`, `'review'`, and `'avg_rating'`. This is likely due to the recipe being included in the recipes dataset, but not in the interactions dataset. In other words, this recipe has no reviews. Since there is only one recipe with zero reviews, we treated it as an outlier and removed it.


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
| `n_interactions`        | float64        |
| `'is_easy'`             | bool           |
| `'is_kid_friendly'`     | bool           |

Below is our cleaned dataframe `cleaned_df`

*Some outputs have been truncated for nicer viewing


<iframe
  src="assets/cleaned_df.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


### Univariate Data

For univariate data analysis, we examined the distribution of the the number of steps each recipe has. In the plot below, it is clear that the distribution of number of steps is right-skewed, indicating that most of the recipes on [food.com](https://www.food.com/) have lower numbers of steps. The right skew of the distribution also suggests that as the number of steps increases, the number of recipes with that many steps on [food.com](https://www.food.com/) decreases.

<iframe
  src="assets/fig_n_steps.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


### Bivariate Data

For bivariate data analysis, we examined the relationship between the distribution of the number of steps each recipe has and the number of reviews each recipe has. In the plot below, as the trendline shows, we can see that as the number of steps a recipe takes increases, the number of reviews it has also increases, suggesting a positive correlation between number of steps and number of reviews. Later, we can see if this relationship affects our predictions of number of reviews at all.

<iframe
  src="assets/bivariate_data.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates
The grouped table below shows the total number of interactions each year's recipes received, separated by the rating the interactions gave the recipes. This data shows us an insight into the rating distribution, possible recipe quality, and engagement patterns. For example, we can see that the total number of interactions where recipes were rated a 5 has generally decreased over time, from 173,244 total interactions rating recipes a 5 in the year 2008, to 94,147 in the year 2018. We can also see that the total number of interactions that rated recipes a 1 has generally increased over time, with 891 total interactions rating a recipe a 1 in 2008, to 10,222 interactions in 2018. These trends may show the difference in how people engage with the recipes(changing from possibly choosing to leave ratings if they particularly loved a recipe in 2008, to leaving a rating if they hated a recipe by 2018), or show how the quality of recipes has gone down over the years.

<iframe
  src="assets/grouped_table.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


## Assessment of Missingness
When investigating NaN values in our dataframe, we discovered that the columns `'description'`, `'rating'`, and `'review'` contained a significant amount of null values.

### NMAR Analysis

We believe that the missingness in the `'review'` column is NMAR because whether a review is left is the user's choice. They may leave a rating but not a review because they did not feel strongly enough about a recipe to warrant a full blown review, or perhaps they just prefer not to leave review in general. In order to confirm that this missingness is indeed Not Missing At Random (NMAR) and not Missing At Random (MAR) though, we would need data on either how the user felt about the recipe, which could be indicated by rating, or their general preferences when it comes to leaving reviews. So, we may want to obtain a numerical rating and a boolean value about their usual review preferences that could explain NMAR missingness for the `'review'` column.

### Missingness Dependency

Unlike `'review'`, we believe that the missingness in the `'description'` column could potentially be MAR (Missing At Random) and dependent on what year the recipe was submitted to [food.com](https://www.food.com/). In order to investigate this, we created a new column `'year'` containing only the year each recipe was submitted. We then ran a permutation test to examine the missingness dependency of `'description'` on `'year'`.

**Null Hypothesis:** The missingness of recipe descriptions does not depend on the year the recipe was submitted.

**Alternative Hypothesis:** The missingness of recipe descriptions does depend on the year the recipe was submitted.

**Test Statistic:** TVD(Total Variation Distiance) between the observed missingness distribution for each year and the distribution obtained after shuffling the `'year'` column.

**Significance Level:** 0.05

**Permutation Test Conclusion:** We obtained a p-value of 0.4529. Since this is significantly greater than our significance level of 0.05, we fail to reject the null hypothesis. We do not have sufficient evidence to suggest that the missingness of recipe descriptions depends on the year the recipe was submitted.

A plot showing the year of recipe submission by missingness of description is shown below.

<iframe
  src="assets/missingness_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing

**Null Hypothesis (H₀):** Tagging a recipe as easy does not significantly affect the number of reviews that the recipe receives.

**Alternative Hypothesis (H₁):** Tagging a recipe as easy significantly increases the number of reviews that the recipe receives.

**Test Statistics:** We will use the difference of means in number of reviews between recipes that were tagged as easy and those that were not.

**Significance Level:** 0.05

### Running a Permutation Test

We chose a permutation test because we are trying to determine whether tagging a recipe as easy and not tagging a recipe as easy come from different population distributions or data generating processes. Our test statistic needs to measure how different two numerical distributions are, which is why we chose to use the difference of means.

**p-value:** 0.0

### Permutation Test Conclusion

Since our p-value of 0.0 is less than our significance level of 0.05, we reject our null hypothesis. We have sufficient evidence to suggest that tagging a recipe as easy significantly increases the number of reviews that the recipe receives.

A plot showing the impact of recipes being tagged as easy on the number of interactions is shown below.

<iframe
  src="assets/permutation.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Framing a Prediction Problem

**Prediction Problem:** Can we predict the number of reviews a recipe has based on how complex the recipe is?

We chose a regression problem, with our response variable being the `'n_interactions'` column. We selected this variable because we aim to predict the number of interactions each recipe receives based on other features that might indicate the complexity of the recipe. We also chose R<sup>2</sup> as our metric for evaluation. We preferred R<sup>2</sup> over MSE because MSE gives us a measure of the magnitude of error between the predicted and actual values. However, MSE would not allow for a direct comparison between different models, meaning we would be unable to evaluate whether a certain model is better at predicting the number of reviews a recipe has. On the other hand, R<sup>2</sup> tells us how well the model's predictions fit the data and provides a more intuitive measure of how much of the variability in `'n_interactions'` is explained by the model.

We first created a new dataframe `filtered_df` containing only the columns we need for the remainder of this report.

We believe that each of the following columns either contributes or indicates the complexity of a recipe:

- `'minutes'` - The longer a recipe takes to complete, the more complex it may be
- `'n_steps'` - The more steps a recipe takes, the more complex it may be
- `'n_ingredients'` - Recipes that require more ingredients can be considered more complex
- `'is_easy'` - Recipes that contain the 'easy\|beginner' tag may be less complex than recipes without this tag
- `'is_kid_friendly'` - Recipes that contain the 'kid-friendly' tag may be less complex than recipes without this tag

We also kept the `'n_interactions'` column as it is what we are trying to predict.


## Baseline Model

Our baseline model uses a Random Forest Regressor, which reduces the risk of overfitting by aggregating the results of multiple decision trees. The subset of features we chose from our `filtered_df` dataframe are: `'minutes'`, `'n_steps'`, and `'n_ingredients'`, which are all discrete quantitative features. We did not need to perform any encodings because all of our features are quantitative, and can be directly handled by decision trees. 

Our model has an R<sup>2</sup> value of 0.8343, meaning around 83.43% of the variance in `'n_interactions'` can be explained by the model. We believe this shows our current model is "good" as the R<sup>2</sup> value is quite high and suggests that our model can capture the significant patterns in the data.

## Final Model

The features we added are `'is_kid_friendly'` and `'is_easy'`, both nominal features containing boolean values(True/False). These features were introduced because we believe they capture specific aspects of a recipe that influence its appeal and thus, the likelihood of receiving more ratings. Since our prediction task focuses on predicting the number of reviews a recipe receives, we believe the addition of the `'is_kid_friendly'` and `'is_easy'` columns will improve predictions by considering how the suitability of recipes for different demographics (such as beginner cooks or families) may influence their rating count.

The modeling algorithm we chose, like our baseline model, is a Random Forest Regressor. The hyperparameters that worked the best were `'minutes'`, `'n_steps'`, `'n_ingredients'`, `'is_kid_friendly'`, and `'is_easy'`. We decided to use Random Forest because of its ability to handle non-linear relationships, versatility, and effectiveness in reducing the risk of overfitting. We used grid search and cross-validation to select our hyperparameters. We experimented with the number of trees and the maximum depths of the trees, as well as employed k-fold cross validation, before ultimately deciding on 5 folds for fitting. 

Our final model has an R<sup>2</sup> value of 0.8945, which is an improvement from our baseline model's R<sup>2</sup> value of 0.8343. This improvement stems from better feature selection, hyperparameter optimization, and reduction of overfitting.


## Fairness Analysis

**Group X:** Recipes from earlier years (before 2011)

**Group Y:** Recipes from later years (after 2011)

We chose the year 2011 as our threshold because it was the median year of the data retrieved from the `'date'` column in the cleaned dataframe. 

**Evaluation Metric:** R<sup>2</sup>

**Null Hypothesis:** The regressor's accuracy is the same for both short and long recipes, and any differences observed are due to random chance.

**Alternative Hypothesis:** The regressor's accuracy is higher for long recipes.

**Test Statistic:** Absolute difference in R<sup>2</sup> values

**Significance Level:** 0.05

**Resulting P-Value:** 0.00

### Permutation Test Conclusion
Since our p-value is 0.00, we reject our null hypothesis. There is sufficient evidence to suggest that the regressor's accuracy is higher for long recipes. Precision parity is important here because false positives may lead to wasted time developing longer recipes when people are actually looking for shorter and simpler recipes.

A plot showing the distribution of the difference in precision accuracy for long and short recipes is shown below.

<iframe
  src="assets/fairness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
