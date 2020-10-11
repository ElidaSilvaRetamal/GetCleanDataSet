README
================

Élida Silva Retamal

Peer-graded Assignment: Getting and Cleaning Data Course Project The
purpose of the **Getting and Ceaning Data Course Project**, from the
Coursera online platform, offered by Johns Hopkins University, aims to
assess our ability to collect, work and clean a data set, in addition to
verifying our ability to write a CodeBook and create a repository on
GitHub.

Meeting the requirements of the evaluation criteria, we produce the
following documents:

  - An R script with the name **run\_analysis.R**.
  - A **README.md** in the repository with the scripts, explaining how
    they work and are connected.
  - A code book describing the variables and transformations we did
    called **CodeBook.md**.

##### The R script was created according to the rules provided for the project, that are:

##### Create one R script called run\_analysis.R that does the following:

  - Step 1: Merges the training and the test sets to create one data
    set.
  - Step 2: Extracts only the measurements on the mean and standard
    deviation for each measurement.
  - Step 3: Uses descriptive activity names to name the activities in
    the data set
  - Step 4: Appropriately labels the data set with descriptive variable
    names.
  - Step 5: From the data set in step 4, creates a second, independent
    tidy data set with the average of each variable for each activity
    and each subject.

\=========================================================================

#### Human Activity Recognition (HAR) Using Smartphones Dataset:

\=========================================================================

### Libraries:

  - library(Hmisc)
  - library(tidyverse)
  - library(data.table)
  - library(Hmisc)
  - library(tidyverse)
  - library(data.table)

#### Step 0. Getting, downloading, unzipping and reading dataset

#### 0.1 - Getting the directory for the data:

``` r
if (!file.exists("data")) {
    dir.create("data")
}
```

#### 0.2 - Dowloading the file:

``` r
fileUrl <-"https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl, destfile = "./data/df1.zip")
```

#### 0.3 Unzip data set to data directory

``` r
unzip(zipfile = "./data/df1.zip", exdir = "./data")
```

#### 0.4 Reading files and assigning column names:

#### 0.4.1 features:

``` r
features <- read.table('./data/UCI HAR Dataset/features.txt', col.names = c("identifier", "signals"))
```

#### 0.4.2 activity\_labels

``` r
activities<- read.table("./data/UCI HAR Dataset/activity_labels.txt", col.names = c("activity","labels"), stringsAsFactors = TRUE)
```

``` r
glimpse(activities)
```

    ## Rows: 6
    ## Columns: 2
    ## $ activity <int> 1, 2, 3, 4, 5, 6
    ## $ labels   <fct> WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, ST...

#### 0.4.3 subject\_train, X\_train and y\_train

``` r
subject_train <- read.table("./data/UCI HAR Dataset/train/subject_train.txt", col.names = "subject")

X_train <- read.table("./data/UCI HAR Dataset/train/X_train.txt", col.names = features$signals)

y_train <- read.table("./data/UCI HAR Dataset/train/y_train.txt", col.names = "activity")
```

#### 0.4.4 subject\_test, X\_test and y\_test

``` r
subject_test <- read.table("./data/UCI HAR Dataset/test/subject_test.txt", col.names = "subject")

X_test <- read.table("./data/UCI HAR Dataset/test/X_test.txt", col.names = features$signals)

y_test <- read.table("./data/UCI HAR Dataset/test/y_test.txt", col.names = "activity" )
```

#### 0.4.5 Size data set - Number rows and columns.

``` r
dim(X_train) 
```

    ## [1] 7352  561

``` r
dim(subject_train) 
```

    ## [1] 7352    1

``` r
dim(X_test) 
```

    ## [1] 2947  561

``` r
dim(y_test) 
```

    ## [1] 2947    1

``` r
dim(subject_test) 
```

    ## [1] 2947    1

``` r
dim(activities)
```

    ## [1] 6 2

``` r
dim(features) 
```

    ## [1] 561   2

#### Step 1. Merges the training and the test sets to create one data set.

``` r
my <- rbind(y_train, y_test); dim(my)
```

    ## [1] 10299     1

``` r
mX <- rbind(X_train, X_test); dim(mX)
```

    ## [1] 10299   561

``` r
msubject <- rbind(subject_train,subject_test); dim(msubject)
```

    ## [1] 10299     1

``` r
merge_dataset <- cbind(my, mX, msubject); dim(merge_dataset)
```

    ## [1] 10299   563

#### Step 2. Extracts only the measurements on the mean and standard deviation for each measurement.

#### 2.1 Tidy dataset:

``` r
data_tidy <- merge_dataset %>%
    select(subject,
           activity,
           contains("mean"),
           contains("std"))
dim(data_tidy)
```

    ## [1] 10299    88

#### Step 3. Uses descriptive activity names to name the activities in the data set.

``` r
data_tidy$activity <- activities[data_tidy$activity, 2]
```

#### Step 4. Appropriately labels the data set with descriptive variable names.

#### 4.1 Names that will be replaced by descriptive names:

  - t: Time
  - f: Frequency
  - Freq..: Frequency
  - Acc: Accelerometer
  - Gyro: Gyroscope
  - Mag: Magnitude
  - BodyBody: Body
  - gravity: Gravity
  - mean: Mean

<!-- end list -->

``` r
names(data_tidy) <- gsub(("Freq|^f"),"Frequency",names(data_tidy), ignore.case = TRUE)
names(data_tidy) <- gsub("^t","Time",names(data_tidy))
names(data_tidy) <- gsub("tBody","TimeBody",names(data_tidy), ignore.case = TRUE)
names(data_tidy) <- gsub("Acc","Accelerometer",names(data_tidy))
names(data_tidy) <- gsub("Mag","Magnitude",names(data_tidy))
names(data_tidy) <- gsub("gravity","Gravity",names(data_tidy))
names(data_tidy) <- gsub("Gyro","Gyroscope",names(data_tidy))
names(data_tidy) <- gsub("BodyBody","Body",names(data_tidy))
names(data_tidy) <- gsub("mean", "Mean", names(data_tidy))
```

#### 4.2 After changing data\_tidy names:

