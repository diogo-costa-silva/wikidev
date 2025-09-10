# SQL Subqueries & CTEs

> **Advanced SQL: subqueries, Common Table Expressions, and complex analytical queries.**

Subqueries and CTEs (Common Table Expressions) are powerful tools for breaking down complex problems into manageable steps. They make your SQL more readable, maintainable, and enable sophisticated analytical operations.

## 🎯 Goals

- **Master subqueries** in WHERE, SELECT, and FROM clauses
- **Use CTEs effectively** for complex analytical queries
- **Build recursive CTEs** for hierarchical data
- **Optimize query performance** with proper techniques

## 🔍 Quick Verification

Test subqueries and CTEs with sample data:

```sql
-- Create test data
CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    department TEXT,
    salary DECIMAL(10,2),
    manager_id INTEGER,
    hire_date DATE
);

INSERT INTO employees (name, department, salary, manager_id, hire_date) VALUES
('Alice Johnson', 'Engineering', 95000, NULL, '2020-01-15'),
('Bob Smith', 'Engineering', 85000, 1, '2020-03-10'),
('Carol Davis', 'Marketing', 70000, NULL, '2019-06-01'),
('David Wilson', 'Engineering', 80000, 1, '2021-02-20'),
('Eve Brown', 'Marketing', 65000, 3, '2021-05-15');

-- Test CTE
WITH high_earners AS (
    SELECT name, salary, department
    FROM employees
    WHERE salary > 75000
)
SELECT department, COUNT(*) as high_earner_count, AVG(salary) as avg_salary
FROM high_earners
GROUP BY department;
```

## 🔍 Subqueries Fundamentals

### Subqueries in WHERE Clause
```sql
-- Find employees earning above average salary
SELECT name, salary, department
FROM employees
WHERE salary > (
    SELECT AVG(salary) 
    FROM employees
);

-- Find employees in departments with more than 2 people
SELECT name, department
FROM employees
WHERE department IN (
    SELECT department
    FROM employees
    GROUP BY department
    HAVING COUNT(*) > 2
);

-- Find employees with no direct reports
SELECT name, department
FROM employees
WHERE id NOT IN (
    SELECT DISTINCT manager_id
    FROM employees
    WHERE manager_id IS NOT NULL
);
```

### Correlated Subqueries
```sql
-- Find employees earning above their department average
SELECT e1.name, e1.salary, e1.department
FROM employees e1
WHERE e1.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department = e1.department
);

-- Find the highest paid employee in each department
SELECT name, salary, department
FROM employees e1
WHERE salary = (
    SELECT MAX(salary)
    FROM employees e2
    WHERE e2.department = e1.department
);

-- Find employees hired before their manager
SELECT e.name, e.hire_date, m.name as manager_name, m.hire_date as manager_hire_date
FROM employees e
JOIN employees m ON e.manager_id = m.id
WHERE e.hire_date < m.hire_date;
```

### EXISTS and NOT EXISTS
```sql
-- Find departments that have employees
SELECT DISTINCT department
FROM employees e1
WHERE EXISTS (
    SELECT 1
    FROM employees e2
    WHERE e2.department = e1.department
    AND e2.salary > 80000
);

-- Find employees who are managers
SELECT name, department
FROM employees e1
WHERE EXISTS (
    SELECT 1
    FROM employees e2
    WHERE e2.manager_id = e1.id
);

-- Find departments with no high earners (salary > 90000)
SELECT DISTINCT department
FROM employees e1
WHERE NOT EXISTS (
    SELECT 1
    FROM employees e2
    WHERE e2.department = e1.department
    AND e2.salary > 90000
);
```

### Subqueries in SELECT Clause
```sql
-- Add calculated columns using subqueries
SELECT 
    name,
    salary,
    department,
    (SELECT AVG(salary) FROM employees) as company_avg_salary,
    salary - (SELECT AVG(salary) FROM employees) as salary_diff_from_avg,
    (SELECT COUNT(*) 
     FROM employees e2 
     WHERE e2.department = employees.department) as dept_size
FROM employees;

-- Ranking without window functions (alternative approach)
SELECT 
    name,
    salary,
    (SELECT COUNT(*) 
     FROM employees e2 
     WHERE e2.salary >= employees.salary) as salary_rank
FROM employees
ORDER BY salary DESC;
```

