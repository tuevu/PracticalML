Remove existing memory and load packages

```
rm(list=ls())
library(caret)
library(rpart)
```

Read input data and preset **NUL*L* and **NA** values to **NA**

```
datain <- read.csv(file='https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv',
                     na.strings=c("","NA"))
validation  <- read.csv(file='https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv',
                     na.strings=c("","NA"))
dim(datain)
dim(validation)
```


There are many blank and NA values. The first step is to eliminate these data to have a clean set of input
The first 6 columns are eliminated as these are personal data without having using equipment.
The last column (160) is the **classe** data (predicted variable)
I eliminate blank and NA values from column 7-159 only

```
colnan=NULL
for (i in 7:159){
  p1 <- sum(is.na(datain[,i])==TRUE)/dim(datain)[1]*100
  if (p1>95){colnan=c(colnan,i)}
}
colnan <- c(1:6,colnan)
#New data
  newdata  <- datain[,-colnan]
  newvalid <- validation[,-colnan]
```

Define new training and new testing data based on the newly created data set

```
inTrain  <- createDataPartition(y=newdata$classe,p=0.7,list=FALSE)
  
training <- newdata[inTrain,]
testing  <- newdata[-inTrain,]
```

Fit with Regressive Partitioning and Regression trees (rpart)

```
modFit <- rpart(classe~.,data=training,method="class")
output <- predict(modFit,testing,type="class")
  
testresult <- predict(modFit,newdata = testing)
confusionMatrix(testresult,testing$classe)
```

***
sss
***
