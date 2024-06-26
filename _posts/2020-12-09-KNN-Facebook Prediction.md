---
layout:     post
title:      KNN-Facebook Prediction
subtitle:   Life is Short, I Use Python
date:       2020-12-09
author:     JieKun Liu
header-img: img/the-first.png
catalog:   true
tags:
    - Machine Learning
---




# Prerequisite
import pandas as pd

# 1. Obtain Data
data = pd.read_csv("./train.csv")

data.head()

# 2.Basic data processing
## 1）Reduce the scope of the data
data = data.query("x < 2.5 & x > 2 & y < 1.5 & y > 1.0")

data

# 2) Processing time characteristics
time_value = pd.to_datetime(data["time"],unit="s")

date = pd.DatetimeIndex(time_value)

data["day"] = date.day

data["weekday"] = date.weekday

data["hour"] = date.hour

data.head()

# 3) Filter locations with few check-ins
place_count = data.groupby("place_id").count()["row_id"]

place_count[place_count > 3].head()

data["place_id"].isin(place_count[place_count > 3].head().index.values)

data_final = data[data["place_id"].isin(place_count[place_count > 3].head().index.values)]

data_final.head()

# Filter characteristic values ​​and target values
x = data_final[["x","y","accuracy","day","weekday","hour"]]
y = data_final["place_id"]

x.head()

y.head()

# Data set division
from sklearn.model_selection import train_test_split

x_train,x_test,y_train,y_test = train_test_split(x,y)

from sklearn.preprocessing import StandardScaler

from sklearn.neighbors import KNeighborsClassifier

from sklearn.model_selection import GridSearchCV

# 3) Feature Engineering: Standardization

transfer = StandardScaler()

x_train = transfer.fit_transform(x_train)

x_test = transfer.transform(x_test)

# 4) KNN algorithm predictor

estimator = KNeighborsClassifier()

# Parameter preparation

param_dict = {"n_neighbors":[3,5,7,9]}

# Join grid search and cross validation

estimator = GridSearchCV(estimator,param_grid=param_dict,cv=3)

estimator.fit(x_train,y_train)

# 5) Model evaluation

## Method1：Directly compare the true value and the predicted value

y_predict = estimator.predict(x_test)

print("y_predict\n",y_predict)

print("Directly compare the true value and the predicted value\n",y_test==y_predict)

## Method2：Calculation accuracy

score = estimator.score(x_test,y_test)

print("Accuracy rate is:",score)

print("Best Parameter is：\n",estimator.best_params_)

print("Best result is：\n",estimator.best_score_)

print("Best estimator is：\n",estimator.best_estimator_)

print("Cross validation result：\n",estimator.cv_results_)