### Subqueries in FROM Clause (Derived Tables)
```sql
-- Use subquery as a table
SELECT 
    department,
    avg_salary,
    CASE 
        WHEN avg_salary > 80000 THEN 'High Pay'
        WHEN avg_salary > 65000 THEN 'Medium Pay'
        ELSE 'Lower Pay'
    END as pay_category
FROM (
    SELECT 
        department,
        AVG(salary) as avg_salary,
        COUNT(*) as employee_count
    FROM employees
    GROUP BY department
) dept_stats
WHERE employee_count >= 2;

-- Complex aggregation using subqueries
SELECT 
    hire_year,
    COUNT(*) as hires,
    AVG(salary) as avg_starting_salary
FROM (
    SELECT 
        strftime('%Y', hire_date) as hire_year,
        salary
    FROM employees
) yearly_hires
GROUP BY hire_year
ORDER BY hire_year;
```

## 🏗️ Common Table Expressions (CTEs)

### Basic CTE Syntax
```sql
-- Simple CTE
WITH department_stats AS (
    SELECT 
        department,
        COUNT(*) as employee_count,
        AVG(salary) as avg_salary,
        MAX(salary) as max_salary,
        MIN(salary) as min_salary
    FROM employees
    GROUP BY department
)
SELECT 
    department,
    employee_count,
    ROUND(avg_salary, 2) as avg_salary,
    max_salary - min_salary as salary_range
FROM department_stats
WHERE employee_count > 1
ORDER BY avg_salary DESC;
```

### Multiple CTEs
```sql
-- Multiple CTEs in single query
WITH 
high_earners AS (
    SELECT name, salary, department
    FROM employees
    WHERE salary > 80000
),
department_averages AS (
    SELECT 
        department,
        AVG(salary) as dept_avg_salary
    FROM employees
    GROUP BY department
),
company_stats AS (
    SELECT 
        AVG(salary) as company_avg_salary,
        COUNT(*) as total_employees
    FROM employees
)
SELECT 
    he.name,
    he.salary,
    he.department,
    da.dept_avg_salary,
    cs.company_avg_salary,
    he.salary - da.dept_avg_salary as diff_from_dept_avg,
    he.salary - cs.company_avg_salary as diff_from_company_avg
FROM high_earners he
JOIN department_averages da ON he.department = da.department
CROSS JOIN company_stats cs
ORDER BY he.salary DESC;
```

### Recursive CTEs
```sql
-- Organizational hierarchy using recursive CTE
WITH RECURSIVE employee_hierarchy AS (
    -- Anchor: Start with top-level managers (no manager)
    SELECT 
        id,
        name,
        department,
        salary,
        manager_id,
        0 as level,
        name as hierarchy_path
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive: Add direct reports
    SELECT 
        e.id,
        e.name,
        e.department,
        e.salary,
        e.manager_id,
        eh.level + 1,
        eh.hierarchy_path || ' -> ' || e.name as hierarchy_path
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.id
)
SELECT 
    level,
    name,
    department,
    salary,
    hierarchy_path
FROM employee_hierarchy
ORDER BY level, name;

-- Generate number sequence using recursive CTE
WITH RECURSIVE number_sequence AS (
    SELECT 1 as n
    UNION ALL
    SELECT n + 1
    FROM number_sequence
    WHERE n < 10
)
SELECT n FROM number_sequence;
```

