# title: "Google Capstone - Cyclistic bike share"
# author: "Khaled Faisal"
# date: '2022-07-16'


## Project Summary
This is capstone for Google data analytics certificate.
Data related to the project has been made available by Motivate International Inc. under this [license](https://ride.divvybikes.com/data-license-agreement).
Project ultimate goal is to design marketing strategies aimed at converting casual riders into annual members, so business question assigned to me to answer is : How do annual members and casual riders use Cyclistic bikes differently?
# Project's data Summary
Data available in separate monthly ZIP format files to download and after extracting the files we get CSV format files, and the summary is as shown below:
---
Table Name	No. of Columns 	No. of Rows
rides_202106	13	729,595
rides_202107	13	822,410
rides_202108	13	804,352
rides_202109	13	756,147
rides_202110	13	631,226
rides_202111	13	359,978
rides_202112	13	247,540
rides_202201	13	103,770
rides_202202	13	115,609
rides_202203	13	284,042
rides_202204	13	371,249
rides_202205	13	634,858
---
---
Total Number of Rows: 5,860,776
---

# Data Eploration
When opening the data using Microsoft Excel, I noticed that the program is spending much time to open the file so I decided to start the project using Google BigQuery.
First step after uploading the data to the data set is to confirm if the tables contains the same number of columns in the same order with the same schema which is confirmed  and all tables contain the same structure as per the below screenshot for one of the tables schema

<img src="01-table_schema.png" alt="table_schema"/>

Then I decided to combine all tables in one as per the below code
```{sql connection=}
CREATE TABLE rides_details.combined_data AS (SELECT * 
FROM rides_details.rides_202106 
UNION ALL
SELECT * 
FROM rides_details.rides_202107 
UNION ALL
SELECT * 
FROM rides_details.rides_202108 
UNION ALL
SELECT * 
FROM rides_details.rides_202109
UNION ALL
SELECT * 
FROM rides_details.rides_202110 
UNION ALL
SELECT * 
FROM rides_details.rides_202111 
UNION ALL
SELECT * 
FROM rides_details.rides_202112 
UNION ALL
SELECT * 
FROM rides_details.rides_202201 
UNION ALL
SELECT * 
FROM rides_details.rides_202202 
UNION ALL
SELECT * 
FROM rides_details.rides_202203 
UNION ALL
SELECT * 
FROM rides_details.rides_202204 
UNION ALL
SELECT * 
FROM rides_details.rides_202205)

```

