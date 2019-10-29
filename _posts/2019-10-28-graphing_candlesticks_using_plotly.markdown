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

This is pretty simple, but will require you to download an updated dataset each time, so wanted to share a boilerplate function to use on any Yahoo Finance url. The function uses some tricks we learned with web scrapping, so this function will only require the necessary libraries and a url to the historical data from Yahoo Finance.  This will allow the function to pull data from the web into a local dataFrame then plot the updated candlesticks without the need for multiple downloads. 

This is still raw at the time of writing, so let me know if this is helpful, or if you have any suggested improvements to the code. Here is the boilerplate:

```
# Importing Libraries
import bs4
import requests
import pandas as pd
import time
import random
import math
import sqlite3
import numpy as np


headers = {'User-agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.186 Safari/537.36'}
sleep_min = 0
sleep_max = 5


def get_chart_info(url):
    response = requests.get(url, headers=headers)
    soup = bs4.BeautifulSoup(response.content, 'html.parser')
    data = soup.find('table', attrs={"class":'W(100%) M(0)'})
    dataset = data.find_all('tr')
    data_list = []
    for i in dataset:
        info = i.find_all('span')
        data_list.append([j.text.strip() for j in info])
    df = pd.DataFrame(data_list, columns = data_list[0]).drop(index=[0, 101],axis=0)
    df = df.sort_values(by='Date')
    df.set_index('Date',inplace=True)
    chart = go.Figure(data=[go.Candlestick(x=df.index,
                                           open=df['Open'],
                                           high=df['High'],
                                           low=df['Low'],
                                           close=df['Close*'])])
    return chart.show()
		
```

Here are a few sample urls I used to test, and a for loop with to handle each request:

```
eth = 'https://finance.yahoo.com/quote/ETH-USD/history?p=ETH-USD'
btc = 'https://finance.yahoo.com/quote/BTC-USD/history?p=BTC-USD'
dow = 'https://finance.yahoo.com/quote/%5EDJI/history?p=%5EDJI'
amd = 'https://finance.yahoo.com/quote/AMD/history?p=AMD'


for url in [eth,btc,dow,amd]:
    print(url)
    get_chart_info(url)
    time.sleep(random.uniform(sleep_min, sleep_max))
```



