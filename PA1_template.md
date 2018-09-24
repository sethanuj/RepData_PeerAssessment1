---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---



## Loading and preprocessing the data
* Loading the data

```r
activity=read.csv("activity.csv", header=TRUE)
```

* Process/transform the data (if necessary) into a format suitable for your analysis

```r
activity$date <- as.Date(activity$date)
names(activity)
```

```
## [1] "steps"    "date"     "interval"
```

```r
str(activity)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Date, format: "2012-10-01" "2012-10-01" ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```

```r
head(activity)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```


## What is mean total number of steps taken per day?
* Make a histogram of the total number of steps taken each day

```r
numSteps<-aggregate(steps~date,data=activity,sum,na.rm=TRUE)
head(numSteps)
```

```
##         date steps
## 1 2012-10-02   126
## 2 2012-10-03 11352
## 3 2012-10-04 12116
## 4 2012-10-05 13294
## 5 2012-10-06 15420
## 6 2012-10-07 11015
```

```r
hist(numSteps$steps, breaks=10, main="Total number of steps per day", xlab="Steps per day", col="steelblue")
```

![](figure/unnamed-chunk-3-1.png)<!-- -->

* Calculate and report the **mean** and **median** total number of steps taken per day 

```r
mean(numSteps$steps, na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(numSteps$steps, na.rm=TRUE)
```

```
## [1] 10765
```


## What is the average daily activity pattern?
* Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
pattern <- aggregate(steps~interval,data=activity,mean,na.rm=TRUE)
plot(steps~interval, data=pattern, type="l", ylab="Number of steps", xlab="5-min intervals",
    main="Average number of steps by 5-minute intervals", col="blue")
```

![](figure/unnamed-chunk-5-1.png)<!-- -->

* Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps? 

```r
pattern[which.max(pattern$steps),]$interval
```

```
## [1] 835
```


## Imputing missing values
* Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

* Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

* Create a new dataset that is equal to the original dataset but with the missing data filled in.

Approach adopted for this exercise: Replace missing values, i.e., NA with zero.


```r
activityWithoutNAs <- activity
activityWithoutNAs[is.na(activityWithoutNAs$steps), "steps"] <- 0
head(activityWithoutNAs)
```

```
##   steps       date interval
## 1     0 2012-10-01        0
## 2     0 2012-10-01        5
## 3     0 2012-10-01       10
## 4     0 2012-10-01       15
## 5     0 2012-10-01       20
## 6     0 2012-10-01       25
```

* Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 

```r
totalSteps2<-aggregate(steps~date,data=activityWithoutNAs,sum)
hist(totalSteps2$steps, breaks=10, main="Total number of steps per day", xlab="Steps per day", col="steelblue")
```

![](figure/unnamed-chunk-9-1.png)<!-- -->

```r
mean(totalSteps2$steps)
```

```
## [1] 9354.23
```

```r
median(totalSteps2$steps)
```

```
## [1] 10395
```

* Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

The **mean** value is less than the value before imputing missing data because we put the missing mean value as 0. 

The **median** value is also less than the value before imputing missing data. 

The varation would vary depending on the strategy adopted for the missing values. 


## Are there differences in activity patterns between weekdays and weekends?
* Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
activityWithoutNAs$day <- "weekday"
activityWithoutNAs$day[weekdays(as.Date(activityWithoutNAs$date), abb=T) %in% c("Sat","Sun")] <- "weekend"
```

* Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:

```r
dt=aggregate(steps~interval+day,activityWithoutNAs,mean)
xyplot(steps~interval|factor(day),data=dt,type="l", grid=TRUE, aspect=1/2, col="steelblue")
```

![](figure/unnamed-chunk-11-1.png)<!-- -->
