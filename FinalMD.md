Practical ML final project
================

Remove existing memory and load packages:

``` r
rm(list=ls())
#install.packages("caret")
library(caret)
```

    ## Loading required package: lattice

    ## Loading required package: ggplot2

Read input data and preset **NUL*L* and **NA\*\* values to **NA**

``` r
datain <- read.csv(file='https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv',
                     na.strings=c("","NA"))
validation  <- read.csv(file='https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv',
                     na.strings=c("","NA"))
dim(datain)
```

    ## [1] 19622   160

``` r
dim(validation)
```

    ## [1]  20 160

There are many blank and NA values. The first step is to eliminate these data to have a clean set of input The first 7 columns are eliminated as these are personal data without having using equipment. The last column (160) is the **classe** data (predicted variable) I eliminate blank and NA values from column 8-159 only

``` r
colnan=NULL
for (i in 8:159){
  p1 <- sum(is.na(datain[,i])==TRUE)/dim(datain)[1]*100
  if (p1>95){colnan=c(colnan,i)}
}
colnan <- c(1:7,colnan)
#New data
  newdata  <- datain[,-colnan]
  newvalid <- validation[,-colnan]
  names(newdata)
```

    ##  [1] "roll_belt"            "pitch_belt"           "yaw_belt"            
    ##  [4] "total_accel_belt"     "gyros_belt_x"         "gyros_belt_y"        
    ##  [7] "gyros_belt_z"         "accel_belt_x"         "accel_belt_y"        
    ## [10] "accel_belt_z"         "magnet_belt_x"        "magnet_belt_y"       
    ## [13] "magnet_belt_z"        "roll_arm"             "pitch_arm"           
    ## [16] "yaw_arm"              "total_accel_arm"      "gyros_arm_x"         
    ## [19] "gyros_arm_y"          "gyros_arm_z"          "accel_arm_x"         
    ## [22] "accel_arm_y"          "accel_arm_z"          "magnet_arm_x"        
    ## [25] "magnet_arm_y"         "magnet_arm_z"         "roll_dumbbell"       
    ## [28] "pitch_dumbbell"       "yaw_dumbbell"         "total_accel_dumbbell"
    ## [31] "gyros_dumbbell_x"     "gyros_dumbbell_y"     "gyros_dumbbell_z"    
    ## [34] "accel_dumbbell_x"     "accel_dumbbell_y"     "accel_dumbbell_z"    
    ## [37] "magnet_dumbbell_x"    "magnet_dumbbell_y"    "magnet_dumbbell_z"   
    ## [40] "roll_forearm"         "pitch_forearm"        "yaw_forearm"         
    ## [43] "total_accel_forearm"  "gyros_forearm_x"      "gyros_forearm_y"     
    ## [46] "gyros_forearm_z"      "accel_forearm_x"      "accel_forearm_y"     
    ## [49] "accel_forearm_z"      "magnet_forearm_x"     "magnet_forearm_y"    
    ## [52] "magnet_forearm_z"     "classe"

Define new training and new testing data based on the newly created data set

``` r
inTrain  <- createDataPartition(y=newdata$classe,p=0.7,list=FALSE)
  
training <- newdata[inTrain,]
testing  <- newdata[-inTrain,]
dim(training)
```

    ## [1] 13737    53

``` r
dim(testing)
```

    ## [1] 5885   53

Fit with Regressive Partitioning and Regression trees (rpart package)
---------------------------------------------------------------------

``` r
#install.packages("rpart")
library(rpart)
modFit <- rpart(classe~.,data=training,method="class")
```

Apply to testing set:

``` r
output <- predict(modFit,newdata=testing,type="class")
```

Quantify the output with testing data

``` r
length(output)
```

    ## [1] 5885

``` r
length(testing$classe)
```

    ## [1] 5885

``` r
confusionMatrix(output,testing$classe)
```

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction    A    B    C    D    E
    ##          A 1477  168    7   38   39
    ##          B   68  681  113   80  109
    ##          C   50  155  795  101   87
    ##          D   67   80   77  666   86
    ##          E   12   55   34   79  761
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.7443          
    ##                  95% CI : (0.7329, 0.7554)
    ##     No Information Rate : 0.2845          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.6763          
    ##  Mcnemar's Test P-Value : < 2.2e-16       
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: A Class: B Class: C Class: D Class: E
    ## Sensitivity            0.8823   0.5979   0.7749   0.6909   0.7033
    ## Specificity            0.9402   0.9220   0.9191   0.9370   0.9625
    ## Pos Pred Value         0.8543   0.6480   0.6692   0.6824   0.8087
    ## Neg Pred Value         0.9526   0.9053   0.9508   0.9393   0.9351
    ## Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839
    ## Detection Rate         0.2510   0.1157   0.1351   0.1132   0.1293
    ## Detection Prevalence   0.2938   0.1786   0.2019   0.1658   0.1599
    ## Balanced Accuracy      0.9112   0.7600   0.8470   0.8139   0.8329

The results from rpart model has acceptable accuracy at 0.73 with small p\_value. However, I will try using another method introduced in the class, which is better for this kind of problem, the method is **random forest**

Application of Random Forest
----------------------------

Here I apply the randomforest packages as in my experiment with **caret** package, the randomforest function took much longer computation time.

``` r
#install.packages("randomForest")
library(randomForest)
```

    ## randomForest 4.6-14

    ## Type rfNews() to see new features/changes/bug fixes.

    ## 
    ## Attaching package: 'randomForest'

    ## The following object is masked from 'package:Biobase':
    ## 
    ##     combine

    ## The following object is masked from 'package:BiocGenerics':
    ## 
    ##     combine

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     margin

``` r
modFitRf <- randomForest(classe~., method="class",data=training)
```

Get the output for testing set

``` r
outputRf <- predict(modFitRf,newdata = testing,type="class")
confusionMatrix(outputRf,testing$classe)
```

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction    A    B    C    D    E
    ##          A 1672    8    0    0    0
    ##          B    1 1129    6    0    0
    ##          C    0    2 1020   17    1
    ##          D    0    0    0  946    1
    ##          E    1    0    0    1 1080
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.9935          
    ##                  95% CI : (0.9911, 0.9954)
    ##     No Information Rate : 0.2845          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.9918          
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: A Class: B Class: C Class: D Class: E
    ## Sensitivity            0.9988   0.9912   0.9942   0.9813   0.9982
    ## Specificity            0.9981   0.9985   0.9959   0.9998   0.9996
    ## Pos Pred Value         0.9952   0.9938   0.9808   0.9989   0.9982
    ## Neg Pred Value         0.9995   0.9979   0.9988   0.9964   0.9996
    ## Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839
    ## Detection Rate         0.2841   0.1918   0.1733   0.1607   0.1835
    ## Detection Prevalence   0.2855   0.1930   0.1767   0.1609   0.1839
    ## Balanced Accuracy      0.9985   0.9949   0.9950   0.9906   0.9989

Obviously the Random Forest approach performs better than rpart. It has higher accuracy ~0.99 (with small p\_value). I will go ahead and predict the validation data with random forest approach \#\#Validation data

``` r
dim(newvalid)
```

    ## [1] 20 53

``` r
outputValid <- predict(modFitRf,newdata=newvalid[,-53],type="class")
outputValid
```

    ##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
    ##  B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
    ## Levels: A B C D E
