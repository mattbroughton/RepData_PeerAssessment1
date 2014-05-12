Project 1 for Reproducible Research
========================================================

This is an R Markdown document. Markdown is a simple formatting syntax for authoring web pages (click the **Help** toolbar button for more details on using R Markdown).

When you click the **Knit HTML** button a web page will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

## Loading and preprocessing the data

```r
# Read in the data and make sure the date is a date class
dataIn <- read.csv("activity.csv")
dataIn$date <- as.Date(dataIn$date)


# Sum the number of steps by dates
dailySum <- aggregate(dataIn$steps, by = list(dataIn$date), sum)
```


## Make a histogram of the total number of steps per day


```r
hist(dailySum$x, xlab = "Total number of steps per day", main = "Daily Number of Steps")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 


## Calculate the mean and median number of steps per day

```r
meanSteps <- mean(dailySum$x, na.rm = TRUE)
medianSteps <- median(dailySum$x, na.rm = TRUE)
```

The mean number of steps per day is 1.0766 &times; 10<sup>4</sup>. The median number of steps per day is 10765.

## Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis).

```r
intervalMean <- aggregate(dataIn$steps, by = list(dataIn$interval), mean, na.rm = TRUE)
plot(intervalMean$Group.1, intervalMean$x, type = "l", xlab = "5-minute Interval", 
    ylab = "Mean Number of Steps ")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

```r
maxInterval <- intervalMean[intervalMean$x == max(intervalMean$x), 1]
```

The interval with the greatest average number of steps across all days is interval 835.

## Inputing missing values

### Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
numNa <- sum(is.na(dataIn$steps))
```

The dataset contains 2304 rows with missing values. For each missing value, we will substitue the mean value from that interval.

### Devise a strategy for filling in all of the missing values in the dataset. Create a new dataset that is equal to the original dataset but with the missing data filled in.

My strategy si to replace each missing value with the mean value from the data set.


```r
dataInNoNa <- dataIn
for (i in 1:nrow(intervalMean)) {
    dataInNoNa$steps[dataInNoNa$interval == intervalMean$Group.1[i] & is.na(dataInNoNa$steps)] <- intervalMean$x[i]
}
```

### Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day.

```r
dailySumNoNa <- aggregate(dataInNoNa$steps, by = list(dataInNoNa$date), sum)

hist(dailySumNoNa$x, xlab = "Total number of steps per day", main = "Daily Number of Steps (Nas Replaced)")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7.png) 

```r
meanStepsNoNa <- mean(dailySum$x, na.rm = TRUE)
medianStepsNoNa <- median(dailySum$x, na.rm = TRUE)
```

The mean number of steps per day is 1.0766 &times; 10<sup>4</sup>. The median number of steps per day is 10765. My replacement of the ` r NA` values had no effect on either the median or the mean, but it did change the standard deviation, or spread, of the data.

## Are there differences in activity patterns between weekdays and weekends?

### Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
dataIn$WeekendWeekday <- "Weekday"
dataIn$WeekendWeekday[weekdays(dataIn$date) == "Saturday" | weekdays(dataIn$date) == 
    "Sunday"] <- "Weekend"
dataIn$WeekendWeekday <- as.factor(dataIn$WeekendWeekday)
```


### Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 

```r
weekendSteps <- aggregate(dataIn$steps[dataIn$WeekendWeekday == "Weekend"], 
    by = list(dataIn$interval[dataIn$WeekendWeekday == "Weekend"]), mean, na.rm = TRUE)
weekdaySteps <- aggregate(dataIn$steps[dataIn$WeekendWeekday == "Weekday"], 
    by = list(dataIn$interval[dataIn$WeekendWeekday == "Weekday"]), mean, na.rm = TRUE)

par(mfrow = c(2, 1))
plot(weekendSteps$Group.1, weekendSteps$x, ylab = "Number of steps", main = "Weekend", 
    xlab = "5-minute Interval", type = "l", ylim = c(0, 250))

plot(weekdaySteps$Group.1, weekdaySteps$x, ylab = "Number of steps", main = "Weekday", 
    xlab = "5-minute Interval", type = "l", ylim = c(0, 250))
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 


