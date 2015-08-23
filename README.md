#### Part1: Merges the training and the test sets to create one data set.
##### Approach: Creating another folder which reads the text files and writes the files in a new folder mergedData

#### Feature Vector mapping
features

#### Merge test and train and write under UCI HAR Dataset/
mergedDataSet in for loop


##### Appended X_test_train which has 561 feature vector as Columns and each row describes activity label
mergedData<- "X_train_X_test.txt" and column names = features

####Part2: Extracts only the measurements on the mean and standard deviation for each measurement. 
##### Approach: find all the column names with "mean" and "std" strings and patch the mergedData with only these columns

find stdCol and meanCol 

rewrite the mergeData using above stdCol and meanCol

####Part 3: Uses descriptive activity names to name the activities in the data set
##### Approach: use activity_labels.txt and replace the activity ids in y_train_y_test.txt with the activity label mapping
use ACTIVITYLABEL from activity_label.txt and find the equivalent labels for y_train_y_test.txt yActivityLabel


####Part 4: Appropriately label the data set with descriptive variable names.
#####Approach: Get appropriate column names and write them to each individual column: Activity subject Mean and Std Columns. Finally create tidyDataSet

#### Subject mapping
subject <- subject_train_subject_test.txt

#### Patching column names from feature vector names

colnames(mergedData)<- features[sort(c(stdCol,meanCol)),2]
colnames(yActivityLabel) <- "activityLabel"
colnames(subject)<-'subjectNumber'


#### Create Tidy DataSet
tidyDataSet <- subject,yActivityLabel,mergedData
tidyDataSet1_train_test.txt

#### Part5: From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
tidyDataSet2<-aggregate(. ~subjectNumber + activityLabel, tidyDataSet, mean)
tidyDataSet2_train_test_Avg-Subj-Act.txt'