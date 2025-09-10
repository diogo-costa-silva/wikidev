# Setup & Identity

← [**Voltar para Git**](index.md) | [**🏠 Home**](../index.md)

> **User configuration, SSH keys, and GitHub setup for secure, authenticated Git workflows.**

Proper Git configuration is essential for a smooth development experience. This guide covers setting up your identity, SSH authentication, and GitHub integration.

## 🎯 Goals

- **Git identity configuration** for proper commit attribution
- **SSH key authentication** for secure, passwordless access
- **GitHub CLI integration** for streamlined workflows
- **Multiple account support** when needed

## 🔍 Quick Verification

After completing this setup, these commands should work:

```bash
# Verify Git identity
git config --global user.name
git config --global user.email

# Test SSH connection to GitHub
ssh -T git@github.com

# Verify GitHub CLI
gh auth status

# Test complete workflow
git clone git@github.com:username/test-repo.git
```

## 👤 Configure Git Identity

### Basic Identity Setup
```bash
# Set your name (used in commits)
git config --global user.name "Your Full Name"

# Set your email (should match GitHub account)
git config --global user.email "your.email@example.com"

# Verify configuration
git config --global --list
```

### Additional Global Settings
```bash
# Set default branch name for new repositories
git config --global init.defaultBranch main

# Set default editor (choose one)
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "vim"          # Vim

# Enable colored output
git config --global color.ui auto

# Set up better diff and merge tools
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

### View Configuration
```bash
# See all configuration
git config --list

# See configuration with file origins
git config --list --show-origin

# See specific setting
git config user.name
```

## 🔐 SSH Key Setup

### Generate SSH Key
```bash
# Generate new SSH key (use your GitHub email)
ssh-keygen -t ed25519 -C "your.email@example.com"

# When prompted:
# - Press Enter to accept default location (~/.ssh/id_ed25519)
# - Set a secure passphrase (recommended)

# Start SSH agent
eval "$(ssh-agent -s)"

# Add key to SSH agent
ssh-add ~/.ssh/id_ed25519
```

### Add SSH Key to GitHub
```bash
# Copy public key to clipboard
# macOS:
pbcopy < ~/.ssh/id_ed25519.pub

# Linux:
cat ~/.ssh/id_ed25519.pub
# Copy the output manually
```

**Then on GitHub:**
1. Go to GitHub.com → Settings → SSH and GPG keys
2. Click "New SSH key"
3. Paste your public key
4. Give it a descriptive title (e.g., "MacBook Pro - 2024")

### Test SSH Connection
```bash
# Test connection to GitHub
ssh -T git@github.com

# Should see message like:
# Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

### SSH Configuration File
Create `~/.ssh/config` for easier SSH management:

```bash
# Edit SSH config
nano ~/.ssh/config
```

Add this configuration:
```
# GitHub
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519
  IdentitiesOnly yes
```

## 🛠️ GitHub CLI Setup

### Install GitHub CLI
```bash
# macOS (Homebrew)
brew install gh

# Linux (Ubuntu/Debian)
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh
```

### Authenticate with GitHub
```bash
# Login to GitHub
gh auth login

# Follow prompts:
# - Choose GitHub.com
# - Choose SSH
# - Upload your SSH public key (if not already done)
# - Choose your preferred protocol for Git operations (SSH)
# - Authenticate in browser
```

### Verify GitHub CLI
```bash
# Check authentication status
gh auth status

# Test basic functionality
gh repo list
gh repo view username/repository
```

## 🏷️ Repository Configuration

### Initialize New Repository
```bash
# Create directory and initialize
mkdir my-new-project
cd my-new-project
git init

# Set local identity (if different from global)
git config user.name "Project Specific Name"
git config user.email "project@example.com"

# Create initial commit
echo "# My New Project" > README.md
git add README.md
git commit -m "Initial commit"
```

### Connect to Remote Repository
```bash
# Add remote origin
git remote add origin git@github.com:username/my-new-project.git

# Verify remote
git remote -v

# Push to remote
git branch -M main
git push -u origin main
```

### Create Repository on GitHub
```bash
# Create repository using GitHub CLI
gh repo create my-new-project --public --description "My awesome project"

# Or create private repository
gh repo create my-new-project --private --description "My private project"

# Create and clone in one step
gh repo create my-new-project --clone --public
```

## 🔧 Advanced Configuration

### Aliases for Common Commands
```bash
# Set up useful aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'

# Pretty log alias
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

### Line Ending Configuration
```bash
# Windows users
git config --global core.autocrlf true

# macOS/Linux users
git config --global core.autocrlf input

# Cross-platform projects
git config --global core.autocrlf false
```

### Ignore File Configuration
```bash
# Set global gitignore file
git config --global core.excludesfile ~/.gitignore_global

# Create global gitignore
echo ".DS_Store\nThumbs.db\n*.swp\n.vscode/\n.idea/" > ~/.gitignore_global
```

## 🐛 Troubleshooting

### Permission Denied (SSH)
```bash
# Check SSH agent is running
ssh-add -l

# Re-add key if needed
ssh-add ~/.ssh/id_ed25519

# Test SSH connection with verbose output
ssh -vT git@github.com
```

### Wrong User in Commits
```bash
# Check current configuration
git config user.name
git config user.email

# Fix for future commits
git config --global user.name "Correct Name"
git config --global user.email "correct@email.com"

# Fix last commit
git commit --amend --author="Correct Name <correct@email.com>"
```

### Remote Repository Issues
```bash
# Check remote configuration
git remote -v

# Change remote URL from HTTPS to SSH
git remote set-url origin git@github.com:username/repository.git

# Add remote if missing
git remote add origin git@github.com:username/repository.git
```

### SSH Key Not Working
```bash
# Generate new key with different algorithm
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# Or use legacy format if needed
ssh-keygen -m PEM -t rsa -b 4096 -C "your.email@example.com"
```

## 🔗 Related Sections

- **[Basic Workflow](basic-workflow.md)** - Daily Git commands and patterns
- **[Multiple GitHub Accounts](multiple-accounts.md)** - Managing multiple accounts
- **[Pull Requests](pull-requests.md)** - GitHub collaboration workflows
- **[GitHub CLI](github-cli.md)** - Advanced GitHub CLI usage

## ⚡ Quick Reference

**Initial Setup:**
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
ssh-keygen -t ed25519 -C "your.email@example.com"
ssh-add ~/.ssh/id_ed25519
# Add public key to GitHub
ssh -T git@github.com
gh auth login
```

**Repository Setup:**
```bash
git init
git remote add origin git@github.com:username/repo.git
git push -u origin main
```

**Check Configuration:**
```bash
git config --list              # All settings
git config user.name           # Specific setting
ssh -T git@github.com         # Test SSH
gh auth status                # Test GitHub CLI
```

---

**Next:** Learn the [Basic Workflow](basic-workflow.md) for daily Git usage, or explore [Multiple GitHub Accounts](multiple-accounts.md) if you need to manage multiple identities.