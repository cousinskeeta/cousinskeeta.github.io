---
layout: post
title:      "Multi-classification Model with Stock Dataset"
date:       2020-03-16 23:49:53 +0000
permalink:  multi-classification_model_with_stock_dataset
---

In this post, I will share how we found the best classification model to predict which stocks are overbought, oversold, or neigher. This is for readers that have a basic understanding of python and a few libraries like, Pandas, Requests bs4, and sklearn. I will share the steps I used to create a dataset from Yahoo Finance, and also how we find the best classification model using some sklearn tools in the process. I will use the OSEMN process to help an independent stock investor select top stocks as overbought or oversold.

First, will use python and two open source web scrapping libraries to obtain the stock data. We will then scrub the data by checking for missing values and duplicates. Afterwards, we will analyze the features and visualize the dataset. We will perform a train-test-split on the data; standardize the dataset, perform a Priciple Component Analysis on the features; build a baseline model; build additional classification models; and compare all models by testing the accuracy of each model's predicitons.
# Obtaining the Dataset
The first step in our process begins with obtaining the data we need. I used a combination of the request's library, beautifulsoup, and pandas to scrape data for each company. This process was broken up in three task. (1) Fundamental, (2) Indicator Data, (3) Combining Data.

The first task was to scrape fundamental data for each stock. This inculded most of the information available on each company's Yahoo Finance page, ie: current price, previous close price, open price, volume, market_cap, etc.

The second task was broken down into two sub-task. In the first sub-task, we looked at each companies historic time series data, like adjusted close, price change over time, and added features to include adjusted close, average gain, average loss. In the second sub tasked we looked at relative stregnth, relative stregnth index, and the target variable.

The last step was combining the datasets based on stock ticker names. This dataset combines fundamental and indicator data for the publicly trades stocks predicting if the stocks are currently overbought or oversold, or neither.

At the time of writing, I was able to obtain a stock dataset with 29 features and 1938 datapoints.

# Scrubbing the Dataset
Next up in our process, Scrubbing! Since I obtained this data from scrapping the web, I have to be sure to check the data for any missing values, and/or duplicate information. Once I identified missing data, I removed those companies from my dataset. If I found duplicates, I also removed them from the dataset, dropping about 30 duplicate data points.

# Exploring the Dataset 
Now that I have a sufficient dataset, we will begin step three in our process, Exploring! We can begin by analyzing the datatypes of the features in our dataset. We will then break out dataset down into two: (1) continuous features, and (2) categorical features. After accessing the features, we can visualize our dataset and feature distribution before modeling.

Now I am over halfway done in my process and ready to begin, Modeling! Before I build the model, I will separate the target variable from the features. By defining the features separately, I can assess correlation amongst the features using a heatmap. If we have high correlation, we will use principal component analysis to reduce the dimensionality of the data, decreasing feature correlation. I used a train test split on my features/target:

<code pyton>

from sklearn.model_selection import train_test_split
<br>
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=123)

</code>

I will use PCA to reduce the number of compenents, but first I must determine the best number of components that will explain the most variance in the features. This will help me decompose the dataset into essential featues to help with my predictions.

While I will not know which features are selected, the critical components will keep the most important values in a lower dimensional state. I decided that 10 components will give the best explaination of the variance in my features.

I used a StandardScaler() from sklearn to standardize features by removing the mean and scaling to unit variance. Centering and scaling happen independently on each feature by computing the relevant statistics on the samples in the training set. Mean and standard deviation are then stored to be used on later data using transform, and in my case, I used the `.fit_transform` method to do this all at once.

I'm standardizing the dataset to have normally distributed data, as many elements used in the objective function of learning algorithm like the RBF kernel of Support Vector Machines, assume that all features are centered around 0 and have variance in the same order. If a feature has a variance that is orders of magnitude larger that others, it might dominate the objective function and make the estimator unable to learn from other features correctly as expected.

# Selecting the Model

Now it's finally time to build the baseline model! Since I have a low to medium number of data points and many features, I built a baseline model using a Support Vector Machine. I also used Pipelines to help build our initial model.

<code python>

from sklearn.svm import SVC
from sklearn.pipeline import Pipeline

pipe_svc = Pipeline([('pca', PCA(n_components=10, random_state=123)), ('clf', SVC(random_state=123))])

pipe_svc.fit(X_train_scaled, y_train)

X_test_scaled = scalr.transform(X_test)

## Print Accuracy
print('Baseline SVM pipeline test accuracy:')

print(pipe_svc.score(X_test_scaled, y_test))
</code>

Now we will create various pipelines to compare models using the built in accuracy scores of Support Vector Machine, Random Forest Classifier, KNN Classifier, Decision Tree Classifier, SGDClassifier, AdaBoost Classifier, Voting Classifier, Stacking Classifiers, and MLPClassifier.

<code python>

from sklearn.pipeline import Pipeline<br>
from sklearn.svm import SVC<br>
from sklearn.neighbors import KNeighborsClassifier<br>
from sklearn.tree import DecisionTreeClassifier<br>
from sklearn.ensemble import RandomForestClassifier<br>
from sklearn.linear_model import SGDClassifier<br>
from sklearn.naive_bayes import MultinomialNB<br>
from sklearn.neural_network import MLPClassifier<br>
from sklearn.ensemble import AdaBoostClassifier, VotingClassifier, StackingClassifier<br>
<br>

