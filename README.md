### Project overview 
* [Kaggle](https://www.kaggle.com/c/house-prices-advanced-regression-techniques/overview/description) provides open datasets that can be used for practicing data science techniques. This readme contains modeling / R notes from the Ames home sale price prediction project. 
* Goal = predict the final price of a home. The dataset used for this project contains 79 explanatory variables describing (almost) every aspect of residential homes in Ames, Iowa. The [Ames Housing dataset](http://www.amstat.org/publications/jse/v19n3/decock.pdf) was compiled by Dean De Cock.  
* [R code on Github](https://github.com/bm-datalab/house_price_prediction). 

### Learnings

**1\. Version control makes tracking model iterations easier** 
* When iterating/testing different models/feature engineering designs it was useful to have revision history on which script versions performed best. In some cases, model performance would get worst and I’d revert back/pull from prior script versions which were working better. 
##### **2\. Linear model reminders: feature scaling and feature interpretability** 
* In R, simple lm() can handle categorical data inputs without one-hot encoding. 
* If using a simple linear model mainly for prediction, the model with scaled features performs the same as the model without scaled features. 
* For example, model 1 and model 2 have the same root mean square error: 
* model_1 <- lm(mpg ~ disp + hp, data=mtcars) * model_2 <- lm(mpg ~ scale(disp) + scale(hp), data=mtcars) 
* For models where coefficient interpretability is needed, it’s recommended to scale features to be able to compare features of different units. 
* After scaling features, correlated features can still distort coefficients which will make interpretation difficult. 

##### **3\. Feature cleaning notes** 
* Given the training dataset contained around 1.5k observations, I decided to recode NAs or fill in NAs using various imputation techniques vs excluding observations with missing data. 
* Tedious feature cleaning was needed to properly handle NAs. NAs could represent the feature value was not included in the dataset or the house did not have the feature attribute. For example, in the data dictionary Pool quality (PoolQC) equal to NA represents no pool. 
* 4 outlier observations were excluded from the training dataset. Compared to decision tree models, in general, linear regression models are more sensitive to outliers. Both linear regression models and decision tree models were used in the final ensemble. 
* I learned how to use the package Vtreat which was handy for working with the XGB model. 

##### **4\. XGB (Extreme Gradient Boosting)** 
* XGB is a popular model for data science competitions. 
* XGB is a faster form of standard gbm models. 
* Random forests models grow many trees in silos which are then aggregated together for a final prediction. Gbm models are different in that many weak trees are built to learn from each other vs operate in silos. For example, tree 1 creates predictions and an error baseline is established. Tree 2 weights the observations from tree 1 that had largest error. Tree 2 then tries to minimize error for those observations that had high error from tree 1\. This process is then completed for X specified rounds. 
* XGB requires a matrix of numeric values as input vs a data frame input which many other model packages use.  
* input_x <- as.matrix(train_df %>%  dplyr::select(-SalePrice_log)) 
* input_y <- train_df$SalePrice_log 

##### **6\. An ensemble of models was used for the final prediction** 
* The final model ensemble was a combination of Random Forest, XGB, Ridge Regression, and Lasso Regression. 
* On the public leaderboard, model results were in the top ~25% of teams.  
* It’s common in Kaggle competitions to use a collection of different models vs one single model. The tradeoff with this approach is the ensemble starts to feel like a black box and makes interpretability difficult.   

##### **7\. Using Log RMSE (Root Mean Square Error) to assess model performance** 
* For this project we used RMSE of the log predicted house sale price vs the log actual house price. Taking the logs helps to weight cheap houses and expensive houses equally. 
* Root Mean Square Error (RMSE) is the standard deviation of the difference between the actual value and the predicted value (residuals). 
* Calculating RMSE using base R: 1) Derive residuals 2) Square the residuals 3) Find the average of the residuals. 4) Take the square root of the result. 
* sqrt(mean((predicted - actual)^2)) 

### **Areas for further improvement** 
* More advanced methods could be used to fill in missing data in the train and test dataset. 
* More work could be done on deriving new features and model parameter tuning. 
* I scored many prediction submissions on the Kaggle test set. This approach is not recommended as it’s easy to overfit to the public leaderboard. Going forward, I’ll plan to use a validation dataset or implement cross-validation to assess performance outside the training data. 
* Need to continue to learn about the most common models, the model strengths & weaknesses, and the key model parameters.
