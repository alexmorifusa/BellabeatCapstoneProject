# Google Analytics Course CapStone Project: Bellabeat

## Scenario:
You are a junior data analyst working on the marketing analyst team at Bellabeat, a high-tech manufacturer of
health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger
player in the global smart device market. Urška Sršen, cofounder and Chief Creative Officer of Bellabeat, believes that
analyzing smart device fitness data could help unlock new growth opportunities for the company. You have been asked to
focus on one of Bellabeat’s products and analyze smart device data to gain insight into how consumers are using their smart
devices. The insights you discover will then help guide marketing strategy for the company. You will present your analysis to
the Bellabeat executive team along with your high-level recommendations for Bellabeat’s marketing strategy.

## 1) Ask
### Business Task
Analyze the given Fitbit data to gain insight and guide marketing strategies for Bellabeat.
#### Primary Stakeholders
Executive Team Members: Urška Sršen and Sando Mur
#### Secondary Stakeholders
Bellabeat marketing team

## 2) Prepare
### Data Set
Fitbit Data: https://www.kaggle.com/datasets/arashnic/fitbit/versions/1

### Choosing Data
I decided to focus on exploring data sets that would best portray the main purpose that users use the Fitbit for and explore other possible uses for the Fitbit.

#### dailyActivity_merged.csv
For broad and more general data. 
#### weightLogInfo_merged.csv
To see if users are focusing on weight loss as their main fitness goal.
#### dailyCalories_merged.csv
In addition to weight, users may be focusing on calorie loss to maximize health goals.
#### dailyIntensities_merged.csv
Check if there is a correlation between workout time and calories loss. I can also explore various intensities and their health effects.

## 3) Process
### Importing data
Unzip the files and use read.csv() to import CSV files.
```{r analysis}
daily_activity <- read.csv("dailyActivity_merged.csv")
weight <- read.csv("weightLogInfo_merged.csv")
daily_cal <- read.csv("dailyCalories_merged.csv")
daily_int <- read.csv("dailyIntensities_merged.csv")
```
### Cleaning the Data
#### Check for NA and remove duplicates
```{r analysis}
sum(is.na(daily_activity)
sum(duplicated(daily_activity))

sum(is.na(weight))
# Contained a lot of NAs due to the Fat column
# The NAs will not disrupt with the data
sum(duplicated(weight))

sum(is.na(daily_cal))
sum(duplicated(daily_cal))

sum(is.na(daily_cal))
sum(duplicated(daily_cal))
```
Convert Date column of weight to ActvivityDate like the other data sets to make joining the data sets possible.
```{r analysis}
weight <- weight %>% mutate(ActivityDate = as.Date(Date, "%m/%d/%Y"))
```
I noticed that some data sets had less inputs than others so I decided to check the number of distinct users I had data for in each data set.
```{r analysis}
library(dplyr)
n_distinct(daily_activity$Id)
n_distinct(weight$Id)
n_distinct(daily_cal$Id)
n_distinct(daily_int$Id)
```
Daily activity, calories, and intensities all had 33 distinct users but the weight data set only had 8. I checked to see if there was missing data. But out of the 8 users, only 3 of them logged 5 or more times. To seek an explanation, I checked the way data was logged by checking the IsManualReport column.
```{r analysis}
weight %>%
  filter(IsManualReport == "True") %>%
  group_by(Id) %>%
  summarise("Mannual Weight Report" = n()) %>%
  distinct()

# A tibble: 5 × 2
          Id `Mannual Weight Report`
       <dbl>                   <int>
1 1503960366                       2
2 2873212765                       2
3 4319703577                       2
4 4558609924                       5
5 6962181067                      30
```
5 out of 8 users manually used the weight log function and only 2 out of the 5 reported more than twice. 
```{r analysis}
weight %>%
  filter(IsManualReport == "False") %>%
  group_by(Id) %>%
  summarise("Auto Weight Report" = n()) %>%
  distinct()

# A tibble: 3 × 2
          Id `Auto Weight Report`
       <dbl>                <int>
1 1927972279                    1
2 5577150313                    1
3 8877689391                   24
```
3 out of 8 users automatically used the weight log function and only 1 of the 3 had their weight reported more than once. If there is an automatical weight log tracker, why did only 8 of the 33 users log their weight? For both methods, users only used the mannual or automatic method. Is there a reason for that?

To have the most effective data, I decided to focus on the 2 users who logged their weight the most. Id: 6962181067 and Id: 8877689391 when I am going to use the weight data set.

#### Transform Data

Comparing Weight Progression:



