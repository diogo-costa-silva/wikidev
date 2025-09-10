# Project Structure

← [**Voltar para Python**](index.md) | [**🏠 Home**](../index.md)

> **`src/` layout, `.gitignore`, and `README` patterns that scale from scripts to packages.**

A well-organized project structure makes code easier to find, test, and maintain. This guide covers modern Python project organization that works for everything from simple scripts to complex packages.

## 🎯 Goals

- **Consistent structure** across all Python projects
- **Scalable organization** from scripts to packages
- **Clean separation** of source code, tests, and documentation
- **Modern conventions** that work with current tooling

## 🔍 Quick Verification

A well-structured Python project should have:

```bash
# Check project structure
ls -la
# Should see: src/, tests/, README.md, .gitignore, requirements.txt

# Verify package installation works
pip install -e .
# Should install without errors

# Test import works
python -c "import your_package_name"
# Should import without errors
```

## 📁 Recommended Structure

### Modern Python Project Layout
```
my-python-project/
├── .gitignore              # Git ignore patterns
├── .python-version         # Python version for pyenv
├── README.md              # Project documentation
├── pyproject.toml         # Modern Python configuration
├── requirements.txt       # Dependencies (or use pyproject.toml)
├── src/                   # Source code directory
│   └── my_package/        # Main package
│       ├── __init__.py
│       ├── main.py        # Entry point
│       ├── core/          # Core functionality
│       │   ├── __init__.py
│       │   └── utils.py
│       └── data/          # Data processing
│           ├── __init__.py
│           └── loader.py
├── tests/                 # Test files
│   ├── __init__.py
│   ├── test_main.py
│   └── test_utils.py
├── docs/                  # Documentation (optional)
│   └── index.md
└── scripts/              # Utility scripts (optional)
    └── setup.py
```

### Why `src/` Layout?

The `src/` layout is preferred because:
- **Prevents accidental imports** during development
- **Forces proper installation** testing
- **Clear separation** between source and other files
- **Industry standard** for Python packages

## 🚀 Setting Up a New Project

### 1. Create Directory Structure
```bash
# Create project directory
mkdir my-python-project && cd my-python-project

# Create main directories
mkdir -p src/my_package/core src/my_package/data
mkdir tests docs scripts

# Create Python files
touch src/my_package/__init__.py
touch src/my_package/main.py
touch src/my_package/core/__init__.py
touch src/my_package/core/utils.py
touch tests/__init__.py
touch tests/test_main.py
```

### 2. Essential Configuration Files

#### `.python-version` (pyenv)
```bash
echo "3.12" > .python-version
```

#### `.gitignore` (Python-specific)
```gitignore
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# Virtual environments
.venv/
venv/
env/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Testing
.pytest_cache/
.coverage
htmlcov/

# Distribution / packaging
build/
dist/
*.egg-info/

# Environment variables
.env
.env.local
```

#### `pyproject.toml` (Modern configuration)
```toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "my-package"
version = "0.1.0"
description = "A sample Python package"
authors = [
    {name = "Your Name", email = "your.email@example.com"},
]
dependencies = [
    "pandas>=2.0.0",
    "numpy>=1.24.0",
]
requires-python = ">=3.11"

[project.optional-dependencies]
dev = [
    "pytest>=7.0",
    "black>=23.0",
    "ruff>=0.1.0",
]

[project.scripts]
my-package = "my_package.main:main"

[tool.setuptools.packages.find]
where = ["src"]

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]

[tool.black]
line-length = 88
target-version = ['py311']

[tool.ruff]
line-length = 88
target-version = "py311"
```

#### `README.md` Template
```markdown
# My Python Project

Brief description of what your project does.

## Installation

```bash
# Clone repository
git clone https://github.com/username/my-python-project.git
cd my-python-project

# Create virtual environment
uv venv .venv
source .venv/bin/activate

# Install package in development mode
pip install -e .
```

## Usage

```python
import my_package

