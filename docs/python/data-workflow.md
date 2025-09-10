# Data Workflow

← [**Voltar para Python**](index.md) | [**🏠 Home**](../index.md)

> **Pandas quickstart and data analysis workflow for Python projects.**

This guide provides a complete workflow for data analysis in Python, from environment setup to final insights. It's designed for both beginners and experienced analysts who want a structured approach.

## 🎯 Goals

- **End-to-end data workflow** from raw data to insights
- **Pandas proficiency** for data manipulation
- **Reproducible analysis** with clear structure
- **Best practices** for data science projects

## 🔍 Quick Verification

Test the data workflow with sample data:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Quick test - load sample data
df = pd.read_csv('https://raw.githubusercontent.com/mwaskom/seaborn-data/master/tips.csv')
print(f"Data loaded: {df.shape}")
print(df.head())

# Basic analysis
print(f"Average tip: ${df['tip'].mean():.2f}")
print(f"Data types: {df.dtypes.to_dict()}")
```

## 🏗️ Project Setup for Data Analysis

### Directory Structure
```bash
# Create data analysis project structure
mkdir data-analysis-project && cd data-analysis-project

# Standard structure for data projects
mkdir -p {data/{raw,processed,external},notebooks,src,reports,references}
touch README.md requirements.txt .gitignore

# Create main analysis notebook
touch notebooks/01-exploratory-analysis.ipynb
touch notebooks/02-data-cleaning.ipynb
touch notebooks/03-modeling.ipynb
```

Directory layout:
```
data-analysis-project/
├── data/
│   ├── raw/                    # Original, immutable data
│   ├── processed/              # Cleaned, transformed data
│   └── external/               # External data sources
├── notebooks/                  # Jupyter notebooks
├── src/                        # Source code modules
├── reports/                    # Generated reports
├── references/                 # Data dictionaries, manuals
├── README.md
├── requirements.txt
└── .gitignore
```

### Environment Setup
```bash
# Set Python version and create environment
echo "3.11.5" > .python-version
python -m venv .venv
source .venv/bin/activate  # or .venv\Scripts\activate on Windows

# Install data analysis stack
pip install pandas numpy matplotlib seaborn jupyter
pip install scikit-learn plotly ipykernel

# Create requirements file
pip freeze > requirements.txt
```

### Essential Imports and Settings
```python
# Standard imports for data analysis
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from pathlib import Path
import warnings
warnings.filterwarnings('ignore')

# Configure pandas display options
pd.set_option('display.max_columns', 100)
pd.set_option('display.max_rows', 100)
pd.set_option('display.width', 1000)
pd.set_option('display.float_format', '{:.2f}'.format)

# Configure matplotlib
plt.style.use('seaborn-v0_8')  # or 'default', 'ggplot'
plt.rcParams['figure.figsize'] = (12, 8)
plt.rcParams['font.size'] = 12

# Configure seaborn
sns.set_palette("husl")

# Set random seed for reproducibility
np.random.seed(42)

print("Data analysis environment configured!")
```

## 📊 Step 1: Data Loading and Initial Inspection

### Loading Different Data Sources
```python
# CSV files (most common)
df = pd.read_csv('data/raw/dataset.csv')

# Excel files
df = pd.read_excel('data/raw/dataset.xlsx', sheet_name='Sheet1')

# JSON data
df = pd.read_json('data/raw/dataset.json')

# From URL
df = pd.read_csv('https://example.com/dataset.csv')

# Multiple CSV files
import glob
csv_files = glob.glob('data/raw/*.csv')
df_list = [pd.read_csv(file) for file in csv_files]
df = pd.concat(df_list, ignore_index=True)

# SQL database
import sqlite3
conn = sqlite3.connect('database.db')
df = pd.read_sql('SELECT * FROM table_name', conn)
```

### Initial Data Inspection
```python
def inspect_data(df):
    """Comprehensive data inspection function"""
    print("=== DATA INSPECTION REPORT ===\n")
    
    # Basic info
    print(f"Dataset shape: {df.shape}")
    print(f"Memory usage: {df.memory_usage(deep=True).sum() / 1024**2:.2f} MB")
    
    # Data types
    print(f"\nData types:")
    print(df.dtypes.value_counts())
    
    # Missing data
    missing = df.isnull().sum()
    if missing.sum() > 0:
        print(f"\nMissing data:")
        missing_df = pd.DataFrame({
            'Column': missing.index,
            'Missing': missing.values,
            'Percentage': (missing.values / len(df) * 100).round(2)
        }).sort_values('Missing', ascending=False)
        print(missing_df[missing_df['Missing'] > 0])
    else:
        print("\nNo missing data found")
    
    # Duplicates
    duplicates = df.duplicated().sum()
    print(f"\nDuplicate rows: {duplicates} ({duplicates/len(df)*100:.2f}%)")
    
    # Sample data
    print(f"\nFirst 3 rows:")
    print(df.head(3))
    
    print("\n" + "="*50)

