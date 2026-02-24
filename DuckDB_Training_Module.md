# DuckDB SQL Training Module for Health Researchers
## National Institute of Health Malaysia
### Ministry of Health Malaysia

---

## 📋 Course Overview

**Target Audience:** Health researchers familiar with R for data analytics  
**Dataset:** COVID-19 Deaths Linelist from KKMNow  
**Duration:** 3 sessions (Basic, Intermediate, Advanced)  
**Objective:** Master SQL-based data wrangling in DuckDB for health informatics workflows

---

## 🎯 Why DuckDB for Health Research?

### Key Advantages Over Traditional R Workflows:

1. **Speed & Performance**
   - Columnar storage: 10-100x faster than R for large datasets
   - Parallel query execution: Automatically uses all CPU cores
   - Zero-copy integration: No data serialization overhead

2. **Simplicity**
   - Direct CSV/Parquet querying without loading into memory
   - No package dependencies or version conflicts
   - Single-file database (portable across systems)

3. **Scalability**
   - Handles datasets larger than RAM (out-of-core processing)
   - Efficient handling of 10GB+ health datasets
   - Seamless transition from GB to TB scale data

4. **SQL Standardization**
   - Industry-standard syntax (transferable skills)
   - Better for collaboration with data engineers
   - More explicit and auditable data transformations

5. **Health Informatics Specific Benefits**
   - Fast aggregations for surveillance reporting
   - Efficient date/time operations for epidemiological analysis
   - Built-in statistical functions
   - Easy integration with existing R pipelines if needed

---

# 📚 MODULE 1: BASIC LEVEL
## Foundation of DuckDB SQL for Data Wrangling

### Session Duration: 1 - 3 hours

---

## 1.1 Setting Up DuckDB

### Installation and First Query

```sql
-- DuckDB can be installed as:
-- 1. Standalone CLI tool (download the path from https://duckdb.org/install/?platform=windows&environment=cli ) , download and load the CLI installation
-- 2. R package: install.packages("duckdb")
-- 3. Python package: pip install duckdb

-- Starting DuckDB (CLI)
-- Just type: duckdb
-- Or connect to a database file: duckdb mydata.db
-- To check duckdb version type command 'duckdb --version"
-- If previously already installed , start your command with 'winget upgrade duckdb.cli"
```
### DuckDB UI

```sql
-- to call the UI , type 'duckdb --ui'
-- wait for the ui to appear in your browser
```

### Creating A Table Using SQL

```sql
CREATE TABLE worker (
    name VARCHAR,
    age INTEGER,    -- whole number
    ic_no BIGINT,   -- BIGINT is needed for a 10-digit number
    unit STRING,    -- DuckDB fully supports STRING!
    date DATE
);

-- if the table already created use syntax " CREATE OR REPLACE ........
-- but this wil rewrite every existing data present 
```

### Insert Data Into a Table

```sql
INSERT INTO worker (name, age, ic_no, unit, date)
VALUES 
    ('Ainil Zafirah', 26, 1234567890, 'Methodology', '2026-01-15'),
    ('Nur Liana', 18 , 9876543210, 'Data Repository', '2025-11-01'),
    ('Nik Nor Syamimi', 32 , 5556667777, 'Big Data Analytics', '2026-02-20');
```
### Read or View A Table

```sql
SELECT * FROM worker ; -- symbol * meaning all columns present in a table
```
### Joining 2 Tables

```sql
-- create another table 'incharge'
CREATE TABLE incharge (
    work_type STRING,
    incharge_officer VARCHAR,
    office_location STRING
);

-- insert data into the table
INSERT INTO  (work_type, incharge_officer, office_location)
VALUES 
    ('JPN', 'Ainil Zafirah', 'Compartment C4'),
    ('KPI', 'Nur Liana', 'Compartment C3'),
    ('Dashboard', 'Yusrina', 'Compartment C2');

-- inner join
SELECT 
    worker.name, 
    worker.unit, 
    incharge.work type, 
    incharge.office_location
FROM worker
INNER JOIN incharge 
    ON worker.name =  incharge.incharge_officer;

-- left join
SELECT 
    worker.name, 
    worker.unit, 
    incharge.work type, 
    incharge.office_location
FROM worker
LEFT JOIN department 
    ON worker.name =  incharge.incharge_officer;

-- full outer join
SELECT 
    worker.name, 
    worker.unit, 
    incharge.work type, 
    incharge.office_location
FROM worker
FULL OUTER JOIN department 
    ON worker.name =  incharge.incharge_officer;
```


### Reading CSV Files Directly

```sql
-- Basic read: No need to import/load data first!
SELECT * 
FROM 'https://storage.data.gov.my/healthcare/covid_deaths_linelist.csv' 
LIMIT 10;

-- This is REVOLUTIONARY compared to R:
-- R requires: df <- read.csv("file.csv")  # Loads entire file into memory
-- DuckDB: Queries directly from file, only loads what you need
```

### Creating Views for Reusability

```sql
-- Create a view to reference the dataset easily
CREATE VIEW covid_deaths AS
SELECT * FROM 'https://storage.data.gov.my/healthcare/covid_deaths_linelist.csv';

-- Now query the view
SELECT * FROM covid_deaths LIMIT 5;
```

---

## 1.2 Basic Data Inspection (Equivalent to R's summary(), str(), head())

### View Data Structure

```sql
-- Check column names and types (R: str(df))
DESCRIBE covid_deaths;

-- Alternative: See detailed schema
PRAGMA table_info('covid_deaths');
```

### Basic Statistics

```sql
-- Count total records (R: nrow(df))
SELECT COUNT(*) AS total_deaths 
FROM covid_deaths;

-- Count by group (R: table(df$state))
SELECT state, COUNT(*) AS death_count
FROM covid_deaths
GROUP BY state
ORDER BY death_count DESC;

-- Summary statistics for age (R: summary(df$age))
SELECT 
    MIN(age) AS min_age,
    MAX(age) AS max_age,
    AVG(age) AS mean_age,
    MEDIAN(age) AS median_age,
    STDDEV(age) AS sd_age,
    COUNT(*) AS n
FROM covid_deaths;
```

---

## 1.3 Selecting and Filtering Data

### Basic SELECT (R: select() from dplyr)

```sql
-- Select specific columns
SELECT date, age, male, state, comorb
FROM covid_deaths
LIMIT 100;

-- Select and rename columns (R: select(df, death_date = date))
SELECT 
    date AS death_date,
    age AS age_years,
    male AS is_male,
    state AS state_name
FROM covid_deaths;

-- Select all except certain columns
SELECT * EXCLUDE (date_announced, date_positive)
FROM covid_deaths
LIMIT 10;
```

### Filtering Rows (R: filter() from dplyr)

```sql
-- Filter by single condition (R: filter(df, age > 60))
SELECT *
FROM covid_deaths
WHERE age > 60;

-- Multiple conditions with AND (R: filter(df, age > 60 & male == 1))
SELECT *
FROM covid_deaths
WHERE age > 60 
  AND male = 1;

-- Multiple conditions with OR
SELECT *
FROM covid_deaths
WHERE state = 'Selangor' 
   OR state = 'Kuala Lumpur';

-- Using IN operator for multiple values (cleaner syntax)
SELECT *
FROM covid_deaths
WHERE state IN ('Selangor', 'Kuala Lumpur', 'Johor');

-- Filtering NULL values (R: filter(df, !is.na(date_dose1)))
SELECT *
FROM covid_deaths
WHERE date_dose1 IS NOT NULL;

-- Filtering by date range
SELECT *
FROM covid_deaths
WHERE date BETWEEN '2021-01-01' AND '2021-12-31';
```

