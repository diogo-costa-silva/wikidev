# Pull Requests

> **PR workflow and collaboration for effective code review and team development.**

Pull Requests (PRs) are the cornerstone of collaborative software development. They enable code review, knowledge sharing, and maintain code quality while allowing multiple developers to work together effectively.

## 🎯 Goals

- **Master PR workflow** from creation to merge
- **Write effective PRs** that get reviewed quickly
- **Conduct quality code reviews** that improve the codebase
- **Handle feedback and iterations** professionally

## 🔍 Quick Verification

Test the PR workflow:

```bash
# Create a feature branch
git checkout -b feature/test-pr

# Make a small change
echo "# Test PR" > test-pr.md
git add test-pr.md
git commit -m "Add test PR documentation"

# Push branch
git push origin feature/test-pr

# Create PR using GitHub CLI (if available)
gh pr create --title "Test PR workflow" --body "Testing the PR creation process"

# Check PR status
gh pr status
```

## 🚀 Creating Effective Pull Requests

### PR Preparation Checklist
```bash
# Before creating a PR, ensure:
# 1. Code is working and tested
git status                           # Clean working directory
npm test                            # All tests pass (if applicable)
npm run lint                        # Code follows style guidelines

# 2. Branch is up to date
git checkout main
git pull origin main
git checkout feature/my-feature
git merge main                      # Or rebase: git rebase main

# 3. Commits are clean
git log --oneline -10               # Review commit history
# Consider squashing if needed: git rebase -i HEAD~3

# 4. Code is pushed
git push origin feature/my-feature
```

### Creating PRs with GitHub CLI
```bash
# Basic PR creation
gh pr create --title "Add user authentication" \
             --body "Implements JWT-based authentication system"

# PR with template and labels
gh pr create --title "feat: add user profiles" \
             --body "$(cat .github/pull_request_template.md)" \
             --label "feature" \
             --label "frontend" \
             --assignee "@me"

# Draft PR (for work in progress)
gh pr create --draft --title "WIP: refactor database layer" \
             --body "Work in progress - not ready for review"

# PR to specific branch
gh pr create --base develop --head feature/my-feature \
             --title "Feature for develop branch"
```

### Writing PR Descriptions
```markdown
<!-- PR Template Example -->
## Summary
Brief description of what this PR accomplishes and why it's needed.

## Changes Made
- [ ] Add user authentication endpoints
- [ ] Implement JWT token generation
- [ ] Add middleware for protected routes
- [ ] Update user model with auth fields

## Type of Change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [x] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed
- [ ] Performance impact assessed

## Screenshots (if applicable)
<!-- Add screenshots of UI changes -->

## Checklist
- [x] Code follows project style guidelines
- [x] Self-review completed
- [x] Comments added for complex logic
- [x] Documentation updated
- [x] No console.log/print statements left
- [ ] Approved by at least one reviewer
```

## 🔍 Code Review Best Practices

### As a Reviewer

#### Review Process
```bash
# Get the PR locally for testing
gh pr checkout 123                  # Checkout PR #123
git log --oneline -5               # Review recent commits
npm install                        # Install dependencies if needed
npm test                          # Run tests locally

# Review files changed
gh pr diff 123                    # View diff
gh pr view 123                    # View PR details

# Check specific files
git show HEAD                     # Latest commit
git diff main...HEAD              # All changes from main
```

#### Quality Review Checklist
```markdown
## Code Quality Review Checklist

### Functionality
- [ ] Code does what the PR description says
- [ ] Edge cases are handled appropriately
- [ ] No obvious bugs or logic errors
- [ ] Performance considerations addressed

### Code Style & Standards
- [ ] Follows project coding conventions
- [ ] Naming is clear and consistent
- [ ] Functions/methods are appropriately sized
- [ ] No dead or commented-out code
- [ ] Error handling is appropriate

### Testing
- [ ] Tests cover new functionality
- [ ] Tests are meaningful and well-written
- [ ] Existing tests still pass
- [ ] Test data/mocks are realistic

### Security
- [ ] No hardcoded secrets or sensitive data
- [ ] Input validation where needed
- [ ] SQL injection/XSS prevention considered
- [ ] Authentication/authorization appropriate

### Documentation
- [ ] README updated if needed
- [ ] Comments explain complex logic
- [ ] API documentation updated
- [ ] Breaking changes documented
```

