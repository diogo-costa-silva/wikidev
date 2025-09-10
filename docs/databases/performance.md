# Database Performance & Optimization

Master database performance optimization through query analysis, indexing strategies, and database tuning techniques.

## Query Performance Analysis

### Understanding EXPLAIN
```sql
-- PostgreSQL EXPLAIN
EXPLAIN SELECT * FROM orders WHERE customer_id = 123;

-- More detailed analysis
EXPLAIN ANALYZE SELECT 
    o.id, o.order_date, c.name 
FROM orders o 
JOIN customers c ON o.customer_id = c.id 
WHERE o.status = 'completed';

-- Include buffer information
EXPLAIN (ANALYZE, BUFFERS) 
SELECT * FROM large_table WHERE indexed_column = 'value';
```

**Reading EXPLAIN Output:**
```sql
-- Example output:
Seq Scan on orders  (cost=0.00..458.00 rows=1000 width=32) (actual time=0.123..5.456 rows=1000 loops=1)
  Filter: (status = 'completed')
  Rows Removed by Filter: 500
Planning Time: 0.234 ms
Execution Time: 5.789 ms

-- Key metrics:
-- cost: Query planner's cost estimate (startup..total)
-- rows: Estimated number of rows
-- width: Average row size in bytes
-- actual time: Real execution time (first row..last row)
-- loops: Number of times this node was executed
```

### MySQL Query Analysis
```sql
-- MySQL EXPLAIN
EXPLAIN SELECT * FROM orders WHERE customer_id = 123;

-- More detailed format
EXPLAIN FORMAT=JSON 
SELECT o.*, c.name 
FROM orders o 
JOIN customers c ON o.customer_id = c.id;

-- Analyze actual execution
EXPLAIN ANALYZE 
SELECT * FROM orders 
WHERE order_date BETWEEN '2023-01-01' AND '2023-12-31';
```

### SQLite Query Analysis
```sql
-- SQLite EXPLAIN QUERY PLAN
.eqp on
SELECT * FROM orders WHERE customer_id = 123;

-- Or explicitly:
EXPLAIN QUERY PLAN 
SELECT o.*, c.name 
FROM orders o 
JOIN customers c ON o.customer_id = c.id;
```

## Indexing Strategies

### Index Types and Usage

**B-Tree Indexes (Default):**
```sql
-- Single column index
CREATE INDEX idx_orders_customer_id ON orders(customer_id);

-- Composite index (column order matters!)
CREATE INDEX idx_orders_status_date ON orders(status, order_date);

-- Query that uses the composite index efficiently:
SELECT * FROM orders 
WHERE status = 'pending' AND order_date >= '2023-01-01';

-- Query that can still use the index (leftmost prefix):
SELECT * FROM orders WHERE status = 'pending';

-- Query that CANNOT use the index efficiently:
SELECT * FROM orders WHERE order_date >= '2023-01-01';  -- Missing status
```

**Partial Indexes (PostgreSQL):**
```sql
-- Index only active users
CREATE INDEX idx_active_users ON users(email) WHERE is_active = TRUE;

-- Index only recent orders
CREATE INDEX idx_recent_orders ON orders(customer_id) 
WHERE order_date >= '2023-01-01';

-- Index non-null values only
CREATE INDEX idx_users_phone ON users(phone) WHERE phone IS NOT NULL;
```

**Functional Indexes:**
```sql
-- Case-insensitive email searches
CREATE INDEX idx_users_email_lower ON users(LOWER(email));

-- Query using the functional index:
SELECT * FROM users WHERE LOWER(email) = 'user@example.com';

-- JSON field indexing (PostgreSQL)
CREATE INDEX idx_products_category ON products 
USING GIN ((metadata->>'category'));

-- Query JSON field:
SELECT * FROM products WHERE metadata->>'category' = 'electronics';
```

### Index Optimization Patterns

**Covering Indexes:**
```sql
-- Include frequently selected columns in the index
CREATE INDEX idx_orders_covering ON orders(customer_id, status) 
INCLUDE (order_date, total_amount);

-- This query can be satisfied entirely from the index:
SELECT customer_id, status, order_date, total_amount 
FROM orders 
WHERE customer_id = 123 AND status = 'completed';
```

