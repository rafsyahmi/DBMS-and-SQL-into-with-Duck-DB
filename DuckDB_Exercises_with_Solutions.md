# DuckDB SQL Practical Exercises
## COVID-19 Deaths Dataset - KKMNow
### National Institute of Health Malaysia

---

## 📋 Dataset Information

**Dataset:** COVID-19 Deaths Linelist  
**Source:** https://storage.data.gov.my/healthcare/covid_deaths_linelist.csv  
**Columns:**
- date (Date of death)
- date_announced, date_positive (Dates)
- date_dose1, date_dose2, date_dose3 (Vaccination dates)
- brand1, brand2, brand3 (Vaccine brands)
- state (16 Malaysian states)
- age (Age at death)
- male (1=Male, 0=Female)
- bid (1=Brought in dead, 0=Died in hospital)
- malaysian (1=Malaysian, 0=Non-Malaysian)
- comorb (1=Has comorbidity, 0=Healthy)

---

# 🟢 BASIC LEVEL EXERCISES

## Exercise 1: Data Exploration

**Task:** Get familiar with the dataset structure and basic statistics.

### Question 1.1: View first 20 records
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT * 
FROM 'https://storage.data.gov.my/healthcare/covid_deaths_linelist.csv'
LIMIT 20;

-- Alternative: Create a view first
CREATE VIEW covid_deaths AS
SELECT * FROM 'https://storage.data.gov.my/healthcare/covid_deaths_linelist.csv';

SELECT * FROM covid_deaths LIMIT 20;
```
</details>

---

### Question 1.2: Count total number of deaths
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT COUNT(*) AS total_deaths
FROM covid_deaths;
```
</details>

---

### Question 1.3: Find the age range (minimum and maximum age)
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    MIN(age) AS youngest_age,
    MAX(age) AS oldest_age,
    MAX(age) - MIN(age) AS age_range
FROM covid_deaths;
```
</details>

---

## Exercise 2: Filtering Data

### Question 2.1: Find all deaths in Selangor
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT *
FROM covid_deaths
WHERE state = 'Selangor';
```
</details>

---

### Question 2.2: Find deaths of males aged 60 or above with comorbidity
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT *
FROM covid_deaths
WHERE age >= 60
  AND male = 1
  AND comorb = 1;
```
</details>

---

### Question 2.3: Find unvaccinated deaths (no first dose) in 2021
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT *
FROM covid_deaths
WHERE date_dose1 IS NULL
  AND YEAR(date) = 2021;

-- Alternative with BETWEEN:
SELECT *
FROM covid_deaths
WHERE date_dose1 IS NULL
  AND date BETWEEN '2021-01-01' AND '2021-12-31';
```
</details>

---

### Question 2.4: Find deaths in Kuala Lumpur, Selangor, or Johor
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT *
FROM covid_deaths
WHERE state IN ('Kuala Lumpur', 'Selangor', 'Johor');
```
</details>

---

## Exercise 3: Sorting and Limiting

### Question 3.1: Find the 10 oldest people who died
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT *
FROM covid_deaths
ORDER BY age DESC
LIMIT 10;
```
</details>

---

### Question 3.2: Show the most recent 20 deaths, sorted by date (newest first)
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT *
FROM covid_deaths
ORDER BY date DESC
LIMIT 20;
```
</details>

---

## Exercise 4: Basic Aggregation

### Question 4.1: Count deaths by state and sort by highest deaths first
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    state,
    COUNT(*) AS death_count
FROM covid_deaths
GROUP BY state
ORDER BY death_count DESC;
```
</details>

---

### Question 4.2: Calculate average age by gender
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    CASE WHEN male = 1 THEN 'Male' ELSE 'Female' END AS gender,
    COUNT(*) AS deaths,
    ROUND(AVG(age), 1) AS avg_age,
    ROUND(MEDIAN(age), 1) AS median_age
