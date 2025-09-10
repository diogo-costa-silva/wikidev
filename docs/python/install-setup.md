# Install & Setup

← [**Voltar para Python**](index.md) | [**🏠 Home**](../index.md)

> **Get `pyenv` and `uv` working properly for modern Python development.**

Modern Python development relies on proper version management and fast package installation. This guide sets up the essential tools that make Python development reproducible and efficient.

## 🎯 Goals

- **Python version management** with `pyenv`
- **Fast package management** with `uv` 
- **Reproducible environments** across different machines
- **Clean workflow** that scales from scripts to projects

## 🔍 Quick Verification

After following this guide, these commands should work:

```bash
# Check installations
pyenv --version
uv --version

# Check Python management
pyenv versions
python --version

# Test environment creation
uv venv test-env --python 3.12
source test-env/bin/activate  # Linux/macOS
python --version
deactivate && rm -rf test-env
```

## 🐍 Installing pyenv

### macOS (Recommended)
```bash
# Install via Homebrew
brew install pyenv

# Add to shell configuration
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo '[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc

# Reload shell
exec zsh
```

### Linux (Ubuntu/Debian)
```bash
# Install dependencies
sudo apt update
sudo apt install -y make build-essential libssl-dev zlib1g-dev \
    libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
    libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev \
    libffi-dev liblzma-dev

# Install pyenv
curl https://pyenv.run | bash

# Add to shell configuration
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo '[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc

# Reload shell
exec bash
```

### Verify pyenv Installation
```bash
pyenv --version
# Should show: pyenv 2.x.x or newer
```

## ⚡ Installing uv

### All Platforms
```bash
# Install uv (fast installer)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Or via pip if you prefer
pip install uv

# Or via Homebrew on macOS
brew install uv
```

### Verify uv Installation
```bash
uv --version
# Should show: uv 0.x.x or newer
```

## 🚀 Setup Workflow

### 1. Install Python Versions
```bash
# Install latest Python versions
pyenv install 3.12
pyenv install 3.11

# Set global default
pyenv global 3.12

# Verify
python --version  # Should show Python 3.12.x
```

### 2. Create Project Environment
```bash
# Navigate to your project
mkdir my-python-project && cd my-python-project

# Set project-specific Python version
echo "3.12" > .python-version

# Create virtual environment with uv
uv venv .venv --python 3.12

# Activate environment
source .venv/bin/activate  # Linux/macOS
# .venv\Scripts\activate   # Windows

# Verify environment
which python  # Should point to .venv/bin/python
python --version
```

### 3. Install Packages
```bash
# Install packages (much faster than pip)
uv pip install pandas matplotlib jupyter

# Install from requirements file
uv pip install -r requirements.txt

# Generate requirements
uv pip freeze > requirements.txt
```

## 📁 Recommended Project Structure

Set up your projects with this consistent structure:

```
my-python-project/
├── .python-version     # pyenv version file
├── .venv/             # virtual environment (gitignored)
├── requirements.txt   # dependencies
├── src/              # source code
│   └── my_project/   # main package
├── tests/            # test files  
├── README.md         # project documentation
└── .gitignore        # ignore .venv, __pycache__, etc.
```

## 🔧 Essential Commands

### pyenv Commands
```bash
# List available Python versions
pyenv install --list

# Install specific version
pyenv install 3.12.1

# List installed versions
pyenv versions

# Set global Python version
pyenv global 3.12.1

# Set local version for project
pyenv local 3.11.5

# Show current version
pyenv version
```

### uv Commands
```bash
# Create virtual environment
uv venv .venv --python 3.12

# Install packages
uv pip install package-name
uv pip install -r requirements.txt
uv pip install -e .  # Install in development mode

# List installed packages
uv pip list
uv pip freeze

# Upgrade packages
uv pip install --upgrade package-name
uv pip install --upgrade-all
```

## 🐛 Common Issues & Solutions

### pyenv: command not found
```bash
# Make sure shell configuration is loaded
exec $SHELL

# Check PATH includes pyenv
echo $PATH | grep pyenv

# Manually source if needed
source ~/.zshrc  # or ~/.bashrc
```

### Python version not switching
```bash
# Check for .python-version file
ls -la .python-version

# Verify pyenv is managing Python
which python
# Should show: /Users/username/.pyenv/shims/python

# Check pyenv status
pyenv version
```

### uv installation issues
```bash
# Try alternative installation
pip install --user uv

# Or download binary directly
curl -LsSf https://github.com/astral-sh/uv/releases/latest/download/uv-installer.sh | sh
```

## 🔗 Related Sections

- **[Environments & Dependencies](environments.md)** - Advanced environment management
- **[Project Structure](project-structure.md)** - Organizing Python projects
- **[Data Workflow](data-workflow.md)** - Setting up for data analysis
- **[Toolkit](../toolkit/index.md)** - VS Code setup and other tools

## ⚡ Quick Reference

**New Project Setup:**
```bash
mkdir project && cd project
echo "3.12" > .python-version
uv venv .venv --python 3.12
source .venv/bin/activate
uv pip install pandas jupyter
```

**Daily Commands:**
```bash
pyenv versions          # List Python versions
source .venv/bin/activate  # Activate environment  
uv pip install package    # Install package
uv pip freeze > requirements.txt  # Save dependencies
```

---

**Next:** Once you have Python and uv set up, learn about [Project Structure](project-structure.md) to organize your code effectively.