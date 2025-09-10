# Data Cleaning

← [**Voltar para Data Analysis**](index.md) | [**🏠 Home**](../index.md)

> **Systematic approaches to handle missing data, outliers, and inconsistencies.**

Data cleaning is often the most time-consuming part of data analysis, but it's crucial for reliable results. This guide provides systematic approaches to common data quality issues.

## 🎯 Goals

- **Systematic data cleaning** workflow
- **Handle missing data** effectively  
- **Detect and treat outliers** appropriately
- **Ensure data consistency** and quality

## 🔍 Quick Verification

Test data cleaning with sample messy data:

```python
import pandas as pd
import numpy as np

# Create messy sample data
df = pd.DataFrame({
    'name': ['Alice', 'BOB', 'carol', None, 'DAVE', 'eve '],
    'age': [25, 999, 30, np.nan, -5, 35],
    'email': ['alice@email.com', 'bob@EMAIL.COM', 'invalid-email', '', 'dave@email.com', None],
    'salary': [50000, 200000, np.nan, 45000, 0, 60000]
})

print("Before cleaning:")
print(df)
print("\nData issues:")
print(f"Missing values: {df.isnull().sum().sum()}")
print(f"Potential outliers in age: {(df['age'] < 0).sum() + (df['age'] > 120).sum()}")
```

## 🧹 Data Cleaning Workflow

### Phase 1: Initial Assessment (10 minutes)
1. **Load and examine data structure**
2. **Identify missing data patterns** 
3. **Detect obvious data quality issues**
4. **Document findings for decision making**

### Phase 2: Missing Data Handling (20-30 minutes)
5. **Analyze missing data mechanisms**
6. **Choose appropriate handling strategy**
7. **Implement missing data treatment**
8. **Validate results**

### Phase 3: Data Consistency (20-40 minutes)  
9. **Standardize formats and values**
10. **Handle duplicates**
11. **Treat outliers**
12. **Final validation**

## 🔍 Phase 1: Initial Assessment

### Data Structure Examination
```python
# Basic data overview
print(f"Dataset shape: {df.shape}")
print(f"\nColumn data types:")
print(df.dtypes)

print(f"\nMemory usage:")
print(df.memory_usage(deep=True))

# First look at data
print(f"\nFirst few rows:")
print(df.head())

print(f"\nLast few rows:")  
print(df.tail())
```

### Missing Data Assessment
```python
# Missing data summary
def missing_data_summary(df):
    missing = df.isnull().sum()
    missing_pct = 100 * missing / len(df)
    
    summary = pd.DataFrame({
        'Missing_Count': missing,
        'Missing_Percentage': missing_pct
    }).sort_values('Missing_Count', ascending=False)
    
    return summary[summary['Missing_Count'] > 0]

print("Missing Data Summary:")
print(missing_data_summary(df))

# Visualize missing data patterns
import seaborn as sns
import matplotlib.pyplot as plt

if df.isnull().sum().sum() > 0:
    plt.figure(figsize=(10, 6))
    sns.heatmap(df.isnull(), yticklabels=False, cbar=True, cmap='viridis')
    plt.title('Missing Data Pattern')
    plt.show()
```

### Data Quality Issues Detection  
```python
def detect_data_issues(df):
    issues = []
    
    # Check for duplicate rows
    duplicates = df.duplicated().sum()
    if duplicates > 0:
        issues.append(f"Duplicate rows: {duplicates}")
    
    # Check for empty strings
    for col in df.select_dtypes(include=['object']):
        empty_strings = (df[col] == '').sum()
        if empty_strings > 0:
            issues.append(f"Empty strings in {col}: {empty_strings}")
    
    # Check for potential outliers in numeric columns
    for col in df.select_dtypes(include=[np.number]):
        Q1 = df[col].quantile(0.25)
        Q3 = df[col].quantile(0.75)
        IQR = Q3 - Q1
        outliers = ((df[col] < (Q1 - 1.5 * IQR)) | (df[col] > (Q3 + 1.5 * IQR))).sum()
        if outliers > 0:
            issues.append(f"Potential outliers in {col}: {outliers}")
    
    return issues

print("Data Quality Issues:")
for issue in detect_data_issues(df):
    print(f"- {issue}")
```

## 🔍 Phase 2: Missing Data Handling

### Understanding Missing Data Types
```python
# Missing Completely At Random (MCAR) - missing values are random
# Missing At Random (MAR) - missing depends on observed data
# Missing Not At Random (MNAR) - missing depends on unobserved data

# Test for missing data patterns
def analyze_missing_patterns(df):
    # Check if missing data is related to other variables
    if df.isnull().sum().sum() > 0:
        # Correlation between missing indicators
        missing_indicators = df.isnull().astype(int)
        if missing_indicators.sum().sum() > 0:
            missing_corr = missing_indicators.corr()
            print("Correlation between missing data patterns:")
            print(missing_corr)
```

