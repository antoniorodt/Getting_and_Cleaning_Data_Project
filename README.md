
## Create Directory
## Set As Working Directory
## Set Proxy
## Download Zip File and extract
   fileUrl<-"https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
   download.file(fileUrl,destfile = "dataset.zip",method="libcurl") 
   DateDownLoad<-date()

## Install Package
install.packages("data.table")
install.packages("reshape2")

##Load  X_test  y_test
X_test <- read.table("./dataset/UCI HAR Dataset/test/X_test.txt", sep = "")
y_test <- read.table("./dataset/UCI HAR Dataset/test/X_test.txt", sep = "")
subject_test <- read.table("./dataset/UCI HAR Dataset/test/subject_test.txt", sep = "")

##Load  X_train  y_train
X_train <- read.table("./dataset/UCI HAR Dataset/train/X_train.txt", sep = "")
y_train <- read.table("./dataset/UCI HAR Dataset/train/X_train.txt", sep = "")
subject_train <- read.table("./dataset/UCI HAR Dataset/train/subject_train.txt", sep = "")

##Load column names
features <- read.table("./dataset/UCI HAR Dataset/features.txt")[,2]

## assign features X_test  X_train
names(X_test) = features
names(X_train) = features

## 2. Extracts only the measurements on the mean and standard deviation for each measurement.
extract_features <- grepl("mean|std", features)

##extract only mean and std of features
X_test = X_test[,extract_features]
X_train = X_train[,extract_features]

# Load: activity labels
activity_labels <- read.table("./dataset/UCI HAR Dataset/activity_labels.txt")[,2]
# Load activity labels
y_test[,2] = activity_labels[y_test[,1]]
names(y_test) = c("Activity_ID", "Activity_Label")
names(subject_test) = "subject"

y_train[,2] = activity_labels[y_train[,1]]
names(y_train) = c("Activity_ID", "Activity_Label")
names(subject_train) = "subject"

require("data.table")
test_data <- cbind(as.data.table(subject_test), y_test, X_test)
train_data <- cbind(as.data.table(subject_train), y_train, X_train)

##1. Merges the training and the test sets to create one data set.
data = rbind(test_data, train_data)

id_labels   = c("subject", "Activity_ID", "Activity_Label")
data_labels = setdiff(colnames(data), id_labels)
tidy_data = dcast(melt_data, subject + Activity_Label ~ variable, mean)
require("reshape2")
melt_data      = melt(data, id = id_labels, measure.vars = data_labels)
tidy_data = dcast(melt_data, subject + Activity_Label ~ variable, mean)
write.table(tidy_data, file = "./tidy_data.txt")