---

## 1.4 Sorting Data (R: arrange() from dplyr)

```sql
-- Sort ascending (R: arrange(df, age))
SELECT *
FROM covid_deaths
ORDER BY age
LIMIT 20;

-- Sort descending (R: arrange(df, desc(age)))
SELECT *
FROM covid_deaths
ORDER BY age DESC
LIMIT 20;

-- Multiple sorting columns
SELECT *
FROM covid_deaths
ORDER BY state, age DESC;

-- Sort with NULL handling
SELECT *
FROM covid_deaths
ORDER BY date_dose1 NULLS LAST;
```

---

## 1.5 Basic Aggregations (R: summarise() from dplyr)

### Count and Sum

```sql
-- Count records (R: nrow(df))
SELECT COUNT(*) AS total_deaths
FROM covid_deaths;

-- Count distinct values (R: length(unique(df$state)))
SELECT COUNT(DISTINCT state) AS num_states
FROM covid_deaths;

-- Count non-NULL values
SELECT COUNT(date_dose1) AS vaccinated_deaths
FROM covid_deaths;
```

### Group By Operations (R: group_by() + summarise())

```sql
-- Deaths by state (R: df %>% group_by(state) %>% summarise(n = n()))
SELECT 
    state,
    COUNT(*) AS death_count
FROM covid_deaths
GROUP BY state
ORDER BY death_count DESC;

-- Multiple aggregations
SELECT 
    state,
    COUNT(*) AS total_deaths,
    AVG(age) AS avg_age,
    SUM(CASE WHEN male = 1 THEN 1 ELSE 0 END) AS male_deaths,
    SUM(CASE WHEN comorb = 1 THEN 1 ELSE 0 END) AS deaths_with_comorbidity
FROM covid_deaths
GROUP BY state;

-- Filtering aggregated results (R: having is like filter after summarise)
SELECT 
    state,
    COUNT(*) AS death_count
FROM covid_deaths
GROUP BY state
HAVING COUNT(*) > 1000
ORDER BY death_count DESC;
```

---

## 1.6 Creating New Columns (R: mutate() from dplyr)

```sql
-- Create age groups (R: mutate(df, age_group = case_when(...)))
SELECT 
    *,
    CASE 
        WHEN age < 18 THEN '0-17'
        WHEN age BETWEEN 18 AND 39 THEN '18-39'
        WHEN age BETWEEN 40 AND 59 THEN '40-59'
        WHEN age >= 60 THEN '60+'
        ELSE 'Unknown'
    END AS age_group
FROM covid_deaths;

-- Create vaccination status indicator
SELECT 
    *,
    CASE 
        WHEN date_dose3 IS NOT NULL THEN 'Booster'
        WHEN date_dose2 IS NOT NULL THEN 'Fully Vaccinated'
        WHEN date_dose1 IS NOT NULL THEN 'Partially Vaccinated'
        ELSE 'Unvaccinated'
    END AS vaccination_status
FROM covid_deaths;

-- Calculate days between dates
SELECT 
    *,
    date - date_positive AS days_positive_to_death,
    date - date_dose2 AS days_since_dose2
FROM covid_deaths
WHERE date_positive IS NOT NULL;

-- Create binary flags
SELECT 
    *,
    CASE WHEN age >= 60 THEN 1 ELSE 0 END AS elderly,
    CASE WHEN state IN ('Selangor', 'Kuala Lumpur') THEN 1 ELSE 0 END AS urban
FROM covid_deaths;
```

---

## 1.7 Handling Missing Values

```sql
-- Count missing values per column
SELECT 
    COUNT(*) - COUNT(date_dose1) AS missing_dose1,
    COUNT(*) - COUNT(date_dose2) AS missing_dose2,
    COUNT(*) - COUNT(date_dose3) AS missing_dose3,
    COUNT(*) - COUNT(brand1) AS missing_brand1
FROM covid_deaths;

-- Filter out rows with missing values
SELECT *
FROM covid_deaths
WHERE age IS NOT NULL 
  AND state IS NOT NULL;

-- Replace NULL with default value (R: replace_na())
SELECT 
    *,
    COALESCE(date_dose1, '1900-01-01') AS dose1_filled,
    COALESCE(brand1, 'Not Vaccinated') AS brand1_filled
FROM covid_deaths;
```

---

## 1.8 Basic String Operations

```sql
-- Convert to uppercase/lowercase
SELECT 
    state,
    UPPER(state) AS state_upper,
    LOWER(brand1) AS brand_lower
FROM covid_deaths
LIMIT 10;

-- String concatenation
SELECT 
    state || ' - ' || CAST(age AS VARCHAR) AS location_age
FROM covid_deaths
LIMIT 10;

-- Pattern matching (R: grepl())
SELECT *
FROM covid_deaths
WHERE state LIKE '%Kuala%';

-- Check if string contains value
SELECT *
FROM covid_deaths
WHERE brand1 LIKE '%Pfizer%';
```

---

## 1.9 Saving Results

```sql
-- Export to CSV (Better than R's write.csv - faster!)
COPY (
    SELECT state, COUNT(*) AS death_count
    FROM covid_deaths
    GROUP BY state
) TO 'state_summary.csv' (HEADER, DELIMITER ',');

-- Export to Parquet (compressed, faster to read back)
COPY (
    SELECT *
    FROM covid_deaths
    WHERE age > 60
) TO 'elderly_deaths.parquet' (FORMAT PARQUET);

-- Create a persistent table in database
CREATE TABLE elderly_deaths AS
SELECT *
FROM covid_deaths
WHERE age >= 60;
```

---

## 🏋️ BASIC LEVEL EXERCISES

### Exercise 1: Data Inspection
```sql
-- Task: Find how many deaths occurred in your state of interest
-- Show: total deaths, average age, percentage male
```

### Exercise 2: Filtering & Sorting
```sql
-- Task: Find all deaths in 2022 where the person was:
-- - Unvaccinated (date_dose1 IS NULL)
-- - Had comorbidity
-- - Sort by age (youngest first)
```

### Exercise 3: Grouping & Aggregation
```sql
-- Task: Create a summary showing for each vaccination brand (brand1):
-- - Number of deaths
-- - Average age
-- - Percentage with comorbidity
-- Only include brands with more than 100 deaths
```

### Exercise 4: Creating Variables
```sql
-- Task: Create a new column 'risk_category' where:
-- - 'High Risk': age >= 60 AND comorb = 1
-- - 'Moderate Risk': age >= 60 OR comorb = 1
-- - 'Low Risk': age < 60 AND comorb = 0
-- Count deaths in each category
```

---

# 📚 MODULE 2: INTERMEDIATE LEVEL
## Advanced Data Wrangling Techniques

### Session Duration: 4 hours

---

## 2.1 Advanced Date Manipulations

### Date Extraction and Formatting

```sql
-- Extract date parts (R: lubridate functions)
SELECT 
    date,
    YEAR(date) AS year,
    MONTH(date) AS month,
    QUARTER(date) AS quarter,
    DAYOFWEEK(date) AS day_of_week,
    DAYNAME(date) AS day_name,
    WEEK(date) AS week_number
FROM covid_deaths
LIMIT 10;

-- Create period groupings for time series
SELECT 
    date_trunc('month', date) AS month,
    COUNT(*) AS deaths
FROM covid_deaths
GROUP BY date_trunc('month', date)
ORDER BY month;

-- Weekly deaths
SELECT 
    date_trunc('week', date) AS week_start,
    COUNT(*) AS weekly_deaths
FROM covid_deaths
GROUP BY week_start
ORDER BY week_start;
```

