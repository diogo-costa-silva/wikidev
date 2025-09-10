# GitHub CLI (gh)

← [**Voltar para Git**](index.md) | [**🏠 Home**](../index.md)

The GitHub CLI brings GitHub to your terminal. Create issues, pull requests, manage repositories, and automate workflows without leaving the command line.

## Installation

### Install GitHub CLI
```bash
# macOS (Homebrew)
brew install gh

# Windows (Winget)
winget install GitHub.cli

# Linux (Debian/Ubuntu)
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh

# Linux (RPM)
sudo dnf install gh

# Manual installation
curl -L https://github.com/cli/cli/releases/latest/download/gh_*_linux_amd64.tar.gz | tar -xz
sudo cp gh_*_linux_amd64/bin/gh /usr/local/bin/
```

### Verify Installation
```bash
# Check version
gh --version

# Check authentication status
gh auth status
```

## Authentication & Setup

### Login to GitHub
```bash
# Interactive login (recommended)
gh auth login

# Login with token
gh auth login --with-token < ~/.github_token

# Login to GitHub Enterprise
gh auth login --hostname enterprise.company.com
```

### Configuration
```bash
# Set default editor
gh config set editor vim

# Set default browser  
gh config set browser firefox

# Configure git protocol
gh config set git_protocol ssh

# View all configuration
gh config list
```

## Repository Management

### Repository Operations
```bash
# Clone repository
gh repo clone owner/repo-name
gh repo clone owner/repo-name my-folder

# Create new repository
gh repo create my-new-repo
gh repo create my-new-repo --public
gh repo create my-new-repo --private --description "My project"

# Fork repository
gh repo fork owner/repo-name
gh repo fork owner/repo-name --clone

# View repository details
gh repo view
gh repo view owner/repo-name

# Archive repository
gh repo archive owner/repo-name

# Delete repository
gh repo delete owner/repo-name
```

### Repository Settings
```bash
# Set repository visibility
gh repo edit --visibility public
gh repo edit --visibility private

# Update repository description
gh repo edit --description "New description"

# Enable/disable features
gh repo edit --enable-issues
gh repo edit --disable-wiki
gh repo edit --enable-projects
```

## Issue Management

### Creating Issues
```bash
# Create issue interactively
gh issue create

# Create issue with title and body
gh issue create --title "Bug report" --body "Description of the bug"

# Create issue from template
gh issue create --template bug_report.md

# Create issue and assign
gh issue create --title "Feature request" --assignee username
```

### Managing Issues
```bash
# List issues
gh issue list
gh issue list --state open
gh issue list --assignee @me
gh issue list --label bug

# View issue details
gh issue view 123

# Edit issue
gh issue edit 123 --title "New title"
gh issue edit 123 --body "New description"
gh issue edit 123 --add-label bug
gh issue edit 123 --add-assignee username

# Close/reopen issue
gh issue close 123
gh issue reopen 123

# Comment on issue
gh issue comment 123 --body "This is my comment"
```

## Pull Request Workflow

### Creating Pull Requests
```bash
# Create PR from current branch
gh pr create

# Create PR with details
gh pr create --title "Add new feature" --body "Description of changes"

# Create PR to specific branch
gh pr create --base main --head feature-branch

# Create draft PR
gh pr create --draft

# Create PR and set reviewers
gh pr create --reviewer username1,username2
```

### Managing Pull Requests
```bash
# List PRs
gh pr list
gh pr list --state open
gh pr list --author @me
gh pr list --assignee username

# View PR details
gh pr view 123
gh pr view 123 --web  # Open in browser

# Edit PR
gh pr edit 123 --title "New title"
gh pr edit 123 --body "New description"
gh pr edit 123 --add-reviewer username

# Review PR
gh pr review 123 --approve
gh pr review 123 --request-changes --body "Please fix..."
gh pr review 123 --comment --body "Looks good!"

# Check PR status
gh pr status
gh pr checks 123

# Merge PR
gh pr merge 123
gh pr merge 123 --merge    # Create merge commit
gh pr merge 123 --squash   # Squash and merge
gh pr merge 123 --rebase   # Rebase and merge

# Close PR
gh pr close 123
```

### PR Checkout and Testing
```bash
# Checkout PR locally
gh pr checkout 123

# View PR diff
gh pr diff 123

# View PR changes
gh pr view 123 --json files --jq '.files[].filename'
```

## Workflow Automation

