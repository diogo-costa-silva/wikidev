# Install & Setup

> **nvm setup and Node.js version management for consistent development environments.**

Node Version Manager (nvm) is essential for Node.js development. This guide sets up nvm and shows you how to manage multiple Node.js versions efficiently.

## 🎯 Goals

- **Node.js version management** with nvm
- **Consistent environments** across projects and team members
- **Easy switching** between Node.js versions
- **Project-specific version pinning** for reproducibility

## 🔍 Quick Verification

After following this guide, these commands should work:

```bash
# Check nvm installation
nvm --version

# Check available Node versions
nvm list

# Check current Node version
node --version
npm --version

# Test Node switching
nvm use 18
node --version
nvm use 20
node --version
```

## 🛠️ Installing nvm

### macOS and Linux
```bash
# Install nvm using curl
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Or using wget
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Restart terminal or reload shell configuration
source ~/.bashrc    # For bash
source ~/.zshrc     # For zsh

# Verify installation
nvm --version
```

### Windows
```powershell
# Install nvm-windows using chocolatey
choco install nvm

# Or download installer from GitHub
# https://github.com/coreybutler/nvm-windows/releases

# Restart command prompt/PowerShell
nvm version
```

### Manual Shell Configuration
If nvm doesn't work after installation, add this to your shell profile:

```bash
# Add to ~/.bashrc, ~/.zshrc, or ~/.profile
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

## 🚀 Node.js Version Management

### Installing Node.js Versions
```bash
# Install latest LTS (Long Term Support) version
nvm install --lts

# Install specific version
nvm install 18.17.0
nvm install 20.5.0

# Install latest version
nvm install node

# List available versions to install
nvm list-remote          # macOS/Linux
nvm list available       # Windows
```

### Using Different Node Versions
```bash
# Switch to specific version
nvm use 18.17.0
nvm use 20.5.0

# Use latest LTS
nvm use --lts

# Use latest version
nvm use node

# Check which version you're using
nvm current
node --version
```

### Setting Default Version
```bash
# Set default Node version for new shells
nvm alias default 18.17.0
nvm alias default node      # Use latest installed
nvm alias default --lts     # Use latest LTS

# Verify default
nvm alias default
```

## 📁 Project-Specific Node Versions

### .nvmrc File
Create a `.nvmrc` file in your project root to specify the Node.js version:

```bash
# Create .nvmrc file
echo "18.17.0" > .nvmrc

# Or for latest LTS
echo "lts/*" > .nvmrc

# Use the version specified in .nvmrc
nvm use

# Install version if not already installed
nvm install
```

### Automatic Version Switching
Add this to your shell profile for automatic switching:

```bash
# Add to ~/.bashrc or ~/.zshrc
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc
```

## 📦 npm Configuration

### Global Package Management
```bash
# View global packages
npm list -g --depth=0

# Install global packages per Node version
npm install -g yarn
npm install -g pnpm
npm install -g nodemon
npm install -g typescript

# View npm configuration
npm config list
npm config get prefix     # Where global packages are installed
```

### npm Configuration
```bash
# Set npm registry (if using private registry)
npm config set registry https://registry.npmjs.org/

# Set default author info
npm config set init-author-name "Your Name"
npm config set init-author-email "your.email@example.com"
npm config set init-license "MIT"

# Save exact versions by default
npm config set save-exact true
```

## 🔧 Recommended Node.js Versions

### Current Recommendations (2024)
```bash
# For new projects
nvm install 20.5.0        # Current LTS (recommended)
nvm use 20.5.0

# For legacy projects
nvm install 18.17.0       # Previous LTS (still supported)

# For cutting edge features
nvm install 21.0.0        # Latest features (use with caution)
```

### Version Strategy
- **Node 20.x**: Current LTS, recommended for new projects
- **Node 18.x**: Previous LTS, good for established projects
- **Node 16.x**: Older LTS, maintenance mode only
- **Odd versions** (19, 21): Current/experimental, avoid in production

## 🏗️ Project Setup Workflow

### Starting a New Project
```bash
# Create project directory
mkdir my-node-project && cd my-node-project

# Set Node version for project
echo "20.5.0" > .nvmrc
nvm use

# Initialize npm project
npm init -y

# Install dependencies
npm install express
npm install --save-dev nodemon typescript @types/node

# Verify setup
node --version
npm --version
```

### Joining an Existing Project
```bash
# Clone project
git clone https://github.com/user/project.git
cd project

# Use project's Node version
nvm use

# If Node version not installed
nvm install

# Install dependencies
npm install

# Verify everything works
npm run test  # or whatever command the project uses
```

## 🐛 Troubleshooting

### nvm command not found
```bash
# Check if nvm is in your PATH
echo $PATH | grep nvm

# Reload shell configuration
source ~/.bashrc  # or ~/.zshrc

# Reinstall nvm if needed
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
```

### Permission Issues with Global Packages
```bash
# Check npm prefix
npm config get prefix

# If it points to system directory, fix with:
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'

# Add to shell profile
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

### Node Version Not Persisting
```bash
# Set default version
nvm alias default 20.5.0

# Or add to shell profile
echo 'nvm use default' >> ~/.bashrc
```

### Slow nvm Performance
```bash
# Add to shell profile to lazy-load nvm
nvm() {
  unset -f nvm
  export NVM_DIR="$HOME/.nvm"
  [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
  nvm "$@"
}
```

## 🚀 Advanced Usage

### Managing Multiple Projects
```bash
# Project A uses Node 18
cd project-a
echo "18.17.0" > .nvmrc
nvm use

# Project B uses Node 20
cd ../project-b
echo "20.5.0" > .nvmrc
nvm use

# Quick project switching with aliases
nvm alias project-a 18.17.0
nvm alias project-b 20.5.0
nvm use project-a
```

### Updating Node Versions
```bash
# List installed versions
nvm list

# Install newer version
nvm install 20.6.0

# Migrate global packages from old version
nvm install 20.6.0 --reinstall-packages-from=20.5.0

# Update default alias
nvm alias default 20.6.0
```

### Cleanup Old Versions
```bash
# List installed versions
nvm list

# Uninstall specific version
nvm uninstall 18.16.0

# Clean up old versions (keep current, default, and LTS)
nvm cache clear
```

## 🔗 Related Sections

- **[Project Init](project-init.md)** - Setting up new Node.js projects
- **[NPM Scripts](npm-scripts.md)** - Automating tasks with package.json
- **[Environment Variables](environment-vars.md)** - Managing configuration
- **[Python Install & Setup](../python/install-setup.md)** - Similar concepts for Python

## ⚡ Quick Reference

**Installation:**
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
```

**Essential Commands:**
```bash
nvm install --lts          # Install latest LTS
nvm use 20.5.0             # Switch to version
nvm alias default 20.5.0   # Set default
echo "20.5.0" > .nvmrc     # Pin project version
nvm use                    # Use version from .nvmrc
```

**Project Setup:**
```bash
mkdir project && cd project
echo "20.5.0" > .nvmrc
nvm use
npm init -y
npm install dependencies
```

**Maintenance:**
```bash
nvm list                   # Show installed versions
nvm list-remote           # Show available versions
nvm uninstall 18.16.0     # Remove old version
nvm cache clear           # Clear nvm cache
```

---

**Next:** Learn about [Project Init](project-init.md) to set up new Node.js projects, or explore [NPM Scripts](npm-scripts.md) for task automation.