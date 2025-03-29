# ServiceNow_Metrics : End to End Automated Data Integration to Visualization

| Synopsis      |
|-----------------|
| Objective |
| Description |
| Tools Used |
| SSIS |
| SQL | 
| Power BI |
| End Results Achieved|

## Objective
To design and implement an automated data pipeline for ServiceNow data, enabling efficient data integration, analysis, and visualization to derive actionable insights for decision-making.

## Description 
The project focuses on analyzing and visualizing ServiceNow data by developing a fully automated ETL pipeline using SSIS and SQL Server. The raw data was extracted from Excel, transformed, and loaded into SQL Server while maintaining data integrity through merge queries. Key trends and patterns were identified using SQL exploratory analysis. A Power BI dashboard was developed to provide interactive visualizations, enabling stakeholders to monitor key performance metrics and gain actionable insights from ServiceNow data.

## Tools Used 
- MS Excel – Source Data Storage
- SQL Server Integration Services (SSIS), Visual Studio – Data Extraction and Integration
- SQL Server – Data Storage and Exploratory Analysis
- Power BI – Data Visualization and Reporting

## SSIS 
### Data Extraction and Integration 
1) Connection Managers
- Created a reliable connection manager to the source and destination SQL Server database, ensuring stable data retrieval and seamless data insertion
![Image](https://github.com/user-attachments/assets/5fada15b-3afa-4a69-ab6e-fac429d79ef4)

2) Package Design
- Developed multiple SSIS packages to logically separate data extraction, transformation, and loading processes.
![Image](https://github.com/user-attachments/assets/fb9904e8-0dec-4eb5-ae58-428446d9c8f1)

3) Data Flow and Control Flow Pipeline:
- Performed data transformations using SSIS transformation components.
- Loaded transformed data into the destination SQL Server database using optimized control flow tasks.
- Added a TRUNCATE TABLE SQL task at the beginning of the flow to ensure the staging table is emptied before each data load, preventing the accumulation of large datasets and maintaining data integrity.
![Image](https://github.com/user-attachments/assets/c3779749-c0e9-4a83-a6ad-de3da6ee5553)

4) Merge Script to Merge Staging Table and Main Table:
- Created a staging table to temporarily hold data extracted from Excel.
- Designed the staging table schema to match the structure of the main table.
- Developed a SQL merge script to efficiently update or insert data from the staging table into the main table and to prevent duplicate data from being inserted
![Image](https://github.com/user-attachments/assets/e6a2464b-c299-403d-bda7-1b89cf6aaa1d)
![Image](https://github.com/user-attachments/assets/7b942711-086d-40de-9193-2f10dbab75cb)

5) Package Execution Status:
- The SSIS package completed execution successfully, resulting in the successful transfer of data from the source to the destination as verified by the execution logs and data validation checks
![Image](https://github.com/user-attachments/assets/69bc5326-e4ca-49be-8ce5-14cdd8f9f2fc)
![Image](https://github.com/user-attachments/assets/fbcaf565-6252-42b7-a6d0-96da1ac285ec)

## SQL
### Exploratory Data Analysis
### Queries 
Tables : 1) serviceNow_task, 2) serviceNow_sla

1) No of Rows 

SELECT COUNT(*) AS total_rows FROM servicenow_task;<br>
SELECT COUNT(*) AS total_rows FROM servicenow_sla;<br>

2) Data types of columns in both tables

SELECT COLUMN_NAME, DATA_TYPE<br>
FROM INFORMATION_SCHEMA.COLUMNS<br>
WHERE TABLE_NAME = 'servicenow_task';<br>

SELECT COLUMN_NAME, DATA_TYPE<br>
FROM INFORMATION_SCHEMA.COLUMNS<br>
WHERE TABLE_NAME = 'servicenow_sla';<br>

3) Identification of Null values (Missing Values)

