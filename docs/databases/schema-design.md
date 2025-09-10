# Database Schema Design

Learn to design maintainable, efficient database schemas with proper relationships, constraints, and normalization principles.

## Schema Design Fundamentals

### Core Principles
1. **Normalize to reduce redundancy** - Eliminate duplicate data
2. **Use meaningful names** - Tables and columns should be self-documenting
3. **Plan for growth** - Consider future requirements and scaling
4. **Enforce data integrity** - Use constraints to maintain data quality
5. **Document relationships** - Clear foreign key relationships

### Basic Schema Example
```sql
-- Users table with proper constraints
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    -- Constraints
    CONSTRAINT check_email_format CHECK (email LIKE '%@%.%'),
    CONSTRAINT check_username_length CHECK (LENGTH(username) >= 3)
);

-- Posts table with foreign key relationship
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    content TEXT,
    user_id INTEGER NOT NULL,
    status VARCHAR(20) DEFAULT 'draft',
    published_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    -- Foreign key constraint
    CONSTRAINT fk_posts_user 
        FOREIGN KEY (user_id) REFERENCES users(id) 
        ON DELETE CASCADE ON UPDATE CASCADE,
    
    -- Check constraints
    CONSTRAINT check_status 
        CHECK (status IN ('draft', 'published', 'archived'))
);
```

## Normalization

### First Normal Form (1NF)
- Each column contains atomic (indivisible) values
- No repeating groups or arrays in columns

```sql
-- ❌ Not in 1NF (multiple values in one column)
CREATE TABLE bad_users (
    id INTEGER,
    name VARCHAR(100),
    phone_numbers VARCHAR(200)  -- "555-1234, 555-5678, 555-9999"
);

-- ✅ 1NF compliant
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE user_phones (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    phone_number VARCHAR(20) NOT NULL,
    phone_type VARCHAR(10) DEFAULT 'mobile'
);
```

### Second Normal Form (2NF)
- Must be in 1NF
- All non-key attributes fully depend on the primary key

```sql
-- ❌ Not in 2NF (order_date depends only on order_id, not the composite key)
CREATE TABLE bad_order_items (
    order_id INTEGER,
    product_id INTEGER,
    quantity INTEGER,
    order_date DATE,  -- Depends only on order_id
    PRIMARY KEY (order_id, product_id)
);

-- ✅ 2NF compliant - separate concerns
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    customer_id INTEGER,
    order_date DATE NOT NULL,
    status VARCHAR(20) DEFAULT 'pending'
);

CREATE TABLE order_items (
    id SERIAL PRIMARY KEY,
    order_id INTEGER REFERENCES orders(id),
    product_id INTEGER REFERENCES products(id),
    quantity INTEGER NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL
);
```