#### Providing Feedback
```bash
# Comment on specific lines
gh pr review 123 --comment -b "Great implementation! Consider extracting this logic into a separate function."

# Request changes
gh pr review 123 --request-changes -b "Please address the security concerns in auth.js"

# Approve PR
gh pr review 123 --approve -b "LGTM! Nice work on the error handling."

# Add suggestions
gh pr review 123 --comment -b "```suggestion
const userData = await fetchUser(id);
if (!userData) {
  throw new Error('User not found');
}
```"
```

### As a PR Author

#### Responding to Feedback
```bash
# Address review comments
git checkout feature/my-feature
# Make necessary changes
git add .
git commit -m "Address review feedback: improve error handling"
git push origin feature/my-feature

# Respond to comments
gh pr comment 123 --body "Thanks for the feedback! I've addressed the security concerns in the latest commit."

# Mark conversations as resolved (via web interface)
# Request re-review after changes
gh pr review 123 --request-changes
```

#### Handling Merge Conflicts
```bash
# When conflicts arise
git checkout feature/my-feature
git fetch origin
git merge origin/main               # Or rebase: git rebase origin/main

# Resolve conflicts in your editor
# After resolving conflicts:
git add .
git commit -m "Resolve merge conflicts with main"
git push origin feature/my-feature
```

## 🔄 PR Workflow Patterns

### Feature Development Workflow
```bash
# 1. Create feature branch from main
git checkout main
git pull origin main
git checkout -b feature/user-dashboard

# 2. Develop feature with small, focused commits
git add components/Dashboard.js
git commit -m "Add dashboard component structure"

git add api/dashboard.js
git commit -m "Add dashboard API endpoints"

git add tests/dashboard.test.js
git commit -m "Add dashboard component tests"

# 3. Push and create PR
git push origin feature/user-dashboard
gh pr create --title "feat: add user dashboard" \
             --body "Implements responsive user dashboard with real-time data"

# 4. Iterate based on feedback
# (make changes, commit, push)

# 5. Merge when approved
gh pr merge 123 --merge  # Or --squash or --rebase
```

### Hotfix Workflow
```bash
# 1. Create hotfix from main/master
git checkout main
git pull origin main
git checkout -b hotfix/security-patch

# 2. Make minimal fix
git add security/auth.js
git commit -m "hotfix: patch XSS vulnerability in auth"

# 3. Create urgent PR
gh pr create --title "HOTFIX: Security patch for auth" \
             --body "Critical security fix - please review ASAP" \
             --label "hotfix" \
             --label "security"

# 4. Fast-track review and merge
# 5. Deploy immediately
```

### Documentation Updates
```bash
# Simple documentation PRs
git checkout -b docs/api-examples
git add docs/api.md
git commit -m "docs: add API usage examples"
git push origin docs/api-examples

gh pr create --title "docs: improve API documentation" \
             --body "Adds clear examples and error handling guidance"
```

## 🛠️ Advanced PR Techniques

### PR Templates
Create `.github/pull_request_template.md`:
```markdown
## Description
Briefly describe what this PR does and why.

## Type of Change
- [ ] 🐛 Bug fix
- [ ] ✨ New feature
- [ ] 💥 Breaking change
- [ ] 📚 Documentation update
- [ ] 🎨 Style/formatting
- [ ] ♻️ Refactoring
- [ ] ⚡ Performance improvement
- [ ] ✅ Test update

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed
- [ ] Performance tested (if applicable)

## Screenshots
<!-- Add screenshots for UI changes -->

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentaion updated
- [ ] Tests added/updated
```

### Auto-linking Issues
```bash
# Link PR to issues in commit messages or PR description
git commit -m "fix: resolve login timeout issue

Closes #123
Fixes #456
Resolves #789"

# Or in PR description:
gh pr create --body "
Implements user authentication system.

Closes #123
Related to #456
"
```

