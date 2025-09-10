# DBeaver - Universal Database Tool

DBeaver is a free, open-source universal database tool for developers, database administrators, and analysts. It supports virtually all popular databases and provides a comprehensive set of features for database management.

## Installation

### Download DBeaver
```bash
# macOS (Homebrew)
brew install --cask dbeaver-community

# Windows (Winget)
winget install dbeaver.dbeaver

# Linux (Snap)
sudo snap install dbeaver-ce

# Manual Download
# Visit: https://dbeaver.io/download/
# Download the appropriate installer for your OS
```

### Verify Installation
```bash
# Check if DBeaver is installed (varies by OS)
# macOS
open -a DBeaver

# Linux
dbeaver &

# Windows
# Check Start Menu or Desktop shortcut
```

## First Time Setup

### Create Your First Connection
1. **Launch DBeaver**
2. **New Database Connection** (Click the plug icon or File → New → Database Connection)
3. **Select Database Type** (PostgreSQL, MySQL, SQLite, etc.)
4. **Configure Connection Settings**

### Connection Configuration Examples

**PostgreSQL:**
```
Server Host: localhost
Port: 5432
Database: mydb
Username: postgres
Password: [your-password]
```

**MySQL:**
```
Server Host: localhost
Port: 3306
Database: mydb
Username: root
Password: [your-password]
```

**SQLite:**
```
Path: /path/to/database.db
```

## Basic Operations

### Database Navigation
```sql
-- Expand database tree in navigator
-- Right-click on database → Browse data

-- View table structure
-- Right-click on table → View Properties

-- Quick data preview
-- Double-click on table name
```

### Query Execution
```sql
-- Open SQL editor
-- File → New → SQL Script or Ctrl+Shift+L

-- Execute query
-- Ctrl+Enter (current statement)
-- Ctrl+Alt+X (execute all)

-- Example queries
SELECT * FROM users LIMIT 10;

SELECT 
    name, 
    email, 
    created_at 
FROM users 
WHERE active = true
ORDER BY created_at DESC;

-- Execute with parameters
SELECT * FROM orders 
WHERE order_date >= ? 
AND customer_id = ?;
-- DBeaver will prompt for parameter values
```

### Data Editing
```sql
-- Edit data directly in result grid
-- Double-click cell to edit
-- Press Tab to move to next cell
-- Ctrl+S to save changes

-- Bulk operations
-- Right-click in result grid
-- Generate SQL → INSERT/UPDATE/DELETE statements
```

## Advanced Features

### ER Diagrams
```sql
-- Generate ER Diagram
-- Right-click on database → Generate ER Diagram

-- Customize diagram
-- Add/remove tables
-- Adjust layout
-- Export as image/PDF
```

### Data Import/Export

**Import CSV:**
```sql
-- Right-click on table → Import Data
-- Select CSV format
-- Configure column mapping
-- Set data types
-- Execute import

-- CSV import example structure
CREATE TABLE temp_users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    age INTEGER
);
```

**Export Data:**
```sql
-- Select data in result grid
-- Right-click → Export Data
-- Choose format (CSV, JSON, XML, SQL)
-- Configure export settings

-- Export formats:
-- - CSV: Comma-separated values
-- - JSON: Structured data format
-- - XML: Markup format
-- - SQL: INSERT statements
```

### Query Management
```sql
-- Save frequently used queries
-- File → Save As → Save as script

-- Organize scripts in folders
-- Project Explorer → Scripts → New Folder

-- Query history
-- View → Query History (Ctrl+H)

-- Query bookmarks
-- Add bookmark: Ctrl+D in SQL editor
-- View bookmarks: View → Bookmarks
```

## Database-Specific Features

### PostgreSQL
```sql
-- View PostgreSQL-specific objects
-- Functions, procedures, triggers
-- Extensions, schemas, tablespaces

-- Execute stored procedures
CALL my_procedure(param1, param2);

-- Work with JSON data
SELECT data->>'name' as name
FROM json_table 
WHERE data->'active' = 'true';

-- Array operations
SELECT unnest(tags) as tag 
FROM articles;
```

### MySQL
```sql
-- MySQL workbench-like features
-- Visual query builder
-- Performance analysis

-- View engine information
SHOW TABLE STATUS;

-- Work with JSON (MySQL 5.7+)
SELECT JSON_EXTRACT(data, '$.name') as name
FROM json_table;

-- Full-text search
SELECT * FROM articles 
WHERE MATCH(title, content) 
AGAINST('search term' IN NATURAL LANGUAGE MODE);
```