``` r
glimpse(data_tidy)
```

    ## Rows: 10,299
    ## Columns: 88
    ## $ subject                                                 <int> 1, 1, 1, 1,...
    ## $ activity                                                <fct> STANDING, S...
    ## $ TimeBodyAccelerometer.Mean...X                          <dbl> 0.2885845, ...
    ## $ TimeBodyAccelerometer.Mean...Y                          <dbl> -0.02029417...
    ## $ TimeBodyAccelerometer.Mean...Z                          <dbl> -0.13290514...
    ## $ TimeGravityAccelerometer.Mean...X                       <dbl> 0.9633961, ...
    ## $ TimeGravityAccelerometer.Mean...Y                       <dbl> -0.1408397,...
    ## $ TimeGravityAccelerometer.Mean...Z                       <dbl> 0.115374940...
    ## $ TimeBodyAccelerometerJerk.Mean...X                      <dbl> 0.07799634,...
    ## $ TimeBodyAccelerometerJerk.Mean...Y                      <dbl> 0.005000803...
    ## $ TimeBodyAccelerometerJerk.Mean...Z                      <dbl> -0.06783080...
    ## $ TimeBodyGyroscope.Mean...X                              <dbl> -0.00610084...
    ## $ TimeBodyGyroscope.Mean...Y                              <dbl> -0.03136479...
    ## $ TimeBodyGyroscope.Mean...Z                              <dbl> 0.10772540,...
    ## $ TimeBodyGyroscopeJerk.Mean...X                          <dbl> -0.09916740...
    ## $ TimeBodyGyroscopeJerk.Mean...Y                          <dbl> -0.05551737...
    ## $ TimeBodyGyroscopeJerk.Mean...Z                          <dbl> -0.06198579...
    ## $ TimeBodyAccelerometerMagnitude.Mean..                   <dbl> -0.9594339,...
    ## $ TimeGravityAccelerometerMagnitude.Mean..                <dbl> -0.9594339,...
    ## $ TimeBodyAccelerometerJerkMagnitude.Mean..               <dbl> -0.9933059,...
    ## $ TimeBodyGyroscopeMagnitude.Mean..                       <dbl> -0.9689591,...
    ## $ TimeBodyGyroscopeJerkMagnitude.Mean..                   <dbl> -0.9942478,...
    ## $ FrequencyBodyAccelerometer.Mean...X                     <dbl> -0.9947832,...
    ## $ FrequencyBodyAccelerometer.Mean...Y                     <dbl> -0.9829841,...
    ## $ FrequencyBodyAccelerometer.Mean...Z                     <dbl> -0.9392687,...
    ## $ FrequencyBodyAccelerometer.MeanFrequency...X            <dbl> 0.252482900...
    ## $ FrequencyBodyAccelerometer.MeanFrequency...Y            <dbl> 0.13183575,...
    ## $ FrequencyBodyAccelerometer.MeanFrequency...Z            <dbl> -0.05205025...
    ## $ FrequencyBodyAccelerometerJerk.Mean...X                 <dbl> -0.9923325,...
    ## $ FrequencyBodyAccelerometerJerk.Mean...Y                 <dbl> -0.9871699,...
    ## $ FrequencyBodyAccelerometerJerk.Mean...Z                 <dbl> -0.9896961,...
    ## $ FrequencyBodyAccelerometerJerk.MeanFrequency...X        <dbl> 0.87038451,...
    ## $ FrequencyBodyAccelerometerJerk.MeanFrequency...Y        <dbl> 0.210697000...
    ## $ FrequencyBodyAccelerometerJerk.MeanFrequency...Z        <dbl> 0.26370789,...
    ## $ FrequencyBodyGyroscope.Mean...X                         <dbl> -0.9865744,...
    ## $ FrequencyBodyGyroscope.Mean...Y                         <dbl> -0.9817615,...
    ## $ FrequencyBodyGyroscope.Mean...Z                         <dbl> -0.9895148,...
    ## $ FrequencyBodyGyroscope.MeanFrequency...X                <dbl> -0.25754888...
    ## $ FrequencyBodyGyroscope.MeanFrequency...Y                <dbl> 0.09794711,...
    ## $ FrequencyBodyGyroscope.MeanFrequency...Z                <dbl> 0.54715105,...
    ## $ FrequencyBodyAccelerometerMagnitude.Mean..              <dbl> -0.9521547,...
    ## $ FrequencyBodyAccelerometerMagnitude.MeanFrequency..     <dbl> -0.08843612...
    ## $ FrequencyBodyAccelerometerJerkMagnitude.Mean..          <dbl> -0.9937257,...
    ## $ FrequencyBodyAccelerometerJerkMagnitude.MeanFrequency.. <dbl> 0.3469885, ...
    ## $ FrequencyBodyGyroscopeMagnitude.Mean..                  <dbl> -0.9801349,...
    ## $ FrequencyBodyGyroscopeMagnitude.MeanFrequency..         <dbl> -0.12898890...
    ## $ FrequencyBodyGyroscopeJerkMagnitude.Mean..              <dbl> -0.9919904,...
    ## $ FrequencyBodyGyroscopeJerkMagnitude.MeanFrequency..     <dbl> -0.07432303...
    ## $ angle.TimeBodyAccelerometerMean.Gravity.                <dbl> -0.11275434...
    ## $ angle.TimeBodyAccelerometerJerkMean..GravityMean.       <dbl> 0.030400372...
    ## $ angle.TimeBodyGyroscopeMean.GravityMean.                <dbl> -0.46476139...
    ## $ angle.TimeBodyGyroscopeJerkMean.GravityMean.            <dbl> -0.01844588...
    ## $ angle.X.GravityMean.                                    <dbl> -0.8412468,...
    ## $ angle.Y.GravityMean.                                    <dbl> 0.1799406, ...
    ## $ angle.Z.GravityMean.                                    <dbl> -0.05862692...
    ## $ TimeBodyAccelerometer.std...X                           <dbl> -0.9952786,...
    ## $ TimeBodyAccelerometer.std...Y                           <dbl> -0.9831106,...
    ## $ TimeBodyAccelerometer.std...Z                           <dbl> -0.9135264,...
    ## $ TimeGravityAccelerometer.std...X                        <dbl> -0.9852497,...
    ## $ TimeGravityAccelerometer.std...Y                        <dbl> -0.9817084,...
    ## $ TimeGravityAccelerometer.std...Z                        <dbl> -0.8776250,...
    ## $ TimeBodyAccelerometerJerk.std...X                       <dbl> -0.9935191,...
    ## $ TimeBodyAccelerometerJerk.std...Y                       <dbl> -0.9883600,...
    ## $ TimeBodyAccelerometerJerk.std...Z                       <dbl> -0.9935750,...
    ## $ TimeBodyGyroscope.std...X                               <dbl> -0.9853103,...
    ## $ TimeBodyGyroscope.std...Y                               <dbl> -0.9766234,...
    ## $ TimeBodyGyroscope.std...Z                               <dbl> -0.9922053,...
    ## $ TimeBodyGyroscopeJerk.std...X                           <dbl> -0.9921107,...
    ## $ TimeBodyGyroscopeJerk.std...Y                           <dbl> -0.9925193,...
    ## $ TimeBodyGyroscopeJerk.std...Z                           <dbl> -0.9920553,...
    ## $ TimeBodyAccelerometerMagnitude.std..                    <dbl> -0.9505515,...
    ## $ TimeGravityAccelerometerMagnitude.std..                 <dbl> -0.9505515,...
    ## $ TimeBodyAccelerometerJerkMagnitude.std..                <dbl> -0.9943364,...
    ## $ TimeBodyGyroscopeMagnitude.std..                        <dbl> -0.9643352,...
    ## $ TimeBodyGyroscopeJerkMagnitude.std..                    <dbl> -0.9913676,...
    ## $ FrequencyBodyAccelerometer.std...X                      <dbl> -0.9954217,...
    ## $ FrequencyBodyAccelerometer.std...Y                      <dbl> -0.9831330,...
    ## $ FrequencyBodyAccelerometer.std...Z                      <dbl> -0.9061650,...
    ## $ FrequencyBodyAccelerometerJerk.std...X                  <dbl> -0.9958207,...
    ## $ FrequencyBodyAccelerometerJerk.std...Y                  <dbl> -0.9909363,...
    ## $ FrequencyBodyAccelerometerJerk.std...Z                  <dbl> -0.9970517,...
    ## $ FrequencyBodyGyroscope.std...X                          <dbl> -0.9850326,...
    ## $ FrequencyBodyGyroscope.std...Y                          <dbl> -0.9738861,...
    ## $ FrequencyBodyGyroscope.std...Z                          <dbl> -0.9940349,...
    ## $ FrequencyBodyAccelerometerMagnitude.std..               <dbl> -0.9561340,...
    ## $ FrequencyBodyAccelerometerJerkMagnitude.std..           <dbl> -0.9937550,...
    ## $ FrequencyBodyGyroscopeMagnitude.std..                   <dbl> -0.9613094,...
    ## $ FrequencyBodyGyroscopeJerkMagnitude.std..               <dbl> -0.9906975,...

#### 4.3 Having a look at the data\_tidy:

``` r
kable(head(data_tidy, 5), align = "c", booktabs = TRUE) 
```

<table>

<thead>

<tr>

<th style="text-align:center;">

subject

</th>

<th style="text-align:center;">

activity

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.Mean…X

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.Mean…Y

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.Mean…Z

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.Mean…X

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.Mean…Y

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.Mean…Z

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.Mean…X

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.Mean…Y

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.Mean…Z

</th>

<th style="text-align:center;">

TimeBodyGyroscope.Mean…X

</th>

<th style="text-align:center;">

TimeBodyGyroscope.Mean…Y

</th>

<th style="text-align:center;">

TimeBodyGyroscope.Mean…Z

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.Mean…X

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.Mean…Y

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.Mean…Z

</th>

<th style="text-align:center;">

TimeBodyAccelerometerMagnitude.Mean..

</th>

<th style="text-align:center;">

TimeGravityAccelerometerMagnitude.Mean..

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerkMagnitude.Mean..

</th>

<th style="text-align:center;">

TimeBodyGyroscopeMagnitude.Mean..

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerkMagnitude.Mean..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.Mean…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.Mean…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.Mean…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.MeanFrequency…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.MeanFrequency…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.MeanFrequency…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.Mean…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.Mean…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.Mean…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.MeanFrequency…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.MeanFrequency…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.MeanFrequency…Z

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.Mean…X

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.Mean…Y

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.Mean…Z

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.MeanFrequency…X

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.MeanFrequency…Y

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.MeanFrequency…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerMagnitude.Mean..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerMagnitude.MeanFrequency..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerkMagnitude.Mean..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerkMagnitude.MeanFrequency..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeMagnitude.Mean..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeMagnitude.MeanFrequency..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeJerkMagnitude.Mean..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeJerkMagnitude.MeanFrequency..

</th>

<th style="text-align:center;">

angle.TimeBodyAccelerometerMean.Gravity.

</th>

<th style="text-align:center;">

angle.TimeBodyAccelerometerJerkMean..GravityMean.

</th>

<th style="text-align:center;">

angle.TimeBodyGyroscopeMean.GravityMean.

</th>

<th style="text-align:center;">

angle.TimeBodyGyroscopeJerkMean.GravityMean.

</th>

<th style="text-align:center;">

angle.X.GravityMean.

</th>

<th style="text-align:center;">

angle.Y.GravityMean.

</th>

<th style="text-align:center;">

angle.Z.GravityMean.

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.std…X

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.std…Y

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.std…Z

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.std…X

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.std…Y

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.std…Z

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.std…X

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.std…Y

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.std…Z

</th>

<th style="text-align:center;">

TimeBodyGyroscope.std…X

</th>

<th style="text-align:center;">

TimeBodyGyroscope.std…Y

</th>

<th style="text-align:center;">

TimeBodyGyroscope.std…Z

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.std…X

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.std…Y

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.std…Z

</th>

<th style="text-align:center;">

TimeBodyAccelerometerMagnitude.std..

</th>

<th style="text-align:center;">

TimeGravityAccelerometerMagnitude.std..

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerkMagnitude.std..

</th>

<th style="text-align:center;">

TimeBodyGyroscopeMagnitude.std..

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerkMagnitude.std..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.std…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.std…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.std…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.std…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.std…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.std…Z

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.std…X

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.std…Y

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.std…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerMagnitude.std..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerkMagnitude.std..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeMagnitude.std..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeJerkMagnitude.std..

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:center;">

1

</td>

<td style="text-align:center;">

STANDING

</td>

<td style="text-align:center;">

