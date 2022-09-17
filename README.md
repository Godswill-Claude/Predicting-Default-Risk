# Creditworthiness – Predicting-Default-Risk

Overview 

This project aims to decide if the 500 new loan applications of the current week of a business should be approved, that is, to determine if the 500 loan applicants are creditworthy to be given a loan. 
The software application used for the entire analytical process and model building is Alteryx. 

The following steps were taken to get the desired results.
Step 1: Business and Data Understanding
Key Decisions:
●	What decisions need to be made?

●	What data is needed to inform those decisions?

●	What kind of model (Continuous, Binary, Non-Binary, Time-Series) do we need to use to help make these decisions?


Answers:
We need to decide if the 500 new loan applications of the current week should be approved.
To make this decision, we need to determine if the 500 loan applicants are creditworthy to give a loan to. 
To help to make this determination, we will need to employ the skills of classification modelling, with a view to systematically evaluate the creditworthiness of these new loan applicants. This way, we will be able to provide a list of creditworthy customers to the manager in the next two days.
Since all we need to determine is whether or not they are creditworthy (a simple YES or NO situation), the binary classification model is most fitting.
The data required to build our model and make our prediction include:
i.	Data on all past applications (training dataset to build our model), which include key relevant fields like: Account-Balance of the applicants, the Purpose for which loan is being taken, Credit-Amount applied for, Age-years of the applicants, Payment-Status-of-Previous-Credit, Duration-of-Credit-Month, Value-Savings-Stocks, Length-of-current-employment, Instalment-per-cent, Guarantors, Duration-in-Current-address, Most-valuable-available-asset, Concurrent-Credits, Type-of-apartment, No-of-Credits-at-this-Bank, Occupation, No-of-dependents, Telephone and Foreign-Worker. Out of these fields, the actual significant predictor variables which provide good insight to the target variable (Credit-Application-Result field) will later be determined.

ii.	The dataset containing the list of customers that need to be processed in the next few days (dataset on which predictions will be made), which includes similar fields like those in the training dataset.
Step 2: Building the Training Set
The following processes were involved in cleaning up the given dataset:
•	First, the field summary tool was used to check the categorical variables to see which ones have too many categories. None fell into this group hence no predictor variable was removed based on this consideration.
•	Again, the results of the field summary tool shows that the field Duration-in-Current-address has too many missing data values. Hence, it was removed.
•	Age-Years has just a couple of null values, hence its field was retained and the missing values were imputed using the median of the entire data field (since the data is skewed to the right), instead of removing a few data points; and the average Age-Years was found to be 36, as required. 
•	More so, the fields Concurrent-Credits and Occupation exhibits uniformity (a specific case of low variability) since there is only one value for the entire field. Consequently, these fields were also removed.
•	Hovering the cursor over the charts in the field summary results shows that the field Guarantors has two unique values, but data is almost uniformly distributed to one of the values (457 NONE and only 43 YES). And as for No-of-dependents, its data stream is heavily skewed – there are 427 ones (1’s) as against 73 twos (2’s). More so, the field Foreign Worker has 481 2’s as against 19 1’s. Hence, all three fields were removed for exhibiting low variability (one value happening much more than the other).
•	The field Telephone has no logical basis for being included in the training dataset since it is not relevant to predicting creditworthiness of an applicant.
•	As for the numerical data fields, the ASSOCIATION ANALYSIS tool was used to check if there were any fields that highly correlate with each other (with a correlation ≥ ±0.70). The correlation matrix (heat plot) and corresponding scatterplots from the I (interactive) output of the ASSOCIATION ANALYSIS tool helped me to determine if there were pairs of predictor variables with inner correlation with each other (duplicate variables, since they are basically one and the same thing), one of which should be removed and not used in building our model to avoid redundancy and errors in our model. The one that will be removed should be the one that has a lesser association with the target variable (as determined by the p-value and number of stars) in the R (report) output of the ASSOCIATION ANALYSIS tool.
The results showed that none of the predictor variables have an inner correlation ≥ ±0.70

