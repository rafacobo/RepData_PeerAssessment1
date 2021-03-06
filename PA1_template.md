# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Setting working folder, unzipping, reading the csv file and formatting dates

```r
setwd("/Users/rafaelcobolopez/Desktop/courseproject/RRPeer1/RepData_PeerAssessment1/RepData_PeerAssessment1")
unzip("activity.zip", "activity.csv")
dataset <- read.csv("activity.csv")
dataset$date <- as.Date(dataset$date, format = "%Y-%m-%d")
```

## What is mean total number of steps taken per day?
Let's see a histogram of the total number of steps taken each day (for this part of the assignment, you can ignore the missing values in the dataset).

```r
sbyd <- data.frame(aggregate(steps ~ date, sum, data = dataset, na.rm = TRUE))
hist(sbyd$steps)
```

![plot of chunk unnamed-chunk-2](PA1_template_files/figure-html/unnamed-chunk-2.png) 

Calculate and report the mean and median total number of steps taken per day

```r
median(sbyd$steps, na.rm = TRUE)
```

```
## [1] 10765
```

```r
mean(sbyd$steps, na.rm = TRUE)
```

```
## [1] 10766
```

## What is the average daily activity pattern?
Make a time series plot of the 5-minute interval (x-axis)  and the average number of steps taken, averaged across all days (y-axis)

```r
mbyi <- data.frame(aggregate(steps ~ interval, mean, data = dataset))
plot(mbyi$interval, mbyi$steps, type = "l", 
  	xlab = "5-Minute Interval", ylab = "Average Number of Steps")
grid()
```

![plot of chunk unnamed-chunk-4](PA1_template_files/figure-html/unnamed-chunk-4.png) 

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
mbyi[which(mbyi$steps == max(mbyi$steps)),]
```

```
##     interval steps
## 104      835 206.2
```

## Imputing missing values
The presence of missing days may introduce bias into some calculations or summaries. Calculate and report the total number of missing values in the dataset 

```r
dim(data.frame(which(is.na(dataset))))[1]
```

```
## [1] 2304
```

Devise a strategy for filling in all of the missing values in the dataset. For example, we will use the mean for that 5-minute interval. Create a new dataset equal to the original dataset but with the NA filled in.

```r
fdataset <- dataset
myna <- which(is.na(fdataset$steps))
for (i in 1:dim(data.frame(myna))[1]) {
  fdataset[myna[i],1] <- mbyi[which(mbyi$interval == fdataset[myna[i],3]),2]
}
```

Make a histogram of the total number of steps taken each day

```r
newsbyd <- data.frame(aggregate(steps ~ date, sum, data = fdataset))
hist(newsbyd$steps)
```

![plot of chunk unnamed-chunk-8](PA1_template_files/figure-html/unnamed-chunk-8.png) 

Calculate and report the mean and median total number of steps taken per day

```r
median(newsbyd$steps)
```

```
## [1] 10766
```

```r
mean(newsbyd$steps)
```

```
## [1] 10766
```

Do these values differ from the estimates from the first part of the assignment? 
What is the impact of imputing missing data on the estimates of the total daily number of steps? 
The value for the mean is the same, and the value for the median is almost the same. In this case the impact of imputing missing data is very low.

## Are there differences in activity patterns between weekdays and weekends?
Yes, there are differences in activity patterns between weekdays and weekends

Using the dataset with the filled-in missing values create a new factor variable in the dataset with two levels weekday and weekend indicating whether a given date is a weekday or weekend day.

```r
fdataset$daytype <- "weekday"
x <- which(weekdays(fdataset$date)=="Saturday"|weekdays(fdataset$date)=="Sunday")
fdataset$daytype[x] <- "weekend"
fdataset$daytype <- as.factor(fdataset$daytype)
```

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 

```r
library(lattice)
fdataw <- fdataset[which(fdataset$daytype == "weekday"),]
fdatawe <- fdataset[which(fdataset$daytype == "weekend"),]
fmbyiw <- data.frame(aggregate(steps ~ interval, mean, data = fdataw))
fmbyiw$daytype <- "weekday"
fmbyiwe <- data.frame(aggregate(steps ~ interval, mean, data = fdatawe))
fmbyiwe$daytype <- "weekend"
fmbyi <- rbind(fmbyiw, fmbyiwe)
fmbyi$daytype <- as.factor(fmbyi$daytype)

xyplot(steps ~ interval | daytype, data = fmbyi,  type = "l", layout = c(1, 2),
  xlab = "Interval", ylab = "Average Number of Steps")
```

![plot of chunk unnamed-chunk-11](PA1_template_files/figure-html/unnamed-chunk-11.png) 
