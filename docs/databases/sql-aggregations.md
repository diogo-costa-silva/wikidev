# SQL Aggregations & Window Functions

> **GROUP BY, window functions, and advanced analytical SQL operations.**

Aggregations and window functions are essential for data analysis in SQL. They allow you to summarize data, calculate running totals, ranks, and perform complex analytical operations efficiently.

## 🎯 Goals

- **Master GROUP BY** for data summarization
- **Understand window functions** for advanced analytics
- **Calculate running totals and rankings**
- **Perform time-series analysis** with SQL

## 🔍 Quick Verification

Test aggregations with sample data:

```sql
-- Create sample sales data
CREATE TABLE sales (
    id INTEGER PRIMARY KEY,
    salesperson TEXT,
    region TEXT,
    product TEXT,
    amount DECIMAL(10,2),
    sale_date DATE
);

INSERT INTO sales (salesperson, region, product, amount, sale_date) VALUES
('Alice', 'North', 'Laptop', 1200.00, '2024-01-15'),
('Bob', 'South', 'Phone', 800.00, '2024-01-16'),
('Alice', 'North', 'Mouse', 25.00, '2024-01-17'),
('Carol', 'West', 'Laptop', 1200.00, '2024-01-18'),
('Bob', 'South', 'Keyboard', 100.00, '2024-01-19');

-- Test basic aggregation
SELECT region, COUNT(*) as sales_count, SUM(amount) as total_sales
FROM sales
GROUP BY region;
```

## 📊 Basic Aggregations

### Essential Aggregate Functions
```sql
-- Count functions
SELECT COUNT(*) as total_rows FROM sales;                    -- All rows
SELECT COUNT(salesperson) as non_null_count FROM sales;      -- Non-null values
SELECT COUNT(DISTINCT region) as unique_regions FROM sales;  -- Unique values

-- Numeric aggregations
SELECT 
    SUM(amount) as total_sales,
    AVG(amount) as average_sale,
    MIN(amount) as smallest_sale,
    MAX(amount) as largest_sale,
    ROUND(AVG(amount), 2) as avg_rounded
FROM sales;

-- String aggregations (database-specific)
SELECT GROUP_CONCAT(product) as all_products FROM sales;     -- MySQL
SELECT STRING_AGG(product, ', ') as all_products FROM sales; -- PostgreSQL
```

### GROUP BY Fundamentals
```sql
-- Single column grouping
SELECT region, COUNT(*) as sales_count
FROM sales
GROUP BY region;

-- Multiple column grouping
SELECT region, product, COUNT(*) as sales_count, SUM(amount) as total
FROM sales
GROUP BY region, product;

-- Grouping with calculated fields
SELECT 
    strftime('%Y-%m', sale_date) as month,
    COUNT(*) as monthly_sales,
    SUM(amount) as monthly_revenue
FROM sales
GROUP BY strftime('%Y-%m', sale_date)
ORDER BY month;
```

### HAVING Clause (Filtering Groups)
```sql
-- Filter groups after aggregation
SELECT region, COUNT(*) as sales_count, SUM(amount) as total_sales
FROM sales
GROUP BY region
HAVING COUNT(*) > 1;                    -- Regions with more than 1 sale

-- Multiple HAVING conditions
SELECT salesperson, COUNT(*) as sale_count, AVG(amount) as avg_sale
FROM sales
GROUP BY salesperson
HAVING COUNT(*) >= 2 AND AVG(amount) > 500;

-- HAVING with complex conditions
SELECT region, product, COUNT(*) as count, SUM(amount) as total
FROM sales
GROUP BY region, product
HAVING SUM(amount) > 1000 OR COUNT(*) > 2;
```

## 🪟 Window Functions

### Basic Window Function Syntax
```sql
-- General syntax:
-- aggregate_function() OVER (PARTITION BY column ORDER BY column)

-- Running totals
SELECT 
    salesperson,
    amount,
    sale_date,
    SUM(amount) OVER (ORDER BY sale_date) as running_total
FROM sales
ORDER BY sale_date;

-- Running totals by group
SELECT 
    salesperson,
    amount,
    sale_date,
    SUM(amount) OVER (
        PARTITION BY salesperson 
        ORDER BY sale_date
    ) as running_total_by_person
FROM sales
ORDER BY salesperson, sale_date;
```

