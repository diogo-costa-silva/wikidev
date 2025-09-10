# Pandas Quick Cheats

← [**Voltar para Data Analysis**](index.md) | [**🏠 Home**](../index.md)

> **Fast reference for common data manipulation tasks.**

This is your go-to reference for pandas operations. These are the commands you'll use most frequently for data loading, cleaning, filtering, and analysis.

## 🎯 Goals

- **Quick lookup** for common pandas operations
- **Copy-paste ready** code snippets
- **Real-world examples** with typical datasets
- **Performance tips** for efficient data processing

## 🔍 Quick Verification

Test pandas is working with sample data:

```python
import pandas as pd
import numpy as np

# Quick test
df = pd.DataFrame({
    'name': ['Alice', 'Bob', 'Carol'],
    'age': [25, 30, 35],
    'city': ['NYC', 'LA', 'Chicago']
})
print(df.head())
print(f"Pandas version: {pd.__version__}")
```

## 📊 Data Loading & Saving

### Reading Files
```python
# CSV files
df = pd.read_csv('file.csv')
df = pd.read_csv('file.csv', sep=';')                    # Different separator
df = pd.read_csv('file.csv', index_col=0)               # Use first column as index
df = pd.read_csv('file.csv', parse_dates=['date_col'])   # Parse dates
df = pd.read_csv('file.csv', na_values=['N/A', 'NULL'])  # Custom NA values

# Excel files
df = pd.read_excel('file.xlsx')
df = pd.read_excel('file.xlsx', sheet_name='Sheet2')     # Specific sheet
df = pd.read_excel('file.xlsx', sheet_name=None)         # All sheets

# JSON files
df = pd.read_json('file.json')
df = pd.read_json('file.json', lines=True)               # JSONL format

# From URL
df = pd.read_csv('https://example.com/data.csv')

# From SQL
import sqlite3
conn = sqlite3.connect('database.db')
df = pd.read_sql('SELECT * FROM table', conn)
```

### Saving Files
```python
# CSV
df.to_csv('output.csv', index=False)                     # Without index
df.to_csv('output.csv', sep='\t')                        # Tab separated

# Excel
df.to_excel('output.xlsx', index=False)
df.to_excel('output.xlsx', sheet_name='MyData')

# JSON
df.to_json('output.json')
df.to_json('output.json', orient='records')              # Array of objects
```

## 🔍 Data Inspection

### Basic Info
```python
df.shape                     # Dimensions (rows, columns)
df.info()                    # Data types, memory usage, non-null counts
df.describe()                # Summary statistics for numerical columns
df.describe(include='all')   # Summary for all columns

df.head()                    # First 5 rows
df.head(10)                  # First 10 rows
df.tail()                    # Last 5 rows
df.sample(5)                 # Random 5 rows
```

### Column Information
```python
df.columns                   # Column names
df.dtypes                    # Data types
df.index                     # Index information

len(df)                      # Number of rows
len(df.columns)              # Number of columns

df.memory_usage(deep=True)   # Memory usage per column
```

### Missing Data
```python
df.isnull().sum()            # Count missing values per column
df.isna().sum()              # Same as isnull()
df.isnull().sum().sum()      # Total missing values

df.isnull().any()            # Which columns have missing values
df.isnull().all()            # Which columns are all missing

# Percentage of missing data
(df.isnull().sum() / len(df) * 100).round(2)
```

## 🎯 Data Selection & Filtering

### Selecting Columns
```python
df['column_name']            # Single column (Series)
df[['col1', 'col2']]        # Multiple columns (DataFrame)

# Select by data type
df.select_dtypes(include=[np.number])      # Numerical columns
df.select_dtypes(include=['object'])       # String/object columns
df.select_dtypes(exclude=[np.number])      # Non-numerical columns

# Select columns by pattern
df.filter(regex='^sales_')   # Columns starting with 'sales_'
df.filter(like='_2023')      # Columns containing '_2023'
```

### Selecting Rows
```python
df.iloc[0]                   # First row by position
df.iloc[0:5]                 # First 5 rows by position
df.iloc[-1]                  # Last row

df.loc[df['age'] > 25]       # Rows where age > 25
df.loc[df.index == 'Alice']  # Rows by index label
```