# Example usage
df = pd.read_csv('https://raw.githubusercontent.com/mwaskom/seaborn-data/master/tips.csv')
inspect_data(df)
```

## 🧹 Step 2: Data Cleaning

### Missing Data Handling
```python
def handle_missing_data(df):
    """Handle missing data with different strategies"""
    df_clean = df.copy()
    
    # Strategy 1: Drop columns with too much missing data (>50%)
    missing_threshold = 0.5
    missing_pct = df_clean.isnull().sum() / len(df_clean)
    cols_to_drop = missing_pct[missing_pct > missing_threshold].index
    if len(cols_to_drop) > 0:
        print(f"Dropping columns with >50% missing: {list(cols_to_drop)}")
        df_clean = df_clean.drop(columns=cols_to_drop)
    
    # Strategy 2: Fill numerical columns
    numerical_cols = df_clean.select_dtypes(include=[np.number]).columns
    for col in numerical_cols:
        if df_clean[col].isnull().sum() > 0:
            # Use median for numerical columns (more robust to outliers)
            df_clean[col] = df_clean[col].fillna(df_clean[col].median())
            print(f"Filled {col} missing values with median: {df_clean[col].median():.2f}")
    
    # Strategy 3: Fill categorical columns
    categorical_cols = df_clean.select_dtypes(include=['object']).columns
    for col in categorical_cols:
        if df_clean[col].isnull().sum() > 0:
            mode_value = df_clean[col].mode()
            if len(mode_value) > 0:
                df_clean[col] = df_clean[col].fillna(mode_value[0])
                print(f"Filled {col} missing values with mode: {mode_value[0]}")
            else:
                df_clean[col] = df_clean[col].fillna('Unknown')
                print(f"Filled {col} missing values with 'Unknown'")
    
    return df_clean

# Apply cleaning
df_clean = handle_missing_data(df)
```

### Data Type Optimization
```python
def optimize_dtypes(df):
    """Optimize data types to reduce memory usage"""
    df_optimized = df.copy()
    
    # Optimize integers
    int_cols = df_optimized.select_dtypes(include=['int64']).columns
    for col in int_cols:
        col_min = df_optimized[col].min()
        col_max = df_optimized[col].max()
        
        if col_min >= 0:  # Unsigned integers
            if col_max < 255:
                df_optimized[col] = df_optimized[col].astype('uint8')
            elif col_max < 65535:
                df_optimized[col] = df_optimized[col].astype('uint16')
            elif col_max < 4294967295:
                df_optimized[col] = df_optimized[col].astype('uint32')
        else:  # Signed integers
            if col_min >= -128 and col_max <= 127:
                df_optimized[col] = df_optimized[col].astype('int8')
            elif col_min >= -32768 and col_max <= 32767:
                df_optimized[col] = df_optimized[col].astype('int16')
            elif col_min >= -2147483648 and col_max <= 2147483647:
                df_optimized[col] = df_optimized[col].astype('int32')
    
    # Optimize floats
    float_cols = df_optimized.select_dtypes(include=['float64']).columns
    for col in float_cols:
        df_optimized[col] = pd.to_numeric(df_optimized[col], downcast='float')
    
    # Convert to categorical for low-cardinality object columns
    object_cols = df_optimized.select_dtypes(include=['object']).columns
    for col in object_cols:
        if df_optimized[col].nunique() / len(df_optimized) < 0.5:  # Less than 50% unique
            df_optimized[col] = df_optimized[col].astype('category')
    
    # Show memory savings
    original_memory = df.memory_usage(deep=True).sum() / 1024**2
    optimized_memory = df_optimized.memory_usage(deep=True).sum() / 1024**2
    print(f"Memory usage - Original: {original_memory:.2f} MB, Optimized: {optimized_memory:.2f} MB")
    print(f"Memory saved: {original_memory - optimized_memory:.2f} MB ({(1 - optimized_memory/original_memory)*100:.1f}%)")
    
    return df_optimized