0.2885845

</td>

<td style="text-align:center;">

\-0.0202942

</td>

<td style="text-align:center;">

\-0.1329051

</td>

<td style="text-align:center;">

0.9633961

</td>

<td style="text-align:center;">

\-0.1408397

</td>

<td style="text-align:center;">

0.1153749

</td>

<td style="text-align:center;">

0.0779963

</td>

<td style="text-align:center;">

0.0050008

</td>

<td style="text-align:center;">

\-0.0678308

</td>

<td style="text-align:center;">

\-0.0061008

</td>

<td style="text-align:center;">

\-0.0313648

</td>

<td style="text-align:center;">

0.1077254

</td>

<td style="text-align:center;">

\-0.0991674

</td>

<td style="text-align:center;">

\-0.0555174

</td>

<td style="text-align:center;">

\-0.0619858

</td>

<td style="text-align:center;">

\-0.9594339

</td>

<td style="text-align:center;">

\-0.9594339

</td>

<td style="text-align:center;">

\-0.9933059

</td>

<td style="text-align:center;">

\-0.9689591

</td>

<td style="text-align:center;">

\-0.9942478

</td>

<td style="text-align:center;">

\-0.9947832

</td>

<td style="text-align:center;">

\-0.9829841

</td>

<td style="text-align:center;">

\-0.9392687

</td>

<td style="text-align:center;">

0.2524829

</td>

<td style="text-align:center;">

0.1318358

</td>

<td style="text-align:center;">

\-0.0520503

</td>

<td style="text-align:center;">

\-0.9923325

</td>

<td style="text-align:center;">

\-0.9871699

</td>

<td style="text-align:center;">

\-0.9896961

</td>

<td style="text-align:center;">

0.8703845

</td>

<td style="text-align:center;">

0.2106970

</td>

<td style="text-align:center;">

0.2637079

</td>

<td style="text-align:center;">

\-0.9865744

</td>

<td style="text-align:center;">

\-0.9817615

</td>

<td style="text-align:center;">

\-0.9895148

</td>

<td style="text-align:center;">

\-0.2575489

</td>

<td style="text-align:center;">

0.0979471

</td>

<td style="text-align:center;">

0.5471510

</td>

<td style="text-align:center;">

\-0.9521547

</td>

<td style="text-align:center;">

\-0.0884361

</td>

<td style="text-align:center;">

\-0.9937256

</td>

<td style="text-align:center;">

0.3469885

</td>

<td style="text-align:center;">

\-0.9801348

</td>

<td style="text-align:center;">

\-0.1289889

</td>

<td style="text-align:center;">

\-0.9919904

</td>

<td style="text-align:center;">

\-0.0743230

</td>

<td style="text-align:center;">

\-0.1127543

</td>

<td style="text-align:center;">

0.0304004

</td>

<td style="text-align:center;">

\-0.4647614

</td>

<td style="text-align:center;">

\-0.0184459

</td>

<td style="text-align:center;">

\-0.8412468

</td>

<td style="text-align:center;">

0.1799406

</td>

<td style="text-align:center;">

\-0.0586269

</td>

<td style="text-align:center;">

\-0.9952786

</td>

<td style="text-align:center;">

\-0.9831106

</td>

<td style="text-align:center;">

\-0.9135264

</td>

<td style="text-align:center;">

\-0.9852497

</td>

<td style="text-align:center;">

\-0.9817084

</td>

<td style="text-align:center;">

\-0.8776250

</td>

<td style="text-align:center;">

\-0.9935191

</td>

<td style="text-align:center;">

\-0.9883600

</td>

<td style="text-align:center;">

\-0.9935750

</td>

<td style="text-align:center;">

\-0.9853103

</td>

<td style="text-align:center;">

\-0.9766234

</td>

<td style="text-align:center;">

\-0.9922053

</td>

<td style="text-align:center;">

\-0.9921107

</td>

<td style="text-align:center;">

\-0.9925193

</td>

<td style="text-align:center;">

\-0.9920553

</td>

<td style="text-align:center;">

\-0.9505515

</td>

<td style="text-align:center;">

\-0.9505515

</td>

<td style="text-align:center;">

\-0.9943364

</td>

<td style="text-align:center;">

\-0.9643352

</td>

<td style="text-align:center;">

\-0.9913676

</td>

<td style="text-align:center;">

\-0.9954218

</td>

<td style="text-align:center;">

\-0.9831330

</td>

<td style="text-align:center;">

\-0.9061650

</td>

<td style="text-align:center;">

\-0.9958207

</td>

<td style="text-align:center;">

\-0.9909363

</td>

<td style="text-align:center;">

\-0.9970517

</td>

<td style="text-align:center;">

\-0.9850326

</td>

<td style="text-align:center;">

\-0.9738861

</td>

<td style="text-align:center;">

\-0.9940349

</td>

<td style="text-align:center;">

\-0.9561340

</td>

<td style="text-align:center;">

\-0.9937550

</td>

<td style="text-align:center;">

\-0.9613094

</td>

<td style="text-align:center;">

\-0.9906975

</td>

</tr>

<tr>

<td style="text-align:center;">

1

</td>

<td style="text-align:center;">

STANDING

</td>

<td style="text-align:center;">

0.2784188

</td>

<td style="text-align:center;">

\-0.0164106

</td>

<td style="text-align:center;">

\-0.1235202

</td>

<td style="text-align:center;">

0.9665611

</td>

<td style="text-align:center;">

\-0.1415513

</td>

<td style="text-align:center;">

0.1093788

</td>

<td style="text-align:center;">

0.0740067

</td>

<td style="text-align:center;">

0.0057711

</td>

<td style="text-align:center;">

0.0293766

</td>

<td style="text-align:center;">

\-0.0161116

</td>

<td style="text-align:center;">

\-0.0838938

</td>

<td style="text-align:center;">

0.1005843

</td>

<td style="text-align:center;">

\-0.1105028

</td>

<td style="text-align:center;">

\-0.0448187

</td>

<td style="text-align:center;">

\-0.0592428

</td>

<td style="text-align:center;">

\-0.9792892

</td>

<td style="text-align:center;">

\-0.9792892

</td>

<td style="text-align:center;">

\-0.9912535

</td>

<td style="text-align:center;">

\-0.9806831

</td>

<td style="text-align:center;">

\-0.9951232

</td>

<td style="text-align:center;">

\-0.9974507

</td>

<td style="text-align:center;">

\-0.9768517

</td>

<td style="text-align:center;">

\-0.9735227

</td>

<td style="text-align:center;">

0.2713085

</td>

<td style="text-align:center;">

0.0428636

</td>

<td style="text-align:center;">

\-0.0143098

</td>

<td style="text-align:center;">

\-0.9950322

</td>

<td style="text-align:center;">

\-0.9813115

</td>

<td style="text-align:center;">

\-0.9897398

</td>

<td style="text-align:center;">

0.6085135

</td>

<td style="text-align:center;">

\-0.0536756

</td>

<td style="text-align:center;">

0.0631483

</td>

<td style="text-align:center;">

\-0.9773867

</td>

<td style="text-align:center;">

\-0.9925300

</td>

<td style="text-align:center;">

\-0.9896058

</td>

<td style="text-align:center;">

\-0.0481674

</td>

<td style="text-align:center;">

\-0.4016079

</td>

<td style="text-align:center;">

\-0.0681783

</td>

<td style="text-align:center;">

\-0.9808566

</td>

<td style="text-align:center;">

\-0.0441499

</td>

<td style="text-align:center;">

\-0.9903355

</td>

<td style="text-align:center;">

0.5320605

</td>

<td style="text-align:center;">

\-0.9882956

</td>

<td style="text-align:center;">

\-0.2719585

</td>

<td style="text-align:center;">

\-0.9958539

</td>

<td style="text-align:center;">

0.1580745

</td>

<td style="text-align:center;">

0.0534770

</td>

<td style="text-align:center;">

\-0.0074346

</td>

<td style="text-align:center;">

\-0.7326262

</td>

<td style="text-align:center;">

0.7035106

</td>

<td style="text-align:center;">

\-0.8447876

</td>

<td style="text-align:center;">

0.1802889

</td>

<td style="text-align:center;">

\-0.0543167

</td>

<td style="text-align:center;">

\-0.9982453

</td>

<td style="text-align:center;">

\-0.9753002

</td>

<td style="text-align:center;">

\-0.9603220

</td>

<td style="text-align:center;">

\-0.9974113

</td>

<td style="text-align:center;">

\-0.9894474

</td>

<td style="text-align:center;">

\-0.9316387

</td>

<td style="text-align:center;">

\-0.9955481

</td>

<td style="text-align:center;">

\-0.9810636

</td>

<td style="text-align:center;">

\-0.9918457

</td>

<td style="text-align:center;">

\-0.9831200

</td>

<td style="text-align:center;">

\-0.9890458

</td>

<td style="text-align:center;">

\-0.9891212

</td>

<td style="text-align:center;">

\-0.9898726

</td>

<td style="text-align:center;">

\-0.9972926

</td>

<td style="text-align:center;">

\-0.9938510

</td>

<td style="text-align:center;">

\-0.9760571

</td>

<td style="text-align:center;">

\-0.9760571

</td>

<td style="text-align:center;">

\-0.9916944

</td>

<td style="text-align:center;">

\-0.9837542

</td>

<td style="text-align:center;">

\-0.9961016