SELECT<br>
    COUNT(*) - COUNT(sys_id) AS missing_sys_id,<br>
    COUNT(*) - COUNT(sys_class_name) AS missing_class,<br>
    COUNT(*) - COUNT(sys_created_by) AS missing_created_by,<br>
    COUNT(*) - COUNT(sys_created_on) AS missing_created_on,<br>
    COUNT(*) - COUNT(priority_details) AS missing_priority,<br>
    COUNT(*) - COUNT(status_details) AS missing_status,<br>
    COUNT(*) - COUNT(business_duration) AS missing_duration<br>
FROM servicenow_task;<br>

SELECT<br> 
    COUNT(*) - COUNT(sys_id) AS missing_sys_id,<br>
    COUNT(*) - COUNT(sla_id) AS missing_sla_id,<br>
    COUNT(*) - COUNT(Target) AS missing_target,<br>
    COUNT(*) - COUNT(has_breached) AS missing_has_breached<br>
FROM servicenow_sla;<br>

4) Identification of Duplicate Values

SELECT sys_id, COUNT(*)<br> 
FROM servicenow_task<br>
GROUP BY sys_id<br>
HAVING COUNT(*) > 1;<br>

SELECT sys_id, COUNT(*) <br>
FROM servicenow_sla<br>
GROUP BY sys_id<br>
HAVING COUNT(*) > 1;<br>

5) Distribution : Task by Priority

SELECT priority_details, COUNT(*) AS count<br>
FROM servicenow_task<br>
GROUP BY priority_details<br>
ORDER BY count DESC;<br>

6) Distribution : Task by Status

SELECT status_details, COUNT(*) AS count<br>
FROM servicenow_task<br>
GROUP BY status_details<br>
ORDER BY count DESC;<br>

7) Distribution : SLA by Target and Breach Status

SELECT target, has_breached, COUNT(*) AS count<br>
FROM servicenow_sla<br>
GROUP BY target, has_breached<br>
ORDER BY count DESC;<br>

8) Distribution : Tasks by Class Name and percentage

SELECT <br>
    sys_class_name,<br> 
    COUNT(*) AS task_count,<br>
    (COUNT(*) * 100.0) / (SELECT COUNT(*) FROM servicenow_task) AS percentage<br>
FROM servicenow_task<br>
GROUP BY sys_class_name<br>
ORDER BY task_count DESC;<br>

9) Ticket Creation Trend : Daily

SELECT sys_created_on, COUNT(*) AS ticket_count<br>
FROM  servicenow_task<br>
GROUP BY sys_created_on<br>
ORDER BY sys_created_on;<br>

10) Top 10 Employees by Tickets Raised

select Top 10(sys_created_by),count(*) AS tickets_raised<br>
FROM Servicenow_task<br>
Group by sys_created_by<br>
order by tickets_raised DESC<br>

11) SLA Records Without Corresponding Task Information

SELECT COUNT(s.sys_id) AS missing_tasks<br>
FROM servicenow_sla s<br>
LEFT JOIN servicenow_task t ON s.sys_id = t.sys_id<br>
WHERE t.sys_id IS NULL;<br>

### Findings 
- The servicenow_task table contains 5016 rows, representing the total number of tasks recorded. The servicenow_sla table contains 5500 rows, indicating the total number of SLAs.
- Provides a baseline understanding of the dataset's size for both tasks and SLAs.
- The data types of columns in servicenow_task include text,date and numerical . The data types of columns in servicenow_sla include texts.
- Essential for data validation, transformation, and ensuring compatibility during analysis.
- In the servicenow_task table and the servicenow_sla table no missing values are found.
- Highlights potential data quality issues that need to be addressed before analysis.
- The primary keys (sys_id) for both the tables contain no duplicate values
- The distribution of tasks by priority shows that majority of tasks are priotorized as "Moderate"
- The distribution of tasks by status shows that majority of tasks are "Closed"
- The distribution of SLAs by target and breach status shows that 
- The daily ticket creation trend shows , highlighting the daily patterns in ticket creation and potential peak periods.
- The distribution of tasks by class name and percentage shows that 50% of tasks are categorized under "Incidents", Highlighting the most common types of tasks and their relative proportions.
- The top 10 employees (performers) by tickets raised are identified
- There are 484 SLA records that do not have corresponding task information which highlights potential data inconsistencies between the SLA and task tables.

## Power BI

