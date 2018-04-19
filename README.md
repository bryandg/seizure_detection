## Early Seizure Detection

#### Overview

The data for this project  was provided by  the American Epilepsy Society as part of a Kaggle [competition](https://www.kaggle.com/c/seizure-prediction). The data consists of intracranial electroencephalography (iEEG) or 'brain wave' data for five dogs and two human patients, all of whom experience epileptic seizures. The goal is to predict seizures at least 5 minutes prior to seizure onset through binary classification. For this project I chose to focus exclusively on 'dog_4.'

#### Problem Statement

* Predict seizures at least 5 minutes prior to seizure onset.

#### Data

* 1000 ~ten-minute intervals of iEEG data recorded at 400hz.
  * 16 seperate electrodes simultaneously recorded brain voltage for each interval
* 2 classes: preictal (65-5min prior to seizure) and interictal (> 4hrs before or after a seizure)
  * 1:9 positve to negative class ratio

![pre_v_int](/plots/pre_v_int.png)

#### Tools

* AWS—downloaded all data directly to AWS for analysis
* Jupyter Notebook
* Python libraries:
  * pandas, scikit-learn, XGBoost, matplotlib, seaborn, NumPy, SciPy
* Tableau

#### Objective

* Maximize recall (early seizure detection) while maintaing good precision
  * Used AUC and F1 during model selection, but looked at recall and precision in order to better understand how the model would function if deployed.
  * Prioritized recall over precision to minimize underdiagnosis.

#### Approach

* Stacked two models
  * Split each ~10-min interval into multiple fifteen-second intervals with the first model predicting for each 15-second interval.
  * These 39 predictions were then passed as features to a second model in order to make a final prediction for the entire 10-minute segment.

#### Features

* Feature selection was based on weight, gain, and cross validation scores.
* Used most sequential measurements with a voltage between -50 and 50, 1st and 99th percentile values, standard deviation of values and means of each of the above measurements across all channels as features.

#### Models

* Acheived higher cross-validation scores with XGBoost than random forest models.
* Took the out-of-sample predictions (3-fold CV) from XGBoost model and passed these predictions to a logistic regression model.
  * Settled on using the mean of the 20 highest probabilites (39 total) as  the primary feature for logistic regression model.
    * This mean provided an optimal tradeoff between robustness to noise and sensitivity to individual results.
* Final testing scores: recall—1.0, precision—0.5, AUC—0.93.

