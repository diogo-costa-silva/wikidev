# Toolkit

← [**🏠 Home**](../index.md)

> **Installation and quick-reference guides for essential development tools.**

The right tools make development faster and more enjoyable. This section provides practical installation guides and quick references for the essential tools that support modern development workflows.

## 🎯 Goals

- **Streamlined installation** guides that actually work
- **Quick reference** for common tool operations
- **Integration tips** for connecting tools together
- **Maintenance guidance** to keep tools updated and working

## 🔍 Quick Verification

Test your essential toolkit:

```bash
# Documentation and deployment
mkdocs --version

# Code editor (check VS Code or Cursor)
code --version || cursor --version
```

## 🛠️ Essential Tools

### Documentation & Deployment
- **[MkDocs](mkdocs.md)** - Modern documentation with Material theme (install, serve, build, deploy)

### Code Editors
- **[VS Code / Cursor](vscode-cursor.md)** - Essential extensions and productivity shortcuts

### System Tools (Optional)
- **Homebrew Essentials** - macOS package management *(coming soon)*

## 🚀 Installation Workflows

### New Machine Setup (macOS)
```bash
# 1. Install Homebrew (if not already installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. Install essential tools
brew install git
brew install --cask visual-studio-code  # or cursor

# 3. For Python development, see Python section
# 4. For Git workflows, see Git section

# 5. Install documentation tools
pip install mkdocs[material]
```

### Project Documentation Setup
```bash
# Initialize new documentation site
mkdocs new my-docs
cd my-docs

# Install with Material theme
python -m venv .venv
source .venv/bin/activate
pip install mkdocs-material

# Serve locally
mkdocs serve

# Deploy to GitHub Pages
mkdocs gh-deploy
```

## 🔗 Related Sections

- **[Python](../python/index.md)** - Python environment setup and tools
- **[Git](../git/index.md)** - Git workflows and GitHub CLI integration
- **[AI Tools](../ai-tools/index.md)** - VS Code extensions for AI development
- **[Databases](../databases/index.md)** - Database tools and SQL workflows

## ⚡ Quick References

**MkDocs Commands:**
```bash
mkdocs new project-name     # Create new site
mkdocs serve               # Local development server
mkdocs build              # Build static site  
mkdocs gh-deploy          # Deploy to GitHub Pages
```


**VS Code Essentials:**
```bash
code .                 # Open current directory
code file.py          # Open specific file
code --install-extension ms-python.python  # Install extension
```

## 📋 Tool Categories

### Core Development
- **Git** - Version control (system installed)
- **VS Code/Cursor** - Primary code editor  
- See **[Python](../python/index.md)** for Python-specific tools

### Database & Analysis  
- **sqlite3** - Command-line SQLite (system installed)
- See **[Databases](../databases/index.md)** for database tools and GUI applications

### Documentation & Deployment
- **MkDocs** - Static site generator

### Optional Enhancements
- **Homebrew** - macOS package manager
- **iTerm2** - Enhanced terminal (macOS)
- **Rectangle** - Window management (macOS)

## 🔧 Maintenance Tasks

**Monthly Updates:**
```bash
# Update Homebrew packages
brew update && brew upgrade

# Update VS Code extensions
code --list-extensions | xargs -L 1 code --install-extension

# For Python and Git tools, see respective sections
```

**Health Checks:**
```bash
# Check tool versions
git --version

# Test MkDocs
mkdocs --version
mkdocs serve --help

# For Python and Git specific checks, see respective sections
```

---

**Start here:** If you're setting up a new development machine, begin with [Python](../python/index.md) for Python environment setup, then [Git](../git/index.md) for version control workflows.