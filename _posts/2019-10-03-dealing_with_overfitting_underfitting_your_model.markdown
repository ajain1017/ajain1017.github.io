---
layout: post
title:      "Dealing with Overfitting, Underfitting your model"
date:       2019-10-03 16:45:10 -0400
permalink:  dealing_with_overfitting_underfitting_your_model
---


After spending mutliple hours on my first Flatiron School Multiple Linear Regression Model, I found one the biggest issue I had was when to use the train test split and the importance of it in the first place. Yes, I know, this doesn't seem too problematic and all of you reading this probably already know when to do so, but it is still something normal people might find hard to deal with.

Let us go over two simple concepts when covering a train-test split.

### Underfitting

Underfitting a statistical model is when the model cannot capture the underlying structure of the data being used. You may see this when some features or descriptive variables are not present in the model itself and thus, gives the model a poor finding. We may see that an underfitting model misses the trends found in the dataset for example, when fitting a linear model to a non-linear dataset.

### Overfitting

Now, the opposite of underfitting, would be overfitting the model. Overfitting, by definition, is "the production of an analysis that corresponds too closely or exactly to a particular set of data, and may therefore fail to fit additional data or predict future observations reliably" (Leinweber, D. J., "Stupid data miner tricks"). This overfitted model may have features that cannot be justified from the data and are too descriptive of the available data making it hard to predict from new data.

### Train-Test Split

To overcome the problems associated with underfitting and overfitting a model can be solved through examining a train-test split of your data. By splitting your data, we may control over-fitting and allow a higher degree of generalizability. You begin with cleaning and feature engineering your data, split the data, model using your trained data and apply the model to test and find out errors. To figure out when to use a train-test split, we may look at the image below.

![](https://image.slidesharecdn.com/mlregression-splittingdatasets-170914113926/95/machine-learning-splitting-datasets-3-638.jpg?cb=1505389251)

-Anu