### Missing Data Strategies

#### Strategy 1: Deletion
```python
# Complete case analysis (listwise deletion)
df_complete = df.dropna()
print(f"Rows after complete case deletion: {len(df_complete)} ({len(df_complete)/len(df)*100:.1f}%)")

# Pairwise deletion (for specific analyses)
correlation_matrix = df[['age', 'salary']].corr()  # Uses available data

# Drop columns with too much missing data
threshold = 0.5  # 50% missing
df_reduced = df.dropna(axis=1, thresh=int(threshold * len(df)))
```

#### Strategy 2: Imputation - Simple
```python
# Mean/median imputation for numerical variables
df['age_imputed'] = df['age'].fillna(df['age'].median())
df['salary_imputed'] = df['salary'].fillna(df['salary'].mean())

# Mode imputation for categorical variables  
df['name_imputed'] = df['name'].fillna(df['name'].mode()[0] if not df['name'].mode().empty else 'Unknown')

# Forward/backward fill for time series
df_time = df.sort_values('some_date_column') if 'some_date_column' in df.columns else df
df_filled = df_time.fillna(method='forward').fillna(method='backward')
```

#### Strategy 3: Imputation - Advanced
```python
from sklearn.impute import KNNImputer, IterativeImputer

# KNN Imputation
numeric_cols = df.select_dtypes(include=[np.number]).columns
knn_imputer = KNNImputer(n_neighbors=5)
df_knn = df.copy()
df_knn[numeric_cols] = knn_imputer.fit_transform(df[numeric_cols])

# Iterative Imputation (MICE-like)
iterative_imputer = IterativeImputer(random_state=42)
df_iterative = df.copy() 
df_iterative[numeric_cols] = iterative_imputer.fit_transform(df[numeric_cols])

# Custom domain-specific imputation
def custom_imputation(df):
    # Age imputation based on other factors
    df['age_custom'] = df['age']
    
    # Example: Impute age based on salary range
    young_mask = (df['salary'] < 40000) & df['age'].isnull()
    df.loc[young_mask, 'age_custom'] = 25
    
    middle_mask = (df['salary'].between(40000, 80000)) & df['age'].isnull()
    df.loc[middle_mask, 'age_custom'] = 35
    
    senior_mask = (df['salary'] > 80000) & df['age'].isnull()
    df.loc[senior_mask, 'age_custom'] = 45
    
    return df
```

## 🔍 Phase 3: Data Consistency

### Standardizing Text Data
```python
def clean_text_columns(df, text_cols):
    df_clean = df.copy()
    
    for col in text_cols:
        if col in df_clean.columns:
            # Convert to string and handle NaN
            df_clean[col] = df_clean[col].astype(str)
            
            # Strip whitespace
            df_clean[col] = df_clean[col].str.strip()
            
            # Standardize case (usually lowercase)
            df_clean[col] = df_clean[col].str.lower()
            
            # Replace empty strings with NaN
            df_clean[col] = df_clean[col].replace(['', 'nan', 'none'], np.nan)
            
    return df_clean

# Apply text cleaning
df_clean = clean_text_columns(df, ['name', 'email'])
```

### Email Validation and Cleaning
```python
import re

def clean_email_column(df, email_col):
    df_clean = df.copy()
    
    # Email validation regex (basic)
    email_pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    
    # Clean email format
    df_clean[email_col] = df_clean[email_col].str.lower().str.strip()
    
    # Mark invalid emails as NaN
    valid_email = df_clean[email_col].str.match(email_pattern, na=False)
    df_clean.loc[~valid_email, email_col] = np.nan
    
    # Report cleaning results
    invalid_count = (~valid_email).sum()
    print(f"Invalid emails found and cleaned: {invalid_count}")
    
    return df_clean

df_clean = clean_email_column(df_clean, 'email')
```

### Handling Duplicates
```python
# Find duplicates
def analyze_duplicates(df):
    print(f"Duplicate rows: {df.duplicated().sum()}")
    
    if df.duplicated().sum() > 0:
        print("Duplicate examples:")
        print(df[df.duplicated(keep=False)].sort_values(list(df.columns)))

# Remove duplicates with different strategies
# Keep first occurrence
df_no_dups_first = df.drop_duplicates(keep='first')

# Keep last occurrence  
df_no_dups_last = df.drop_duplicates(keep='last')

# Remove all duplicates (keep none)
df_no_dups_none = df.drop_duplicates(keep=False)

# Duplicate detection on subset of columns
df_unique_names = df.drop_duplicates(subset=['name'], keep='first')
```

