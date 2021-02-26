# Optimizing an ML Pipeline in Azure

## Overview

This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary

The provided datasets contains the data of one banking marketing campagin used for training the machine learning model and the output is a binary output whether the customer will repond to the marketing campagin or not.<br> 
The dataset had 20 different features about the customers and the number of rows are 32950 out of which the label no is 29258 represnting 87.79% so the labels are not comparable which risks the model having bias problem.

<br>
In this notebook, I am going to run experiment on microsoft azure studio in 2 different methods:<br>
1. Using **LogisticRegression** algorithm to predict the marketing response of the customer, the parameters of the algorithm itself will be selected from a pool of values aiming on increasing the model accuracy. This is acheived by running a hyperdrive to select the parameters using a grid search of random selection <br>
2. Using **AutoMl** to automate the whole process

<img src="snaps_project/1-compute-run.png">

<img src="snaps_project/2-compute-cluster-idle.png">

## Scikit-learn Pipeline



<img src="Hyperdrive_flow.png">

<img src="snaps_project/3-exp_hyperdrive_sent.png">

<img src="snaps_project/4-hyper_drive_details.png">

<img src="snaps_project/5-child_preparing1.png">

<img src="snaps_project/6-resize_hyperdive.png">

<img src="snaps_project/7-resize_hyperdive2.png">

<img src="snaps_project/8-resize_hyperdive3.png">

<img src="snaps_project/9-exp_hyperdrive_execution.png">

<img src="snaps_project/10-hyperdrive_all_nodes_running.png">

<img src="snaps_project/11-hyper_6done.png">

<img src="snaps_project/12-hyperdrive_complete.png">

<img src="snaps_project/13-hyperdrive_complete_2.png">

<img src="snaps_project/14-best_run_hyper_notebook.png">

<img src="snaps_project/15-best_run_hyper_gui_all.png">

<img src="snaps_project/16-best_run_hyper_gui_run7.png">

<img src="snaps_project/17-best_run_hyper_notebook_run7.png">

<img src="snaps_project/18-hyperdrive_complete_finish.png">

<img src="snaps_project/19-hyperdrive_model.png">

<img src="snaps_project/20-register_model.png">

<img src="snaps_project/21-data_automl.png">

## AutoML

<img src="AutoML_Flow.png">

<img src="snaps_project/22-automL_submit.png">

<img src="snaps_project/23-autoML_featurisation.png">

<img src="snaps_project/24-automal_details.png">

<img src="snaps_project/25-automl_data_evaluation.png">

<img src="snaps_project/26-automl_data_imbalance.png">

<img src="snaps_project/27-automl_childs_start.png">

<img src="snaps_project/28-automl_cluster_resize.png">

<img src="snaps_project/29-automl_steps.png">

<img src="snaps_project/30-automl_cluster_resize2.png">

<img src="snaps_project/31-automl_running_models.png">

<img src="snaps_project/32-automl_running_iterations.png">

<img src="snaps_project/33-automl_instances_idle.png">

<img src="snaps_project/34-automl_nodes_leaving.png">

<img src="snaps_project/35-automl_cluster_resize2_2.png">

<img src="snaps_project/36-automl_completed.png">

<img src="snaps_project/37-automl_best_gui.png">

<img src="snaps_project/38-automl_best_gui_2.png">

<img src="snaps_project/39-automl_best_model_metric.png">

<img src="snaps_project/40-all_models.png">





## Pipeline comparison



## Issues I faced during the project and solution



## Future work



## cluster clean up:

Deleting resources after finishing required computation is very important to prevent over-charging as resources do not delete even being idle. AS I have manually created the cluster so need to delete it from the console as below:

<img src="snaps_project/41-cluster_deleting.png">

Now my cluster is no more showing in the compute clusters:

<img src="snaps_project/42-cluster_deleted.png">
