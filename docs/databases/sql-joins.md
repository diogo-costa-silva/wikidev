# SQL Joins

> **INNER, LEFT, RIGHT, FULL joins and when to use each approach.**

Joins are fundamental to SQL - they let you combine data from multiple tables based on relationships. Understanding join types and when to use each one is essential for effective database queries.

## 🎯 Goals

- **Master all join types** and their use cases
- **Understand join logic** and result sets
- **Avoid common join mistakes** that cause incorrect results
- **Optimize join performance** with proper indexing

## 🔍 Quick Verification

Test joins with sample data:

```sql
-- Create test tables
CREATE TABLE customers (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    city TEXT
);

CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    product TEXT,
    amount DECIMAL(10,2),
    order_date DATE
);

-- Insert sample data
INSERT INTO customers (name, city) VALUES 
('Alice', 'New York'),
('Bob', 'London'),
('Carol', 'Paris');

INSERT INTO orders (customer_id, product, amount, order_date) VALUES 
(1, 'Laptop', 999.99, '2024-01-15'),
(1, 'Mouse', 25.99, '2024-01-16'),
(2, 'Keyboard', 79.99, '2024-01-17');

-- Test INNER JOIN
SELECT c.name, o.product, o.amount
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id;
```

## 🔄 Join Types Overview

### Visual Join Guide
```
Table A        Table B
-------        -------
1              1
2              2  
3              4

INNER:      LEFT:       RIGHT:      FULL:
------      -----       ------      -----
1           1           1           1
2           2           2           2
            3           4           3
                                    4
```

## 🎯 INNER JOIN

### Purpose
Returns only rows that have matching values in both tables.

### Syntax
```sql
SELECT columns
FROM table1 t1
INNER JOIN table2 t2 ON t1.key = t2.key;
```

### Examples
```sql
-- Basic inner join
SELECT c.name, o.product, o.amount
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id;

-- Multiple conditions
SELECT c.name, o.product, o.amount
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id
WHERE o.amount > 50 AND c.city = 'New York';

-- Join with aggregation
SELECT c.name, COUNT(o.id) as order_count, SUM(o.amount) as total_spent
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name;
```

### When to Use
- **Default choice** for most queries
- When you only want records that exist in both tables
- For reporting on actual relationships (customers who have orders)

## ⬅️ LEFT JOIN (LEFT OUTER JOIN)

### Purpose
Returns all rows from the left table, and matching rows from the right table. NULL values for non-matching right table columns.

### Syntax
```sql
SELECT columns
FROM table1 t1
LEFT JOIN table2 t2 ON t1.key = t2.key;
```

### Examples
```sql
-- All customers, with their orders (if any)
SELECT c.name, o.product, o.amount
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id;

-- Find customers with no orders
SELECT c.name, c.city
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.customer_id IS NULL;

-- Customer statistics (including those with 0 orders)
SELECT c.name, 
       COUNT(o.id) as order_count,
       COALESCE(SUM(o.amount), 0) as total_spent
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name;
```

### When to Use
- **Preserving all records** from the main table
- **Finding missing relationships** (customers without orders)
- **Complete reporting** (show all customers, even those with 0 orders)

## ➡️ RIGHT JOIN (RIGHT OUTER JOIN)

### Purpose
Returns all rows from the right table, and matching rows from the left table. NULL values for non-matching left table columns.

### Syntax
```sql
SELECT columns
FROM table1 t1
RIGHT JOIN table2 t2 ON t1.key = t2.key;
```

### Examples
```sql
-- All orders, with customer info (if available)
SELECT c.name, o.product, o.amount
FROM customers c
RIGHT JOIN orders o ON c.id = o.customer_id;

-- Find orders without valid customers (orphaned orders)
SELECT o.id, o.product, o.amount
FROM customers c
RIGHT JOIN orders o ON c.id = o.customer_id
WHERE c.id IS NULL;
```

### When to Use
- **Less common** than LEFT JOIN
- **Data quality checks** (finding orphaned records)
- **Can usually be rewritten** as LEFT JOIN by switching table order

## ↔️ FULL OUTER JOIN

### Purpose
Returns all rows when there's a match in either table. NULL values where no match exists.

### Syntax
```sql
SELECT columns
FROM table1 t1
FULL OUTER JOIN table2 t2 ON t1.key = t2.key;
```

### Examples
```sql
-- All customers and all orders, matched where possible
SELECT c.name, o.product, o.amount
FROM customers c
FULL OUTER JOIN orders o ON c.id = o.customer_id;

-- Find all mismatched data
SELECT c.name, o.product, o.customer_id
FROM customers c
FULL OUTER JOIN orders o ON c.id = o.customer_id
WHERE c.id IS NULL OR o.customer_id IS NULL;
```

### When to Use
- **Data reconciliation** between systems
- **Finding all mismatches** in both directions
- **Complete data auditing**

**Note:** Not all databases support FULL OUTER JOIN (notably MySQL). Use UNION of LEFT and RIGHT joins as alternative.

## 🚫 CROSS JOIN

### Purpose
Returns Cartesian product - every row from first table combined with every row from second table.

### Syntax
```sql
SELECT columns
FROM table1
CROSS JOIN table2;

-- Or implicit syntax (not recommended)
SELECT columns
FROM table1, table2;
```

