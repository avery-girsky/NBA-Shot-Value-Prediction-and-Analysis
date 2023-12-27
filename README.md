# NBA Shot Prediction and Player Performance Analysis

## Overview
For a final project in a Machine Learning course, myself and three fellow students built a model to predict the value of an NBA shot based on location factors, action type (i.e. dunk, jump shot), and other game related information (i.e. time remaining, home/away, etc.). We notably left player name out of the modeling process, which surely affected model performance, but also gave us a lot of power in analyzing the model results. We were able to compare the model's expectation over all of a single player's shots against the actual results that player produced. The concept of points produced over prediction, or expectation, is extremely useful in analysis of a player's performance. Our results showed that players such as Nikola Jokic, Steph Curry, and Kevin Durant produce the most value on their shots in 2023, as any NBA fan would expect. However, we also noticed some players, including Luke Kennard, Bojan Bogdanovic, and Buddy Hield, that received little attention for their play in 2023, but according to our analysis had incredible scoring seasons.

## Data

To study our problem we used the following datasets:

1. Shot-Level Data (primary): Over 4M shots taken between 2004 and 2023.
2. Game Outcome Data: Includes final scores of each game in our time span, used to compute the clutch indicator.
3. Season-long Team Stats: Provides defensive rating of shot taker opponent.
4. NBA Abbreviations: Allows to connect teams between datasets.

After merging datasets and adding features, we used the following in our models.

**Feature: Description; Type**

- POSITION GROUP: Guard, forward, or center; object
- SHOT TYPE: 2-point shot or 3-point shot; object
- BASIC ZONE: Description of the area on the court; object
- LOC X: Horizontal component of court location; float
- LOC Y: Vertical component of court location; float
- SHOT DISTANCE: Distance from the hoop; integer
- ACTION GROUP: Description of how shot was attempted (i.e. dunk shot); object
- TIME LEFT QUARTER: Seconds remaining in the quarter; integer
- TIME LEFT GAME: Seconds remaining in the game; integer
- DEFENSIVE RATING: Opposing team season-long defensive rating; float
- BUZZER: Is shot taken in the last 5 seconds of a quarter; boolean
- CLUTCH: Is shot taken in the last 2 minutes of the game; boolean
- HOME: Is shot taker on the home team; boolean

**Outcome**

- SHOT OUTCOME: False if shot missed, True if shot made; boolean
- SHOT VALUE: 0 if shot missed, 2 if made two-pointer, and 3 if made three-pointer; integer

The data was one-hot encoded for modeling purposes.

## Code
The project includes four notebooks:
1. Data.ipynb - Load datasets, perform merge and feature engineering
2. Court.ipynb - Includes a function that draws a basketball court in matplotlib, used in visualizations
3. EDA.ipynb - Exploratory analysis, provides insights to the data through visualizations; motivates modeling process
4. Model and Results.ipynb - Runs many different models to predict the probability of a shot being made and/or the value of the shot. After selecting best model in terms of MSE, checks true player performance against the model's expectation.

The EDA and Model and Results notebooks require running the other two beforehand. The Data notebook requires the datasets provided in the Data.zip folder. This folder was uploaded with git lfs.

Libraries used include: Numpy, Pandas, Scikit-Learn, TensorFlow, Matplotlib, Seaborn.

Lastly, our final report is uploaded as well to provide a more extensive, results-oriented breakdown of the project.

## Predictive Modeling
For the models we constructed, to conserve run time we trained on the data from 2017 to 2022 and used the 2023 season to analyze players. Cross validation was performed within the training data for each type of model to determine hyperparameters, then the best of each were compared with prediction on 2023 data.

We built the following models with the goal of minimizing mean squared error of the probability of a shot being made. We used regression models with the outcome `SHOT_MADE` which is either 0 or 1. Any classification results in the report use the probability predictions at a threshold of 0.5.

- Decision Tree Classifier: The only purely classification model, provides a baseline accuracy. Only split is on `SHOT_DISTANCE`.
- (Regularized) Logistic Regression: Ridge regression model with a C of 0.01 and max iterations of 300.
- Decision Tree Regressor: minimum impurity decrease for a split set to 0.003 to avoid overfitting, intuitive but not best results.
- Random Forest: performed poorly, hypothesized that choosing a random subset of features weakens model performance.
- XGBoost: the shot distance and action type were deemed most important by feature importance.
- AdaBoost: strongest of tree-ensemble models.
- Neural Network: feed-forward network, three layers with 128, 64, and 32 nodes, ReLU activation, Adam optimizer, learning rate of 0.001, dropout rate of 0.2, and MSE as loss function. Performed the best overall on probability task.
- (Regularized) Linear Regression: outcome was `SHOT_VALUE`, compared to the ANN predictions scaled by the value of each shot.

## Results


## Future Work
