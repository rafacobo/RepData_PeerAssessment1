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

```r
# For this part of the assignment, you can ignore the missing values in the dataset
sbyd <- data.frame(aggregate(steps ~ date, sum, data = dataset, na.rm = TRUE))
# Make a histogram of the total number of steps taken each day
hist(sbyd$steps)
```

![plot of chunk unnamed-chunk-2](PA1_template_files/figure-html/unnamed-chunk-2.png) 

```r
# Calculate and report the mean and median total number of steps taken per day
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

```r
# Make a time series plot of the 5-minute interval (x-axis)  and the average number 
# of steps taken, averaged across all days (y-axis)
mbyi <- data.frame(aggregate(steps ~ interval, mean, data = dataset))
plot(mbyi$interval, mbyi$steps, type = "l", 
  	xlab = "Average Number of Steps", ylab = "5-Minute Interval")
grid()
```

![plot of chunk unnamed-chunk-3](PA1_template_files/figure-html/unnamed-chunk-3.png) 

```r
# Which 5-minute interval, on average across all the days in the dataset, 
# contains the maximum number of steps?
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

![plot of chunk unnamed-chunk-6](PA1_template_files/figure-html/unnamed-chunk-6.png) 

# Calculate and report the mean and median total number of steps taken per day
median(newsbyd$steps)
mean(newsbyd$steps)
# Do these values differ from the estimates from the first part of the assignment? 
# What is the impact of imputing missing data on the estimates of the total daily number of steps?
```

## Are there differences in activity patterns between weekdays and weekends?
