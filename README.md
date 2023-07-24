<div align="center">
<img src="https://github.com/skswar/Explainable_ML_for_Production_Downtime_Shap/blob/main/img/banner.png" alt="Intro Logo" width="60%"/>
</div>
<h3 align="center">Can we Explain the "Why" of Patterns/Trends Observed in Business Intelligence Tools/Graphs?</h3>
<hr>

## Table of contents
* [Introduction](#introduction)
* [Feature Derivation](#feature-derivation)
* [Model Building](#model-building)
* [Explaning Why with SHAP](#explaning-why-with-shap)
* [Conclusion](#conclusion)
* [Link to Notebook](#link-to-notebook)

## Introduction
In this project I came across a situation/data where I had to figure out a way to answer questions related to downtime trends taking place for a particular manufacturing process. The data available at had was limited with only few information such as when the process is running, information related to the products being produced, environmental conditions, machine operating speed and operator details. With such limited information in hand the task was to model the data for downtime and if possible figure the factors influencing the most towards a downtime. 

## Feature Derivation
The details regarding machine downtime were not immediately accessible in the dataset. After a thorough examination, it was decided to categorize a data point as downtime when the machine didn't produce any output for a span exceeding fifteen minutes during its operation. Utilizing some additional available information, the strategy was to analyze if production information related to last 30 minute can help algorithm effectively determine whether a process will be transition into a downtime or not.

With that plan, features such as feed speed variance in last 30 mins, total and rejected cards mean and variance in last 30 mins, avaerage temperature and humidity in the previous hour, hour of the day and day of the week columns etc. was calculated. After derving the new columns, preprocessing/data cleaning were performed to remove nan data points where previous 30 mins information were not available. The target variable was made 1 whenever it was a downtime state and 0 to indicate machine run state. All this preprocessing structured every line in the data such data columns indicated what happened in the past 30 mins and the target variable indicated the current state of machine.

## Model Building
Following a stratified sampling approach to divide the data into training and testing subsets, a Catboost model was employed for training. The selection of Catboost was not due to any specific advantages it holds over other models, but rather because the project's focus was not centered on identifying the 'best' model. The primary interest lay in determining whether, given a successful prediction, it would be possible to identify the most influential factors contributing to those predictions. With the establishment of a baseline model, additional efforts could then be directed towards enhancing the model's performance. Model parameters were largely defined based on prior experience, and the Area Under the Curve (AUC) was selected as the evaluation metric for model training.

Prior training, the model was able to predict Downtime with 98% accurancy. This was good enough for the inital goal of this project.





