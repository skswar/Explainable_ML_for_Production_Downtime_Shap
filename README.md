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



