# Homework1 SQL Answers

## Question 3
SELECT count(1) FROM green_taxi_data 
WHERE lpep_pickup_datetime >= '2025-11-01' AND lpep_pickup_datetime < '2025-12-01'
AND trip_distance <= 1;
Result: 8007

## Question 4
SELECT lpep_pickup_datetime::DATE AS day, MAX(trip_distance) AS max_dist
FROM green_taxi_data
WHERE trip_distance < 100
GROUP BY 1 ORDER BY max_dist DESC LIMIT 1;
Result: 2025-11-14

## Question 5
SELECT z."Zone", SUM(t.total_amount) AS total_sum
FROM green_taxi_data t
JOIN zones z ON t."PULocationID" = z."LocationID"
WHERE t.lpep_pickup_datetime::DATE = '2025-11-18'
GROUP BY 1 ORDER BY total_sum DESC LIMIT 1;
Result: East Harlem North

## Question 6
SELECT z_do."Zone", MAX(t.tip_amount) AS max_tip
FROM green_taxi_data t
JOIN zones z_pu ON t."PULocationID" = z_pu."LocationID"
JOIN zones z_do ON t."DOLocationID" = z_do."LocationID"
WHERE z_pu."Zone" = 'East Harlem North'
GROUP BY 1 ORDER BY max_tip DESC LIMIT 1;
Result: Yorkville West

# Data Engineering Zoomcamp 2026 - Module 3: Data Warehousing

This repository contains the solution for the Module 3 homework, focusing on **Google Cloud Storage (GCS)** and **BigQuery (BQ)** performance optimization through Partitioning and Clustering.

---

## 🏗️ Data Loading & Setup

### 1. Data Ingestion
The dataset includes Yellow Taxi Trip Records from **January 2024 to June 2024**.
- **Script**: `load_yellow_taxi_data.py` was used to download Parquet files and upload them to a GCS bucket.
- **Service Account**: Authenticated using a JSON key with `Storage Admin` privileges.

### 2. Table Creation
The following SQL commands were used to set up the environment in BigQuery:

```sql
-- Create an External Table pointing to GCS
CREATE OR REPLACE EXTERNAL TABLE `kestra-sandbox-485523.zoomcamp.external_yellow_tripdata`
OPTIONS (
  format = 'PARQUET',
  uris = ['gs://your-bucket-name/yellow_tripdata_2024-*.parquet']
);

-- Create a Regular (Materialized) Table
CREATE OR REPLACE TABLE `kestra-sandbox-485523.zoomcamp.yellow_tripdata_2024` AS
SELECT * FROM `kestra-sandbox-485523.zoomcamp.external_yellow_tripdata`;

-- Create an Optimized Partitioned and Clustered Table
CREATE OR REPLACE TABLE `kestra-sandbox-485523.zoomcamp.yellow_tripdata_2024_partitioned`
PARTITION BY DATE(tpep_dropoff_datetime)
CLUSTER BY VendorID AS
SELECT * FROM `kestra-sandbox-485523.zoomcamp.external_yellow_tripdata`;