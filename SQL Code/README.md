# 🗂 NYC Taxi Data Processing Pipeline (SQL Script + Explanation)

This document describes and explains the full SQL workflow for processing **NYC Yellow Taxi** data through multiple stages:  
**staging → cleaning → transformation → presentation → metadata logging**.

---

## 1. Check Data Range in Staging Table

### 🧾 Code

SELECT
MIN(tpep_pickup_datetime) AS min_pickup,
MAX(tpep_pickup_datetime) AS max_pickup
FROM stg.nyctaxi_yellow;

- Retrieves the earliest (MIN) and latest (MAX) pickup timestamps from stg.nyctaxi_yellow.

- Helps understand the data range before cleaning or further processing.

## 2. Data Cleaning Procedure (Staging)
CREATE OR ALTER PROCEDURE stg.data_cleaning_stg
@start_date datetime2,
@end_date datetime2 
AS
BEGIN
DELETE FROM stg.nyctaxi_yellow
WHERE tpep_pickup_datetime < @start_date
OR tpep_pickup_datetime > @end_date
END;

- Defines a stored procedure stg.data_cleaning_stg to remove records outside a defined date range.

- Ensures staging data only includes valid records between @start_date and @end_date.

## 3.Verify Cleaned Data Range:

SELECT
MIN(tpep_pickup_datetime) AS min_pickup,
MAX(tpep_pickup_datetime) AS max_pickup
FROM stg.nyctaxi_yellow;

- Runs again to confirm the new minimum and maximum pickup timestamps after cleaning.

## 4. Create Metadata Schema and Log Table
CREATE SCHEMA metadata;

CREATE TABLE metadata.processing_log
(
    pipeline_run_id varchar(255),
    table_processed varchar(255),
    rows_processed INT,
    latest_processed_pickup datetime2(6),
    processed_datetime datetime2(6)
);

- Creates a metadata schema to store audit information.

- Defines a processing_log table that tracks:

	- Pipeline run ID

	- Table processed

	- Number of rows handled

	- Most recent pickup datetime processed

	- Timestamp when the process was executed

## 5. Insert Metadata for Staging Table
CREATE OR ALTER PROCEDURE metadata.insert_staging_metadata
    @pipeline_run_id VARCHAR(255),
    @table_name VARCHAR(255),
    @processed_date DATETIME2
AS
INSERT INTO metadata.processing_log (pipeline_run_id, table_processed, rows_processed, latest_processed_pickup, processed_datetime)
SELECT
    @pipeline_run_id AS pipeline_id,
    @table_name AS table_processed,
    COUNT(*) AS rows_processed,
    MAX(tpep_pickup_datetime) AS latest_processed_pickup,
    @processed_date AS processed_datetime
FROM stg.nyctaxi_yellow;

- Inserts a log entry for the staging data load.

- Records key statistics about the process:

	- Total rows processed

	- Most recent pickup timestamp

	- Processing timestamp

## 6. Create Presentation Table

DROP TABLE IF EXISTS dbo.nyctaxi_yellow;

CREATE TABLE dbo.nyctaxi_yellow
(
  vendor varchar(50),
  tpep_pickup_datetime date,
  tpep_dropoff_datetime date,
  pu_borough varchar(100),
  pu_zone varchar(100),
  do_borough varchar(100),
  do_zone varchar(100),
  payment_method varchar(50),
  passenger_count int,
  trip_distance FLOAT,
  total_amount FLOAT
);
- Drops any existing presentation table to avoid duplication.

- Recreates dbo.nyctaxi_yellow as a clean, ready-for-analytics dataset.

- Contains standardized, human-readable columns.


