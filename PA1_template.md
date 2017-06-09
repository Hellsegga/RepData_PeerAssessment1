Loading and preprocessing the data
----------------------------------

    d <- read.csv("activity.csv")

What is mean total number of steps taken per day?
-------------------------------------------------

    perday <- with(d, aggregate(steps ~date, FUN=sum, na.rm=TRUE))
    hist(perday$steps, main="Steps taken each day", xlab="Number of steps", ylab="Frequency")

![](PA1_template_files/figure-markdown_strict/d-1.png)

    meanPerDay <- as.integer(mean(perday$steps))
    medianPerDay <- as.integer(median(perday$steps))

The mean is 10766 and the median is 10765

What is the average daily activity pattern?
-------------------------------------------

    perinterval <- with(d, aggregate(steps ~interval, FUN=mean))
    with(perinterval, plot(interval, steps, type="l"))

![](PA1_template_files/figure-markdown_strict/e-1.png)

    intervalWithMostSteps <- perinterval$interval[perinterval$steps==max(perinterval$steps)]

The interval with most steps is 835

Imputing missing values
-----------------------

    completeCases <- sum(complete.cases(d))
    missingCases <- nrow(d) - completeCases
    d1 <- d
    meanperday <- with(d, aggregate(steps ~date, FUN=mean, na.rm=TRUE))
    # replace missing values by the mean for that day
    d1$steps <- ifelse(is.na(d1$steps), meanperday$steps[d1$date], d1$steps)
    # it appears some days only have missing values so above will still leave us with some missing values which we fill with global mean
    d1$steps <- ifelse(is.na(d1$steps), mean(meanperday$steps), d1$steps)

    perday <- with(d1, aggregate(steps ~date, FUN=sum, na.rm=TRUE))
    hist(perday$steps, main="Steps taken each day", xlab="Number of steps", ylab="Frequency")

![](PA1_template_files/figure-markdown_strict/f-1.png)

    meanPerDay2 <- as.integer(mean(perday$steps))
    medianPerDay2 <- as.integer(median(perday$steps))
    meanChange <- round((meanPerDay2-meanPerDay)/meanPerDay*100, digits=2)
    medianChange <- round((medianPerDay2-medianPerDay)/medianPerDay*100, digits=2)

There are 2304 missing cases

The mean is 10452 and the median is 10600

The mean with impued missing values differs by -2.92%

The median with imputed missing values differs by -1.53%

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

    d1$date <- as.Date(d1$date)
    d1$w <- factor(c("weekday","weekend"))
    d1$w <- ifelse(weekdays(d1$date)=="Saturday" | weekdays(d1$date)=="Sunday", "weekend", "weekday")
    d1weekday <- d1[d1$w=="weekday",]
    d1weekend <- d1[d1$w=="weekend",]
    perintervalWeekday <- with(d1weekday, aggregate(steps ~interval, FUN=mean))
    perintervalWeekend <- with(d1weekend, aggregate(steps ~interval, FUN=mean))
    par(mfrow=c(1,2))
    with(perintervalWeekday, plot(interval, steps, type="l", main="Activity on weekdays"))
    with(perintervalWeekend, plot(interval, steps, type="l", main="Activity on weekends"))

![](PA1_template_files/figure-markdown_strict/g-1.png)
