CodeBook
================

Élida Silva Retamal

#### Peer-Graded Assignment: Getting and Cleaning Data Course Project

This code book aims to meet the evaluation criteria of the course
**Getting and Ceaning Data Course Project**, from Coursera online
platform, offered by Johns Hopkins University.

#### Selection of Files

Among the files provided for the preparation of the project, it was
necessary to select only those described below.

  - ‘README.txt’
    
      - Provides a brief description of the data source and the
        experiment
      - Informs about data usage license
      - Reports all available files
      - Informs about what was provided for each record

  - ‘features\_info.txt’: Shows information about the variables used on
    the feature vector.

  - ‘features.txt’: List of all features. It was read and assigned with
    the name **features** and the columns were named as **identifier**
    and **signals**

  - ‘activity\_labels.txt’: Links the class labels with their activity
    name. It was read and assigned with the name **activities** and the
    columns were named as **activity** and **signals**.

  - ‘train/X\_train.txt’: Training set. It was read and assigned with
    the name **X\_train**.

  - ‘train/y\_train.txt’: Training labels. It was read and assigned with
    the name **y\_train**.

  - ‘test/X\_test.txt’: Test set. It was read and assigned with the name
    **X\_test**

  - ‘test/y\_test.txt’: Test labels. It was read and assigned with the
    name **y\_test**

  - ‘train/subject\_train.txt’: Each row identifies the subject who
    performed the activity for each window sample. Its range is from 1
    to 30. Files used for the train data.

It was read and assigned with the name **subject\_train** and the column
was named as **subject**

  - ‘train/subject\_test.txt’: Each row identifies the subject who
    performed the activity for each window sample. Its range is from 1
    to 30. Files used for the test data. It was read and assigned with
    the name **subject\_test** and the column was named as **subject**

#### Data Set Information

The data sets and their description were provided in the project
instructions and made available through the following link:

<http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones>

Upon accessing this link, we identified that it is the Machine Learning
Repository created by students from UCI - University of California at
Irvine, Machine Learning Center and Intelligent Systems.

#### New Data Set

  - my: stores merged y\_train and y\_test data sets
  - mX: stores merged X\_train and X\_test data sets
  - msubject: stores merged subject\_train and subject\_test data sets
  - merge\_dataset: stores merged my, mX and msubject. It merges the
    training and the test sets to create one data set **(Step 1)**
  - data\_tidy: Extracts only the measurements on the mean and standard
    deviation for each measurement **(Step 2)**
  - second\_data: Second tidy data set with the average of each variable
    for each activity and subject **(Step 5)**

#### Descriptive Variable Names

\[1\] “subject”  
\[2\] “activity”  
\[3\] “TimeBodyAccelerometer.Mean…X”  
\[4\] “TimeBodyAccelerometer.Mean…Y”  
\[5\] “TimeBodyAccelerometer.Mean…Z”  
\[6\] “TimeGravityAccelerometer.Mean…X”  
\[7\] “TimeGravityAccelerometer.Mean…Y”  
\[8\] “TimeGravityAccelerometer.Mean…Z”  
\[9\] “TimeBodyAccelerometerJerk.Mean…X”  
\[10\] “TimeBodyAccelerometerJerk.Mean…Y”  
\[11\] “TimeBodyAccelerometerJerk.Mean…Z”  
\[12\] “TimeBodyGyroscope.Mean…X”  
\[13\] “TimeBodyGyroscope.Mean…Y”  
\[14\] “TimeBodyGyroscope.Mean…Z”  
\[15\] “TimeBodyGyroscopeJerk.Mean…X”  
\[16\] “TimeBodyGyroscopeJerk.Mean…Y”  
\[17\] “TimeBodyGyroscopeJerk.Mean…Z”  
\[18\] “TimeBodyAccelerometerMagnitude.Mean..”  
\[19\] “TimeGravityAccelerometerMagnitude.Mean..”  
\[20\] “TimeBodyAccelerometerJerkMagnitude.Mean..”  
\[21\] “TimeBodyGyroscopeMagnitude.Mean..”  
\[22\] “TimeBodyGyroscopeJerkMagnitude.Mean..”  
\[23\] “FrequencyBodyAccelerometer.Mean…X”  
\[24\] “FrequencyBodyAccelerometer.Mean…Y”  
\[25\] “FrequencyBodyAccelerometer.Mean…Z”  
\[26\] “FrequencyBodyAccelerometer.MeanFrequency…X”  
\[27\] “FrequencyBodyAccelerometer.MeanFrequency…Y”  
\[28\] “FrequencyBodyAccelerometer.MeanFrequency…Z”  
\[29\] “FrequencyBodyAccelerometerJerk.Mean…X”  
\[30\] “FrequencyBodyAccelerometerJerk.Mean…Y”  
\[31\] “FrequencyBodyAccelerometerJerk.Mean…Z”  
\[32\] “FrequencyBodyAccelerometerJerk.MeanFrequency…X”  
\[33\] “FrequencyBodyAccelerometerJerk.MeanFrequency…Y”  
\[34\] “FrequencyBodyAccelerometerJerk.MeanFrequency…Z”  
\[35\] “FrequencyBodyGyroscope.Mean…X”  
\[36\] “FrequencyBodyGyroscope.Mean…Y”  
\[37\] “FrequencyBodyGyroscope.Mean…Z”  
\[38\] “FrequencyBodyGyroscope.MeanFrequency…X”  
\[39\] “FrequencyBodyGyroscope.MeanFrequency…Y”  
\[40\] “FrequencyBodyGyroscope.MeanFrequency…Z”  
\[41\] “FrequencyBodyAccelerometerMagnitude.Mean..”  
\[42\] “FrequencyBodyAccelerometerMagnitude.MeanFrequency..”  
\[43\] “FrequencyBodyAccelerometerJerkMagnitude.Mean..”  
\[44\] “FrequencyBodyAccelerometerJerkMagnitude.MeanFrequency..”