## 7. Create Presentation Table:
CREATE PROCEDURE dbo.process_presentation
AS
INSERT INTO dbo.nyctaxi_yellow
SELECT
    CASE
        WHEN nty.VendorID = 1 THEN 'Creative Mobile Technologies'
        WHEN nty.VendorID = 2 THEN 'VeriFone'
        ELSE 'Unknown'
    END AS vendor,
    FORMAT(nty.tpep_pickup_datetime,'yyyy-MM-dd') AS tpep_pickup_datetime,
    FORMAT(nty.tpep_dropoff_datetime,'yyyy-MM-dd') AS tpep_dropoff_datetime,
    lu1.Borough AS pu_borough,
    lu1.Zone AS pu_zone,
    lu2.Borough AS do_borough,
    lu2.Zone AS do_zone,
    CASE
        WHEN nty.payment_type = 1 THEN 'Credit Card'
        WHEN nty.payment_type = 2 THEN 'Cash'
        WHEN nty.payment_type = 3 THEN 'No Charge'
        WHEN nty.payment_type = 4 THEN 'Dispute'
        WHEN nty.payment_type = 5 THEN 'Unknown'
        WHEN nty.payment_type = 6 THEN 'Voided Trip'
        ELSE 'Unknown'
    END AS payment_method,
    nty.passenger_count,
    nty.trip_distance,
    nty.total_amount
FROM stg.nyc_taxi_yellow nty
LEFT JOIN stg.taxi_zone_lookup lu1 ON nty.PULocationID = lu1.LocationID
LEFT JOIN stg.taxi_zone_lookup lu2 ON nty.DOLocationID = lu2.LocationID;
This procedure transforms and loads cleaned data into the presentation table.

Key transformations:

- Converts numeric codes to human-readable labels for:

	- Vendor (VendorID)

	- Payment type (payment_type)

- Joins with lookup tables (taxi_zone_lookup) to enrich data with borough and zone names.

- Formats datetime fields into simple date strings (yyyy-MM-dd).

- Final output is ready for reporting and visualization.

## 8. Insert Metadata for Presentation Table:

CREATE PROCEDURE metadata.insert_presentation_metadata
    @pipeline_run_id VARCHAR(255),
    @table_name VARCHAR(255),
    @processed_date DATETIME2
AS
INSERT INTO metadata.processing_log (pipeline_run_id, table_processed, rows_processed, latest_processed_pickup, processed_datetime)
SELECT
    @pipeline_run_id AS pipeline_id,
    @table_name AS table_processed,
    COUNT(*) AS rows_processed,
    MAX(tpep_pickup_datetime) AS latest_processed_pickup,
    @processed_date AS processed_datetime
FROM dbo.nyctaxi_yellow;


- Similar to the staging metadata insertion procedure.

- Logs processing details for the presentation layer after transformation.

- Maintains traceability across the entire ETL pipeline.

## 9. View Metadata Logs:

SELECT * FROM [metadata].[processing_log];
Retrieves all entries from the metadata.processing_log.

- Provides a clear audit trail of each pipeline execution, showing:

	- Which tables were processed

	- How many rows were handled

	- When they were processed
## 10. Summary Table:

| Step | Component                    | Description                                 |
| ---- | ---------------------------- | ------------------------------------------- |
| 1    | Data Range Check             | Inspects the range of timestamps in staging |
| 2    | Data Cleaning                | Deletes out-of-range records                |
| 3    | Post-Clean Validation        | Rechecks data range after cleaning          |
| 4    | Metadata Setup               | Creates schema and log table                |
| 5    | Insert Staging Metadata      | Logs staging process info                   |
| 6    | Create Presentation Table    | Defines the clean, final table              |
| 7    | Process Presentation Data    | Transforms & loads enriched data            |
| 8    | Insert Presentation Metadata | Logs presentation process info              |
| 9    | View Processing Log          | Displays pipeline history                   |


## 11. ETL Pipeline Flow Diagram:

        ┌────────────────────────┐
        │   Raw Taxi Data (Stg)  │
        └──────────┬─────────────┘
                   │
                   ▼
        ┌────────────────────────┐
        │ Data Cleaning (stg.)   │
        └──────────┬─────────────┘
                   │
                   ▼
        ┌────────────────────────┐
        │  Transform & Load (dbo)│
        │  + Join Lookup Tables  │
        └──────────┬─────────────┘
                   │
                   ▼
        ┌────────────────────────┐
        │ Metadata Logging        │
        │ (Insert Log Entries)    │
        └────────────────────────┘
