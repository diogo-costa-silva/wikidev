# Feature Engineering

← [**Voltar para Data Analysis**](index.md) | [**🏠 Home**](../index.md)

> **Creating and transforming variables to improve model performance and insights.**

Feature engineering is the process of using domain knowledge to create new features from raw data. Good features can make the difference between a mediocre and excellent model, and often provide valuable business insights.

## 🎯 Goals

- **Create meaningful features** from raw data
- **Transform variables** for better model performance
- **Handle categorical data** effectively
- **Engineer time-based features** for temporal data

## 🔍 Quick Verification

Test feature engineering with sample data:

```python
import pandas as pd
import numpy as np
from datetime import datetime, timedelta

# Create sample dataset
np.random.seed(42)
df = pd.DataFrame({
    'customer_id': range(1, 1001),
    'age': np.random.randint(18, 80, 1000),
    'income': np.random.randint(20000, 150000, 1000),
    'purchase_date': pd.date_range('2023-01-01', periods=1000, freq='H'),
    'product_category': np.random.choice(['Electronics', 'Clothing', 'Books', 'Home'], 1000),
    'purchase_amount': np.random.uniform(10, 1000, 1000),
    'city': np.random.choice(['NYC', 'LA', 'Chicago', 'Houston', 'Phoenix'], 1000)
})

print("Sample data:")
print(df.head())
print(f"\nDataset shape: {df.shape}")
```

## 🛠️ Basic Feature Engineering

### Numerical Feature Transformations

#### Mathematical Transformations
```python
# Log transformations for skewed distributions
df['income_log'] = np.log1p(df['income'])  # log1p handles zeros
df['purchase_amount_log'] = np.log1p(df['purchase_amount'])

# Square root transformation
df['age_sqrt'] = np.sqrt(df['age'])

# Power transformations
df['income_squared'] = df['income'] ** 2
df['purchase_amount_cubed'] = df['purchase_amount'] ** 3

# Reciprocal transformation (for rates)
df['income_reciprocal'] = 1 / (df['income'] + 1)  # Add 1 to avoid division by zero
```

#### Binning and Discretization
```python
# Equal-width binning
df['age_binned'] = pd.cut(df['age'], bins=5, labels=['Very Young', 'Young', 'Middle', 'Mature', 'Senior'])

# Equal-frequency binning (quantiles)
df['income_quartiles'] = pd.qcut(df['income'], q=4, labels=['Low', 'Medium-Low', 'Medium-High', 'High'])

# Custom binning with domain knowledge
age_bins = [0, 25, 35, 50, 65, 100]
age_labels = ['Gen Z', 'Millennial', 'Gen X', 'Baby Boomer', 'Silent Gen']
df['generation'] = pd.cut(df['age'], bins=age_bins, labels=age_labels, include_lowest=True)

# Income brackets
income_bins = [0, 30000, 60000, 100000, np.inf]
income_labels = ['Low Income', 'Middle Income', 'Upper Middle', 'High Income']
df['income_bracket'] = pd.cut(df['income'], bins=income_bins, labels=income_labels, include_lowest=True)
```

#### Scaling and Normalization
```python
from sklearn.preprocessing import StandardScaler, MinMaxScaler, RobustScaler

# Standard scaling (z-score normalization)
scaler_std = StandardScaler()
df['age_standardized'] = scaler_std.fit_transform(df[['age']])

# Min-Max scaling (0-1 range)
scaler_minmax = MinMaxScaler()
df['income_normalized'] = scaler_minmax.fit_transform(df[['income']])

# Robust scaling (less sensitive to outliers)
scaler_robust = RobustScaler()
df['purchase_amount_robust'] = scaler_robust.fit_transform(df[['purchase_amount']])
```

### Categorical Feature Engineering

#### Encoding Techniques
```python
# One-hot encoding
category_dummies = pd.get_dummies(df['product_category'], prefix='category')
df = pd.concat([df, category_dummies], axis=1)

# Label encoding for ordinal categories
from sklearn.preprocessing import LabelEncoder
label_encoder = LabelEncoder()
df['city_encoded'] = label_encoder.fit_transform(df['city'])

# Ordinal encoding for ordered categories
income_bracket_mapping = {'Low Income': 1, 'Middle Income': 2, 'Upper Middle': 3, 'High Income': 4}
df['income_bracket_ordinal'] = df['income_bracket'].map(income_bracket_mapping)
```

#### Target Encoding
```python
# Create target variable for example
df['high_value_customer'] = (df['purchase_amount'] > df['purchase_amount'].quantile(0.75)).astype(int)

# Target encoding (mean of target for each category)
target_encoding = df.groupby('product_category')['high_value_customer'].mean()
df['category_target_encoded'] = df['product_category'].map(target_encoding)

print("Target encoding values:")
print(target_encoding)
```

