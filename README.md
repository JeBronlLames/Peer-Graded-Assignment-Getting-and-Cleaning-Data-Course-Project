# Peer-Graded-Assignment-Getting-and-Cleaning-Data-Course-Project
This repo represents my submission for the Week 4 Assignment for "Getting and Cleaning Data

--------------------------------------------
Getting & Cleaning Data - Week 4 Assignment
--------------------------------------------
JeBronlLames
--------------------------------------------
The code produces a tidy data set, having 1 observation per row, 1 variable per column, no duplicate columns, and descriptive column names.

The original data are here: "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

It should be noted that the original data was pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). The sensor acceleration signal, which has gravitational and body motion components, was separated using a Butterworth low-pass filter into body acceleration and gravity. The gravitational force is assumed to have only low frequency components, therefore a filter with 0.3 Hz cutoff frequency was used. From each window, a vector of features was obtained by calculating variables from the time and frequency domain. See 'features_info.txt' for more details.
It should be noted that the original data set was then normalized.

--------------------------------------------

The data were processed in the following order:
- Data downloaded
- Features are read into R, to be used as the names of the columns in the data frame to be manipulated
- Data read into R
- Beginning with two data frames, the x_train and x_test tables, the features are assigned as the names of the columns to MAKE THEM DESCRIPTIVE
- Duplicate column names are indexed, then removed from the two data frames under construction.
- A subset of these tables is created, selecting only columns CONTAINING ONLY MEASUREMENTS ON THE MEAN AND STANDARD DEVIATION OF ALL VARIABLES (including meanFreq(), as it is, strictly speaking, a measurement on the mean of a variable)
- Beginning with the y_train and y_test files, which are the activity labels as numbers 1:6, these were read in as tables and strings were created where the values 1:6 were replaced by the ACTIVITY LABELS
- A column is added to each of the two subset tables for the ACTIVITY value
- The SUBJECT data was read in to create a string of the values for each training and testing.
- A column is added to each fo the two subset tables for the SUBJECT value.

*** - The two subset tables are then merged by binding the rows of the testing table underneath the rows of the training table, creating ONE FULL DATA SET CONTAINING BOTH TRAINING AND TESTING DATA, HAVING 1 OBSERVATION PER ROW, 1 VARIABLE PER COLUMN, NO DUPLICATE COLUMNS, AND DESCRIPTIVE VARIABLE NAMES (names taken directly from the features.txt file from the original .zip package)

- This complete dataset is then GROUPED BY ACTIVITY AND SUBJECT

*** - This complete dataset is then SUMMARISED WITH THE MEAN OF EACH VARIABLE FOR EACH ACTIVITY AND SUBJECT.

- For additional clarity, and organization, the summarized dataset is then arranged in ascending order by activity, then by subject. 


--------------------------------------------

Two files are associated with this submission:

1. run_analysis.R : The R script that processes the original data into a tidy dataset and then outputs a summarized table.
2. TableOfMeansByActivityAndSubject_Week4_Assignment.txt : The output .txt file containing the summarized table.
