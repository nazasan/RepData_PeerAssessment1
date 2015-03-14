# Reproducible Research: Peer Assessment 1
Nazaret Sánchez  


## Loading and preprocessing the data
Load the data from a .csv file:   

```r
data <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?
Calculate the total number of steps taken per day  

```r
library(plyr)
steps_per_day <- ddply(data, .(date), summarize, steps = sum(steps))
```

Make a histogram of the total number of steps taken each day  

```r
barplot(steps_per_day$steps, names.arg=steps_per_day$date, xlab="Date", ylab="Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

Calculate and report the mean and median of the total number of steps taken per day  

```r
mean(steps_per_day$steps, na.rm = TRUE)
```

```
## [1] 10766.19
```

```r
median(steps_per_day$steps, na.rm = TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern
Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
library(plyr)
steps_per_interval <- ddply(data, .(interval), summarize, steps = mean(steps, na.rm = TRUE))
plot(steps_per_interval, type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 
Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
steps_per_interval$interval[which.max(steps_per_interval$steps)]
```

```
## [1] 835
```

## Imputing missing values
Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(data))
```

```
## [1] 2304
```
Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.    
I change the NA by zero.   
   
Create a new dataset that is equal to the original dataset but with the missing data filled in.  

```r
data2 <- data
data2[is.na(data2)] <- 0
```
Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
library(plyr)
steps_per_day2 <- ddply(data2, .(date), summarize, steps = sum(steps))
barplot(steps_per_day2$steps, names.arg=steps_per_day2$date, xlab="Date", ylab="Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png) 

```r
mean(steps_per_day2$steps)
```

```
## [1] 9354.23
```

```r
median(steps_per_day2$steps)
```

```
## [1] 10395
```

## Are there differences in activity patterns between weekdays and weekends?
Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
weekday <- weekdays(as.Date(data2$date, "%Y-%m-%d"))
for (i in 1:length(weekday)) {
    if ((weekday[i] == "sabado") | (weekday[i] == "domingo")) 
        weekday[i] = "weekend" else weekday[i] = "weekday"
}
data2$weekday <- as.factor(weekday)
```

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 

```r
library(lattice)
xyplot(steps ~ interval | weekday, data = data2, type = "l",layout = c(1, 2), 
       ylab = "number of steps", 
        main = "Average number of steps for all weekday days or weekend days")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 