#### Frequency Encoding
```python
# Frequency encoding (how often each category appears)
category_counts = df['product_category'].value_counts()
df['category_frequency'] = df['product_category'].map(category_counts)

# Relative frequency
df['category_relative_freq'] = df['category_frequency'] / len(df)
```

## 📅 Time-Based Feature Engineering

### Date and Time Features
```python
# Extract date components
df['purchase_year'] = df['purchase_date'].dt.year
df['purchase_month'] = df['purchase_date'].dt.month
df['purchase_day'] = df['purchase_date'].dt.day
df['purchase_hour'] = df['purchase_date'].dt.hour
df['purchase_dayofweek'] = df['purchase_date'].dt.dayofweek
df['purchase_dayofyear'] = df['purchase_date'].dt.dayofyear

# Day names and month names
df['purchase_day_name'] = df['purchase_date'].dt.day_name()
df['purchase_month_name'] = df['purchase_date'].dt.month_name()

# Weekend indicator
df['is_weekend'] = (df['purchase_dayofweek'] >= 5).astype(int)

# Business hours indicator
df['is_business_hours'] = ((df['purchase_hour'] >= 9) & (df['purchase_hour'] <= 17)).astype(int)

# Season extraction
def get_season(month):
    if month in [12, 1, 2]:
        return 'Winter'
    elif month in [3, 4, 5]:
        return 'Spring'
    elif month in [6, 7, 8]:
        return 'Summer'
    else:
        return 'Fall'

df['season'] = df['purchase_month'].apply(get_season)
```

### Cyclical Features
```python
# Encode cyclical features (hour, day, month) as sine/cosine
def encode_cyclical(data, col, max_val):
    data[col + '_sin'] = np.sin(2 * np.pi * data[col] / max_val)
    data[col + '_cos'] = np.cos(2 * np.pi * data[col] / max_val)
    return data

# Encode hour (0-23)
df = encode_cyclical(df, 'purchase_hour', 24)

# Encode day of week (0-6)
df = encode_cyclical(df, 'purchase_dayofweek', 7)

# Encode month (1-12)
df = encode_cyclical(df, 'purchase_month', 12)
```

### Time-Based Aggregations
```python
# Sort by customer and date for time-based features
df = df.sort_values(['customer_id', 'purchase_date'])

# Days since first purchase
df['days_since_first_purchase'] = df.groupby('customer_id')['purchase_date'].transform(
    lambda x: (x - x.min()).dt.days
)

# Time since last purchase
df['days_since_last_purchase'] = df.groupby('customer_id')['purchase_date'].transform(
    lambda x: (x.max() - x).dt.days
)

# Purchase frequency features
purchase_freq = df.groupby('customer_id').size()
df['customer_purchase_frequency'] = df['customer_id'].map(purchase_freq)

# Rolling averages (example with 30-day window)
df['rolling_avg_amount'] = df.groupby('customer_id')['purchase_amount'].transform(
    lambda x: x.rolling(window=30, min_periods=1).mean()
)
```

## 🔢 Advanced Feature Engineering

### Interaction Features
```python
# Numerical interactions
df['age_income_interaction'] = df['age'] * df['income']
df['age_income_ratio'] = df['income'] / (df['age'] + 1)  # Avoid division by zero

# Polynomial features
from sklearn.preprocessing import PolynomialFeatures
poly = PolynomialFeatures(degree=2, interaction_only=True, include_bias=False)
numerical_cols = ['age', 'income']
poly_features = poly.fit_transform(df[numerical_cols])
poly_feature_names = poly.get_feature_names_out(numerical_cols)

# Add polynomial features to dataframe
for i, name in enumerate(poly_feature_names):
    if name not in numerical_cols:  # Skip original features
        df[f'poly_{name}'] = poly_features[:, i]
```

### Statistical Features
```python
# Group-based statistical features
customer_stats = df.groupby('customer_id').agg({
    'purchase_amount': ['mean', 'std', 'min', 'max', 'sum', 'count'],
    'age': 'first',  # Customer demographics don't change
    'income': 'first'
}).round(2)

# Flatten column names
customer_stats.columns = ['_'.join(col).strip() for col in customer_stats.columns]
customer_stats = customer_stats.reset_index()

# Category-based features
category_stats = df.groupby('product_category').agg({
    'purchase_amount': ['mean', 'median'],
    'age': 'mean',
    'income': 'mean'
}).round(2)

category_stats.columns = ['_'.join(col).strip() for col in category_stats.columns]
category_stats = category_stats.reset_index()

print("Customer statistics sample:")
print(customer_stats.head())
```

