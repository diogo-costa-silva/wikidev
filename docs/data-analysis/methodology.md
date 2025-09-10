# Methodology

← [**Voltar para Data Analysis**](index.md) | [**🏠 Home**](../index.md)

> **EDA fundamentals and systematic approach to data exploration.**

Effective data analysis follows a systematic methodology. This guide provides a proven framework for exploratory data analysis (EDA) that helps you understand your data thoroughly before diving into modeling or reporting.

## 🎯 Goals

- **Systematic approach** to data exploration
- **Comprehensive understanding** of data structure and quality
- **Hypothesis generation** from initial exploration
- **Reproducible methodology** across different datasets

## 🔍 Quick Verification

Test this methodology with a sample dataset:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Load sample dataset
df = pd.read_csv('https://raw.githubusercontent.com/mwaskom/seaborn-data/master/tips.csv')

# Follow the methodology steps
print("Shape:", df.shape)
print("Info:")
df.info()
print("Summary statistics:")
df.describe()
```

## 📋 EDA Methodology Framework

### Phase 1: Initial Data Assessment (5-10 minutes)
1. **Data Shape & Structure**
2. **Column Types & Missing Values**
3. **High-Level Summary Statistics**

### Phase 2: Deep Data Exploration (30-60 minutes)
4. **Individual Variable Analysis**
5. **Relationships & Correlations**
6. **Data Quality Issues**

### Phase 3: Hypothesis Formation (15-30 minutes)
7. **Pattern Identification**
8. **Hypothesis Generation**
9. **Next Steps Planning**

## 🔍 Phase 1: Initial Assessment

### 1. Data Shape & Structure
```python
# Basic data information
print(f"Dataset shape: {df.shape}")
print(f"Number of rows: {df.shape[0]:,}")
print(f"Number of columns: {df.shape[1]}")

# Memory usage
print(f"Memory usage: {df.memory_usage(deep=True).sum() / 1024**2:.2f} MB")

# Column overview
print("\nColumns:")
for i, col in enumerate(df.columns):
    print(f"{i+1}. {col} ({df[col].dtype})")
```

### 2. Column Types & Missing Values
```python
# Data types and missing values
print("Data Types and Missing Values:")
print(df.info())

# Missing value summary
missing_data = df.isnull().sum()
missing_percent = 100 * missing_data / len(df)
missing_summary = pd.DataFrame({
    'Missing Count': missing_data,
    'Missing Percentage': missing_percent
})
missing_summary = missing_summary[missing_summary['Missing Count'] > 0]
print("\nMissing Values Summary:")
print(missing_summary)
```

### 3. High-Level Summary Statistics
```python
# Numerical columns summary
print("Numerical Variables Summary:")
print(df.describe())

# Categorical columns summary
categorical_cols = df.select_dtypes(include=['object']).columns
if len(categorical_cols) > 0:
    print("\nCategorical Variables Summary:")
    for col in categorical_cols:
        print(f"\n{col}:")
        print(df[col].value_counts().head())
```

## 🔍 Phase 2: Deep Exploration

### 4. Individual Variable Analysis

#### Numerical Variables
```python
# For each numerical column
numerical_cols = df.select_dtypes(include=[np.number]).columns

for col in numerical_cols:
    print(f"\n--- {col.upper()} ---")
    
    # Basic statistics
    print(f"Mean: {df[col].mean():.2f}")
    print(f"Median: {df[col].median():.2f}")
    print(f"Std Dev: {df[col].std():.2f}")
    print(f"Min: {df[col].min():.2f}")
    print(f"Max: {df[col].max():.2f}")
    
    # Distribution analysis
    plt.figure(figsize=(12, 4))
    
    # Histogram
    plt.subplot(1, 3, 1)
    plt.hist(df[col].dropna(), bins=30, edgecolor='black')
    plt.title(f'{col} Distribution')
    plt.xlabel(col)
    plt.ylabel('Frequency')
    
    # Box plot
    plt.subplot(1, 3, 2)
    plt.boxplot(df[col].dropna())
    plt.title(f'{col} Box Plot')
    plt.ylabel(col)
    
    # Q-Q plot for normality
    from scipy import stats
    plt.subplot(1, 3, 3)
    stats.probplot(df[col].dropna(), dist="norm", plot=plt)
    plt.title(f'{col} Q-Q Plot')
    
    plt.tight_layout()
    plt.show()
    
    # Outlier detection (IQR method)
    Q1 = df[col].quantile(0.25)
    Q3 = df[col].quantile(0.75)
    IQR = Q3 - Q1
    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR
    outliers = df[(df[col] < lower_bound) | (df[col] > upper_bound)]
    print(f"Potential outliers: {len(outliers)} ({len(outliers)/len(df)*100:.1f}%)")
```

#### Categorical Variables
```python
# For each categorical column
categorical_cols = df.select_dtypes(include=['object']).columns