### Outlier Detection and Treatment
```python
def detect_outliers(df, columns, method='iqr'):
    outliers_info = {}
    
    for col in columns:
        if method == 'iqr':
            Q1 = df[col].quantile(0.25)
            Q3 = df[col].quantile(0.75)
            IQR = Q3 - Q1
            lower_bound = Q1 - 1.5 * IQR
            upper_bound = Q3 + 1.5 * IQR
            
            outliers = df[(df[col] < lower_bound) | (df[col] > upper_bound)]
            
        elif method == 'zscore':
            z_scores = np.abs((df[col] - df[col].mean()) / df[col].std())
            outliers = df[z_scores > 3]
            
        outliers_info[col] = {
            'count': len(outliers),
            'percentage': len(outliers) / len(df) * 100,
            'values': outliers[col].tolist()
        }
    
    return outliers_info

# Detect outliers
numeric_cols = ['age', 'salary']
outlier_info = detect_outliers(df, numeric_cols)

for col, info in outlier_info.items():
    print(f"{col}: {info['count']} outliers ({info['percentage']:.1f}%)")
```

### Outlier Treatment Strategies
```python
def treat_outliers(df, col, method='cap', percentiles=(5, 95)):
    df_treated = df.copy()
    
    if method == 'remove':
        # Remove outliers using IQR
        Q1 = df[col].quantile(0.25)
        Q3 = df[col].quantile(0.75)
        IQR = Q3 - Q1
        lower_bound = Q1 - 1.5 * IQR
        upper_bound = Q3 + 1.5 * IQR
        
        df_treated = df_treated[(df_treated[col] >= lower_bound) & 
                               (df_treated[col] <= upper_bound)]
        
    elif method == 'cap':
        # Cap outliers at percentiles
        lower_cap = df[col].quantile(percentiles[0]/100)
        upper_cap = df[col].quantile(percentiles[1]/100)
        
        df_treated[col] = df_treated[col].clip(lower=lower_cap, upper=upper_cap)
        
    elif method == 'transform':
        # Log transformation for positive skewed data
        if (df[col] > 0).all():
            df_treated[f'{col}_log'] = np.log(df[col])
        
        # Square root transformation
        df_treated[f'{col}_sqrt'] = np.sqrt(df[col].abs())
        
    return df_treated

# Apply outlier treatment
df_treated = treat_outliers(df_clean, 'age', method='cap', percentiles=(1, 99))
df_treated = treat_outliers(df_treated, 'salary', method='cap', percentiles=(5, 95))
```

## 🧪 Data Validation

### Comprehensive Validation Function
```python
def validate_cleaned_data(df_original, df_cleaned):
    print("=== DATA CLEANING VALIDATION ===\n")
    
    # Shape comparison
    print(f"Shape before: {df_original.shape}")
    print(f"Shape after: {df_cleaned.shape}")
    print(f"Rows removed: {df_original.shape[0] - df_cleaned.shape[0]}")
    
    # Missing data comparison  
    print(f"\nMissing values before: {df_original.isnull().sum().sum()}")
    print(f"Missing values after: {df_cleaned.isnull().sum().sum()}")
    
    # Data type consistency
    print(f"\nData type changes:")
    for col in df_cleaned.columns:
        if col in df_original.columns:
            if df_original[col].dtype != df_cleaned[col].dtype:
                print(f"- {col}: {df_original[col].dtype} → {df_cleaned[col].dtype}")
    
    # Value range validation for numeric columns
    print(f"\nNumeric column ranges:")
    for col in df_cleaned.select_dtypes(include=[np.number]).columns:
        print(f"- {col}: [{df_cleaned[col].min():.2f}, {df_cleaned[col].max():.2f}]")
    
    print("\n" + "="*40)

# Run validation
validate_cleaned_data(df, df_treated)
```

### Business Logic Validation
```python
def business_logic_validation(df):
    """Apply domain-specific validation rules"""
    issues = []
    
    # Age validation
    if 'age' in df.columns:
        invalid_age = ((df['age'] < 16) | (df['age'] > 100)).sum()
        if invalid_age > 0:
            issues.append(f"Invalid age values: {invalid_age}")
    
    # Salary validation  
    if 'salary' in df.columns:
        invalid_salary = (df['salary'] < 0).sum()
        if invalid_salary > 0:
            issues.append(f"Negative salaries: {invalid_salary}")
    
    # Email format validation
    if 'email' in df.columns:
        invalid_emails = df['email'].str.contains('@', na=False) == False
        invalid_count = invalid_emails.sum()
        if invalid_count > 0:
            issues.append(f"Invalid email formats: {invalid_count}")
    
    return issues
```