### Date Calculations

```sql
-- Calculate time intervals (days between events)
SELECT 
    date,
    date_positive,
    date_dose2,
    date - date_positive AS days_positive_to_death,
    date_dose2 - date_dose1 AS days_between_doses,
    date - date_dose2 AS days_since_fully_vaccinated,
    CASE 
        WHEN date_dose2 IS NULL THEN NULL
        WHEN date - date_dose2 < 14 THEN 'Incomplete Protection'
        WHEN date - date_dose2 BETWEEN 14 AND 180 THEN 'Recent Vaccination'
        WHEN date - date_dose2 > 180 THEN 'Waning Immunity'
    END AS vaccine_protection_period
FROM covid_deaths
WHERE date_positive IS NOT NULL
LIMIT 20;

-- Analyze vaccination timing relative to death
SELECT 
    CASE 
        WHEN date_dose2 IS NULL THEN 'Unvaccinated'
        WHEN date < date_dose2 THEN 'Died before fully vaccinated'
        WHEN date - date_dose2 < 14 THEN 'Died <2 weeks after dose 2'
        WHEN date - date_dose2 BETWEEN 14 AND 90 THEN 'Died 2 weeks - 3 months'
        WHEN date - date_dose2 BETWEEN 91 AND 180 THEN 'Died 3-6 months'
        WHEN date - date_dose2 > 180 THEN 'Died >6 months'
    END AS time_since_vaccination,
    COUNT(*) AS death_count
FROM covid_deaths
GROUP BY time_since_vaccination
ORDER BY death_count DESC;
```

---

## 2.2 Window Functions (R: Advanced dplyr operations)

### Running Totals and Cumulative Statistics

```sql
-- Calculate cumulative deaths over time (R: cumsum())
SELECT 
    date,
    COUNT(*) AS daily_deaths,
    SUM(COUNT(*)) OVER (ORDER BY date) AS cumulative_deaths
FROM covid_deaths
GROUP BY date
ORDER BY date;

-- 7-day moving average (critical for surveillance!)
SELECT 
    date,
    COUNT(*) AS daily_deaths,
    AVG(COUNT(*)) OVER (
        ORDER BY date 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS deaths_7day_avg
FROM covid_deaths
GROUP BY date
ORDER BY date;

-- 14-day rolling sum
SELECT 
    date,
    COUNT(*) AS daily_deaths,
    SUM(COUNT(*)) OVER (
        ORDER BY date 
        ROWS BETWEEN 13 PRECEDING AND CURRENT ROW
    ) AS deaths_14day_sum
FROM covid_deaths
GROUP BY date
ORDER BY date;
```

### Ranking and Percentiles

```sql
-- Rank states by death count (R: mutate(rank = rank(desc(n))))
SELECT 
    state,
    COUNT(*) AS death_count,
    RANK() OVER (ORDER BY COUNT(*) DESC) AS rank,
    DENSE_RANK() OVER (ORDER BY COUNT(*) DESC) AS dense_rank,
    ROW_NUMBER() OVER (ORDER BY COUNT(*) DESC) AS row_num
FROM covid_deaths
GROUP BY state;

-- Age percentiles by state
SELECT 
    state,
    age,
    PERCENT_RANK() OVER (PARTITION BY state ORDER BY age) AS age_percentile,
    NTILE(4) OVER (PARTITION BY state ORDER BY age) AS age_quartile
FROM covid_deaths;

-- Find the oldest and youngest death in each state
SELECT DISTINCT
    state,
    FIRST_VALUE(age) OVER (PARTITION BY state ORDER BY age) AS youngest_age,
    LAST_VALUE(age) OVER (PARTITION BY state ORDER BY age 
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS oldest_age
FROM covid_deaths;
```

### Lead and Lag (Compare with previous/next records)

```sql
-- Compare current day deaths with previous day
SELECT 
    date,
    COUNT(*) AS deaths,
    LAG(COUNT(*), 1) OVER (ORDER BY date) AS prev_day_deaths,
    COUNT(*) - LAG(COUNT(*), 1) OVER (ORDER BY date) AS change_from_prev_day,
    LEAD(COUNT(*), 1) OVER (ORDER BY date) AS next_day_deaths
FROM covid_deaths
GROUP BY date
ORDER BY date;

-- Calculate week-over-week change
SELECT 
    date_trunc('week', date) AS week,
    COUNT(*) AS weekly_deaths,
    LAG(COUNT(*), 1) OVER (ORDER BY date_trunc('week', date)) AS prev_week_deaths,
    ROUND(
        100.0 * (COUNT(*) - LAG(COUNT(*), 1) OVER (ORDER BY date_trunc('week', date))) / 
        LAG(COUNT(*), 1) OVER (ORDER BY date_trunc('week', date)), 
        2
    ) AS percent_change
FROM covid_deaths
GROUP BY week
ORDER BY week;
```

---

## 2.3 Joins and Combining Data

### Creating Reference Tables

```sql
-- Create a state population reference table
CREATE TEMP TABLE state_population AS
SELECT * FROM (VALUES
    ('Johor', 3800000),
    ('Kedah', 2200000),
    ('Kelantan', 1900000),
    ('Melaka', 950000),
    ('Negeri Sembilan', 1130000),
    ('Pahang', 1680000),
    ('Penang', 1770000),
    ('Perak', 2510000),
    ('Perlis', 250000),
    ('Sabah', 3900000),
    ('Sarawak', 2820000),
    ('Selangor', 6700000),
    ('Terengganu', 1250000),
    ('Kuala Lumpur', 1980000),
    ('Labuan', 100000),
    ('Putrajaya', 110000)
) AS t(state, population);
```

### Inner Join

```sql
-- Calculate death rate per 100,000 population (INNER JOIN)
SELECT 
    d.state,
    COUNT(*) AS total_deaths,
    p.population,
    ROUND(COUNT(*) * 100000.0 / p.population, 2) AS deaths_per_100k
FROM covid_deaths d
INNER JOIN state_population p ON d.state = p.state
GROUP BY d.state, p.population
ORDER BY deaths_per_100k DESC;
```

### Left Join (Keep all records from left table)

```sql
-- Check for any states in deaths data not in population table
SELECT 
    d.state,
    COUNT(*) AS death_count,
    p.population
FROM covid_deaths d
LEFT JOIN state_population p ON d.state = p.state
GROUP BY d.state, p.population
ORDER BY p.population NULLS FIRST;
```

### Self Join (Compare records within same table)

```sql
-- Find cases where multiple people died on same date in same state
SELECT 
    d1.date,
    d1.state,
    COUNT(*) AS deaths_same_day
FROM covid_deaths d1
GROUP BY d1.date, d1.state
HAVING COUNT(*) > 10
ORDER BY deaths_same_day DESC;
```

---

## 2.4 Subqueries and CTEs (Common Table Expressions)

### Subquery in WHERE clause

