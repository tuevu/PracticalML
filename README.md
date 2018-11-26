# PracticalML
Final Project for Practical Machine Learning from Coursera

In this project, the goal is using data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways.
The goal of this project is to predict the manner in which they did the exercise. This is the "classe" variable in the training set. Create a report describing how to built model, how to use cross validation, what is the expected out of sample error is. Finally use the prediction model to predict 20 different test cases.

Overall information: 

Training data has 19622 rows and 160 columns.
Testing data only has 20 objects and 160 columns. Therefore I call it validation.
The training set will be splitted into newtraining and newtesting set using bootstraping approach with 70% and 30% allocation, respectively.
The variable “classes” is the predicted variables and consists of 5 classes: "A, B, C, D, E":
- exactly according to the specification (Class A)
- throwing the elbows to the front (Class B)
- lifting the dumbbell only halfway (Class C)
- lowering the dumbbell only halfway (Class D)
- throwing the hips to the front (Class E)

**Steps**
-	The first 6 variables are taken off from the list of predictor (which are X, username, raw time step and date, numwindow)
-	All The predictors consisting of blank and NA values are taken off due to not enough values using 95% threshold
-	The newtraining and newtesting are created with 52 predictors and 1 predicted variables (**classe**)
-	The cross validation in this study is by random sampling
-	As there are 5 different classes, the best approach is to be predicting with trees. I use two models here which are **Regressive Partitioning and Regression trees** and **Random Forest**
-	The 20 test cases are referred to validation
