Reproducible Research project week 2
================

## Loading and preprocessing the data

Show any code that is needed to

1.  Load the data (i.e. read.csv())
2.  Process/transform the data (if necessary) into a format suitable for
    your analysis

<!-- end list -->

``` r
step_counts <- read.csv2("activity.csv", header = TRUE, sep = ",")
```

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in
the dataset.

1.  Calculate the total number of steps taken per day

<!-- end list -->

``` r
steps_daily <- aggregate(steps ~ date, data = step_counts, sum)
```

2.  If you do not understand the difference between a histogram and a
    barplot, research the difference between them. Make a histogram of
    the total number of steps taken each day

<!-- end list -->

``` r
hist(steps_daily$steps)
```

![](PA1_template_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

3.  Calculate and report the mean and median of the total number of
    steps taken per day

<!-- end list -->

``` r
mean(steps_daily$steps)
```

    ## [1] 10766.19

``` r
median(steps_daily$steps)
```

    ## [1] 10765

## What is the average daily activity pattern?

1.  Make a time series plot (i.e. type = “l”) of the 5-minute interval
    (x-axis) and the average number of steps taken, averaged across all
    days (y-axis)

<!-- end list -->

``` r
steps_interval <- aggregate(steps ~ interval, data = step_counts, mean)
plot(steps_interval, type = "l")
```

![](PA1_template_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

2.  Which 5-minute interval, on average across all the days in the
    dataset, contains the maximum number of steps?

<!-- end list -->

``` r
steps_interval[which.max(steps_interval$steps), 1]
```

    ## [1] 835

## Imputing missing values

Note that there are a number of days/intervals where there are missing
values (coded as NA). The presence of missing days may introduce bias
into some calculations or summaries of the data.

1.  Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with NAs)

<!-- end list -->

``` r
sum(is.na(step_counts$steps))
```

    ## [1] 2304

2.  Devise a strategy for filling in all of the missing values in the
    dataset. The strategy does not need to be sophisticated. For
    example, you could use the mean/median for that day, or the mean for
    that 5-minute interval, etc.

Strategy is to replace the NA values with the mean for that 5-minute
interval

3.  Create a new dataset that is equal to the original dataset but with
    the missing data filled in.

<!-- end list -->

``` r
for(i in 1:17568) {
    if(is.na(step_counts$steps[i])) {
        if(i %% 288 == 0) { step_counts$steps[i] <- steps_interval$steps[288] }
        else { step_counts$steps[i] <- steps_interval$steps[i %% 288] }
    }
}
```

4.  Make a histogram of the total number of steps taken each day and
    Calculate and report the mean and median total number of steps taken
    per day. Do these values differ from the estimates from the first
    part of the assignment? What is the impact of imputing missing data
    on the estimates of the total daily number of steps?

<!-- end list -->

``` r
steps_daily <- aggregate(steps ~ date, data = step_counts, sum)
hist(steps_daily$steps)
```

![](PA1_template_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
mean(steps_daily$steps)
```

    ## [1] 10766.19

``` r
median(steps_daily$steps)
```

    ## [1] 10766.19

The mean is exactly the same as before with the missing values. The
median is in this case slightly higher and equal to the mean.

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the
dataset with the filled-in missing values for this part.

1.  Create a new factor variable in the dataset with two levels –
    “weekday” and “weekend” indicating whether a given date is a
    weekday or weekend day.

<!-- end list -->

``` r
step_counts$date <- as.Date(step_counts$date)
days <- c('maandag', 'dinsdag', 'woensdag', 'donderdag', 'vrijdag', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday')
step_counts$day <- factor((weekdays(step_counts$date) %in% days), levels=c(FALSE, TRUE), labels=c('weekend', 'weekday'))
```

2.  Make a panel plot containing a time series plot (i.e. type = “l”) of
    the 5-minute interval (x-axis) and the average number of steps
    taken, averaged across all weekday days or weekend days (y-axis).
    See the README file in the GitHub repository to see an example of
    what this plot should look like using simulated data.

<!-- end list -->

``` r
library(lattice)
steps_interval <- aggregate(steps ~ interval + day, data = step_counts, mean)
xyplot(steps_interval$steps ~ steps_interval$interval | steps_interval$day, layout = c(1, 2), type = "l", xlab = "Interval", ylab = "Number of steps")
```

![](PA1_template_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->
