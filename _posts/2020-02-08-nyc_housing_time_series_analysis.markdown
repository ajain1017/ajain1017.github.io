---
layout: post
title:      "NYC Housing Time Series Analysis"
date:       2020-02-08 19:45:18 -0500
permalink:  nyc_housing_time_series_analysis
---


## Introduction

In this project, we were asked to find the five best zip codes to invest in out of a massive dataset given to us from data off of Zillow, an online real-estate database company. My first instinct was to limit the dataset to NYC, where I was born and raised. More interesting for me was to see the performance of each borough within NYC in its past and where in NYC would be the best to invest in.

## Zip Code Selection

To begin with my zip code selection, I checked for the coeifficient of variance as the customer using this data that I imagined was risk averse. Therefore, I would want to limit the extent of variability in relation to the mean of our data. That reduced my 114 zip codes in NYC to 68 zip codes I would be working with.

## Time Series Analysis

Once I had my dataset ready, I was able to look more closely at what I was working with. There were 12 zip codes from the Bronx, 5 zip codes from Brooklyn, 3 zip codes from Manhattan, 36 zip codes from Queens and 12 zip codes from Staten Island.

I melted each borough into its own time series where we took the mean of each house value. To do so, I used the fucntion below:

```
def melt_data(df):
    melted = pd.melt(df, id_vars=['RegionID', 'Zip', 'City', 'State', 'Metro', 'CountyName', 'SizeRank', 'std', 'mean', 'cv'],         var_name='time')
    melted['time'] = pd.to_datetime(melted['time'], infer_datetime_format=True)
    melted = melted.dropna(subset=['value'])
    return melted.groupby('time').aggregate({'value':'mean'})
```

I find that the Mean Home Value in Manhattan has been decreasing, whereas the other boroughs have been increasing over time. Return on investment has been decreasing for both Manhattan and Brooklyn in the recent past, while the other three boroughs see increase in return. However, all boroughs still achieve positive return. There is a noticeable increase in mean home value in 2004, but that is due to the increase in zip codes in NYC, specifically Manhattan. Other observations was that for Manhattan and Brooklyn, as time passed, the gap between mean value and median value increased. This represents the increase in luxury homes in those boroughs, therefore pushing the mean higher as time moves on.

Since the most recent reccession had a large influence on the 2007-2009 housing values, along with the increase in zip codes from 2004, I decided to run my model over the past ten years of data from 2008-2018. This also influences my choice of running 2-year and 5-year projections as my customer is looking to hold a long position, and we do not have enough data to put together a strong 10 year projection.

After performing a Dickey-Fuller test on each borough's mean time series, I found that none of the time series were stationary. After time-decomposition, there is evidence of seasonality, yet to be sure, I decided to use a SARIMA model for predicting future returns on investment.

## SARIMA Modeling
To perform the modelling, I used pmdarima's and pyramid's auto_arima, ndiffs and nsdiffs functions. I first have an orders function, which takes in three arguments, the dataset, the differencing value and seasonal differencing value. To find d and D, I used a KPSS and CH test, then inputed those values into my orders function. Once that was set, a loop was run to fit the model, and project our investments.

```
def orders(ts, d, D):
    stepwise_model = auto_arima(ts, start_p=1, start_q=1,
                           max_p=4, max_q=4, m=12,
                           start_P=0, stationary = True, seasonal=True,
                           d=d, D=D, trace=False,
                           error_action='ignore',  
                           suppress_warnings=True, 
                           stepwise=True)
   
    return stepwise_model.order, stepwise_model.seasonal_order
		
def fit_sarimax_model(ts, order, seasonal_order, summary=True, plot=True):   
  
    # fit model
    model = sm.tsa.statespace.SARIMAX(ts,
                                    order=order,
                                    seasonal_order=seasonal_order,
                                    trend='ct',
                                    enforce_stationarity=True,
                                    enforce_invertibility=False)
    output = model.fit()
    
    print(output.summary())
    output.plot_diagnostics(figsize=(10, 12))
    plt.show()
    return output
```

```
# estimate number of differences using Kwiatkowski–Phillips–Schmidt–Shin test	   
n_kpss = ndiffs(dic_of_ts_roi5[x], alpha=0.05, test='kpss')
# estimate number of seasonal differences using Canova and Hansen test
D = nsdiffs(dic_of_ts_roi5[x], m=12, max_D=12, test='ch')
```

## Interpretating Results
Once I had my dataframe consisiting of RMSE, last forecasted value, maximum and minimum forecasted value based off the confidence interval, and variability (percent of size of confidence interval over max value), I sorted the values to fit my customer's investment strategy. Remember, he/she was risk averse, therefore, I would like to take the best predicitng model with the smallest confidence interval. I first use my variability term calculated by taking 1 - (the difference of size of confidence interval divided by the maximum value in the confidence interval). Afterwards, I take only the models with RMSE values less than the mean. Lastly, I take the 5 highest forecasted returns on investment for both 2-year and 5-year investments.

Five Best Zip Codes for a 2-year Investment:

| Zip	 | RMSE_2| Forecast_2 | Variability_2 |
| -------- | -------- | -------- | -------- |
|10456|	3.038941|	48.146268	|	0.803209|
|10305	|1.022362|	35.466762	|	0.553846|
|10304	|1.421296	|32.133567	|	0.668394|
|10307	|1.785705	|31.045801	|0.611774|
|10314	|2.797841	|30.458947	|0.671863|


Five Best Zip Codes for a 5-year Investment:

| Zip	 | RMSE_5 | Forecast_5 | Variability_5 |
| -------- | -------- | -------- | -------- |
| 10303  |3.993304	|108.442486|	0.212376|
| 11364 | 4.832035	|56.984150	|0.149975|
| 11358 | 8.446108	|55.433156|	0.276048|
| 10309 | 2.886740	|53.383004	|0.281765|
| 10305 | 5.480618	|51.571522	|	0.292127|

In conclusion, I found that Richmond County of Staten Island, Flushing, Queens, and the Bronx showed the most reliable 2-year and 5-year investment choices in NYC. Considering that our variability and RMSE are much better for the 2-year investment, I would suggest looking for a shorter investment period than 5-years.

-Anu