### Third Normal Form (3NF)
- Must be in 2NF
- No transitive dependencies (non-key attributes don't depend on other non-key attributes)

```sql
-- ❌ Not in 3NF (city_name depends on city_id, not customer_id)
CREATE TABLE bad_customers (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    city_id INTEGER,
    city_name VARCHAR(50),  -- Transitive dependency
    city_state VARCHAR(20)  -- Transitive dependency
);

-- ✅ 3NF compliant
CREATE TABLE cities (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    state VARCHAR(20) NOT NULL,
    country VARCHAR(50) NOT NULL
);

CREATE TABLE customers (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    city_id INTEGER REFERENCES cities(id)
);
```

## Data Types Best Practices

### Choose Appropriate Types
```sql
-- Text and strings
VARCHAR(n)          -- Variable length, up to n characters
TEXT               -- Unlimited length text
CHAR(n)            -- Fixed length, padded with spaces

-- Numbers
SERIAL/AUTO_INCREMENT  -- Auto-incrementing integers
INTEGER            -- 32-bit integers (-2B to +2B)
BIGINT            -- 64-bit integers
DECIMAL(p,s)      -- Exact decimal (precision, scale)
NUMERIC(p,s)      -- Same as DECIMAL
REAL/FLOAT        -- Approximate decimal

-- Dates and times
DATE              -- Date only (YYYY-MM-DD)
TIME              -- Time only (HH:MM:SS)
TIMESTAMP         -- Date and time with timezone
TIMESTAMPTZ       -- Timestamp with timezone (PostgreSQL)

-- Boolean
BOOLEAN           -- TRUE/FALSE/NULL

-- JSON (PostgreSQL, MySQL 5.7+)
JSON              -- JSON data
JSONB             -- Binary JSON (PostgreSQL, faster queries)
```

### Data Type Examples
```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    sku VARCHAR(50) UNIQUE NOT NULL,           -- Product identifier
    name VARCHAR(200) NOT NULL,                -- Product name
    description TEXT,                          -- Long description
    price DECIMAL(10,2) NOT NULL,             -- Price (8 digits + 2 decimal)
    weight_kg DECIMAL(5,3),                   -- Weight in kg (e.g., 12.345)
    is_active BOOLEAN DEFAULT TRUE,            -- Active status
    category_id INTEGER REFERENCES categories(id),
    metadata JSONB,                           -- Flexible data (PostgreSQL)
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    -- Constraints
    CONSTRAINT positive_price CHECK (price > 0),
    CONSTRAINT positive_weight CHECK (weight_kg > 0)
);
```

## Relationships and Constraints

### One-to-Many Relationships
```sql
-- One customer can have many orders
CREATE TABLE customers (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);

CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    customer_id INTEGER NOT NULL,
    order_date DATE NOT NULL,
    total_amount DECIMAL(10,2),
    
    CONSTRAINT fk_customer 
        FOREIGN KEY (customer_id) REFERENCES customers(id)
        ON DELETE RESTRICT  -- Prevent deleting customers with orders
);
```

### Many-to-Many Relationships
```sql
-- Students can enroll in many courses, courses can have many students
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);

CREATE TABLE courses (
    id SERIAL PRIMARY KEY,
    course_code VARCHAR(10) UNIQUE NOT NULL,
    name VARCHAR(100) NOT NULL,
    credits INTEGER DEFAULT 3
);

-- Junction table for many-to-many relationship
CREATE TABLE enrollments (
    id SERIAL PRIMARY KEY,
    student_id INTEGER NOT NULL,
    course_id INTEGER NOT NULL,
    enrolled_date DATE DEFAULT CURRENT_DATE,
    grade VARCHAR(2),
    
    CONSTRAINT fk_enrollment_student 
        FOREIGN KEY (student_id) REFERENCES students(id) 
        ON DELETE CASCADE,
    CONSTRAINT fk_enrollment_course 
        FOREIGN KEY (course_id) REFERENCES courses(id) 
        ON DELETE CASCADE,
    
    -- Prevent duplicate enrollments
    CONSTRAINT unique_enrollment 
        UNIQUE (student_id, course_id)
);
```

### Self-Referencing Relationships
```sql
-- Employees table with manager relationship
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    manager_id INTEGER,
    department VARCHAR(50),
    
    CONSTRAINT fk_manager 
        FOREIGN KEY (manager_id) REFERENCES employees(id)
        ON DELETE SET NULL  -- If manager is deleted, set to NULL
);

-- Query hierarchical data
WITH RECURSIVE employee_hierarchy AS (
    -- Base case: top-level managers
    SELECT id, name, manager_id, 1 as level, name as path
    FROM employees 
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case
    SELECT e.id, e.name, e.manager_id, eh.level + 1,
           eh.path || ' -> ' || e.name
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.id
)
SELECT * FROM employee_hierarchy ORDER BY level, name;
```

## Constraints and Data Integrity

### Primary Keys
```sql
-- Single column primary key (most common)
CREATE TABLE users (
    id SERIAL PRIMARY KEY,  -- AUTO_INCREMENT in MySQL
    username VARCHAR(50) UNIQUE NOT NULL
);

-- Composite primary key
CREATE TABLE order_items (
    order_id INTEGER,
    product_id INTEGER,
    quantity INTEGER NOT NULL,
    PRIMARY KEY (order_id, product_id),
    
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);

-- UUID primary key (useful for distributed systems)
CREATE TABLE sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),  -- PostgreSQL
    user_id INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Foreign Key Constraints
```sql
-- Foreign key with different actions
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    user_id INTEGER NOT NULL,
    category_id INTEGER,
    
    -- Restrict deletion of referenced user
    CONSTRAINT fk_posts_user 
        FOREIGN KEY (user_id) REFERENCES users(id) 
        ON DELETE RESTRICT ON UPDATE CASCADE,
    
    -- Set to NULL if category is deleted
    CONSTRAINT fk_posts_category 
        FOREIGN KEY (category_id) REFERENCES categories(id) 
        ON DELETE SET NULL ON UPDATE CASCADE
);

