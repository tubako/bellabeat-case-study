# Bellabeat Case Study

## Business task

Analyzing another brand's smart device usage data in order to improve Bellabeat's future marketing strategies. 
    >>number of steps/sleep vs number of intensity/sleep
    
## The data
The data that will be used in this study is called "FitBit Fitness Tracker Data" from Kaggle, made available by Mobius. It was collected through a survey via Amazon's Mechanical Turk. The sample size of the data is not ideal --just 30 eligible FitBit users. Still, it is enough to get an idea on trends surrounding smart device usage. Keeping the business task in mind, out of the 18 files that are present in the data, only 4 of them will be used in this study.
   
    
```{r}
daily_activity <- read_csv("Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
daily_sleep <- read_csv("Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
heart_rate <- read_csv("Fitabase Data 4.12.16-5.12.16/heartrate_seconds_merged.csv")
hourly_steps <- read_csv("Fitabase Data 4.12.16-5.12.16/hourlySteps_merged.csv")
```
## Cleaning and transforming the data

To do this analysis, R programming language seems the easiest and most efficient one to use. Since the data is not too big, but it consists of multiple spreadsheets, any merging or cleaning will be done very quickly through R. In this project, following packages will be used: .... 
    
```{r}
library(tidyverse)
library(snakecase)
library(skimr)

```
    
I will start the cleaning process by checking the metadata, to see if they are spelled or written correctly, if they have any uppercase characters or any disorder in general.
    
    
```{r}
head(daily_activity)
head(daily_sleep)
head(heart_rate)
head(hourly_steps)
```
Now it would be ideal to confirm how many users are participating, to see if it actually is 30 for all the data sets. For this, checking how many unique ids there are would be enough.

```{r}
n_unique(daily_activity$id)
n_unique(daily_sleep$id)
n_unique(heart_rate$id)
n_unique(hourly_steps$id)
```
hourly_steps and daily_activity data sets have sufficient participants. For daily_sleep and heart_rate data sets, participants are less than 30 which is not ideal, however for the sake of this study, these data sets will still be used.

From these previews of these data frames, it is understood that changing the column names from camel case (ex. ActivityDate) into snake case (ex. activity_date) is necessary. 

```{r}
names(daily_activity) <- to_snake_case(names(daily_activity))
names(daily_sleep) <- to_snake_case(names(daily_sleep))
names(heart_rate) <- to_snake_case(names(heart_rate))
names(hourly_steps) <- to_snake_case(names(hourly_steps))
```


Also even though the data set daily_sleep contains daily sleep data, time of sleep is also present with the date of sleep which is irrelevant and unnecessary. So it is better to change the date format from datetime to just date. 

```{r}
daily_sleep <- daily_sleep %>% 
  mutate(sleep_day= as_date(sleep_day, format= "%m/%d/%Y  %I:%M:%S %p"))
```


While at it, it is good to make all date formats the same.

```{r}
daily_activity <- daily_activity %>% 
  mutate(activity_date= as_date(activity_date, format= "%m/%d/%Y"))
hourly_steps <- hourly_steps %>% 
  mutate(activity_hour= as_datetime(activity_hour, format= "%m/%d/%Y  %I:%M:%S %p"))
```

Next step in the cleaning process will be checking and getting rid of any missing values. 

```{r}
sum(is.na(daily_activity))
sum(is.na(daily_sleep))
sum(is.na(heart_rate))
sum(is.na(hourly_steps))
```

According to the results, the data sets do not contain any missing values. Finally it is time to check for duplicate values.

```{r}
sum(duplicated(daily_activity))
sum(duplicated(daily_sleep))

sum(duplicated(hourly_steps))
```

Knowing daily_sleep has 3 duplicate values, it is good to get rid of them and check if they have been removed for sure.

```{r}
daily_sleep <- distinct(daily_sleep)
sum(duplicated(daily_sleep))
```

Okay, daily_sleep data set also only consists of distinct values.  

## Analyzing

```{r}
average_daily_activity <- daily_activity %>%
  group_by(id) %>% 
  summarise(average_steps = mean(total_steps), average_sedentary_minutes = mean(sedentary_minutes), 
  average_active_minutes = mean(mean(very_active_minutes), mean(fairly_active_minutes), 
  mean(lightly_active_minutes)), average_sedentary_minutes= mean(sedentary_minutes))

head(average_daily_activity)
```


```{r}
ggplot(data = average_daily_activity, aes( = average_sedentary_minutes)) + geom_bar()
  
```

