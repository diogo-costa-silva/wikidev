# pyenv

← [**Voltar para Python**](index.md) | [**🏠 Home**](../index.md)

> **Python version management for consistent development environments.**

pyenv lets you easily switch between multiple Python versions, ensuring consistent development environments across projects and team members. It's essential for modern Python development.

## 🎯 Goals

- **Multiple Python versions** installed and managed
- **Project-specific Python versions** with automatic switching
- **Global and local version control** for different needs
- **Integration with virtual environments** for complete isolation

## 🔍 Quick Verification

After following this guide, these commands should work:

```bash
# Check pyenv installation
pyenv --version

# List available Python versions
pyenv versions

# Check current Python version
python --version
which python

# Switch Python version temporarily
pyenv shell 3.11.5
python --version
```

## 📦 Installation

### macOS Installation
```bash
# Install using Homebrew (recommended)
brew install pyenv

# Or using pyenv-installer
curl https://pyenv.run | bash
```

### Linux Installation
```bash
# Install dependencies first
sudo apt update
sudo apt install -y make build-essential libssl-dev zlib1g-dev \
    libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
    libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev \
    libffi-dev liblzma-dev

# Install pyenv
curl https://pyenv.run | bash
```

### Shell Configuration
Add to your shell profile (`~/.bashrc`, `~/.zshrc`, or `~/.profile`):

```bash
# Add pyenv to PATH
export PYENV_ROOT="$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

# Optional: pyenv-virtualenv
eval "$(pyenv virtualenv-init -)"
```

Reload your shell:
```bash
source ~/.bashrc  # or ~/.zshrc
```

## 🐍 Python Version Management

### Installing Python Versions
```bash
# List all available Python versions
pyenv install --list

# List only CPython versions (most common)
pyenv install --list | grep "^\s*3\.[0-9]"

# Install specific Python versions
pyenv install 3.11.5
pyenv install 3.12.0
pyenv install 3.10.12

# Install latest version of Python 3.11
pyenv install 3.11

# Install with optimizations (slower but faster runtime)
PYTHON_CONFIGURE_OPTS="--enable-optimizations" pyenv install 3.11.5
```

### Switching Python Versions
```bash
# Set global Python version (system-wide default)
pyenv global 3.11.5

# Set local Python version (current directory and subdirectories)
cd my-project
pyenv local 3.10.12

# Set shell Python version (current shell session only)
pyenv shell 3.12.0

# Check current version
pyenv version

# List all installed versions
pyenv versions
```

### Version Priority Order
pyenv uses this priority order to determine which Python version to use:

