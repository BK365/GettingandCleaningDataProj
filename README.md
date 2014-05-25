GettingandCleaningDataProj
==========================
README
========================================================

This script works by reading the xtrain/ytrain and xtest/ytest tables.  The script then sets the variable names of ytrain/ytest, reads the features file, then sets xtest/xtrain variable names equal to the data in features.  xtrain/xtest are then subset to only have columns with "mean" and "std" in the name.  Subjecttrain/subjecttest are then read and combined with ytrain/ytest.  The two column table is then bound to xtrain/xtest.  The two tables are then combined.  This table is then melted and recast to average the variables by subject and activity.

```{r}
##Read data
library(reshape2)
xtrain <- read.table("UCI HAR Dataset/train/X_train.txt") 
ytrain <- read.table("UCI HAR Dataset/train/y_train.txt")

xtest <- read.table("UCI HAR Dataset/test/X_test.txt")
ytest <- read.table("UCI HAR Dataset/test/y_test.txt")

##Set activity names
names(ytrain) <- "activity"
names(ytest) <- "activity"

#Get features names and set xtrain/xtest names
features <- read.table("UCI HAR Dataset/features.txt")
features <- features[2]
names(xtrain) <- features[1:561,]
names(xtest) <- features[1:561,]

##subset xtrain and xtest for mean and std
xtrainmean <- xtrain[ , grepl("mean" , names(xtrain))]
xtrainstd <- xtrain[ , grepl("std" , names(xtrain))]
xtrain <- cbind(xtrainmean,xtrainstd)

xtestmean <- xtest[ , grepl("mean" , names(xtest))]
xteststd <- xtest[ , grepl("std" , names(xtest))] 
xtest <- cbind(xtestmean,xteststd)

##get the subjects                  
subjecttest <- read.table("UCI HAR Dataset/test/subject_test.txt")
subjecttrain <- read.table("UCI HAR Dataset/train/subject_train.txt")xtrain[1]
names(subjecttrain) <- c("subject")
names(subjecttest) <- c("subject")

#add descriptive activity labels
ytrain[ytrain$V1 ==1 ,] <- "WALKING"
ytrain[ytrain$V1 ==2 ,] <- "WALKING UPSTAIRS"
ytrain[ytrain$V1 ==3 ,] <- "WALKING DOWNSTAIRS"
ytrain[ytrain$V1 ==4 ,] <- "SITTING"
ytrain[ytrain$V1 ==5 ,] <- "STANDING"
ytrain[ytrain$V1 ==6 ,] <- "LAYING"

ytest[ytrain$V1 ==1 ,] <- "WALKING"
ytest[ytrain$V1 ==2 ,] <- "WALKING UPSTAIRS"
ytest[ytrain$V1 ==3 ,] <- "WALKING DOWNSTAIRS"
ytest[ytrain$V1 ==4 ,] <- "SITTING"
ytest[ytrain$V1 ==5 ,] <- "STANDING"
ytest[ytrain$V1 ==6 ,] <- "LAYING"

##add the train and test tables together
xytrain <- cbind(subjecttrain,ytrain)
xytrain <- cbind(xytrain,xtrain)
xytest <- cbind(subjecttest,ytest)
xytest <- cbind(xytest,xtest)

xycombo <- rbind(xytrain,xytest)

##melt and recast the tidy data
xytidy<-melt(xycombo, id=c("subject", "activity"))
xytidy<-dcast(xytidy, subject + activity ~ variable, fun.aggregate=mean)
```

**Tidy set:**

```{r fig.width=7, fig.height=6}
xytidy
```