**Index Only Scans:**
```sql
-- Create index that covers the entire query
CREATE INDEX idx_orders_summary ON orders(customer_id, status, total_amount);

-- Query that can use index-only scan:
SELECT customer_id, SUM(total_amount) 
FROM orders 
WHERE status = 'completed' 
GROUP BY customer_id;
```

## Query Optimization Techniques

### Efficient WHERE Clauses
```sql
-- ✅ Good: Use indexed columns in WHERE
SELECT * FROM orders WHERE customer_id = 123;

-- ✅ Good: Range queries on indexed columns
SELECT * FROM orders 
WHERE order_date BETWEEN '2023-01-01' AND '2023-12-31';

-- ❌ Avoid: Functions in WHERE clause (prevents index usage)
SELECT * FROM orders WHERE YEAR(order_date) = 2023;

-- ✅ Better: Use range instead
SELECT * FROM orders 
WHERE order_date >= '2023-01-01' AND order_date < '2024-01-01';

-- ❌ Avoid: Leading wildcards
SELECT * FROM customers WHERE name LIKE '%Smith';

-- ✅ Better: Use full-text search or trigram indexes
SELECT * FROM customers WHERE to_tsvector(name) @@ to_tsquery('Smith');
```

### JOIN Optimization
```sql
-- ✅ Good: JOIN on indexed columns
SELECT o.*, c.name 
FROM orders o 
JOIN customers c ON o.customer_id = c.id 
WHERE o.status = 'pending';

-- Ensure both join columns are indexed:
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
-- customers.id is already indexed (primary key)

-- ✅ Good: Filter early to reduce join size
SELECT o.*, c.name 
FROM orders o 
JOIN customers c ON o.customer_id = c.id 
WHERE o.status = 'pending'          -- Filter orders first
  AND c.region = 'North America';   -- Then filter customers

-- ❌ Avoid: Cartesian products
-- Always include JOIN conditions!
```

### Subquery vs JOIN Performance
```sql
-- Subquery approach:
SELECT * FROM customers 
WHERE id IN (
    SELECT customer_id FROM orders WHERE status = 'completed'
);

-- JOIN approach (often faster):
SELECT DISTINCT c.* 
FROM customers c 
JOIN orders o ON c.id = o.customer_id 
WHERE o.status = 'completed';

-- EXISTS is often fastest for existence checks:
SELECT * FROM customers c 
WHERE EXISTS (
    SELECT 1 FROM orders o 
    WHERE o.customer_id = c.id AND o.status = 'completed'
);
```

### Pagination Optimization
```sql
-- ❌ Slow: OFFSET becomes slower with larger offsets
SELECT * FROM orders ORDER BY created_at DESC LIMIT 20 OFFSET 10000;

-- ✅ Fast: Cursor-based pagination
SELECT * FROM orders 
WHERE created_at < '2023-01-01 10:30:00'  -- cursor from previous page
ORDER BY created_at DESC 
LIMIT 20;

-- ✅ Alternative: Use ID-based pagination
SELECT * FROM orders 
WHERE id > 5000  -- last ID from previous page
ORDER BY id 
LIMIT 20;
```

## Database Configuration Tuning

### PostgreSQL Optimization
```sql
-- postgresql.conf key settings
shared_buffers = 256MB              -- 25% of RAM for dedicated server
work_mem = 4MB                      -- Per-query memory for sorts/hashes
maintenance_work_mem = 64MB         -- Memory for maintenance operations
effective_cache_size = 1GB          -- OS cache size estimate
random_page_cost = 1.1              -- SSD: 1.1, HDD: 4.0

-- Connection settings
max_connections = 100               -- Adjust based on workload
checkpoint_completion_target = 0.9  -- Spread checkpoints over time

-- Query planning
default_statistics_target = 100     -- Statistics detail level
```

**PostgreSQL Monitoring Queries:**
```sql
-- Check buffer hit ratio (should be > 99%)
SELECT 
    sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) * 100 AS hit_ratio
FROM pg_statio_user_tables;

-- Find slow queries
SELECT query, calls, total_time, mean_time
FROM pg_stat_statements 
ORDER BY total_time DESC 
LIMIT 10;

-- Check index usage
SELECT 
    schemaname, tablename, indexname, idx_tup_read, idx_tup_fetch
FROM pg_stat_user_indexes 
ORDER BY idx_tup_read DESC;
```

