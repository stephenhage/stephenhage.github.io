---
layout: default
title: "Forecasting My Stocks"
date: 2018-02-27 11:21:50 -0600
categories: content
tags: forecasting stocks arima holt-winters statistics R
---


# Not a Day Trader

I like paying attention to the market and making my best guesses as to which stocks will grow. At the same time, I know it's a fool's errand for someone like me to trade everyday. So how can I use my data science skills to forecast my portfolio value? And how can I keep from going insane tracking the ups and downs of each stock?

### Some Guidelines
I'm not savvy enough to use P/E ratios and cash balances to short stocks or anything like that. In my simplified world, I buy stocks in companies whose products/services I enjoy using, and then I hold them for a really long time. That is, unless I have an inkling the stock is at a local maximum, at which point I unload hoping I've sold high.

This project is to develop forecasts for the stock's high close value in the next month. That way, if the actual close exceeds it, either I need to sell or re-evaluate the model. These are the individual stocks I own right now:

## Amazon

Amazon is on a real tear lately with exponential growth basically since September 2017. I wouldn't have expected their close price distribution to be normal, but it's not terribly far off.

![Distribution of Closing Prices](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/AMZNdistribution.jpg?raw=true)

Trying to analyze the prices by day is too noisy, and that isn't the framework I'm using anyway. So I'm going to group the close values by week and forecast out the next five weeks' highs. Where possible, I'll use the upper 80% confidence interval so that if the price exceeds even that, I'll sell. So for Amazon, if the stock hits $1648 in the next week, I'll unload it.

![Forecast Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/AMZNforecasts.jpg?raw=true)

I replicated the same process for the rest of these stocks:

## Facebook

![Facebook Distribution Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/FBdistribution.jpg?raw=true)
![Facebook Forecast Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/fbforecasts.jpg?raw=true)

## Alphabet
![Alphabet Distribution Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/GOOGLdistribution.jpg?raw=true)
![Alphabet Forecast Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/GOOGLforecasts.jpg?raw=true)

## Microsoft
![Microsoft Distribution Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/MSFTdistribution.jpg?raw=true)
![Microsoft Forecast Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/MSFTforecasts.jpg?raw=true)

## Tesla

![Tesla Distribution Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/TSLAdistribution.jpg?raw=true)
![Tesla Forecast Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/TSLAforecasts.jpg?raw=true)

## Walmart

![Walmart Distribution Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/WMTdistribution.jpg?raw=true)
![Walmart Forecast Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/WMTforecasts.jpg?raw=true)

## Okta

![Okta Distribution Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/OKTAdistribution.jpg?raw=true)
![Okta Forecast Charts](https://github.com/stephenhage/stephenhage.github.io/blob/master/images/stockfc/OKTAforecasts.jpg?raw=true)