### Ranking Functions
```sql
-- ROW_NUMBER: Sequential numbering
SELECT 
    salesperson,
    amount,
    ROW_NUMBER() OVER (ORDER BY amount DESC) as row_num
FROM sales;

-- RANK: Ranking with gaps for ties
SELECT 
    salesperson,
    amount,
    RANK() OVER (ORDER BY amount DESC) as rank
FROM sales;

-- DENSE_RANK: Ranking without gaps
SELECT 
    salesperson,
    amount,
    DENSE_RANK() OVER (ORDER BY amount DESC) as dense_rank
FROM sales;

-- Ranking within partitions
SELECT 
    region,
    salesperson,
    amount,
    RANK() OVER (PARTITION BY region ORDER BY amount DESC) as region_rank
FROM sales;
```

### Value Functions
```sql
-- LEAD and LAG: Access previous/next rows
SELECT 
    salesperson,
    sale_date,
    amount,
    LAG(amount) OVER (ORDER BY sale_date) as previous_sale,
    LEAD(amount) OVER (ORDER BY sale_date) as next_sale
FROM sales
ORDER BY sale_date;

-- Calculate differences between consecutive rows
SELECT 
    salesperson,
    sale_date,
    amount,
    amount - LAG(amount) OVER (ORDER BY sale_date) as difference_from_previous
FROM sales
ORDER BY sale_date;

-- FIRST_VALUE and LAST_VALUE
SELECT 
    salesperson,
    amount,
    sale_date,
    FIRST_VALUE(amount) OVER (
        PARTITION BY salesperson 
        ORDER BY sale_date 
        ROWS UNBOUNDED PRECEDING
    ) as first_sale,
    LAST_VALUE(amount) OVER (
        PARTITION BY salesperson 
        ORDER BY sale_date 
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) as last_sale
FROM sales;
```

### Statistical Window Functions
```sql
-- Percentiles and quantiles
SELECT 
    salesperson,
    amount,
    NTILE(4) OVER (ORDER BY amount) as quartile,
    PERCENT_RANK() OVER (ORDER BY amount) as percent_rank,
    CUME_DIST() OVER (ORDER BY amount) as cumulative_distribution
FROM sales;

-- Moving averages
SELECT 
    sale_date,
    amount,
    AVG(amount) OVER (
        ORDER BY sale_date 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) as moving_avg_3days
FROM sales
ORDER BY sale_date;
```

## 📅 Time-Series Analysis

### Date-Based Aggregations
```sql
-- Sales by year
SELECT 
    strftime('%Y', sale_date) as year,
    COUNT(*) as sales_count,
    SUM(amount) as annual_sales
FROM sales
GROUP BY strftime('%Y', sale_date);

-- Sales by month
SELECT 
    strftime('%Y-%m', sale_date) as month,
    COUNT(*) as sales_count,
    SUM(amount) as monthly_sales,
    AVG(amount) as avg_sale_amount
FROM sales
GROUP BY strftime('%Y-%m', sale_date)
ORDER BY month;

-- Sales by day of week
SELECT 
    CASE strftime('%w', sale_date)
        WHEN '0' THEN 'Sunday'
        WHEN '1' THEN 'Monday'
        WHEN '2' THEN 'Tuesday'
        WHEN '3' THEN 'Wednesday'
        WHEN '4' THEN 'Thursday'
        WHEN '5' THEN 'Friday'
        WHEN '6' THEN 'Saturday'
    END as day_of_week,
    COUNT(*) as sales_count,
    AVG(amount) as avg_amount
FROM sales
GROUP BY strftime('%w', sale_date)
ORDER BY strftime('%w', sale_date);
```

### Running Calculations
```sql
-- Cumulative sales over time
SELECT 
    sale_date,
    amount,
    SUM(amount) OVER (ORDER BY sale_date) as cumulative_sales
FROM sales
ORDER BY sale_date;

-- Year-over-year comparison
WITH monthly_sales AS (
    SELECT 
        strftime('%Y', sale_date) as year,
        strftime('%m', sale_date) as month,
        SUM(amount) as monthly_total
    FROM sales
    GROUP BY strftime('%Y', sale_date), strftime('%m', sale_date)
)
SELECT 
    year,
    month,
    monthly_total,
    LAG(monthly_total) OVER (
        PARTITION BY month 
        ORDER BY year
    ) as previous_year_same_month,
    monthly_total - LAG(monthly_total) OVER (
        PARTITION BY month 
        ORDER BY year
    ) as year_over_year_change
FROM monthly_sales
ORDER BY year, month;
```