### GitHub Actions
```bash
# List workflows
gh workflow list

# View workflow runs
gh run list
gh run list --workflow=ci.yml

# View specific run
gh run view 1234567

# Watch workflow run
gh run watch

# Rerun workflow
gh run rerun 1234567
gh run rerun 1234567 --failed-jobs
```

### Releases
```bash
# List releases
gh release list

# View release
gh release view v1.0.0

# Create release
gh release create v1.0.0
gh release create v1.0.0 --title "Version 1.0.0" --notes "Release notes"

# Upload assets to release
gh release upload v1.0.0 dist/*.tar.gz

# Download release assets
gh release download v1.0.0
gh release download v1.0.0 --pattern "*.tar.gz"

# Delete release
gh release delete v1.0.0
```

## Advanced Features

### Multiple GitHub Accounts
```bash
# Login to different hosts
gh auth login --hostname github.com
gh auth login --hostname github.enterprise.com

# Switch between accounts
gh auth switch --hostname github.com
gh auth switch --hostname github.enterprise.com

# Set per-repository host
gh repo set-default --host github.enterprise.com
```

### SSH Key Management
```bash
# List SSH keys
gh ssh-key list

# Add SSH key
gh ssh-key add ~/.ssh/id_ed25519.pub --title "My key"

# Delete SSH key
gh ssh-key delete 12345
```

### Gist Management
```bash
# Create gist
gh gist create file.txt
gh gist create --public file1.txt file2.txt
echo "Hello world" | gh gist create --filename hello.txt

# List gists
gh gist list

# View gist
gh gist view abc123

# Edit gist
gh gist edit abc123

# Clone gist
gh gist clone abc123
```

## Development Workflows

### Feature Development Workflow
```bash
# 1. Create and switch to feature branch
git checkout -b feature/new-feature

# 2. Make changes and commit
git add .
git commit -m "Add new feature"

# 3. Push branch
git push origin feature/new-feature

# 4. Create PR
gh pr create --title "Add new feature" --body "Description of the feature"

# 5. Request review
gh pr edit --add-reviewer teammate1,teammate2

# 6. Check CI status
gh pr checks

# 7. Merge when approved
gh pr merge --squash
```

### Issue-Driven Development
```bash
# 1. Create issue
gh issue create --title "Implement user authentication"

# 2. Create branch from issue
gh issue develop 123 --checkout

# 3. Work on the issue
# ... make changes ...

# 4. Create PR that closes issue
gh pr create --title "Implement user authentication" --body "Closes #123"

# 5. Link PR to issue
gh pr edit --add-assignee @me
```

### Release Workflow
```bash
# 1. Create release branch
git checkout -b release/v1.2.0

# 2. Update version files
# ... update package.json, setup.py, etc ...

# 3. Create PR for release
gh pr create --title "Release v1.2.0" --base main

# 4. After PR merge, create release
gh release create v1.2.0 --generate-notes

# 5. Upload build artifacts
gh release upload v1.2.0 dist/*.zip
```

## Scripting and Automation

### JSON Output for Scripting
```bash
# Get PR information as JSON
gh pr list --json number,title,state,author

# Get specific fields
gh pr view 123 --json title,body,state

# Use with jq for processing
gh issue list --json number,title,labels | jq '.[] | select(.labels[].name == "bug")'
```

### Automation Scripts
```bash
#!/bin/bash
# auto-pr-review.sh - Automatically request reviews based on file changes

# Get list of changed files in PR
CHANGED_FILES=$(gh pr view --json files --jq '.files[].filename')

# Request review from team based on changed files
if echo "$CHANGED_FILES" | grep -q "frontend/"; then
    gh pr edit --add-reviewer frontend-team
fi

if echo "$CHANGED_FILES" | grep -q "backend/"; then
    gh pr edit --add-reviewer backend-team
fi

if echo "$CHANGED_FILES" | grep -q "docs/"; then
    gh pr edit --add-reviewer tech-writers
fi
```

### Bulk Operations
```bash
# Close all issues with specific label
gh issue list --label "wontfix" --json number --jq '.[].number' | \
  xargs -I {} gh issue close {}

# Add label to all open PRs
gh pr list --json number --jq '.[].number' | \
  xargs -I {} gh pr edit {} --add-label "needs-review"

# Get statistics
echo "Open issues: $(gh issue list --json number --jq 'length')"
echo "Open PRs: $(gh pr list --json number --jq 'length')"
echo "Total releases: $(gh release list --json name --jq 'length')"
```

## Extensions

