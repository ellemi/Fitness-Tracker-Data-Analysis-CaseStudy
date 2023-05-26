# Case Study: Fitness Tracker Data Analysis 

## Introduction
This case study was my capstone project for the **Google Data Analytics Professional Certificate**.

High-tech company, Bellabeat, manufactures health tracking smart devices for women, including Leaf, a tracker that can be worn a a bracelet, necklace or clip. The company's tracking app provides users with data related to sleep, activity, menstrual cycle, stress and mindfullness.

Co-founders, Urška Sršen and Sando Mur started this successful company in 2013 and want to grow their company into new areas. 

The following uses Google's framework for making good decisions, the six phases of data analysis: ask, prepare, process, analyze, share, act.


#### Business Tasks
1. Explore smart device usage data to see how customers are currently using their smart devices.
2. Recommend applications for Bellabeat customers and make suggestions to inform Bellabeat marketing strategy.


## Ask
The anacronym, **SMART**, is a reminder to ask questions that are: Specific, Measurable, Action-oriented, Relevant, and Time-bound.

 1. What are some trends in smart device usage?
 2. How could these trends apply to Bellabeat customers?
 3. How could these trends help influence Bellabeat marketing strategy?

## Prepare
Dataset used: [Fitbit Tracking Data](https://www.kaggle.com/datasets/arashnic/fitbit), made available in the Public Domain on Kaggle, contains personal tracking data collected between 03.12.2016 - 05.12.2016 from 30 Fitbit users who responded to a survey distributed via Amazon Mechanical Turk. (MTurk is a crowdsourcing marketplace where one can connect with an on-demand human workforce)

I downloaded the 18 .csv files to a folder on my harddrive and opened each using Excel for initial review of the file structures. 

Using good data will help ensure quality analysis. The anacronym **ROCCC** (Reliable, Original, Comprehensive, Current, Cited)helps when determining good data. 

#### Insights:
- Contains data from only 30 users over 30 days
- No information is given about users' gender and Bellabeats is positioned as a company for women
- There is no indication that the users represented in the data are typical or that the data were vetted in any way
- Data originated from a third-party and was collected nearly 10 years ago
- While the set contains data from only 30 users, the parameters captured are typical of fitness trackers

The dataset is considered low-quality not recommended to use it for high-level analysis. But general takeaways should be possible.


## Process
I chose to use two spreadsheets: dailyActivity and sleepDay. Much of the data from the other files are contained in dailyActivity. Some of the other files include activity trackd by the hour or minute, but does not include information about the user's timezone. Some files include info from only a few users. Only sever users are included in the weightLogInfo_merged file, for instance.

I used SQL in Big Query to review, organize and clean the data. I first created a dataset and then imported the two .csv files,  dailyActivity_merged and sleepDay_merged. 

File:  dailyActivity_merged
```
--to look at the number of rows
SELECT COUNT (*)
FROM dailyActivity_merged;

--to check for the number of users
SELECT COUNT (DISTINCT Id) AS Total_Users
FROM dailyActivity_merged;

--to check for duplicates
SELECT Id, ActivityDate, TotalSteps, COUNT(*)
FROM dailyActivity_merged
GROUP BY Id, ActivityDate, TotalSteps
HAVING COUNT(*) > 1

-- to check for rows without user ID or date
SELECT COUNT (Id)
FROM dailyActivity_merged
WHERE ID IS NULL OR ActivityDate IS NULL;

-- to look at number and percentage of active days per user
SELECT Id, COUNT(ActivityDate) as Active_Days, 
ROUND(COUNT(ActivityDate) * 100 / COUNT(COUNT(ActivityDate)) OVER ()) AS Percentage_Active_days
FROM dailyActivity_merged
GROUP BY Id
ORDER BY active_days DESC;

--to calculate total activity minutes vs sedentary minutes per user
SELECT DISTINCT Id, 
SUM(VeryActiveMinutes) as Mins_Very_Active,
SUM(FairlyActiveMinutes) as Mins_Fairly_Active, 
SUM(LightlyActiveMinutes) as Mins_Lightly_Active,
SUM((VeryActiveMinutes+FairlyActiveMinutes+LightlyActiveMinutes)) as Total_Minutes_Active,
SUM(SedentaryMinutes) as Mins_Sedentary,
FROM dailyActivity_merged
Group by Id;

--to calcualte average activity per user
SELECT Id,
ROUND (AVG (VeryActiveMinutes), 0) AS Average_Mins_Very_Active,
ROUND (AVG (FairlyActiveMinutes), 0) AS Average_Mins_Fairly_Active, 
ROUND (AVG (LightlyActiveMinutes), 0) AS Average_Mins_Lightly_Active, 
ROUND (AVG (SedentaryMinutes), 0) AS Average_Mins_Sedentary,
FROM dailyActivity_merged
GROUP BY Id
ORDER BY Average_Mins_Very_Active DESC;

```

#### Insights:




#

File: sleepDay_merged

```
--to look at the number of rows
SELECT COUNT (*)
FROM `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`;

--to check for the number of users
SELECT COUNT (DISTINCT Id) AS total_users
FROM `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`;

--to check for duplicates
SELECT Id, SleepDay
FROM `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`
GROUP BY Id, SleepDay
HAVING COUNT(*) > 1

-- to check for rows without user ID
SELECT COUNT (Id)
FROM `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`
WHERE Id IS NULL;

-- to look at number of sleep days recorded per user
SELECT Id, COUNT(SleepDay) as sleep_days, 
FROM `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`
GROUP BY Id
ORDER BY sleep_days ASC;

-- to separate out date & time columns and extract day of week
SELECT Id, 
CAST (SleepDay AS DATE) AS date, 
FORMAT_TIMESTAMP ('%A',SleepDay) AS day_of_week,
CAST (SleepDay AS TIME) AS time
FROM `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`;

--to calculate total sleep minutes per user
SELECT DISTINCT Id, 
SUM(TotalMinutesAsleep) as minutes_asleep
FROM `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`

-- to calculate number of days of sleep tracked by each user as well as minimum, maximum and average time sleeping and average time in bed
SELECT Id,
COUNT (Id) AS num_days_sleep_tracked,
ROUND (AVG (TotalMinutesAsleep) /60, 2) AS  avg_hours_asleep,
ROUND (MIN (TotalMinutesAsleep) /60.0,2) AS min_hours_asleep,
ROUND (MAX (TotalMinutesAsleep) /60,2) AS max_hours_asleep,
ROUND (AVG (TotalTimeInBed) / 60,2) AS avg_hours_inBed
from `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`
GROUP BY Id;

-- calculate percentage of time in bed as sleeping
SELECT DISTINCT Id, 
CAST (SleepDay AS DATE) AS date,
ROUND (TotalMinutesAsleep/TotalTimeInBed * 100) AS percent_time_sleeping,
ROUND ((TotalTimeInBed-TotalMinutesAsleep)/TotalTimeInBed * 100) AS percent_time_awake
FROM `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`
ORDER BY percent_time_awake DESC;


--to calculate time and percentage of time asleep vs. awake in bed 
SELECT DISTINCT Id, 
CAST (SleepDay AS DATE) AS date,
SUM (TotalMinutesAsleep) as minutes_asleep,
ROUND (TotalMinutesAsleep/TotalTimeInBed * 100) AS percent_time_sleeping,
SUM (TotalTimeInBed-TotalMinutesAsleep) AS time_awake,
ROUND ((TotalTimeInBed - TotalMinutesAsleep)/TotalTimeInBed * 100) AS percent_time_awake
FROM `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`
GROUP BY Id, SleepDay, TotalMinutesAsleep, TotalTimeInBed
ORDER BY Id;

--Average Sleep Time per user
SELECT Id, 
ROUND (AVG(TotalMinutesAsleep)/60,0) as avg_sleep_time_hour,
ROUND (AVG(TotalTimeInBed)/60,0) as avg_time_bed_hour,
ROUND (AVG(TotalTimeInBed - TotalMinutesAsleep)) as avg_hours_wasted_in_bed
FROM `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged`
GROUP BY Id;
```


