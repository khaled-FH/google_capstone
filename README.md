# title: "Google Capstone - Cyclistic bike share"
# author: "Khaled Faisal"
# date: '2022-07-16'


## Project Summary
This is capstone for Google data analytics certificate.
Data related to the project has been made available by Motivate International Inc. under this [license](https://ride.divvybikes.com/data-license-agreement).
Project ultimate goal is to design marketing strategies aimed at converting casual riders into annual members, so business question assigned to me to answer is : How do annual members and casual riders use Cyclistic bikes differently?
# Project's data Summary
Data available in separate monthly ZIP format files to download and after extracting the files we get CSV format files, and the summary is as shown below:
<img src="00-data_summary.png" alt="data_summary"/>
# Data Eploration
When opening the data using Microsoft Excel, I noticed that the program is spending much time to open the file so I decided to start the project using Google BigQuery.
First step after uploading the data to the data set is to confirm if the tables contains the same number of columns in the same order with the same schema which is confirmed  and all tables contain the same structure as per the below screenshot for one of the tables schema

<img src="01-table_schema.png" alt="table_schema"/>

Then I decided to combine all tables in one as per the below code
```sql
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
The query generated the table and I confirmed that the number of rows in this table equal to the sum of all rows mentioned above in the data summary section and as per below image:
<img src="02-combined_table.png" alt="combined_table"/>
# Cleaning Process
1- Check duplicates “ride_id” the primary key
```sql
SELECT 
 DISTINCT(ride_id)
FROM `cyclistic-bike-share-0001.rides_details.combined_data`

```
Total Rows are 5,860,776 matching table total rows, so no duplicates in the primary key

2- Check NULLS in “started_at” and “ended_at”
```sql
SELECT 
 started_at
FROM `cyclistic-bike-share-0001.rides_details.combined_data`
WHERE started_at IS NULL
```  
 No Null found in “started_at”
```sql
SELECT 
 ended_at
FROM `cyclistic-bike-share-0001.rides_details.combined_data`
WHERE ended_at IS NULL 
``` 
 No Null found in “ended_at”
 
3- Check Nulls “start_station_name” and “start_station_id”
```sql
SELECT 
 ride_id,
 start_station_name,
 start_station_id
FROM `cyclistic-bike-share-0001.rides_details.combined data` 
WHERE start_station_name IS NULL
```
 Return 823167 Rows
```sql
SELECT 
 ride_id,
 start_station_name,
 start_station_id
FROM `cyclistic-bike-share-0001.rides_details.combined_data` 
WHERE start_station_id IS NULL
```
 Return 823164 Rows
 The difference shows that there are 3 rows has start_station_id but does not have start_station_name
 
4- To Know what is the difference I used the below query
```sql
SELECT 
 ride_id,
 start_station_name,
 start_station_id
FROM `cyclistic-bike-share-0001.rides_details.combined_data` 
WHERE start_station_name IS NULL
  AND start_station_id IS NOT NULL
```
 Which returned three Station “ID WL-008”, “20215”, and “13221”
 
5- Check the Missing Start Stations Names
```sql
SELECT  
  DISTINCT(start_station_name),
  start_station_id
FROM `cyclistic-bike-share-0001.rides_details.combined_data` 
WHERE 
  start_station_id = "WL-008"
  OR start_station_id = "20215"  
  OR start_station_id = "13221"
```
 Which returned the following:
• “WL-008” = “Clinton St & Roosevelt Rd”
• “20215” = “Wood St & Milwaukee Ave”
• “13221” = “Hegewisch Metra Station”
• The Three Null Station Names

6- Update the Missing Start Stations Names
```sql
UPDATE `cyclistic-bike-share-0001.rides_details.combined_data`
SET start_station_name = CASE start_station_id
  WHEN "WL-008" THEN "Clinton St & Roosevelt Rd"
  WHEN "20215" THEN "Wood St & Milwaukee Ave"
  WHEN "13221" THEN "Hegewisch Metra Station"
  END
WHERE start_station_id IN ("WL-008", "20215", "13221")
```

7- Check if the missing station names already updated
```sql
SELECT  
  DISTINCT(start_station_name),
  start_station_id
FROM `cyclistic-bike-share-0001.rides_details.combined_data` 
WHERE 
  start_station_id = "WL-008"
  OR start_station_id = "20215"  
  OR start_station_id = "13221"
```
 Updated Successfully.
 
8- Check Nulls in “end_station_name” and “end_station_id”
```sql
SELECT 
 ride_id,
 end_station_name,
 end_station_id
FROM `cyclistic-bike-share-0001.rides_details.combined_data`
WHERE end_station_name IS NULL
```
 Returns 878338 Rows
```sql
SELECT 
 ride_id,
 end_station_name,
 end_station_id
FROM `cyclistic-bike-share-0001.rides_details.combined_data`
WHERE end_station_id IS NULL
```
 Returns 878338 Rows
```sql
SELECT 
 ride_id,
 end_station_name,
 end_station_id
FROM `cyclistic-bike-share-0001.rides_details.combined_data`
WHERE end_station_id IS NULL
AND end_station_id IS NULL
```
 Returns 878338 Rows
```sql
SELECT 
 ride_id,
 end_station_name,
 end_station_id
FROM `cyclistic-bike-share-0001.rides_details.combined_data`
WHERE end_station_id IS NULL
OR end_station_id IS NULL
```
 Returns 878338 Rows
 No Row Differences, so “end_station_name” nor “end_station_id” can be updated

9-Check NULLS in “member_casual”
```sql
SELECT 
 member_casual
FROM `cyclistic-bike-share-0001.rides_details.combined_data`
WHERE member_casual IS NULL 
```sql
  No Null found in “member_casual”. 