FROM covid_deaths
GROUP BY male;
```
</details>

---

### Question 4.3: Find states with more than 5,000 deaths
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    state,
    COUNT(*) AS death_count
FROM covid_deaths
GROUP BY state
HAVING COUNT(*) > 5000
ORDER BY death_count DESC;
```
</details>

---

## Exercise 5: Creating New Columns

### Question 5.1: Create age groups and count deaths in each group
```sql
-- Age groups: 0-17, 18-39, 40-59, 60+
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    CASE 
        WHEN age < 18 THEN '0-17'
        WHEN age BETWEEN 18 AND 39 THEN '18-39'
        WHEN age BETWEEN 40 AND 59 THEN '40-59'
        WHEN age >= 60 THEN '60+'
    END AS age_group,
    COUNT(*) AS deaths
FROM covid_deaths
GROUP BY age_group
ORDER BY age_group;
```
</details>

---

### Question 5.2: Create vaccination status and count each category
```sql
-- Categories: Unvaccinated, Partially Vaccinated, Fully Vaccinated, Booster
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    CASE 
        WHEN date_dose3 IS NOT NULL THEN 'Booster'
        WHEN date_dose2 IS NOT NULL THEN 'Fully Vaccinated'
        WHEN date_dose1 IS NOT NULL THEN 'Partially Vaccinated'
        ELSE 'Unvaccinated'
    END AS vaccination_status,
    COUNT(*) AS deaths,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS percentage
FROM covid_deaths
GROUP BY vaccination_status
ORDER BY deaths DESC;
```
</details>

---

### Question 5.3: Calculate days from positive test to death
```sql
-- Only for records with both dates available
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    date,
    date_positive,
    age,
    state,
    date - date_positive AS days_positive_to_death
FROM covid_deaths
WHERE date_positive IS NOT NULL
  AND date - date_positive >= 0
ORDER BY days_positive_to_death DESC
LIMIT 20;
```
</details>

---

# 🟡 INTERMEDIATE LEVEL EXERCISES

## Exercise 6: Date Operations

### Question 6.1: Calculate monthly death counts for 2021
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    date_trunc('month', date) AS month,
    COUNT(*) AS deaths
FROM covid_deaths
WHERE YEAR(date) = 2021
GROUP BY month
ORDER BY month;

-- Alternative with explicit month names:
SELECT 
    YEAR(date) AS year,
    MONTH(date) AS month_num,
    MONTHNAME(date) AS month_name,
    COUNT(*) AS deaths
FROM covid_deaths
WHERE YEAR(date) = 2021
GROUP BY year, month_num, month_name
ORDER BY month_num;
```
</details>

---

### Question 6.2: Find the deadliest day of the week
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    DAYOFWEEK(date) AS day_num,
    DAYNAME(date) AS day_name,
    COUNT(*) AS deaths
FROM covid_deaths
GROUP BY day_num, day_name
ORDER BY deaths DESC;
```
</details>

---

### Question 6.3: Calculate quarterly deaths by state for 2021
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    state,
    'Q' || QUARTER(date) AS quarter,
    COUNT(*) AS deaths
FROM covid_deaths
WHERE YEAR(date) = 2021
GROUP BY state, quarter
ORDER BY state, quarter;
```
</details>

---

## Exercise 7: Window Functions

### Question 7.1: Calculate 7-day moving average of daily deaths
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH daily_deaths AS (
    SELECT 
        date,
        COUNT(*) AS deaths
    FROM covid_deaths
    GROUP BY date
)
SELECT 
    date,
    deaths,
    ROUND(AVG(deaths) OVER (
        ORDER BY date 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ), 2) AS ma7_deaths
FROM daily_deaths
ORDER BY date;
```
</details>

---

### Question 7.2: Calculate cumulative deaths over time
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH daily_deaths AS (
    SELECT 
        date,
        COUNT(*) AS daily_deaths
    FROM covid_deaths
    GROUP BY date
)
SELECT 
    date,
    daily_deaths,
    SUM(daily_deaths) OVER (ORDER BY date) AS cumulative_deaths
