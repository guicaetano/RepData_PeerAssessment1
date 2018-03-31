---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data
##### 1. Load libraries


```r
library(dplyr)
library(ggplot2)
library(lubridate)
library(tidyr)
```

##### 2. Read files into R and converting date into date class


```r
activity <- read.csv("activity.csv")

activity$date <- ymd(activity$date)
```

## What is mean total number of steps taken per day?

###1.Calculate the total number of steps taken per day


```r
steps_day <- aggregate(activity["steps"], by = activity["date"], sum)
```

##### 2. Make a histogram of the total number of steps taken each day


```r
hist(steps_day$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

##### 3. Calculate and report the mean and median of the total number of steps taken per day


```r
median_steps<- median(steps_day$steps, na.rm = TRUE)

mean_steps<- mean(steps_day$steps, na.rm = TRUE)
```

* Mean: 

```
## [1] 10766.19
```

* Median:  

```
## [1] 10765
```

## What is the average daily activity pattern?

### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
steps_by_minute <- aggregate(activity["steps"], by = activity["interval"], mean, na.rm = TRUE)
plot(y = steps_by_minute$steps,x = steps_by_minute$interval, type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

### 2.Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
which.max(steps_by_minute$steps)
```

```
## [1] 104
```

## Imputing missing values

### 1.Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
sum(is.na(activity))
```

```
## [1] 2304
```

### 2.Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

Decided to consider each NA Value as "0"


```r
activity_no_NA <- fill(activity, steps, .direction = "up")
activity_no_NA <- fill(activity_no_NA, steps)
```

### 3.Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
steps_day_no_NA <- aggregate(activity_no_NA["steps"], by = activity["date"], sum, na.rm = TRUE)
```

### 4.Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
hist(steps_day_no_NA$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

*Median without NA

```r
median(steps_day_no_NA$steps)
```

```
## [1] 10395
```

*Mean without NA

```r
mean(steps_day_no_NA$steps)
```

```
## [1] 9354.23
```

## Are there differences in activity patterns between weekdays and weekends?

### 1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
activity$weekday <- ifelse (weekdays(activity$date) %in% c("sábado","domingo"),"weekend", "weekday")
```

### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis)


```r
activity_week <- aggregate(steps ~ interval + weekday, data = activity, mean, na.rm = TRUE)
ggplot(activity_week,aes(interval, steps)) + geom_line() + facet_grid(weekday~.)
```

![](PA1_template_files/figure-html/unnamed-chunk-17-1.png)<!-- -->


