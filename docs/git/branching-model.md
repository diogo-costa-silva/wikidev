# Branching Model

> **Feature branch strategy for solo work and team collaboration.**

A good branching strategy helps you organize work, collaborate effectively, and maintain a clean project history. This guide covers a simple but powerful branching model suitable for most projects.

## 🎯 Goals

- **Simple branching strategy** that scales from solo to team work
- **Clean main branch** that's always deployable
- **Feature isolation** for safe development
- **Easy collaboration** with clear merge patterns

## 🔍 Quick Verification

Test the branching workflow:

```bash
# Check current branch
git branch

# Create feature branch
git checkout -b feature/test-workflow
echo "test" > test.txt
git add test.txt
git commit -m "Add test file"

# Switch back to main
git checkout main
ls                           # test.txt should not be here

# Clean up
git branch -d feature/test-workflow
```

## 🌳 Branch Strategy Overview

### Main Branches
- **`main`** - Production-ready code, always stable
- **Feature branches** - Individual features or fixes
- **Optional: `develop`** - Integration branch for teams

### Branch Naming Convention
```bash
feature/user-authentication    # New features
bugfix/login-error            # Bug fixes  
hotfix/security-patch         # Urgent production fixes
chore/update-dependencies     # Maintenance tasks
docs/api-documentation        # Documentation updates
```

## 🚀 Solo Development Workflow

### Starting New Feature
```bash
# Ensure you're on main and up to date
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/add-user-profiles

# Work on your feature...
git add .
git commit -m "Add user profile model"
git commit -m "Add profile creation endpoint"
```

### Completing Feature
```bash
# Push feature branch
git push origin feature/add-user-profiles

# Switch to main and merge
git checkout main
git merge feature/add-user-profiles

# Push updated main
git push origin main

# Clean up feature branch
git branch -d feature/add-user-profiles
git push origin --delete feature/add-user-profiles
```

### Quick Feature Workflow
```bash
# One-liner to create and switch to feature branch
git checkout -b feature/quick-fix main

# Work and commit...
git add . && git commit -m "Fix validation bug"

# Merge back to main
git checkout main && git merge feature/quick-fix
git push origin main

# Delete feature branch
git branch -d feature/quick-fix
```

## 👥 Team Development Workflow

### Starting Team Feature
```bash
# Sync with remote main
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/shopping-cart

# Work and push regularly
git add .
git commit -m "Add cart model"
git push origin feature/shopping-cart

# Continue development...
git commit -m "Add cart API endpoints"
git push origin feature/shopping-cart
```

### Collaborating on Feature Branch
```bash
# Get someone else's feature branch
git checkout -b feature/shopping-cart origin/feature/shopping-cart

# Or sync existing feature branch
git checkout feature/shopping-cart
git pull origin feature/shopping-cart

# Work and push your changes
git add .
git commit -m "Add cart validation"
git push origin feature/shopping-cart
```

### Creating Pull Request
```bash
# Push latest changes
git push origin feature/shopping-cart

# Create pull request (using GitHub CLI)
gh pr create --title "Add shopping cart functionality" \
             --body "Implements cart model, API endpoints, and validation"

# Or create via GitHub web interface
# Then wait for review and approval
```

### Merging Pull Request
```bash
# After PR approval, merge via GitHub interface
# Then clean up locally:

git checkout main
git pull origin main              # Get merged changes
git branch -d feature/shopping-cart
git push origin --delete feature/shopping-cart
```

## 🔄 Merge Strategies

### Fast-Forward Merge (Simple)
```bash
# When no changes on main since branch creation
git checkout main
git merge feature/simple-fix     # Fast-forward merge
```

### Merge Commit (Preserve History)
```bash
# Creates merge commit even if fast-forward possible
git checkout main
git merge --no-ff feature/user-auth

# Result: Clear feature integration in history
```

### Rebase and Merge (Clean History)
```bash
# Rewrite feature commits on top of current main
git checkout feature/user-auth
git rebase main

# Then merge (will be fast-forward)
git checkout main
git merge feature/user-auth
```

## 🔧 Advanced Branch Operations

### Interactive Rebase (Clean History)
```bash
# Clean up commits before merging
git checkout feature/messy-commits
git rebase -i main

# Interactive editor opens:
# pick 1234567 Add initial feature
# squash 2345678 Fix typo
# squash 3456789 Another fix
# reword 4567890 Complete feature implementation
```

### Cherry-Pick Specific Commits
```bash
# Apply specific commit to current branch
git cherry-pick abc123

# Cherry-pick from another branch
git checkout main
git cherry-pick feature/hotfix~2   # Second-to-last commit
```

### Stash Work in Progress
```bash
# Save work in progress
git stash push -m "WIP: working on user validation"

# Switch branches and work...
git checkout feature/other-task

# Come back and restore work
git checkout feature/user-auth
git stash pop
```

## 🚨 Hotfix Workflow

