# Environments & Dependencies

← [**Voltar para Python**](index.md) | [**🏠 Home**](../index.md)

> **Virtual environment management and dependency handling with uv and pyenv.**

Managing Python environments and dependencies properly is crucial for reproducible development. This guide shows how to use modern tools like uv and pyenv to create isolated, manageable project environments.

## 🎯 Goals

- **Isolated environments** for each project
- **Reproducible dependencies** across machines
- **Fast environment creation** with uv
- **Consistent Python versions** with pyenv integration

## 🔍 Quick Verification

After following this guide, these commands should work:

```bash
# Check tools
uv --version
pyenv versions

# Create and activate environment
uv venv myproject
source myproject/bin/activate  # Linux/Mac
# or myproject\Scripts\activate  # Windows

# Check environment
which python
python --version
```

## 🏗️ Environment Creation with uv

### Basic Environment Setup
```bash
# Create virtual environment
uv venv myproject

# Activate environment
source myproject/bin/activate        # Linux/Mac
myproject\Scripts\activate.bat       # Windows CMD
myproject\Scripts\Activate.ps1       # Windows PowerShell

# Verify activation
which python                         # Should point to venv
python --version
```

### Project-Specific Environments
```bash
# Create environment in project directory
cd my-data-project
uv venv .venv                        # Common convention

# Activate project environment
source .venv/bin/activate

# Install project dependencies
uv pip install pandas numpy jupyter
```

### Environment with Specific Python Version
```bash
# List available Python versions
pyenv versions

# Use specific Python version for environment
uv venv --python 3.11 myproject-py311
uv venv --python 3.12 myproject-py312

# Or use pyenv python directly
pyenv shell 3.11.5
uv venv myproject-specific
```

## 📦 Dependency Management

### Installing Packages
```bash
# Install single package
uv pip install requests

# Install multiple packages
uv pip install pandas numpy matplotlib seaborn

# Install with specific versions
uv pip install "pandas>=1.5.0,<2.0.0"
uv pip install "numpy==1.24.3"

# Install from requirements file
uv pip install -r requirements.txt

# Install development dependencies
uv pip install -e ".[dev]"           # If using pyproject.toml
```

### Creating Requirements Files
```bash
# Generate requirements.txt
uv pip freeze > requirements.txt

# Create requirements with pinned versions
uv pip freeze --exclude-editable > requirements.txt

# Separate dev requirements
uv pip freeze | grep -E "(pytest|black|mypy)" > requirements-dev.txt
```

### Modern pyproject.toml Approach
Create `pyproject.toml` for better dependency management:

```toml
[project]
name = "my-data-project"
version = "0.1.0"
description = "Data analysis project"
authors = [{name = "Your Name", email = "your.email@example.com"}]
readme = "README.md"
license = {text = "MIT"}

dependencies = [
    "pandas>=1.5.0",
    "numpy>=1.24.0",
    "matplotlib>=3.6.0",
    "seaborn>=0.12.0"
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "black>=22.0.0",
    "mypy>=1.0.0",
    "jupyter>=1.0.0"
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

Install from pyproject.toml:
```bash
# Install main dependencies
uv pip install -e .

# Install with dev dependencies
uv pip install -e ".[dev]"
```

## 🔄 Environment Workflows

### Daily Development Workflow
```bash
# Start work session
cd my-project
source .venv/bin/activate

# Update dependencies if needed
uv pip install -r requirements.txt

# Work on project...

# End session (optional - closing terminal also works)
deactivate
```

### Project Handoff Workflow
```bash
# Package your environment for others
uv pip freeze > requirements.txt

# Include Python version info
python --version > .python-version

# Others can recreate your environment:
# pyenv install $(cat .python-version)
# pyenv shell $(cat .python-version)
# uv venv .venv
# source .venv/bin/activate
# uv pip install -r requirements.txt
```

### Multiple Project Management
```bash
# Project A (data analysis)
cd ~/projects/data-analysis
source .venv/bin/activate
python analysis.py

# Switch to Project B (web scraping)
deactivate
cd ~/projects/web-scraper
source .venv/bin/activate
python scraper.py
```

## 🔧 Advanced Environment Features

### Environment Variables
```bash
# Create .env file for project
echo "API_KEY=your_secret_key" > .env
echo "DATABASE_URL=sqlite:///data.db" >> .env

# Load in Python with python-dotenv
uv pip install python-dotenv
```

Python code:
```python
from dotenv import load_dotenv
import os

load_dotenv()

api_key = os.getenv('API_KEY')
db_url = os.getenv('DATABASE_URL')
```

### Custom Environment Scripts
Create activation scripts with custom paths:

```bash
# Create custom activate script
cat > .venv/bin/activate-custom << 'EOF'
#!/bin/bash
source .venv/bin/activate
export PYTHONPATH="${PYTHONPATH}:$(pwd)/src"
export DATA_DIR="$(pwd)/data"
echo "Environment activated with custom paths"
echo "Python: $(which python)"
echo "Data dir: $DATA_DIR"
EOF

