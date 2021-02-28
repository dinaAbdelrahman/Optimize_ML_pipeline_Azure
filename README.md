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
* LogisticRegression + hyperdive: algorithm to predict the marketing response of the customer, the parameters of the algorithm itself will be selected from a pool of values aiming on increasing the model accuracy. This is acheived by running a hyperdrive to select the parameters using a grid search of random selection. I used the hyperdrive to optimize the selection of c and max_iter<br>
* AutoMl: to automate the whole process inclusding features selection, dataset featurization, cross validation and model selection and running<br>

**Results Summary:**<br>
I have evaluated both strategies based on the model accuracy:
* LogisticRegression + hyperdive: the best model accuracy acheived was 91.58%
* AutoMl: the best model accuracy acheived was 91.68% which is slightly higher than the hyperdrive results <br>

Further information will be provided below in each startegy. <br>

Before I started working on the project, I created one compute instance to host my jupiter notebook of one STANDARD_DS3_V2 instance.

![i1](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/1-compute-run.PNG)

<br>
To run my training jobs, I created one compute cluster which consists of 4 machines STANDARD_DS3_V2 with one machine all time available to take up the initial work of performing the training preparation meanwhile the other 3 machines come up when the actual training job start. For cost saving, it is recommended to have the minimum machines set to 0. 
<br>

![i2](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/2-compute-cluster-idle.PNG)

## Scikit-learn Pipeline

Below is showing my workflow to run the Scikit-learn pipeline, I already have my compute instance and compute cluster up running:
1. Clone the Git repository into my workspace
2. Setup my enviromenet to be able to use the Scikit-learn library, all dependencies are in the conda_dependencies.yml
3. define my hyperdrive parameters as well as the values the parameters can take in the hyperdive notebook as well as the early termination policy, primary metric for evaluation, my total runs as well as maximum concurrent sessions
4. Specify my train script Train.py
5. Submit the experiement to the compute cluster to run
6. Once the job is submitted, it will run the training steps with the inputs taken from the hyperdrive pool performing the datset import --> data cleaning --> fitting model using the passed parameters which changes in each run
7. After performing all runs, I register the model having the best accuracy



![i_P1](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/Hyperdrive_flow.PNG)

<br>
Here my experiment is submited successfully to run with run type Hyperdive<br>

![i3](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/3-exp_hyperdrive_sent.png)

<br>
From the azure console, the settings for running script train.py. The sampling policy is Random from the parameter space for C and max_iter, my early termination ploicy is using Bandit and the primary metric set to Accuracy
<br>

![i4](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/4-hyper_drive_details.PNG)

<br>
4 children run has been prepared, the first one showing running state as I have 1 machine always on, while the other 3 are waiting Azure to turn on the other 3 machines.
<br>

![i5](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/5-child_preparing1.png)

<br>
This is interesting part to monitor HW and that is the real benefit of using cloud to dynamically scal up/down resources as per requirements. Once Azure detect I have 4 parallel jobs, it starts scaling up the cluster from 1 active node to 4 which is the maximum of concurrent sessions, if my cluster has more machines only 4 will be up. If I have 4 nodes so cannot configure more concurrent jobs as it would exceed the allocated HW.
<br>

![i6](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/6-resize_hyperdive.PNG)

<br>
This is same view but showing all nodes, initially had one running nodes. So once resize command sent, we would see shortly 4 nodes up running
<br>

![i7](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/7-resize_hyperdive2.PNG)

![i8](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/8-resize_hyperdive3.PNG)

<br>
Back to our experiment, the first child is done and 4 more children initialized and running on the 4 nodes. Not every run has same time of execution so we can see run 5 is in late stage and is almost finialized. This shows the parallelizm power of submitting runs so once 1 run is done, another is put in schedule. Another observation is each run has different value for C and max_iter as specified in the hyperdrive settings so that confirm the execution going as intended.
<br>

![i9](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/9-exp_hyperdrive_execution.PNG)

<br>
All our 4 nodes are busy, handling the chidren runs. Scaling the HW up and down fully managed by Azure.
<br>

