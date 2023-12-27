# NBA Shot Prediction and Player Performance Analysis

## Overview
For a final project in a Machine Learning course, myself and three fellow students built a model to predict the value of an NBA shot based on location factors, action type (i.e. dunk, jump shot), and other game related information (i.e. time remaining, home/away, etc.). We notably left player name out of the modeling process, which surely affected model performance, but also gave us a lot of power in analyzing the model results. We were able to compare the model's expectation over all of a single player's shots against the actual results that player produced. The concept of points produced over prediction, or expectation, is extremely useful in analysis of a player's performance. Our results showed that players such as Nikola Jokic, Steph Curry, and Kevin Durant produce the most value on their shots in 2023, as any NBA fan would expect. However, we also noticed some players, including Luke Kennard, Bojan Bogdanovic, and Buddy Hield, that received little attention for their play in 2023, but according to our analysis had incredible scoring seasons.

## Data
To study our problem we used the following datasets:

1. Shot-Level Data (primary): Over 4M shots taken between 2004 and 2023.
2. Game Outcome Data: Includes final scores of each game in our time span
3. Season-long Team Stats: Provides defensive rating of shot taker opponent
4. NBA Abbreviations: Allows to connect teams between datasets

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


## Code

## Predictive Modeling

## Results

## Future Work
