Practical ML final project
================

GitHub Documents
----------------

This is an R Markdown format used for publishing markdown documents to GitHub. When you click the **Knit** button all R code chunks are run and a markdown file (.md) suitable for publishing to GitHub is generated.

Including Code
--------------

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
set.seed(5282)
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
    ##          A 1494  252   15   82   43
    ##          B   38  644   97   40   78
    ##          C   50  112  822  151  137
    ##          D   65   83   54  604   61
    ##          E   27   48   38   87  763
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.7353          
    ##                  95% CI : (0.7238, 0.7465)
    ##     No Information Rate : 0.2845          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.6638          
    ##  Mcnemar's Test P-Value : < 2.2e-16       
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: A Class: B Class: C Class: D Class: E
    ## Sensitivity            0.8925   0.5654   0.8012   0.6266   0.7052
    ## Specificity            0.9069   0.9467   0.9074   0.9466   0.9584
    ## Pos Pred Value         0.7922   0.7179   0.6462   0.6967   0.7923
    ## Neg Pred Value         0.9550   0.9008   0.9558   0.9283   0.9352
    ## Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839
    ## Detection Rate         0.2539   0.1094   0.1397   0.1026   0.1297
    ## Detection Prevalence   0.3205   0.1524   0.2161   0.1473   0.1636
    ## Balanced Accuracy      0.8997   0.7561   0.8543   0.7866   0.8318

Plotting result

``` r
library(rattle)
```

    ## Rattle: A free graphical interface for data science with R.
    ## Version 5.2.0 Copyright (c) 2006-2018 Togaware Pty Ltd.
    ## Type 'rattle()' to shake, rattle, and roll your data.

``` r
fancyRpartPlot(modFit)
```

    ## Warning: labs do not fit even at cex 0.15, there may be some overplotting

![](FinalMD_files/figure-markdown_github/unnamed-chunk-8-1.png) The results from rpart model has acceptable accuracy at 0.73 with small p\_value. However, I will try using another method introduced in the class, which is better for this kind of problem, the method is **random forest**

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

    ## The following object is masked from 'package:rattle':
    ## 
    ##     importance

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
set.seed(5282)
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
    ##          A 1674    0    0    0    0
    ##          B    0 1137    7    0    0
    ##          C    0    2 1019    8    0
    ##          D    0    0    0  955    3
    ##          E    0    0    0    1 1079
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.9964          
    ##                  95% CI : (0.9946, 0.9978)
    ##     No Information Rate : 0.2845          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.9955          
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: A Class: B Class: C Class: D Class: E
    ## Sensitivity            1.0000   0.9982   0.9932   0.9907   0.9972
    ## Specificity            1.0000   0.9985   0.9979   0.9994   0.9998
    ## Pos Pred Value         1.0000   0.9939   0.9903   0.9969   0.9991
    ## Neg Pred Value         1.0000   0.9996   0.9986   0.9982   0.9994
    ## Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839
    ## Detection Rate         0.2845   0.1932   0.1732   0.1623   0.1833
    ## Detection Prevalence   0.2845   0.1944   0.1749   0.1628   0.1835
    ## Balanced Accuracy      1.0000   0.9984   0.9956   0.9950   0.9985

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