for col in categorical_cols:
    print(f"\n--- {col.upper()} ---")
    
    # Value counts
    value_counts = df[col].value_counts()
    print(f"Unique values: {df[col].nunique()}")
    print(f"Most common: {value_counts.index[0]} ({value_counts.iloc[0]} times)")
    
    # Display top categories
    print("Top categories:")
    print(value_counts.head(10))
    
    # Visualization
    plt.figure(figsize=(10, 6))
    
    if df[col].nunique() <= 10:
        # Bar plot for few categories
        value_counts.plot(kind='bar')
        plt.title(f'{col} Distribution')
        plt.xlabel(col)
        plt.ylabel('Count')
        plt.xticks(rotation=45)
    else:
        # Show top 10 categories
        value_counts.head(10).plot(kind='bar')
        plt.title(f'{col} Distribution (Top 10)')
        plt.xlabel(col)
        plt.ylabel('Count')
        plt.xticks(rotation=45)
    
    plt.tight_layout()
    plt.show()
```

### 5. Relationships & Correlations

#### Correlation Analysis
```python
# Correlation matrix for numerical variables
numerical_df = df.select_dtypes(include=[np.number])

if len(numerical_df.columns) > 1:
    # Correlation matrix
    corr_matrix = numerical_df.corr()
    
    # Heatmap
    plt.figure(figsize=(10, 8))
    sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', center=0,
                square=True, linewidths=0.5)
    plt.title('Correlation Matrix')
    plt.tight_layout()
    plt.show()
    
    # Find strong correlations
    strong_corr = []
    for i in range(len(corr_matrix.columns)):
        for j in range(i+1, len(corr_matrix.columns)):
            corr_value = corr_matrix.iloc[i, j]
            if abs(corr_value) > 0.7:  # Strong correlation threshold
                strong_corr.append({
                    'var1': corr_matrix.columns[i],
                    'var2': corr_matrix.columns[j],
                    'correlation': corr_value
                })
    
    if strong_corr:
        print("Strong correlations (>0.7):")
        for corr in strong_corr:
            print(f"{corr['var1']} ↔ {corr['var2']}: {corr['correlation']:.3f}")
```

#### Bivariate Analysis
```python
# Numerical vs Numerical
numerical_cols = df.select_dtypes(include=[np.number]).columns
if len(numerical_cols) >= 2:
    # Pairplot for numerical variables
    sns.pairplot(df[numerical_cols])
    plt.suptitle('Pairplot of Numerical Variables', y=1.02)
    plt.show()

# Categorical vs Numerical
categorical_cols = df.select_dtypes(include=['object']).columns
if len(categorical_cols) > 0 and len(numerical_cols) > 0:
    for cat_col in categorical_cols[:2]:  # Limit to first 2 categorical
        for num_col in numerical_cols[:2]:  # Limit to first 2 numerical
            plt.figure(figsize=(10, 6))
            sns.boxplot(data=df, x=cat_col, y=num_col)
            plt.title(f'{num_col} by {cat_col}')
            plt.xticks(rotation=45)
            plt.tight_layout()
            plt.show()
```

### 6. Data Quality Issues

#### Missing Data Patterns
```python
# Missing data visualization
if df.isnull().sum().sum() > 0:
    plt.figure(figsize=(12, 6))
    
    # Missing data matrix
    sns.heatmap(df.isnull(), yticklabels=False, cbar=True, cmap='viridis')
    plt.title('Missing Data Pattern')
    plt.show()
    
    # Missing data correlation
    missing_corr = df.isnull().corr()
    if missing_corr.abs().max().max() > 0.5:  # If missing data is correlated
        print("Missing data appears to be correlated - investigate further")
```

#### Duplicate Detection
```python
# Check for duplicates
total_duplicates = df.duplicated().sum()
print(f"Total duplicate rows: {total_duplicates}")

if total_duplicates > 0:
    # Show some duplicate examples
    print("Example duplicates:")
    print(df[df.duplicated()].head())
```

## 🔍 Phase 3: Hypothesis Formation

### 7. Pattern Identification

#### Key Questions to Ask
```python
# Template for pattern identification
patterns_checklist = """
Distribution Patterns:
□ Are variables normally distributed?
□ Are there obvious outliers?
□ Are there multiple modes/peaks?

Relationship Patterns:
□ Which variables are strongly correlated?
□ Are there non-linear relationships?
□ Do categorical variables show clear group differences?

Data Quality Patterns:
□ Is missing data random or systematic?
□ Are there data entry errors?
□ Are there impossible values?

Business Logic Patterns:
□ Do the relationships make business sense?
□ Are there unexpected patterns that warrant investigation?
□ What might explain the strongest relationships?
"""

