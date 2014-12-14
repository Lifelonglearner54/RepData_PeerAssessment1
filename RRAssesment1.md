# RR-PeerAssessment-1
Krishnan Srinivasan  
Saturday, December 13, 2014  
# Introduction
This is Peer Assesment 1 project under the module Reproducible Research of Data Science Specialisation Course offered by Coursera. 

The project is about analysing the data collected about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. 

## Data 
This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

* Data set 
          [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) [52K]

The variables included in this dataset are:

steps: Number of steps taking in a 5-minute interval 

date: The date on which the measurement was taken in YYYY-MM-DD format

interval: Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

### Setting the environment

```r
library ("knitr")
```

```
## Warning: package 'knitr' was built under R version 3.1.2
```

```r
knitr::opts_chunk$set(echo=TRUE)
setwd ("D:/REPRES_ONE")
```

### Download Data

```r
if(!file.exists("getdata-projectfiles-UCI HAR Dataset.zip")) {
    tempFile <- tempfile()
    download.file("http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip",tempFile)
    unzip(tempFile)
    unlink(tempFile)
}
```

### Getting the data and transforming columns

```r
 inFile = "activity.csv"
  data <- read.csv(inFile,
                         header=TRUE,  na.strings="NA",
                         colClasses=c("numeric", "character", "numeric"))
        data$interval <- factor(data$interval)
        data$date <- as.Date(data$date, format="%Y-%m-%d")
        attach(data)
```
#### Look at the summary columns to understand the data values

```r
summary(data)
```

```
##      steps             date               interval    
##  Min.   :  0.00   Min.   :2012-10-01   0      :   61  
##  1st Qu.:  0.00   1st Qu.:2012-10-16   5      :   61  
##  Median :  0.00   Median :2012-10-31   10     :   61  
##  Mean   : 37.38   Mean   :2012-10-31   15     :   61  
##  3rd Qu.: 12.00   3rd Qu.:2012-11-15   20     :   61  
##  Max.   :806.00   Max.   :2012-11-30   25     :   61  
##  NA's   :2304                          (Other):17202
```

```r
attach(data)
```

```
## The following objects are masked from data (pos = 3):
## 
##     date, interval, steps
```
## Answering Questions in the Peer Assesment 
------------------------------------------------
### What is mean total number of steps taken per day?

1. Make a histogram of the total number of steps taken each day


```r
TotSteps <- aggregate(steps~date,data,sum,na.rm=T)
hist(TotSteps$steps,main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps")
```

![](./RRAssesment1_files/figure-html/unnamed-chunk-5-1.png) 

2. Calculate and report the mean and median total number of steps taken per day


```r
AverageSteps <-round (mean(TotSteps$steps),2)
MedianSteps <- round (median(TotSteps$steps),2)
```
Mean is 10766.19 and median is 10765 .

### What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
AverageInterval <- aggregate(steps ~ interval, data, mean,na.rm=T)
plot(AverageInterval$interval,AverageInterval$steps, type="l", xlab="5 minute Interval", ylab="Number of Steps",main="Average Number of Steps per Day by Interval")
```

![](./RRAssesment1_files/figure-html/unnamed-chunk-7-1.png) 
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

Interval 835 contains the maximum number of steps.

### Imputing missing values

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
naRows<-sum(!complete.cases(data))
```
Number of incomplete rows 2304.

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

I prefer to use the mean .

3.Create a new dataset that is equal to the original dataset but with the missing data filled in.

The new dataset called modifiedData .


```r
modifiedData <- transform(data, steps = ifelse(is.na(steps), AverageInterval$steps[match(interval, AverageInterval$interval)], steps))
```
There are 0 records with NA values

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
newsteps<- aggregate(steps ~ date, modifiedData, sum)
hist(newsteps$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps")
```

![](./RRAssesment1_files/figure-html/unnamed-chunk-10-1.png) 

```r
AverageSteps <-round (mean(newsteps$steps),2)
MedianSteps <- round (median(newsteps$steps),2)
```

The new Mean is 10766.19 and median is 10766.19 .