### Boolean Filtering
```python
# Single condition
df[df['age'] > 25]
df[df['city'] == 'NYC']
df[df['name'].str.contains('Alice')]

# Multiple conditions
df[(df['age'] > 25) & (df['city'] == 'NYC')]           # AND
df[(df['age'] < 20) | (df['age'] > 60)]                # OR
df[~(df['city'] == 'NYC')]                             # NOT

# Using query method (more readable)
df.query('age > 25')
df.query('age > 25 and city == "NYC"')
df.query('age in [25, 30, 35]')
```

### Advanced Selection
```python
# Sample data
df.sample(n=1000)            # Random sample of 1000 rows
df.sample(frac=0.1)          # Random 10% sample

# Conditional selection
df.where(df > 0)             # Keep values > 0, others become NaN
df.mask(df < 0)              # Make values < 0 NaN

# First/last occurrence
df.drop_duplicates()         # Remove duplicates
df.drop_duplicates(subset=['name'])  # Remove duplicates by column
df.drop_duplicates(keep='last')      # Keep last occurrence
```

## ✏️ Data Cleaning & Transformation

### Handling Missing Data
```python
# Remove missing data
df.dropna()                  # Drop rows with any missing values
df.dropna(subset=['col1'])   # Drop rows with missing values in specific column
df.dropna(axis=1)            # Drop columns with any missing values
df.dropna(thresh=2)          # Drop rows with less than 2 non-null values

# Fill missing data
df.fillna(0)                 # Fill with 0
df.fillna(df.mean())         # Fill with mean (numerical columns)
df.fillna(method='forward')  # Forward fill
df.fillna(method='backward') # Backward fill

# Fill by column
df['age'].fillna(df['age'].median())
df['city'].fillna('Unknown')
```

### Data Type Conversion
```python
# Convert data types
df['age'] = df['age'].astype(int)
df['price'] = df['price'].astype(float)
df['category'] = df['category'].astype('category')

# Convert to datetime
df['date'] = pd.to_datetime(df['date'])
df['date'] = pd.to_datetime(df['date'], format='%Y-%m-%d')

# Convert strings
df['name'] = df['name'].str.lower()
df['name'] = df['name'].str.upper()
df['name'] = df['name'].str.title()
```

### Renaming & Reordering
```python
# Rename columns
df.rename(columns={'old_name': 'new_name'})
df.rename(columns={'col1': 'new_col1', 'col2': 'new_col2'})

# Rename all columns to lowercase
df.columns = df.columns.str.lower()

# Reorder columns
df[['col2', 'col1', 'col3']]
df.reindex(columns=['col2', 'col1', 'col3'])
```

## 🔢 Data Aggregation & Grouping

### Basic Aggregations
```python
# Single column aggregations
df['age'].sum()
df['age'].mean()
df['age'].median()
df['age'].std()
df['age'].min()
df['age'].max()
df['age'].count()
df['age'].nunique()          # Number of unique values

# Multiple aggregations at once
df['age'].agg(['mean', 'std', 'min', 'max'])
```

### Group Operations
```python
# Group by single column
df.groupby('city')['age'].mean()
df.groupby('city').size()                    # Count of rows per group
df.groupby('city')['age'].agg(['mean', 'std'])

# Group by multiple columns
df.groupby(['city', 'gender'])['age'].mean()

# Multiple columns aggregation
df.groupby('city').agg({
    'age': 'mean',
    'salary': ['mean', 'sum'],
    'name': 'count'
})

# Group and transform
df['age_group_mean'] = df.groupby('city')['age'].transform('mean')
df['age_rank'] = df.groupby('city')['age'].rank()
```

### Pivot Tables
```python
# Basic pivot table
pd.pivot_table(df, values='salary', index='city', columns='gender')

# With aggregation function
pd.pivot_table(df, values='salary', index='city', columns='gender', 
               aggfunc='mean', fill_value=0)

# Multiple values
pd.pivot_table(df, values=['salary', 'age'], index='city', columns='gender')
```

## 🔄 Data Reshaping

### Concatenating & Merging
```python
# Concatenate DataFrames
pd.concat([df1, df2])                        # Vertically (stack rows)
pd.concat([df1, df2], axis=1)                # Horizontally (side by side)
pd.concat([df1, df2], ignore_index=True)     # Reset index

# Merge DataFrames
pd.merge(df1, df2, on='key_column')          # Inner join
pd.merge(df1, df2, on='key_column', how='left')     # Left join
pd.merge(df1, df2, on='key_column', how='outer')    # Outer join
pd.merge(df1, df2, left_on='key1', right_on='key2') # Different key names
```