</td>

<td style="text-align:center;">

\-0.9986803

</td>

<td style="text-align:center;">

\-0.9749298

</td>

<td style="text-align:center;">

\-0.9554381

</td>

<td style="text-align:center;">

\-0.9966523

</td>

<td style="text-align:center;">

\-0.9820839

</td>

<td style="text-align:center;">

\-0.9926268

</td>

<td style="text-align:center;">

\-0.9849043

</td>

<td style="text-align:center;">

\-0.9871681

</td>

<td style="text-align:center;">

\-0.9897847

</td>

<td style="text-align:center;">

\-0.9758658

</td>

<td style="text-align:center;">

\-0.9919603

</td>

<td style="text-align:center;">

\-0.9833219

</td>

<td style="text-align:center;">

\-0.9963995

</td>

</tr>

<tr>

<td style="text-align:center;">

1

</td>

<td style="text-align:center;">

STANDING

</td>

<td style="text-align:center;">

0.2796531

</td>

<td style="text-align:center;">

\-0.0194672

</td>

<td style="text-align:center;">

\-0.1134617

</td>

<td style="text-align:center;">

0.9668781

</td>

<td style="text-align:center;">

\-0.1420098

</td>

<td style="text-align:center;">

0.1018839

</td>

<td style="text-align:center;">

0.0736360

</td>

<td style="text-align:center;">

0.0031040

</td>

<td style="text-align:center;">

\-0.0090456

</td>

<td style="text-align:center;">

\-0.0316983

</td>

<td style="text-align:center;">

\-0.1023354

</td>

<td style="text-align:center;">

0.0961269

</td>

<td style="text-align:center;">

\-0.1084857

</td>

<td style="text-align:center;">

\-0.0424103

</td>

<td style="text-align:center;">

\-0.0558288

</td>

<td style="text-align:center;">

\-0.9837031

</td>

<td style="text-align:center;">

\-0.9837031

</td>

<td style="text-align:center;">

\-0.9885313

</td>

<td style="text-align:center;">

\-0.9763171

</td>

<td style="text-align:center;">

\-0.9934032

</td>

<td style="text-align:center;">

\-0.9935941

</td>

<td style="text-align:center;">

\-0.9725115

</td>

<td style="text-align:center;">

\-0.9833040

</td>

<td style="text-align:center;">

0.1245312

</td>

<td style="text-align:center;">

\-0.0646106

</td>

<td style="text-align:center;">

0.0826769

</td>

<td style="text-align:center;">

\-0.9909937

</td>

<td style="text-align:center;">

\-0.9816423

</td>

<td style="text-align:center;">

\-0.9875663

</td>

<td style="text-align:center;">

0.1154340

</td>

<td style="text-align:center;">

\-0.1934363

</td>

<td style="text-align:center;">

0.0382543

</td>

<td style="text-align:center;">

\-0.9754332

</td>

<td style="text-align:center;">

\-0.9937147

</td>

<td style="text-align:center;">

\-0.9867557

</td>

<td style="text-align:center;">

\-0.2166851

</td>

<td style="text-align:center;">

\-0.0172642

</td>

<td style="text-align:center;">

\-0.1107203

</td>

<td style="text-align:center;">

\-0.9877948

</td>

<td style="text-align:center;">

0.2578991

</td>

<td style="text-align:center;">

\-0.9892801

</td>

<td style="text-align:center;">

0.6607950

</td>

<td style="text-align:center;">

\-0.9892548

</td>

<td style="text-align:center;">

\-0.2127279

</td>

<td style="text-align:center;">

\-0.9950305

</td>

<td style="text-align:center;">

0.4145028

</td>

<td style="text-align:center;">

\-0.1185593

</td>

<td style="text-align:center;">

0.1778995

</td>

<td style="text-align:center;">

0.1006992

</td>

<td style="text-align:center;">

0.8085291

</td>

<td style="text-align:center;">

\-0.8489335

</td>

<td style="text-align:center;">

0.1806373

</td>

<td style="text-align:center;">

\-0.0491178

</td>

<td style="text-align:center;">

\-0.9953796

</td>

<td style="text-align:center;">

\-0.9671870

</td>

<td style="text-align:center;">

\-0.9789440

</td>

<td style="text-align:center;">

\-0.9995740

</td>

<td style="text-align:center;">

\-0.9928658

</td>

<td style="text-align:center;">

\-0.9929172

</td>

<td style="text-align:center;">

\-0.9907428

</td>

<td style="text-align:center;">

\-0.9809556

</td>

<td style="text-align:center;">

\-0.9896866

</td>

<td style="text-align:center;">

\-0.9762921

</td>

<td style="text-align:center;">

\-0.9935518

</td>

<td style="text-align:center;">

\-0.9863787

</td>

<td style="text-align:center;">

\-0.9884618

</td>

<td style="text-align:center;">

\-0.9956321

</td>

<td style="text-align:center;">

\-0.9915318

</td>

<td style="text-align:center;">

\-0.9880196

</td>

<td style="text-align:center;">

\-0.9880196

</td>

<td style="text-align:center;">

\-0.9903969

</td>

<td style="text-align:center;">

\-0.9860515

</td>

<td style="text-align:center;">

\-0.9950910

</td>

<td style="text-align:center;">

\-0.9963128

</td>

<td style="text-align:center;">

\-0.9655059

</td>

<td style="text-align:center;">

\-0.9770493

</td>

<td style="text-align:center;">

\-0.9912488

</td>

<td style="text-align:center;">

\-0.9814148

</td>

<td style="text-align:center;">

\-0.9904159

</td>

<td style="text-align:center;">

\-0.9766422

</td>

<td style="text-align:center;">

\-0.9933990

</td>

<td style="text-align:center;">

\-0.9873282

</td>

<td style="text-align:center;">

\-0.9890155

</td>

<td style="text-align:center;">

\-0.9908667

</td>

<td style="text-align:center;">

\-0.9860277

</td>

<td style="text-align:center;">

\-0.9951274

</td>

</tr>

<tr>

<td style="text-align:center;">

1

</td>

<td style="text-align:center;">

STANDING

</td>

<td style="text-align:center;">

0.2791739

</td>

<td style="text-align:center;">

\-0.0262006

</td>

<td style="text-align:center;">

\-0.1232826

</td>

<td style="text-align:center;">

0.9676152

</td>

<td style="text-align:center;">

\-0.1439764

</td>

<td style="text-align:center;">

0.0998501

</td>

<td style="text-align:center;">

0.0773206

</td>

<td style="text-align:center;">

0.0200576

</td>

<td style="text-align:center;">

\-0.0098648

</td>

<td style="text-align:center;">

\-0.0434100

</td>

<td style="text-align:center;">

\-0.0913862

</td>

<td style="text-align:center;">

0.0855377

</td>

<td style="text-align:center;">

\-0.0911699

</td>

<td style="text-align:center;">

\-0.0363326

</td>

<td style="text-align:center;">

\-0.0604647

</td>

<td style="text-align:center;">

\-0.9865418

</td>

<td style="text-align:center;">

\-0.9865418

</td>

<td style="text-align:center;">

\-0.9930780

</td>

<td style="text-align:center;">

\-0.9820599

</td>

<td style="text-align:center;">

\-0.9955022

</td>

<td style="text-align:center;">

\-0.9954906

</td>

<td style="text-align:center;">

\-0.9835697

</td>

<td style="text-align:center;">

\-0.9910798

</td>

<td style="text-align:center;">

0.0290444

</td>

<td style="text-align:center;">

0.0803023

</td>

<td style="text-align:center;">

0.1856947

</td>

<td style="text-align:center;">

\-0.9944466

</td>

<td style="text-align:center;">

\-0.9887272

</td>

<td style="text-align:center;">

\-0.9913542

</td>

<td style="text-align:center;">

0.0357981

</td>

<td style="text-align:center;">

\-0.0930358

</td>

<td style="text-align:center;">

0.1680952

</td>

<td style="text-align:center;">

\-0.9871096

</td>

<td style="text-align:center;">

\-0.9936015

</td>

<td style="text-align:center;">

\-0.9871913

</td>

<td style="text-align:center;">

0.2168625

</td>

<td style="text-align:center;">

\-0.1352454

</td>

<td style="text-align:center;">

\-0.0497280

</td>

<td style="text-align:center;">

\-0.9875187

</td>

<td style="text-align:center;">

0.0735815

</td>

<td style="text-align:center;">

\-0.9927689

</td>

<td style="text-align:center;">

0.6789213

</td>

<td style="text-align:center;">

\-0.9894128

</td>

<td style="text-align:center;">

\-0.0356842

</td>

<td style="text-align:center;">

\-0.9952207

</td>

<td style="text-align:center;">

0.4045725

</td>

<td style="text-align:center;">

\-0.0367880

</td>

<td style="text-align:center;">

\-0.0128925

</td>

<td style="text-align:center;">

0.6400110

</td>

<td style="text-align:center;">

\-0.4853665

</td>

<td style="text-align:center;">

\-0.8486494

</td>

<td style="text-align:center;">

0.1819348

</td>

<td style="text-align:center;">

\-0.0476632

</td>

<td style="text-align:center;">

\-0.9960915

</td>

<td style="text-align:center;">

\-0.9834027

</td>

<td style="text-align:center;">

\-0.9906751

</td>

<td style="text-align:center;">

\-0.9966456

</td>