### Growth Rate Calculations
```sql
-- Month-over-month growth
WITH monthly_sales AS (
    SELECT 
        strftime('%Y-%m', sale_date) as month,
        SUM(amount) as total
    FROM sales
    GROUP BY strftime('%Y-%m', sale_date)
)
SELECT 
    month,
    total,
    LAG(total) OVER (ORDER BY month) as previous_month,
    ROUND(
        (total - LAG(total) OVER (ORDER BY month)) * 100.0 / 
        LAG(total) OVER (ORDER BY month), 2
    ) as growth_rate_percent
FROM monthly_sales
ORDER BY month;
```

## 🏆 Advanced Analytical Queries

### Top N Analysis
```sql
-- Top 3 salespeople by total sales
SELECT salesperson, total_sales
FROM (
    SELECT 
        salesperson,
        SUM(amount) as total_sales,
        RANK() OVER (ORDER BY SUM(amount) DESC) as rank
    FROM sales
    GROUP BY salesperson
)
WHERE rank <= 3;

-- Top product in each region
SELECT region, product, total_sales
FROM (
    SELECT 
        region,
        product,
        SUM(amount) as total_sales,
        ROW_NUMBER() OVER (PARTITION BY region ORDER BY SUM(amount) DESC) as rn
    FROM sales
    GROUP BY region, product
)
WHERE rn = 1;
```

### Cohort Analysis
```sql
-- Customer cohort analysis (simplified example)
WITH customer_cohorts AS (
    SELECT 
        salesperson,
        MIN(sale_date) as first_purchase_date,
        strftime('%Y-%m', MIN(sale_date)) as cohort_month
    FROM sales
    GROUP BY salesperson
),
cohort_data AS (
    SELECT 
        cc.cohort_month,
        strftime('%Y-%m', s.sale_date) as purchase_month,
        COUNT(DISTINCT s.salesperson) as customers
    FROM customer_cohorts cc
    JOIN sales s ON cc.salesperson = s.salesperson
    GROUP BY cc.cohort_month, strftime('%Y-%m', s.sale_date)
)
SELECT 
    cohort_month,
    purchase_month,
    customers,
    FIRST_VALUE(customers) OVER (
        PARTITION BY cohort_month 
        ORDER BY purchase_month
    ) as cohort_size
FROM cohort_data
ORDER BY cohort_month, purchase_month;
```

### RFM Analysis (Recency, Frequency, Monetary)
```sql
WITH rfm_data AS (
    SELECT 
        salesperson,
        MAX(sale_date) as last_purchase,
        COUNT(*) as frequency,
        SUM(amount) as monetary,
        -- Calculate days since last purchase
        julianday('2024-02-01') - julianday(MAX(sale_date)) as recency_days
    FROM sales
    GROUP BY salesperson
),
rfm_scores AS (
    SELECT 
        salesperson,
        recency_days,
        frequency,
        monetary,
        -- Assign quintile scores (1-5)
        NTILE(5) OVER (ORDER BY recency_days DESC) as recency_score,
        NTILE(5) OVER (ORDER BY frequency ASC) as frequency_score,
        NTILE(5) OVER (ORDER BY monetary ASC) as monetary_score
    FROM rfm_data
)
SELECT 
    salesperson,
    recency_score || frequency_score || monetary_score as rfm_score,
    CASE 
        WHEN recency_score >= 4 AND frequency_score >= 4 AND monetary_score >= 4 
        THEN 'Champions'
        WHEN recency_score >= 3 AND frequency_score >= 3 AND monetary_score >= 3 
        THEN 'Loyal Customers'
        WHEN recency_score >= 3 AND frequency_score <= 2 
        THEN 'Potential Loyalists'
        ELSE 'Other'
    END as customer_segment
FROM rfm_scores
ORDER BY rfm_score DESC;
```

## 📈 Business Intelligence Queries

