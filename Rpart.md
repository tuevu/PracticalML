Remove existing memory and load packages

```
rm(list=ls())
library(caret)
library(rpart)
```

Read input data and preset *NULL* and *NA* values to *NA*

```
datain <- read.csv(file='https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv',
                     na.strings=c("","NA"))
validation  <- read.csv(file='https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv',
                     na.strings=c("","NA"))
```
