---
layout: post
title:      "King County DataSet Regression Analysis: My First Project"
date:       2019-09-28 20:11:09 -0400
permalink:  king_county_dataset_regression_analysis_my_first_project
---


Two weeks and a crazy number of sleepless nights later, I finally made a multiple linear regression model using python, pandas and numerous other packages I've had almost no experience using. AND, at the end of the day, my model might not even be the best and there are definitely things I could have done differently. However, I will get into what I did.

In this project, I used King County Housing Data with the goal of creating a price estimating model for home owners interested in selling their homes.

My process was derived from the Data Science Life Cycle including:

#### Business Understanding
* Clear understanding of problem domain and questions needed to be answered

#### Data Cleaning
* Cleaning and wrangling data
* Checklist: Up-to-date data, missing values, duplicates, outliers, valid labels

#### Data Exploration
* Basic statistical findings: highlight patterns, relations, variance and bias of data

#### Feature Engineering
* The process of using expert knowledge to transform raw data into meaningful features that directly address the problem you are trying to solve

#### Predictive Modelling
* Use data analysis methods to answer question

#### Data Visualization
* Summarize and present the findings

With Business Understanding, I thought of three aspects that would be important to me when trying to sell my house. The first was, when during the year should I be selling my house. I therefore explored on what day and in which season were the houses sold the most. I found that the Spring and during the beginnig of the week was the best time to sell. To my thinking, most people have the most time during the Summer to move in, and also look for places to be during their downtime in good weather so it made most sense to me that Spring would be the highest selling time followed right behind by the Summer. What was confusing to me was that houses were bought on Tuesday the most followed by the other two days around it, Monday and Wednesday. I originally thought that the weekend is when most people have the most time, but then thinking over it again, realtors and bankers are in office Mondays through Friday, so maybe thats when the sales get processed the most.

With Data Cleaning, I wanted to eliminate null values, placeholder values and duplicates early. I therefore went right to business dropping duplicates in ID, and filling in null values and placeholder approriately (filling in nulls with the most common value, which turned out to be the median, and calculating placeholder values for sqft_basement using square footage of total living space minus square footage of living space without basement). I also took out outliers for some of the categorical data features as mean for those features was far below the max.

With Data Exploration, I ran simple multicollinearity tests and dropped any features that had correlation with other features greater than 0.75. I also found that changing some of the features to different data types would make the most sense, i.e. changing year renovated to simply whether it were renovated or not as most houses weren't renovated.

Feature Engineering was where I got most creative with the advice of others around me. A very close friend of mine noticed that zipcode and year built had a lot of different unique values, and location and how old the house was could strongly affect price. Therefore, I first binned them into groups and then created dummy variables for the resulting bins. I also one hot encoded the rest of the categorical data, and log normalized and scaled my continuous variables including my target variable. I wanted my model to have features with strong features with normal distribution, linearity and heteroskedasticity.

With Predictive Modelling, I split my data using a train test split with test size 33% of the data and then used ols statsmodel to make a multiple linear regression. Before doing so, I used a forward-backward selector algorithm found on https://datascience.stackexchange.com/questions/937/does-scikit-learn-have-forward-selection-stepwise-regression-algorithm. With this, I used the best features with p-values acceptable at a 5% significance level. Then we made fit our model over the train set and predicted over the test set.

For Data Visualization, I printed a summary of the error statistic I found best to display and plotted my predicted price estimates against the actual prices. The best summary statistics I found useful were R-Squared, Mean Squared Error, Mean Absolute Error and Root Mean Squared Error. R-sqaured is the most important as it shows what percentage of variability is explained by your model. Having one closer to 100% deems a better model. After all my work, my model unfortunately had an R-Sqaured of 76% and had an average distance from actual price of $15,295 which is definitely not the best.

My imporvements would include better feature selection and better data cleaning as I'm sure there were other data values that could have been ommitted in my model to minimize error. All in all, it is my first project and knowing I have 9 more months to improve makes this whole process worth it. Best to all of you and thank you for reading all that's been roaming around my mind the past couple of days.

-Anu


