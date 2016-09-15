## Week 4: Tidy Data Assignment
## "run_analysis.R"
 
library("downloader") ## Requires <install.packages("downloader")>
      
      ## Assigning the link
      site <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
      
      ## Downloading and unzipping the .zip file
      download(site, dest="./dataset.zip", mode="wb") 
      unzip ("./dataset.zip", exdir = "./unzipped")
      
      ## Assigning the paths for training and testing files
      train_s <- "./unzipped/UCI HAR Dataset/train/subject_train.txt"
      train_X <- "./unzipped/UCI HAR Dataset/train/X_train.txt"
      train_Y <- "./unzipped/UCI HAR Dataset/train/y_train.txt"
      test_s <- "./unzipped/UCI HAR Dataset/test/subject_test.txt"
      test_X <- "./unzipped/UCI HAR Dataset/test/X_test.txt"
      test_Y <- "./unzipped/UCI HAR Dataset/test/y_test.txt"
      
library("dtplyr") ## data.table + dplyr code now lives in "dtplyr" package. Please library(dtplyr)!      
      
      ## Reading in the "features" file - This file is to be used as the variable names of the data set
      features <- "./unzipped/UCI HAR Dataset/features.txt"
      feat <- read.table(features)
      f <- feat$V2 ## `f` is the string containing the names of the variables of the all data sets
                   ## NOTE: f contains duplicates which must be removed in order for the data to be manipulated by
                   ## the "dtplyr" package, because a "tbl_dt" format does not allow for duplicate column names
      
      ## Reading in the training and testing .txt files - THE "X" TABLES HAVE DUPLICATE VARIABLE NAMES
      trs <- read.table(train_s)
      trx <- read.table(train_X, col.names = f, check.names = FALSE)
      try <- read.table(train_Y)
      
      tes <- read.table(test_s)
      tex <- read.table(test_X, col.names = f, check.names = FALSE)
      tey <- read.table(test_Y)
      
      ## Indexing duplicate column names from `f`
      ind <- which(duplicated(f))
      
      ## "Selecting" only those columns whose variable names are not duplicates - So that we then coerce into 
      ## tbl_dt format and begin to use the "dtplyr" package to clip and merge the data sets
      train_x_unique_vars <- tbl_dt(trx[,-(ind)])
      test_x_unique_vars <- tbl_dt(tex[,-(ind)])
      
      ## Selecting only those columns for variables related to the mean(), meanFreq(), and std() measurements
      ## I chose to include the meanFreq() measurements because, strictly speaking, it is a measurement
      ## on the mean of the frequency, which is itself a measurement, satisfying Step 2 of the instructions.
      train_x_mean_std <- select(train_x_unique_vars, contains("mean()"), contains("meanFreq()"), contains("std()"))
      test_x_mean_std <- select(test_x_unique_vars, contains("mean()"), contains("meanFreq()"), contains("std()"))
      
      ## Modifying the "train_y.txt" and "test_y.txt" files to create strings that will be added as columns to 
      ## the train_x_mean_std and test_x_mean_std data tables in order relate observations to activity names
      activity <- "./unzipped/UCI HAR Dataset/activity_labels.txt"
      act <- read.table(activity)
      
      try[try == 1] <- "walklevelground"; try[try == 2] <- "walkupstairs"; try[try == 3] <- "walkdownstairs"
      try[try == 4] <- "sitting"; try[try == 5] <- "standing"; try[try == 6] <- "laying"
      tey[tey == 1] <- "walklevelground"; tey[tey == 2] <- "walkupstairs"; tey[tey == 3] <- "walkdownstairs"
      tey[tey == 4] <- "sitting"; tey[tey == 5] <- "standing"; tey[tey == 6] <- "laying"
      
      ## Adding a column to each the train and test data sets to include the activity name for each observation
      train_x_ms_act <- mutate(train_x_mean_std, activity = try)
      test_x_ms_act <- mutate(test_x_mean_std, activity = tey)
      
      ## Adding a column to each the train and test data sets, for a new variable representing whether
      ## the observation is related to training or testing - This is being done so that the two data sets can be
      ## merged under their common variable names, bringing the 7,352 and 2,947 rows together
      ## to form 10,299 total rows for the merged data set.
      train_x_ms_act_sub <- mutate(train_x_ms_act, subject = trs)
      test_x_ms_act_sub <- mutate(test_x_ms_act, subject = tes)      
      
      ## MERGING THE DATASETS INTO A SINGLE DATA FRAME (tbl_dt format) - This operation adds the rows of the
      ## testing dataset beneath the rows of the training dataset, to form one complete dataset as per Step 1.
      step4_DATA_MERGED <- bind_rows(train_x_ms_act_sub, test_x_ms_act_sub) ## dim(DATA_MERGED) = 10,299 x 81 as planned
      step4_output <- step4_DATA_MERGED
      ## *** The order of the rows of the datasets, the activity names, and the subject information is preserved 
      ## in this method, so that the complete dataset "DATA MERGED" contains all of the information required for
      ## each Step1, Step 2, and Step 3. The variables representing measurements on the mean and standard deviation
      ## on each variable are also preserveed in their proper order, and are descriptive, following the exact same
      ## naming as described in the original "features.txt" file. ***
      
      ## GROUPING THE DATA BY "ACTIVITY" AND "SUBJECT"
      step5_DATA_MERGED_GROUPED <- group_by(step4_DATA_MERGED, activity, subject) 
      
      ## TAKING THE MEAN OF EACH VARIABLE FOR EACH ACTIVITY AND SUBJECT
      step5_DATA_MEAN <- summarise(step5_DATA_MERGED_GROUPED, mean)
      step5_output <- arrange(step5_DATA_MEAN, activity, subject)
      ## Printing the outputs of steps 4 and 5
      print(step4_output)
      
      print(step5_output)
      
      ## Writing the step 5 output to a .csv file
      
      write.table(step5_output, "./TableOfMeansByActivityAndSubject_Week4_Assignment.txt", row.names = FALSE)
      
      ###### THIS COMPLETES THE ASSIGNMENT ######