-- Foreign key actions:
-- RESTRICT  - Prevent deletion/update
-- CASCADE   - Delete/update dependent rows
-- SET NULL  - Set foreign key to NULL
-- SET DEFAULT - Set to default value
-- NO ACTION - Same as RESTRICT (default)
```

### Check Constraints
```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    discount_percent INTEGER DEFAULT 0,
    status VARCHAR(20) DEFAULT 'active',
    
    -- Price must be positive
    CONSTRAINT positive_price CHECK (price > 0),
    
    -- Discount between 0 and 100
    CONSTRAINT valid_discount 
        CHECK (discount_percent >= 0 AND discount_percent <= 100),
    
    -- Status must be one of allowed values
    CONSTRAINT valid_status 
        CHECK (status IN ('active', 'inactive', 'discontinued')),
    
    -- Discounted items must have positive discount
    CONSTRAINT discount_logic 
        CHECK (
            (status = 'active' AND discount_percent >= 0) OR 
            (status != 'active')
        )
);
```

### Unique Constraints
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    
    -- Single column unique constraints
    CONSTRAINT unique_username UNIQUE (username),
    CONSTRAINT unique_email UNIQUE (email),
    
    -- Composite unique constraint
    -- (Multiple users can have same phone if one is NULL)
    CONSTRAINT unique_phone UNIQUE (phone)
);

-- Add unique constraint to existing table
ALTER TABLE users ADD CONSTRAINT unique_username UNIQUE (username);
```

## Indexing Strategy

### Primary Indexes
```sql
-- Primary key automatically creates unique index
CREATE TABLE users (
    id SERIAL PRIMARY KEY,  -- Automatic index on id
    username VARCHAR(50) UNIQUE,  -- Automatic index on username
    email VARCHAR(100) NOT NULL
);
```

### Secondary Indexes
```sql
-- Single column indexes
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_created_at ON users(created_at);

-- Composite indexes
CREATE INDEX idx_orders_customer_date ON orders(customer_id, order_date);
CREATE INDEX idx_posts_status_published ON posts(status, published_at);

-- Partial indexes (PostgreSQL)
CREATE INDEX idx_active_users ON users(username) WHERE is_active = TRUE;

-- Functional indexes
CREATE INDEX idx_users_lower_email ON users(LOWER(email));
```

### Index Best Practices
```sql
-- ✅ Good: Index on foreign keys
CREATE INDEX idx_posts_user_id ON posts(user_id);

-- ✅ Good: Index on frequently queried columns
CREATE INDEX idx_orders_status ON orders(status);

-- ✅ Good: Composite index with most selective column first
CREATE INDEX idx_orders_status_date ON orders(status, order_date);

-- ❌ Avoid: Too many indexes on small tables
-- ❌ Avoid: Indexes on columns that change frequently
-- ❌ Avoid: Indexes on very low selectivity columns (e.g., boolean)
```

## Common Schema Patterns

### Audit Trail Pattern
```sql
-- Add audit columns to track changes
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    
    -- Audit columns
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_by INTEGER REFERENCES users(id),
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_by INTEGER REFERENCES users(id),
    version INTEGER DEFAULT 1
);

-- Trigger to update timestamp (PostgreSQL example)
CREATE OR REPLACE FUNCTION update_modified_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    NEW.version = OLD.version + 1;
    RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER update_users_modtime 
    BEFORE UPDATE ON users 
    FOR EACH ROW EXECUTE FUNCTION update_modified_column();
```

### Soft Delete Pattern
```sql
-- Instead of deleting records, mark them as deleted
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    is_deleted BOOLEAN DEFAULT FALSE,
    deleted_at TIMESTAMP NULL,
    deleted_by INTEGER REFERENCES users(id),
    
    -- Unique constraint that ignores soft-deleted records
    CONSTRAINT unique_active_username 
        UNIQUE (username) WHERE is_deleted = FALSE,
    CONSTRAINT unique_active_email 
        UNIQUE (email) WHERE is_deleted = FALSE
);

-- Views for active records only
CREATE VIEW active_users AS 
SELECT * FROM users WHERE is_deleted = FALSE;
```

