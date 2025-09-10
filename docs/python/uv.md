# uv - Fast Python Package Manager

← [**Voltar para Python**](index.md) | [**🏠 Home**](../index.md)

uv is a blazingly fast Python package installer and resolver written in Rust. It's designed as a drop-in replacement for pip and pip-tools, offering significantly faster dependency resolution and package installation.

## Installation

### Install uv
```bash
# macOS and Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows (PowerShell)
irm https://astral.sh/uv/install.ps1 | iex

# Via pip (if you have Python)
pip install uv

# Via Homebrew (macOS)
brew install uv

# Via cargo
cargo install uv
```

### Verify Installation
```bash
# Check version
uv --version

# Test basic functionality
uv pip list
```

## Basic Usage

### Package Installation
```bash
# Install packages (drop-in replacement for pip)
uv pip install requests
uv pip install "django>=4.0"
uv pip install -e .  # Editable install

# Install from requirements.txt
uv pip install -r requirements.txt

# Install with extras
uv pip install "fastapi[all]"
```

### Virtual Environment Management
```bash
# Create virtual environment
uv venv
uv venv .venv  # Specify directory
uv venv --python 3.11  # Specify Python version

# Activate environment
source .venv/bin/activate  # Linux/macOS
.venv\Scripts\activate     # Windows

# Install in virtual environment
uv pip install --system requests  # System-wide
uv pip install requests            # In activated venv
```

### Dependency Resolution
```bash
# Generate requirements.txt with resolved versions
uv pip compile requirements.in

# Upgrade dependencies
uv pip compile --upgrade requirements.in

# Sync environment with requirements
uv pip sync requirements.txt
```

## Advanced Features

### Lock Files and Reproducible Installs
```bash
# Generate lock file with exact versions
uv pip compile requirements.in --output-file requirements.txt

# Include development dependencies
uv pip compile requirements.in dev-requirements.in \
  --output-file requirements.txt

# Sync environment exactly to lock file
uv pip sync requirements.txt
```

### Multiple Python Versions
```bash
# Install specific Python version
uv python install 3.11
uv python install 3.12

# List available Python versions
uv python list

# Create venv with specific Python
uv venv --python 3.11 .venv-311
uv venv --python 3.12 .venv-312

# Pin Python version for project
echo "3.11" > .python-version
uv venv  # Uses version from .python-version
```

### Project Management
```bash
# Initialize new project
uv init my-project
cd my-project

# Add dependencies
uv add requests
uv add pytest --dev  # Development dependency
uv add "django>=4.0,<5.0"

# Remove dependencies  
uv remove requests
uv remove pytest --dev

# Run commands in project environment
uv run python script.py
uv run pytest
uv run --with jupyter jupyter lab
```

## Performance Comparison

### Speed Benchmarks
```bash
# Benchmark against pip
time pip install django
# vs
time uv pip install django

# Results typically show:
# pip: ~10-30 seconds
# uv: ~1-3 seconds (10x faster)
```

### Disk Usage
```bash
# uv uses less disk space through:
# - Shared package cache
# - Efficient storage format
# - Deduplication

# Check cache size
du -sh ~/.cache/uv/  # Linux/macOS
```

## Development Workflows

### Python Project Setup
```bash
# 1. Create project directory
mkdir my-python-project
cd my-python-project

# 2. Initialize with uv
uv init
# Creates: pyproject.toml, src/my_python_project/__init__.py

# 3. Add dependencies
uv add requests httpx
uv add pytest black ruff --dev

# 4. Create virtual environment
uv venv

# 5. Run development commands
uv run python -m pytest
uv run black .
uv run ruff check
```

### Requirements Management
```bash
# Create requirements.in for dependencies
cat > requirements.in << EOF
django>=4.2
psycopg2-binary
redis
celery
EOF

# Create dev-requirements.in for development
cat > dev-requirements.in << EOF
-r requirements.in
pytest
black
ruff
mypy
jupyter
EOF

# Generate lock files
uv pip compile requirements.in
uv pip compile dev-requirements.in

# Install for production
uv pip sync requirements.txt

# Install for development
uv pip sync dev-requirements.txt
```

### Docker Integration
```dockerfile
# Dockerfile with uv
FROM python:3.11-slim

# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/uv

# Copy requirements
COPY requirements.txt .

# Install dependencies
RUN uv pip install --system --no-cache -r requirements.txt

# Copy application code
COPY . /app
WORKDIR /app

CMD ["python", "app.py"]
```

## Configuration

### Configuration File
```toml
# pyproject.toml
[tool.uv]
# Use system Python by default
system-site-packages = true

# Cache directory
cache-dir = "~/.cache/uv"

# Index URL
index-url = "https://pypi.org/simple"

# Extra index URLs
extra-index-url = [
    "https://download.pytorch.org/whl/cpu"
]

# Trusted hosts
trusted-host = ["pypi.org"]

# Compile settings
[tool.uv.pip]
upgrade = false
no-deps = false
```

### Environment Variables
```bash
# Set default index
export UV_INDEX_URL="https://pypi.org/simple"

# Set cache directory
export UV_CACHE_DIR="~/.cache/uv"

# Disable cache
export UV_NO_CACHE=1

# Set Python path
export UV_PYTHON_PREFERENCE="only-managed"
```

## Integration with Development Tools

### VS Code Integration
```json
// .vscode/settings.json
{
    "python.defaultInterpreterPath": ".venv/bin/python",
    "python.terminal.activateEnvironment": true,
    "python.formatting.provider": "black",
    "python.linting.ruffEnabled": true
}
```

