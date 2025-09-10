# Database Setup & Tools

← [**Voltar para Databases**](index.md) | [**🏠 Home**](../index.md)

Set up a complete database development environment with multiple database systems, GUI tools, and command-line clients for efficient database work.

## Quick Setup Guide

### SQLite (Recommended for Beginners)
```bash
# SQLite is usually pre-installed on macOS and Linux
sqlite3 --version

# If not installed:
# macOS
brew install sqlite

# Linux (Ubuntu/Debian)
sudo apt-get install sqlite3

# Windows
# Download from: https://sqlite.org/download.html
```

**First SQLite Database:**
```bash
# Create and connect to database
sqlite3 myapp.db

# Inside SQLite shell:
.help              # Show help
.tables            # List tables
.schema table_name # Show table structure
.exit              # Exit SQLite

# Run SQL file
sqlite3 myapp.db < setup.sql

# One-liner query
sqlite3 myapp.db "SELECT * FROM users;"
```

### PostgreSQL
```bash
# macOS (Homebrew)
brew install postgresql
brew services start postgresql

# Linux (Ubuntu/Debian)
sudo apt-get install postgresql postgresql-contrib
sudo systemctl start postgresql
sudo systemctl enable postgresql

# Windows
# Download installer from: https://www.postgresql.org/download/

# Create user and database
sudo -u postgres createuser --interactive myapp_user
sudo -u postgres createdb myapp_dev -O myapp_user

# Connect
psql -U myapp_user -d myapp_dev -h localhost
```

**PostgreSQL Configuration:**
```bash
# Find config files
sudo -u postgres psql -c "SHOW config_file;"

# Common settings in postgresql.conf:
listen_addresses = 'localhost'
port = 5432
max_connections = 100

# Enable connections in pg_hba.conf:
local   all   myapp_user   md5
host    all   myapp_user   127.0.0.1/32   md5
```

### MySQL/MariaDB
```bash
# macOS (Homebrew)
brew install mysql
brew services start mysql

# Linux (Ubuntu/Debian)
sudo apt-get install mysql-server
sudo systemctl start mysql
sudo systemctl enable mysql

# Secure installation
sudo mysql_secure_installation

# Create database and user
mysql -u root -p
```

**MySQL Setup:**
```sql
-- Create database and user
CREATE DATABASE myapp_dev;
CREATE USER 'myapp_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON myapp_dev.* TO 'myapp_user'@'localhost';
FLUSH PRIVILEGES;

-- Test connection
mysql -u myapp_user -p myapp_dev
```

## GUI Tools

### DBeaver (Universal Database Tool)
```bash
# Installation covered in detail in dbeaver.md
brew install --cask dbeaver-community  # macOS
sudo snap install dbeaver-ce           # Linux
# Windows: Download from dbeaver.io
```

**First Connection Setup:**
1. Open DBeaver
2. Click "New Database Connection"
3. Select your database type
4. Configure connection details
5. Test connection
6. Save and connect

### Database-Specific GUI Tools

**PostgreSQL: pgAdmin**
```bash
# Web-based administration tool
pip install pgadmin4
pgadmin4

# Or download desktop version from:
# https://www.pgadmin.org/download/
```

**MySQL: MySQL Workbench**
```bash
# Download from: https://dev.mysql.com/downloads/workbench/
brew install --cask mysql-workbench  # macOS
```

**SQLite: DB Browser for SQLite**
```bash
brew install --cask db-browser-for-sqlite  # macOS
sudo apt-get install sqlitebrowser          # Linux
# Windows: Download from sqlitebrowser.org
```

## Command Line Tools

### Essential CLI Commands

**SQLite:**
```bash
# Basic operations
sqlite3 database.db
.databases         # List databases
.tables            # List tables
.schema           # Show all schemas
.dump             # Backup database
.read script.sql  # Execute SQL file
.output file.csv  # Redirect output
.mode csv         # Set output format
```

