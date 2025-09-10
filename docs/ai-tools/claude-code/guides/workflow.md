# Development Workflows

← [**Voltar para Claude Code**](../index.md) | [**AI Tools**](../../index.md) | [**🏠 Home**](../../../index.md)

> **End-to-end development patterns with Claude Code**

This guide covers practical workflows for common development scenarios, from feature development to debugging and code review.

## 🏗️ Feature Development Workflow

### 1. Planning & Architecture

Start by discussing the feature at a high level:

```bash
# Get architectural guidance
claude "Design a user authentication system for a Flask app. Include database models, routes, and security considerations."

# Plan implementation steps
claude "Break down implementing JWT authentication into specific tasks and files to create."
```

### 2. Scaffolding & Structure

Generate the basic structure:

```bash
# Create directory structure
claude "Create the folder structure for authentication module" --output src/auth/

# Generate base files
claude "Create __init__.py, models.py, routes.py, and utils.py for auth module" --output src/auth/

# Update main app integration
claude "Show how to integrate this auth module with the main Flask app" --files app.py
```

### 3. Implementation

Implement core functionality file by file:

```bash
# Start with models
claude "Implement User model with SQLAlchemy, include password hashing" --files src/auth/models.py

# Add authentication logic
claude "Implement JWT token generation and validation functions" --files src/auth/utils.py

# Create API endpoints
claude "Create login, register, and profile routes" --files src/auth/routes.py --context src/auth/models.py src/auth/utils.py
```

### 4. Testing & Validation

Generate comprehensive tests:

```bash
# Create unit tests
claude "Generate unit tests for User model and auth utilities" --files src/auth/ --output tests/auth/

# Create integration tests
claude "Create API integration tests for authentication endpoints" --files src/auth/routes.py --output tests/test_auth_api.py

# Run and fix tests
pytest tests/auth/
claude "Fix failing tests" --files tests/auth/ src/auth/
```

### 5. Documentation & Commit

Document and commit your work:

```bash
# Generate documentation
claude "Create API documentation for authentication endpoints" --files src/auth/routes.py --output docs/auth-api.md

# Create commit with AI
git add .
claude git commit "Add JWT authentication system with user registration and login"
```

## 🐛 Debug Workflow

### 1. Error Analysis

When you encounter an error:

```bash
# Analyze error logs
claude "Analyze this error and suggest fixes" --files logs/error.log

# If logs are in terminal output
your-command 2>&1 | claude "What's causing this error and how do I fix it?"
```

### 2. Code Investigation

Investigate the problematic code:

```bash
# Analyze specific function
claude "Debug this function - what could cause the TypeError?" --files src/utils.py:45-67

# Check related code
claude "Review all places where User.create() is called" --files src/

# Trace data flow
claude "Trace how user_data flows through this function" --files src/auth/routes.py
```

### 3. Fix Generation

Apply fixes systematically:

```bash
# Generate fix
claude "Fix the identified issues in this function" --files src/problematic_file.py

# Add defensive programming
claude "Add error handling and input validation" --files src/auth/routes.py

# Create regression test
claude "Create test to prevent this bug recurring" --files src/problematic_file.py --output tests/
```

## 🔍 Code Review Workflow

### 1. Pre-commit Review

Before committing changes:

```bash
# Stage changes
git add .

# Get AI review of staged changes
claude "Review these changes for bugs, security issues, and best practices" --git-diff

# Apply suggestions
claude "Apply the review suggestions" --git-diff --files src/
```

### 2. Pull Request Review

When reviewing team members' PRs:

```bash
# Analyze specific commit
claude "Review this commit for potential issues" --git-commit abc123

# Review entire PR
claude "Analyze this PR diff and provide feedback" --git-diff main..feature-branch

# Check specific concerns
claude "Does this code follow our security guidelines?" --files src/auth/
```

### 3. Legacy Code Review

When working with existing code:

```bash
# Understand unfamiliar code
claude "Explain what this function does and how it works" --files src/legacy_module.py

# Identify improvement opportunities
claude "Suggest refactoring opportunities for better maintainability" --files src/

# Generate modernization plan
claude "How can we modernize this Python 2 code to Python 3.9+?" --files src/old_module.py
```