```sql
-- Find deaths above the overall average age
SELECT *
FROM covid_deaths
WHERE age > (SELECT AVG(age) FROM covid_deaths);

-- Deaths in states with above-average death counts
SELECT *
FROM covid_deaths
WHERE state IN (
    SELECT state
    FROM covid_deaths
    GROUP BY state
    HAVING COUNT(*) > (
        SELECT COUNT(*) / COUNT(DISTINCT state) 
        FROM covid_deaths
    )
);
```

### CTEs for Readable Complex Queries

```sql
-- Better alternative to nested subqueries: Use CTEs
WITH monthly_stats AS (
    SELECT 
        date_trunc('month', date) AS month,
        COUNT(*) AS monthly_deaths,
        AVG(age) AS avg_age
    FROM covid_deaths
    GROUP BY month
),
state_stats AS (
    SELECT 
        state,
        COUNT(*) AS total_deaths,
        AVG(age) AS avg_age
    FROM covid_deaths
    GROUP BY state
)
SELECT 
    m.month,
    m.monthly_deaths,
    m.avg_age AS monthly_avg_age,
    s.state,
    s.total_deaths AS state_total
FROM monthly_stats m
CROSS JOIN state_stats s
WHERE s.state = 'Selangor'
ORDER BY m.month;
```

### Multiple CTEs for Step-by-Step Analysis

```sql
-- Complex vaccine effectiveness analysis using CTEs
WITH vaccination_status AS (
    SELECT 
        *,
        CASE 
            WHEN date_dose3 IS NOT NULL THEN 'Booster'
            WHEN date_dose2 IS NOT NULL THEN 'Fully Vaccinated'
            WHEN date_dose1 IS NOT NULL THEN 'Partially Vaccinated'
            ELSE 'Unvaccinated'
        END AS vax_status,
        CASE 
            WHEN age < 18 THEN '0-17'
            WHEN age BETWEEN 18 AND 39 THEN '18-39'
            WHEN age BETWEEN 40 AND 59 THEN '40-59'
            WHEN age >= 60 THEN '60+'
        END AS age_group
    FROM covid_deaths
),
age_vax_summary AS (
    SELECT 
        age_group,
        vax_status,
        COUNT(*) AS death_count,
        AVG(CASE WHEN comorb = 1 THEN 100.0 ELSE 0 END) AS pct_with_comorbidity
    FROM vaccination_status
    WHERE age_group IS NOT NULL
    GROUP BY age_group, vax_status
)
SELECT 
    age_group,
    vax_status,
    death_count,
    ROUND(pct_with_comorbidity, 1) AS comorbidity_pct,
    ROUND(100.0 * death_count / SUM(death_count) OVER (PARTITION BY age_group), 2) AS pct_of_age_group
FROM age_vax_summary
ORDER BY age_group, death_count DESC;
```

---

## 2.5 PIVOT and UNPIVOT Operations

### Pivot: Convert rows to columns (R: pivot_wider())

```sql
-- Show vaccination brands as columns with death counts
PIVOT (
    SELECT state, brand1, COUNT(*) AS deaths
    FROM covid_deaths
    WHERE brand1 IS NOT NULL
    GROUP BY state, brand1
)
ON brand1
USING SUM(deaths);

-- Alternative using CASE WHEN (more flexible)
SELECT 
    state,
    COUNT(*) AS total_deaths,
    SUM(CASE WHEN brand1 = 'Pfizer' THEN 1 ELSE 0 END) AS pfizer_deaths,
    SUM(CASE WHEN brand1 = 'Sinovac' THEN 1 ELSE 0 END) AS sinovac_deaths,
    SUM(CASE WHEN brand1 = 'AstraZeneca' THEN 1 ELSE 0 END) AS astrazeneca_deaths,
    SUM(CASE WHEN brand1 = 'Moderna' THEN 1 ELSE 0 END) AS moderna_deaths
FROM covid_deaths
WHERE brand1 IS NOT NULL
GROUP BY state
ORDER BY total_deaths DESC;
```

### Unpivot: Convert columns to rows (R: pivot_longer())

```sql
-- Create a sample wide table first
CREATE TEMP TABLE monthly_deaths AS
SELECT 
    state,
    SUM(CASE WHEN MONTH(date) = 1 THEN 1 ELSE 0 END) AS jan,
    SUM(CASE WHEN MONTH(date) = 2 THEN 1 ELSE 0 END) AS feb,
    SUM(CASE WHEN MONTH(date) = 3 THEN 1 ELSE 0 END) AS mar
FROM covid_deaths
WHERE YEAR(date) = 2021
GROUP BY state;

-- Unpivot it
UNPIVOT monthly_deaths
ON jan, feb, mar
INTO 
    NAME month
    VALUE deaths;
```

---

## 2.6 Advanced Filtering with EXISTS and NOT EXISTS

```sql
-- Find states that had deaths of vaccinated individuals (EXISTS)
SELECT DISTINCT state
FROM covid_deaths d1
WHERE EXISTS (
    SELECT 1 
    FROM covid_deaths d2
    WHERE d2.state = d1.state 
      AND d2.date_dose2 IS NOT NULL
);

-- Find dates with no deaths of elderly (NOT EXISTS)
SELECT DISTINCT date
FROM covid_deaths d1
WHERE NOT EXISTS (
    SELECT 1
    FROM covid_deaths d2
    WHERE d2.date = d1.date
      AND d2.age >= 60
)
ORDER BY date;
```

---

## 2.7 String Pattern Matching and Regular Expressions

```sql
-- Advanced string matching (LIKE, SIMILAR TO, REGEXP)
SELECT *
FROM covid_deaths
WHERE state LIKE 'K%'  -- Starts with K
  AND brand1 NOT LIKE '%Pfizer%';  -- Doesn't contain Pfizer

-- Using regular expressions
SELECT *
FROM covid_deaths
WHERE regexp_matches(state, '^(Johor|Selangor|Penang)$');

-- Extract patterns from strings
SELECT 
    brand1,
    regexp_extract(brand1, '[A-Z][a-z]+', 0) AS brand_clean
FROM covid_deaths
WHERE brand1 IS NOT NULL
LIMIT 10;
```

---

## 2.8 UNION, INTERSECT, and EXCEPT

```sql
-- UNION: Combine results from multiple queries
SELECT state, COUNT(*) AS deaths, 'High Comorbidity' AS category
FROM covid_deaths
WHERE comorb = 1
GROUP BY state
UNION ALL
SELECT state, COUNT(*) AS deaths, 'No Comorbidity' AS category
FROM covid_deaths
WHERE comorb = 0
GROUP BY state;

-- INTERSECT: Find common elements
SELECT state FROM covid_deaths WHERE age > 80
INTERSECT
SELECT state FROM covid_deaths WHERE comorb = 1;

-- EXCEPT: Find differences
SELECT DISTINCT state FROM covid_deaths
EXCEPT
SELECT DISTINCT state FROM covid_deaths WHERE date_dose1 IS NOT NULL;
```

---

## 2.9 Sampling Data

```sql
-- Random sample of 1000 records (R: sample_n(df, 1000))
SELECT * 
FROM covid_deaths 
USING SAMPLE 1000;

-- Random 10% sample (R: sample_frac(df, 0.1))
SELECT * 
FROM covid_deaths 
USING SAMPLE 10 PERCENT (bernoulli);

-- Stratified sampling: 100 records per state
SELECT *
FROM (
    SELECT *, 
           ROW_NUMBER() OVER (PARTITION BY state ORDER BY RANDOM()) AS rn
    FROM covid_deaths
) subq
WHERE rn <= 100;
```