**PostgreSQL:**
```bash
# Connection and basic operations
psql -h localhost -U username -d database
\l                # List databases
\dt               # List tables
\d table_name     # Describe table
\i script.sql     # Execute SQL file
\o output.txt     # Redirect output
\q                # Quit

# Backup and restore
pg_dump database > backup.sql
psql database < backup.sql
```

**MySQL:**
```bash
# Connection and operations
mysql -h localhost -u username -p database
SHOW DATABASES;
USE database_name;
SHOW TABLES;
DESCRIBE table_name;
SOURCE script.sql;
\q

# Backup and restore
mysqldump database > backup.sql
mysql database < backup.sql
```

### Advanced CLI Productivity

**Create Connection Aliases:**
```bash
# Add to ~/.bashrc or ~/.zshrc
alias mydb="sqlite3 ~/projects/myapp.db"
alias pgdev="psql -h localhost -U myuser -d myapp_dev"
alias mysqldev="mysql -h localhost -u myuser -p myapp_dev"

# Usage
mydb "SELECT * FROM users LIMIT 5;"
```

**Quick SQL Execution Scripts:**
```bash
#!/bin/bash
# quick_query.sh - Run SQL against different databases

DB_TYPE=$1
QUERY=$2

case $DB_TYPE in
    "sqlite")
        sqlite3 ~/projects/app.db "$QUERY"
        ;;
    "postgres")
        psql -h localhost -U myuser -d myapp_dev -c "$QUERY"
        ;;
    "mysql")
        mysql -h localhost -u myuser -p myapp_dev -e "$QUERY"
        ;;
    *)
        echo "Usage: quick_query.sh [sqlite|postgres|mysql] 'SQL QUERY'"
        ;;
esac

# Usage examples:
# ./quick_query.sh sqlite "SELECT COUNT(*) FROM users;"
# ./quick_query.sh postgres "SELECT version();"
```

## Data Import/Export

### CSV Import/Export

**SQLite:**
```bash
# Import CSV
sqlite3 database.db
.mode csv
.import data.csv table_name

# Export to CSV
.headers on
.mode csv
.output data.csv
SELECT * FROM table_name;
.output stdout
```

**PostgreSQL:**
```sql
-- Import CSV
COPY table_name FROM '/path/to/data.csv' 
WITH (FORMAT csv, HEADER true);

-- Export to CSV
COPY (SELECT * FROM table_name) TO '/path/to/output.csv' 
WITH (FORMAT csv, HEADER true);
```

**MySQL:**
```sql
-- Import CSV
LOAD DATA INFILE '/path/to/data.csv'
INTO TABLE table_name
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

-- Export to CSV
SELECT * FROM table_name
INTO OUTFILE '/path/to/output.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

### JSON Import/Export

**PostgreSQL (JSON Support):**
```sql
-- Import JSON data
CREATE TABLE json_data (
    id SERIAL PRIMARY KEY,
    data JSONB
);

INSERT INTO json_data (data) 
SELECT * FROM json_populate_recordset(null::json_data, '[
    {"name": "Alice", "age": 30},
    {"name": "Bob", "age": 25}
]');

-- Query JSON data
SELECT data->>'name' as name, data->>'age' as age 
FROM json_data;
```

## Development Environment Setup

### Docker Database Setup
```yaml
# docker-compose.yml for development databases
version: '3.8'
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: myapp_dev
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypass
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_DATABASE: myapp_dev
      MYSQL_USER: myuser
      MYSQL_PASSWORD: mypass
      MYSQL_ROOT_PASSWORD: rootpass
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
  mysql_data:
```

**Start development databases:**
```bash
# Start all databases
docker-compose up -d

# Connect to services
psql -h localhost -p 5432 -U myuser -d myapp_dev
mysql -h localhost -P 3306 -u myuser -p myapp_dev
redis-cli -h localhost -p 6379

# Stop services
docker-compose down
```

### Environment Variables
```bash
# .env file for database connections
DATABASE_URL=postgresql://myuser:mypass@localhost:5432/myapp_dev
MYSQL_URL=mysql://myuser:mypass@localhost:3306/myapp_dev
REDIS_URL=redis://localhost:6379/0