But then again, looking at the R output of the ASSOCIATION ANALYSIS tool, it can be observed that the fields DURATION OF CREDIT MONTH, CREDIT AMOUNT and MOST VALUABLE AVAILABLE ASSET are the most statistically significant variables (with p-values all less than 0.05, and a minimum of two stars **) to the target variable CREDIT APPLICATION RESULT. 
Step 3: Train your Classification Models
After creating my Estimation and Validation samples (with 70% of my entire training dataset going to Estimation and 30% reserved for Validation) and setting the Random Seed to 1 in the CREATE SAMPLES tool in Alteryx, the following models were created with the resulting statistics for each included below.
1.	Logistic regression: After connecting the LOGISTIC REGRESSION TOOL to the estimation dataset, the STEPWISE tool was connected to the output of the LOGISTIC REGRESSION tool to automate the process of determining which predictor variables are statistically significant. The R output of the STEPWISE TOOL showed an R-squared value of 0.2048, which is far less than the ideal value of 1. And then again, looking at the upper part of the report, we can see that there are 6 predictor variables which show some statistical significance to the building of the model (with p-values of less than 0.05 and a range of 1 to 3 stars), namely Account.Balance, Payment.Status.of.Previous.Credit, Purpose, Credit.Amount, Length.of.current.employment, Instalment.per.cent and Most.valuable.available.asset.

2.	Decision Tree Model: The R output showed a Root node error: 97/350 = 0.27714 or 27.7%. This gives us an idea of the actual percentage of the out of our overall dataset which fell in the correct terminal node. Thus, 97 out of 350 (27.7%) fell in the wrong terminal node, whilst 72.3% fell in the correct terminal node.
More so, the variables actually used for the tree construction are: Account.Balance, Duration.of.Credit.Month, Purpose and Value.Savings.Stocks, as revealed by the Model Summary section of the report and the splitted tree diagram below.
 
The I output of the decision tree revealed that the decision tree was splitted as shown below.
The results of the confusion matrix helps in determining where there may be biases in our dataset or if it is skewed to one side, plus the level of Miscalculations.

But since DECISION TREE model has the downside of overfitting a dataset (wherein the model fits the sample dataset a little too well and as a result does not predict the future accurately enough), we will need to validate it later and compare with other models to choose the best for our prediction.

3.	Forest Model: The R output shows an OOB (Out of The Bag) estimate of the error rate of 23.1%, which is quite high and a bit worrisome to trust to predict accurately.
The CONFUSION MATRIX shows that there was only a 6.7% error in predicting the CREDITWORTHY category as against 66% for NON-CREDITWORTHY prediction.

 

The VARIABLE IMPORTANCE PLOT shows that all 12 predictor variables are accommodated by this model, with CREDIT AMOUNT, AGE YEARS, DURATION OF CREDIT MONTH, ACCOUNT BALANCE and MOST VALUABLE AVAILABLE ASSET being the most important predictor variables for this model.
	
4.	Boosted Model: The VARIABLE IMPORTANCE PLOT of the R output shows 12 statistically significant predictor variables, with ACCOUNT BALANCE and CREDIT AMOUNT being the two most insightful predictors for the target variable in this model. 


Next, I used the UNION tool to union all 4 models together, and then finally the MODEL COMPARISON TOOL was used to compare all 4 models side-by-side to ascertain which is the best model.



BIASES IN EACH MODEL
Bias may be considered as the tendency of a model to predict one of its outcomes much more accurately than the others (in this case, Creditworthy vs Non-Creditworthy).
 To determine the biases in each model, I considered the accuracies on both the CREDITWORTHY and NON-CREDITWORTHY segments of the Model Comparison Report, plus I checked/worked out the PPV and NPV values using the Confusion Matrix. If one of the prediction’s percentage accuracy is way higher than the other, then the model has a bias towards that higher end, otherwise it has little or no bias. 