FROM daily_deaths
ORDER BY date;
```
</details>

---

### Question 7.3: Rank states by death count and show top 5
```sql
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH state_deaths AS (
    SELECT 
        state,
        COUNT(*) AS deaths
    FROM covid_deaths
    GROUP BY state
)
SELECT 
    state,
    deaths,
    RANK() OVER (ORDER BY deaths DESC) AS rank
FROM state_deaths
ORDER BY rank
LIMIT 5;
```
</details>

---

### Question 7.4: Compare daily deaths with previous day
```sql
-- Show date, deaths, previous day deaths, and change
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH daily_deaths AS (
    SELECT 
        date,
        COUNT(*) AS deaths
    FROM covid_deaths
    GROUP BY date
)
SELECT 
    date,
    deaths,
    LAG(deaths, 1) OVER (ORDER BY date) AS prev_day_deaths,
    deaths - LAG(deaths, 1) OVER (ORDER BY date) AS change_from_prev_day,
    ROUND(100.0 * (deaths - LAG(deaths, 1) OVER (ORDER BY date)) / 
          LAG(deaths, 1) OVER (ORDER BY date), 2) AS pct_change
FROM daily_deaths
ORDER BY date;
```
</details>

---

## Exercise 8: Joins

### Question 8.1: Create a state population table and calculate death rates
```sql
-- First create population reference, then calculate deaths per 100k
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
-- Create population reference table
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

-- Calculate death rates
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
</details>

---

## Exercise 9: CTEs (Common Table Expressions)

### Question 9.1: Multi-level analysis using CTEs
```sql
-- Create CTEs for:
-- 1. Age groups with death counts
-- 2. Vaccination status with death counts  
-- 3. Combine both to show deaths by age group and vax status
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH age_classification AS (
    SELECT 
        *,
        CASE 
            WHEN age < 18 THEN '0-17'
            WHEN age BETWEEN 18 AND 39 THEN '18-39'
            WHEN age BETWEEN 40 AND 59 THEN '40-59'
            WHEN age >= 60 THEN '60+'
        END AS age_group
    FROM covid_deaths
),
vax_classification AS (
    SELECT 
        *,
        CASE 
            WHEN date_dose3 IS NOT NULL THEN 'Booster'
            WHEN date_dose2 IS NOT NULL THEN 'Fully Vaccinated'
            WHEN date_dose1 IS NOT NULL THEN 'Partially Vaccinated'
            ELSE 'Unvaccinated'
        END AS vax_status
    FROM age_classification
)
SELECT 
    age_group,
    vax_status,
    COUNT(*) AS deaths,
    ROUND(AVG(CASE WHEN comorb = 1 THEN 100.0 ELSE 0 END), 1) AS pct_with_comorbidity,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (PARTITION BY age_group), 2) AS pct_of_age_group
FROM vax_classification
WHERE age_group IS NOT NULL
GROUP BY age_group, vax_status
ORDER BY age_group, deaths DESC;
```
</details>

---

### Question 9.2: Find states with above-average death rates
```sql
-- Use CTEs to:
-- 1. Calculate death count per state
-- 2. Calculate overall average
-- 3. Filter states above average
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH state_deaths AS (
    SELECT 
        state,
        COUNT(*) AS deaths
    FROM covid_deaths
    GROUP BY state
),
avg_deaths AS (
    SELECT AVG(deaths) AS avg_death_count
    FROM state_deaths
)
SELECT 
    sd.state,
    sd.deaths,
    ad.avg_death_count,
    ROUND(sd.deaths - ad.avg_death_count, 0) AS above_average_by
FROM state_deaths sd, avg_deaths ad
WHERE sd.deaths > ad.avg_death_count
ORDER BY sd.deaths DESC;
```
</details>

---

## Exercise 10: Pivot Operations