---

## 2.10 Performance Optimization Tips

```sql
-- Use EXPLAIN to understand query performance
EXPLAIN SELECT * FROM covid_deaths WHERE age > 60;

-- Create indexes for frequently filtered columns (persistent tables)
CREATE INDEX idx_age ON covid_deaths(age);
CREATE INDEX idx_state ON covid_deaths(state);
CREATE INDEX idx_date ON covid_deaths(date);

-- Use column projection (select only needed columns)
-- SLOW: SELECT * FROM large_table;
-- FAST: SELECT id, date, age FROM large_table;

-- Filter early in the query
-- SLOW: Large JOIN then filter
-- FAST: Filter first, then JOIN smaller result sets

-- Partition by column for faster filtering
COPY covid_deaths TO 'deaths_partitioned' (PARTITION_BY (state));
```

---

## 🏋️ INTERMEDIATE LEVEL EXERCISES

### Exercise 1: Time Series Analysis
```sql
-- Calculate 14-day rolling average of deaths by state
-- Show date, state, daily deaths, 14-day avg
-- Only for Selangor and Johor
```

### Exercise 2: Vaccine Effectiveness Study
```sql
-- Compare death rates between vaccination statuses
-- Calculate: days from dose2 to death
-- Group into: <14 days, 14-90 days, 91-180 days, >180 days
-- Show: count, avg age, % with comorbidity for each group
```

### Exercise 3: Complex Join with Calculation
```sql
-- Using the state_population table:
-- Calculate monthly death rate per 100,000 population
-- Show: month, state, deaths, population, rate per 100k
-- Only show rates > 5 per 100k
```

### Exercise 4: CTE Chain
```sql
-- Create a multi-step analysis:
-- 1. Create age groups (0-17, 18-39, 40-59, 60+)
-- 2. Create vaccination status categories
-- 3. For each age group and vax status, show:
--    - Total deaths
--    - % of all deaths in that age group
--    - Average days from positive test to death
```

---

# 📚 MODULE 3: ADVANCED LEVEL
## Expert DuckDB Techniques for Health Analytics

### Session Duration: 5 hours

---

## 3.1 Advanced Window Functions

### Complex Moving Calculations

```sql
-- Calculate mortality rate trends with multiple metrics
WITH daily_stats AS (
    SELECT 
        date,
        COUNT(*) AS deaths,
        AVG(age) AS avg_age,
        SUM(CASE WHEN comorb = 1 THEN 1 ELSE 0 END) AS deaths_with_comorb
    FROM covid_deaths
    GROUP BY date
)
SELECT 
    date,
    deaths,
    -- 7-day averages
    AVG(deaths) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS ma7_deaths,
    AVG(avg_age) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS ma7_age,
    -- 30-day averages
    AVG(deaths) OVER (ORDER BY date ROWS BETWEEN 29 PRECEDING AND CURRENT ROW) AS ma30_deaths,
    -- Compare current to 7-day average
    deaths - AVG(deaths) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS deviation_from_ma7,
    -- Standard deviation (volatility)
    STDDEV(deaths) OVER (ORDER BY date ROWS BETWEEN 29 PRECEDING AND CURRENT ROW) AS stddev_30day,
    -- Z-score (how many standard deviations from mean)
    (deaths - AVG(deaths) OVER (ORDER BY date ROWS BETWEEN 29 PRECEDING AND CURRENT ROW)) / 
    STDDEV(deaths) OVER (ORDER BY date ROWS BETWEEN 29 PRECEDING AND CURRENT ROW) AS z_score
FROM daily_stats
ORDER BY date;
```

### Advanced Ranking and Filtering

```sql
-- Find top 3 age groups by deaths in each state
WITH age_state_deaths AS (
    SELECT 
        state,
        CASE 
            WHEN age < 18 THEN '0-17'
            WHEN age BETWEEN 18 AND 39 THEN '18-39'
            WHEN age BETWEEN 40 AND 59 THEN '40-59'
            WHEN age >= 60 THEN '60+'
        END AS age_group,
        COUNT(*) AS deaths
    FROM covid_deaths
    GROUP BY state, age_group
),
ranked AS (
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY state ORDER BY deaths DESC) AS rank_in_state
    FROM age_state_deaths
)
SELECT *
FROM ranked
WHERE rank_in_state <= 3
ORDER BY state, rank_in_state;
```

### Cumulative Distribution Functions

```sql
-- Analyze age distribution using percentiles
SELECT 
    age,
    COUNT(*) AS frequency,
    CUME_DIST() OVER (ORDER BY age) AS cumulative_pct,
    PERCENT_RANK() OVER (ORDER BY age) AS percent_rank,
    NTILE(100) OVER (ORDER BY age) AS percentile
FROM covid_deaths
GROUP BY age
ORDER BY age;
```

---

## 3.2 Complex Aggregations and GROUPING SETS

### Multiple Aggregation Levels Simultaneously

```sql
-- Generate subtotals and grand totals in one query (R: multiple summarise calls)
SELECT 
    state,
    CASE WHEN age >= 60 THEN 'Elderly' ELSE 'Non-Elderly' END AS age_category,
    COUNT(*) AS deaths,
    AVG(age) AS avg_age
FROM covid_deaths
GROUP BY GROUPING SETS (
    (state, age_category),  -- By state and age category
    (state),                -- By state only
    (age_category),         -- By age category only
    ()                      -- Grand total
)
ORDER BY state NULLS LAST, age_category NULLS LAST;

-- Rollup: Hierarchical aggregation
SELECT 
    YEAR(date) AS year,
    MONTH(date) AS month,
    state,
    COUNT(*) AS deaths
FROM covid_deaths
GROUP BY ROLLUP (year, month, state)
ORDER BY year, month, state;

-- Cube: All possible combinations
SELECT 
    state,
    male,
    comorb,
    COUNT(*) AS deaths
FROM covid_deaths
GROUP BY CUBE (state, male, comorb)
ORDER BY state NULLS LAST, male NULLS LAST, comorb NULLS LAST;
```

---

## 3.3 Advanced Statistical Functions

### Correlation and Covariance

```sql
-- Analyze relationship between age and vaccination timing
SELECT 
    CORR(age, CASE WHEN date_dose1 IS NOT NULL THEN 1 ELSE 0 END) AS corr_age_vaccination,
    CORR(age, comorb) AS corr_age_comorbidity,
    COVAR_POP(age, male) AS covar_age_sex
FROM covid_deaths;
```

### Regression Analysis

```sql
-- Linear regression: Predict days from positive to death based on age
SELECT 
    REGR_SLOPE(date - date_positive, age) AS slope,
    REGR_INTERCEPT(date - date_positive, age) AS intercept,
    REGR_R2(date - date_positive, age) AS r_squared,
    REGR_COUNT(date - date_positive, age) AS n_observations
FROM covid_deaths
WHERE date_positive IS NOT NULL 
  AND date - date_positive >= 0;
```

### Mode, Median, and Quantiles

```sql
-- Detailed distribution statistics
SELECT 
    state,
    MODE(age) AS most_common_age,
    MEDIAN(age) AS median_age,
    quantile_cont(age, 0.25) AS q1_age,
    quantile_cont(age, 0.75) AS q3_age,
    quantile_cont(age, 0.75) - quantile_cont(age, 0.25) AS iqr_age,
    APPROX_QUANTILE(age, [0.1, 0.9]) AS age_10_90_percentile
FROM covid_deaths
GROUP BY state;
```