### MySQL Optimization
```sql
-- my.cnf key settings
[mysqld]
innodb_buffer_pool_size = 256M      -- 70-80% of RAM for dedicated server
innodb_log_file_size = 64M          -- Large enough for peak write load
innodb_flush_log_at_trx_commit = 2  -- Better performance, slight durability risk
query_cache_size = 32M              -- Cache repeated SELECT statements
tmp_table_size = 32M                -- In-memory temporary table size
max_heap_table_size = 32M           -- Maximum MEMORY table size
```

**MySQL Monitoring Queries:**
```sql
-- Check buffer pool hit ratio
SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool_read%';

-- Find slow queries (enable slow_query_log first)
SELECT * FROM mysql.slow_log 
ORDER BY start_time DESC 
LIMIT 10;

-- Check table and index sizes
SELECT 
    table_name,
    ROUND(data_length / 1024 / 1024, 2) AS data_mb,
    ROUND(index_length / 1024 / 1024, 2) AS index_mb
FROM information_schema.tables 
WHERE table_schema = 'your_database'
ORDER BY (data_length + index_length) DESC;
```

## Table Optimization

### Table Statistics
```sql
-- PostgreSQL: Update table statistics
ANALYZE users;
ANALYZE;  -- All tables

-- Check table statistics
SELECT 
    tablename,
    n_tup_ins as inserts,
    n_tup_upd as updates,
    n_tup_del as deletes,
    last_analyze,
    last_autoanalyze
FROM pg_stat_user_tables;

-- MySQL: Update table statistics  
ANALYZE TABLE users;

-- Check table statistics
SHOW TABLE STATUS LIKE 'users';
```

### Table Maintenance
```sql
-- PostgreSQL: Reclaim space and update statistics
VACUUM ANALYZE users;

-- Full vacuum (locks table, use sparingly)
VACUUM FULL users;

-- MySQL: Optimize table (rebuilds table)
OPTIMIZE TABLE users;

-- Check for fragmentation
SHOW TABLE STATUS LIKE 'users';
-- Look at Data_free column for fragmentation
```

### Partitioning (Advanced)
```sql
-- PostgreSQL range partitioning by date
CREATE TABLE orders (
    id SERIAL,
    order_date DATE NOT NULL,
    customer_id INTEGER,
    total_amount DECIMAL(10,2)
) PARTITION BY RANGE (order_date);

-- Create partitions
CREATE TABLE orders_2023_q1 PARTITION OF orders
    FOR VALUES FROM ('2023-01-01') TO ('2023-04-01');

CREATE TABLE orders_2023_q2 PARTITION OF orders
    FOR VALUES FROM ('2023-04-01') TO ('2023-07-01');

-- Queries automatically use appropriate partition
SELECT * FROM orders WHERE order_date = '2023-02-15';
```

## Connection and Resource Management

### Connection Pooling
```python
# Python example with connection pooling
from sqlalchemy import create_engine
from sqlalchemy.pool import QueuePool

# Create engine with connection pooling
engine = create_engine(
    'postgresql://user:pass@localhost/db',
    poolclass=QueuePool,
    pool_size=10,           # Keep 10 connections open
    max_overflow=20,        # Allow 20 additional connections
    pool_pre_ping=True,     # Verify connections before use
    pool_recycle=3600       # Recycle connections after 1 hour
)
```

### Query Timeout Configuration
```sql
-- PostgreSQL: Set statement timeout
SET statement_timeout = '30s';

-- MySQL: Set query timeout
SET SESSION max_execution_time = 30000;  -- 30 seconds

-- For specific queries, use application-level timeouts
```

## Monitoring and Alerting

### Key Metrics to Monitor
```sql
-- Database size growth
SELECT 
    pg_database.datname,
    pg_size_pretty(pg_database_size(pg_database.datname)) AS size
FROM pg_database 
ORDER BY pg_database_size(pg_database.datname) DESC;

-- Active connections
SELECT count(*) as active_connections 
FROM pg_stat_activity 
WHERE state = 'active';

-- Lock monitoring (PostgreSQL)
SELECT 
    blocked_locks.pid AS blocked_pid,
    blocked_activity.usename AS blocked_user,
    blocking_locks.pid AS blocking_pid,
    blocking_activity.usename AS blocking_user,
    blocked_activity.query AS blocked_statement,
    blocking_activity.query AS blocking_statement
FROM pg_catalog.pg_locks blocked_locks
JOIN pg_catalog.pg_stat_activity blocked_activity ON blocked_activity.pid = blocked_locks.pid
JOIN pg_catalog.pg_locks blocking_locks ON (blocking_locks.locktype = blocked_locks.locktype)
JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
WHERE NOT blocked_locks.granted;
```