### Time Series with CTEs
```sql
-- Generate date series for reporting
WITH RECURSIVE date_series AS (
    SELECT DATE('2024-01-01') as date
    UNION ALL
    SELECT DATE(date, '+1 day')
    FROM date_series
    WHERE date < DATE('2024-12-31')
),
monthly_hires AS (
    SELECT 
        strftime('%Y-%m', hire_date) as hire_month,
        COUNT(*) as hires
    FROM employees
    GROUP BY strftime('%Y-%m', hire_date)
)
SELECT 
    strftime('%Y-%m', ds.date) as month,
    COALESCE(mh.hires, 0) as hires
FROM date_series ds
LEFT JOIN monthly_hires mh ON strftime('%Y-%m', ds.date) = mh.hire_month
WHERE strftime('%d', ds.date) = '01'  -- First day of each month
ORDER BY month;
```

## 📊 Advanced Analytical Patterns

### Running Totals and Moving Averages
```sql
-- Running totals using CTE
WITH ordered_employees AS (
    SELECT 
        name,
        salary,
        hire_date,
        ROW_NUMBER() OVER (ORDER BY hire_date) as hire_order
    FROM employees
),
running_totals AS (
    SELECT 
        name,
        salary,
        hire_date,
        hire_order,
        (SELECT SUM(salary) 
         FROM ordered_employees oe2 
         WHERE oe2.hire_order <= ordered_employees.hire_order) as cumulative_salary_cost
    FROM ordered_employees
)
SELECT 
    name,
    salary,
    hire_date,
    cumulative_salary_cost,
    cumulative_salary_cost - LAG(cumulative_salary_cost, 1, 0) OVER (ORDER BY hire_date) as salary_added
FROM running_totals
ORDER BY hire_date;
```

### Comparative Analysis
```sql
-- Compare departments across multiple metrics
WITH department_metrics AS (
    SELECT 
        department,
        COUNT(*) as employee_count,
        AVG(salary) as avg_salary,
        STDDEV(salary) as salary_stddev,
        MAX(salary) - MIN(salary) as salary_range,
        AVG(julianday('now') - julianday(hire_date)) / 365.25 as avg_tenure_years
    FROM employees
    GROUP BY department
),
company_benchmarks AS (
    SELECT 
        AVG(avg_salary) as company_avg_salary,
        AVG(employee_count) as avg_dept_size
    FROM department_metrics
)
SELECT 
    dm.department,
    dm.employee_count,
    ROUND(dm.avg_salary, 2) as avg_salary,
    ROUND(dm.salary_stddev, 2) as salary_stddev,
    dm.salary_range,
    ROUND(dm.avg_tenure_years, 1) as avg_tenure_years,
    ROUND(dm.avg_salary - cb.company_avg_salary, 2) as salary_vs_company_avg,
    CASE 
        WHEN dm.employee_count > cb.avg_dept_size THEN 'Large'
        WHEN dm.employee_count < cb.avg_dept_size THEN 'Small'
        ELSE 'Average'
    END as dept_size_category
FROM department_metrics dm
CROSS JOIN company_benchmarks cb
ORDER BY dm.avg_salary DESC;
```

### Data Quality Analysis
```sql
-- Data quality checks using CTEs
WITH data_quality_checks AS (
    SELECT 
        'employees' as table_name,
        COUNT(*) as total_records,
        COUNT(DISTINCT id) as unique_ids,
        COUNT(*) - COUNT(DISTINCT id) as duplicate_id_count,
        SUM(CASE WHEN name IS NULL OR name = '' THEN 1 ELSE 0 END) as missing_names,
        SUM(CASE WHEN salary IS NULL OR salary <= 0 THEN 1 ELSE 0 END) as invalid_salaries,
        SUM(CASE WHEN department IS NULL OR department = '' THEN 1 ELSE 0 END) as missing_departments,
        SUM(CASE WHEN hire_date IS NULL THEN 1 ELSE 0 END) as missing_hire_dates,
        SUM(CASE WHEN manager_id = id THEN 1 ELSE 0 END) as self_referencing_managers
    FROM employees
),
quality_summary AS (
    SELECT 
        table_name,
        total_records,
        duplicate_id_count,
        missing_names + invalid_salaries + missing_departments + missing_hire_dates + self_referencing_managers as total_issues,
        ROUND(
            (missing_names + invalid_salaries + missing_departments + missing_hire_dates + self_referencing_managers) * 100.0 / total_records, 
            2
        ) as issue_percentage
    FROM data_quality_checks
)
SELECT 
    table_name,
    total_records,
    total_issues,
    issue_percentage,
    CASE 
        WHEN issue_percentage = 0 THEN 'Excellent'
        WHEN issue_percentage < 1 THEN 'Good'
        WHEN issue_percentage < 5 THEN 'Fair'
        ELSE 'Poor'
    END as data_quality_rating
FROM quality_summary;
```