<td style="text-align:center;">

\-0.9813928

</td>

<td style="text-align:center;">

\-0.9784764

</td>

<td style="text-align:center;">

\-0.9926974

</td>

<td style="text-align:center;">

\-0.9875528

</td>

<td style="text-align:center;">

\-0.9934976

</td>

<td style="text-align:center;">

\-0.9913848

</td>

<td style="text-align:center;">

\-0.9924073

</td>

<td style="text-align:center;">

\-0.9875542

</td>

<td style="text-align:center;">

\-0.9911194

</td>

<td style="text-align:center;">

\-0.9966410

</td>

<td style="text-align:center;">

\-0.9933289

</td>

<td style="text-align:center;">

\-0.9864214

</td>

<td style="text-align:center;">

\-0.9864214

</td>

<td style="text-align:center;">

\-0.9933808

</td>

<td style="text-align:center;">

\-0.9873511

</td>

<td style="text-align:center;">

\-0.9952666

</td>

<td style="text-align:center;">

\-0.9963121

</td>

<td style="text-align:center;">

\-0.9832444

</td>

<td style="text-align:center;">

\-0.9902291

</td>

<td style="text-align:center;">

\-0.9913783

</td>

<td style="text-align:center;">

\-0.9869268

</td>

<td style="text-align:center;">

\-0.9943908

</td>

<td style="text-align:center;">

\-0.9928104

</td>

<td style="text-align:center;">

\-0.9916460

</td>

<td style="text-align:center;">

\-0.9886776

</td>

<td style="text-align:center;">

\-0.9867420

</td>

<td style="text-align:center;">

\-0.9916998

</td>

<td style="text-align:center;">

\-0.9878358

</td>

<td style="text-align:center;">

\-0.9952369

</td>

</tr>

<tr>

<td style="text-align:center;">

1

</td>

<td style="text-align:center;">

STANDING

</td>

<td style="text-align:center;">

0.2766288

</td>

<td style="text-align:center;">

\-0.0165697

</td>

<td style="text-align:center;">

\-0.1153618

</td>

<td style="text-align:center;">

0.9682244

</td>

<td style="text-align:center;">

\-0.1487502

</td>

<td style="text-align:center;">

0.0944859

</td>

<td style="text-align:center;">

0.0734444

</td>

<td style="text-align:center;">

0.0191216

</td>

<td style="text-align:center;">

0.0167800

</td>

<td style="text-align:center;">

\-0.0339604

</td>

<td style="text-align:center;">

\-0.0747080

</td>

<td style="text-align:center;">

0.0773920

</td>

<td style="text-align:center;">

\-0.0907701

</td>

<td style="text-align:center;">

\-0.0376325

</td>

<td style="text-align:center;">

\-0.0582893

</td>

<td style="text-align:center;">

\-0.9928272

</td>

<td style="text-align:center;">

\-0.9928272

</td>

<td style="text-align:center;">

\-0.9934800

</td>

<td style="text-align:center;">

\-0.9852037

</td>

<td style="text-align:center;">

\-0.9958076

</td>

<td style="text-align:center;">

\-0.9972859

</td>

<td style="text-align:center;">

\-0.9823010

</td>

<td style="text-align:center;">

\-0.9883694

</td>

<td style="text-align:center;">

0.1810898

</td>

<td style="text-align:center;">

0.0579879

</td>

<td style="text-align:center;">

0.5597863

</td>

<td style="text-align:center;">

\-0.9962920

</td>

<td style="text-align:center;">

\-0.9887900

</td>

<td style="text-align:center;">

\-0.9906244

</td>

<td style="text-align:center;">

0.2733502

</td>

<td style="text-align:center;">

0.0791354

</td>

<td style="text-align:center;">

0.2923842

</td>

<td style="text-align:center;">

\-0.9824465

</td>

<td style="text-align:center;">

\-0.9929838

</td>

<td style="text-align:center;">

\-0.9886664

</td>

<td style="text-align:center;">

\-0.1533426

</td>

<td style="text-align:center;">

\-0.0884027

</td>

<td style="text-align:center;">

\-0.1622304

</td>

<td style="text-align:center;">

\-0.9935908

</td>

<td style="text-align:center;">

0.3943103

</td>

<td style="text-align:center;">

\-0.9955228

</td>

<td style="text-align:center;">

0.5590577

</td>

<td style="text-align:center;">

\-0.9914330

</td>

<td style="text-align:center;">

\-0.2735820

</td>

<td style="text-align:center;">

\-0.9950928

</td>

<td style="text-align:center;">

0.0877530

</td>

<td style="text-align:center;">

0.1233200

</td>

<td style="text-align:center;">

0.1225420

</td>

<td style="text-align:center;">

0.6935783

</td>

<td style="text-align:center;">

\-0.6159706

</td>

<td style="text-align:center;">

\-0.8478652

</td>

<td style="text-align:center;">

0.1851512

</td>

<td style="text-align:center;">

\-0.0438923

</td>

<td style="text-align:center;">

\-0.9981386

</td>

<td style="text-align:center;">

\-0.9808173

</td>

<td style="text-align:center;">

\-0.9904816

</td>

<td style="text-align:center;">

\-0.9984293

</td>

<td style="text-align:center;">

\-0.9880982

</td>

<td style="text-align:center;">

\-0.9787449

</td>

<td style="text-align:center;">

\-0.9964202

</td>

<td style="text-align:center;">

\-0.9883587

</td>

<td style="text-align:center;">

\-0.9924549

</td>

<td style="text-align:center;">

\-0.9851836

</td>

<td style="text-align:center;">

\-0.9923781

</td>

<td style="text-align:center;">

\-0.9874018

</td>

<td style="text-align:center;">

\-0.9913545

</td>

<td style="text-align:center;">

\-0.9964730

</td>

<td style="text-align:center;">

\-0.9945110

</td>

<td style="text-align:center;">

\-0.9912754

</td>

<td style="text-align:center;">

\-0.9912754

</td>

<td style="text-align:center;">

\-0.9958537

</td>

<td style="text-align:center;">

\-0.9890626

</td>

<td style="text-align:center;">

\-0.9952580

</td>

<td style="text-align:center;">

\-0.9986065

</td>

<td style="text-align:center;">

\-0.9801295

</td>

<td style="text-align:center;">

\-0.9919150

</td>

<td style="text-align:center;">

\-0.9969025

</td>

<td style="text-align:center;">

\-0.9886067

</td>

<td style="text-align:center;">

\-0.9929065

</td>

<td style="text-align:center;">

\-0.9859818

</td>

<td style="text-align:center;">

\-0.9919558

</td>

<td style="text-align:center;">

\-0.9879443

</td>

<td style="text-align:center;">

\-0.9900635

</td>

<td style="text-align:center;">

\-0.9943890

</td>

<td style="text-align:center;">

\-0.9890594

</td>

<td style="text-align:center;">

\-0.9954648

</td>

</tr>

</tbody>

</table>

#### Step 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

#### 5.1 Creating the second dataset:

``` r
second_data <- data_tidy %>%
    group_by(subject, activity) %>%
    summarise_all(funs(mean))
```

    ## Warning: `funs()` is deprecated as of dplyr 0.8.0.
    ## Please use a list of either functions or lambdas: 
    ## 
    ##   # Simple named list: 
    ##   list(mean = mean, median = median)
    ## 
    ##   # Auto named with `tibble::lst()`: 
    ##   tibble::lst(mean, median)
    ## 
    ##   # Using lambdas
    ##   list(~ mean(., trim = .2), ~ median(., na.rm = TRUE))
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_warnings()` to see where this warning was generated.

#### 5.2 Writing a table for the second data set.

``` r
write.table(second_data, file = "Exported_data.txt", row.names = FALSE)
```

#### 5.3 Exporting the second data set.

``` r
exported <- read.table("Exported_data.txt")
```

#### 5.4 Taking a look at the second data set.

``` r
kable(head(second_data, 5), align = "c", booktabs = TRUE)
```

<table>

<thead>

<tr>

<th style="text-align:center;">

subject

</th>

<th style="text-align:center;">

activity

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.Mean…X

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.Mean…Y

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.Mean…Z

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.Mean…X

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.Mean…Y

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.Mean…Z

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.Mean…X

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.Mean…Y

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.Mean…Z

</th>

<th style="text-align:center;">

TimeBodyGyroscope.Mean…X

</th>

<th style="text-align:center;">

TimeBodyGyroscope.Mean…Y

</th>

<th style="text-align:center;">

TimeBodyGyroscope.Mean…Z

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.Mean…X

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.Mean…Y

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.Mean…Z

</th>

<th style="text-align:center;">

TimeBodyAccelerometerMagnitude.Mean..

</th>

<th style="text-align:center;">

TimeGravityAccelerometerMagnitude.Mean..

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerkMagnitude.Mean..

</th>

<th style="text-align:center;">

TimeBodyGyroscopeMagnitude.Mean..

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerkMagnitude.Mean..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.Mean…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.Mean…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.Mean…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.MeanFrequency…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.MeanFrequency…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.MeanFrequency…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.Mean…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.Mean…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.Mean…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.MeanFrequency…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.MeanFrequency…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.MeanFrequency…Z

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.Mean…X

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.Mean…Y

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.Mean…Z

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.MeanFrequency…X

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.MeanFrequency…Y

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.MeanFrequency…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerMagnitude.Mean..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerMagnitude.MeanFrequency..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerkMagnitude.Mean..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerkMagnitude.MeanFrequency..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeMagnitude.Mean..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeMagnitude.MeanFrequency..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeJerkMagnitude.Mean..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeJerkMagnitude.MeanFrequency..

</th>

<th style="text-align:center;">

angle.TimeBodyAccelerometerMean.Gravity.

</th>

<th style="text-align:center;">

angle.TimeBodyAccelerometerJerkMean..GravityMean.

</th>

<th style="text-align:center;">

angle.TimeBodyGyroscopeMean.GravityMean.

</th>

<th style="text-align:center;">

angle.TimeBodyGyroscopeJerkMean.GravityMean.

</th>

<th style="text-align:center;">

angle.X.GravityMean.

</th>

<th style="text-align:center;">

angle.Y.GravityMean.

</th>

<th style="text-align:center;">

angle.Z.GravityMean.

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.std…X

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.std…Y

</th>

<th style="text-align:center;">

TimeBodyAccelerometer.std…Z

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.std…X

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.std…Y

</th>

<th style="text-align:center;">

TimeGravityAccelerometer.std…Z

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.std…X

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.std…Y

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerk.std…Z

</th>

<th style="text-align:center;">

TimeBodyGyroscope.std…X

</th>

<th style="text-align:center;">

TimeBodyGyroscope.std…Y

</th>

<th style="text-align:center;">

TimeBodyGyroscope.std…Z

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.std…X

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.std…Y

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerk.std…Z

</th>

<th style="text-align:center;">

TimeBodyAccelerometerMagnitude.std..

</th>

<th style="text-align:center;">

TimeGravityAccelerometerMagnitude.std..

</th>

<th style="text-align:center;">

TimeBodyAccelerometerJerkMagnitude.std..

</th>

<th style="text-align:center;">

TimeBodyGyroscopeMagnitude.std..

</th>

<th style="text-align:center;">

TimeBodyGyroscopeJerkMagnitude.std..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.std…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.std…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometer.std…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.std…X

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.std…Y

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerk.std…Z

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.std…X

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.std…Y

</th>

<th style="text-align:center;">

FrequencyBodyGyroscope.std…Z

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerMagnitude.std..

</th>

<th style="text-align:center;">

FrequencyBodyAccelerometerJerkMagnitude.std..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeMagnitude.std..

</th>

<th style="text-align:center;">

FrequencyBodyGyroscopeJerkMagnitude.std..

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:center;">

1

</td>

<td style="text-align:center;">

LAYING

</td>

<td style="text-align:center;">

0.2215982

</td>

<td style="text-align:center;">

\-0.0405140

</td>

<td style="text-align:center;">

\-0.1132036

</td>

<td style="text-align:center;">

\-0.2488818

</td>

<td style="text-align:center;">

0.7055498

</td>

<td style="text-align:center;">

0.4458177

</td>

<td style="text-align:center;">

0.0810865

</td>

<td style="text-align:center;">

0.0038382

</td>

<td style="text-align:center;">

0.0108342

</td>

<td style="text-align:center;">

\-0.0165531

</td>

<td style="text-align:center;">

\-0.0644861

</td>

<td style="text-align:center;">

0.1486894

</td>

<td style="text-align:center;">

\-0.1072709

</td>

<td style="text-align:center;">

\-0.0415173

</td>

<td style="text-align:center;">

\-0.0740501

</td>

<td style="text-align:center;">

\-0.8419292

</td>

<td style="text-align:center;">

\-0.8419292

</td>

<td style="text-align:center;">

\-0.9543963

</td>

<td style="text-align:center;">

\-0.8747595

</td>

<td style="text-align:center;">

\-0.9634610

</td>

<td style="text-align:center;">

\-0.9390991

</td>

<td style="text-align:center;">

\-0.8670652

</td>

<td style="text-align:center;">

\-0.8826669

</td>

<td style="text-align:center;">

\-0.1587927

</td>

<td style="text-align:center;">

0.0975348

</td>

<td style="text-align:center;">

0.0894377

</td>

<td style="text-align:center;">

\-0.9570739

</td>

<td style="text-align:center;">

\-0.9224626

</td>

<td style="text-align:center;">

\-0.9480609

</td>

<td style="text-align:center;">

0.1324191

</td>

<td style="text-align:center;">

0.0245136

</td>

<td style="text-align:center;">

0.0243879

</td>

<td style="text-align:center;">

\-0.8502492

</td>

<td style="text-align:center;">

\-0.9521915

</td>

<td style="text-align:center;">

\-0.9093027

</td>

<td style="text-align:center;">

\-0.0035468

</td>

<td style="text-align:center;">

\-0.0915291

</td>

<td style="text-align:center;">

0.0104581

</td>

<td style="text-align:center;">

\-0.8617676

</td>

<td style="text-align:center;">

0.0864086

</td>

<td style="text-align:center;">

\-0.9333004

</td>

<td style="text-align:center;">

0.2663912

</td>

<td style="text-align:center;">

\-0.8621902

</td>

<td style="text-align:center;">

\-0.1397750

</td>

<td style="text-align:center;">

\-0.9423669

</td>

<td style="text-align:center;">

0.1764859

</td>

<td style="text-align:center;">

0.0213660

</td>

<td style="text-align:center;">

0.0030604

</td>

<td style="text-align:center;">

\-0.0016670

</td>

<td style="text-align:center;">

0.0844372

</td>

<td style="text-align:center;">

0.4267062

</td>

<td style="text-align:center;">

\-0.5203438

</td>

<td style="text-align:center;">

\-0.3524131

</td>

<td style="text-align:center;">

\-0.9280565

</td>

<td style="text-align:center;">

\-0.8368274

</td>

<td style="text-align:center;">

\-0.8260614

</td>

<td style="text-align:center;">

\-0.8968300

</td>

<td style="text-align:center;">

\-0.9077200

</td>

<td style="text-align:center;">

\-0.8523663

</td>

<td style="text-align:center;">

\-0.9584821

</td>

<td style="text-align:center;">

\-0.9241493

</td>

<td style="text-align:center;">

\-0.9548551

</td>

<td style="text-align:center;">

\-0.8735439

</td>

<td style="text-align:center;">

\-0.9510904

</td>

<td style="text-align:center;">

\-0.9082847

</td>

<td style="text-align:center;">

\-0.9186085

</td>

<td style="text-align:center;">

\-0.9679072

</td>

<td style="text-align:center;">

\-0.9577902

</td>

<td style="text-align:center;">

\-0.7951449

</td>

<td style="text-align:center;">

\-0.7951449

</td>

<td style="text-align:center;">

\-0.9282456

</td>

<td style="text-align:center;">

\-0.8190102

</td>

<td style="text-align:center;">

\-0.9358410

</td>

<td style="text-align:center;">

\-0.9244374

</td>

<td style="text-align:center;">

\-0.8336256

</td>

<td style="text-align:center;">

\-0.8128916

</td>

<td style="text-align:center;">

\-0.9641607

</td>

<td style="text-align:center;">

\-0.9322179

</td>

<td style="text-align:center;">

\-0.9605870

</td>

<td style="text-align:center;">

\-0.8822965

</td>

<td style="text-align:center;">

\-0.9512320

</td>

<td style="text-align:center;">

\-0.9165825

</td>

<td style="text-align:center;">

\-0.7983009

</td>

<td style="text-align:center;">

\-0.9218040

</td>

<td style="text-align:center;">

\-0.8243194

</td>

<td style="text-align:center;">

\-0.9326607

</td>

</tr>

<tr>

<td style="text-align:center;">

1

</td>

<td style="text-align:center;">

SITTING

</td>

<td style="text-align:center;">

0.2612376

</td>

<td style="text-align:center;">

\-0.0013083

</td>

<td style="text-align:center;">

\-0.1045442

</td>

<td style="text-align:center;">

0.8315099

</td>

<td style="text-align:center;">

0.2044116

</td>

<td style="text-align:center;">

0.3320437

</td>

<td style="text-align:center;">

0.0774825

</td>

<td style="text-align:center;">

\-0.0006191

</td>

<td style="text-align:center;">

\-0.0033678

</td>

<td style="text-align:center;">

\-0.0453501

</td>

<td style="text-align:center;">

\-0.0919242

</td>

<td style="text-align:center;">

0.0629314

</td>

<td style="text-align:center;">

\-0.0936794

</td>

<td style="text-align:center;">

\-0.0402118

</td>

<td style="text-align:center;">

\-0.0467026

</td>

<td style="text-align:center;">

\-0.9485368

</td>

<td style="text-align:center;">

\-0.9485368

</td>

<td style="text-align:center;">

\-0.9873642

</td>

<td style="text-align:center;">

\-0.9308925

</td>

<td style="text-align:center;">

\-0.9919763

</td>

<td style="text-align:center;">

\-0.9796412

</td>

<td style="text-align:center;">

\-0.9440846

</td>

<td style="text-align:center;">

\-0.9591849

</td>

<td style="text-align:center;">

\-0.0495136

</td>

<td style="text-align:center;">

0.0759461

</td>

<td style="text-align:center;">

0.2388299

</td>

<td style="text-align:center;">

\-0.9865970

</td>

<td style="text-align:center;">

\-0.9815795

</td>

<td style="text-align:center;">

\-0.9860531

</td>

<td style="text-align:center;">

0.2566108

</td>

<td style="text-align:center;">

0.0475438

</td>

<td style="text-align:center;">

0.0923920

</td>

<td style="text-align:center;">

\-0.9761615

</td>

<td style="text-align:center;">

\-0.9758386

</td>

<td style="text-align:center;">

\-0.9513155

</td>

<td style="text-align:center;">

0.1891530

</td>

<td style="text-align:center;">

0.0631271

</td>

<td style="text-align:center;">

\-0.0297839

</td>

<td style="text-align:center;">

\-0.9477829

</td>

<td style="text-align:center;">

0.2366550

</td>

<td style="text-align:center;">

\-0.9852621

</td>

<td style="text-align:center;">

0.3518522

</td>

<td style="text-align:center;">

\-0.9584356

</td>

<td style="text-align:center;">

\-0.0002622

</td>

<td style="text-align:center;">

\-0.9897975

</td>

<td style="text-align:center;">

0.1847759

</td>

<td style="text-align:center;">

0.0274415

</td>

<td style="text-align:center;">

0.0297098

</td>

<td style="text-align:center;">

0.0676981

</td>

<td style="text-align:center;">

\-0.0648816

</td>

<td style="text-align:center;">

\-0.5912475

</td>

<td style="text-align:center;">

\-0.0604660

</td>

<td style="text-align:center;">

\-0.2180172

</td>

<td style="text-align:center;">

\-0.9772290

</td>

<td style="text-align:center;">

\-0.9226186

</td>

<td style="text-align:center;">

\-0.9395863

</td>

<td style="text-align:center;">

\-0.9684571

</td>

<td style="text-align:center;">

\-0.9355171

</td>

<td style="text-align:center;">

\-0.9490409

</td>

<td style="text-align:center;">

\-0.9864307

</td>

<td style="text-align:center;">

\-0.9813720

</td>

<td style="text-align:center;">

\-0.9879108

</td>

<td style="text-align:center;">

\-0.9772113

</td>

<td style="text-align:center;">

\-0.9664739

</td>

<td style="text-align:center;">

\-0.9414259

</td>

<td style="text-align:center;">

\-0.9917316

</td>

<td style="text-align:center;">

\-0.9895181

</td>

<td style="text-align:center;">

\-0.9879358

</td>

<td style="text-align:center;">

\-0.9270784

</td>

<td style="text-align:center;">

\-0.9270784

</td>

<td style="text-align:center;">

\-0.9841200

</td>

<td style="text-align:center;">

\-0.9345318

</td>

<td style="text-align:center;">

\-0.9883087

</td>

<td style="text-align:center;">

\-0.9764123

</td>

<td style="text-align:center;">

\-0.9172750

</td>

<td style="text-align:center;">

\-0.9344696

</td>

<td style="text-align:center;">

\-0.9874930

</td>

<td style="text-align:center;">

\-0.9825139

</td>

<td style="text-align:center;">

\-0.9883392

</td>

<td style="text-align:center;">

\-0.9779042

</td>

<td style="text-align:center;">

\-0.9623450

</td>

<td style="text-align:center;">

\-0.9439178

</td>

<td style="text-align:center;">

\-0.9284448

</td>

<td style="text-align:center;">

\-0.9816062

</td>

<td style="text-align:center;">

\-0.9321984

</td>

<td style="text-align:center;">

\-0.9870496

</td>

</tr>

<tr>

<td style="text-align:center;">

1

</td>

<td style="text-align:center;">

STANDING

</td>

<td style="text-align:center;">

0.2789176

</td>

<td style="text-align:center;">

\-0.0161376

</td>

<td style="text-align:center;">

\-0.1106018

</td>

<td style="text-align:center;">

0.9429520

</td>

<td style="text-align:center;">

\-0.2729838

</td>

<td style="text-align:center;">

0.0134906

</td>

<td style="text-align:center;">

0.0753767

</td>

<td style="text-align:center;">

0.0079757

</td>

<td style="text-align:center;">

\-0.0036852

</td>

<td style="text-align:center;">

\-0.0239877

</td>

<td style="text-align:center;">

\-0.0593972

</td>

<td style="text-align:center;">

0.0748008

</td>

<td style="text-align:center;">

\-0.0996092

</td>

<td style="text-align:center;">

\-0.0440628

</td>

<td style="text-align:center;">

\-0.0489505

</td>

<td style="text-align:center;">

\-0.9842782

</td>

<td style="text-align:center;">

\-0.9842782

</td>

<td style="text-align:center;">

\-0.9923678

</td>

<td style="text-align:center;">

\-0.9764938

</td>

<td style="text-align:center;">

\-0.9949668

</td>

<td style="text-align:center;">

\-0.9952499

</td>

<td style="text-align:center;">

\-0.9770708

</td>

<td style="text-align:center;">

\-0.9852971

</td>

<td style="text-align:center;">

0.0865154

</td>

<td style="text-align:center;">

0.1174789

</td>

<td style="text-align:center;">

0.2448586

</td>

<td style="text-align:center;">

\-0.9946308

</td>

<td style="text-align:center;">

\-0.9854187

</td>

<td style="text-align:center;">

\-0.9907522

</td>

<td style="text-align:center;">

0.3141829

</td>

<td style="text-align:center;">

0.0391619

</td>

<td style="text-align:center;">

0.1385815

</td>

<td style="text-align:center;">

\-0.9863868

</td>

<td style="text-align:center;">

\-0.9889845

</td>

<td style="text-align:center;">

\-0.9807731

</td>

<td style="text-align:center;">

\-0.1202930

</td>

<td style="text-align:center;">

\-0.0447192

</td>

<td style="text-align:center;">

0.1006076

</td>

<td style="text-align:center;">

\-0.9853564

</td>

<td style="text-align:center;">

0.2845553

</td>

<td style="text-align:center;">

\-0.9925425

</td>

<td style="text-align:center;">

0.4222201

</td>

<td style="text-align:center;">

\-0.9846176

</td>

<td style="text-align:center;">

\-0.0286058

</td>

<td style="text-align:center;">

\-0.9948154

</td>

<td style="text-align:center;">

0.3344987

</td>

<td style="text-align:center;">

\-0.0002223

</td>

<td style="text-align:center;">

0.0219638

</td>

<td style="text-align:center;">

\-0.0337938

</td>

<td style="text-align:center;">

\-0.0279229

</td>

<td style="text-align:center;">

\-0.7434079

</td>

<td style="text-align:center;">

0.2701750

</td>

<td style="text-align:center;">

0.0122529

</td>

<td style="text-align:center;">

\-0.9957599

</td>

<td style="text-align:center;">

\-0.9731901

</td>

<td style="text-align:center;">

\-0.9797759

</td>

<td style="text-align:center;">

\-0.9937630

</td>

<td style="text-align:center;">

\-0.9812260

</td>

<td style="text-align:center;">

\-0.9763241

</td>

<td style="text-align:center;">

\-0.9946045

</td>

<td style="text-align:center;">

\-0.9856487

</td>

<td style="text-align:center;">

\-0.9922512

</td>

<td style="text-align:center;">

\-0.9871919

</td>

<td style="text-align:center;">

\-0.9877344

</td>

<td style="text-align:center;">

\-0.9806456

</td>

<td style="text-align:center;">

\-0.9929451

</td>

<td style="text-align:center;">

\-0.9951379

</td>

<td style="text-align:center;">

\-0.9921085

</td>

<td style="text-align:center;">

\-0.9819429

</td>

<td style="text-align:center;">

\-0.9819429

</td>

<td style="text-align:center;">

\-0.9930962

</td>

<td style="text-align:center;">

\-0.9786900

</td>

<td style="text-align:center;">

\-0.9947332

</td>

<td style="text-align:center;">

\-0.9960283

</td>

<td style="text-align:center;">

\-0.9722931

</td>

<td style="text-align:center;">

\-0.9779373

</td>

<td style="text-align:center;">

\-0.9950738

</td>

<td style="text-align:center;">

\-0.9870182

</td>

<td style="text-align:center;">

\-0.9923498

</td>

<td style="text-align:center;">

\-0.9874971

</td>

<td style="text-align:center;">

\-0.9871077

</td>

<td style="text-align:center;">

\-0.9823453

</td>

<td style="text-align:center;">

\-0.9823138

</td>

<td style="text-align:center;">

\-0.9925360

</td>

<td style="text-align:center;">

\-0.9784661

</td>

<td style="text-align:center;">

\-0.9946711

</td>

</tr>

<tr>

<td style="text-align:center;">

1

</td>

<td style="text-align:center;">

WALKING

</td>

<td style="text-align:center;">

0.2773308

</td>

<td style="text-align:center;">

\-0.0173838

</td>

<td style="text-align:center;">

\-0.1111481

</td>

<td style="text-align:center;">

0.9352232

</td>

<td style="text-align:center;">

\-0.2821650

</td>

<td style="text-align:center;">

\-0.0681029

</td>

<td style="text-align:center;">

0.0740416

</td>

<td style="text-align:center;">

0.0282721

</td>

<td style="text-align:center;">

\-0.0041684

</td>

<td style="text-align:center;">

\-0.0418310

</td>

<td style="text-align:center;">

\-0.0695300

</td>

<td style="text-align:center;">

0.0849448

</td>

<td style="text-align:center;">

\-0.0899975

</td>

<td style="text-align:center;">

\-0.0398429

</td>

<td style="text-align:center;">

\-0.0461309

</td>

<td style="text-align:center;">

\-0.1369712

</td>

<td style="text-align:center;">

\-0.1369712

</td>

<td style="text-align:center;">

\-0.1414288

</td>

<td style="text-align:center;">

\-0.1609796

</td>

<td style="text-align:center;">

\-0.2987037

</td>

<td style="text-align:center;">

\-0.2027943

</td>

<td style="text-align:center;">

0.0897127

</td>

<td style="text-align:center;">

\-0.3315601

</td>

<td style="text-align:center;">

\-0.2075484

</td>

<td style="text-align:center;">

0.1130936

</td>

<td style="text-align:center;">

0.0497265

</td>

<td style="text-align:center;">

\-0.1705470

</td>

<td style="text-align:center;">

\-0.0352255

</td>

<td style="text-align:center;">

\-0.4689992

</td>

<td style="text-align:center;">

\-0.2092620

</td>

<td style="text-align:center;">

\-0.3862371

</td>

<td style="text-align:center;">

\-0.1855303

</td>

<td style="text-align:center;">

\-0.3390322

</td>

<td style="text-align:center;">

\-0.1030594

</td>

<td style="text-align:center;">

\-0.2559409

</td>

<td style="text-align:center;">

0.0147845

</td>

<td style="text-align:center;">

\-0.0657746

</td>

<td style="text-align:center;">

0.0007733

</td>

<td style="text-align:center;">

\-0.1286235

</td>

<td style="text-align:center;">

0.1906437

</td>

<td style="text-align:center;">

\-0.0571194

</td>

<td style="text-align:center;">

0.0938222

</td>

<td style="text-align:center;">

\-0.1992526

</td>

<td style="text-align:center;">

0.2688444

</td>

<td style="text-align:center;">

\-0.3193086

</td>

<td style="text-align:center;">

0.1906634

</td>

<td style="text-align:center;">

0.0604537

</td>

<td style="text-align:center;">

\-0.0079304

</td>

<td style="text-align:center;">

0.0130595

</td>

<td style="text-align:center;">

\-0.0187432

</td>

<td style="text-align:center;">

\-0.7292472

</td>

<td style="text-align:center;">

0.2769530

</td>

<td style="text-align:center;">

0.0688589

</td>

<td style="text-align:center;">

\-0.2837403

</td>

<td style="text-align:center;">

0.1144613

</td>

<td style="text-align:center;">

\-0.2600279

</td>

<td style="text-align:center;">

\-0.9766096

</td>

<td style="text-align:center;">

\-0.9713060

</td>

<td style="text-align:center;">

\-0.9477172

</td>

<td style="text-align:center;">

\-0.1136156

</td>

<td style="text-align:center;">

0.0670025

</td>

<td style="text-align:center;">

\-0.5026998

</td>

<td style="text-align:center;">

\-0.4735355

</td>

<td style="text-align:center;">

\-0.0546078

</td>

<td style="text-align:center;">

\-0.3442666

</td>

<td style="text-align:center;">

\-0.2074219

</td>

<td style="text-align:center;">

\-0.3044685

</td>

<td style="text-align:center;">

\-0.4042555

</td>

<td style="text-align:center;">

\-0.2196886

</td>

<td style="text-align:center;">

\-0.2196886

</td>

<td style="text-align:center;">

\-0.0744718

</td>

<td style="text-align:center;">

\-0.1869784

</td>

<td style="text-align:center;">

\-0.3253249

</td>

<td style="text-align:center;">

\-0.3191347

</td>

<td style="text-align:center;">

0.0560400

</td>

<td style="text-align:center;">

\-0.2796868

</td>

<td style="text-align:center;">

\-0.1335866

</td>

<td style="text-align:center;">

0.1067399

</td>

<td style="text-align:center;">

\-0.5347134

</td>

<td style="text-align:center;">

\-0.5166919

</td>

<td style="text-align:center;">

\-0.0335082

</td>

<td style="text-align:center;">

\-0.4365622

</td>

<td style="text-align:center;">

\-0.3980326

</td>

<td style="text-align:center;">

\-0.1034924

</td>

<td style="text-align:center;">

\-0.3210180

</td>

<td style="text-align:center;">

\-0.3816019

</td>

</tr>

<tr>

<td style="text-align:center;">

1

</td>

<td style="text-align:center;">

WALKING\_DOWNSTAIRS

</td>

<td style="text-align:center;">

0.2891883

</td>

<td style="text-align:center;">

\-0.0099185

</td>

<td style="text-align:center;">

\-0.1075662

</td>

<td style="text-align:center;">

0.9318744

</td>

<td style="text-align:center;">

\-0.2666103

</td>

<td style="text-align:center;">

\-0.0621200

</td>

<td style="text-align:center;">

0.0541553

</td>

<td style="text-align:center;">

0.0296504

</td>

<td style="text-align:center;">

\-0.0109720

</td>

<td style="text-align:center;">

\-0.0350782

</td>

<td style="text-align:center;">

\-0.0909371

</td>

<td style="text-align:center;">

0.0900850

</td>

<td style="text-align:center;">

\-0.0739592

</td>

<td style="text-align:center;">

\-0.0439903

</td>

<td style="text-align:center;">

\-0.0270461

</td>

<td style="text-align:center;">

0.0271883

</td>

<td style="text-align:center;">

0.0271883

</td>

<td style="text-align:center;">

\-0.0894475

</td>

<td style="text-align:center;">

\-0.0757413

</td>

<td style="text-align:center;">

\-0.2954638

</td>

<td style="text-align:center;">

0.0382292

</td>

<td style="text-align:center;">

0.0015499

</td>

<td style="text-align:center;">

\-0.2255745

</td>

<td style="text-align:center;">

\-0.3073952

</td>

<td style="text-align:center;">

0.0632201

</td>

<td style="text-align:center;">

0.2943227

</td>

<td style="text-align:center;">

\-0.0276639

</td>

<td style="text-align:center;">

\-0.1286672

</td>

<td style="text-align:center;">

\-0.2883347

</td>

<td style="text-align:center;">

\-0.2531643

</td>

<td style="text-align:center;">

\-0.3375897

</td>

<td style="text-align:center;">

0.0093722

</td>

<td style="text-align:center;">

\-0.3524496

</td>

<td style="text-align:center;">

\-0.0557023

</td>

<td style="text-align:center;">

\-0.0318694

</td>

<td style="text-align:center;">

\-0.1004537

</td>

<td style="text-align:center;">

0.0825511

</td>

<td style="text-align:center;">

\-0.0756762

</td>

<td style="text-align:center;">

0.0965845

</td>

<td style="text-align:center;">

0.1191871

</td>

<td style="text-align:center;">

0.0262185

</td>

<td style="text-align:center;">

0.0764915

</td>

<td style="text-align:center;">

\-0.1857203

</td>

<td style="text-align:center;">

0.3496139

</td>

<td style="text-align:center;">

\-0.2819634

</td>

<td style="text-align:center;">

0.1900007

</td>

<td style="text-align:center;">

\-0.0026951

</td>

<td style="text-align:center;">

0.0899317

</td>

<td style="text-align:center;">

0.0633383

</td>

<td style="text-align:center;">

\-0.0399768

</td>

<td style="text-align:center;">

\-0.7444838

</td>

<td style="text-align:center;">

0.2672458

</td>

<td style="text-align:center;">

0.0650047

</td>

<td style="text-align:center;">

0.0300353

</td>

<td style="text-align:center;">

\-0.0319359

</td>

<td style="text-align:center;">

\-0.2304342

</td>

<td style="text-align:center;">

\-0.9505598

</td>

<td style="text-align:center;">

\-0.9370187

</td>

<td style="text-align:center;">

\-0.8959397

</td>

<td style="text-align:center;">

\-0.0122839

</td>

<td style="text-align:center;">

\-0.1016014

</td>

<td style="text-align:center;">

\-0.3457350

</td>

<td style="text-align:center;">

\-0.4580305

</td>

<td style="text-align:center;">

\-0.1263492

</td>

<td style="text-align:center;">

\-0.1247025

</td>

<td style="text-align:center;">

\-0.4870273

</td>

<td style="text-align:center;">

\-0.2388248

</td>

<td style="text-align:center;">

\-0.2687615

</td>

<td style="text-align:center;">

0.0198844

</td>

<td style="text-align:center;">

0.0198844

</td>

<td style="text-align:center;">

\-0.0257877

</td>

<td style="text-align:center;">

\-0.2257244

</td>

<td style="text-align:center;">

\-0.3065106

</td>

<td style="text-align:center;">

0.0243308

</td>

<td style="text-align:center;">

\-0.1129637

</td>

<td style="text-align:center;">

\-0.2979279

</td>

<td style="text-align:center;">

\-0.0863279

</td>

<td style="text-align:center;">

\-0.1345800

</td>

<td style="text-align:center;">

\-0.4017215

</td>

<td style="text-align:center;">

\-0.4954225

</td>

<td style="text-align:center;">

\-0.1814147

</td>

<td style="text-align:center;">

\-0.2384436

</td>

<td style="text-align:center;">

\-0.1865303

</td>

<td style="text-align:center;">

\-0.1040523

</td>

<td style="text-align:center;">

\-0.3983504

</td>

<td style="text-align:center;">

\-0.3919199

</td>

</tr>

</tbody>

</table>
