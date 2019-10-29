---
layout: post
title:      "Graphing Candlesticks Using Plotly"
date:       2019-10-28 23:00:38 -0400
permalink:  graphing_candlesticks_using_plotly
---


In this post, I will share a tutorial on how to graph candlesticks for financial data like FOREX, Stocks or Crypto Currency charts. 

One of my hobbies is trading, so I spend a lot of time going back and forth between charts doing technical analysis. Here are a few references if you would like to repeat the steps I took.

* I used one of Plotly's Graph Objects called candlestick traces, here is a link to the documenttion: https://plot.ly/python/reference/#candlestick
* I also downloaded the BTC-USD dataset from Yahoo Finance here: https://finance.yahoo.com/quote/BTC-USD/history?p=BTC-USD

Here are the steps I took:

1. Downloaded the BTC-USD dataset and added the file to my working repository
2. Created a new jupyter notebook and imported the following libraries: 
```
import pandas as pd
import plotly.graph_objects as go
import datetime
```
3. Loaded the data set using pandas:
```
BTC_USD = pd.read_csv('BTC-USD.csv',parse_dates=['Date'])
```
4. Sorted the dataFrame by Date:
```
BTC_USD = BTC_USD.sort_values(by='Date')
```
5. Set the 'Date' as the DataFrame index:
```
BTC_USD.set_index('Date',inplace=True)
```
6. Created a chart figure:
```
chart = go.Figure(data=[go.Candlestick(x=BTC_USD.index,
                                       open=BTC_USD['Open'],
                                       high=BTC_USD['High'],
                                       low=BTC_USD['Low'],
                                       close=BTC_USD['Close'])])
chart.show()
```

Here is the entire code if you would like to repurpose and use:

```
# Importing Libraries
import pandas as pd
import plotly.graph_objects as go
import datetime

# Using pandas to load .csv data
BTC_USD = pd.read_csv('BTC-USD.csv',parse_dates=['Date'])

# Sorting data by Date
BTC_USD = BTC_USD.sort_values(by='Date')

# Setting Date as the index
BTC_USD.set_index('Date',inplace=True)

# Creating chart figure with Open, High, Low, and Close prices
chart = go.Figure(data=[go.Candlestick(x=BTC_USD.index,
                                       open=BTC_USD['Open'],
                                       high=BTC_USD['High'],
                                       low=BTC_USD['Low'],
                                       close=BTC_USD['Close'])])
chart.show()
```

This is pretty simple, but will require you to download an updated dataset each time, so maybe in the future, I will post a boilerplate web scrapping code that will pull data from the web to create a local dataFrame to plot updated candlesticks without the need for multiple downloads, so let me know if you would be interested by leaving a comment below.