print(patterns_checklist)
```

### 8. Hypothesis Generation
```python
# Document key findings and hypotheses
findings_template = """
KEY FINDINGS SUMMARY:

Data Structure:
- Dataset: {rows} rows × {cols} columns
- Missing data: {missing_pct:.1f}% overall
- Data quality: [Good/Fair/Poor]

Key Variables:
1. Target variable: [if applicable]
2. Most important predictors: [list top 3-5]
3. Variables with issues: [list any problematic ones]

Relationships Discovered:
1. [Strongest correlation/relationship found]
2. [Second strongest relationship]
3. [Any surprising relationships]

HYPOTHESES FOR FURTHER INVESTIGATION:
1. [Hypothesis 1 based on data patterns]
2. [Hypothesis 2 based on correlations]
3. [Hypothesis 3 based on business logic]

NEXT STEPS:
□ Address data quality issues
□ Create derived variables
□ Test specific hypotheses
□ Begin modeling/analysis
"""

# Fill in the template with your actual findings
findings = findings_template.format(
    rows=len(df),
    cols=len(df.columns),
    missing_pct=df.isnull().sum().sum() / (len(df) * len(df.columns)) * 100
)

print(findings)
```

## 🛠️ EDA Automation Template

### Complete EDA Function
```python
def automated_eda(df, target_column=None):
    """
    Perform comprehensive EDA on a dataset.
    
    Parameters:
    df: pandas DataFrame
    target_column: str, name of target variable (if applicable)
    """
    
    print("=" * 50)
    print("AUTOMATED EXPLORATORY DATA ANALYSIS")
    print("=" * 50)
    
    # Phase 1: Initial Assessment
    print("\n1. DATASET OVERVIEW")
    print(f"Shape: {df.shape}")
    print(f"Memory usage: {df.memory_usage(deep=True).sum() / 1024**2:.2f} MB")
    
    print("\n2. DATA TYPES")
    print(df.info())
    
    print("\n3. MISSING VALUES")
    missing = df.isnull().sum()
    if missing.sum() > 0:
        print(missing[missing > 0])
    else:
        print("No missing values found")
    
    print("\n4. SUMMARY STATISTICS")
    print(df.describe())
    
    # Phase 2: Variable Analysis
    print("\n5. VARIABLE DISTRIBUTIONS")
    numerical_cols = df.select_dtypes(include=[np.number]).columns
    categorical_cols = df.select_dtypes(include=['object']).columns
    
    print(f"Numerical variables: {len(numerical_cols)}")
    print(f"Categorical variables: {len(categorical_cols)}")
    
    # Phase 3: Relationships
    if len(numerical_cols) > 1:
        print("\n6. CORRELATIONS")
        corr_matrix = df[numerical_cols].corr()
        high_corr = []
        for i in range(len(corr_matrix.columns)):
            for j in range(i+1, len(corr_matrix.columns)):
                corr_val = corr_matrix.iloc[i, j]
                if abs(corr_val) > 0.7:
                    high_corr.append((
                        corr_matrix.columns[i], 
                        corr_matrix.columns[j], 
                        corr_val
                    ))
        
        if high_corr:
            print("High correlations (>0.7):")
            for var1, var2, corr in high_corr:
                print(f"  {var1} ↔ {var2}: {corr:.3f}")
        else:
            print("No high correlations found")
    
    print("\n" + "=" * 50)
    print("EDA COMPLETE - Review findings and plan next steps")
    print("=" * 50)

# Usage example
# automated_eda(df, target_column='target_var_name')
```

## 🔗 Related Sections

- **[Data Cleaning](data-cleaning.md)** - Address issues found during EDA
- **[Pandas Quick Cheats](pandas-cheats.md)** - Essential pandas commands for EDA
- **[Visualization Principles](visualization.md)** - Create effective plots for exploration
- **[Feature Engineering](feature-engineering.md)** - Create new variables based on EDA insights

## ⚡ Quick Reference

**Phase 1: Initial Assessment (5-10 min)**
```python
df.shape                    # Dataset dimensions
df.info()                   # Data types and missing values
df.describe()               # Summary statistics
df.head()                   # First few rows
```

**Phase 2: Deep Exploration (30-60 min)**
```python
# Distributions
df[col].hist()              # Histogram
df[col].value_counts()      # Categorical frequencies
df.boxplot(column=col)      # Box plot

# Relationships
df.corr()                   # Correlation matrix
sns.pairplot(df)           # Pairwise relationships
sns.boxplot(x=cat, y=num)  # Category vs numeric
```

**Phase 3: Patterns & Hypotheses (15-30 min)**
```python
# Missing patterns
df.isnull().sum()           # Missing counts
sns.heatmap(df.isnull())    # Missing pattern visualization

# Outlier detection
Q1, Q3 = df[col].quantile([0.25, 0.75])
IQR = Q3 - Q1
outliers = df[(df[col] < Q1-1.5*IQR) | (df[col] > Q3+1.5*IQR)]
```

---

**Next:** Apply EDA insights in [Data Cleaning](data-cleaning.md) or explore [Pandas Quick Cheats](pandas-cheats.md) for efficient data manipulation techniques.