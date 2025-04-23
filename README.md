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
- The distribution of SLAs by target and breach status shows that majority of SLA's are beyond the SLA stating the organisation did not meet the agreed resolution and response timings.
- The daily ticket creation trend shows , highlighting the daily patterns in ticket creation and potential peak periods.
- The distribution of tasks by class name and percentage shows that 50% of tasks are categorized under "Incidents", Highlighting the most common types of tasks and their relative proportions.
- The top 10 employees (performers) by tickets raised are identified
- There are 484 SLA records that do not have corresponding task information which highlights potential data inconsistencies between the SLA and task tables.

## Power BI
### Data Visualization & Report Building

Table View :
- Created a Date table and Duplicate Date table using DAX
- Created columns like Month (Dynamically represents the current month) and Year
- Calculated Columns to create criterias for the tickets raised like the ticket aging days and time taken to close hours

Model View :
| Table 1        | Table 2        | Relationship Type | Key Field |
|---------------|---------------|------------------|-----------|
| servicenow_task | servicenow_sla | One-to-One       | sys_id    |

Report View :
The following visuals have been used to represent data effectively in Power BI:
- Card Visual – To Display key metrics such as count of tickets along with status details and SLA count with responses and resolutions
- Gauge Chart – To measure progress against the target user performance to the performance achieved.
- Slicers – To enable filtering of data by Month, sys_id, user_name interactively for better user experience.
- Doughnut Chart – To show the percentage distribution of Priority Status
- Table Visual – To summarize the user information with detailed insights of tickets raised and other SLA related details
- Stacked Column Chart – To Compare tickets with different status categories.

1) Summary Page

![Image](https://github.com/user-attachments/assets/8bae3386-f2c3-41be-922a-1c74ad35d382)

- Title of the report, Logo and the Last updated date which denotes the latest refresh date of the report
- Buttons to help page navigate the user to SLA and Userwise detailed tabs
- Slicers to filter the data by Sys_id, Month and User name
- Card visuals that provides information on total tickets raised along with individual count of tickets by their class name
- Card visuals to indicate the SLA responses and resolutions that is within or beyond the SLA
- Doughnut chart is used to show the distribution of tickets by priority ( Moderate, High, Low, Critical)
- Stacked Column chart to compare the tickets with its status categories ( Open, closed, ...)
- Visual to indicate the aging of the open incidents and categorized into days
- Visual to indicate the business duration ( Time taken for a ticket to be closed ) and categorized as hours
- Line chart to indicate the 12 Month Trend for tickets raised

2) SLA Page

![Image](https://github.com/user-attachments/assets/db7dca44-6341-4e68-bd2f-8657fb2661c7)
- Slicers to filter the data by Sys_id, Month and User name
- Back Button to help the user nagivate to previous page (Summary Page)
- Card visuals to indicate the SLA responses and resolutions that is within or beyond the SLA, (%) of SLA within or beyond SLA is also calculated
- Stacked Column chart to distribute the Responses and Resolution SLA's with its Priority Status ( Moderate, High, Low, Critical)
- Line chart to indicate the 12 Month Trend for the % of Responses and Resolutions

3) Userwise Page

![Image](https://github.com/user-attachments/assets/4d1edd2d-bc5c-4722-9ebd-8659cb616e25)
- Slicers to filter the data by Sys_id, Month and User name
- Back Button to help the user nagivate to previous page (Summary Page)
- Card visual that shows the unique users in the system
- Gauge visual to indicate the user performance of users where target is 100% and the
- Card visuals that indicate the different status of tickets like (Open, Closed, Resolved, ...) along with the Total Tickets and User Performance %
- Table that summarizes all the users with the tickets opened and closed by them denoting their performance 

## End Results Achieved 

1) Automated Data Pipeline
- Implemented an end-to-end ETL workflow using SSIS toload ServiceNow data into SQL Server, reducing manual intervention, ensuring timely and efficient data processing.
- Used SQL Merge queries to maintain data consistency, avoiding duplication and ensuring data accuracy.

2) Comprehensive Data Analysis
- Leveraged SQL queries and Power BI DAX measures to identify trends, patterns, and anomalies in ServiceNow data.

3) Interactive Reporting & Real-Time Insights
- Designed dynamic Power BI report with interactive visuals (Cards, Gauges, Slicers, Doughnut Charts, Tables, and Stacked Column Charts).
- Enabled real-time data refresh by integrating SQL Server with Power BI via Import mode.
- Provided user-level security (RLS) for role-based access control.

4) Improved Decision-Making
- Empowered ITSM (Service Management) stakeholders with data-driven insights to optimize their processes
- Enhanced operational efficiency through identifying key parametes like the time taken to close a ticket and the aging of the open tickets
- Helped the team to identify the top performers.


