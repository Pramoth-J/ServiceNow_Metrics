# ServiceNow_Metrics : End to end data pipeline and visualization

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