---

## 3.4 Advanced Date/Time Operations

### Date Series Generation

```sql
-- Generate complete date range even for dates with no deaths
WITH date_range AS (
    SELECT generate_series(
        DATE '2020-01-01',
        DATE '2023-12-31',
        INTERVAL '1 day'
    )::DATE AS date
)
SELECT 
    d.date,
    COALESCE(COUNT(c.date), 0) AS deaths
FROM date_range d
LEFT JOIN covid_deaths c ON d.date = c.date
GROUP BY d.date
ORDER BY d.date;
```

### Epidemiological Week Calculations

```sql
-- Calculate epi weeks (important for disease surveillance!)
SELECT 
    date,
    YEARWEEK(date) AS year_week,
    -- ISO week (Monday start)
    EXTRACT(WEEK FROM date) AS iso_week,
    -- CDC/MMWR week calculation
    FLOOR((DAYOFYEAR(date) - DAYOFWEEK(date) + 10) / 7) AS cdc_week,
    COUNT(*) AS deaths
FROM covid_deaths
GROUP BY date, year_week, iso_week, cdc_week
ORDER BY date;
```

### Time Series Gaps and Anomalies

```sql
-- Find gaps in reporting (days with zero deaths - unusual)
WITH all_dates AS (
    SELECT generate_series(
        MIN(date),
        MAX(date),
        INTERVAL '1 day'
    )::DATE AS date
    FROM covid_deaths
),
deaths_per_day AS (
    SELECT date, COUNT(*) AS deaths
    FROM covid_deaths
    GROUP BY date
)
SELECT 
    ad.date,
    COALESCE(d.deaths, 0) AS deaths,
    CASE WHEN d.deaths IS NULL THEN 'No Deaths Reported' ELSE 'Normal' END AS status
FROM all_dates ad
LEFT JOIN deaths_per_day d ON ad.date = d.date
WHERE d.deaths IS NULL OR d.deaths = 0
ORDER BY ad.date;
```

---

## 3.5 Nested Data Structures (Lists and Structs)

### Working with Lists (Arrays)

```sql
-- Aggregate vaccination brands into a list per person
SELECT 
    state,
    age,
    LIST_VALUE(brand1, brand2, brand3) AS all_brands_received
FROM covid_deaths
WHERE brand1 IS NOT NULL
LIMIT 10;

-- Count unique brands used by state
SELECT 
    state,
    COUNT(DISTINCT brand1) AS unique_brands,
    LIST(DISTINCT brand1 ORDER BY brand1) AS brands_list
FROM covid_deaths
WHERE brand1 IS NOT NULL
GROUP BY state;
```

### Creating Structs (Named tuples)

```sql
-- Create structured patient records
SELECT 
    {
        'id': ROW_NUMBER() OVER (),
        'demographics': {
            'age': age,
            'sex': CASE WHEN male = 1 THEN 'Male' ELSE 'Female' END,
            'state': state
        },
        'clinical': {
            'comorbidity': comorb = 1,
            'bid': bid = 1
        },
        'vaccination': {
            'brand': brand1,
            'fully_vaccinated': date_dose2 IS NOT NULL
        }
    } AS patient_record
FROM covid_deaths
LIMIT 10;
```

---

## 3.6 User-Defined Functions (UDFs) and Macros

### Creating Macros (Reusable expressions)

```sql
-- Define age group macro
CREATE MACRO age_group(age) AS (
    CASE 
        WHEN age < 18 THEN '0-17'
        WHEN age BETWEEN 18 AND 39 THEN '18-39'
        WHEN age BETWEEN 40 AND 59 THEN '40-59'
        WHEN age >= 60 THEN '60+'
        ELSE 'Unknown'
    END
);

-- Use the macro
SELECT 
    age_group(age) AS age_category,
    COUNT(*) AS deaths
FROM covid_deaths
GROUP BY age_category;

-- Vaccination status macro
CREATE MACRO vaccination_status(dose1, dose2, dose3) AS (
    CASE 
        WHEN dose3 IS NOT NULL THEN 'Booster'
        WHEN dose2 IS NOT NULL THEN 'Fully Vaccinated'
        WHEN dose1 IS NOT NULL THEN 'Partially Vaccinated'
        ELSE 'Unvaccinated'
    END
);

SELECT 
    vaccination_status(date_dose1, date_dose2, date_dose3) AS vax_status,
    COUNT(*) AS count
FROM covid_deaths
GROUP BY vax_status;
```

### Python UDFs (Advanced analytics)

```sql
-- Create Python UDF for custom calculation
CREATE FUNCTION calculate_risk_score(age INTEGER, comorb INTEGER, vaccinated INTEGER)
RETURNS DOUBLE
LANGUAGE PYTHON AS $$
def calculate_risk_score(age, comorb, vaccinated):
    score = 0.0
    if age > 60:
        score += 2.0
    elif age > 40:
        score += 1.0
    
    if comorb == 1:
        score += 1.5
    
    if vaccinated == 0:
        score += 2.0
    
    return score
$$;

-- Use the custom function
SELECT 
    state,
    AVG(calculate_risk_score(
        age, 
        comorb, 
        CASE WHEN date_dose2 IS NOT NULL THEN 1 ELSE 0 END
    )) AS avg_risk_score
FROM covid_deaths
GROUP BY state
ORDER BY avg_risk_score DESC;
```

---

## 3.7 Handling Very Large Datasets Efficiently

### Partitioned Reads

```sql
-- Read only specific partitions (if data is partitioned)
SELECT *
FROM read_csv('deaths_partitioned/state=*/year=2021/*.csv',
    hive_partitioning = true)
WHERE state IN ('Selangor', 'Johor');
```

### Parallel Processing

```sql
-- DuckDB automatically parallelizes, but you can control:
SET threads TO 8;  -- Use 8 CPU cores

-- Process in chunks for memory efficiency
CREATE TEMP TABLE results AS
SELECT * FROM covid_deaths WHERE age > 60;

-- Export large results incrementally
COPY (SELECT * FROM large_table) 
TO 'output' (FORMAT PARQUET, PARTITION_BY (state, year));
```

### Memory Management

```sql
-- Set memory limit
SET memory_limit = '4GB';

-- Use streaming for very large aggregations
SET enable_object_cache = false;
SET temp_directory = '/path/to/temp';
```

---

## 3.8 Advanced Join Patterns

### Lateral Joins (Correlated subqueries)

```sql
-- For each state, get 5 most recent deaths
SELECT 
    s.state,
    recent.*
FROM (SELECT DISTINCT state FROM covid_deaths) s
CROSS JOIN LATERAL (
    SELECT date, age, male
    FROM covid_deaths
    WHERE state = s.state
    ORDER BY date DESC
    LIMIT 5
) recent;
```

### Inequality Joins (Range joins)

```sql
-- Find deaths that occurred within 30 days of each other in same state
SELECT 
    d1.date AS date1,
    d2.date AS date2,
    d1.state,
    d2.date - d1.date AS days_apart
FROM covid_deaths d1
JOIN covid_deaths d2 
    ON d1.state = d2.state
    AND d2.date > d1.date
    AND d2.date <= d1.date + INTERVAL '30 days'
WHERE d1.state = 'Selangor'
LIMIT 100;
```

---

## 3.9 Advanced Text Analytics

### Full Text Search

