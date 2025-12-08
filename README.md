# Cuisine Classifier 🍽️
By Tanvi Vidyala and Nithya Nair

## Introduction
To be Added

## Data Cleaning and Exploratory Data Analysis
To be Added

## Assessment of Missingness
To be Added

## Framing a Prediction Problem 🍲
**Prediction Problem**: How can we use the nutritional facts and ingredient lists to classify whether a recipe would be tagged as European or American? 

At the time of prediction, the nutrition information (calories, total fat, sodium, protein, saturated fat, and carbohydrates) as well as the list of ingredients in each recipe wil be known.

This problem will be **binary classification** since there are two possible classes we are predicting (American or European). Our prediction will be the `LABEL` column. We'll be evaluating our model's success using **both accuracy** and **F1-score**. Accuracy is appropriate because our classes are nearly balanced, while F1-score adds to it by accounting for precision and recall, giving a more nuanced view of model performance across both cuisine categories.

## Baseline Model 🥗
We used a **Random Forest Classifier** for this problem because it can handle different data types well and is robust to noise while capturing nonlinear patterns in the dataset. <br> 

Our baseline model used all 7 **quantitative** nutritional variables as predictors:
`CALORIES`<br>
`TOTAL_FAT`<br>
`SUGAR`<br>
`SODIUM`<br>
`PROTEIN`<br>
`SATURATED_FAT`<br>
`CARBS`<br>

As well as one **nominal** text feature containing lists of ingredients for each recipe:<br>
`INGREDIENTS`

The `INGREDIENTS` column was turned into a quantitative variable using the built-in `TfidfVectorizer`. We also encoded the `LABELS` target column using `LabelEncoder` to turn categorical labels (American and European) into integers. 

Our model returned a F-1 Score of **0.77** for the American class and an F-1 Score of **0.76** for the European class. The model accuracy was similar at **0.7602**. This indicated somewhat strong, balanced performance across both our categories. Since both two classes are nearly evenly distributed, and that the model performs consistently across them, we believe the model is “good” for a baseline.

## Final Model 🍕
Firstly, we wanted to assess which combination of our initial 7 quantitative features would result in an optimal accuracy rate. To assess this, we tested all 26 possible combinations of our features to understand which variables held the strongest signal for distinguishing American and European recipes.

#### Top 10 Feature Combinations

| Rank | Feature Combination                          | Accuracy     |
| ---- | -------------------------------------------- | ------------ |
| 1    | sugar, sodium, protein, saturated_fat, carbs | **0.763701** |
| 2    | protein, saturated_fat                       | 0.762875     |
| 3    | sodium, protein                              | 0.762324     |
| 4    | sugar, protein, carbs                        | 0.762049     |
| 5    | sugar, sodium, protein, carbs                | 0.761498     |
| 6    | sodium, protein, saturated_fat, carbs        | 0.761223     |
| 7    | saturated_fat, carbs                         | 0.761223     |
| 8    | protein, carbs                               | 0.760947     |
| 9    | sugar, protein, saturated_fat, carbs         | 0.760397     |
| 10   | sugar, sodium, protein, saturated_fat        | 0.760397     |

Across all subsets, we found that the combination of `SUGAR`, `SODIUM`, `PROTEIN`, `SATURATED_FAT`, and `CARBS` was the most effective combination of unmodified features. This lead us to drop `CALORIES` and `TOTAL_FAT`. `CALORIES` did not meaningfully distinguish the cuisines since it is an aggregate measure, and `TOTAL_FAT` is largely multicollinear with saturated fat, which already captures the more relevant nutritional signal.

Next, we used a 5-fold Grid Search to find the optimal hyperparameters for our Random Forest Classification model. We assessed various values for `max_depth`, `min_samples_split`, and `criterion`. 

```python
hyperparameters = {
    'max_depth': [2, 25, 50, 75, 100, 125, 150, 175, 200, None],
    'min_samples_split': [2, 5, 10, 20, 50, 100, 200],
    'criterion': ['gini', 'entropy']
}
```

The best hyperparameters returned by Grid Search was **Entropy** for `criterion`, **100** for `max-depth`, and **100** for `min_samples_split`.

Lastly, we engineered two new features `PROTEIN_DENSITY` and `FAT_PROTEIN_RATIO` to add to the model. 

`PROTEIN_DENSITY` highlights how much protein a recipe provides relative to its calories, capturing differences in macronutrient balance between cuisines that raw protein alone cannot.

`FAT_PROTEIN_RATIO` measures saturated fat relative to protein, reflecting contrasts in cooking styles—such as dairy-based fats in European dishes versus protein-heavy American recipes—and reveals patterns not visible in either nutrient alone.

In the end, the performance of the final model on the training data achieved a F-1 score of **0.78** for the American class and an F-1 Score of **0.77** for the European class on the test data. This was a slight improvement from the baseline which was 0.77 for the American class and 0.76 for the European class. The overall model accuracy improved as well increasing from 0.762 with the baseline model to **0.773**.

<iframe
  src="assets/confusion-matrix.html"
  width="1200"
  height="600"
  frameborder="0"
></iframe>

## Fairness Analysis 🍪
To assess the fairness of our final model, we decided to evaluate whether the recall score is significantly different across both cuisine groups, American and European. 

- **Group X**: Recipes classified as American
- **Group Y**: Recipes classified as European
- **Evaluation Metric**: Recall
- **Null**: Our model is fair. There is no significant difference in recall for the American and European cuisine classes, and any differences are due to random chance.
- **Alternative**: Our model is unfair. There is a significant difference in recall for the American and European cuisine classes.
- **Test Statistic**: Calculated Difference in Recall (~0.0256)
- **Significance Level**: α = 0.05
- **P-Value**: 1.0

Since our p-value of 1.0 is greater than the significance level of 0.05, we fail to reject the null hypothesis, and we did not find evidence to support that our model treats American and European recipes differently.

<iframe
  src="assets/fairness-analysis.html"
  width="1000"
  height="600"
  frameborder="0"
></iframe>
