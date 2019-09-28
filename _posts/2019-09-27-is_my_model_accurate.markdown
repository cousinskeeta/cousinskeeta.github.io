---
layout: post
title:      "Is My Model Accurate?"
date:       2019-09-28 03:44:48 +0000
permalink:  is_my_model_accurate
---

In this post, I will explain how I selected features in a pandas dataframe and will discuss the methods I used in my first project.  This project focused on homeowners looking to sell, and it was up to me to find the best feature that would yield higher prices. I wanted to use a multi regression model.  I had to figure out which model was the most accurate. 

In my initial EDA, I was eager to see the data stored in the King County Dataset. The dataset contained over 20,000 data points. I analyzed each feature's distribution, checked for colinearity between the prediction target, and checked the features against eachother for multicolinearity. I also blogged about Visualizing Pandas DataFrame Lat/Long with Folium Maps in my last post, but I will focus on feature selection and building multi regression models in this post. 

I identifed features that showed a visually normal distribuion, colinearity with the target, and homoscedastic (for OLS). After running an OLS model on the raw data, I started wrangling/munging the data. I converted categorical features, I stardardized and normalized the continous and engineered features (for RFE). I also used the train/test split model with the best features.

I learned that we can measure the success of our models by measuring the coefficient of determination (R_squared) for both OLS and RFE, and also by measuring the mean squared error (MSE) for the Train/Test Split model. R_squared is a relative measure of fit, whereas values closer to 100% are better. RMSE, is the square root of your MSE. RMSE is an absolute measure of fit, whereas lower values indicate a better fit.

I hope this learning lesson can help future data scientist learn something that took me an embarrasing amount of time to understand. I discussed how I selected features in a pandas dataframe and also discussed the methods I used in my first project.  If you're working on multi-regression model, be sure to check your R_squared, MSE, or RMSE for model accuracy. 

