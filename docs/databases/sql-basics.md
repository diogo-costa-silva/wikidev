# SQL Basics

← [**Voltar para Databases**](index.md) | [**🏠 Home**](../index.md)

> **SELECT, WHERE, ORDER BY, LIMIT fundamentals that form the foundation of SQL.**

These are the core SQL commands you'll use in almost every query. Master these patterns and you'll be able to retrieve and filter data effectively from any database.

## 🎯 Goals

- **Basic query structure** with SELECT statements
- **Filtering data** with WHERE conditions
- **Sorting results** with ORDER BY
- **Limiting output** with LIMIT/TOP
- **Column operations** and aliases

## 🔍 Quick Verification

Test these concepts with a sample database:

```sql
-- Create test table
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT UNIQUE,
    age INTEGER,
    created_at DATE DEFAULT CURRENT_DATE
);

-- Insert sample data
INSERT INTO users (name, email, age) VALUES 
('Alice Johnson', 'alice@example.com', 28),
('Bob Smith', 'bob@example.com', 35),
('Carol Davis', 'carol@example.com', 22);

-- Test basic query
SELECT name, email FROM users WHERE age > 25 ORDER BY age;
```

## 📝 SELECT Statement Structure

### Basic Syntax
```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition
ORDER BY column
LIMIT number;
```

### Complete Query Order
SQL queries are written in this order:
1. **SELECT** - What columns to retrieve
2. **FROM** - Which table(s) to query
3. **WHERE** - Filter conditions
4. **GROUP BY** - Group rows (covered later)
5. **HAVING** - Filter groups (covered later)
6. **ORDER BY** - Sort results
7. **LIMIT** - Limit number of results

## 🔍 SELECT Clause

### Selecting Columns
```sql
-- Select specific columns
SELECT name, email FROM users;

-- Select all columns
SELECT * FROM users;

-- Select with expressions
SELECT name, age, age + 5 AS age_in_5_years FROM users;

-- Select distinct values
SELECT DISTINCT age FROM users;
```

### Column Aliases
```sql
-- Using AS keyword
SELECT name AS full_name, email AS email_address FROM users;

-- Without AS keyword (works but less clear)
SELECT name full_name, email email_address FROM users;

-- Aliases with spaces (use quotes)
SELECT name AS "Full Name", email AS "Email Address" FROM users;
```

### Calculated Columns
```sql
-- Arithmetic operations
SELECT name, age, age * 365 AS days_lived FROM users;

-- String concatenation
SELECT name || ' (' || email || ')' AS name_and_email FROM users;
-- Or using CONCAT function
SELECT CONCAT(name, ' (', email, ')') AS name_and_email FROM users;

-- Case statements
SELECT name, 
       CASE 
           WHEN age < 25 THEN 'Young'
           WHEN age < 35 THEN 'Adult'
           ELSE 'Mature'
       END AS age_group
FROM users;
```

## 🎯 WHERE Clause

### Comparison Operators
```sql
-- Equality
SELECT * FROM users WHERE age = 28;

-- Inequality
SELECT * FROM users WHERE age != 28;
SELECT * FROM users WHERE age <> 28;  -- Alternative syntax

-- Range comparisons
SELECT * FROM users WHERE age > 25;
SELECT * FROM users WHERE age >= 25;
SELECT * FROM users WHERE age < 30;
SELECT * FROM users WHERE age <= 30;

-- Between (inclusive)
SELECT * FROM users WHERE age BETWEEN 25 AND 35;
```

### String Matching
```sql
-- Exact match
SELECT * FROM users WHERE name = 'Alice Johnson';

-- Pattern matching with LIKE
SELECT * FROM users WHERE name LIKE 'A%';      -- Starts with A
SELECT * FROM users WHERE name LIKE '%son';    -- Ends with son
SELECT * FROM users WHERE name LIKE '%li%';    -- Contains li
SELECT * FROM users WHERE name LIKE '_ob%';    -- Second char is 'ob'

-- Case insensitive (database dependent)
SELECT * FROM users WHERE LOWER(name) LIKE '%alice%';
```

### NULL Handling
```sql
-- Check for NULL values
SELECT * FROM users WHERE email IS NULL;
SELECT * FROM users WHERE email IS NOT NULL;

-- NULL comparisons (these DON'T work as expected)
SELECT * FROM users WHERE email = NULL;    -- Wrong!
SELECT * FROM users WHERE email != NULL;   -- Wrong!
```

### Logical Operators
```sql
-- AND condition
SELECT * FROM users WHERE age > 25 AND name LIKE 'A%';

-- OR condition
SELECT * FROM users WHERE age < 25 OR age > 35;

-- NOT condition
SELECT * FROM users WHERE NOT age = 28;

-- Multiple conditions with parentheses
SELECT * FROM users WHERE (age < 25 OR age > 35) AND name LIKE '%a%';

-- IN operator
SELECT * FROM users WHERE age IN (22, 28, 35);

-- NOT IN operator
SELECT * FROM users WHERE age NOT IN (22, 28);
```

## 📊 ORDER BY Clause

### Basic Sorting
```sql
-- Sort ascending (default)
SELECT * FROM users ORDER BY age;
SELECT * FROM users ORDER BY age ASC;

-- Sort descending
SELECT * FROM users ORDER BY age DESC;

-- Sort by multiple columns
SELECT * FROM users ORDER BY age DESC, name ASC;
```