### SQLite
```sql
-- Attach multiple databases
ATTACH DATABASE 'other.db' AS other;

-- Query across databases
SELECT u.name, p.title 
FROM users u
JOIN other.posts p ON u.id = p.user_id;

-- Analyze database
ANALYZE;

-- Vacuum database
VACUUM;
```

### MongoDB (NoSQL)
```javascript
// DBeaver supports MongoDB through plugins
// Enable MongoDB plugin in preferences

// Basic queries
db.users.find({active: true})

// Aggregation pipelines
db.orders.aggregate([
    {$match: {status: "completed"}},
    {$group: {_id: "$customer_id", total: {$sum: "$amount"}}},
    {$sort: {total: -1}}
])

// Insert documents
db.users.insertOne({
    name: "John Doe",
    email: "john@example.com",
    active: true
})
```

## Performance Analysis

### Query Performance
```sql
-- Enable execution plan
-- Preferences → SQL Editor → Execute → Show execution plan

-- Analyze slow queries
EXPLAIN ANALYZE 
SELECT u.name, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.name;

-- PostgreSQL specific
EXPLAIN (ANALYZE, BUFFERS) 
SELECT * FROM large_table WHERE indexed_column = 'value';
```

### Connection Monitoring
```sql
-- Monitor active connections
-- Window → Perspective → Database Perspective
-- View connection status in navigator

-- Connection pool settings
-- Edit connection → Connection settings → Connection
-- Set max connections, timeout values
```

## Data Modeling

### Schema Design
```sql
-- Create new schema/database
-- Right-click in navigator → Create → Schema

CREATE SCHEMA ecommerce;

-- Design tables visually
-- Right-click on schema → Create → Table
-- Use visual table editor

CREATE TABLE ecommerce.products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    category_id INTEGER REFERENCES categories(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT positive_price CHECK (price > 0)
);
```

### Relationship Management
```sql
-- Create foreign key constraints
ALTER TABLE orders 
ADD CONSTRAINT fk_customer 
FOREIGN KEY (customer_id) REFERENCES customers(id);

-- View relationships in ER diagram
-- Navigate → Generate ER Diagram

-- Create indexes for performance
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
CREATE INDEX idx_products_name ON products(name);
```

## Development Workflows

### Version Control Integration
```sql
-- Connect to Git repository
-- File → Import → General → Projects from Git

-- Track SQL scripts in version control
-- Keep connection configurations separate
-- Use environment variables for sensitive data
```

### Team Collaboration
```sql
-- Export connection configurations
-- File → Export → DBeaver → Connections
-- Share with team (excluding passwords)

-- Shared script folders
-- Project → Properties → Resource → Linked Resources
-- Link to shared network drive or Git repository

-- Code formatting
-- Format SQL: Ctrl+Shift+F
-- Configure formatting in Preferences → SQL Editor → Formatting
```

### Development Best Practices
```sql
-- Use transactions for data changes
BEGIN;
UPDATE products SET price = price * 1.1 WHERE category = 'electronics';
-- Review changes before commit
COMMIT;
-- or ROLLBACK if needed

-- Test queries on small datasets first
SELECT * FROM large_table LIMIT 100;

-- Use parameterized queries to prevent SQL injection
SELECT * FROM users WHERE id = ? AND active = ?;
```

## Extensions and Plugins

### Popular Extensions
```bash
# Install extensions through DBeaver
# Help → Install New Software
# Select DBeaver extensions repository

# Popular extensions:
# - Cloud databases (AWS RDS, Azure, GCP)
# - NoSQL databases (MongoDB, Cassandra)
# - Big data (Apache Spark, Hadoop)
# - Office formats (Excel import/export)
```

### Custom Drivers
```sql
-- Add custom JDBC drivers
-- Database → Driver Manager → New

-- Example: Add custom PostgreSQL driver
-- Driver Name: PostgreSQL Custom
-- Class Name: org.postgresql.Driver
-- URL Template: jdbc:postgresql://{host}[:{port}]/[{database}]
-- Add JAR files for the driver
```

## Troubleshooting

### Connection Issues
```sql
-- Test connection
-- Edit Connection → Test Connection

-- Common connection problems:
-- 1. Wrong host/port
-- 2. Firewall blocking connection
-- 3. Database not running
-- 4. Wrong credentials
-- 5. SSL/TLS configuration

-- Debug with connection properties
-- Advanced → Connection Properties
-- Add: ssl=true, sslmode=require
```

### Performance Issues
```sql
-- Adjust memory settings
-- Preferences → DBeaver → Memory
-- Increase heap size for large datasets

-- Optimize query results
-- Preferences → SQL Editor → Results
-- Set result set limit (default 200,000 rows)

-- Use result pagination
-- Large datasets automatically paginated
-- Navigate with toolbar buttons
```