### Question 10.1: Create a pivot table of vaccine brands by state
```sql
-- Show states as rows, vaccine brands as columns with death counts
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
SELECT 
    state,
    COUNT(*) AS total_deaths,
    SUM(CASE WHEN brand1 = 'Pfizer' THEN 1 ELSE 0 END) AS pfizer,
    SUM(CASE WHEN brand1 = 'Sinovac' THEN 1 ELSE 0 END) AS sinovac,
    SUM(CASE WHEN brand1 = 'AstraZeneca' THEN 1 ELSE 0 END) AS astrazeneca,
    SUM(CASE WHEN brand1 = 'Moderna' THEN 1 ELSE 0 END) AS moderna,
    SUM(CASE WHEN brand1 = 'Cansino' THEN 1 ELSE 0 END) AS cansino,
    SUM(CASE WHEN brand1 IS NULL THEN 1 ELSE 0 END) AS unvaccinated
FROM covid_deaths
GROUP BY state
ORDER BY total_deaths DESC;
```
</details>

---

# 🔴 ADVANCED LEVEL EXERCISES

## Exercise 11: Complex Time Series Analysis

### Question 11.1: Detect outbreak periods using statistical methods
```sql
-- Calculate:
-- - 30-day moving average
-- - 30-day standard deviation
-- - Z-score for each day
-- - Flag days where deaths > mean + 2*SD as "outbreak"
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH daily_deaths AS (
    SELECT 
        date,
        COUNT(*) AS deaths
    FROM covid_deaths
    GROUP BY date
),
with_stats AS (
    SELECT 
        date,
        deaths,
        AVG(deaths) OVER (
            ORDER BY date 
            ROWS BETWEEN 29 PRECEDING AND CURRENT ROW
        ) AS ma30,
        STDDEV(deaths) OVER (
            ORDER BY date 
            ROWS BETWEEN 29 PRECEDING AND CURRENT ROW
        ) AS sd30
    FROM daily_deaths
)
SELECT 
    date,
    deaths,
    ROUND(ma30, 2) AS moving_avg_30d,
    ROUND(sd30, 2) AS std_dev_30d,
    ROUND((deaths - ma30) / NULLIF(sd30, 0), 2) AS z_score,
    CASE 
        WHEN deaths > (ma30 + 2 * sd30) THEN 'OUTBREAK'
        WHEN deaths > (ma30 + sd30) THEN 'Elevated'
        ELSE 'Normal'
    END AS alert_level
FROM with_stats
WHERE sd30 IS NOT NULL
ORDER BY date;
```
</details>

---

### Question 11.2: Week-over-week percentage change analysis
```sql
-- Calculate weekly deaths and compare with previous week
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH weekly_deaths AS (
    SELECT 
        date_trunc('week', date) AS week_start,
        COUNT(*) AS deaths
    FROM covid_deaths
    GROUP BY week_start
)
SELECT 
    week_start,
    deaths,
    LAG(deaths, 1) OVER (ORDER BY week_start) AS prev_week_deaths,
    deaths - LAG(deaths, 1) OVER (ORDER BY week_start) AS absolute_change,
    ROUND(
        100.0 * (deaths - LAG(deaths, 1) OVER (ORDER BY week_start)) / 
        NULLIF(LAG(deaths, 1) OVER (ORDER BY week_start), 0),
        2
    ) AS pct_change
FROM weekly_deaths
ORDER BY week_start;
```
</details>

---

## Exercise 12: Vaccine Effectiveness Analysis

