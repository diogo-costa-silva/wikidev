# Basic Workflow

> **Clone, status, add, commit, push, pull patterns that form the foundation of Git usage.**

These are the essential Git commands you'll use every day. Master these patterns and you'll be productive with Git in any project, whether working solo or with a team.

## 🎯 Goals

- **Core Git commands** for daily development
- **Safe workflows** that prevent data loss
- **Clear patterns** for common scenarios
- **Team collaboration** fundamentals

## 🔍 Quick Verification

Test that you can perform basic Git operations:

```bash
# Check Git is working
git --version
git status

# Verify your identity is set
git config user.name
git config user.email

# Test basic operations in a test directory
mkdir git-test && cd git-test
git init
echo "Hello Git" > test.txt
git add test.txt
git commit -m "Initial commit"
git log --oneline
cd .. && rm -rf git-test
```

## 🚀 Essential Commands

### Repository Initialization

#### Starting from Scratch
```bash
# Create new repository
mkdir my-project && cd my-project
git init

# Add initial files
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"

# Connect to remote repository
git remote add origin git@github.com:username/my-project.git
git branch -M main
git push -u origin main
```

#### Cloning Existing Repository
```bash
# Clone repository
git clone git@github.com:username/repository.git
cd repository

# Check status and recent history
git status
git log --oneline -n 5
```

### Daily Workflow Pattern

#### 1. Check Status
```bash
# Always start by checking what's changed
git status

# See what files have been modified
git diff

# See staged changes
git diff --staged
```

#### 2. Stage Changes
```bash
# Stage specific files
git add file1.py file2.js

# Stage all changes in current directory
git add .

# Stage all changes in repository
git add -A

# Stage parts of a file interactively
git add -p filename
```

#### 3. Commit Changes
```bash
# Commit staged changes
git commit -m "Add user authentication feature"

# Commit all tracked changes (skip staging)
git commit -am "Fix bug in data processing"

# Open editor for longer commit message
git commit
```

#### 4. Sync with Remote
```bash
# Push changes to remote
git push

# Push new branch to remote
git push -u origin feature-branch

# Pull latest changes
git pull

# Fetch changes without merging
git fetch
```

## 📝 Commit Message Patterns

### Good Commit Messages
```bash
# Use imperative mood (as if giving a command)
git commit -m "Add user login functionality"
git commit -m "Fix database connection timeout"
git commit -m "Update documentation for API endpoints"
git commit -m "Remove deprecated authentication method"

# For larger changes, add more context
git commit -m "Implement user dashboard

- Add dashboard view with user statistics
- Include charts for activity tracking  
- Add export functionality for user data

Closes #123"
```

### Commit Message Format
```
type: short description (50 chars or less)

Longer explanation if needed (wrap at 72 chars)
- Key changes or reasons
- Breaking changes
- Related issues

Closes #issue-number
```

## 🌿 Branch Workflow

### Basic Branching
```bash
# Create and switch to new branch
git checkout -b feature/user-auth

# Or using newer syntax
git switch -c feature/user-auth

# List branches
git branch

# Switch between branches
git checkout main
git switch feature/user-auth

# Delete branch (after merging)
git branch -d feature/user-auth
```

### Feature Branch Pattern
```bash
# Start new feature
git checkout main
git pull origin main
git checkout -b feature/user-dashboard

# Work on feature
echo "dashboard code" > dashboard.py
git add dashboard.py
git commit -m "Add user dashboard structure"

# More commits...
git commit -m "Implement dashboard charts"
git commit -m "Add dashboard tests"

# Push feature branch
git push -u origin feature/user-dashboard

# Later: merge back to main
git checkout main
git pull origin main
git merge feature/user-dashboard
git push origin main
git branch -d feature/user-dashboard
```

## 🔄 Syncing and Updates

### Keeping Up with Remote Changes
```bash
# Fetch latest changes without merging
git fetch origin

# Pull and merge changes
git pull origin main

# Pull with rebase (cleaner history)
git pull --rebase origin main

# See what changed remotely
git fetch
git log HEAD..origin/main --oneline
```

### Handling Diverged Branches
```bash
# When local and remote have diverged
git pull --rebase origin main

# If conflicts occur, resolve them then:
git add conflicted-file.py
git rebase --continue

# Or abort rebase if needed
git rebase --abort
```

## 📋 Common Scenarios

### Starting Work Day
```bash
# Update main branch
git checkout main
git pull origin main

# Create branch for today's work
git checkout -b feature/new-feature
```

### End of Work Day
```bash
# Stage and commit progress
git add .
git commit -m "WIP: implement user validation"

# Push to backup remotely
git push -u origin feature/new-feature
```

### Preparing for Team Review
```bash
# Make sure branch is up to date
git checkout main
git pull origin main
git checkout feature/new-feature
git rebase main

# Clean up commits if needed
git log --oneline -n 5
git rebase -i HEAD~3  # Interactive rebase last 3 commits

# Push clean branch
git push --force-with-lease origin feature/new-feature
```

## 🆘 Fixing Mistakes

### Undo Last Commit (Keep Changes)
```bash
git reset --soft HEAD~1
# Changes are now staged, ready to recommit
```

### Undo Last Commit (Discard Changes)
```bash
git reset --hard HEAD~1
# WARNING: This permanently deletes changes
```

### Unstage Files
```bash
# Unstage specific file
git restore --staged filename

# Unstage all files
git restore --staged .

# Old syntax (still works)
git reset HEAD filename
```

### Discard Working Changes
```bash
# Discard changes in specific file
git restore filename

# Discard all working changes
git restore .

# Old syntax (still works)
git checkout -- filename
```

### Fix Last Commit Message
```bash
git commit --amend -m "Corrected commit message"
```

## 📊 Viewing History

### Log Commands
```bash
# Basic log
git log --oneline

# Last 10 commits
git log --oneline -n 10

# Detailed log with files changed
git log --stat

# Visual branch history
git log --oneline --graph --all

# Search commits
git log --grep="fix bug"
git log --author="John Doe"
```

### Diff Commands
```bash
# See unstaged changes
git diff

# See staged changes
git diff --staged

# Compare with specific commit
git diff HEAD~2

# Compare branches
git diff main feature/new-feature
```

## 🔗 Related Sections

- **[Setup & Identity](setup-identity.md)** - Configuring Git and SSH keys
- **[Branching Model](branching-model.md)** - Feature branch strategy
- **[Pull Requests](pull-requests.md)** - Code review and collaboration
- **[Resolve Conflicts](resolve-conflicts.md)** - Handling merge conflicts

## ⚡ Quick Reference

**Daily Commands:**
```bash
git status              # Check what's changed
git add .               # Stage all changes
git commit -m "message" # Commit changes
git push                # Push to remote
git pull                # Pull from remote
```

**Branch Commands:**
```bash
git checkout -b branch-name  # Create and switch to branch
git checkout main           # Switch to main
git merge branch-name       # Merge branch
git branch -d branch-name   # Delete branch
```

**Undo Commands:**
```bash
git restore filename        # Discard working changes
git restore --staged file   # Unstage file
git reset --soft HEAD~1     # Undo commit, keep changes
git commit --amend          # Fix last commit
```

**Information Commands:**
```bash
git log --oneline          # See commit history
git diff                   # See unstaged changes
git diff --staged          # See staged changes
git branch                 # List branches
```

---

**Next:** Learn about [Setup & Identity](setup-identity.md) to configure Git properly, or explore [Branching Model](branching-model.md) for team collaboration workflows.