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

Prior training, the model was able to predict Downtime with 98% accurancy. This was good enough for the inital goal of this project. The following image shows confusion matrix of the model. There are few misclassified cases in which the plan dig deeper later.
<p align="center">
<img src="https://github.com/skswar/Explainable_ML_for_Production_Downtime_Shap/blob/main/img/conf_mat.png" width="40%"/>
</p>

## Explaning Why with SHAP
The next step was to discern the features that held the most significance for our model. The following feature importance graph (calculated by taking mean of the SHAP values) revealed that variables such as the variance in FeedSpeed over the last 30 minutes, the humidity from the previous hour, and the variance in the number of rejected cards in the past 30 minutes, served as the pivotal factors driving the model's predictions. However, understanding the specific values of these features that lead to a certain prediction necessitated further investigation. This was where SHAP (SHapley Additive exPlanations) became instrumental.
<p align="center">
<img src="https://github.com/skswar/Explainable_ML_for_Production_Downtime_Shap/blob/main/img/overallfi.png" width="60%"/>
</p>

The following SHAP summary plot shows us what values of the individual features caused our model towards predicting a downtime or a run state. As it can be viewd in the plot, the higher the variance in the machine operating speed (FeedSpeed_Var_last30) in the last 30 min the more chance of moving into a downtime. Similarly a lower mean machine operating speed (FeedSpeed_Mean_last30) will cause most likely to go into a downtime state. This essentially indicates the machine was slowing down. A similar explanation holds for the number of rejected cards variance and mean in the last 30 mins. The humidity variable is not pretty distinguishable and thus another plot might be helpful which is disccused later. For Temperature in last 1 hour if have went lower then the model is more likely to predict downtime. Also when model see operators SM3, JT1 it is more likely to predict a downtime state transition.
<p align="center">
<img src="https://github.com/skswar/Explainable_ML_for_Production_Downtime_Shap/blob/main/img/shap_summ.png" width="50%"/>
<p align="center">Downtime is a target Variable 1 thus right side of the 0 reference line predicts downtime</p>
</p>

We need to note that SHAP considers all possible combinations of features and computes the average contribution of each feature across all instances. Therefore the above summary plot provided us with a overall affeect of features on predicting downtime. For understanding individual data points we can look into individual shap value's force plot.

The following two scatter plots helps us understand what sort of values of individual features contributed more towards a certain kind of prediction. In the below plot, we can see that within the range of 40-45 humidity level in the previous hour there is a darker cloud towards lesser than 0 Shap values. Shap values increase more it is not within this range thus causing the model to predict downtime. This information will be more visible when we individually look at different jobs.
<p align="center">
<img src="https://github.com/skswar/Explainable_ML_for_Production_Downtime_Shap/blob/main/img/scatt_2.png" width="35%"/>
</p>

With overall understanding of the SHAP values, it was also possible to go into individual cases of prediction. As in manufacturing, products are devloped in batches of requirement therefore we can filter out individual batches/jobs to identify what happend in specific cases. As can be seen in the following force-plot for  specific job, with increasing humidity in the previous hour, the model is more likely to predict a downtime.
<p align="center">
<img src="https://github.com/skswar/Explainable_ML_for_Production_Downtime_Shap/blob/main/img/humdty_eff.PNG" width="65%"/>
</p>

The follwing image shows as the rejected products mean in the last 30 mins reduces, the model is more likely to predict downtime. This can be explained by the correaltion the number of rejected products has with total products being produced. Less mean of rejected products simply indicates less number of products being produced. Thus it is more likely that machine will transition towards a downtime.
<p align="center">
<img src="https://github.com/skswar/Explainable_ML_for_Production_Downtime_Shap/blob/main/img/reject_eff.PNG" width="65%"/>
</p>

In the following image we can understand the values of the temperature that is best to maintain in order to not move into a downtime. As per the model, if temperature reduces in the previous hour then it is not good for the production process.
<p align="center">
<img src="https://github.com/skswar/Explainable_ML_for_Production_Downtime_Shap/blob/main/img/temp_eff.PNG" width="65%"/>
</p>

Ultimately, it's feasible to compare the feature importance across different batches, allowing us to ascertain if any particular factor on particular bacth had a greater influence on leading to downtime. By doing so, we can focus our attention on that specific batch to uncover potential causes and implement necessary corrections.


## Conclusion
In summary, the primary objective of this project was to elucidate the reasons leading to machine downtime. We sought to determine if the activities of the preceding 30 minutes could shed light on the current machine opearating state. Furthermore, the aim was to identify the most influential factors driving this prediction. Tree-based algorithms have recently demonstrated exceptional performance in many machine learning challenges, and thus employing one in this project was deemed worthwhile. It was known that with help of tree ased models feature importance can be derived which will further help towards the project goal.

Using a Catboost Tree Based model, it was possible to calculate feature importance, which furthered our goal of identifying key contributors to downtime prediction. The utilization of SHAP values was instrumental in drilling down to individual feature values, helping us to better understand their contribution to downtime.

This process eabled to gain a baseline understanding of the production line dynamics, and identify parameter ranges to maintain during the process for maximum efficiency. Some of the insights derived also proved valuable for deeper analysis and subsequent corrective action planning.

If there are any suggestions/advise or you are simply interested in a discussion regarding this project or any other Data Science related concepts, please feel free to contact me through [Linked-In](http://linkedin.com/in/sayankrswar) or shoot an [email](mailto:sayankrswar@hotmail.com). I Thank You for having your patience in reading the entire article.

## Link to Notebook
In order to view the notebook with plotly rendered graphs, please visit the following link:
[Explaning_Downtime_With_Machine_Learning.ipynb](https://nbviewer.org/github/skswar/Explainable_ML_for_Production_Downtime_Shap/blob/main/python_script/Explaning_Downtime_With_Machine_Learning.ipynb)







