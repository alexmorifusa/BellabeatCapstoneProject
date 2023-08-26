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
##### Comparing Weight Progression:
Overall Weight Progression
```{r script}
plot(weight$ActivityDate, weight$WeightPounds, main="General Weight Change")
```
<p align="center">
  <img       src="https://github.com/alexmorifusa/BellabeatCapstoneProject/assets/137368881/ca3706c6-9197-49c7-a8c5-49acd2fe034d" width="350" and height="350">

The horrizontal correlation shows that there is little to no change in weight as the days progress. The majority of these users only inputted data once or twice so data may be inaccurate. 

Focus on Individuals with more data.
Id = 6962181067
```{r script}
ex1_weight <- weight %>%
  select(Id, ActivityDate, WeightPounds) %>%
  filter(Id==6962181067) %>%
  distinct()

plot(ex1_weigth$ActivityDate, ex1_weight$WeightPounds, main="Example 1 Weight Change")
```
<p align="center">
  <img src="https://github.com/alexmorifusa/BellabeatCapstoneProject/assets/137368881/7ba69047-1704-4e08-83cb-fef31f71f9e2" width="350" and height="350">

There seems to be little to no correlation on how the weight is changing as the days progress. For better accuracy, I looked on the other user who inputted into the weight log more than 5 times. 
Id = 8877689391
```{r script}
ex2_weight <- weight %>%
  select(Id, ActivityDate, WeightPounds) %>%
  filter(Id==8877689391) %>%
  distinct()

plot(ex2_weight$ActivityDate, ex2_weight$WeightPounds, main="Example 2 Weight Change")
```
<p align="center">
<img src="https://github.com/alexmorifusa/BellabeatCapstoneProject/assets/137368881/6a79cf4d-3d2d-4ce8-a684-1a58a928bc08" width="350" and height="350">
  
Again there is little to no correlation on how weight is changing as the days progressed. It is clear that for those who logged weight, weight loss was not their goal for using the fitibit or they were just not consistent enough with their exercise.

#### Comparing Calorie Loss and Amount of Exercise Time
I decided to create a SumActiveMin column which adds up the Lightly, Fairly, and Very Active exercise times. 
Overall Calorie Loss Progression
```{r script}
plot(merged_cal_int$SumActiveMin, merged_cal_int$Calories,
     main="General Active Min vs Calories")
```
<p align="center">
<img src="https://github.com/alexmorifusa/BellabeatCapstoneProject/assets/137368881/f0be85d1-1564-45d9-a990-aee02506f6f0" width="350" and height="350">
  
Unlike the weight change graph, the general calorie loss increases as active minutes increases. Positive Correlation can be seen by the amount of active minutes and calorie loss. To better compare with the weight loss trends, I decided to use the same 2 users.
Id = 6962181067
```{r script}
active_vs_cal <- merged_cal_int %>%
  select(Id, ActivityDay, Calories, SumActiveMin) %>%
  filter(Id==6962181067) %>%
  distinct()

plot(active_vs_cal$SumActiveMin, active_vs_cal$Calories,
     main="Active Min vs Calories")
```
<p align="center">
<img src="https://github.com/alexmorifusa/BellabeatCapstoneProject/assets/137368881/44d61317-d396-4344-8b2d-831403eb2ee3" width="350" and height="350">

Id = 8877689391
```{r script}
active_vs_cal2 <- merged_cal_int %>%
  select(Id, ActivityDay, Calories, SumActiveMin) %>%
  filter(Id==8877689391) %>%
  distinct()

plot(active_vs_cal2$SumActiveMin, active_vs_cal2$Calories,
     main="Active Min vs Calories 2")
```
<p align="center">
<img src="https://github.com/alexmorifusa/BellabeatCapstoneProject/assets/137368881/808a18d5-4f25-4d97-a760-60523023569d" width="350" and height="350">

For both users, positive correlation can be seen as active minutes increases. Users are burning calories as they stay more active, but weight is not effective meaning that there are outside factors that are probably playing into the fact that there is no weight change.

## 4) Analyze
I want to focus on two questions to guide my analysis:
#### 1) Why most users do not use the weight logging function
* Only 8 out of 33 users logged weight and the method for logging weight split between mannual and automatic. Out of those two, the mannual method had 5 users while the automatic method only had 3. No user switched between methods.
* A possible explanation is that the weight log is hard to access. Since there is an automatic weight logging method, all users should be able to use it. Possibly adding a tutorial on the Fitbit or the app that it connects to can increase the weight log usage.

#### 2) Why is there no change in weight, but there is a positive correlation between active min and calorie loss.
* Although there are only 8 user data, majority of users do not know of or decide not to track their weight.
* The Fitbit tracks active minutes and calories well as the more active a user is, the more calories they burn.
* Marketing to those who are looking to track and lose weight or collaboeating with a weight loss program can lead to a greater pool of customers.

#### Limitations When Working with Data
* Although the company directs its audience as mainly women, the data does not specify whether the given data was measured from female users.
* Little sample size may lead to inaccurate results. 

## 5) Share
Github README.md: 
## 6) Act
Recommendations based on my analysis:
* Current users are not focused on tracking weight changes, but more to measure how long they are active for. Collaborating with weight loss programs or marketing towards those looking to lose weight can lead to a larger pool of customers.
* Instead of solely targeting women, becoming appealing to all genders can also lead to a larger customer pool.
* To encourage logging weight, a tutorial can be installed into the Fitbit or the app that connects to the Fitbit to ease the process of logging weight. 