### Data Type Issues
```sql
-- Handle special data types
-- JSON, UUID, arrays, geometric types

-- PostgreSQL UUID
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
SELECT uuid_generate_v4();

-- Handle time zones
SELECT NOW() AT TIME ZONE 'UTC';

-- Binary data
-- DBeaver automatically handles BLOB/binary data
-- Can view/export as hex or save to file
```

## Integration with Development Tools

### VS Code Integration
```json
// Use DBeaver connections in VS Code
// Install SQLTools extension
// Import DBeaver connections:

{
    "sqltools.connections": [
        {
            "name": "PostgreSQL Local",
            "driver": "PostgreSQL",
            "host": "localhost",
            "port": 5432,
            "database": "mydb",
            "username": "postgres"
        }
    ]
}
```

### Command Line Tools
```bash
# Export data via command line
# Use DBeaver CLI (commercial version)
# or integrate with database-specific CLI tools

# PostgreSQL
psql -d mydb -c "COPY users TO '/tmp/users.csv' CSV HEADER;"

# MySQL
mysql -e "SELECT * FROM users;" mydb > users.txt

# SQLite
sqlite3 mydb.db ".mode csv" ".header on" ".output users.csv" "SELECT * FROM users;"
```

## Advanced SQL Features

### Window Functions
```sql
-- Ranking functions
SELECT 
    name,
    salary,
    RANK() OVER (ORDER BY salary DESC) as salary_rank,
    ROW_NUMBER() OVER (ORDER BY salary DESC) as row_num
FROM employees;

-- Running totals
SELECT 
    date,
    amount,
    SUM(amount) OVER (ORDER BY date) as running_total
FROM transactions;
```

### Common Table Expressions (CTEs)
```sql
-- Recursive CTE for hierarchical data
WITH RECURSIVE employee_hierarchy AS (
    -- Base case: top-level managers
    SELECT id, name, manager_id, 1 as level
    FROM employees 
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case: employees with managers
    SELECT e.id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.id
)
SELECT * FROM employee_hierarchy ORDER BY level, name;
```

### JSON Operations (PostgreSQL)
```sql
-- Query JSON data
SELECT 
    id,
    data->>'name' as name,
    data->>'email' as email,
    jsonb_array_length(data->'tags') as tag_count
FROM users 
WHERE data->>'active' = 'true';

-- Update JSON data
UPDATE users 
SET data = jsonb_set(data, '{last_login}', '"2023-12-01"')
WHERE id = 1;
```

## Backup and Recovery

### Database Backup
```sql
-- PostgreSQL backup via DBeaver
-- Right-click on database → Tools → Backup
-- Select backup format and options

-- Manual backup commands
-- PostgreSQL
pg_dump -h localhost -U postgres -d mydb > backup.sql

-- MySQL
mysqldump -u root -p mydb > backup.sql

-- Restore from backup
-- Right-click on database → Tools → Restore
```

### Data Export Strategies
```sql
-- Regular data exports
-- Create scheduled exports using DBeaver task scheduler
-- Right-click on connection → Tasks → Create Task

-- Export specific tables
SELECT * INTO OUTFILE '/tmp/customers.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
FROM customers;
```

## Security Best Practices

### Connection Security
```sql
-- Use SSL connections
-- Edit Connection → SSL → Use SSL
-- Configure certificate validation

-- Connection pooling security
-- Limit connection lifetime
-- Set appropriate timeout values

-- Credential management
-- Use connection variables for passwords
-- Don't save passwords in shared configs
```

### Query Security
```sql
-- Use parameterized queries
-- Avoid dynamic SQL construction
-- Example of safe parameterized query:
SELECT * FROM users 
WHERE username = ? AND password = crypt(?, password);

-- Review query permissions
-- Grant minimal necessary privileges
GRANT SELECT ON products TO readonly_user;
GRANT INSERT, UPDATE ON orders TO app_user;
```

## Resources and Community

**Official Resources:**
- [DBeaver Official Website](https://dbeaver.io/)
- [DBeaver Documentation](https://dbeaver.io/docs/)
- [DBeaver GitHub Repository](https://github.com/dbeaver/dbeaver)

**Learning Resources:**
- [DBeaver Video Tutorials](https://dbeaver.io/docs/video-tutorials/)
- [Database-specific guides](https://dbeaver.io/docs/database-guides/)

**Related Tools:**
- **[SQL Fundamentals](../sql/)** - Core SQL concepts and syntax
- **[PostgreSQL Tools](https://www.postgresql.org/download/)** - Database-specific tooling
- **[MySQL Workbench](https://dev.mysql.com/downloads/workbench/)** - MySQL-specific alternative