### Distance and Similarity Features
```python
# Distance from group means
category_means = df.groupby('product_category')['income'].mean()
df['income_distance_from_category_mean'] = df.apply(
    lambda row: abs(row['income'] - category_means[row['product_category']]), axis=1
)

# Z-score within groups
df['income_zscore_within_category'] = df.groupby('product_category')['income'].transform(
    lambda x: (x - x.mean()) / x.std()
)

# Percentile rank within groups
df['income_percentile_within_category'] = df.groupby('product_category')['income'].transform(
    lambda x: x.rank(pct=True)
)
```

## 🎯 Domain-Specific Features

### Customer Behavior Features
```python
# Customer lifetime value proxy
df['customer_lifetime_value'] = df.groupby('customer_id')['purchase_amount'].transform('sum')

# Purchase pattern features
df['avg_purchase_amount'] = df.groupby('customer_id')['purchase_amount'].transform('mean')
df['purchase_consistency'] = df.groupby('customer_id')['purchase_amount'].transform('std')

# Recency, Frequency, Monetary (RFM) features
current_date = df['purchase_date'].max()

rfm_features = df.groupby('customer_id').agg({
    'purchase_date': lambda x: (current_date - x.max()).days,  # Recency
    'customer_id': 'count',  # Frequency
    'purchase_amount': 'sum'  # Monetary
}).rename(columns={
    'purchase_date': 'recency_days',
    'customer_id': 'frequency',
    'purchase_amount': 'monetary_total'
})

# Score each RFM component
rfm_features['recency_score'] = pd.qcut(rfm_features['recency_days'], 5, labels=[5,4,3,2,1])
rfm_features['frequency_score'] = pd.qcut(rfm_features['frequency'].rank(method='first'), 5, labels=[1,2,3,4,5])
rfm_features['monetary_score'] = pd.qcut(rfm_features['monetary_total'], 5, labels=[1,2,3,4,5])

# Combine RFM scores
rfm_features['rfm_score'] = (
    rfm_features['recency_score'].astype(str) + 
    rfm_features['frequency_score'].astype(str) + 
    rfm_features['monetary_score'].astype(str)
)

print("RFM Features sample:")
print(rfm_features.head())
```

### Text-Based Features (if applicable)
```python
# Example with product descriptions (if available)
# This is a conceptual example - adapt based on your text data

# If you have text data like product descriptions or reviews
sample_text_data = pd.DataFrame({
    'customer_id': [1, 2, 3, 4, 5],
    'review_text': [
        'Great product, fast delivery',
        'Poor quality, not recommended',
        'Excellent value for money',
        'Good product, will buy again',
        'Average product, decent price'
    ]
})

# Text length features
sample_text_data['review_length'] = sample_text_data['review_text'].str.len()
sample_text_data['review_word_count'] = sample_text_data['review_text'].str.split().str.len()

# Sentiment analysis (requires textblob: pip install textblob)
try:
    from textblob import TextBlob
    sample_text_data['review_sentiment'] = sample_text_data['review_text'].apply(
        lambda x: TextBlob(x).sentiment.polarity
    )
except ImportError:
    print("TextBlob not installed. Install with: pip install textblob")
```

## 🧪 Feature Selection and Validation

### Feature Importance
```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.feature_selection import SelectKBest, f_regression

# Prepare features for selection (numerical only)
feature_columns = df.select_dtypes(include=[np.number]).columns
feature_columns = [col for col in feature_columns if col != 'high_value_customer']  # Exclude target

X = df[feature_columns].fillna(0)  # Handle any NaN values
y = df['high_value_customer']

# Random Forest feature importance
rf = RandomForestRegressor(n_estimators=100, random_state=42)
rf.fit(X, y)

feature_importance = pd.DataFrame({
    'feature': feature_columns,
    'importance': rf.feature_importances_
}).sort_values('importance', ascending=False)

print("Top 10 most important features:")
print(feature_importance.head(10))
```

### Feature Correlation Analysis
```python
# Correlation matrix for numerical features
correlation_matrix = df[feature_columns].corr()

# Find highly correlated features
def find_correlated_features(corr_matrix, threshold=0.9):
    # Create a mask for the upper triangle
    upper_tri = corr_matrix.where(
        np.triu(np.ones_like(corr_matrix, dtype=bool), k=1)
    )
    
    # Find features with correlation above threshold
    high_corr_pairs = []
    for column in upper_tri.columns:
        correlated_features = upper_tri.index[abs(upper_tri[column]) > threshold].tolist()
        for feature in correlated_features:
            high_corr_pairs.append((feature, column, upper_tri.loc[feature, column]))
    
    return high_corr_pairs

high_corr = find_correlated_features(correlation_matrix)
if high_corr:
    print("Highly correlated feature pairs (>0.9):")
    for feature1, feature2, corr in high_corr:
        print(f"{feature1} - {feature2}: {corr:.3f}")
else:
    print("No highly correlated features found.")
```

## 📊 Feature Engineering Pipeline