### Emergency Production Fix
```bash
# Create hotfix from main (production)
git checkout main
git pull origin main
git checkout -b hotfix/security-patch

# Make minimal fix
git add .
git commit -m "hotfix: patch XSS vulnerability"

# Deploy immediately to production
git checkout main
git merge --no-ff hotfix/security-patch
git tag -a v1.2.1 -m "Security patch"
git push origin main --tags

# Clean up
git branch -d hotfix/security-patch
```

## 📊 Branch Management

### View Branch Status
```bash
# List all branches
git branch -a                    # Local and remote
git branch -r                    # Remote only
git branch -v                    # With last commit

# Show merged/unmerged branches
git branch --merged main         # Branches merged to main
git branch --no-merged main      # Branches not yet merged
```

### Clean Up Old Branches
```bash
# Delete merged local branches
git branch --merged main | grep -v "main\|develop" | xargs -n 1 git branch -d

# Delete tracking branches for deleted remotes
git remote prune origin

# Show branches with last activity
git for-each-ref --format='%(refname:short) %(committerdate)' refs/heads | sort -k2
```

### Branch Policies and Protection
```bash
# Using GitHub CLI to protect main branch
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":[]}' \
  --field enforce_admins=true \
  --field required_pull_request_reviews='{"required_approving_review_count":1}' \
  --field restrictions=null
```

## 🎯 Common Scenarios

### Working on Multiple Features
```bash
# Feature A
git checkout -b feature/payment-system
# Work on payment...

# Switch to Feature B
git stash push -m "WIP payment integration"
git checkout -b feature/notification-service main
# Work on notifications...

# Back to Feature A
git checkout feature/payment-system
git stash pop
```

### Updating Feature Branch with Main Changes
```bash
# Option 1: Merge main into feature (preserves history)
git checkout feature/long-running
git merge main

# Option 2: Rebase feature on main (clean history)
git checkout feature/long-running
git rebase main
```

### Handling Merge Conflicts
```bash
# Conflict occurs during merge
git merge feature/conflicting-changes

# Resolve conflicts in files, then:
git add resolved-file.py
git commit -m "Resolve merge conflicts"

# Or abort merge
git merge --abort
```

## 📋 Team Guidelines

### Branch Naming Rules
```bash
# Good branch names
feature/user-authentication
bugfix/header-responsive-design
hotfix/payment-gateway-timeout
chore/upgrade-python-dependencies

# Avoid
fix-stuff
temp-branch
johns-changes
```

### Commit Message Standards
```bash
# Good commits on feature branches
git commit -m "Add user registration endpoint"
git commit -m "Implement password validation rules"  
git commit -m "Add user profile tests"

# Before merging, consider squashing:
git commit -m "feat: implement user authentication system

- Add user registration and login endpoints
- Implement JWT token-based authentication  
- Add password validation and hashing
- Include comprehensive test coverage"
```

### Pull Request Guidelines
```bash
# Create descriptive PR
gh pr create --title "feat: add user authentication system" \
             --body "
## Summary
Implements complete user authentication with registration, login, and JWT tokens.

## Changes
- User model and database migrations
- Registration and login API endpoints  
- JWT token generation and validation
- Password hashing and validation rules
- Comprehensive test suite

## Testing
- All existing tests pass
- New tests cover authentication flows
- Manual testing completed for edge cases
"
```

## 🔗 Related Sections

- **[Basic Workflow](basic-workflow.md)** - Essential Git commands
- **[Setup & Identity](setup-identity.md)** - Git configuration
- **[Pull Requests](pull-requests.md)** - Collaboration workflows
- **[Merge vs Rebase](merge-vs-rebase.md)** - Choosing merge strategies

## ⚡ Quick Reference

**Feature Branch Workflow:**
```bash
git checkout main && git pull       # Start from updated main
git checkout -b feature/new-thing   # Create feature branch
# ... develop feature ...
git push origin feature/new-thing   # Push feature
# ... create PR, review, approve ...
git checkout main && git pull       # Switch back and sync
git branch -d feature/new-thing     # Clean up
```

**Branch Management:**
```bash
git branch                          # List local branches
git branch -r                       # List remote branches  
git checkout -b new-branch          # Create and switch
git branch -d branch-name           # Delete merged branch
git push origin --delete branch     # Delete remote branch
```

**Merge Strategies:**
```bash
git merge feature-branch            # Merge (fast-forward if possible)
git merge --no-ff feature-branch    # Always create merge commit
git rebase main                     # Rebase current branch on main
```

**Emergency Fixes:**
```bash
git checkout -b hotfix/urgent-fix main
# ... make minimal fix ...
git checkout main && git merge hotfix/urgent-fix
git tag v1.2.3 && git push --tags
```

---

**Next:** Learn about [Pull Requests](pull-requests.md) for team collaboration, or explore [Merge vs Rebase](merge-vs-rebase.md) to understand different integration strategies.