1. **Shell version** (`pyenv shell 3.11.5`)
2. **Local version** (`.python-version` file)
3. **Global version** (`~/.pyenv/version` file)
4. **System version** (if pyenv can't find anything else)

## 📁 Project-Specific Versions

### Using .python-version Files
```bash
# Create project directory
mkdir my-data-project && cd my-data-project

# Set Python version for this project
pyenv local 3.11.5

# This creates a .python-version file
cat .python-version
# Output: 3.11.5

# Version is now automatic when you enter this directory
cd ..
python --version  # Global version

cd my-data-project
python --version  # 3.11.5 (local version)
```

### Team Collaboration
```bash
# Share Python version with team
echo "3.11.5" > .python-version
git add .python-version
git commit -m "Pin Python version to 3.11.5"

# Team members can install the same version
pyenv install $(cat .python-version)
# pyenv automatically uses this version in the project directory
```

### Multiple Python Versions in One Project
```bash
# Set multiple versions (first one is primary)
pyenv local 3.11.5 3.10.12 3.12.0

# Check versions
pyenv versions
# * 3.11.5 (set by /path/to/project/.python-version)
#   3.10.12 (set by /path/to/project/.python-version)
#   3.12.0 (set by /path/to/project/.python-version)

# Access specific versions
python3.11 --version
python3.10 --version
python3.12 --version
```

## 🔄 Integration with Virtual Environments

### pyenv + venv (Built-in)
```bash
# Create project with specific Python version
mkdir ml-project && cd ml-project
pyenv local 3.11.5

# Create virtual environment using local Python version
python -m venv .venv

# Activate environment
source .venv/bin/activate

# Verify Python version
python --version  # Should be 3.11.5
which python      # Points to .venv/bin/python
```

### pyenv + uv Integration
```bash
# Use pyenv-managed Python with uv
cd my-project
pyenv local 3.11.5

# Create uv environment with current pyenv Python
uv venv .venv --python $(which python)

# Or specify explicitly
uv venv .venv --python 3.11
```

### pyenv-virtualenv Plugin
```bash
# Install pyenv-virtualenv (if not already installed)
brew install pyenv-virtualenv

# Create named virtual environment
pyenv virtualenv 3.11.5 myproject-env

# Activate virtual environment
pyenv activate myproject-env

# Set as local environment for project
cd my-project
pyenv local myproject-env

# Deactivate
pyenv deactivate

# List virtual environments
pyenv virtualenvs

# Delete virtual environment
pyenv uninstall myproject-env
```

## 🛠️ Advanced Usage

### Building Python with Custom Options
```bash
# Enable optimizations (slower build, faster runtime)
PYTHON_CONFIGURE_OPTS="--enable-optimizations" pyenv install 3.11.5

# Enable shared library (needed for some applications)
PYTHON_CONFIGURE_OPTS="--enable-shared" pyenv install 3.11.5

# Debug build (for Python core development)
PYTHON_CONFIGURE_OPTS="--with-pydebug" pyenv install 3.11.5

# Multiple options
PYTHON_CONFIGURE_OPTS="--enable-optimizations --enable-shared" pyenv install 3.11.5
```

### Alternative Python Implementations
```bash
# Install PyPy (faster for some workloads)
pyenv install pypy3.10-7.3.12

# Install Miniconda
pyenv install miniconda3-latest

# Install specific pre-release
pyenv install 3.12.0rc3

# List available implementations
pyenv install --list | grep -E "(pypy|miniconda|anaconda)"
```

### Updating pyenv
```bash
# Update pyenv itself (Homebrew)
brew upgrade pyenv

# Update pyenv (git installation)
cd ~/.pyenv && git pull

# Update available Python versions database
pyenv install --list | head -20  # This updates the list
```

## 📊 Managing Multiple Projects

### Project Setup Script
Create a script to standardize project setup:

```bash
#!/bin/bash
# setup-python-project.sh

PROJECT_NAME=$1
PYTHON_VERSION=${2:-3.11.5}

if [ -z "$PROJECT_NAME" ]; then
    echo "Usage: $0 <project-name> [python-version]"
    exit 1
fi

# Create project directory
mkdir "$PROJECT_NAME" && cd "$PROJECT_NAME"

# Set Python version
pyenv local "$PYTHON_VERSION"

# Create virtual environment
python -m venv .venv

# Create .gitignore
cat > .gitignore << 'EOF'
.venv/
__pycache__/
*.pyc
.env
EOF

# Create README
echo "# $PROJECT_NAME" > README.md
echo "" >> README.md
echo "Python version: $PYTHON_VERSION" >> README.md

echo "Project $PROJECT_NAME created with Python $PYTHON_VERSION"
echo "To activate: cd $PROJECT_NAME && source .venv/bin/activate"
```

Usage:
```bash
chmod +x setup-python-project.sh
./setup-python-project.sh my-new-project 3.11.5
```

### Environment Switching Automation
Add to your shell configuration for automatic environment activation:

```bash
# Auto-activate virtual environment when .python-version exists
auto_venv() {
    if [ -f ".python-version" ] && [ -d ".venv" ]; then
        if [ -z "$VIRTUAL_ENV" ] || [ "$VIRTUAL_ENV" != "$(pwd)/.venv" ]; then
            echo "Activating virtual environment for $(basename $(pwd))"
            source .venv/bin/activate
        fi
    fi
}

# Add to prompt command (bash) or precmd (zsh)
# Bash
PROMPT_COMMAND="auto_venv; $PROMPT_COMMAND"

# Zsh
autoload -U add-zsh-hook
add-zsh-hook chpwd auto_venv
```

## 🧹 Maintenance and Cleanup

### Managing Installed Versions
```bash
# List all installed versions with sizes
du -sh ~/.pyenv/versions/*

# Remove old Python versions
pyenv uninstall 3.9.10
pyenv uninstall 3.8.15

# Clean up build artifacts
rm -rf ~/.pyenv/sources/*

# Rehash shims after installing packages
pyenv rehash
```

### Health Check Script
```bash
#!/bin/bash
# pyenv-health-check.sh

echo "=== pyenv Health Check ==="
echo "pyenv version: $(pyenv --version)"
echo "pyenv root: $PYENV_ROOT"

echo -e "\nInstalled Python versions:"
pyenv versions

echo -e "\nCurrent configuration:"
echo "Global: $(pyenv global)"
echo "Local: $(pyenv local 2>/dev/null || echo 'Not set')"
echo "Shell: $(pyenv shell 2>/dev/null || echo 'Not set')"

echo -e "\nCurrent Python:"
echo "Version: $(python --version)"
echo "Path: $(which python)"

echo -e "\nShims check:"
ls ~/.pyenv/shims/ | head -10
```

## ⚠️ Troubleshooting

### pyenv Command Not Found
```bash
# Check if pyenv is in PATH
echo $PATH | grep pyenv

# If not, add to shell configuration
echo 'export PATH="$HOME/.pyenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init --path)"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
source ~/.bashrc
```

### Python Version Not Switching
```bash
# Check version priority
pyenv version-name
pyenv which python

# Check for .python-version files
find . -name ".python-version" -exec cat {} \;

# Clear shell version if set
pyenv shell --unset

# Rehash shims
pyenv rehash
```

### Build Failures
```bash
# Install build dependencies (Ubuntu/Debian)
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev \
    libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
    libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev \
    libffi-dev liblzma-dev

# macOS - install command line tools
xcode-select --install

# Clear failed build
rm -rf ~/.pyenv/sources/Python-3.11.5/
pyenv install 3.11.5
```

### Performance Issues
```bash
# Check if using system Python by mistake
which python
# Should show ~/.pyenv/shims/python, not /usr/bin/python

# Rebuild shims if slow
rm -rf ~/.pyenv/shims/
pyenv rehash

# Check for conflicting Python installations
brew list | grep python
```

## 🔗 Related Sections

- **[Install & Setup](../python/install-setup.md)** - Setting up Python development environment
- **[Environments & Dependencies](../python/environments.md)** - Virtual environment management
- **[uv](uv.md)** - Modern Python package and environment management
- **[Project Structure](../python/project-structure.md)** - Organizing Python projects

## ⚡ Quick Reference

**Installation and Setup:**
```bash
brew install pyenv                  # macOS
curl https://pyenv.run | bash       # Linux
# Add to shell config and reload
```

**Version Management:**
```bash
pyenv install 3.11.5               # Install Python version
pyenv global 3.11.5                # Set global version
pyenv local 3.10.12                # Set local version
pyenv shell 3.12.0                 # Set shell version
pyenv versions                      # List installed versions
```

**Project Setup:**
```bash
cd my-project
pyenv local 3.11.5                 # Set Python version
python -m venv .venv                # Create virtual environment
source .venv/bin/activate           # Activate environment
```

**Maintenance:**
```bash
pyenv install --list               # List available versions
pyenv uninstall 3.9.10            # Remove version
pyenv rehash                       # Rebuild shims
du -sh ~/.pyenv/versions/*         # Check disk usage
```

**Troubleshooting:**
```bash
pyenv version-name                 # Check active version
pyenv which python                # Check Python path
pyenv doctor                       # Health check (if available)
```

---

**Next:** Learn about [uv](uv.md) for modern Python package management, or explore [Install & Setup](../python/install-setup.md) for complete Python environment configuration.