# tidydataassignment
class project for getting and leaning data coursers course
The data and description of data used can be found at: https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip 

# The goal of the script is take this data to: 

* Merge the training and the test sets to create one data set.
* Extract only the measurements on the mean and standard deviation for each measurement. 
* Use descriptive activity names to name the activities in the data set
* Appropriately label the data set with descriptive variable names. 
* Create a second, independent tidy data set with the average of each variable for each activity and each subject.

# Description of step taken to clean up UCI HSAR data set with associated code

# Merge subjects, activity, and feature measurements as columns
sub_test <- read.table("./test/subject_test.txt")
x_test <- read.table("./test/x_test.txt")
y_test <- read.table("./test/y_test.txt")
sub_train <- read.table("./train/subject_train.txt")
x_train <- read.table("./train/x_train.txt")
y_train <- read.table("./train/y_train.txt")
test <- cbind(sub_test, y_test, x_test)
train <- cbind(sub_train, y_train, x_train)

# Merge test and train set as rows
combined <- rbind(test, train)

# Read in features as character vector
features <- read.table("./features.txt")
features <- as.vector(features[,2])
features <- make.names(features, unique=TRUE)

# Add column names
colnames(combined) <- c("subjects", "activity", features)

# Extract subjects, activity, and columns with measurements of meand and standard deviation
library(dplyr)
comb_subset <- select(combined, subjects, activity, contains("mean"), contains("std"), -contains("meanFreq"))

# Add activity labels to values in column 2
activity_labels <- read.table("./activity_labels.txt")
comb_subset[,2] <- activity_labels[comb_subset[,2], 2]

# Clean up column names to be more descriptive
names(comb_subset) <- gsub("\\.", " ", names(comb_subset))
names(comb_subset) <- gsub("\\  ", "", names(comb_subset))
names(comb_subset) <- gsub("^t", "Time", names(comb_subset))
names(comb_subset) <- gsub("^f", "Freq", names(comb_subset))
names(comb_subset) <- gsub("^a", "A", names(comb_subset))
names(comb_subset) <- gsub("tbody", "TimeBody", names(comb_subset))
names(comb_subset) <- gsub("gravity", "Gravity", names(comb_subset))
names(comb_subset) <- gsub("MeanGravity", "Mean Gravity", names(comb_subset))

# Determine means for all features sorted by subjects and activity
means <- comb_subset %>% group_by(subjects, Activity) %>% summarise_each(funs(mean))

# Write to table in txt
write.table(means, "means.txt", row.name=FALSE)

# Read txt table and confirm expected structure
meanstable <- read.table("means.txt", header=TRUE)
str(meanstable)