\[45\] “FrequencyBodyGyroscopeMagnitude.Mean..”  
\[46\] “FrequencyBodyGyroscopeMagnitude.MeanFrequency..”  
\[47\] “FrequencyBodyGyroscopeJerkMagnitude.Mean..”  
\[48\] “FrequencyBodyGyroscopeJerkMagnitude.MeanFrequency..”  
\[49\] “angle.TimeBodyAccelerometerMean.Gravity.”  
\[50\] “angle.TimeBodyAccelerometerJerkMean..GravityMean.”  
\[51\] “angle.TimeBodyGyroscopeMean.GravityMean.”  
\[52\] “angle.TimeBodyGyroscopeJerkMean.GravityMean.”  
\[53\] “angle.X.GravityMean.”  
\[54\] “angle.Y.GravityMean.”  
\[55\] “angle.Z.GravityMean.”  
\[56\] “TimeBodyAccelerometer.std…X”  
\[57\] “TimeBodyAccelerometer.std…Y”  
\[58\] “TimeBodyAccelerometer.std…Z”  
\[59\] “TimeGravityAccelerometer.std…X”  
\[60\] “TimeGravityAccelerometer.std…Y”  
\[61\] “TimeGravityAccelerometer.std…Z”  
\[62\] “TimeBodyAccelerometerJerk.std…X”  
\[63\] “TimeBodyAccelerometerJerk.std…Y”  
\[64\] “TimeBodyAccelerometerJerk.std…Z”  
\[65\] “TimeBodyGyroscope.std…X”  
\[66\] “TimeBodyGyroscope.std…Y”  
\[67\] “TimeBodyGyroscope.std…Z”  
\[68\] “TimeBodyGyroscopeJerk.std…X”  
\[69\] “TimeBodyGyroscopeJerk.std…Y”  
\[70\] “TimeBodyGyroscopeJerk.std…Z”  
\[71\] “TimeBodyAccelerometerMagnitude.std..”  
\[72\] “TimeGravityAccelerometerMagnitude.std..”  
\[73\] “TimeBodyAccelerometerJerkMagnitude.std..”  
\[74\] “TimeBodyGyroscopeMagnitude.std..”  
\[75\] “TimeBodyGyroscopeJerkMagnitude.std..”  
\[76\] “FrequencyBodyAccelerometer.std…X”  
\[77\] “FrequencyBodyAccelerometer.std…Y”  
\[78\] “FrequencyBodyAccelerometer.std…Z”  
\[79\] “FrequencyBodyAccelerometerJerk.std…X”  
\[80\] “FrequencyBodyAccelerometerJerk.std…Y”  
\[81\] “FrequencyBodyAccelerometerJerk.std…Z”  
\[82\] “FrequencyBodyGyroscope.std…X”  
\[83\] “FrequencyBodyGyroscope.std…Y”  
\[84\] “FrequencyBodyGyroscope.std…Z”  
\[85\] “FrequencyBodyAccelerometerMagnitude.std..”  
\[86\] “FrequencyBodyAccelerometerJerkMagnitude.std..”  
\[87\] “FrequencyBodyGyroscopeMagnitude.std..”  
\[88\] “FrequencyBodyGyroscopeJerkMagnitude.std..”

**Accelerometers:** capable of measuring linear acceleration in the
direction of a reference axis. Acceleration is the rate of change of
speed over time, represented in m / s2 (meter per second squared) in the
International System.

**Gyroscopes:** capable of measuring the angular velocity around a
reference axis. The angular velocity is a quantity that represents the
rate of change of the angular position in time, whose unit of
measurement in the International System is rad / s (radian per second).