![i10](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/10-hyperdrive_all_nodes_running.PNG)

<br>
At this instance, 6 children are competed out of 12.
<br>

![i11](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/11-hyper_6done.PNG)

<br>
Now the hyperdrive has competed successfully, time taken is 13 minutes and 6 seconds to run 12 differents children across 4 nodes. All 12 runs ended without errors.
<br>

![i12](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/12-hyperdrive_complete.PNG)

![i13](https://github.com/dinaAbdelrahman/Optimize_ML_pipeline_Azure/blob/main/snaps_project/13-hyperdrive_complete_2.PNG)

<br>
Finally was able to get the best run showing in my notebook after several troubleshooting as from console was showing sucess but no best run --> please check the section "Issues I faced during the project and solution" for further details.
<br>

<img src="snaps_project/14-best_run_hyper_notebook.png">

<br>
From the console, the results of the 12 runs are showing each its accuracy as well as the GUI representation of the paramerters with the accuracy, Run 7 is having the best accuracy 91.58 while the used parameters C and Max_iter were 1 and 200 respectively.
<br>

<img src="snaps_project/15-best_run_hyper_gui_all.png">

<img src="snaps_project/16-best_run_hyper_gui_run7.png">

<br>
We can get the parameters of the best run from the jupiter notebook as well as shown below:
<br>

<img src="snaps_project/17-best_run_hyper_notebook_run7.png">

<br>
After the run was done, we can see the on-demand nodes were shutdown and our minimum 1 node is still up but in idle state.
<br>

<img src="snaps_project/18-hyperdrive_complete_finish.png">

<br>
Best model is registeres and saved in the output folder. The metrics were added as tags for the model.
<br>

<img src="snaps_project/19-hyperdrive_model.png">

<img src="snaps_project/20-register_model.png">

<img src="snaps_project/21-data_automl.png">

## AutoML

Below is showing my workflow to run the AutoML pipeline, I already have my compute instance and compute cluster up running:
1. Clone the Git repository into my workspace
2. Import my dataset as tabular dataset for access during training on remote compute
3. Here I will reply on the power on AutoML to train the models however as I need to apply the data cleaning function to do data transformation and fields encoding ,handling missing values.
4. As my clean function returns 2 data frames: 1 for fetaures and 1 for labels, I have to combine them to submit them to the AutoML, I got errors when I submitted them at 2 independant dataframes. --> please check the section "Issues I faced during the project and solution" for further details.
5. Submit the experiement to the compute cluster to run
6. Once the job is submitted, azure will handle all background work including Generating features for the dataset, fit featurizers and featurize the dataset, Dataset Cross Validation Split, and finally perform Models list Selection and fitting and recording their accuracy as being the selected the primary metrics to maximize during the run.
7. After performing all runs, I register the model having the best accuracy

<img src="AutoML_Flow.png">

<br>
Here my experiment is submited successfully to run with run type AutoML <br>

<img src="snaps_project/22-automL_submit.png">

<img src="snaps_project/23-autoML_featurisation.png">

<br>
From the azure console, the primary metric set to Accuracy. I set the maximum time to run the AutoML is 0.5 hour and number of my cross validation to 5. Still I will have same max concurrent sessions to 4 while my cluster max number of nodes set to 4
<br>

<img src="snaps_project/24-automal_details.png">

<br>
During the step of AutoML of data featurization, the class imbalance was detected which is highlighted in the start. This won't stop the run however it set an alert of the risk of model bias towards the biggest class which requires pre-work -as example- in the data preparation phase to resample the dataset to get comparable class. 
<br>

<img src="snaps_project/25-automl_data_evaluation.png">

<img src="snaps_project/26-automl_data_imbalance.png">

<img src="snaps_project/27-automl_childs_start.png">

<br>
This is the same view we had in the start of the run of the hyperdrive, also in AutoML, all work of preparation is done on the up node. Once the actual model training starts, we can see Azure starts rezising the cluster from 1 node to 4 nodes to run the 4 concurrent training tasks.
<br>

<img src="snaps_project/28-automl_cluster_resize.png">

<img src="snaps_project/29-automl_steps.png">

<img src="snaps_project/30-automl_cluster_resize2.png">

<br>
Now the AutoML slected few models to use them to train the input dataset. We don't need to split the input data into training and evaluation as this is part of the AutoML task.
<br>

<img src="snaps_project/31-automl_running_models.png">

<br>
This is view of the models running with the consumed time to finish the training and evaluation job.
<br>

<img src="snaps_project/32-automl_running_iterations.png">

<br>
We can see the 4 nodes are up while 2 are idle, in fact the training jobs are in the last 2 running on 2 nodes and 2 nodes are in idle state. This is also important observation, when Azure finish the task on one node it keep it in idle mode till the expiry of the timeout then this node is totally removed from the cluster. This prevent un-necessary destroy of node if new task coming during this timeout interval.
<br>

<img src="snaps_project/33-automl_instances_idle.png">

<img src="snaps_project/34-automl_nodes_leaving.png">

<img src="snaps_project/35-automl_cluster_resize2_2.png">

<br>
Finally our AutoML has completed in 23 minutes and 5 seconds which is almost 10 minutes greater than the Hyperdrive run while the accuracy gain is 0.01% so this is another factor to be taken into consideration.<br>
Another comparison, the Hyperdrive had 12 runs while the AutoML had run 37 different algorithms.
<br>

<img src="snaps_project/36-automl_completed.png">

<img src="snaps_project/37-automl_best_gui.png">

<br>
Our best run from the AutoML was acheived by the Voting Ensemble algorithm. In classification, a hard voting ensemble involves summing the votes for crisp class labels from other models and predicting the class with the most votes. A soft voting ensemble involves summing the predicted probabilities for class labels and predicting the class label with the largest sum probability.<br> Here the Voting Ensemble algorithm details involves taking the vote of 5 top algorithms but infortunately this information was lost due to VM running the workspace timeout.
<br>

<img src="snaps_project/38-automl_best_gui_2.png">

<br>
Below snaps shows the best model metrics apart from the accuracy as for such classification problem in presence of imbalance class, the F1_score maybe a better selection parameter for model preferences when we want to go into production state.
<br>

<img src="snaps_project/39-automl_best_model_metric.png">

<br>
My best AutoML is registered and appears in the models list
<br>

<img src="snaps_project/40-all_models.png">

## Pipeline comparison

Below table summarize the pipeline running and my own observations:

<img src="pipeline_compare.png">

## Issues I faced during the project and solution

I run into too many erros that I googled a lot and found mostly answers in Azure Git repository in the tutorials/How to use azure part.
1. ModuleNotFoundError: No module named 'sklearn' --> around 2 weeks here, the solution to put the enviroment dependency in yaml file and use it in the notebook. https://azure.github.io/azureml-web/docs/cheatsheet/environment/
2. My hyperdrive was running successful but no best run come out in the notebook. Here one mentor detected I used "accuracy" in the settings definition of Hyperdrive and suggested to change to "Accuracy" AND IT WORKED, please Microsoft remove the case sensitivity
3. Now I took care from point 2 while working on automal and put primary metrics "Accuracy". Again experiement run without best run --> this time it turns to be "accuracy", I get to know that as I use AUC_weighted and things went fine so "Accuracy" for hyperdrive and "accuracy" for AutoML

## Future work

I beleive below steps can improve the work:
* Most important thing which is the crucial from my point of view is to work on the data preparation phase including class imbalance elimination, study of outliers and drop them from the records, deep exploration of features probably mixing few fields or dropping others can enhance the output models.
* traditional things: increase the hyperdrive run, include few more parameters in the hyperdrive pool. No need to increase AutoML time as experiment already finished before the expiry but we can choose other metric like the AUC.

## cluster clean up:

Deleting resources after finishing required computation is very important to prevent over-charging as resources do not delete even being idle. AS I have manually created the cluster so need to delete it from the console as below:

<img src="snaps_project/41-cluster_deleting.png">

Now my cluster is no more showing in the compute clusters:

<img src="snaps_project/42-cluster_deleted.png">
