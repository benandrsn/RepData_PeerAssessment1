Peer Assessment Project
========================================================

Prework: Data Load and Setup



```r
act<-read.csv("activity.csv", colClasses=c("numeric","Date","numeric"))


dss<-(data.frame(tapply(act[,1],act$date,sum, na.rm=TRUE))) #for daily average
dss$date<-rownames(dss)
names(dss)[names(dss)=="tapply.act...1...act.date..sum..na.rm...TRUE."] <-"steps"
rownames(dss)<-1:61

dsm<- (data.frame(tapply(act[,1],act$interval,mean, na.rm=TRUE))) #for time series
dsm$interval<-rownames(dsm)
names(dsm)[names(dsm)=="tapply.act...1...act.interval..mean..na.rm...TRUE."] <-"meanSteps"
rownames(dsm)<-1:nrow(dsm)
```

Question 1: Plot, Median, Mean
This shows a histogram of the data collapsed by day:

```r
barplot(dss$steps, names.arg=rownames(dss), main="Steps per Day",
        xlab="Day",ylab="Steps taken")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

This is the code to get the mean and the median of 
this number of steps taken per day:

```r
mean(dss$steps)
```

```
## [1] 9354
```

```r
median(dss$steps)
```

```
## [1] 10395
```

Question 2: Time series plot
Average steps taken by time interval

This code shows that number of steps taken per time interval.

```r
plot(dsm$interval,dsm$meanSteps, type="l", main="Average Steps by Interval",
     xlab="Interval", ylab="Mean Steps")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

Question 3: NA
How many NAs are there?

```r
length(act$steps[is.na(act$steps)])
```

```
## [1] 2304
```

Here I replace the NAs with the average number of steps taken during this time interval.

```r
library(plyr)
impute.mean <- function(x) replace(x, is.na(x), mean(x, na.rm = TRUE))
act2 <- ddply(act, ~ interval, transform, steps = impute.mean(steps))
act2<-act2[order(act2$date,act2$interval), ]
rownames(act2)<-1:nrow(act2)
```

act2 is our new dataset that has the NAs filled in. With this, we will do the 
same as we did with Question 1 (create a histogram and find the median and mean).


```r
dss2<-(data.frame(tapply(act2[,1],act2$date,sum, na.rm=TRUE))) #for daily average
dss2$date<-rownames(dss2)
names(dss2)[names(dss2)=="tapply.act2...1...act2.date..sum..na.rm...TRUE."] <-"steps"
rownames(dss2)<-1:61
```


```r
barplot(dss2$steps, names.arg=rownames(dss2), main="Steps per Day",
        xlab="Day",ylab="Steps taken")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 

```r
mean(dss2$steps)
```

```
## [1] 10766
```

```r
median(dss2$steps)
```

```
## [1] 10766
```

The values for the median and the mean do not change since I used the mean 
values to replace the NAs.  The means and medians stayed the same.

Question 4: Weekdays v. Weekends
This code creates a factor for weekday and weekend and then puts the data into a
format that is easy to work with for graphing.

```r
act2$dow<-weekdays(act2$date)
act2$factor<-act2$factor <- act2$dow %in% c("Sunday","Saturday")
act2$factor <- factor(act2$factor,labels=c("Weekday","Weekend"))

library(reshape2)
act3 <- melt(subset(act2, select = c("interval", "steps", "factor")),
                              measure.vars="steps")
act3<-dcast(act3, interval ~ factor, fun.aggregate=mean, value.var="value")
```
These are the two plots:

```r
plot(act3$interval,act3$Weekday, type="l", main="Average Steps by Interval-Weekday",
     xlab="Interval", ylab="Mean Steps",ylim=c(0, 240))
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11.png) 

```r
plot(act3$interval,act3$Weekend, type="l", main="Average Steps by Interval-Weekend",
     xlab="Interval", ylab="Mean Steps", ylim=c(0, 240))
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12.png) 