## 🎯 Business Intelligence Queries

### Customer Analytics (Example with Sales Data)
```sql
-- Complex customer segmentation
WITH customer_metrics AS (
    SELECT 
        customer_id,
        COUNT(*) as order_count,
        SUM(order_amount) as total_spent,
        AVG(order_amount) as avg_order_value,
        MAX(order_date) as last_order_date,
        MIN(order_date) as first_order_date,
        julianday('now') - julianday(MAX(order_date)) as days_since_last_order
    FROM orders
    GROUP BY customer_id
),
customer_segments AS (
    SELECT 
        customer_id,
        order_count,
        total_spent,
        avg_order_value,
        days_since_last_order,
        CASE 
            WHEN days_since_last_order <= 30 AND total_spent > 1000 THEN 'VIP Active'
            WHEN days_since_last_order <= 30 THEN 'Active'
            WHEN days_since_last_order <= 90 THEN 'At Risk'
            ELSE 'Inactive'
        END as customer_segment,
        NTILE(4) OVER (ORDER BY total_spent) as spending_quartile
    FROM customer_metrics
)
SELECT 
    customer_segment,
    spending_quartile,
    COUNT(*) as customer_count,
    AVG(total_spent) as avg_total_spent,
    AVG(order_count) as avg_orders,
    AVG(avg_order_value) as avg_order_value
FROM customer_segments
GROUP BY customer_segment, spending_quartile
ORDER BY customer_segment, spending_quartile;
```

### Financial Analysis
```sql
-- Revenue analysis with growth rates
WITH monthly_revenue AS (
    SELECT 
        strftime('%Y-%m', order_date) as month,
        SUM(order_amount) as revenue,
        COUNT(*) as order_count,
        COUNT(DISTINCT customer_id) as unique_customers
    FROM orders
    GROUP BY strftime('%Y-%m', order_date)
),
revenue_with_growth AS (
    SELECT 
        month,
        revenue,
        order_count,
        unique_customers,
        LAG(revenue) OVER (ORDER BY month) as previous_month_revenue,
        revenue - LAG(revenue) OVER (ORDER BY month) as revenue_change,
        ROUND(
            (revenue - LAG(revenue) OVER (ORDER BY month)) * 100.0 / 
            LAG(revenue) OVER (ORDER BY month), 
            2
        ) as growth_rate_percent
    FROM monthly_revenue
)
SELECT 
    month,
    revenue,
    order_count,
    unique_customers,
    COALESCE(revenue_change, 0) as revenue_change,
    COALESCE(growth_rate_percent, 0) as growth_rate_percent,
    CASE 
        WHEN growth_rate_percent > 20 THEN 'High Growth'
        WHEN growth_rate_percent > 5 THEN 'Moderate Growth'
        WHEN growth_rate_percent > 0 THEN 'Slow Growth'
        WHEN growth_rate_percent = 0 THEN 'No Change'
        ELSE 'Decline'
    END as growth_category
FROM revenue_with_growth
ORDER BY month;
```

## 🚀 Performance Optimization