### Question 12.1: Compare outcomes by vaccination status and time since vaccination
```sql
-- For fully vaccinated individuals, create time periods since dose 2:
-- <14 days, 14-90 days, 91-180 days, >180 days
-- Calculate: count, avg age, % with comorbidity, avg days positive to death
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH vaccinated_deaths AS (
    SELECT 
        *,
        date - date_dose2 AS days_since_dose2,
        date - date_positive AS days_positive_to_death,
        CASE 
            WHEN date_dose2 IS NULL THEN 'Unvaccinated'
            WHEN date - date_dose2 < 14 THEN '<14 days'
            WHEN date - date_dose2 BETWEEN 14 AND 90 THEN '14-90 days'
            WHEN date - date_dose2 BETWEEN 91 AND 180 THEN '91-180 days'
            WHEN date - date_dose2 > 180 THEN '>180 days'
        END AS time_since_vaccination
    FROM covid_deaths
    WHERE date_positive IS NOT NULL
)
SELECT 
    time_since_vaccination,
    COUNT(*) AS deaths,
    ROUND(AVG(age), 1) AS avg_age,
    ROUND(AVG(CASE WHEN comorb = 1 THEN 100.0 ELSE 0 END), 1) AS pct_with_comorbidity,
    ROUND(AVG(days_positive_to_death), 1) AS avg_days_positive_to_death,
    ROUND(MEDIAN(days_positive_to_death), 1) AS median_days_positive_to_death
FROM vaccinated_deaths
WHERE time_since_vaccination IS NOT NULL
GROUP BY time_since_vaccination
ORDER BY 
    CASE time_since_vaccination
        WHEN 'Unvaccinated' THEN 1
        WHEN '<14 days' THEN 2
        WHEN '14-90 days' THEN 3
        WHEN '91-180 days' THEN 4
        WHEN '>180 days' THEN 5
    END;
```
</details>

---

### Question 12.2: Vaccine brand comparison by age group
```sql
-- For each age group (0-17, 18-39, 40-59, 60+):
-- Compare death counts across different vaccine brands
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH classified AS (
    SELECT 
        CASE 
            WHEN age < 18 THEN '0-17'
            WHEN age BETWEEN 18 AND 39 THEN '18-39'
            WHEN age BETWEEN 40 AND 59 THEN '40-59'
            WHEN age >= 60 THEN '60+'
        END AS age_group,
        COALESCE(brand1, 'Unvaccinated') AS vaccine_brand
    FROM covid_deaths
    WHERE age IS NOT NULL
)
SELECT 
    age_group,
    vaccine_brand,
    COUNT(*) AS deaths,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (PARTITION BY age_group), 2) AS pct_in_age_group
FROM classified
GROUP BY age_group, vaccine_brand
ORDER BY age_group, deaths DESC;
```
</details>

---

## Exercise 13: Data Quality and Validation

### Question 13.1: Comprehensive data quality report
```sql
-- Check for:
-- - Total records
-- - Missing values in key columns
-- - Invalid dates (future dates, death before positive test)
-- - Invalid ages (<0, >120)
-- - Logical inconsistencies (dose2 before dose1)
-- Write your query here:
```

<details>
<summary>💡 Solution</summary>

```sql
WITH quality_metrics AS (
    SELECT 
        COUNT(*) AS total_records,
        COUNT(*) - COUNT(age) AS missing_age,
        COUNT(*) - COUNT(state) AS missing_state,
        COUNT(*) - COUNT(date) AS missing_death_date,
        SUM(CASE WHEN age < 0 OR age > 120 THEN 1 ELSE 0 END) AS invalid_age,
        SUM(CASE WHEN date > CURRENT_DATE THEN 1 ELSE 0 END) AS future_death_dates,
        SUM(CASE WHEN date < date_positive THEN 1 ELSE 0 END) AS death_before_positive,
        SUM(CASE WHEN date_dose2 < date_dose1 THEN 1 ELSE 0 END) AS dose2_before_dose1,
        SUM(CASE WHEN date_dose3 < date_dose2 THEN 1 ELSE 0 END) AS dose3_before_dose2,
        ROUND(AVG(age), 2) AS avg_age,
        ROUND(MEDIAN(age), 2) AS median_age
    FROM covid_deaths
)
SELECT 
    'Total Records' AS metric,
    total_records::VARCHAR AS value,
    '' AS severity
FROM quality_metrics
UNION ALL
SELECT 'Missing Age', missing_age::VARCHAR, CASE WHEN missing_age > 0 THEN 'WARNING' ELSE 'OK' END FROM quality_metrics
UNION ALL
SELECT 'Missing State', missing_state::VARCHAR, CASE WHEN missing_state > 0 THEN 'WARNING' ELSE 'OK' END FROM quality_metrics
UNION ALL
SELECT 'Invalid Age (<0 or >120)', invalid_age::VARCHAR, CASE WHEN invalid_age > 0 THEN 'ERROR' ELSE 'OK' END FROM quality_metrics
UNION ALL
SELECT 'Future Death Dates', future_death_dates::VARCHAR, CASE WHEN future_death_dates > 0 THEN 'ERROR' ELSE 'OK' END FROM quality_metrics
UNION ALL
SELECT 'Death Before Positive Test', death_before_positive::VARCHAR, CASE WHEN death_before_positive > 0 THEN 'WARNING' ELSE 'OK' END FROM quality_metrics
UNION ALL
SELECT 'Dose 2 Before Dose 1', dose2_before_dose1::VARCHAR, CASE WHEN dose2_before_dose1 > 0 THEN 'ERROR' ELSE 'OK' END FROM quality_metrics
UNION ALL
SELECT 'Dose 3 Before Dose 2', dose3_before_dose2::VARCHAR, CASE WHEN dose3_before_dose2 > 0 THEN 'ERROR' ELSE 'OK' END FROM quality_metrics
UNION ALL
SELECT 'Average Age', avg_age::VARCHAR, 'INFO' FROM quality_metrics
UNION ALL
SELECT 'Median Age', median_age::VARCHAR, 'INFO' FROM quality_metrics;
```
</details>

