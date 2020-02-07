---
layout: post
title:      "Time-Series Modeling"
date:       2020-02-07 20:47:30 +0000
permalink:  time-series_modeling
---


## Introduction

For this project, I utilized my Time Series Analysis skills to forecast real estate prices of zipcodes using data from [Zillow](https://www.zillow.com/research/data/). 

## Objectives

The CEO of PoManns Capital Real Estate hired my consultancy to answer the following quesitons based on the Zillow Data data:

- <b>Question:</b> *What are the top 5 best zipcodes for PoManns Capital to invest in Houston, Texas?*

## The Project

Our project team used the OSEMN framework to help PoManns Capital  select the top 5 best zipcodes to invest in short to long term periods.

* **Obtaining** Data - We used data from Zillow with data from all over the United States
* **Scrubbing** Data - We broke down the data by country, then by state, then by city, then by zip code
* **Exploring** Data - We did a time-series analysis to see which zip codes had trends, seasonality, and other features that may influence price.
* **Modeling** Data - We used a method to select the best features in predicting price
* We trained the model on the entire dataset
* We did some forecasts to see which zip codes would yield better return on investments in the short to long term periods
* **Interpreting** Data - 
* Short Term - We looked at forecasts for the first year and the average Return on Investment.
* Mid Term - We looked at forecasts for the couple years and the average Return on Investment.
* Long Term - We looked at forecasts for three years and the average Return on Investment.


## The Results

The entire framework can be found in the `Houston_Zipcodes.ipynb` Notebook in this repository. Our results are below:

- <b>Question:</b> *What are the top 5 best zipcodes for PoManns Capital to invest in Houston, Texas?*

* 77095
* 77070
* 77036
* 77082
* 77084





