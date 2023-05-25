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
Ask SMART questions (Specific, Measurable, Action-oriented, Relevant, and Time-bound) 

1. What are some trends in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat marketing strategy?

### Prepare
Dataset used: [Fitbit Tracking Data](https://www.kaggle.com/datasets/arashnic/fitbit), made available in the Public Domain on Kaggle, contains personal tracking data collected between 03.12.2016-05.12.2016 from 30 Fitbit users who responded to a survey distributed via Amazon Mechanical Turk. (MTurk is a crowdsourcing marketplace where one can connect with an on-demand human workforce)

I downloaded the 18 .csv files to a folder on my harddrive and opened each using Excel for initial review of the file structures. 

Using good data will help ensure quality analysis. The anacronym **ROCCC** helps when determining good data.

  **R**eliable: low
Contains data from only 30 users over 30 days. No information is given about users' gender.

  **O**riginal: low
Data originated from a third-party.

  **C**omprehensive: medium
While the set contains data from only 30 users, the parameters captured are typical of fitness trackers. 

  **C**urrent: low
Data was collected nearly 10 years ago.

  **C**ited: low
There's no indication that the users represented in the data are typical or that the data were vetted in any way.

I chose to use two spreadsheets: dailyActivity and sleepDay. These data are considered low-quality and their use is not recommended for high-level business analysis.


### Process
Tools: Excel, SQL in Big Query

I used Excel to review data and check formatting. I found that several of the files included a TIMESTAMP field including both date and time. I split into 2 columns: Date and Hour and selected the data types Date and Time respectively in each spreadsheet. 

I also formatted the columns with numbers to show only 2 numbers after the decimal (#.##) and save the files as .xls

I used SQL to review, organize and clean the data. I first created a dataset and then imported four .csv files including dailyActivity_merged, hourlyIntensities_merged, hourlySteps_merged, sleepDay_merged. 