```sql
-- Search across multiple text fields (if you have text data)
SELECT *
FROM covid_deaths
WHERE state LIKE '%Kuala%'
   OR brand1 LIKE '%Pfizer%'
UNION
SELECT *
FROM covid_deaths
WHERE state LIKE '%Selangor%';
```

### Fuzzy Matching

```sql
-- Levenshtein distance for fuzzy matching (data cleaning)
SELECT 
    state,
    levenshtein(state, 'Selangor') AS distance,
    jaro_winkler_similarity(state, 'Selangor') AS similarity
FROM (SELECT DISTINCT state FROM covid_deaths)
WHERE levenshtein(state, 'Selangor') < 3
ORDER BY distance;
```

---

## 3.10 Data Quality and Validation

### Comprehensive Data Quality Checks

```sql
-- Create data quality report
WITH quality_checks AS (
    SELECT 
        'Total Records' AS check_name,
        COUNT(*)::VARCHAR AS result,
        'Count' AS metric_type
    FROM covid_deaths
    
    UNION ALL
    
    SELECT 
        'Duplicate Dates per Person',
        COUNT(*)::VARCHAR,
        'Count'
    FROM (
        SELECT date, age, male, state, COUNT(*) AS cnt
        FROM covid_deaths
        GROUP BY date, age, male, state
        HAVING COUNT(*) > 1
    )
    
    UNION ALL
    
    SELECT 
        'Missing Age Values',
        COUNT(*)::VARCHAR,
        'Count'
    FROM covid_deaths
    WHERE age IS NULL
    
    UNION ALL
    
    SELECT 
        'Invalid Age Values (<0 or >120)',
        COUNT(*)::VARCHAR,
        'Count'
    FROM covid_deaths
    WHERE age < 0 OR age > 120
    
    UNION ALL
    
    SELECT 
        'Future Death Dates',
        COUNT(*)::VARCHAR,
        'Count'
    FROM covid_deaths
    WHERE date > CURRENT_DATE
    
    UNION ALL
    
    SELECT 
        'Death before Positive Test',
        COUNT(*)::VARCHAR,
        'Count'
    FROM covid_deaths
    WHERE date < date_positive
    
    UNION ALL
    
    SELECT 
        'Dose 2 before Dose 1',
        COUNT(*)::VARCHAR,
        'Count'
    FROM covid_deaths
    WHERE date_dose2 < date_dose1
    
    UNION ALL
    
    SELECT 
        'Avg Age',
        ROUND(AVG(age), 2)::VARCHAR,
        'Average'
    FROM covid_deaths
    
    UNION ALL
    
    SELECT 
        'Vaccination Rate',
        ROUND(100.0 * COUNT(CASE WHEN date_dose1 IS NOT NULL THEN 1 END) / COUNT(*), 2)::VARCHAR || '%',
        'Percentage'
    FROM covid_deaths
)
SELECT * FROM quality_checks;
```

### Outlier Detection

```sql
-- Detect statistical outliers using IQR method
WITH age_stats AS (
    SELECT 
        quantile_cont(age, 0.25) AS q1,
        quantile_cont(age, 0.75) AS q3,
        quantile_cont(age, 0.75) - quantile_cont(age, 0.25) AS iqr
    FROM covid_deaths
)
SELECT 
    d.*,
    'Outlier' AS flag
FROM covid_deaths d, age_stats s
WHERE d.age < (s.q1 - 1.5 * s.iqr)
   OR d.age > (s.q3 + 1.5 * s.iqr);
```

---

## 3.11 Building Production-Ready Analytical Pipelines

### Complete ETL Pipeline Example

```sql
-- STEP 1: Extract (already done - reading CSV)
CREATE TEMP TABLE raw_deaths AS
SELECT * FROM 'https://storage.data.gov.my/healthcare/covid_deaths_linelist.csv';

-- STEP 2: Transform
CREATE TABLE cleaned_deaths AS
WITH validated_data AS (
    SELECT 
        *,
        -- Fix invalid dates
        CASE 
            WHEN date > CURRENT_DATE THEN NULL
            WHEN date < DATE '2020-01-01' THEN NULL
            ELSE date
        END AS validated_date,
        -- Fix invalid ages
        CASE 
            WHEN age < 0 OR age > 120 THEN NULL
            ELSE age
        END AS validated_age,
        -- Create age groups
        CASE 
            WHEN age < 18 THEN '0-17'
            WHEN age BETWEEN 18 AND 39 THEN '18-39'
            WHEN age BETWEEN 40 AND 59 THEN '40-59'
            WHEN age >= 60 THEN '60+'
        END AS age_group,
        -- Vaccination status
        CASE 
            WHEN date_dose3 IS NOT NULL THEN 'Booster'
            WHEN date_dose2 IS NOT NULL THEN 'Fully Vaccinated'
            WHEN date_dose1 IS NOT NULL THEN 'Partially Vaccinated'
            ELSE 'Unvaccinated'
        END AS vaccination_status,
        -- Time metrics
        date - date_positive AS days_positive_to_death,
        date - date_dose2 AS days_since_full_vaccination
    FROM raw_deaths
    WHERE age IS NOT NULL
      AND state IS NOT NULL
)
SELECT * FROM validated_data;

-- STEP 3: Create aggregated tables for reporting
CREATE TABLE daily_deaths_summary AS
SELECT 
    date,
    state,
    COUNT(*) AS deaths,
    AVG(age) AS avg_age,
    SUM(CASE WHEN male = 1 THEN 1 ELSE 0 END) AS male_deaths,
    SUM(CASE WHEN comorb = 1 THEN 1 ELSE 0 END) AS deaths_with_comorbidity,
    SUM(CASE WHEN vaccination_status = 'Unvaccinated' THEN 1 ELSE 0 END) AS unvaccinated_deaths
FROM cleaned_deaths
GROUP BY date, state;

-- STEP 4: Export for reporting tools
COPY daily_deaths_summary TO 'daily_summary.csv' (HEADER);
COPY cleaned_deaths TO 'cleaned_deaths.parquet' (FORMAT PARQUET);

-- STEP 5: Create views for common queries
CREATE VIEW high_risk_deaths AS
SELECT *
FROM cleaned_deaths
WHERE age >= 60 AND comorb = 1;

CREATE VIEW recent_deaths AS
SELECT *
FROM cleaned_deaths
WHERE date >= CURRENT_DATE - INTERVAL '30 days';
```

---

## 3.12 Integration with R (If needed)

### Using DuckDB from R

```r
# R code example (not SQL, but showing integration)
library(duckdb)

# Connect to DuckDB
con <- dbConnect(duckdb::duckdb(), dbdir = "covid_analysis.duckdb")

# Run SQL query from R
result <- dbGetQuery(con, "
    SELECT 
        state, 
        COUNT(*) AS deaths,
        AVG(age) AS avg_age
    FROM 'https://storage.data.gov.my/healthcare/covid_deaths_linelist.csv'
    GROUP BY state
    ORDER BY deaths DESC
")

# Use result as R dataframe
print(result)
head(result)

# Run complex DuckDB query and use results in R
deaths_df <- dbGetQuery(con, "
    WITH monthly_stats AS (
        SELECT 
            date_trunc('month', date) AS month,
            state,
            COUNT(*) AS deaths
        FROM covid_deaths
        GROUP BY month, state
    )
    SELECT * FROM monthly_stats
    ORDER BY month, state
")

# Now use R for visualization
library(ggplot2)
ggplot(deaths_df, aes(x = month, y = deaths, color = state)) +
    geom_line() +
    theme_minimal()

# Disconnect
dbDisconnect(con)
```

