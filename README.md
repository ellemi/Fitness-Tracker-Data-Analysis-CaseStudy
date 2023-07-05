# Case Study: Fitness Tracker Data Analysis 

## Introduction
This case study was my capstone project for the **Google Data Analytics Professional Certificate**.

High-tech company, Bellabeat, manufactures health tracking smart devices for women, including Leaf, a tracker that can be worn a a bracelet, necklace or clip. The company's tracking app provides users with data related to sleep, activity, menstrual cycle, stress and mindfullness.

Co-founders, Urška Sršen and Sando Mur started this successful company in 2013 and want to grow their company into new areas. 

#### Business Task
Explore available smart device usage data to see how customers are currently using their smart devices and make suggestions to inform Bellabeat marketing strategy.

The following uses Google's framework for making good decisions, the six phases of data analysis: ask, prepare, process, analyze, share, act.


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
- The data contained is from only 30 users over 30 days
- Data originated from a third-party and was collected nearly 10 years ago
- No information is given about the gender, location or age of the users
- There is no indication that the users represented in the data are typical of the company's customers
- Some files include activity tracked by the hour or minute, but does not include information about the user's timezone
- The number of users is inconsistent across the files; Only seven users are included in the weightLogInfo_merged file, for instance.

This sample size is too small and too old to return a complete analysis. One month of activity data recorded nearly a decade ago from only 30 users is not sufficient for a meaningfuly analysis in such a rapidly-changing field. 

Additionally, without any information about the gender or location of the users, it is difficult to determine how similar these users are to thoes of the company's customers. 

Therefore, these data are considered low-quality and their use is not recommended for high-level business analysis.


## Process
I chose to use two spreadsheets: dailyActivity and sleepDay. Much of the data from the other files are contained in dailyActivity. 

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

#

File: sleepDay_merged

```
-- look at the number of rows
SELECT COUNT (*)
FROM sleepDay_merged;

-- check for the number of users
SELECT COUNT (DISTINCT Id) AS total_users
FROM sleepDay_merged;

-- check for duplicates
SELECT Id, SleepDay
FROM sleepDay_merged
GROUP BY Id, SleepDay
HAVING COUNT(*) > 1;

-- check for rows without user ID
SELECT COUNT (Id)
FROM sleepDay_merged
WHERE Id IS NULL;

-- number of sleep days recorded per user
SELECT Id, COUNT(SleepDay) as sleep_days, 
FROM sleepDay_merged
GROUP BY Id
ORDER BY sleep_days ASC;

-- separate out date & time columns and extract day of week
SELECT Id, 
CAST (SleepDay AS DATE) AS date, 
FORMAT_TIMESTAMP ('%A',SleepDay) AS day_of_week,
CAST (SleepDay AS TIME) AS time
FROM sleepDay_merged;

-- count days of week of user activity
SELECT Id, 
FORMAT_DATE('%A', DATE(sleepDay)) AS day_of_week_name, 
COUNT(*) AS count_of_days_of_week
FROM sleepDay_merged
GROUP BY Id, day_of_week_name;

-- total sleep minutes per user
SELECT DISTINCT Id, 
SUM(TotalMinutesAsleep) as minutes_asleep
FROM sleepDay_merged;

-- number of days of sleep tracked by each user as well as minimum, maximum and average time sleeping and average time in bed
SELECT Id,
COUNT (Id) AS num_days_sleep_tracked,
ROUND (AVG (TotalMinutesAsleep) /60, 2) AS  avg_hours_asleep,
ROUND (MIN (TotalMinutesAsleep) /60.0,2) AS min_hours_asleep,
ROUND (MAX (TotalMinutesAsleep) /60,2) AS max_hours_asleep,
ROUND (AVG (TotalTimeInBed) / 60,2) AS avg_hours_inBed
FROM sleepDay_merged
GROUP BY Id;

-- percentage of time in bed as sleeping
SELECT DISTINCT Id, 
CAST (SleepDay AS DATE) AS date,
ROUND (TotalMinutesAsleep/TotalTimeInBed * 100) AS percent_time_sleeping,
ROUND ((TotalTimeInBed-TotalMinutesAsleep)/TotalTimeInBed * 100) AS percent_time_awake
FROM sleepDay_merged
ORDER BY percent_time_awake DESC;


--time and percentage of time asleep vs. awake in bed 
SELECT DISTINCT Id, 
CAST (SleepDay AS DATE) AS date,
SUM (TotalMinutesAsleep) as minutes_asleep,
ROUND (TotalMinutesAsleep/TotalTimeInBed * 100) AS percent_time_sleeping,
SUM (TotalTimeInBed-TotalMinutesAsleep) AS time_awake,
ROUND ((TotalTimeInBed - TotalMinutesAsleep)/TotalTimeInBed * 100) AS percent_time_awake
FROM sleepDay_merged
GROUP BY Id, SleepDay, TotalMinutesAsleep, TotalTimeInBed
ORDER BY Id;

--average Sleep Time per user
SELECT Id, 
ROUND (AVG(TotalMinutesAsleep)/60,0) as avg_sleep_time_hour,
ROUND (AVG(TotalTimeInBed)/60,0) as avg_time_bed_hour,
ROUND (AVG(TotalTimeInBed - TotalMinutesAsleep)) as avg_hours_wasted_in_bed
FROM sleepDay_merged
GROUP BY Id;

-- to join both tables
SELECT Id, ActivityDate, 
FORMAT_TIMESTAMP ('%A',SleepDay) AS DayOfWeek,
TotalSteps, VeryActiveMinutes, FairlyActiveMinutes, LightlyActiveMinutes, SedentaryMinutes, Calories,
TotalMinutesAsleep, TotalTimeInBed
FROM `my-project-number-1-367520.bellabeat_analysis.dailyActivity_merged`
LEFT JOIN `my-project-number-1-367520.bellabeat_analysis.sleepDay_merged` using(Id);
```

#### Insights:
