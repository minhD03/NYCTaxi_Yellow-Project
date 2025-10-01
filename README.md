# NYC Taxi Yellow Project
## 1. Introduction:
This project helps tracking the Taxi System through Power BI dashboard. The data will be updated regularly using my designed pipeline in Microsoft Fabric. 

## 2. Preview Images: 
These are the screenshots of my dashboard. My full working steps and report can be viewed here: [Report Link](https://github.com/minhD03/NYCTaxi_Yellow-Project/blob/cf2e4579d42d79dbeb3748a0bd6376449f71965e/NYCTaxi%20-%20Nhat%20Minh%20Dang.pdf)

![alt text](https://github.com/minhD03/NYCTaxi_Yellow-Project/blob/cf2e4579d42d79dbeb3748a0bd6376449f71965e/Images/Dashboard%201.png)
![alt text](https://github.com/minhD03/Payroll-Project/blob/34def8fb7416c1a571bd876d1d7e0f672d19944f/Images/Dashboard%202.png)

## 3. About Dataset:

The datasets contains yellow trip records in 2024, which include pickup and drop-off
dates/times, pickup and drop-off locations, trip distances, itemized fares, rate types, payment
types and driver-reported passenger counts. The dataset was collected and provided to NYC
Taxi and Limousine Commission (TLC) by technology providers authorized under the Taxicab
& Livery Passenger Enhancement Programs (TPEP/LPEP). The data contains parquet files of
yellow trip recorded within each month followed by a taxi zone lookup table. Furthermore,
there was also a taxi zone lookup file that helps explaining the location id with borough, zone
and service zone. The datasource can be accessed [Here](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page) or download in my [GitHub Repository](https://github.com/minhD03/NYCTaxi_Yellow-Project/tree/cf2e4579d42d79dbeb3748a0bd6376449f71965e/Data)

Below is the description about columns in dataset:

![alt text](https://github.com/minhD03/NYCTaxi_Yellow-Project/blob/fa478965d9e2c18bf68c236202b911107396f464/Images/About%20Dataset.png)

## 4. Tool used: 
### a) Microsoft Fabric:

Microsoft Fabric is an end-to-end analytics platform that unifies data engineering, data
science, real-time analytics and business intelligence under one roof. It integrates services like
Power BI, Data Factory and Synapse into a single SaaS experience, enabling seamless data
movement, transformation and visualization. Designed for scalability and collaboration, Fabric
empowers organizations to build robust data pipelines, model complex datasets and deliver
impactful insights—all without switching between fragmented tools.
When it comes to processing Parquet files, Microsoft Fabric supports them across various
components, especially within Data Factory pipelines and Dataflow Gen2. You can configure
Parquet as both a source and destination format in copy activities, with options to set
compression types (e.g., gzip, snappy, Brotli), control file naming patterns and optimize write
performance using V-Order. In Dataflows, Fabric uses Power Query connectors to ingest
Parquet files, allowing for transformation and enrichment before loading into Lakehouses or
other destinations. This makes it ideal for handling large-scale, columnar data efficiently across
your analytics workflows.

### b) Power BI:

Power BI is a powerful business analytics tool developed by Microsoft that enables users to visualize data, uncover insights and make informed decisions. It allows seamless integration with various data sources—ranging from Excel spreadsheets to cloud-based databases—and transforms raw data into interactive dashboards and reports. With features like real-time data updates, natural language queries and customizable visuals, Power BI empowers organizations to monitor key performance indicators (KPIs), identify trends and share insights across teams. Its intuitive interface and robust analytical capabilities make it a go-to solution for both technical analysts and business stakeholders seeking data-driven clarity. Therefore, this tool is suitable for live tracking taxi trips records.
