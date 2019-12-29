---
layout: post
title:      "Quick Tips for Descriptive Statistics and Visualizations"
date:       2019-12-29 01:35:31 +0000
permalink:  quick_tips_for_descriptive_statistics_and_visualizations
---


## Overview
In this blog post, I would like to share a few tips that helped me out in my last project. These tips are for beginner level python users, seeking to learn more tips and magic tricks with the pandas and seaborn libraries. I will cover tips about a importing libraries, and viewing descriptive statistics and creating visualizations.

Always remember to import the libraries for EDA. Dont' forget the inline magic for matplotlib.pyplot. I usually set the style to seaborn as well. Feel free to copy:

```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
plt.style.use('seaborn')

```

## Descriptive Statistics

### A.1) `.info()` and `.describe()`

All numeric columns in a pandas dataframe will have descriptive statisics, which can be beneficial in exploratory data analysis. If you are just starting out, you can use built in methods for pandas dataframes.

These methods are `.info()` and `.describe()`. 

* `.info` - returns the number of columns datatype for each column in the DataFrame.
*  `.describe` - returns table of numeric columns with descriptive statistics, including: count, mean, median, min, max, standard deviation, and quantiles.

#### A.2) Syntax  
<`pd.DataFrame()`>.info()
<`pd.DataFrame()`>.describe()

### B.1) `.unique()`, `nunique()`, and `value_counts()`

While the first two methods are specific to the entire dataframe, three methods were built to examine data stored as Series in Dataframe columns. 

These methods are `.unique()`, `nunique()`, and `value_counts()`. 

* `unique()` - returns numpy array of all unique values within the column/series
* `nunique()` - returns count of unique values 
* `value_counts()` - returns counts for each unique value within the column/series

#### B.2) Syntax  
<`pd.DataFrame()`>[<`column_name`>].unique()
<`pd.DataFrame()`>[<`column_name`>].nunique()
<`pd.DataFrame()`>[<`column_name`>].value_counts()

## Visualizing Datasets
Here are three tips in visualzing your data:

### 1.a) Use Histograms

Histograms are built into pandas using the matplotlib.pyplot library. Histograms can check the distribution of the data, and a easy way to check skewness and/or normality. This is basic, but the tip is in resizing the histogram. This tip is to add a `figsize = (height, width)` parameter in the `.hist()` method.

#### 1.b) Syntax
<`pd.DataFrame()`>[<`column_name`>].hist(`figsize = (20,18)`)

### 2.a) Use Distplots

Distplots from Seaborn are used to check distribution. These plots combine a KDE plot atop a histogram. I usually use a for loop to do this analysis. If I know some columns contain categorical data, I can use the max number of categories to only plot charts for columms with more datapoint than the categorical features. I usuall set an empty list to store all feature values that meet this criteria as well. 

#### 2.b) Example `FOR` Loop and `Distplot`

```
feat = []
for i in df.columns:
    if df[i].nunique() > 2:
        feat.append(i)
        sns.distplot(df[i])
        plt.title(i)
        plt.show()
```

As you can see I used the nunique() method here to exclude visualizations of the binary data/catagories. Feel free to use the same loop below to visualize distributions using a Distribution Plot. 
				

### 3.a) Use Jointplots

Jointplots from Seaborn are used to check for linearity amongst features and target variables. These plots combine a KDE plot for the feature and the target variable, as well as creating a scatter plot with a regression line to check for linearity. This could sound complex at first, but once you start modeling you will find this very helpful. 

#### 3.b) Example `FOR` Loop and `Jointplot`

```
for i in feat:
    sns.jointplot(x=i, y=`<target_variable>`, data=df, kind='reg')
    plt.show();
```

I used a similar loop above to visualize and check for linearity amongst features and the target varialbe. I used the features from the first loop to fill my empty list, and check the linearith for each one of those features. 

## Summary

So that's it. I shared some tips when importing your libraries for the inline magic. I covered some tips on pandas dataframes and series using built in methods, including how to resize histograms using the keyword arguments. I finished off this post with more visualization tips working with seaborn distplots and jointplots. If you find this useful, please let me know. Feel free to comment, share, or reach out to me if you have any questions about importing libraries, and viewing descriptive statistics and creating visualizations.