# Apply optimization
df_optimized = optimize_dtypes(df_clean)
```

## 📈 Step 3: Exploratory Data Analysis (EDA)

### Automated EDA Function
```python
def quick_eda(df):
    """Quick exploratory data analysis"""
    print("=== QUICK EDA REPORT ===\n")
    
    # Dataset overview
    print(f"Dataset shape: {df.shape}")
    print(f"Columns: {list(df.columns)}")
    
    # Numerical columns analysis
    numerical_cols = df.select_dtypes(include=[np.number]).columns
    if len(numerical_cols) > 0:
        print(f"\n=== NUMERICAL COLUMNS ({len(numerical_cols)}) ===")
        print(df[numerical_cols].describe())
        
        # Create histograms for numerical columns
        n_cols = min(len(numerical_cols), 4)
        fig, axes = plt.subplots(nrows=(len(numerical_cols)-1)//n_cols + 1, 
                               ncols=n_cols, figsize=(15, 4*((len(numerical_cols)-1)//n_cols + 1)))
        if len(numerical_cols) == 1:
            axes = [axes]
        elif (len(numerical_cols)-1)//n_cols == 0:
            axes = axes.flatten()
        else:
            axes = axes.flatten()
            
        for i, col in enumerate(numerical_cols):
            if i < len(axes):
                df[col].hist(ax=axes[i], bins=30, alpha=0.7, edgecolor='black')
                axes[i].set_title(f'Distribution of {col}')
                axes[i].set_xlabel(col)
                axes[i].set_ylabel('Frequency')
        
        # Hide unused subplots
        for i in range(len(numerical_cols), len(axes)):
            axes[i].set_visible(False)
            
        plt.tight_layout()
        plt.show()
    
    # Categorical columns analysis
    categorical_cols = df.select_dtypes(include=['object', 'category']).columns
    if len(categorical_cols) > 0:
        print(f"\n=== CATEGORICAL COLUMNS ({len(categorical_cols)}) ===")
        for col in categorical_cols:
            print(f"\n{col}:")
            value_counts = df[col].value_counts()
            print(value_counts.head(10))
            
            # Create bar plot for categorical with few categories
            if df[col].nunique() <= 10:
                plt.figure(figsize=(10, 6))
                value_counts.plot(kind='bar')
                plt.title(f'Distribution of {col}')
                plt.xlabel(col)
                plt.ylabel('Count')
                plt.xticks(rotation=45)
                plt.tight_layout()
                plt.show()
    
    # Correlation analysis for numerical columns
    if len(numerical_cols) > 1:
        print(f"\n=== CORRELATION ANALYSIS ===")
        correlation_matrix = df[numerical_cols].corr()
        
        plt.figure(figsize=(10, 8))
        sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', center=0,
                    square=True, linewidths=0.5)
        plt.title('Correlation Matrix')
        plt.tight_layout()
        plt.show()
        
        # Find strong correlations
        high_corr_pairs = []
        for i in range(len(correlation_matrix.columns)):
            for j in range(i+1, len(correlation_matrix.columns)):
                corr_val = correlation_matrix.iloc[i, j]
                if abs(corr_val) > 0.7:
                    high_corr_pairs.append((
                        correlation_matrix.columns[i],
                        correlation_matrix.columns[j],
                        corr_val
                    ))
        
        if high_corr_pairs:
            print("Strong correlations (>0.7):")
            for var1, var2, corr in high_corr_pairs:
                print(f"  {var1} ↔ {var2}: {corr:.3f}")
        else:
            print("No strong correlations found (>0.7)")

# Run quick EDA
quick_eda(df_optimized)
```

### Advanced Analysis Patterns
```python
def analyze_relationships(df, target_column=None):
    """Analyze relationships between variables"""
    
    if target_column and target_column in df.columns:
        print(f"=== ANALYZING RELATIONSHIPS WITH {target_column} ===\n")
        
        # Numerical vs target
        numerical_cols = df.select_dtypes(include=[np.number]).columns
        numerical_cols = [col for col in numerical_cols if col != target_column]
        
        if len(numerical_cols) > 0 and df[target_column].dtype in [np.number]:
            # Scatter plots for numerical target
            n_cols = min(len(numerical_cols), 3)
            fig, axes = plt.subplots(1, n_cols, figsize=(5*n_cols, 4))
            if n_cols == 1:
                axes = [axes]
            
            for i, col in enumerate(numerical_cols[:n_cols]):
                df.plot.scatter(x=col, y=target_column, ax=axes[i], alpha=0.6)
                axes[i].set_title(f'{col} vs {target_column}')
            
            plt.tight_layout()
            plt.show()
        
        # Categorical vs target
        categorical_cols = df.select_dtypes(include=['object', 'category']).columns
        for col in categorical_cols:
            if col != target_column and df[col].nunique() <= 10:
                plt.figure(figsize=(10, 6))
                if df[target_column].dtype in [np.number]:
                    # Box plot for numerical target
                    sns.boxplot(data=df, x=col, y=target_column)
                    plt.title(f'{target_column} by {col}')
                    plt.xticks(rotation=45)
                else:
                    # Count plot for categorical target
                    sns.countplot(data=df, x=col, hue=target_column)
                    plt.title(f'{col} distribution by {target_column}')
                    plt.xticks(rotation=45)
                
                plt.tight_layout()
                plt.show()

# Example usage (if you have a target variable)
# analyze_relationships(df_optimized, target_column='total_bill')
```

## 🔧 Step 4: Feature Engineering

### Quick Feature Engineering
```python
def create_basic_features(df):
    """Create basic engineered features"""
    df_featured = df.copy()
    
    # For numerical columns - create binned versions
    numerical_cols = df_featured.select_dtypes(include=[np.number]).columns
    for col in numerical_cols:
        if df_featured[col].nunique() > 10:  # Only bin if many unique values
            try:
                df_featured[f'{col}_binned'] = pd.qcut(df_featured[col], q=5, 
                                                     labels=['Low', 'Med-Low', 'Medium', 'Med-High', 'High'])
            except:
                df_featured[f'{col}_binned'] = pd.cut(df_featured[col], bins=5,
                                                    labels=['Low', 'Med-Low', 'Medium', 'Med-High', 'High'])
    
    # For categorical columns - create frequency encoding
    categorical_cols = df_featured.select_dtypes(include=['object', 'category']).columns
    for col in categorical_cols:
        value_counts = df_featured[col].value_counts()
        df_featured[f'{col}_frequency'] = df_featured[col].map(value_counts)
    
    # Create interaction features for first 2 numerical columns
    if len(numerical_cols) >= 2:
        col1, col2 = numerical_cols[0], numerical_cols[1]
        df_featured[f'{col1}_{col2}_ratio'] = df_featured[col1] / (df_featured[col2] + 1)
        df_featured[f'{col1}_{col2}_sum'] = df_featured[col1] + df_featured[col2]
    
    print(f"Original features: {len(df.columns)}")
    print(f"Engineered features: {len(df_featured.columns)}")
    print(f"New features added: {len(df_featured.columns) - len(df.columns)}")
    
    return df_featured

# Apply feature engineering
df_featured = create_basic_features(df_optimized)
```

## 💾 Step 5: Save Processed Data

### Data Export and Versioning
```python
def save_processed_data(df, version='v1', description=''):
    """Save processed data with versioning"""
    from datetime import datetime
    
    # Create processed data directory
    processed_dir = Path('data/processed')
    processed_dir.mkdir(exist_ok=True)
    
    # Generate filename with timestamp and version
    timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
    filename = f'processed_data_{version}_{timestamp}.csv'
    filepath = processed_dir / filename
    
    # Save data
    df.to_csv(filepath, index=False)
    
    # Save metadata
    metadata = {
        'version': version,
        'timestamp': timestamp,
        'shape': df.shape,
        'columns': list(df.columns),
        'dtypes': df.dtypes.to_dict(),
        'description': description,
        'memory_usage_mb': df.memory_usage(deep=True).sum() / 1024**2
    }
    
    metadata_file = processed_dir / f'metadata_{version}_{timestamp}.json'
    import json
    with open(metadata_file, 'w') as f:
        json.dump(metadata, f, indent=2, default=str)
    
    print(f"Data saved to: {filepath}")
    print(f"Metadata saved to: {metadata_file}")
    print(f"Shape: {df.shape}")
    
    return str(filepath)

# Save processed data
data_path = save_processed_data(df_featured, version='v1', 
                               description='Initial cleaning and feature engineering')
```

## 📋 Complete Workflow Template

### Analysis Template Function
```python
def data_analysis_workflow(data_path, target_column=None):
    """Complete data analysis workflow"""
    print("🚀 STARTING DATA ANALYSIS WORKFLOW\n")
    
    # Step 1: Load data
    print("📂 Step 1: Loading data...")
    df = pd.read_csv(data_path)
    print(f"   Loaded {df.shape[0]} rows and {df.shape[1]} columns")
    
    # Step 2: Initial inspection
    print("\n🔍 Step 2: Initial data inspection...")
    inspect_data(df)
    
    # Step 3: Data cleaning
    print("\n🧹 Step 3: Data cleaning...")
    df_clean = handle_missing_data(df)
    df_optimized = optimize_dtypes(df_clean)
    
    # Step 4: EDA
    print("\n📊 Step 4: Exploratory data analysis...")
    quick_eda(df_optimized)
    
    if target_column:
        analyze_relationships(df_optimized, target_column)
    
    # Step 5: Feature engineering
    print("\n🔧 Step 5: Feature engineering...")
    df_featured = create_basic_features(df_optimized)
    
    # Step 6: Save results
    print("\n💾 Step 6: Saving processed data...")
    output_path = save_processed_data(df_featured, 
                                    description='Complete workflow processing')
    
    print("\n✅ WORKFLOW COMPLETED!")
    print(f"📈 Final dataset: {df_featured.shape}")
    print(f"💾 Saved to: {output_path}")
    
    return df_featured

# Example usage
# df_final = data_analysis_workflow('data/raw/dataset.csv', target_column='target')
```

### Project Documentation Template
```python
def generate_analysis_report(df, output_file='reports/analysis_report.md'):
    """Generate markdown report of the analysis"""
    from datetime import datetime
    
    # Create reports directory
    Path('reports').mkdir(exist_ok=True)
    
    report = f"""# Data Analysis Report
Generated on: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}

## Dataset Summary
- **Shape**: {df.shape[0]} rows × {df.shape[1]} columns
- **Memory Usage**: {df.memory_usage(deep=True).sum() / 1024**2:.2f} MB
- **Missing Values**: {df.isnull().sum().sum()} ({df.isnull().sum().sum() / (df.shape[0] * df.shape[1]) * 100:.2f}%)

## Data Types
{df.dtypes.value_counts().to_frame('Count').to_markdown()}

## Numerical Columns Summary
{df.describe().round(2).to_markdown()}

## Categorical Columns Summary
"""
    
    categorical_cols = df.select_dtypes(include=['object', 'category']).columns
    for col in categorical_cols[:5]:  # Limit to first 5
        report += f"\n### {col}\n"
        value_counts = df[col].value_counts().head(10)
        report += value_counts.to_frame('Count').to_markdown()
        report += "\n"
    
    report += f"""
## Key Findings
- Dataset contains {len(df.select_dtypes(include=[np.number]).columns)} numerical and {len(categorical_cols)} categorical variables
- Data quality is {'good' if df.isnull().sum().sum() < df.shape[0] * 0.05 else 'needs attention'} with {df.isnull().sum().sum()} missing values
- Memory optimized to {df.memory_usage(deep=True).sum() / 1024**2:.2f} MB

## Next Steps
- [ ] Feature engineering for specific use case
- [ ] Model development and validation
- [ ] Results interpretation and business recommendations
"""
    
    with open(output_file, 'w') as f:
        f.write(report)
    
    print(f"📄 Analysis report saved to: {output_file}")

# Generate report
# generate_analysis_report(df_featured)
```

## 🔗 Related Sections

- **[Environments & Dependencies](environments.md)** - Setting up Python environment
- **[Install & Setup](install-setup.md)** - Python and tool installation
- **[Project Structure](project-structure.md)** - Organizing analysis projects
- **[Data Cleaning](../data-analysis/data-cleaning.md)** - Advanced cleaning techniques

## ⚡ Quick Reference

**Project Setup:**
```bash
mkdir data-project && cd data-project
mkdir -p {data/{raw,processed},notebooks,src,reports}
python -m venv .venv && source .venv/bin/activate
pip install pandas numpy matplotlib seaborn jupyter
```

**Essential Imports:**
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from pathlib import Path
```

**Data Loading:**
```python
df = pd.read_csv('data.csv')            # CSV
df = pd.read_excel('data.xlsx')         # Excel
df = pd.read_json('data.json')          # JSON
df = pd.read_sql(query, connection)     # SQL
```

**Quick Analysis:**
```python
df.shape                                # Dimensions
df.info()                              # Overview
df.describe()                          # Statistics
df.isnull().sum()                      # Missing values
```

**Data Cleaning:**
```python
df.dropna()                            # Remove missing
df.fillna(df.mean())                   # Fill missing
df.drop_duplicates()                   # Remove duplicates
df.astype('category')                  # Optimize types
```

---

**Next:** Explore [Feature Engineering](../data-analysis/feature-engineering.md) for advanced feature creation or [Methodology](../data-analysis/methodology.md) for systematic analysis approaches.