### Advanced Sorting
```sql
-- Sort by expression
SELECT * FROM users ORDER BY LENGTH(name);

-- Sort by column position (not recommended)
SELECT name, age FROM users ORDER BY 2, 1;  -- Sort by age, then name

-- Custom sort order
SELECT * FROM users 
ORDER BY CASE 
    WHEN name LIKE 'A%' THEN 1 
    WHEN name LIKE 'B%' THEN 2 
    ELSE 3 
END, name;
```

### NULL Handling in Sorting
```sql
-- NULLs typically sort last in ASC, first in DESC
SELECT * FROM users ORDER BY email;

-- Force NULL position (PostgreSQL/Oracle)
SELECT * FROM users ORDER BY email NULLS FIRST;
SELECT * FROM users ORDER BY email NULLS LAST;
```

## 🔢 LIMIT Clause

### Basic Limiting
```sql
-- Limit to first 5 rows
SELECT * FROM users LIMIT 5;

-- Get top 3 oldest users
SELECT * FROM users ORDER BY age DESC LIMIT 3;
```

### Pagination (OFFSET)
```sql
-- Skip first 10 rows, take next 5
SELECT * FROM users ORDER BY id LIMIT 5 OFFSET 10;

-- Alternative syntax (MySQL)
SELECT * FROM users ORDER BY id LIMIT 10, 5;  -- LIMIT offset, count
```

### Database-Specific Variations
```sql
-- SQL Server uses TOP
SELECT TOP 5 * FROM users ORDER BY age DESC;

-- Oracle uses ROWNUM or FETCH FIRST
SELECT * FROM users WHERE ROWNUM <= 5;
SELECT * FROM users ORDER BY age FETCH FIRST 5 ROWS ONLY;
```

## 💡 Practical Examples

### Data Exploration
```sql
-- Quick table overview
SELECT COUNT(*) AS total_users FROM users;
SELECT * FROM users LIMIT 5;

-- Check data distribution
SELECT DISTINCT age FROM users ORDER BY age;
SELECT MIN(age) AS youngest, MAX(age) AS oldest FROM users;

-- Find duplicates
SELECT email, COUNT(*) 
FROM users 
GROUP BY email 
HAVING COUNT(*) > 1;
```

### Common Query Patterns
```sql
-- Find users by name pattern
SELECT * FROM users 
WHERE name LIKE '%John%' 
ORDER BY name;

-- Get recent users (assuming created_at exists)
SELECT name, email, created_at 
FROM users 
WHERE created_at >= DATE('now', '-30 days')
ORDER BY created_at DESC;

-- Find users in age range
SELECT name, age 
FROM users 
WHERE age BETWEEN 25 AND 35 
ORDER BY age, name;
```

### Filtering and Ranking
```sql
-- Top 5 oldest users
SELECT name, age 
FROM users 
ORDER BY age DESC 
LIMIT 5;

-- Users whose name starts with vowel
SELECT * FROM users 
WHERE name REGEXP '^[AEIOU]'  -- MySQL
-- WHERE name LIKE '[AEIOU]%'   -- SQL Server
-- WHERE SUBSTR(name, 1, 1) IN ('A', 'E', 'I', 'O', 'U')  -- Universal
ORDER BY name;
```

## ⚠️ Common Mistakes

### 1. Forgetting Quotes
```sql
-- Wrong
SELECT * FROM users WHERE name = Alice;

-- Correct
SELECT * FROM users WHERE name = 'Alice';
```

### 2. NULL Comparisons
```sql
-- Wrong
SELECT * FROM users WHERE email = NULL;

-- Correct
SELECT * FROM users WHERE email IS NULL;
```

### 3. Case Sensitivity
```sql
-- May not match if case differs
SELECT * FROM users WHERE name = 'alice johnson';

-- Better for case-insensitive search
SELECT * FROM users WHERE LOWER(name) = LOWER('Alice Johnson');
```

### 4. ORDER BY with LIMIT
```sql
-- Unpredictable results without ORDER BY
SELECT * FROM users LIMIT 5;  -- Which 5 users?

-- Predictable results
SELECT * FROM users ORDER BY id LIMIT 5;  -- First 5 by ID
```

## 🔗 Related Sections

- **[Setup & Tools](setup-tools.md)** - Database client setup and data import
- **[Joins](joins.md)** - Combining data from multiple tables
- **[Aggregations](aggregations.md)** - GROUP BY, COUNT, SUM, and window functions
- **[Data Analysis](../data-analysis/index.md)** - Using SQL for data exploration

## ⚡ Quick Reference

**Basic Query Structure:**
```sql
SELECT column1, column2
FROM table_name
WHERE condition
ORDER BY column
LIMIT 10;
```

**Common WHERE Conditions:**
```sql
WHERE age = 25              -- Equality
WHERE age BETWEEN 20 AND 30 -- Range
WHERE name LIKE 'A%'        -- Pattern
WHERE email IS NOT NULL     -- NULL check
WHERE age IN (25, 30, 35)   -- Multiple values
```

**Sorting and Limiting:**
```sql
ORDER BY age DESC           -- Sort descending
ORDER BY age, name          -- Multiple columns
LIMIT 5                     -- First 5 rows
LIMIT 5 OFFSET 10          -- Skip 10, take 5
```

**Common Functions:**
```sql
COUNT(*)                    -- Count rows
DISTINCT column             -- Unique values
column AS alias             -- Column alias
CASE WHEN ... END          -- Conditional logic
```

---

**Next:** Learn about [Joins](joins.md) to combine data from multiple tables, or explore [Setup & Tools](setup-tools.md) for database client configuration.