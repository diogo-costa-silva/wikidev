# Git

← [**🏠 Home**](../index.md)

> **Simple, robust workflows for solo work and team collaboration.**

Git is essential for any software project. This section covers practical Git workflows that actually work in real development scenarios, from simple solo projects to team collaboration.

## 🎯 Goals

- **Clean workflows** that prevent common Git problems
- **Collaboration patterns** for teams and open source
- **Conflict resolution** strategies that actually work  
- **Best practices** for commit messages, branching, and releases

## 🔍 Quick Verification

Test your Git setup:

```bash
# Check Git installation and version
git --version

# Verify your identity setup
git config --global user.name
git config --global user.email

# Check SSH setup for GitHub
ssh -T git@github.com

# Test basic Git functionality
git status
git log --oneline -n 5
```

## 📚 Essential Guides

### Setup & Fundamentals
- **[Setup & Identity](setup-identity.md)** - User configuration, SSH keys, and GitHub setup
- **[Basic Workflow](basic-workflow.md)** - clone, status, add, commit, push, pull patterns
- **[Branching Model](branching-model.md)** - main + feature branch strategy that scales

### Team Collaboration
- **[Pull Requests](pull-requests.md)** - Creating, reviewing, and merging PRs effectively
- **[Merge vs Rebase](merge-vs-rebase.md)** - When to use each approach
- **[Resolve Conflicts](resolve-conflicts.md)** - Step-by-step conflict resolution

### Advanced Tools & Practices
- **[GitHub CLI](github-cli.md)** - Command-line interface for GitHub operations
- **[.gitignore & Secrets](gitignore-secrets.md)** - What to ignore and how to handle secrets
- **[Commit Messages](commit-messages.md)** - Conventional commits and clear history
- **[Multiple GitHub Accounts](multiple-accounts.md)** - SSH and gh CLI for different accounts
- **[Tags & Releases](tags-releases.md)** - Versioning and release management

## 🚀 Common Workflows

### Daily Development
```bash
# Start work on new feature
git checkout main
git pull origin main
git checkout -b feature/new-feature

# Make changes and commit
git add .
git commit -m "feat: add new feature"

# Push and create PR
git push -u origin feature/new-feature
gh pr create --title "Add new feature" --body "Description here"  # Requires GitHub CLI
```

### Project Initialization
```bash
# New repository
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin git@github.com:username/repo.git
git push -u origin main

# Clone existing project
git clone git@github.com:username/repo.git
cd repo
git status
```

## 🔗 Related Sections

- **[Python](../python/index.md)** - Git workflows for Python projects
- **[Node.js](../node/index.md)** - Version control for Node applications
- **[AI Tools](../ai-tools/index.md)** - Using Claude Code with Git workflows
- **[Toolkit](../toolkit/index.md)** - VS Code, MkDocs, and development tools

## ⚡ Quick References

**Essential Commands:**
```bash
# Status and info
git status                    # Working directory status
git log --oneline -n 10      # Recent commits
git diff                     # Unstaged changes
git diff --staged            # Staged changes

# Basic workflow
git add file.txt             # Stage specific file
git add .                    # Stage all changes
git commit -m "message"      # Commit with message
git push                     # Push to remote
git pull                     # Pull from remote
```

**Branching:**
```bash
# Branch management
git branch                   # List branches
git branch feature-name      # Create branch
git checkout feature-name    # Switch to branch
git checkout -b feature-name # Create and switch

# Modern git switch/restore
git switch main              # Switch to main
git switch -c feature-name   # Create and switch
git restore file.txt         # Restore file
```

**Undo Operations:**
```bash
# Undo staged changes
git restore --staged file.txt

# Undo committed changes (safe)
git revert HEAD              # Revert last commit
git revert commit-hash       # Revert specific commit

# Reset (be careful!)
git reset --soft HEAD~1      # Undo last commit, keep changes
git reset --hard HEAD~1      # Undo last commit, lose changes
```

**Conflict Resolution:**
```bash
# During merge conflict
git status                   # See conflicted files
# Edit files to resolve conflicts
git add resolved-file.txt    # Mark as resolved
git commit                   # Complete merge

# Abort merge if needed
git merge --abort
```

**Remote Management:**
```bash
# Remote repositories
git remote -v                # List remotes
git remote add upstream url  # Add upstream remote
git fetch upstream           # Fetch from upstream
git merge upstream/main      # Merge upstream changes
```

**Useful Aliases:**
```bash
# Add to ~/.gitconfig or set with git config --global
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
git config --global alias.lg "log --oneline --graph --all"
```

**Commit Message Format:**
```
feat: add user authentication

- Implement login/logout functionality
- Add JWT token handling  
- Include password validation

Closes #123
```

---

**Start here:** If you're new to Git, begin with [Setup & Identity](setup-identity.md) to configure Git properly, then follow [Basic Workflow](basic-workflow.md) to learn the essential commands.