chmod +x .venv/bin/activate-custom

# Use custom activation
source .venv/bin/activate-custom
```

### Global Tool Management
```bash
# Install global tools with uv
uv tool install jupyter
uv tool install black
uv tool install mypy

# List installed tools
uv tool list

# Update global tools
uv tool upgrade jupyter

# Use tools from any directory
jupyter notebook
black --check .
```

## 🧹 Environment Maintenance

### Cleaning Up Environments
```bash
# Remove virtual environment
rm -rf .venv

# Clean uv cache
uv cache clean

# Remove unused packages (be careful!)
uv pip freeze | grep -v "package-to-keep" | xargs uv pip uninstall
```

### Updating Dependencies
```bash
# Show outdated packages
uv pip list --outdated

# Update specific package
uv pip install --upgrade pandas

# Update all packages (use with caution)
uv pip freeze | sed 's/==.*//' | xargs uv pip install --upgrade
```

### Environment Health Check
```bash
# Check environment integrity
python -m pip check

# Verify key packages work
python -c "import pandas, numpy, matplotlib; print('All imports successful')"

# Check environment size
du -sh .venv/
```

## ⚠️ Common Issues & Solutions

### Environment Not Activating
```bash
# Check if activation script exists
ls .venv/bin/activate

# If missing, recreate environment
rm -rf .venv
uv venv .venv
source .venv/bin/activate
```

### Wrong Python Version in Environment
```bash
# Check what Python was used
cat .venv/pyvenv.cfg

# Recreate with correct Python
rm -rf .venv
pyenv shell 3.11.5
uv venv --python 3.11 .venv
```

### Package Installation Fails
```bash
# Check pip version
python -m pip --version

# Upgrade pip in environment
uv pip install --upgrade pip

# Install with verbose output for debugging
uv pip install --verbose problematic-package
```

### Dependency Conflicts
```bash
# Check for conflicts
python -m pip check

# Show dependency tree
uv pip install pipdeptree
pipdeptree

# Resolve conflicts by specifying compatible versions
uv pip install "package1>=1.0,<2.0" "package2>=2.0,<3.0"
```

## 📊 Project Templates

### Data Science Project Template
```bash
# Create data science environment
mkdir data-project && cd data-project
uv venv .venv
source .venv/bin/activate

# Install data science stack
uv pip install pandas numpy matplotlib seaborn jupyter scikit-learn

# Install development tools
uv pip install pytest black mypy ipykernel

# Create project structure
mkdir -p {data,notebooks,src,tests}
touch src/__init__.py

# Save environment
uv pip freeze > requirements.txt
echo "3.11.5" > .python-version
```

### Web Development Template
```bash
# Create web project environment
mkdir web-project && cd web-project
uv venv .venv
source .venv/bin/activate

# Install web stack
uv pip install fastapi uvicorn requests python-dotenv

# Install development tools
uv pip install pytest black mypy httpx

# Create project structure
mkdir -p {app,tests,static,templates}
touch app/__init__.py

# Save environment
uv pip freeze > requirements.txt
```

### CLI Tool Template
```bash
# Create CLI project environment
mkdir cli-tool && cd cli-tool
uv venv .venv
source .venv/bin/activate

# Install CLI essentials
uv pip install click typer rich

# Install development tools
uv pip install pytest black mypy

# Create project structure
mkdir -p {src,tests}
touch src/__init__.py

# Save environment
uv pip freeze > requirements.txt
```

## 🔗 Related Sections

- **[Install & Setup](install-setup.md)** - Setting up pyenv and uv
- **[Project Structure](project-structure.md)** - Organizing Python projects
- **[Data Workflow](data-workflow.md)** - Setting up pandas environment
- **[Testing](../testing/pytest-basics.md)** - Testing in virtual environments

## ⚡ Quick Reference

**Environment Creation:**
```bash
uv venv myproject                    # Create environment
source myproject/bin/activate        # Activate (Linux/Mac)
myproject\Scripts\activate          # Activate (Windows)
deactivate                          # Deactivate
```

**Package Management:**
```bash
uv pip install package              # Install package
uv pip install -r requirements.txt  # Install from file
uv pip freeze > requirements.txt    # Save dependencies
uv pip list --outdated             # Check for updates
```

**Project Setup:**
```bash
cd project-dir
uv venv .venv                       # Create local environment
source .venv/bin/activate           # Activate
uv pip install -e ".[dev]"         # Install with dev dependencies
```

**Troubleshooting:**
```bash
python -m pip check                 # Check for conflicts
rm -rf .venv && uv venv .venv       # Reset environment
uv cache clean                      # Clear cache
```

---

**Next:** Learn about [Project Structure](project-structure.md) for organizing Python projects, or explore [Data Workflow](data-workflow.md) for pandas-specific environment setup.