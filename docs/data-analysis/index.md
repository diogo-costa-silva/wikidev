# Data Analysis

← [**🏠 Home**](../index.md)

> **Practical, reproducible workflows for data analysis from start to finish.**

Data analysis done right combines solid methodology with practical tools. This section provides battle-tested approaches that help you extract meaningful insights efficiently and communicate them effectively.

## 🎯 Goals

- **Reproducible workflows** that work consistently across projects
- **Practical methodologies** for exploratory data analysis (EDA)
- **Tool mastery** for pandas, visualization, and statistical analysis  
- **Clear communication** of findings and recommendations

## 🔍 Quick Verification

Test your data analysis environment:

```bash
# Check Python and key libraries
python -c "import pandas as pd; import matplotlib.pyplot as plt; import seaborn as sns; print('✅ Data analysis tools ready')"

# Check Jupyter setup
jupyter --version

# Verify sample dataset access
python -c "import pandas as pd; df = pd.read_csv('https://raw.githubusercontent.com/mwaskom/seaborn-data/master/tips.csv'); print(f'✅ Sample data loaded: {df.shape}')"
```

## 📚 Essential Guides

### Methodology & Process
- **[Methodology](methodology.md)** - EDA fundamentals and systematic approach to data exploration
- **[Data Cleaning](data-cleaning.md)** - Handle missing data, outliers, and quality issues
- **[Feature Engineering](feature-engineering.md)** - Create meaningful variables for analysis

### Tools & Techniques  
- **[Pandas Quick Cheats](pandas-cheats.md)** - Fast reference for common data manipulation tasks
- **[Visualization Principles](visualization.md)** - Create clear, impactful charts and graphs
- **[Communication of Insights](insights-communication.md)** - Present findings that drive decisions

### Advanced (Optional)
- **Metrics & Experiments** - A/B testing and statistical significance *(coming soon)*

## 🚀 Common Workflows

### New Dataset Exploration
1. **Load & inspect** → [Pandas Quick Cheats](pandas-cheats.md)
2. **Clean & validate** → [Data Cleaning](data-cleaning.md)  
3. **Explore patterns** → [Methodology](methodology.md)
4. **Visualize findings** → [Visualization Principles](visualization.md)
5. **Communicate results** → [Communication of Insights](insights-communication.md)

### Typical Analysis Pipeline
```python
# 1. Load and inspect
df = pd.read_csv('data.csv')
df.info()
df.describe()

# 2. Clean and prepare
df_clean = df.dropna().copy()
# ... cleaning steps

# 3. Explore and analyze
# ... EDA and statistical analysis

# 4. Visualize key findings
# ... create charts

# 5. Document insights
# ... write summary
```

## 🔗 Related Sections

- **[Python](../python/index.md)** - Environment setup and notebook workflows
- **[Databases](../databases/index.md)** - SQL queries and data extraction  
- **[Toolkit](../toolkit/index.md)** - Jupyter, pandas, and visualization tools

## ⚡ Quick References

**Pandas Essentials:**
```python
df.head()                    # First few rows
df.info()                    # Data types and memory
df.describe()                # Summary statistics
df.isnull().sum()           # Missing values count
df.value_counts()           # Frequency counts
```

**Visualization Starter:**
```python
import matplotlib.pyplot as plt
import seaborn as sns

# Quick distribution plot
sns.histplot(data=df, x='column_name')
plt.show()

# Correlation heatmap
sns.heatmap(df.corr(), annot=True, cmap='coolwarm')
plt.show()
```

---

**Start here:** If you're new to data analysis, begin with [Methodology](methodology.md) to understand the systematic approach, then move to [Pandas Quick Cheats](pandas-cheats.md) for practical tools.