---

## Exercise 14: Production Pipeline

### Question 14.1: Complete ETL pipeline
```sql
-- Create a production-ready pipeline that:
-- 1. Extracts data from CSV
-- 2. Validates and cleans data
-- 3. Creates derived variables
-- 4. Generates summary tables
-- 5. Exports results
-- Write your complete pipeline here:
```

<details>
<summary>💡 Solution</summary>

```sql
-- ==========================================
-- STEP 1: EXTRACT - Load raw data
-- ==========================================
CREATE TEMP TABLE raw_covid_deaths AS
SELECT * FROM 'https://storage.data.gov.my/healthcare/covid_deaths_linelist.csv';

-- ==========================================
-- STEP 2: VALIDATE AND CLEAN
-- ==========================================
CREATE TABLE cleaned_covid_deaths AS
SELECT 
    -- Original fields with validation
    CASE 
        WHEN date > CURRENT_DATE THEN NULL
        WHEN date < DATE '2020-01-01' THEN NULL
        ELSE date
    END AS date,
    date_announced,
    date_positive,
    date_dose1,
    date_dose2,
    date_dose3,
    brand1,
    brand2,
    brand3,
    state,
    CASE 
        WHEN age < 0 OR age > 120 THEN NULL
        ELSE age
    END AS age,
    male,
    bid,
    malaysian,
    comorb,
    
    -- ==========================================
    -- STEP 3: DERIVED VARIABLES
    -- ==========================================
    
    -- Age groups
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
    
    -- Time calculations
    date - date_positive AS days_positive_to_death,
    date - date_dose2 AS days_since_dose2,
    date_dose2 - date_dose1 AS days_between_doses,
    
    -- Risk categorization
    CASE 
        WHEN age >= 60 AND comorb = 1 THEN 'High Risk'
        WHEN age >= 60 OR comorb = 1 THEN 'Moderate Risk'
        ELSE 'Low Risk'
    END AS risk_category,
    
    -- Date components
    YEAR(date) AS death_year,
    MONTH(date) AS death_month,
    QUARTER(date) AS death_quarter,
    date_trunc('week', date) AS death_week

FROM raw_covid_deaths
WHERE age IS NOT NULL
  AND state IS NOT NULL
  AND date IS NOT NULL;

-- ==========================================
-- STEP 4: GENERATE SUMMARY TABLES
-- ==========================================

-- Executive Dashboard: High-level KPIs
CREATE TABLE executive_summary AS
SELECT 
    COUNT(*) AS total_deaths,
    COUNT(DISTINCT state) AS states_affected,
    ROUND(AVG(age), 1) AS avg_age,
    ROUND(100.0 * SUM(CASE WHEN vaccination_status = 'Unvaccinated' THEN 1 ELSE 0 END) / COUNT(*), 2) AS pct_unvaccinated,
    ROUND(100.0 * SUM(CASE WHEN comorb = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS pct_with_comorbidity,
    MIN(date) AS first_death,
    MAX(date) AS latest_death
FROM cleaned_covid_deaths;

-- Epidemiologist Table: Detailed statistics by state and time
CREATE TABLE epi_summary AS
SELECT 
    state,
    death_year,
    death_quarter,
    age_group,
    vaccination_status,
    COUNT(*) AS deaths,
    ROUND(AVG(age), 1) AS avg_age,
    ROUND(AVG(days_positive_to_death), 1) AS avg_days_to_death,
    ROUND(100.0 * SUM(CASE WHEN comorb = 1 THEN 1 ELSE 0 END) / COUNT(*), 1) AS pct_comorbidity
FROM cleaned_covid_deaths
GROUP BY state, death_year, death_quarter, age_group, vaccination_status;

-- State Health Department: State-specific metrics
CREATE TABLE state_summary AS
WITH state_stats AS (
    SELECT 
        state,
        COUNT(*) AS total_deaths,
        ROUND(AVG(age), 1) AS avg_age,
        COUNT(DISTINCT death_year || '-' || death_month) AS months_with_deaths
    FROM cleaned_covid_deaths
    GROUP BY state
)
SELECT 
    state,
    total_deaths,
    avg_age,
    months_with_deaths,
    RANK() OVER (ORDER BY total_deaths DESC) AS death_rank
FROM state_stats
ORDER BY total_deaths DESC;

-- Time Series: Daily deaths with moving averages
CREATE TABLE daily_time_series AS
WITH daily AS (
    SELECT 
        date,
        COUNT(*) AS deaths
    FROM cleaned_covid_deaths
    GROUP BY date
)
SELECT 
    date,
    deaths,
    AVG(deaths) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS ma7,
    AVG(deaths) OVER (ORDER BY date ROWS BETWEEN 29 PRECEDING AND CURRENT ROW) AS ma30,
    SUM(deaths) OVER (ORDER BY date) AS cumulative_deaths
FROM daily
ORDER BY date;

-- ==========================================
-- STEP 5: EXPORT RESULTS
-- ==========================================

-- Export cleaned data
COPY cleaned_covid_deaths TO 'cleaned_covid_deaths.parquet' (FORMAT PARQUET);

-- Export summaries
COPY executive_summary TO 'executive_summary.csv' (HEADER);
COPY state_summary TO 'state_summary.csv' (HEADER);
COPY epi_summary TO 'epi_summary.parquet' (FORMAT PARQUET);
COPY daily_time_series TO 'daily_time_series.csv' (HEADER);

-- Create views for BI tools
CREATE VIEW latest_7_days AS
SELECT * FROM cleaned_covid_deaths
WHERE date >= CURRENT_DATE - INTERVAL '7 days';

CREATE VIEW high_risk_deaths AS
SELECT * FROM cleaned_covid_deaths
WHERE risk_category = 'High Risk';

SELECT 'ETL Pipeline Completed Successfully' AS status;
```
</details>

