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

The model performance metrics are discussed extensively in the report, but here we will acknowledge that the neural network had the lowest MSE on the probability prediction task. The differences in model performance were small, but with the large amount of data we have these small differences do matter. After deciding the best model, we scaled the probability results by the points from the shot (2 or 3) to get a shot value prediction. Comparing the scaled neural network with a linear regression model, we saw a slightly better MSE from the neural network. We were surprised to see such a small margin, indicating that a simpler model could still address our problem. However, we used the ANN in the player analysis.

The most important consequence of our work is the ability to use this modeling to judge player's shooting ability, shot selection, and scoring value in a much more accurate way than FG% or even more advanced metrics (eFG%, TS%, etc.). The more accurate of a model we can create (improvements will be discussed shortly), the more credence it lends to our value over expectation metric. A player that produces more points than expected is one that is able to shoot and score at an above average clip. Alternatively, we can look at this metric on a per shot basis as well.

A secondary focus on our project is on a player's shot selection. It is interesting to analyze the expected points per shot because those with the smallest values are the ones taking shots that are the least valuable. Even if they are a great scorer and produce over expectation, the amount of points they add per shot will still be low due to their poor shot selection.

## Future Work

Much of the future work that we would like to explore is a result of two issues: lack of time and lack of data.

Within the context of this project, it is very important to acknowledge the weaknesses of this statistic. First of all, the metric we use to judge players fails to account for the creation of the shot. For example, we see Robert Williams III have a very high value added per shot. However, he is a player that rarely shoots the ball based on his role in the 2023 Boston Celtics offense, and when he does take shots they are often unguarded and close to the basket, created by a teammate with a good pass. We see that he has a high expected points per shot because of the average distance from the basket, but we fail to account for the absence of defensive pressure on his shots. Hence, he still produces beyond his expectation, but it is hard to say he is a great scorer based on how he gets his points.

A player on the opposite end of the spectrum is Giannis Antetokounmpo. Our model assigns him a very slight negative points added over expectation, something that is not in line with the commonly held belief that he is a great scorer (over 30 PPG in 2023). In comparison to a player like Robert Williams III, Antetokounmpo is also taking shots close to the basket, but now through driving to the basket and being a talented finisher near the hoop. Our model fails to account for the defensive pressure on his shots, and also misses his ability to draw fouls and attempt over 12 free throws per game.

These two case studies show us that to improve our model, the most important step would be to add in closest defender data (which exists, but is protected by the NBA and not publicly available). Another feature that could be very helpful is the time remaining on the shot clock. We saw that the time left in the quarter was a useful predictor when there was very little time remaining. Similar situations arise at the end of a shot clock where a player needs to take a "bad" shot in order to avoid a shot clock violation. We believe this additional feature can improve our model (again it exists, but is protected).

These two future work possibilities involve our lack of data problem. The last one would be to include all available years (2004-2023) in the model training, something that we avoided only because of time constraits placed on the project. Based on exploring simpler models across the entire dataset, we believe that including all the data could reduce the MSE on the shot value prediction from ~1.35 to ~1.2.

Overall, we believe this style of analysis has the potential to become a much better metric to judge a player’s scoring ability than current shooting efficiency metrics. While these percentages do incorporate the value of shots, they fail to include the expectation component that our analysis provides