### Draft PRs for Collaboration
```bash
# Create draft PR for early feedback
gh pr create --draft --title "WIP: redesign user interface" \
             --body "Early work on UI redesign. Feedback welcome on approach."

# Convert to ready when complete
gh pr ready 123

# Or mark as draft again
gh pr edit 123 --add-label "work-in-progress"
```

## 🔒 Security and Quality Gates

### Automated Checks
```yaml
# Example .github/workflows/pr-checks.yml
name: PR Checks
on:
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm ci
      - name: Run tests
        run: npm test
      - name: Run linter
        run: npm run lint
      - name: Check types
        run: npm run type-check
```

### Branch Protection Rules
```bash
# Configure branch protection via GitHub CLI
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":["test","lint"]}' \
  --field enforce_admins=true \
  --field required_pull_request_reviews='{"required_approving_review_count":1}' \
  --field dismiss_stale_reviews=true
```

## 📊 PR Analytics and Management

### Tracking PR Status
```bash
# View PR status
gh pr status

# List all PRs
gh pr list --state open
gh pr list --author "@me"
gh pr list --label "bug"

# PR details
gh pr view 123
gh pr checks 123                    # View CI status
gh pr diff 123                      # View changes
```

### PR Metrics
```bash
# Time tracking (example commands)
gh pr list --json createdAt,mergedAt,number | jq '
  .[] | 
  select(.mergedAt != null) |
  {
    pr: .number,
    days_open: ((now - (.createdAt | fromdateiso8601)) / 86400 | floor)
  }
'
```

## 💡 Team Collaboration Tips

### Code Review Culture
```markdown
## Effective Review Comments

### Good Examples:
✅ "Consider extracting this logic into a utility function for reusability"
✅ "This looks great! One small suggestion: we could use Array.includes() for better readability"
✅ "Security concern: this input should be validated before use"

### Avoid:
❌ "This is wrong" (not constructive)
❌ "Why did you do it this way?" (sounds confrontational)
❌ "Fix this" (no explanation)

### Better Alternatives:
✅ "This approach might cause issues with X. Consider Y instead because..."
✅ "Help me understand the reasoning behind this approach"
✅ "This could be improved by..."
```

### PR Size Guidelines
```bash
# Ideal PR sizes
echo "Small PRs (< 200 lines): ✅ Quick to review, easy to understand"
echo "Medium PRs (200-500 lines): ⚠️ Still manageable with good description"
echo "Large PRs (> 500 lines): ❌ Consider splitting into smaller PRs"

# Check PR size
gh pr diff 123 --name-only | wc -l  # Files changed
gh pr diff 123 | grep -E "^[+-]" | wc -l  # Lines changed
```

## 🔗 Related Sections

- **[Basic Workflow](basic-workflow.md)** - Essential Git commands for PRs
- **[Branching Model](branching-model.md)** - Branch strategies for PR workflows
- **[Setup & Identity](setup-identity.md)** - Git configuration for collaboration
- **[Multiple Accounts](multiple-accounts.md)** - Managing multiple GitHub accounts

## ⚡ Quick Reference

**Creating PRs:**
```bash
# Create PR with GitHub CLI
gh pr create --title "feat: add feature" --body "Description"

# Create draft PR
gh pr create --draft --title "WIP: feature"

# Create PR with labels
gh pr create --label "bug,frontend" --assignee "@me"
```

**Managing PRs:**
```bash
gh pr list                          # List PRs
gh pr view 123                      # View PR details
gh pr checkout 123                  # Checkout PR locally
gh pr diff 123                      # View PR changes
gh pr merge 123                     # Merge PR
```

**Code Review:**
```bash
gh pr review 123 --approve         # Approve PR
gh pr review 123 --request-changes # Request changes
gh pr review 123 --comment         # Add comment
```

**PR Status:**
```bash
gh pr status                       # Your PRs status
gh pr checks 123                   # CI status
gh pr ready 123                    # Mark draft as ready
```

**Best Practices:**
- Keep PRs small and focused
- Write clear descriptions
- Include tests for new features
- Respond to feedback promptly
- Use draft PRs for work in progress

---

**Next:** Learn about [Merge vs Rebase](merge-vs-rebase.md) for different integration strategies, or explore [Commit Messages](commit-messages.md) for effective commit practices.