## 🚀 Refactoring Workflow

### 1. Analysis Phase

Assess the current state:

```bash
# Code quality analysis
claude "Analyze code quality and identify areas for improvement" --files src/

# Identify patterns
claude "Find duplicate code and suggest how to eliminate it" --files src/

# Performance review
claude "Identify performance bottlenecks in this code" --files src/api/
```

### 2. Planning Phase

Plan the refactoring:

```bash
# Create refactoring plan
claude "Create a step-by-step refactoring plan to improve this code" --files src/messy_module.py

# Assess risks
claude "What are the risks of refactoring this core module and how to mitigate them?" --files src/core.py
```

### 3. Execution Phase

Execute refactoring incrementally:

```bash
# Extract functions
claude "Extract these repeated code blocks into reusable functions" --files src/

# Improve naming
claude "Suggest better names for these variables and functions" --files src/unclear_names.py

# Simplify logic
claude "Simplify this complex conditional logic" --files src/complex_logic.py
```

### 4. Validation Phase

Ensure refactoring didn't break anything:

```bash
# Update tests
claude "Update tests to work with refactored code" --files tests/ src/

# Add missing tests
claude "Add tests for the newly extracted functions" --files src/refactored_module.py --output tests/

# Performance validation
claude "Compare the performance characteristics of old vs new implementation" --files src/old_backup.py src/new_implementation.py
```

## 📦 Release Preparation Workflow

### 1. Code Cleanup

Prepare code for release:

```bash
# Final code review
claude "Do a final review of all changes since last release" --git-diff v1.0.0..HEAD

# Update documentation
claude "Update all documentation to reflect recent changes" --files docs/ --context src/

# Version management
claude "Update version numbers and changelog" --files setup.py CHANGELOG.md
```

### 2. Testing & Quality Assurance

Ensure release quality:

```bash
# Test coverage analysis
claude "Identify areas lacking test coverage" --files src/ tests/

# Generate missing tests
claude "Create tests for uncovered code paths" --files src/uncovered_module.py --output tests/

# Integration testing
claude "Create end-to-end tests for the main user workflows" --files src/ --output tests/integration/
```

### 3. Release Documentation

Prepare release materials:

```bash
# Generate release notes
claude "Create release notes highlighting new features and bug fixes" --git-diff v1.0.0..HEAD

# Update README
claude "Update README with new features and installation instructions" --files README.md src/

# API documentation
claude "Generate comprehensive API documentation" --files src/api/ --output docs/api/
```

## 🔧 Configuration & Customization

### Project-Specific Workflows

Create `.claude.json` for your project:

```json
{
  "workflows": {
    "review": {
      "prompt": "Review for security, performance, and maintainability",
      "include": ["src/**/*.py"],
      "exclude": ["tests/", "migrations/"]
    },
    "test": {
      "prompt": "Generate comprehensive tests with edge cases",
      "context": ["requirements.txt", "conftest.py"]
    },
    "deploy": {
      "prompt": "Prepare code for production deployment",
      "include": ["src/", "config/", "requirements.txt"]
    }
  }
}
```

Use custom workflows:

```bash
# Use predefined workflow
claude review --files src/new_feature.py

# Chain workflows
claude review --files src/ && claude test --files src/ --output tests/
```

## 💡 Pro Tips

### Effective Context Management
- Use `--context` to include relevant files without editing them
- Create `.claude.json` to define default includes/excludes
- Use specific file paths instead of broad directories when possible

### Iterative Development
- Make small, focused changes and review each step
- Use git commits as checkpoints during AI-assisted development
- Keep a conversation log of what worked and what didn't

### Quality Assurance
- Always review AI-generated code before committing
- Run tests after each AI-assisted change
- Use AI to generate tests, but verify they test the right things

---

**Related:** [Prompting Patterns](prompting.md) • [MCP Configurations](../mcp/configurations.md) • [Git Integration](../../git/index.md)