# Load in scripts
export $(grep -v '^#' .env | xargs)
```

## Database-Specific Configuration

### PostgreSQL Optimization
```sql
-- postgresql.conf optimizations for development
shared_buffers = 256MB
work_mem = 4MB
maintenance_work_mem = 64MB
effective_cache_size = 1GB
random_page_cost = 1.1

-- Enable query logging
log_statement = 'all'
log_min_duration_statement = 0
```

### MySQL Optimization
```sql
-- my.cnf optimizations
[mysqld]
innodb_buffer_pool_size = 256M
innodb_log_file_size = 64M
max_connections = 100
query_cache_size = 32M

-- Enable slow query log
slow_query_log = 1
slow_query_log_file = /var/log/mysql/slow.log
long_query_time = 1
```

### SQLite Optimization
```sql
-- SQLite pragmas for better performance
PRAGMA synchronous = NORMAL;
PRAGMA cache_size = 10000;
PRAGMA temp_store = memory;
PRAGMA journal_mode = WAL;
PRAGMA foreign_keys = ON;
```

## Backup and Recovery

### Automated Backup Scripts
```bash
#!/bin/bash
# backup_databases.sh - Automated database backups

BACKUP_DIR="/backups/$(date +%Y%m%d)"
mkdir -p "$BACKUP_DIR"

# PostgreSQL backup
pg_dump -h localhost -U myuser myapp_dev > "$BACKUP_DIR/postgres_backup.sql"

# MySQL backup
mysqldump -h localhost -u myuser -p myapp_dev > "$BACKUP_DIR/mysql_backup.sql"

# SQLite backup (just copy file)
cp ~/projects/myapp.db "$BACKUP_DIR/sqlite_backup.db"

# Compress backups
tar -czf "$BACKUP_DIR.tar.gz" "$BACKUP_DIR"
rm -rf "$BACKUP_DIR"

echo "Backup completed: $BACKUP_DIR.tar.gz"
```

### Recovery Procedures
```bash
# PostgreSQL restore
createdb myapp_restored
psql myapp_restored < backup.sql

# MySQL restore
mysql -u myuser -p myapp_restored < backup.sql

# SQLite restore (copy file back)
cp backup.db ~/projects/myapp.db
```

## Security Setup

### User Management
```sql
-- PostgreSQL user management
CREATE ROLE app_readonly;
GRANT CONNECT ON DATABASE myapp_dev TO app_readonly;
GRANT USAGE ON SCHEMA public TO app_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO app_readonly;

CREATE USER readonly_user WITH PASSWORD 'secure_password';
GRANT app_readonly TO readonly_user;
```

### Connection Security
```bash
# Use SSL connections in production
psql "postgresql://user:pass@host:5432/db?sslmode=require"

# Limit connection sources
# In pg_hba.conf:
host myapp_dev myuser 10.0.0.0/8 md5
```

## Troubleshooting

### Common Connection Issues
```bash
# Check if database service is running
brew services list | grep postgres  # macOS
systemctl status postgresql         # Linux

# Check listening ports
netstat -tlnp | grep :5432  # PostgreSQL
netstat -tlnp | grep :3306  # MySQL

# Test connectivity
telnet localhost 5432  # PostgreSQL
telnet localhost 3306  # MySQL
```

### Performance Issues
```sql
-- Check active connections
SELECT * FROM pg_stat_activity;  -- PostgreSQL
SHOW PROCESSLIST;                -- MySQL

-- Check database sizes
SELECT datname, pg_size_pretty(pg_database_size(datname)) 
FROM pg_database;  -- PostgreSQL

-- Analyze query performance
EXPLAIN ANALYZE SELECT * FROM large_table WHERE condition;
```

## Related Resources

- **[DBeaver](dbeaver.md)** - GUI database tool setup and usage
- **[SQL Basics](sql-basics.md)** - Start writing queries
- **[Schema Design](schema-design.md)** - Database design principles
- **[Performance](performance.md)** - Database optimization techniques