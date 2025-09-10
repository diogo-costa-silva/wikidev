# Python

← [**🏠 Home**](../index.md)

> **Reproducible environments for scripts, notebooks, and small projects.**

Modern Python development is about creating consistent, reproducible environments that work the same way across different machines and team members. This section covers the essential tools and patterns for clean Python development.

## 🎯 Goals

- **Reproducible environments** with `pyenv` and `uv`
- **Clean project structure** that scales from scripts to packages
- **Effective workflows** for notebooks, testing, and CLI tools
- **Modern tooling** that actually makes development faster

## 🔍 Quick Verification

Test your Python environment:

```bash
# Check Python version management
pyenv --version
pyenv versions

# Check fast package manager
uv --version

# Check current Python and pip
python --version
python -m pip --version

# Test virtual environment creation
uv venv test-env --python 3.12
source test-env/bin/activate  # Linux/macOS
deactivate && rm -rf test-env
```

## 📚 Essential Guides

### Environment & Setup
- **[Install & Setup](install-setup.md)** - Get `pyenv` and `uv` working properly
- **[pyenv](pyenv.md)** - Python version management and project-specific versions
- **[uv](uv.md)** - Fast Python package manager and environment creation
- **[Environments & Dependencies](environments.md)** - Virtual environments and package management
- **[Project Structure](project-structure.md)** - `src/` layout, `.gitignore`, and `README` patterns

### Development Workflows
- **[Notebooks](notebooks.md)** - Jupyter and VS Code notebook best practices
- **[Data Workflow](data-workflow.md)** - Pandas quickstart for data projects
- **[CLI Basics](cli-basics.md)** - `python -m` usage and `argparse` patterns
- **[Testing](testing.md)** - pytest fundamentals and test organization

### Advanced (Optional)
- **Lint & Format** - `ruff` and `black` setup *(coming soon)*
- **Packaging** - Building and distributing Python packages *(coming soon)*

## 🚀 Common Workflows

### New Project Setup
1. **Create directory** → `mkdir my-project && cd my-project`
2. **Initialize environment** → [Install & Setup](install-setup.md)
3. **Set up structure** → [Project Structure](project-structure.md)
4. **Install dependencies** → [Environments & Dependencies](environments.md)
5. **Start coding** → Choose notebooks, scripts, or packages

### Typical Project Structure
```
my-project/
├── .gitignore           # Python-specific ignores
├── .python-version      # pyenv version pinning  
├── README.md           # Project documentation
├── pyproject.toml      # Modern Python configuration
├── requirements.txt    # or uv.lock for dependencies
├── src/               # Source code directory
│   └── my_project/    # Main package
├── tests/             # Test files
├── notebooks/         # Jupyter notebooks (optional)
└── scripts/           # Utility scripts (optional)
```

## 🔗 Related Sections

- **[Data Analysis](../data-analysis/index.md)** - Using Python for data exploration and analysis
- **[Git](../git/index.md)** - Version control workflow for Python projects
- **[AI Tools](../ai-tools/index.md)** - Using Claude Code and other AI tools with Python
- **[Toolkit](../toolkit/index.md)** - VS Code and development environment setup

## ⚡ Quick References

**Environment Management:**
```bash
# Create and activate virtual environment
uv venv .venv --python 3.12
source .venv/bin/activate  # Linux/macOS
# .venv\Scripts\activate   # Windows

# Install dependencies
uv pip install pandas matplotlib jupyter
uv pip install -r requirements.txt

# Generate requirements
uv pip freeze > requirements.txt
```

**Project Initialization:**
```bash
# Quick project setup
mkdir my-project && cd my-project
echo "3.12" > .python-version
uv venv .venv --python 3.12
source .venv/bin/activate
mkdir src tests
touch README.md .gitignore pyproject.toml
```

**Common Commands:**
```bash
# Run Python module
python -m my_package

# Run tests
python -m pytest

# Install in development mode
pip install -e .

# Start Jupyter
jupyter lab
```

**Basic Script Template:**
```python
#!/usr/bin/env python3
"""
Brief description of what this script does.
"""

def main():
    """Main function."""
    print("Hello, World!")

if __name__ == "__main__":
    main()
```

---

**Start here:** If you're new to modern Python development, begin with [Install & Setup](install-setup.md) to get `pyenv` and `uv` working, then follow [Project Structure](project-structure.md) for organizing your code.