### Examples
```sql
-- Generate all possible customer-product combinations
SELECT c.name, p.product_name, p.price
FROM customers c
CROSS JOIN products p;

-- Create date series for reporting
SELECT d.date, c.name
FROM date_series d
CROSS JOIN customers c
ORDER BY d.date, c.name;
```

### When to Use
- **Generating combinations** for analysis
- **Creating template data** (all dates × all customers)
- **Be careful** - can create huge result sets!

## 🔗 Multiple Joins

### Chaining Joins
```sql
-- Three table join
SELECT c.name, o.product, od.quantity, p.price
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id
INNER JOIN order_details od ON o.id = od.order_id
INNER JOIN products p ON od.product_id = p.id;

-- Mixed join types
SELECT c.name, o.product, od.quantity
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
LEFT JOIN order_details od ON o.id = od.order_id
WHERE c.city = 'New York';
```

### Self Joins
```sql
-- Find employees and their managers
SELECT e.name as employee, m.name as manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;

-- Find customers from the same city
SELECT c1.name as customer1, c2.name as customer2, c1.city
FROM customers c1
INNER JOIN customers c2 ON c1.city = c2.city AND c1.id < c2.id;
```

## ⚠️ Common Join Mistakes

### 1. Forgetting Join Conditions
```sql
-- Wrong - Cartesian product
SELECT c.name, o.product
FROM customers c, orders o;

-- Correct
SELECT c.name, o.product
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id;
```

### 2. Wrong Join Type
```sql
-- Wrong - Missing customers without orders
SELECT c.name, COUNT(o.id) as order_count
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id
GROUP BY c.name;

-- Correct - Shows all customers
SELECT c.name, COUNT(o.id) as order_count
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
GROUP BY c.name;
```

### 3. NULL Handling in Conditions
```sql
-- Wrong - Won't find records where o.customer_id IS NULL
SELECT c.name
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.customer_id != 1;

-- Correct - Handle NULLs properly
SELECT c.name
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.customer_id != 1 OR o.customer_id IS NULL;
```

## 🚀 Join Performance Tips

### Use Proper Indexes
```sql
-- Index foreign key columns
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
CREATE INDEX idx_customers_id ON customers(id);
```

### Filter Early
```sql
-- Good - Filter before join
SELECT c.name, o.product
FROM customers c
INNER JOIN (
    SELECT * FROM orders WHERE order_date >= '2024-01-01'
) o ON c.id = o.customer_id
WHERE c.city = 'New York';
```

### Choose Efficient Join Order
```sql
-- Put most selective table first
SELECT c.name, o.product
FROM (
    SELECT * FROM customers WHERE city = 'New York'
) c
INNER JOIN orders o ON c.id = o.customer_id;
```

## 💡 Practical Examples

### Business Reporting
```sql
-- Customer order summary
SELECT c.name,
       COUNT(o.id) as total_orders,
       COALESCE(SUM(o.amount), 0) as total_spent,
       MAX(o.order_date) as last_order_date
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name
ORDER BY total_spent DESC;
```

### Data Quality Checks
```sql
-- Find orphaned orders (orders without valid customers)
SELECT o.id, o.product, o.customer_id
FROM orders o
LEFT JOIN customers c ON o.customer_id = c.id
WHERE c.id IS NULL;

-- Find customers without any orders
SELECT c.name, c.city
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.customer_id IS NULL;
```

### Analytics Queries
```sql
-- Monthly sales by city
SELECT c.city,
       strftime('%Y-%m', o.order_date) as month,
       COUNT(o.id) as order_count,
       SUM(o.amount) as monthly_sales
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id
GROUP BY c.city, strftime('%Y-%m', o.order_date)
ORDER BY c.city, month;
```

## 🔗 Related Sections

- **[Query Basics](query-basics.md)** - Foundation SQL concepts
- **[Aggregations](aggregations.md)** - GROUP BY with joins
- **[Subqueries & CTEs](subqueries-ctes.md)** - Alternative to complex joins
- **[Schema Design](schema-design.md)** - Designing relationships for efficient joins

## ⚡ Quick Reference

**Join Types:**
```sql
INNER JOIN    -- Only matching records
LEFT JOIN     -- All from left + matching from right
RIGHT JOIN    -- All from right + matching from left  
FULL JOIN     -- All records from both tables
CROSS JOIN    -- Cartesian product (all combinations)
```

**Basic Patterns:**
```sql
-- Standard inner join
FROM table1 t1
INNER JOIN table2 t2 ON t1.id = t2.table1_id

-- Find missing relationships
FROM table1 t1
LEFT JOIN table2 t2 ON t1.id = t2.table1_id
WHERE t2.table1_id IS NULL

-- Multiple joins
FROM table1 t1
INNER JOIN table2 t2 ON t1.id = t2.table1_id
INNER JOIN table3 t3 ON t2.id = t3.table2_id
```

**Performance Tips:**
- Index foreign key columns
- Filter early with WHERE conditions
- Use appropriate join type for your needs
- Consider subqueries for complex logic

---

**Next:** Learn about [Aggregations & Window Functions](aggregations.md) for advanced data analysis, or explore [Query Basics](query-basics.md) for foundational concepts.