pipe_svm = Pipeline([('pca', PCA(n_components=10)),
        ('clf', SVC(random_state=123))])
<br>        
pipe_rfc = Pipeline([('pca', PCA(n_components=10)),
        ('clf', RandomForestClassifier(random_state=123))])
<br>
pipe_tree = Pipeline([('pca', PCA(n_components=10)),
        ('clf', DecisionTreeClassifier(random_state=123))])
<br>
pipe_knc = Pipeline([('pca', PCA(n_components=10)),
        ('clf', KNeighborsClassifier())])
<br>
pipe_sgd = Pipeline([('pca', PCA(n_components=10)),
        ('clf', SGDClassifier(random_state=123))])
<br>
pipe_ada = Pipeline([('pca', PCA(n_components=10)),
        ('clf', AdaBoostClassifier(random_state=123))])
 <br>       
pipe_vote = Pipeline([('pca', PCA(n_components=10)),
        ('clf', VotingClassifier(estimators=[ ('svm', pipe_svm), ('rf', pipe_rfc), 
                                             ('dt', pipe_tree),('knc',pipe_knc),('sgd',pipe_sgd),
                                             ('ada',pipe_ada)]))])
<br>
pipe_stack = Pipeline([('pca', PCA(n_components=10)),
        ('clf', StackingClassifier(estimators=[ ('svm', pipe_svm), ('rf', pipe_rfc), 
                                             ('dt', pipe_tree),('knc',pipe_knc),('sgd',pipe_sgd),
                                               ('ada',pipe_ada)]))])
<br>
pipe_MLPC = Pipeline([('pca', PCA(n_components=10)),
        ('clf', MLPClassifier(random_state=123))])

<br>
pipelines = [pipe_svm, pipe_rfc, pipe_tree, pipe_knc, 
             pipe_sgd, pipe_ada, pipe_vote, pipe_stack, pipe_MLPC]
pipeline_names = ['Baseline SVM','Random Forest','Decision Tree',
                  'KNClassifier','SGD','AdaBoost','Voting','Stacking', 'MLPClassifier']

<br>

for pipe in pipelines:<br>
    print(pipe)<br>
    pipe.fit(X_train_scaled, y_train)<br>
<br>
scores_df = pd.DataFrame()
scores = []<br>
for index, val in enumerate(pipelines):<br>
    s = val.score(X_test_scaled, y_test)<br>
    scores.append(s)<br>
    print('%s pipeline test accuracy: %.3f' % (pipeline_names[index], val.score(X_test_scaled, y_test)))<br>
<br>
scores_df['Model'] = pipeline_names<br>
scores_df['Accuracy_Score'] = scores<br>
scores_df.sort_values(by='Accuracy_Score',ascending=False)<br>
<br>
</code>

The MLPClassifier performed the best in comparision to other models and the initial baseline model. The MLPClassifier is a Multi-layer Perceptron classifier. This model optimizes the log-loss function using LBFGS or stochastic gradient descent, and in my example we used the default solver 'adam', which works pretty well on relatively large datasets, but for small datasets, it's recommended to use, 'lbfgs', as this function can converge faster and perform better.

Multi-layer Perceptron (MLP) is a supervised learning algorithm that learns a function by training on a dataset. Given a set of features and a target, it can learn a non-linear function approximator for either classification or regression. It is different from logistic regression, in that between the input and the output layer, there can be one or more non-linear layers, called hidden layers. MLP also requires tuning a number of hyperparameters such as the number of hidden neurons, layers, and iterations.

Below I performed a grid search cross-validation to see if I could improve the model by tuning the hyperparameters.

<code>

from sklearn.model_selection import GridSearchCV

pipe_MLPC = Pipeline([('pca', PCA(n_components=10)),
        ('clf', MLPClassifier(random_state=123))])

param_grid_MLPC = [ 
  {'clf__hidden_layer_sizes': [(100,),(200,),(300,)],
   'clf__activation': ['identity', 'logistic', 'tanh', 'relu'],
   'clf__solver': ['lbfgs', 'sgd', 'adam'],
   'clf__alpha': [0.0001, 0.0002, 0.0003],
   'clf__learning_rate': ['constant', 'invscaling', 'adaptive']
  }
]


gs_MLPC = GridSearchCV(estimator=pipe_MLPC,
            param_grid=param_grid_MLPC,
            scoring='accuracy',
            cv=3, verbose=2, return_train_score = True)

gs_MLPC.fit(X_train_scaled, y_train)

## Best accuracy
print('Best accuracy: %.3f' % gs_MLPC.best_score_)

## Best params
print('\nBest params:\n', gs_MLPC.best_params_)
</code>

# Interpret 
The comparison of all models against our baseline model shows us which model performed the best. This is done by assessing each model's test accuracy. By combining grid search and cross-validataion to fine tune my model, I was able to accurately classify publicly traded stocks, based on ten principal component analysis instead of the original 29 in the dataset. My final tuned model had an accuracy score of 99.1%; where 38% of oversold stocks were correctly classified, and 60% of overbought stocks were also classified correctly. That gave me about 98% correctly classified based on the confusion matrix. I only had a 1.4% error rate of classification.

Here is the link to my repo: https://github.com/cousinskeeta