---

## 🏆 Challenge Exercise: Comprehensive Analysis

### Question: Vaccine Effectiveness Meta-Analysis

Create a comprehensive analysis that answers:
1. Which age group benefited most from vaccination?
2. How did vaccine effectiveness change over time?
3. Which vaccine brand showed best outcomes?
4. What was the impact of booster doses?

Your analysis should include:
- Multiple CTEs for step-by-step logic
- Statistical calculations
- Time-based comparisons
- Multi-dimensional groupings
- Clear output table for reporting

<details>
<summary>💡 Solution</summary>

```sql
-- Comprehensive Vaccine Effectiveness Analysis
WITH 
-- Step 1: Classify all records
classified_deaths AS (
    SELECT 
        *,
        CASE 
            WHEN age < 18 THEN '0-17'
            WHEN age BETWEEN 18 AND 39 THEN '18-39'
            WHEN age BETWEEN 40 AND 59 THEN '40-59'
            WHEN age >= 60 THEN '60+'
        END AS age_group,
        CASE 
            WHEN date_dose3 IS NOT NULL THEN 'Booster'
            WHEN date_dose2 IS NOT NULL THEN 'Fully Vaccinated'
            WHEN date_dose1 IS NOT NULL THEN 'Partially Vaccinated'
            ELSE 'Unvaccinated'
        END AS vax_status,
        date_trunc('quarter', date) AS death_quarter,
        COALESCE(brand1, 'None') AS primary_brand
    FROM covid_deaths
    WHERE age IS NOT NULL
),

-- Step 2: Calculate baseline (unvaccinated) statistics
unvaccinated_baseline AS (
    SELECT 
        age_group,
        COUNT(*) AS unvax_deaths,
        AVG(age) AS unvax_avg_age
    FROM classified_deaths
    WHERE vax_status = 'Unvaccinated'
    GROUP BY age_group
),

-- Step 3: Calculate vaccinated outcomes
vaccinated_outcomes AS (
    SELECT 
        age_group,
        vax_status,
        primary_brand,
        COUNT(*) AS deaths,
        AVG(age) AS avg_age,
        ROUND(AVG(CASE WHEN comorb = 1 THEN 100.0 ELSE 0 END), 1) AS pct_comorbidity
    FROM classified_deaths
    WHERE vax_status != 'Unvaccinated'
    GROUP BY age_group, vax_status, primary_brand
),

-- Step 4: Time-based effectiveness
time_based_effectiveness AS (
    SELECT 
        death_quarter,
        vax_status,
        COUNT(*) AS deaths,
        ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (PARTITION BY death_quarter), 2) AS pct_of_quarter
    FROM classified_deaths
    GROUP BY death_quarter, vax_status
),

-- Step 5: Final comprehensive report
final_report AS (
    SELECT 
        v.age_group,
        v.vax_status,
        v.primary_brand,
        v.deaths AS vaccinated_deaths,
        u.unvax_deaths,
        ROUND(100.0 * v.deaths / u.unvax_deaths, 2) AS relative_risk,
        v.avg_age AS vax_avg_age,
        u.unvax_avg_age,
        v.pct_comorbidity
    FROM vaccinated_outcomes v
    LEFT JOIN unvaccinated_baseline u ON v.age_group = u.age_group
)

-- Final output
SELECT 
    age_group,
    vax_status,
    primary_brand,
    vaccinated_deaths,
    unvax_deaths,
    relative_risk,
    CASE 
        WHEN relative_risk < 50 THEN 'Highly Effective'
        WHEN relative_risk < 75 THEN 'Moderately Effective'
        WHEN relative_risk < 100 THEN 'Somewhat Effective'
        ELSE 'Not Effective'
    END AS effectiveness_category,
    ROUND(vax_avg_age, 1) AS avg_age_vaccinated,
    ROUND(unvax_avg_age, 1) AS avg_age_unvaccinated,
    pct_comorbidity
FROM final_report
ORDER BY age_group, relative_risk;

-- Time-based trends
SELECT * FROM time_based_effectiveness
ORDER BY death_quarter, vax_status;
```
</details>

---

## 📝 Answer Key Summary

### Basic Level
- Focus on: SELECT, WHERE, ORDER BY, GROUP BY, basic aggregations
- Key concepts: Filtering, sorting, counting, simple calculations

### Intermediate Level  
- Focus on: Window functions, CTEs, JOINs, date operations
- Key concepts: Moving averages, cumulative sums, multi-step queries

### Advanced Level
- Focus on: Complex analytics, statistical methods, data pipelines
- Key concepts: Production-ready code, quality checks, comprehensive analysis

---

**Remember:**
- Always test your queries with LIMIT first
- Comment your code for future reference
- Validate your results make sense
- Use CTEs for complex logic
- Consider performance for large datasets

---

*Prepared by: Health Informatics Division, Ministry of Health Malaysia*
