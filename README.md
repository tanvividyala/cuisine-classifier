# Cuisine Classifier!
By Tanvi Vidyala and Nithya Nair

## Step 5: Framing a Prediction Problem 
**Prediction Problem**: How can we use the following nutritional features to predict whether a recipe would be tagged as European or American using a Random Forest Classifier? 

`CALORIES`<br>
`TOTAL_FAT`<br>
`SUGAR`<br>
`SODIUM`<br>
`PROTEIN`<br>
`SATURATED_FAT`<br>
`CARBS`<br>
`INGREDIENTS`<br>

At the time of prediction, the nutrition information (calories, total fat, sodium, protein, saturated fat, and carbohydrates) as well as the list of ingredients in each recipe wil be known.

This problem will be **binary classification** since there are two possible classes we are predicting (American or European). Our prediction will be thE `LABEL` column. We'll be evaluating our model's success using **F1-score** because it balances both precision and recall and is robust to the size differences in our classes.  