### Sales Performance Dashboard
```sql
-- Comprehensive sales dashboard
SELECT 
    'Total Sales' as metric,
    CAST(SUM(amount) as TEXT) as value
FROM sales

UNION ALL

SELECT 
    'Average Sale',
    CAST(ROUND(AVG(amount), 2) as TEXT)
FROM sales

UNION ALL

SELECT 
    'Total Transactions',
    CAST(COUNT(*) as TEXT)
FROM sales

UNION ALL

SELECT 
    'Active Salespeople',
    CAST(COUNT(DISTINCT salesperson) as TEXT)
FROM sales

UNION ALL

SELECT 
    'Top Region',
    region || ' (' || CAST(ROUND(SUM(amount)) as TEXT) || ')'
FROM sales
GROUP BY region
ORDER BY SUM(amount) DESC
LIMIT 1;
```

### Trend Analysis
```sql
-- Sales trend with moving averages
WITH daily_sales AS (
    SELECT 
        sale_date,
        SUM(amount) as daily_total
    FROM sales
    GROUP BY sale_date
)
SELECT 
    sale_date,
    daily_total,
    AVG(daily_total) OVER (
        ORDER BY sale_date 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) as moving_avg_3day,
    AVG(daily_total) OVER (
        ORDER BY sale_date 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) as moving_avg_7day
FROM daily_sales
ORDER BY sale_date;
```

## ⚠️ Common Pitfalls

### GROUP BY Errors
```sql
-- Wrong - column not in GROUP BY or aggregate
-- SELECT salesperson, region, SUM(amount) 
-- FROM sales 
-- GROUP BY salesperson;

-- Correct - include all non-aggregate columns in GROUP BY
SELECT salesperson, region, SUM(amount) as total
FROM sales 
GROUP BY salesperson, region;
```

### Window Function Frame Issues
```sql
-- Be careful with LAST_VALUE - needs proper frame
-- Wrong (may not get actual last value)
SELECT 
    amount,
    LAST_VALUE(amount) OVER (ORDER BY sale_date) as last_amount
FROM sales;

-- Correct - specify frame properly
SELECT 
    amount,
    LAST_VALUE(amount) OVER (
        ORDER BY sale_date 
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) as last_amount
FROM sales;
```

## 🚀 Performance Tips

### Efficient Aggregations
```sql
-- Use indexes on GROUP BY columns
CREATE INDEX idx_sales_region ON sales(region);
CREATE INDEX idx_sales_date ON sales(sale_date);

-- Filter before grouping when possible
SELECT region, COUNT(*) as recent_sales
FROM sales
WHERE sale_date >= '2024-01-01'    -- Filter first
GROUP BY region;

-- Use HAVING for post-aggregation filtering
SELECT salesperson, COUNT(*) as sale_count
FROM sales
GROUP BY salesperson
HAVING COUNT(*) > 5;               -- More efficient than WHERE
```

## 🔗 Related Sections

- **[Query Basics](query-basics.md)** - Foundation SQL concepts
- **[Joins](joins.md)** - Combining aggregations with joins
- **[Subqueries & CTEs](subqueries-ctes.md)** - Complex analytical queries
- **[Schema Design](schema-design.md)** - Designing tables for efficient aggregations

## ⚡ Quick Reference

**Basic Aggregations:**
```sql
SELECT 
    COUNT(*),                       -- Count rows
    SUM(column),                   -- Sum values
    AVG(column),                   -- Average
    MIN(column), MAX(column)       -- Min/Max
FROM table
GROUP BY grouping_column;
```

**Window Functions:**
```sql
-- Running totals
SUM(amount) OVER (ORDER BY date)

-- Ranking
ROW_NUMBER() OVER (ORDER BY amount DESC)
RANK() OVER (PARTITION BY region ORDER BY amount)

-- Previous/next values
LAG(amount) OVER (ORDER BY date)
LEAD(amount) OVER (ORDER BY date)
```

**Common Patterns:**
```sql
-- Top N per group
ROW_NUMBER() OVER (PARTITION BY group ORDER BY value DESC)

-- Moving averages
AVG(value) OVER (ORDER BY date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW)

-- Growth rates
(current - LAG(current)) / LAG(current) * 100
```

---

**Next:** Learn about [Subqueries & CTEs](subqueries-ctes.md) for complex analytical queries, or explore [Query Basics](query-basics.md) for foundational concepts.