### Git Integration
```bash
# .gitignore additions for uv projects
.venv/
__pycache__/
*.pyc
*.pyo
*.pyd
.Python
.pytest_cache/
.coverage
htmlcov/

# Keep lock files in version control
# requirements.txt - KEEP
# pyproject.toml - KEEP
```

### Pre-commit Hooks
```yaml
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: uv-sync
        name: uv sync
        entry: uv sync
        language: system
        pass_filenames: false
        
      - id: uv-run-tests
        name: run tests with uv
        entry: uv run pytest
        language: system
        pass_filenames: false
```

## Migration from pip/pipenv/poetry

### From pip + requirements.txt
```bash
# 1. Install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# 2. Create virtual environment  
uv venv

# 3. Install existing requirements
uv pip install -r requirements.txt

# 4. Generate new lock file
echo "# Add your direct dependencies here" > requirements.in
# Edit requirements.in with your direct dependencies
uv pip compile requirements.in
```

### From pipenv
```bash
# 1. Export Pipfile to requirements
pipenv requirements > requirements.txt
pipenv requirements --dev > dev-requirements.txt

# 2. Create requirements.in files
# Extract direct dependencies from Pipfile to requirements.in
# Extract dev dependencies to dev-requirements.in

# 3. Switch to uv
uv venv
uv pip compile requirements.in
uv pip compile dev-requirements.in
uv pip sync dev-requirements.txt
```

### From poetry
```bash
# 1. Export poetry dependencies
poetry export -f requirements.txt --output requirements.txt
poetry export -f requirements.txt --dev --output dev-requirements.txt

# 2. Create uv project
uv init --no-readme
# Copy dependencies from pyproject.toml to new format

# 3. Install with uv
uv sync
```

## Best Practices

### Project Structure
```bash
my-project/
├── pyproject.toml          # Project configuration
├── requirements.in         # Direct dependencies
├── requirements.txt        # Locked dependencies
├── dev-requirements.in     # Development dependencies  
├── dev-requirements.txt    # Locked dev dependencies
├── .python-version         # Python version pin
├── .venv/                  # Virtual environment
├── src/
│   └── my_project/
│       └── __init__.py
└── tests/
    └── test_main.py
```

### Dependency Management
```bash
# Pin major versions in .in files
django>=4.2,<5.0
requests>=2.28
pytest>=7.0

# Keep .txt files in version control
git add requirements.txt dev-requirements.txt

# Regenerate lock files regularly
uv pip compile --upgrade requirements.in
uv pip compile --upgrade dev-requirements.in
```

### Performance Tips
```bash
# Use --no-cache for CI/CD
uv pip install --no-cache -r requirements.txt

# Pre-download wheels for offline installation
uv pip download -r requirements.txt -d wheels/

# Install from local wheels
uv pip install --find-links wheels/ -r requirements.txt --no-index
```

## Common Use Cases

### Data Science Projects
```bash
# Create data science environment
uv init data-project
cd data-project

# Add data science packages
uv add pandas numpy matplotlib seaborn
uv add jupyter ipykernel scikit-learn
uv add plotly --optional viz  # Optional extras

# Development tools
uv add pytest black ruff --dev

# Run jupyter
uv run jupyter lab
```

### Web Development
```bash
# Django project
uv init django-app
cd django-app

uv add "django>=4.2" psycopg2-binary redis
uv add gunicorn whitenoise
uv add pytest-django black ruff --dev

# FastAPI project  
uv init fastapi-app
cd fastapi-app

uv add "fastapi[all]" sqlalchemy alembic
uv add pytest httpx --dev

uv run uvicorn main:app --reload
```

### Package Development
```bash
# Create package project
uv init --lib my-package
cd my-package

# Add build dependencies
uv add build twine --dev

# Add testing dependencies
uv add pytest pytest-cov tox --dev

# Build package
uv run python -m build

# Upload to PyPI
uv run twine upload dist/*
```

## Troubleshooting

### Common Issues
```bash
# Clear cache if having issues
uv cache clean

# Force reinstall
uv pip install --force-reinstall package-name

# Verbose output for debugging
uv pip install -v requests

# Check environment
uv pip list
uv pip check  # Verify compatibility
```

### Python Version Issues
```bash
# List available Python versions
uv python list

# Install missing Python version
uv python install 3.11

# Force specific Python
uv venv --python /usr/bin/python3.11 .venv
```

### Dependency Conflicts
```bash
# Show dependency tree
uv pip show --files package-name

# Resolve conflicts with compile
uv pip compile --resolver=backtracking requirements.in

# Override conflicting versions
echo "package-name==1.2.3" >> requirements.in
```

## Comparison with Other Tools

| Feature | uv | pip | poetry | pipenv |
|---------|----|----|---------|---------|
| **Speed** | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **Memory Usage** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **Compatibility** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| **Lock Files** | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Learning Curve** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |

## Resources

**Official:**
- [uv Documentation](https://docs.astral.sh/uv/)
- [GitHub Repository](https://github.com/astral-sh/uv)
- [Installation Guide](https://docs.astral.sh/uv/getting-started/installation/)

**Related Tools:**
- **[pyenv](pyenv.md)** - Python version management
- **[Python Environments](../python/environments.md)** - Environment setup patterns
- **[Python Project Structure](../python/project-structure.md)** - Project organization