### Reshaping Data
```python
# Melt (wide to long)
df_melted = pd.melt(df, id_vars=['id'], value_vars=['col1', 'col2'])

# Pivot (long to wide)
df_pivoted = df.pivot(index='id', columns='variable', values='value')

# Stack and unstack
df.stack()                   # Pivot columns to rows
df.unstack()                 # Pivot rows to columns
```

## 📊 String Operations

### Common String Methods
```python
# String operations (use .str accessor)
df['name'].str.len()                         # Length of strings
df['name'].str.lower()                       # Convert to lowercase
df['name'].str.upper()                       # Convert to uppercase
df['name'].str.title()                       # Title case
df['name'].str.strip()                       # Remove leading/trailing whitespace

# Pattern matching
df['name'].str.contains('Alice')             # Contains substring
df['name'].str.startswith('A')               # Starts with
df['name'].str.endswith('e')                 # Ends with
df['name'].str.match(r'A.*')                 # Regex match

# String replacement
df['name'].str.replace('Alice', 'Alicia')
df['name'].str.replace(r'[0-9]', '', regex=True)  # Remove numbers

# String splitting
df['name'].str.split(' ')                    # Split by space
df['name'].str.split(' ', expand=True)       # Split into separate columns
```

## 📅 Date & Time Operations

### DateTime Basics
```python
# Convert to datetime
df['date'] = pd.to_datetime(df['date'])

# Extract date components
df['year'] = df['date'].dt.year
df['month'] = df['date'].dt.month
df['day'] = df['date'].dt.day
df['weekday'] = df['date'].dt.day_name()
df['quarter'] = df['date'].dt.quarter

# Date filtering
df[df['date'] > '2023-01-01']
df[df['date'].between('2023-01-01', '2023-12-31')]

# Resample by time period
df.set_index('date').resample('M').mean()    # Monthly average
df.set_index('date').resample('D').sum()     # Daily sum
```

## ⚡ Performance Tips

### Efficient Operations
```python
# Use vectorized operations instead of loops
# Bad
result = []
for index, row in df.iterrows():
    result.append(row['a'] + row['b'])

# Good
result = df['a'] + df['b']

# Use query() for complex conditions
df.query('age > 25 and city == "NYC"')       # Often faster than boolean indexing

# Use categorical data for string columns with few unique values
df['category'] = df['category'].astype('category')

# Read large files in chunks
chunk_iter = pd.read_csv('large_file.csv', chunksize=10000)
for chunk in chunk_iter:
    # Process chunk
    process(chunk)
```

### Memory Optimization
```python
# Check memory usage
df.info(memory_usage='deep')

# Optimize data types
df['int_col'] = pd.to_numeric(df['int_col'], downcast='integer')
df['float_col'] = pd.to_numeric(df['float_col'], downcast='float')

# Use categorical for string columns with repetitive values
df['category'] = df['category'].astype('category')
```

## 🔗 Related Sections

- **[Methodology](methodology.md)** - Systematic approach to data exploration
- **[Data Cleaning](data-cleaning.md)** - Advanced cleaning techniques
- **[Visualization Principles](visualization.md)** - Creating effective plots with pandas
- **[Python Data Workflow](../python/data-workflow.md)** - Setting up pandas environment

## ⚡ Quick Reference Card

### Essential Imports
```python
import pandas as pd
import numpy as np
```

### Loading & Inspection
```python
df = pd.read_csv('file.csv')     # Load data
df.shape                         # Dimensions
df.info()                        # Overview
df.head()                        # First rows
df.describe()                    # Statistics
```

### Selection & Filtering
```python
df['col']                        # Select column
df[['col1', 'col2']]            # Select multiple columns
df[df['age'] > 25]              # Filter rows
df.query('age > 25')            # Query syntax
```

### Cleaning & Transformation
```python
df.dropna()                      # Drop missing values
df.fillna(0)                     # Fill missing values
df.rename(columns={'old': 'new'}) # Rename columns
df.groupby('col').mean()         # Group and aggregate
```

### Export
```python
df.to_csv('output.csv', index=False)  # Save to CSV
df.to_excel('output.xlsx')            # Save to Excel
```

---

**Next:** Apply these pandas techniques in [Data Cleaning](data-cleaning.md) or explore [Methodology](methodology.md) for systematic data analysis approaches.