## 📊 Automated Cleaning Pipeline

### Complete Cleaning Pipeline
```python
def automated_cleaning_pipeline(df):
    """Complete data cleaning pipeline"""
    print("Starting automated data cleaning pipeline...")
    df_clean = df.copy()
    
    # Step 1: Handle missing data
    print("1. Handling missing data...")
    for col in df_clean.select_dtypes(include=[np.number]).columns:
        df_clean[col] = df_clean[col].fillna(df_clean[col].median())
    
    for col in df_clean.select_dtypes(include=['object']).columns:
        df_clean[col] = df_clean[col].fillna('Unknown')
    
    # Step 2: Standardize text  
    print("2. Standardizing text data...")
    text_cols = df_clean.select_dtypes(include=['object']).columns
    for col in text_cols:
        df_clean[col] = df_clean[col].str.strip().str.lower()
    
    # Step 3: Remove duplicates
    print("3. Removing duplicates...")
    df_clean = df_clean.drop_duplicates()
    
    # Step 4: Handle outliers
    print("4. Treating outliers...")
    for col in df_clean.select_dtypes(include=[np.number]).columns:
        Q1 = df_clean[col].quantile(0.25)
        Q3 = df_clean[col].quantile(0.75)
        IQR = Q3 - Q1
        lower_bound = Q1 - 1.5 * IQR
        upper_bound = Q3 + 1.5 * IQR
        
        df_clean[col] = df_clean[col].clip(lower=lower_bound, upper=upper_bound)
    
    print("Data cleaning pipeline completed!")
    return df_clean

# Apply automated pipeline
df_auto_cleaned = automated_cleaning_pipeline(df)
```

## 💡 Best Practices

### Documentation and Reproducibility
```python
# Always document cleaning steps
cleaning_log = {
    'timestamp': pd.Timestamp.now(),
    'original_shape': df.shape,
    'steps_applied': [
        'Median imputation for age',
        'Email format standardization', 
        'IQR outlier capping for salary',
        'Duplicate removal'
    ],
    'final_shape': df_treated.shape,
    'rows_removed': df.shape[0] - df_treated.shape[0]
}

# Save cleaning log
import json
with open('data_cleaning_log.json', 'w') as f:
    json.dump(cleaning_log, f, default=str, indent=2)
```

### Before/After Comparison
```python
def compare_distributions(df_before, df_after, column):
    """Compare distributions before and after cleaning"""
    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 4))
    
    # Before
    df_before[column].hist(ax=ax1, bins=30, alpha=0.7)
    ax1.set_title(f'{column} - Before Cleaning')
    ax1.set_xlabel(column)
    ax1.set_ylabel('Frequency')
    
    # After  
    df_after[column].hist(ax=ax2, bins=30, alpha=0.7)
    ax2.set_title(f'{column} - After Cleaning')
    ax2.set_xlabel(column)
    ax2.set_ylabel('Frequency')
    
    plt.tight_layout()
    plt.show()

# Compare key columns
compare_distributions(df, df_treated, 'age')
compare_distributions(df, df_treated, 'salary')
```

## 🔗 Related Sections

- **[Methodology](methodology.md)** - Systematic EDA approach
- **[Pandas Quick Cheats](pandas-cheats.md)** - Data manipulation commands
- **[Feature Engineering](feature-engineering.md)** - Creating new variables from clean data
- **[Visualization Principles](visualization.md)** - Plotting clean data effectively

## ⚡ Quick Reference

**Missing Data:**
```python
df.isnull().sum()                    # Count missing values
df.dropna()                          # Remove missing rows
df.fillna(df.mean())                 # Fill with mean
df.fillna(method='forward')          # Forward fill
```

**Outliers:**
```python
Q1, Q3 = df[col].quantile([0.25, 0.75])
IQR = Q3 - Q1
outliers = df[(df[col] < Q1-1.5*IQR) | (df[col] > Q3+1.5*IQR)]
df[col].clip(lower=Q1-1.5*IQR, upper=Q3+1.5*IQR)  # Cap outliers
```

**Text Cleaning:**
```python
df[col].str.strip()                  # Remove whitespace
df[col].str.lower()                  # Convert to lowercase
df[col].str.replace('old', 'new')    # Replace text
```

**Duplicates:**
```python
df.duplicated().sum()                # Count duplicates
df.drop_duplicates()                 # Remove duplicates
df.drop_duplicates(subset=['col'])   # Remove based on column
```

---

**Next:** Apply clean data in [Feature Engineering](feature-engineering.md), or explore [Pandas Quick Cheats](pandas-cheats.md) for more data manipulation techniques.