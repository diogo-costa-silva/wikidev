# Databases

← [**🏠 Home**](../index.md)

> **Comprehensive guide to databases, SQL, and data management tools.**

Master database fundamentals, write efficient SQL queries, and work confidently with database management tools. This section covers everything from basic concepts to advanced optimization techniques.

## 🎯 Goals

- **SQL mastery** - Write clear, efficient queries that work across database systems
- **Database design** - Create well-structured, maintainable database schemas
- **Tool proficiency** - Use GUI and command-line tools effectively
- **Performance awareness** - Optimize queries and understand database performance

## 🔍 Quick Verification

Test your database environment:

```bash
# Check available database clients
sqlite3 --version           # SQLite (usually pre-installed)
psql --version             # PostgreSQL
mysql --version            # MySQL
mongosh --version          # MongoDB

# Test basic SQL functionality
sqlite3 ":memory:" "SELECT 'Databases are working!' as message;"

# Check GUI tool (DBeaver)
# Launch DBeaver from applications or command line
```

**Quick Test Database:**
```sql
-- Create sample tables
CREATE TABLE authors (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT UNIQUE
);

CREATE TABLE books (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    author_id INTEGER,
    published_year INTEGER,
    FOREIGN KEY (author_id) REFERENCES authors(id)
);

-- Insert sample data
INSERT INTO authors (name, email) VALUES 
('Jane Doe', 'jane@example.com'),
('John Smith', 'john@example.com');

INSERT INTO books (title, author_id, published_year) VALUES
('Database Design Basics', 1, 2023),
('Advanced SQL Techniques', 2, 2022);

-- Test join query
SELECT b.title, a.name as author_name 
FROM books b 
JOIN authors a ON b.author_id = a.id;
```

## 🗄️ Database Fundamentals

### Getting Started
- **[Database Setup & Tools](setup-tools.md)** - Install and configure database systems and tools
- **[SQL Basics](sql-basics.md)** - Essential queries: SELECT, WHERE, ORDER BY, GROUP BY
- **[Schema Design](schema-design.md)** - Tables, relationships, normalization, and constraints

### Core SQL Skills
- **[SQL Joins](sql-joins.md)** - INNER, LEFT, RIGHT, FULL joins and practical patterns
- **[SQL Aggregations](sql-aggregations.md)** - GROUP BY, window functions, analytics queries
- **[SQL Subqueries & CTEs](sql-subqueries-ctes.md)** - Common Table Expressions and nested queries

### Database Tools
- **[DBeaver](dbeaver.md)** - Universal database GUI for development and analysis
- **[Performance & Optimization](performance.md)** - Query optimization and database tuning

## 🚀 Common Database Workflows

### Development Setup
```sql
-- 1. Local development database
CREATE DATABASE myapp_dev;
USE myapp_dev;

-- 2. Create application tables
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 3. Add indexes for performance
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_username ON users(username);

-- 4. Insert test data
INSERT INTO users (username, email) VALUES
('testuser1', 'test1@example.com'),
('testuser2', 'test2@example.com');
```

### Data Analysis Workflow
```sql
-- 1. Explore data structure
SELECT column_name, data_type, is_nullable
FROM information_schema.columns 
WHERE table_name = 'sales';

-- 2. Basic data quality checks
SELECT 
    COUNT(*) as total_rows,
    COUNT(DISTINCT customer_id) as unique_customers,
    MIN(order_date) as earliest_order,
    MAX(order_date) as latest_order
FROM sales;

-- 3. Business insights
SELECT 
    DATE_TRUNC('month', order_date) as month,
    COUNT(*) as total_orders,
    SUM(amount) as total_revenue,
    AVG(amount) as avg_order_value
FROM sales 
GROUP BY DATE_TRUNC('month', order_date)
ORDER BY month;
```

### Database Migration Pattern
```sql
-- Version-controlled database changes
-- migration_001_create_users.sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- migration_002_add_email_to_users.sql
ALTER TABLE users ADD COLUMN email VARCHAR(100);
CREATE UNIQUE INDEX idx_users_email ON users(email);

-- Track applied migrations
CREATE TABLE migrations (
    id SERIAL PRIMARY KEY,
    filename VARCHAR(255) NOT NULL,
    applied_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 🎯 Database Types & Use Cases

### Relational Databases (SQL)
**PostgreSQL**
- Best for: Complex queries, JSON data, extensions
- Strengths: ACID compliance, advanced features, open source
- Use cases: Web applications, analytics, data warehousing

**MySQL**
- Best for: Web applications, high read loads
- Strengths: Performance, replication, wide adoption  
- Use cases: WordPress, e-commerce, content management

**SQLite**
- Best for: Development, small applications, embedded systems
- Strengths: Zero configuration, serverless, single file
- Use cases: Mobile apps, prototyping, testing

### NoSQL Databases
**MongoDB** *(coming soon)*
- Document-based, flexible schema
- Best for: Rapid development, hierarchical data

**Redis** *(coming soon)*
- In-memory key-value store
- Best for: Caching, sessions, real-time features

## 🔗 Related Sections

- **[Data Analysis](../data-analysis/index.md)** - Use SQL results for analysis and visualization
- **[Python](../python/index.md)** - Connect Python to databases with pandas and SQLAlchemy
- **[AI Tools](../ai-tools/index.md)** - AI-assisted SQL query writing and optimization
- **[Toolkit](../toolkit/index.md)** - Development tools that integrate with databases

## ⚡ Quick References

**Essential SQL Patterns:**
```sql
-- Data exploration
SELECT * FROM table_name LIMIT 10;
SELECT COUNT(*), COUNT(DISTINCT column) FROM table_name;
SELECT column, COUNT(*) FROM table_name GROUP BY column ORDER BY COUNT(*) DESC;

-- Common joins
SELECT a.*, b.name 
FROM orders a 
INNER JOIN customers b ON a.customer_id = b.id;

-- Window functions
SELECT name, salary, 
       RANK() OVER (ORDER BY salary DESC) as salary_rank
FROM employees;

-- Filtering and aggregation
SELECT category, AVG(price) as avg_price
FROM products 
WHERE price > 10
GROUP BY category
HAVING AVG(price) > 50;
```

**DBeaver Shortcuts:**
```bash
Ctrl+Enter         # Execute current statement
Ctrl+Shift+Enter   # Execute all statements
F4                 # Edit data inline
Ctrl+F             # Find/replace in results
F5                 # Refresh data
```

**Performance Tips:**
```sql
-- Use EXPLAIN to understand query plans
EXPLAIN ANALYZE SELECT * FROM large_table WHERE indexed_column = 'value';

-- Add indexes for frequently queried columns
CREATE INDEX idx_orders_customer_id ON orders(customer_id);

-- Use LIMIT for large result sets
SELECT * FROM large_table ORDER BY created_at DESC LIMIT 100;
```

## 📋 Database Design Principles

### Schema Design Best Practices
1. **Normalize to reduce redundancy** - Follow 3NF principles
2. **Use appropriate data types** - VARCHAR vs TEXT, INT vs BIGINT
3. **Add constraints** - PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK
4. **Plan for growth** - Consider scaling and performance from start
5. **Document relationships** - Clear naming and documentation

### Security Considerations
```sql
-- Create application-specific users
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT SELECT, INSERT, UPDATE, DELETE ON myapp.* TO 'app_user'@'localhost';

-- Never store sensitive data in plain text
-- Use proper hashing for passwords
-- Implement role-based access control
```

## 🚀 Getting Started Path

### For Beginners
1. **[Database Setup & Tools](setup-tools.md)** - Install SQLite and DBeaver
2. **[SQL Basics](sql-basics.md)** - Master SELECT, WHERE, ORDER BY
3. **[Schema Design](schema-design.md)** - Understand tables and relationships
4. **[SQL Joins](sql-joins.md)** - Combine data from multiple tables

### For Developers
1. **[SQL Aggregations](sql-aggregations.md)** - Analytics and reporting queries
2. **[SQL Subqueries & CTEs](sql-subqueries-ctes.md)** - Complex query patterns
3. **[DBeaver](dbeaver.md)** - GUI tool for database management
4. **[Performance](performance.md)** - Optimize queries and database design

### For Data Analysts
1. **[DBeaver](dbeaver.md)** - Set up analysis environment
2. **[SQL Aggregations](sql-aggregations.md)** - Window functions and analytics
3. **[Data Analysis Integration](../data-analysis/index.md)** - Export to Python/pandas
4. **[Performance](performance.md)** - Handle large datasets efficiently

---

**Start here:** If you're new to databases, begin with [Database Setup & Tools](setup-tools.md) to set up your environment, then move to [SQL Basics](sql-basics.md) to learn fundamental queries.