### Installing Extensions
```bash
# List available extensions
gh extension list

# Install extension
gh extension install owner/gh-extension-name

# Popular extensions
gh extension install github/gh-copilot
gh extension install dlvhdr/gh-dash
gh extension install vilmibm/gh-screensaver
```

### Creating Custom Extensions
```bash
# Create new extension
gh extension create my-extension

# Extension structure
my-extension/
├── gh-my-extension      # Executable script
├── README.md
└── LICENSE

# Make executable
chmod +x gh-my-extension

# Install local extension
gh extension install .
```

## Configuration and Customization

### Aliases
```bash
# Create aliases
gh alias set pv 'pr view'
gh alias set co 'pr checkout'
gh alias set prc 'pr create --draft'

# List aliases
gh alias list

# Use alias
gh pv 123  # Same as 'gh pr view 123'
```

### Environment Variables
```bash
# Set default repository
export GH_REPO="owner/repo-name"

# Set pager
export GH_PAGER="less -R"

# Disable prompts (for scripts)
export GH_PROMPT_DISABLED=1

# Set host
export GH_HOST="github.enterprise.com"
```

## Integration with Git

### Enhanced Git Commands
```bash
# Enhanced git clone with additional info
gh repo clone owner/repo-name

# Automatically set upstream when pushing
git push origin feature-branch
gh pr create  # Creates PR for the pushed branch

# View repository in browser
gh repo view --web
gh pr view --web
gh issue view 123 --web
```

## CI/CD Integration

### GitHub Actions Workflow
```yaml
# .github/workflows/pr-management.yml
name: PR Management

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  auto-assign:
    runs-on: ubuntu-latest
    steps:
      - name: Auto-assign PR
        run: |
          gh pr edit ${{ github.event.number }} \
            --add-assignee ${{ github.event.pull_request.user.login }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Deployment Scripts
```bash
#!/bin/bash
# deploy.sh - Deploy after successful PR merge

# Check if we're on main branch
if [ "$(git branch --show-current)" != "main" ]; then
    echo "Not on main branch, exiting"
    exit 1
fi

# Get latest tag
LATEST_TAG=$(gh release list --limit 1 --json tagName --jq '.[0].tagName')

# Deploy the latest release
echo "Deploying $LATEST_TAG..."
gh release download "$LATEST_TAG" --pattern "*.tar.gz"
# ... deployment steps ...
```

## Troubleshooting

### Common Issues
```bash
# Check authentication
gh auth status
gh auth refresh

# Clear credentials and re-authenticate
gh auth logout
gh auth login

# Check API rate limits
gh api rate_limit

# Debug API calls
gh --debug api repos/:owner/:repo

# Update gh CLI
gh extension upgrade --all
# or
brew upgrade gh  # macOS
```

### Multiple SSH Keys
```bash
# List SSH keys for different hosts
ssh-add -l

# Test SSH connection
ssh -T git@github.com

# Configure SSH for different hosts
cat >> ~/.ssh/config << EOF
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_github

Host github.enterprise.com
    HostName github.enterprise.com
    User git
    IdentityFile ~/.ssh/id_ed25519_enterprise
EOF
```

## Best Practices

### Security
```bash
# Use fine-grained personal access tokens
gh auth login --scopes "repo,read:org"

# Don't store tokens in scripts
export GITHUB_TOKEN=$(gh auth token)

# Use environment-specific tokens
gh auth login --hostname github.enterprise.com
```

### Workflow Efficiency
```bash
# Set up useful aliases
gh alias set issues 'issue list --assignee @me'
gh alias set prs 'pr list --author @me'
gh alias set co 'pr checkout'

# Use templates for consistency
mkdir .github
cat > .github/PULL_REQUEST_TEMPLATE.md << EOF
## Description
Brief description of changes

## Testing
- [ ] Tests added/updated
- [ ] Manual testing completed

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
EOF
```

## Related Tools and Resources

**Official Documentation:**
- [GitHub CLI Manual](https://cli.github.com/manual/)
- [GitHub CLI Repository](https://github.com/cli/cli)
- [GitHub REST API](https://docs.github.com/en/rest)

**Related Tools:**
- **[Git](../git/)** - Version control fundamentals
- **[GitHub Actions](https://docs.github.com/en/actions)** - CI/CD workflows
- **[Hub](https://hub.github.com/)** - Alternative GitHub CLI (legacy)

**Integration Guides:**
- VS Code GitHub Pull Requests extension
- JetBrains GitHub integration
- Vim/Neovim GitHub plugins