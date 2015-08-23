
Collect, work with, and clean a data set
Prepare tidy data that can be used for later analysis. 
You will be required to submit: 
1) a tidy data set as described below, 
2) a link to a Github repository with your script for performing the analysis
3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. 
4) You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.  

Wearable computing: Accelerometers from the Samsung Galaxy S smartphone.

https:////d396qusza40orc.cloudfront.net//getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip 

You should create one R script called run_analysis.R that does the following. 
Merges the training and the test sets to create one data set.
Extracts only the measurements on the mean and standard deviation for each measurement. 
Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names. 
From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

 Each feature vector is a row on the text file
 'activity_labels.txt': Links the class labels with their activity name
 'train//subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30
 Triaxial acceleration from the accelerometer (total_acc)
 Estimated body acceleration (body_acc_)
 Triaxial Angular velocity (body_gyro)
 Number of Features = 561 (features)

url<-'https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip'
download.file(url,destfile='accelerometersData.zip')
unzip('accelerometersData.zip')

#### Feature Vector mapping
features <- read.table('UCI HAR Dataset//features.txt',sep=' ')

#### Part1: Merges the training and the test sets to create one data set.
##### Approach: Creating another folder which reads the text files and writes the files in a new folder mergedData
trainFiles<-list.files('UCI HAR Dataset//train//Inertial Signals//')
testFiles<-list.files('UCI HAR Dataset//test//Inertial Signals//')
ii <- 1
dir.create('UCI HAR Dataset//mergedData//')
dir.create('UCI HAR Dataset//mergedData//Inertial Signals')
while(ii <= length(trainFiles)){
  print(file.path('UCI HAR Dataset//train//Inertial Signals',trainFiles[ii]))
  train = read.table(file.path('UCI HAR Dataset//train//Inertial Signals',trainFiles[ii]),sep='')
  print(file.path('UCI HAR Dataset//test//Inertial Signals',testFiles[ii]))
  test = read.table(file.path('UCI HAR Dataset//test//Inertial Signals',testFiles[ii]),sep='')
  mergedDataSet =  rbind(train,test)
  path<-file.path('UCI HAR Dataset/mergedData/Inertial Signals',trainFiles[ii])
  write.table(mergedDataSet,file=paste(substr(path,1,nchar(path)-4),testFiles[ii],sep='_'),row.names=FALSE)
  ii <- ii+1
}

trainFiles<-list.files("UCI HAR Dataset/train/")
testFiles<-list.files("UCI HAR Dataset/test/")
ii<-2
while(ii <= length(trainFiles)){
  print(trainFiles[ii])
  train = read.table(file.path('UCI HAR Dataset/train/',trainFiles[ii],sep=""))
  print(testFiles[ii])
  test = read.table(file.path('UCI HAR Dataset/test/',testFiles[ii],sep=""))
  mergedDataSet =  rbind(train,test)
  path<-file.path('UCI HAR Dataset/mergedData/',trainFiles[ii])
  write.table(mergedDataSet,file=paste(substr(path,1,nchar(path)-4),testFiles[ii],sep="_"),row.names=FALSE)
  ii <- ii+1
}

##### Appended X_test_train which has 561 feature vector as Columns and each row describes activity label
mergedData<-read.table('UCI HAR Dataset/mergedData/X_train_X_test.txt',sep=' ')
colnames(mergedData)<- features[,2]

####Part2: Extracts only the measurements on the mean and standard deviation for each measurement. 
##### Approach: find all the column names with "mean" and "std" strings and patch the mergedData with only these columns

stdCol <- grep("std()",names(mergedData))
meanCol <- grep("mean()",names(mergedData))

mergedData <- mergedData[,sort(c(stdCol,meanCol))]

####Part 3: Uses descriptive activity names to name the activities in the data set
##### Approach: use activity_labels.txt and replace the activity ids in y_train_y_test.txt with the activity label mapping
ACTIVITYLABEL <- read.table("UCI HAR Dataset/activity_labels.txt",sep=" ")
yActivityLabel <- read.table('UCI HAR Dataset/mergedData/y_train_y_test.txt',sep=' ')
yActivityLabel<- with(yActivityLabel, replace(yActivityLabel, yActivityLabel== toString(ACTIVITYLABEL[1,1]),toString(ACTIVITYLABEL[1,2])))
yActivityLabel<- with(yActivityLabel, replace(yActivityLabel, yActivityLabel== toString(ACTIVITYLABEL[2,1]),toString(ACTIVITYLABEL[2,2])))
yActivityLabel<- with(yActivityLabel, replace(yActivityLabel, yActivityLabel== toString(ACTIVITYLABEL[3,1]),toString(ACTIVITYLABEL[3,2])))
yActivityLabel<- with(yActivityLabel, replace(yActivityLabel, yActivityLabel== toString(ACTIVITYLABEL[4,1]),toString(ACTIVITYLABEL[4,2])))
yActivityLabel<- with(yActivityLabel, replace(yActivityLabel, yActivityLabel== toString(ACTIVITYLABEL[5,1]),toString(ACTIVITYLABEL[5,2])))
yActivityLabel<- with(yActivityLabel, replace(yActivityLabel, yActivityLabel== toString(ACTIVITYLABEL[6,1]),toString(ACTIVITYLABEL[6,2])))

#### Subject mapping
subject <- read.table('UCI HAR Dataset/mergedData/subject_train_subject_test.txt')

####Part 4: Appropriately label the data set with descriptive variable names.
#####Approach: Get appropriate column names and write them to each individual column: Activity subject Mean and Std Columns. Finally create tidyDataSet
colnames(mergedData)<- features[sort(c(stdCol,meanCol)),2]
colnames(yActivityLabel) <- "activityLabel"
colnames(subject)<-'subjectNumber'

#### Create Tidy DataSet
tidyDataSet <- cbind(subject,yActivityLabel,mergedData)
write.table(tidyDataSet,'UCI HAR Dataset//mergedData//tidyDataSet1_train_test.txt',row.names=FALSE)

#### Part5: From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
tidyDataSet2<-aggregate(. ~subjectNumber + activityLabel, tidyDataSet, mean)
write.table(tidyDataSet2,'UCI HAR Dataset//mergedData//tidyDataSet2_train_test_Avg-Subj-Act.txt',row.names=FALSE)