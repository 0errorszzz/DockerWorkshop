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