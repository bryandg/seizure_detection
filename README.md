## Early Seizure Detection

#### Data

* 1000 ~ten-minute iEEG intervals recorded at 400hz.
  * 16 seperate electrodes simultaneously recorded brain voltage for each interval
* 2 classes: preictal (65-5min prior to seizure) and interictal (> 4hrs before or after a seizure)
  * 1:9 positve to negative class ratio

![pre_v_int](/plots/pre_v_int.png)

#### Tools

* AWS—I downloaded all data directly to AWS for analysis

#### Objective

* Maximize recall (early seizure detection) while maintaing good precision
  * I used AUC and F1 during model selection, but looked at recall and precision in order to better understand how the model would function if deployed.
  * Prioritized recall over precision as underdiagnosis is more problematic than overdiagnosis.

#### Approach

* I split each ~10min interval into 39 fifteen-second intervals for model training
* The 39 predictions for each 10min interval would then be passed as features to a second model for making a prediction for each 10min segment.

#### Features

* I settled on using max sequential measurements with a voltage between -50 and 50, 1st and 99th percentile values, standard deviation of values and means of each of the above measurements across all channels as features. I based feature selection on weight, gain, and cross validation scores.

#### Models

* I started off with a random forest and found that a combination of setting minimum leaf size to fifteen and adjusting my threshold increased my initial f1 score from ~0.25 to ~0.45.
* Next, I tried XGBoost which instantly gave me a slightly better score than the random forest model and I was able to modestly increase the initial score through tuning hyperparameters.
* I then took the out-of-sample predictions (3-fold cv) from the XGBoost model and passed the mean of these predictions to a logistic regression model.
  * Eventually I settled on using the mean of the 20 highest probabilites (39 total) as my primary feature. This mean provided more robustness to noise than using a smaller proportion of the probabilities, but didn't allow several low proabilites to offset high probabilities for other intervals.
* Final testing scores: recall—1.0, precision—0.5, AUC—0.93.

