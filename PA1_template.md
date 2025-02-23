---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---



## Loading and preprocessing the data
if (!file.exists("activity.csv") ) {
        unzip("activity.zip")
}
raw_data <- read.csv("activity.csv", header = TRUE)
main_data <- na.omit(raw_data)


## What is mean total number of steps taken per day?
```
steps_per_day <- aggregate(main_data$steps, by = list(Steps.Date = main_data$date), FUN = "sum")
hist(steps_per_day$x, col = "green", 
     breaks = 20,
     main = "Total number of steps taken each day",
     xlab = "Number of steps per day")
mean_steps <- mean(steps_per_day[,2])
```
print (mean_steps) 
```
median_steps <- median(steps_per_day[,2])
```
print (median_steps)

## What is the average daily activity pattern?
```
avaraged_day <- aggregate(main_data$steps, 
                          by = list(Interval = main_data$interval), 
                          FUN = "mean")
```
plot(avaraged_day$Interval, avaraged_day$x, type = "l", 
     main = "Average daily activity pattern", 
     ylab = "Avarage number of steps taken", 
     xlab = "5-min intervals")
interval_row <- which.max(avaraged_day$x)
max_interval <- avaraged_day[interval_row,1]
print (max_interval)     


## Imputing missing values
```
NA_number <- length(which(is.na(raw_data$steps)))
print (NA_number)
library(Hmisc)
raw_data_filled <- raw_data
raw_data_filled$steps <- impute(raw_data$steps, fun=mean)
steps_per_day_noNA <- aggregate(raw_data_filled$steps, 
                                by = list(Steps.Date = raw_data_filled$date), 
                                FUN = "sum")
hist(steps_per_day_noNA$x, col = "green", 
     breaks = 20,
     main = "Total number of steps taken each day (filled data)",
     xlab = "Number of steps per day")
mean_steps_noNA <- mean(steps_per_day_noNA[,2])
```
print (mean_steps_noNA)     
```
median_steps_noNA <- median(steps_per_day_noNA[,2])
```

print (median_steps_noNA)     

## Are there differences in activity patterns between weekdays and weekends?
```
raw_data_filled$date <- as.Date(raw_data_filled$date)
raw_data_filled$weekday <- weekdays(raw_data_filled$date)
raw_data_filled$day_type <- ifelse(raw_data_filled$weekday=="суббота" |
                                           raw_data_filled$weekday=="воскресенье","Weekend","Weekday")
raw_data_filled$day_type <- factor(raw_data_filled$day_type)

day_types_data <- aggregate(steps ~ interval + day_type, data=raw_data_filled, mean)

library(ggplot2)
```
ggplot(day_types_data, aes(interval, steps)) + 
        geom_line() + 
        facet_grid(day_type ~ .) +
        xlab("5-minute intervals") + 
        ylab("Avarage number of steps taken") +
        ggtitle("Weekdays and weekends activity patterns")
        