### Automated Feature Engineering Pipeline
```python
def feature_engineering_pipeline(df):
    """Comprehensive feature engineering pipeline"""
    df_featured = df.copy()
    
    # 1. Basic transformations
    if 'income' in df_featured.columns:
        df_featured['income_log'] = np.log1p(df_featured['income'])
        df_featured['income_sqrt'] = np.sqrt(df_featured['income'])
    
    if 'age' in df_featured.columns:
        # Age groups
        df_featured['age_group'] = pd.cut(df_featured['age'], 
                                        bins=[0, 25, 35, 50, 65, 100],
                                        labels=['18-25', '26-35', '36-50', '51-65', '65+'])
    
    # 2. Date features
    date_columns = df_featured.select_dtypes(include=['datetime64']).columns
    for col in date_columns:
        df_featured[f'{col}_year'] = df_featured[col].dt.year
        df_featured[f'{col}_month'] = df_featured[col].dt.month
        df_featured[f'{col}_dayofweek'] = df_featured[col].dt.dayofweek
        df_featured[f'{col}_hour'] = df_featured[col].dt.hour
        df_featured[f'{col}_is_weekend'] = (df_featured[col].dt.dayofweek >= 5).astype(int)
    
    # 3. Categorical encoding
    categorical_columns = df_featured.select_dtypes(include=['object']).columns
    for col in categorical_columns:
        if df_featured[col].nunique() < 10:  # Only for low-cardinality categories
            dummies = pd.get_dummies(df_featured[col], prefix=col)
            df_featured = pd.concat([df_featured, dummies], axis=1)
    
    print(f"Original features: {len(df.columns)}")
    print(f"Engineered features: {len(df_featured.columns)}")
    print(f"New features added: {len(df_featured.columns) - len(df.columns)}")
    
    return df_featured

# Apply the pipeline
df_engineered = feature_engineering_pipeline(df)
```

### Feature Engineering Best Practices
```python
def validate_engineered_features(df_original, df_engineered):
    """Validate the quality of engineered features"""
    
    print("=== FEATURE ENGINEERING VALIDATION ===\n")
    
    # 1. Shape comparison
    print(f"Original shape: {df_original.shape}")
    print(f"Engineered shape: {df_engineered.shape}")
    print(f"Features added: {df_engineered.shape[1] - df_original.shape[1]}")
    
    # 2. Missing values check
    original_missing = df_original.isnull().sum().sum()
    engineered_missing = df_engineered.isnull().sum().sum()
    print(f"\nMissing values - Original: {original_missing}, Engineered: {engineered_missing}")
    
    # 3. Data types distribution
    print(f"\nData types in engineered dataset:")
    print(df_engineered.dtypes.value_counts())
    
    # 4. Memory usage
    original_memory = df_original.memory_usage(deep=True).sum() / 1024**2
    engineered_memory = df_engineered.memory_usage(deep=True).sum() / 1024**2
    print(f"\nMemory usage - Original: {original_memory:.2f} MB, Engineered: {engineered_memory:.2f} MB")
    
    print("\n" + "="*50)

# Validate features
validate_engineered_features(df, df_engineered)
```

## 🔗 Related Sections

- **[Data Cleaning](data-cleaning.md)** - Clean data before feature engineering
- **[Methodology](methodology.md)** - EDA approach for feature discovery
- **[Pandas Quick Cheats](pandas-cheats.md)** - Data manipulation for feature creation
- **[Visualization Principles](visualization.md)** - Visualize feature distributions and relationships

## ⚡ Quick Reference

**Basic Transformations:**
```python
# Log transformation
np.log1p(df['column'])

# Binning
pd.cut(df['column'], bins=5)
pd.qcut(df['column'], q=4)

# Scaling
from sklearn.preprocessing import StandardScaler
StandardScaler().fit_transform(df[['column']])
```

**Categorical Encoding:**
```python
# One-hot encoding
pd.get_dummies(df['category'])

# Label encoding
from sklearn.preprocessing import LabelEncoder
LabelEncoder().fit_transform(df['category'])

# Target encoding
df.groupby('category')['target'].mean()
```

**Time Features:**
```python
# Date components
df['date'].dt.year
df['date'].dt.month
df['date'].dt.dayofweek

# Cyclical encoding
def encode_cyclical(data, col, max_val):
    data[col + '_sin'] = np.sin(2 * np.pi * data[col] / max_val)
    data[col + '_cos'] = np.cos(2 * np.pi * data[col] / max_val)
```

**Interactions:**
```python
# Numerical interactions
df['col1_col2_interaction'] = df['col1'] * df['col2']

# Polynomial features
from sklearn.preprocessing import PolynomialFeatures
PolynomialFeatures(degree=2).fit_transform(df[numerical_cols])
```

---

**Next:** Apply engineered features in modeling or explore [Pandas Quick Cheats](pandas-cheats.md) for more data manipulation techniques.