### CTE vs Subquery Performance
```sql
-- CTE approach (better readability, may cache results)
WITH expensive_calculation AS (
    SELECT 
        department,
        AVG(salary) as dept_avg_salary
    FROM employees
    GROUP BY department
)
SELECT 
    e.name,
    e.salary,
    ec.dept_avg_salary,
    e.salary - ec.dept_avg_salary as diff_from_avg
FROM employees e
JOIN expensive_calculation ec ON e.department = ec.department;

-- Equivalent subquery approach
SELECT 
    e.name,
    e.salary,
    dept_stats.dept_avg_salary,
    e.salary - dept_stats.dept_avg_salary as diff_from_avg
FROM employees e
JOIN (
    SELECT 
        department,
        AVG(salary) as dept_avg_salary
    FROM employees
    GROUP BY department
) dept_stats ON e.department = dept_stats.department;
```

### Optimization Tips
```sql
-- Use indexes for subquery columns
CREATE INDEX idx_employees_department ON employees(department);
CREATE INDEX idx_employees_salary ON employees(salary);
CREATE INDEX idx_employees_manager_id ON employees(manager_id);

-- Limit subquery results when possible
WITH top_departments AS (
    SELECT department
    FROM employees
    GROUP BY department
    HAVING COUNT(*) > 2  -- Filter early
    ORDER BY AVG(salary) DESC
    LIMIT 5  -- Limit results
)
SELECT e.name, e.department, e.salary
FROM employees e
JOIN top_departments td ON e.department = td.department;
```

## ⚠️ Common Pitfalls

### NULL Handling in Subqueries
```sql
-- Wrong - may exclude valid results due to NULLs
SELECT name
FROM employees
WHERE id NOT IN (
    SELECT manager_id  -- Contains NULLs!
    FROM employees
);

-- Correct - handle NULLs explicitly
SELECT name
FROM employees
WHERE id NOT IN (
    SELECT manager_id
    FROM employees
    WHERE manager_id IS NOT NULL
);

-- Alternative using NOT EXISTS
SELECT name
FROM employees e1
WHERE NOT EXISTS (
    SELECT 1
    FROM employees e2
    WHERE e2.manager_id = e1.id
);
```

### Correlated Subquery Performance
```sql
-- Slow - correlated subquery runs for each row
SELECT name, salary
FROM employees e1
WHERE salary > (
    SELECT AVG(salary)
    FROM employees e2
    WHERE e2.department = e1.department
);

-- Faster - use CTE or window function
WITH dept_averages AS (
    SELECT 
        department,
        AVG(salary) as avg_salary
    FROM employees
    GROUP BY department
)
SELECT e.name, e.salary
FROM employees e
JOIN dept_averages da ON e.department = da.department
WHERE e.salary > da.avg_salary;
```

## 🔗 Related Sections

- **[Query Basics](query-basics.md)** - Foundation SQL concepts
- **[Joins](joins.md)** - Combining tables with subqueries
- **[Aggregations](aggregations.md)** - Window functions vs subqueries
- **[Schema Design](schema-design.md)** - Designing for efficient subqueries

## ⚡ Quick Reference

**Basic Subqueries:**
```sql
-- WHERE clause
WHERE column > (SELECT AVG(column) FROM table)
WHERE column IN (SELECT column FROM table WHERE condition)

-- SELECT clause
SELECT column, (SELECT COUNT(*) FROM table2) as count

-- FROM clause
FROM (SELECT * FROM table WHERE condition) subquery
```

**Common Table Expressions:**
```sql
-- Basic CTE
WITH cte_name AS (
    SELECT columns FROM table WHERE condition
)
SELECT * FROM cte_name;

-- Multiple CTEs
WITH cte1 AS (...), cte2 AS (...)
SELECT * FROM cte1 JOIN cte2 ON ...;

-- Recursive CTE
WITH RECURSIVE cte AS (
    SELECT ... -- Anchor
    UNION ALL
    SELECT ... FROM cte WHERE ... -- Recursive
)
```

**Performance Tips:**
- Use EXISTS instead of IN for better NULL handling
- Consider CTEs for readability and potential caching
- Index columns used in subquery WHERE clauses
- Use LIMIT in subqueries when appropriate

---

**Next:** Learn about [Views & Materialized Views](views.md) for reusable complex queries, or explore [Schema Design](schema-design.md) for optimal database structure.