---

## 🏋️ ADVANCED LEVEL EXERCISES

### Exercise 1: Outbreak Detection
```sql
-- Implement a statistical algorithm to detect outbreak periods
-- Calculate 30-day moving average and standard deviation
-- Flag dates where deaths > mean + 2*SD as "outbreak"
-- Show: date, deaths, moving_avg, moving_sd, z_score, outbreak_flag
```

### Exercise 2: Vaccine Effectiveness Analysis
```sql
-- Multi-level analysis comparing outcomes by:
-- 1. Vaccination status (unvaxxed, partial, full, booster)
-- 2. Age group
-- 3. Time since last dose (<14d, 14-90d, 91-180d, >180d)
-- Calculate:
-- - Death counts
-- - Average age
-- - % with comorbidity
-- - Days from positive to death
-- Use CTEs, window functions, and proper statistical methods
```

### Exercise 3: Cohort Analysis
```sql
-- Create vaccination cohorts (grouped by month of dose 2)
-- Track deaths over time since vaccination
-- Create a "survival" table showing:
-- - Cohort (vaccination month)
-- - Months since vaccination (0, 1, 2, 3, 4, 5, 6+)
-- - Deaths in that period
-- - Cumulative deaths
-- - Death rate per 1000 vaccinated (need to estimate denominator)
```

### Exercise 4: Complete Data Pipeline
```sql
-- Build a production pipeline that:
-- 1. Reads raw CSV
-- 2. Validates all fields
-- 3. Creates 5+ derived variables
-- 4. Generates 3+ summary tables for different stakeholders:
--    - Executive dashboard (high-level KPIs)
--    - Epidemiologist table (detailed statistics)
--    - State health department table (state-specific metrics)
-- 5. Exports to appropriate formats
-- Include quality checks and error handling
```

---

## 📊 Performance Comparison: DuckDB vs R

### Benchmark Examples

```sql
-- DuckDB: Calculate state-level statistics
-- Typical time: < 1 second for millions of rows

SELECT 
    state,
    COUNT(*) AS deaths,
    AVG(age) AS avg_age,
    STDDEV(age) AS sd_age,
    MEDIAN(age) AS median_age,
    quantile_cont(age, 0.25) AS q1,
    quantile_cont(age, 0.75) AS q3
FROM covid_deaths
GROUP BY state;
```

**Equivalent R code (much slower):**
```r
# R: Same calculation
# Typical time: 5-20 seconds for millions of rows

library(dplyr)
result <- covid_deaths %>%
    group_by(state) %>%
    summarise(
        deaths = n(),
        avg_age = mean(age, na.rm = TRUE),
        sd_age = sd(age, na.rm = TRUE),
        median_age = median(age, na.rm = TRUE),
        q1 = quantile(age, 0.25, na.rm = TRUE),
        q3 = quantile(age, 0.75, na.rm = TRUE)
    )
```

### Why DuckDB is Faster:

1. **Columnar storage**: Only reads relevant columns
2. **Vectorized execution**: Processes thousands of rows at once
3. **Parallel processing**: Automatically uses all CPU cores
4. **Query optimization**: Intelligent query planning
5. **No copying overhead**: Direct file access without loading to memory

---

## 🎓 Best Practices for Health Informatics

### 1. Documentation and Reproducibility
```sql
-- Always comment your queries
-- Use meaningful variable names
-- Create views for complex logic

-- GOOD:
CREATE VIEW elderly_unvaccinated_deaths AS
SELECT * 
FROM covid_deaths
WHERE age >= 60 
  AND date_dose1 IS NULL;

-- BAD:
CREATE VIEW v1 AS SELECT * FROM d WHERE a >= 60 AND x IS NULL;
```

### 2. Data Privacy and Security
```sql
-- Anonymize age in public reports (age groups instead of exact age)
SELECT 
    age_group(age) AS age_category,
    state,
    COUNT(*) AS deaths
FROM covid_deaths
GROUP BY age_category, state;

-- Never export individual-level identifiable data
-- Use aggregated summaries instead
```

### 3. Validation and Quality Checks
```sql
-- Always validate results
-- Check for:
-- - Missing values
-- - Duplicates
-- - Outliers
-- - Logical inconsistencies (death before birth, etc.)

-- Example validation query
SELECT 
    'Invalid age' AS issue,
    COUNT(*) AS records
FROM covid_deaths
WHERE age < 0 OR age > 120
UNION ALL
SELECT 
    'Death before positive test' AS issue,
    COUNT(*) AS records
FROM covid_deaths
WHERE date < date_positive;
```

### 4. Version Control for Queries
```sql
-- Save queries with versioning
-- Example filename: analysis_v2_20250119.sql

-- Header comments should include:
-- Author: [Your name]
-- Date: 2025-01-19
-- Purpose: Calculate state-level vaccination effectiveness
-- Changes from v1: Added age stratification
```

---

## 🎯 Summary: DuckDB Superiority for Health Data

### When to Use DuckDB:

✅ Large datasets (>1GB)  
✅ Complex aggregations and groupings  
✅ Time series analysis  
✅ Multiple data sources (CSV, Parquet, JSON)  
✅ Need for reproducible SQL scripts  
✅ Collaboration with data engineers  
✅ Production data pipelines  
✅ Fast prototyping and exploration  

### When R Might Be Better:

📊 Statistical modeling (GLM, survival analysis)  
📊 Advanced visualizations (ggplot2)  
📊 Machine learning  
📊 Small datasets that fit in memory  
📊 Interactive data exploration (RStudio)  

### Best Approach: **Use Both!**

```r
# Use DuckDB for data wrangling (fast)
con <- dbConnect(duckdb())
clean_data <- dbGetQuery(con, "
    SELECT 
        state,
        age_group(age) AS age_grp,
        vaccination_status(date_dose1, date_dose2, date_dose3) AS vax_status,
        COUNT(*) AS deaths
    FROM covid_deaths
    GROUP BY state, age_grp, vax_status
")

# Use R for statistical analysis and visualization
model <- glm(deaths ~ age_grp + vax_status, data = clean_data, family = poisson)
summary(model)
```

---

## 📚 Additional Resources

### DuckDB Documentation
- Official Docs: https://duckdb.org/docs/
- SQL Functions: https://duckdb.org/docs/sql/functions/overview
- Data Import: https://duckdb.org/docs/data/overview

### Health Informatics Resources
- KKMNow Data Catalogue: https://data.moh.gov.my/data-catalogue
- WHO Data Standards: https://www.who.int/data
- CDC Data Analytics: https://www.cdc.gov/surveillance

### SQL Learning Resources
- Mode Analytics SQL Tutorial
- SQLZoo Interactive Tutorials
- LeetCode SQL Practice Problems

---

## 🎉 Congratulations!

You now have comprehensive knowledge of:
- ✅ Basic SQL operations in DuckDB
- ✅ Intermediate data wrangling techniques
- ✅ Advanced analytics and optimization
- ✅ Production-ready data pipelines
- ✅ Integration with R workflows

**Next Steps:**
1. Practice with the KKMNow dataset
2. Build your own analytical queries
3. Create reusable analysis templates
4. Share knowledge with colleagues
5. Contribute to MOH data analytics initiatives

---

**Training Module Prepared by:**
National Institute of Health Malaysia

**Date:** January 2025  
**Version:** 1.0

---