### Performance Monitoring Tools

**PostgreSQL Tools:**
```bash
# pg_stat_statements extension (enable in postgresql.conf)
shared_preload_libraries = 'pg_stat_statements'

# Install extension in database
CREATE EXTENSION pg_stat_statements;

# Query slow queries
SELECT query, calls, total_time, mean_time 
FROM pg_stat_statements 
ORDER BY total_time DESC 
LIMIT 10;
```

**MySQL Tools:**
```sql
-- Enable performance schema
SET GLOBAL performance_schema = ON;

-- Query slow queries from performance schema
SELECT 
    TRUNCATE(TIMER_WAIT/1000000000000,6) as exec_time,
    SQL_TEXT
FROM performance_schema.events_statements_history_long 
WHERE TIMER_WAIT > 1000000000000  -- 1 second
ORDER BY TIMER_WAIT DESC 
LIMIT 10;
```

## Common Performance Issues

### N+1 Query Problem
```sql
-- ❌ Bad: N+1 queries (1 + N individual queries)
-- Application code that does:
SELECT * FROM posts;  -- 1 query
-- Then for each post:
SELECT * FROM users WHERE id = ?;  -- N queries

-- ✅ Good: Use JOIN to fetch related data
SELECT p.*, u.username, u.email
FROM posts p
JOIN users u ON p.user_id = u.id;

-- ✅ Alternative: Use IN clause
SELECT * FROM users 
WHERE id IN (1, 2, 3, 4, 5);  -- Get all users in one query
```

### Missing Indexes
```sql
-- Identify missing indexes by analyzing slow queries
-- Look for queries with high cost and sequential scans

-- PostgreSQL: Find tables with high sequential scan ratio
SELECT 
    tablename,
    seq_scan,
    idx_scan,
    CASE WHEN seq_scan + idx_scan > 0 
         THEN seq_scan::float / (seq_scan + idx_scan) 
         ELSE 0 
    END AS seq_scan_ratio
FROM pg_stat_user_tables
WHERE seq_scan + idx_scan > 1000  -- Tables with significant activity
ORDER BY seq_scan_ratio DESC;
```

### Large Result Sets
```sql
-- ❌ Avoid: Fetching unnecessary data
SELECT * FROM large_table;

-- ✅ Good: Limit columns and rows
SELECT id, name, email FROM users WHERE active = true LIMIT 100;

-- ✅ Good: Use streaming for large exports
SELECT id, name, email FROM users WHERE active = true;
-- Process in batches in application code
```

## Optimization Checklist

### Query Level
- [ ] Use EXPLAIN to analyze query plans
- [ ] Ensure WHERE clause columns are indexed
- [ ] Avoid functions in WHERE clauses
- [ ] Use appropriate JOIN types
- [ ] Consider EXISTS instead of IN for large subqueries
- [ ] Use LIMIT for large result sets
- [ ] Implement cursor-based pagination

### Index Level
- [ ] Create indexes on foreign key columns
- [ ] Create composite indexes for multi-column WHERE clauses
- [ ] Consider partial indexes for filtered queries
- [ ] Remove unused indexes
- [ ] Monitor index usage statistics

### Table Level
- [ ] Update table statistics regularly
- [ ] Monitor table fragmentation
- [ ] Consider partitioning for very large tables
- [ ] Normalize appropriately (avoid over-normalization)

### Database Level
- [ ] Configure appropriate buffer sizes
- [ ] Set up connection pooling
- [ ] Monitor slow query logs
- [ ] Set up performance monitoring
- [ ] Regular maintenance tasks (VACUUM, ANALYZE)

## Related Resources

- **[SQL Basics](sql-basics.md)** - Write efficient basic queries
- **[SQL Joins](sql-joins.md)** - Optimize table relationships
- **[Schema Design](schema-design.md)** - Design for performance from the start
- **[DBeaver](dbeaver.md)** - Use GUI tools for performance analysis
- **[Database Setup & Tools](setup-tools.md)** - Configure database systems optimally