The following biases were noticed in each model’s prediction ability.
Logistic/Stepwise Regression: From Model Comparison Report, the overall percent accuracy of the Logistic model is 76%, which is strong.
Accuracy in predicting Creditworthiness is 0.8762 (87.6%), while that for Non-creditworthiness is 0.4889 (48.9%). The difference is high.
More so, from the Confusion Matrix:
PPV= true positives / (true positives + false positives) = 92 / (92+23) = 0.80 or 80%
NPV= true negatives / (true negatives + false negatives) = 22 / (22+13) = 0.63 or 63%
The difference between the PPV and NPV values is also much.
So, after checking the confusion matrix there is bias seen in the model's prediction towards Creditworthy.
Forest Model: From Model Comparison Report, the overall percent accuracy of the Forest model is 79.3%, which is strong.
Accuracy in predicting Creditworthiness is 97.1%, while that for Non-creditworthiness is 37.8% - a difference quite high, suggesting bias.
However, from the Confusion Matrix:
PPV= true positives / (true positives + false positives) = 102 / (102+28) = 0.78
NPV= true negatives / (true negatives + false negatives) = 17/ (17+3) = 0.85
I noticed that the PPV and NPV values are quite close. So, after checking the confusion matrix there is little or no bias seen in the model's prediction.
Decision Tree Model: From the Model Comparison Report, the overall percent accuracy of the Decision Tree model is 74.7%, which is strong.
Accuracy in predicting Creditworthiness is 88.6%, while that for Non-creditworthiness is 42.2%. The difference is high.
More so, from the Confusion Matrix:
PPV= true positives / (true positives + false positives) = 93 / (93+26) = 0.78
NPV= true negatives / (true negatives + false negatives) = 19 / (19+12) = 0.61
The difference between the PPV and NPV values is also much.
So, after checking the confusion matrix there is bias seen in the model's prediction towards Creditworthy.
Boosted Model: From the Model Comparison Report, the overall percent accuracy of the Boosted model is 78.7%, which is strong.
Accuracy in predicting Creditworthiness is 96.2%, while that for Non-creditworthiness is 37.8%. The difference is high.
More so, from the Confusion Matrix:
PPV= true positives / (true positives + false positives) = 101 / (101+28) = 0.78
NPV= true negatives / (true negatives + false negatives) = 17 / (17+4) = 0.81
the difference between the PPV and NPV values is also much.
So, just like the forest model, this model is almost not biased at all after checking the confusion matrix.

Step 4: Writeup
In summary, the FOREST MODEL seems to perform slightly above all other models in all considerations.

•	In the overall accuracy against the validation set, it was the highest at 0.7933, as seen above.
•	More so, looking at the GAIN CHART and the ROC curve below, the FOREST MODEL reaches the top (in the y-axis) the quickest slightly above the other models.
More so, the curve of the FOREST MODEL is the overall highest curve of all. This means that for a given amount of false positive predictions (wrongly predicted creditworthy people), this model will give the best number of true positive predictions (correctly predicted creditworthy people).
Again, comparing the Area Under the Curve (AUC) values for all the models as seen in the Model Comparison Report, the FOREST MODEL has an AUC of 0.7368 (which is 2nd only to that of the BOOSTED MODEL), and the higher the AUC, the higher the curve, vice versa. Thus, the higher the AUC, the better the model’s predicting ability.


•	The FOREST’S MODEL accuracy in predicting Creditworthiness is 97.1% (which is the highest of all the 4 models considered), while that for Non-creditworthiness is 37.8% (which is equal to that of the next-best model – the BOOSTED MODEL).

•	In terms of Confusion Matrix, the Forest Model has a PPV of 0.78 and an NPV of 0.85, which are quite close. Thus, there is little or no bias seen in the model's prediction ability. And with the exception of the BOOSTED MODEL, the FOREST MODEL ranks the best in the bias consideration. And in actuality, it’s rating, bias-wise, is approximately as good as that of the BOOSTED MODEL.

Thus, putting all of the four strong factors above together, the RANDOM FOREST MODEL is the best choice.

Next, the INPUT DATA TOOL was used to introduce the dataset to be scored to the workflow, which together with the FOREST MODEL was connected to the SCORE TOOL. The results displayed shows the probabilities of Creditworthy and Non-Creditworthy for each record.

The interpretation was done as follows: If Score_Creditworthy is greater than Score_NonCreditworthy, the person was labeled as “Creditworthy”.

A formula tool was then used to find the actual number of “Creditworthy” applicants, using the criteria above. And finally, a SUMMARIZE tool was used to aggregate the number of CREDITWORTHY applicants, which was found to be 408.

Thus, 408 new customers would qualify for a loan.