### Polymorphic Associations
```sql
-- Comments that can belong to posts or events
CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    content TEXT NOT NULL,
    commentable_type VARCHAR(50) NOT NULL,  -- 'post' or 'event'
    commentable_id INTEGER NOT NULL,
    user_id INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    -- Ensure valid combination
    CONSTRAINT valid_commentable_type 
        CHECK (commentable_type IN ('post', 'event')),
    
    -- Index for polymorphic queries
    INDEX idx_comments_polymorphic (commentable_type, commentable_id)
);

-- Query comments for a specific post
SELECT c.*, u.username 
FROM comments c 
JOIN users u ON c.user_id = u.id 
WHERE c.commentable_type = 'post' AND c.commentable_id = 123;
```

## Schema Evolution and Migrations

### Migration Best Practices
```sql
-- Always use reversible migrations
-- migration_001_create_users.sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- migration_001_rollback.sql
DROP TABLE users;
```

### Safe Schema Changes
```sql
-- ✅ Safe operations (no downtime)
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
ALTER TABLE users ADD COLUMN is_verified BOOLEAN DEFAULT FALSE;
CREATE INDEX idx_users_phone ON users(phone);

-- ⚠️  Potentially unsafe operations (may cause downtime)
ALTER TABLE users ALTER COLUMN email TYPE VARCHAR(255);  -- May lock table
ALTER TABLE users DROP COLUMN old_column;                -- Data loss
ALTER TABLE users ADD COLUMN new_col NOT NULL;          -- May fail if data exists

-- Safer approach for NOT NULL columns
ALTER TABLE users ADD COLUMN new_col VARCHAR(50);
UPDATE users SET new_col = 'default_value' WHERE new_col IS NULL;
ALTER TABLE users ALTER COLUMN new_col SET NOT NULL;
```

### Version Control for Schema
```bash
# Track schema changes in version control
migrations/
├── 001_create_users.sql
├── 002_add_posts_table.sql  
├── 003_add_user_phone.sql
├── 004_create_comments.sql
└── applied_migrations.txt

# Migration tracking table
CREATE TABLE schema_migrations (
    version VARCHAR(50) PRIMARY KEY,
    applied_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Performance Considerations

### Query-Driven Design
```sql
-- Design indexes based on your query patterns

-- If you frequently query:
SELECT * FROM orders WHERE customer_id = ? AND status = 'pending';
-- Create composite index:
CREATE INDEX idx_orders_customer_status ON orders(customer_id, status);

-- If you frequently sort:
SELECT * FROM posts WHERE user_id = ? ORDER BY created_at DESC;
-- Create composite index:
CREATE INDEX idx_posts_user_created ON posts(user_id, created_at DESC);
```

### Denormalization for Performance
```sql
-- Sometimes strategic denormalization improves performance
CREATE TABLE order_summary (
    id SERIAL PRIMARY KEY,
    customer_id INTEGER REFERENCES customers(id),
    order_date DATE NOT NULL,
    item_count INTEGER NOT NULL,      -- Denormalized from order_items
    total_amount DECIMAL(10,2) NOT NULL,  -- Denormalized calculation
    
    -- Keep normalized data for consistency
    CONSTRAINT positive_item_count CHECK (item_count > 0),
    CONSTRAINT positive_total CHECK (total_amount > 0)
);

-- Use triggers or application code to maintain denormalized values
```

## Documentation and Naming

### Naming Conventions
```sql
-- Tables: plural nouns
users, posts, order_items, user_preferences

-- Columns: singular nouns, descriptive
user_id, first_name, created_at, is_active

-- Indexes: descriptive with idx_ prefix
idx_users_email, idx_posts_user_created, idx_orders_status

-- Constraints: descriptive with type prefix
pk_users_id, fk_posts_user, unique_users_email, check_positive_price

-- Foreign keys: referenced_table_id
user_id, category_id, parent_post_id
```

### Schema Documentation
```sql
-- Use comments to document tables and columns
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,  -- bcrypt hash
    email_verified_at TIMESTAMP,         -- When email was verified
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Add table and column comments
COMMENT ON TABLE users IS 'Application users with authentication';
COMMENT ON COLUMN users.password_hash IS 'bcrypt hashed password';
COMMENT ON COLUMN users.email_verified_at IS 'Timestamp when email was verified, NULL if unverified';
```

## Related Resources

- **[SQL Basics](sql-basics.md)** - Learn to query your designed schemas
- **[SQL Joins](sql-joins.md)** - Work with related data
- **[Database Setup & Tools](setup-tools.md)** - Set up your development environment  
- **[DBeaver](dbeaver.md)** - Visual schema design and management
- **[Performance](performance.md)** - Optimize your database design