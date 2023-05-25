# Case Study: Fitness Tracker Data Analysis 

## Introduction
This case study was my capstone project for the **Google Data Analytics Professional Certificate**.

High-tech company, Bellabeat, manufactures health tracking smart devices for women, including Leaf, a tracker that can be worn a a bracelet, necklace or clip. The company's tracking app provides users with data related to sleep, activity, menstrual cycle, stress and mindfullness.

Co-founders, Urška Sršen and Sando Mur started this successful company in 2013 and want to grow their company into new areas. 

The following uses Google's framework for making good decisions, the six phases of data analysis: ask, prepare, process, analyze, share, act.


#### Business Tasks
1. Explore smart device usage data to see how customers are currently using their smart devices.
2. Recommend applications for Bellabeat customers and make suggestions to inform Bellabeat marketing strategy.
#
### Ask
The anacronym, **SMART**, is a reminder to ask questions that are: Specific, Measurable, Action-oriented, Relevant, and Time-bound.

 1. What are some trends in smart device usage?
 2. How could these trends apply to Bellabeat customers?
 3. How could these trends help influence Bellabeat marketing strategy?

### Prepare
Dataset used: [Fitbit Tracking Data](https://www.kaggle.com/datasets/arashnic/fitbit), made available in the Public Domain on Kaggle, contains personal tracking data collected between 03.12.2016 - 05.12.2016 from 30 Fitbit users who responded to a survey distributed via Amazon Mechanical Turk. (MTurk is a crowdsourcing marketplace where one can connect with an on-demand human workforce)

I downloaded the 18 .csv files to a folder on my harddrive and opened each using Excel for initial review of the file structures. 

Using good data will help ensure quality analysis. The anacronym **ROCCC** (Reliable, Original, Comprehensive, Current, Cited)helps when determining good data. 

Concerns:
- Contains data from only 30 users over 30 days
- No information is given about users' gender
- There's no indication that the users represented in the data are typical or that the data were vetted in any way
- Data originated from a third-party and was collected nearly 10 years ago
- While the set contains data from only 30 users, the parameters captured are typical of fitness trackers

I chose to use two spreadsheets: dailyActivity and sleepDay. Much of the data from the other files are contained in dailyActivity. Some of the other files include activity trackd by the hour or minute, but does not include information about the user's timezone. 

Therefore, these data are considered low-quality and their use is not recommended for high-level business analysis.


### Process
I used SQL in Big Query to review, organize and clean the data. I first created a dataset and then imported the two .csv files,  dailyActivity_merged and sleepDay_merged. 

```
--to look at the number of rows in dailyActivity
SELECT COUNT (*)
FROM dailyActivity_merged;

--to check for the number of users in dailyActivity
SELECT COUNT (DISTINCT Id) AS total_users
FROM dailyActivity_merged;

--to check for duplicates
SELECT Id, ActivityDate, TotalSteps, Count(*)
FROM dailyActivity_merged
GROUP BY id, ActivityDate, TotalSteps
HAVING Count(*) > 1;

-- to check for rows without user ID
SELECT COUNT (Id)
FROM dailyActivity_merged
WHERE Id IS NULL;

-- to look number of active days per user
SELECT Id, COUNT(ActivityDate) as active_days, ROUND(COUNT(ActivityDate) * 100 / COUNT(COUNT(ActivityDate)) OVER ()) AS percentage
FROM dailyActivity_merged
GROUP BY Id
ORDER BY active_days DESC;


```
