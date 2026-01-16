🏃 Fitness Tracker Data Analysis (PostgreSQL)

This project uses PostgreSQL to create, clean, and analyze a dataset of fitness trackers. The goal is to evaluate pricing, ratings, and value-for-money across different brands and models while applying database optimization techniques such as indexes.

📁 Project Description

The SQL script performs the following tasks:

Creates a PostgreSQL database and table

Imports a cleaned CSV dataset

Cleans and converts numeric fields

Executes analytical queries

Creates indexes to improve query performance

🛠 Technologies Used

PostgreSQL

SQL

CSV Dataset (Fitness Trackers)

📂 Dataset Overview

The dataset contains information about fitness trackers, including:

Brand name

Device type

Model name

Color

Selling price

Original price

Display type

Rating

Strap material

Average battery life

Example dataset file:

D597 Task 1 Dataset 1_Fitness_trackers_clean.csv

🚀 Setup Instructions
1️⃣ Create the Database
CREATE DATABASE fitness_db;

2️⃣ Create the Table
CREATE TABLE fit_track (
    tracker_id      SERIAL PRIMARY KEY,
    brand_name      VARCHAR(100) NOT NULL,
    device_type     VARCHAR(100) NOT NULL,
    model_name      VARCHAR(100) NOT NULL,
    color           VARCHAR(50),
    selling_price   TEXT,
    original_price  TEXT,
    display         VARCHAR(100),
    rating          NUMERIC(3,1),
    strap_material  VARCHAR(100),
    avg_batt_life   INTEGER
);

📥 Load & Clean Data
Import CSV File
COPY fit_track (
    brand_name,
    device_type,
    model_name,
    color,
    selling_price,
    original_price,
    display,
    rating,
    strap_material,
    avg_batt_life
)
FROM 'C:/WGU/D597 Task 1 Dataset 1_Fitness_trackers_clean.csv'
WITH (FORMAT csv, HEADER true);

Clean Numeric Fields
UPDATE fit_track
SET selling_price  = REPLACE(selling_price, ',', ''),
    original_price = REPLACE(original_price, ',', '');

Convert Text to Numeric
ALTER TABLE fit_track
ALTER COLUMN selling_price TYPE NUMERIC(10,2)
USING selling_price::numeric,
ALTER COLUMN original_price TYPE NUMERIC(10,2)
USING original_price::numeric;

🔍 Analytical Queries
Query 1: Best Value-for-Money Trackers

Calculates a value score using rating divided by selling price.

SELECT
    tracker_id,
    brand_name,
    model_name,
    selling_price,
    rating,
    ROUND((rating / NULLIF(selling_price, 0)), 4) AS value_score
FROM fit_track
ORDER BY value_score DESC
LIMIT 10;

Query 2: Brand Comparison

Compares brands by:

Number of models

Average rating

Average price

SELECT
    brand_name,
    COUNT(*) AS num_models,
    ROUND(AVG(rating), 2) AS avg_rating,
    ROUND(AVG(selling_price), 2) AS avg_price
FROM fit_track
GROUP BY brand_name
ORDER BY avg_rating DESC, avg_price ASC;

Query 3: Affordable & Highly Rated Trackers

Identifies trackers with ratings ≥ 4.0 at the lowest prices.

SELECT
    tracker_id,
    brand_name,
    model_name,
    selling_price,
    rating
FROM fit_track
WHERE rating >= 4.0
ORDER BY selling_price ASC, rating DESC
LIMIT 10;

⚡ Performance Optimization

Indexes are created to improve query efficiency:

-- Value-for-money calculation
CREATE INDEX idx_fit_track_value_score
ON fit_track ((rating / selling_price));

-- Brand grouping
CREATE INDEX idx_fit_track_brand
ON fit_track (brand_name);

-- Price & rating filtering
CREATE INDEX idx_fit_track_price_rating
ON fit_track (selling_price, rating);

📈 Key Insights

Combines data cleaning, analysis, and optimization

Demonstrates calculated metrics using SQL expressions

Shows practical use of indexes for performance

Suitable for large analytical datasets