# Example usage
result = my_package.do_something()
print(result)
```

## Development

```bash
# Install development dependencies
pip install -e .[dev]

# Run tests
pytest

# Format code
black src/ tests/

# Lint code
ruff check src/ tests/
```

## License

MIT License - see LICENSE file for details.
```

## 📝 Code Organization Patterns

### Package `__init__.py`
```python
"""My Package - A sample Python package."""

from .main import main_function
from .core.utils import utility_function

__version__ = "0.1.0"
__all__ = ["main_function", "utility_function"]
```

### Main Module Pattern
```python
# src/my_package/main.py
"""Main module with CLI entry point."""

import argparse
from .core.utils import process_data

def main():
    """Main function - entry point for CLI."""
    parser = argparse.ArgumentParser(description="My Python Package")
    parser.add_argument("--input", "-i", help="Input file")
    parser.add_argument("--output", "-o", help="Output file")
    
    args = parser.parse_args()
    
    if args.input:
        result = process_data(args.input)
        print(f"Processed: {result}")

if __name__ == "__main__":
    main()
```

### Utility Module Pattern
```python
# src/my_package/core/utils.py
"""Utility functions for data processing."""

from pathlib import Path
from typing import Union

def process_data(input_path: Union[str, Path]) -> dict:
    """Process input data and return results.
    
    Args:
        input_path: Path to input file
        
    Returns:
        Dictionary with processed results
    """
    input_path = Path(input_path)
    
    if not input_path.exists():
        raise FileNotFoundError(f"Input file not found: {input_path}")
    
    # Processing logic here
    return {"status": "processed", "file": str(input_path)}
```

## 🧪 Testing Structure

### Test Organization
```python
# tests/test_main.py
"""Tests for main module."""

import pytest
from my_package.main import main_function

def test_main_function():
    """Test main function works correctly."""
    result = main_function("test_input")
    assert result is not None
    assert isinstance(result, dict)

def test_main_function_with_invalid_input():
    """Test main function handles invalid input."""
    with pytest.raises(ValueError):
        main_function(None)
```

### Running Tests
```bash
# Install test dependencies
pip install -e .[dev]

# Run all tests
pytest

# Run with coverage
pytest --cov=src/my_package --cov-report=html

# Run specific test file
pytest tests/test_main.py

# Run with verbose output
pytest -v
```

## 🔧 Development Workflow

### 1. Initial Setup
```bash
mkdir new-project && cd new-project
echo "3.12" > .python-version
uv venv .venv
source .venv/bin/activate
```

### 2. Create Structure
```bash
mkdir -p src/my_package tests
touch src/my_package/__init__.py
touch src/my_package/main.py
# ... create other files
```

### 3. Install in Development Mode
```bash
pip install -e .
# Now you can import your package from anywhere
```

### 4. Development Cycle
```bash
# Make changes to code
# Run tests
pytest

# Format code
black src/ tests/

# Check linting
ruff check src/ tests/

# Install new dependencies
uv pip install new-package
```

## 🔗 Related Sections

- **[Install & Setup](install-setup.md)** - Setting up pyenv and uv
- **[Environments & Dependencies](environments.md)** - Managing virtual environments
- **[Testing](testing.md)** - pytest fundamentals and best practices
- **[CLI Basics](cli-basics.md)** - Building command-line interfaces

## ⚡ Quick Reference

**New Project Checklist:**
- [ ] Create `src/package_name/` directory
- [ ] Add `__init__.py` files
- [ ] Create `tests/` directory  
- [ ] Set up `.gitignore`
- [ ] Write `README.md`
- [ ] Configure `pyproject.toml`
- [ ] Install with `pip install -e .`

**Daily Commands:**
```bash
pip install -e .           # Install in development mode
pytest                     # Run tests
black src/ tests/          # Format code
ruff check src/ tests/     # Lint code
```

---

**Next:** Learn about [Environments & Dependencies](environments.md) for advanced environment management, or jump to [Testing](testing.md) to set up proper testing workflows.