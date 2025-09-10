# Prompting Patterns

← [**Voltar para Claude Code**](../index.md) | [**AI Tools**](../../index.md) | [**🏠 Home**](../../../index.md)

> **Effective prompt strategies for Claude Code development tasks**

Master these prompting patterns to get better results from Claude Code. Good prompts are specific, contextual, and clearly communicate your intent.

## 🎯 Core Principles

### 1. Be Specific
**Bad:** `"Fix this code"`  
**Good:** `"Fix the TypeError in the user_login function on line 45"`

### 2. Provide Context
**Bad:** `"Add error handling"`  
**Good:** `"Add error handling for database connection failures and invalid user input"`

### 3. Specify Constraints
**Bad:** `"Optimize this function"`  
**Good:** `"Optimize this function for memory usage while maintaining O(n) time complexity"`

### 4. Request Explanations When Needed
**Bad:** `"Write a decorator"`  
**Good:** `"Write a decorator that logs function execution time, explain how it works"`

## 🔧 Code Analysis Patterns

### Code Review
```bash
# Comprehensive review
claude "Analyze this Python file for:
1. Potential bugs and security vulnerabilities  
2. Performance issues
3. Code style violations (PEP 8)
4. Maintainability concerns
Provide specific line-by-line feedback." --files src/main.py

# Security-focused review
claude "Perform a security audit of this authentication module. Look for:
- SQL injection vulnerabilities
- Password handling issues  
- Session management problems
- Input validation gaps" --files src/auth/

# Performance review
claude "Identify performance bottlenecks in this code:
- Database query inefficiencies
- Memory leaks
- Algorithmic complexity issues
- I/O optimization opportunities" --files src/api.py
```

### Architecture Analysis
```bash
# Dependency analysis
claude "Analyze the dependencies in this module:
1. Identify circular imports
2. Suggest ways to reduce coupling
3. Recommend abstraction opportunities" --files src/services/

# Design pattern recognition
claude "Identify design patterns used in this codebase and suggest improvements:
- Which patterns are implemented well?
- Where could additional patterns help?
- Any anti-patterns to refactor?" --files src/

# Code organization review  
claude "Review the organization of this codebase:
- Is the folder structure logical?
- Are responsibilities properly separated?
- Any modules that should be split or merged?" --files src/
```

## 🏗️ Code Generation Patterns

### Function Generation
```bash
# Detailed function specification
claude "Create a Python function that:
- Takes a list of user dictionaries as input
- Validates each user has required fields (name, email, age)
- Filters users between ages 18-65
- Returns sorted list by name
- Includes comprehensive docstring and type hints
- Handles edge cases gracefully" --output src/user_utils.py

# API endpoint generation
claude "Create a FastAPI endpoint that:
- Accepts POST requests at /api/users/bulk-update
- Takes a list of user updates in JSON format
- Validates input using Pydantic models
- Updates users in database transactionally
- Returns summary of successful/failed updates
- Includes proper error handling and status codes" --files src/models.py --output src/api/users.py
```

### Class Generation
```bash
# Complete class with methods
claude "Create a Python class for managing file uploads:
- Accepts multiple file types (images, documents)
- Validates file size and type
- Generates unique filenames
- Stores files in organized directory structure
- Includes methods for upload, delete, and list operations
- Uses proper exception handling
- Add comprehensive docstrings and type hints" --output src/file_manager.py

# Database model generation
claude "Create SQLAlchemy models for a blog system:
- User model (id, username, email, created_at)
- Post model (id, title, content, author_id, published_at)
- Comment model (id, content, post_id, author_id, created_at)
- Include proper relationships and constraints
- Add __repr__ methods and validation" --files src/database.py --output src/models.py
```

### Test Generation
```bash
# Comprehensive test suite
claude "Create comprehensive pytest tests for this User class:
- Test all public methods
- Include edge cases and error conditions
- Test input validation
- Mock external dependencies (database, API calls)
- Use parametrized tests where appropriate
- Include setup/teardown fixtures" --files src/models/user.py --output tests/test_user.py

# Integration tests
claude "Create integration tests for the user authentication system:
- Test full registration flow
- Test login with valid/invalid credentials  
- Test password reset process
- Test JWT token validation
- Use real database in test mode
- Include cleanup between tests" --files src/auth/ --output tests/integration/test_auth.py
```

## 🐛 Debugging Patterns

### Error Investigation
```bash
# Error analysis with context
claude "Analyze this error and provide solutions:
Error: KeyError: 'user_id' in session handling
Context: This happens during user login after authentication
Files involved: auth.py, session.py, models.py
Recent changes: Updated session storage from cookies to database
Provide step-by-step debugging approach." --files src/auth.py src/session.py

# Stack trace analysis
claude "Analyze this stack trace and identify the root cause:
[PASTE FULL STACK TRACE HERE]
Also suggest:
1. Immediate fix for the error
2. Code changes to prevent similar issues
3. Additional logging for better debugging" --files src/
```

### Performance Debugging
```bash
# Performance bottleneck analysis
claude "This API endpoint is slow (>5 seconds). Analyze potential causes:
- Database query performance
- N+1 query problems
- Large data serialization
- External API calls
- Memory usage issues
Provide specific optimization suggestions." --files src/api/slow_endpoint.py src/models.py

# Memory leak investigation
claude "Investigate potential memory leaks in this long-running process:
- Identify variables that might not be garbage collected
- Check for circular references
- Review file handle management
- Analyze cache usage patterns
Suggest fixes and monitoring approaches." --files src/background_worker.py
```

## 🔄 Refactoring Patterns

### Code Improvement
```bash
# Comprehensive refactoring
claude "Refactor this legacy function to modern Python standards:
- Use type hints and dataclasses where appropriate
- Replace string formatting with f-strings
- Use context managers for resource handling
- Implement proper error handling
- Break down into smaller, focused functions
- Maintain exact same functionality" --files src/legacy_code.py

# Pattern implementation
claude "Refactor this code to use the Strategy pattern:
- Current code has multiple if/elif chains for different algorithms
- Each algorithm should be a separate class
- Create a context class to manage strategy selection
- Maintain backward compatibility
- Add easy way to register new strategies" --files src/algorithm_selector.py
```

### Architecture Refactoring
```bash
# Dependency injection
claude "Refactor this tightly coupled code to use dependency injection:
- Identify hard-coded dependencies
- Create interfaces/protocols for external services
- Implement container for dependency management
- Update all classes to accept dependencies via constructor
- Maintain current functionality while improving testability" --files src/services/

# Microservice extraction
claude "Analyze this monolith module and suggest microservice extraction:
- Identify logical service boundaries  
- Propose API contracts between services
- Plan data migration strategy
- Identify shared components
- Estimate complexity and risks of extraction" --files src/large_module.py
```

## 📝 Documentation Patterns

### API Documentation
```bash
# OpenAPI documentation
claude "Generate OpenAPI (Swagger) documentation for these API endpoints:
- Include all request/response schemas
- Add example requests and responses
- Document error codes and messages
- Include authentication requirements
- Add usage examples for each endpoint" --files src/api/

# Docstring generation
claude "Add comprehensive docstrings to all functions in this module:
- Use Google docstring format
- Include parameter and return type descriptions
- Add usage examples where helpful
- Document exceptions that may be raised
- Explain complex algorithms or business logic" --files src/utils.py
```

### User Documentation
```bash
# README generation
claude "Create a comprehensive README for this project:
- Project description and goals
- Installation instructions (multiple methods)
- Configuration options
- Usage examples
- API reference
- Contributing guidelines
- License information" --files package.json src/main.py

# Tutorial creation
claude "Create a step-by-step tutorial for using this library:
- Start with simple hello world example
- Progress to intermediate use cases
- Include common pitfalls and solutions
- Add troubleshooting section
- Use real-world examples throughout" --files src/ examples/
```

## ⚙️ Context Management Patterns

### Effective File Selection
```bash
# Focused context
claude "Refactor error handling" --files src/auth.py src/exceptions.py --context requirements.txt

# Broad exploration
claude "How does user authentication work in this app?" --files src/auth/ --context src/models.py src/config.py

# Change impact analysis
claude "What will break if I change this User model?" --files src/models/user.py --context src/ tests/
```

### Using Git Context
```bash
# Diff-based prompts
claude "Review these changes before I commit them" --git-diff

# Commit message generation
claude "Generate a conventional commit message for these changes" --git-diff

# Release preparation
claude "What should be included in release notes for these changes?" --git-diff v1.2.0..HEAD
```

## 🎨 Prompt Templates

### Save Common Patterns
```bash
# Set up reusable prompts
claude config set-prompt "security-review" "Perform a security audit focusing on:
1. Input validation and sanitization
2. Authentication and authorization
3. Data encryption and protection
4. SQL injection and XSS vulnerabilities
5. Dependency security issues
Provide specific remediation steps for each issue found."

claude config set-prompt "performance-audit" "Analyze performance characteristics:
1. Time complexity of algorithms
2. Memory usage patterns
3. Database query efficiency
4. I/O operations optimization
5. Caching opportunities
Provide specific optimization recommendations with expected impact."

# Use saved prompts
claude security-review --files src/api/
claude performance-audit --files src/core/
```

## ❌ Common Anti-Patterns to Avoid

### Vague Prompts
```bash
# Bad
claude "Make this better" --files src/code.py

# Good  
claude "Improve error handling and add input validation to this function" --files src/code.py
```

### No Context
```bash
# Bad
claude "Fix the bug"

# Good
claude "Fix the AttributeError on line 23 when user object is None" --files src/user_service.py
```

### Overwhelming Scope
```bash
# Bad
claude "Rewrite the entire application" --files src/

# Good
claude "Refactor the user authentication module to use JWT tokens" --files src/auth/
```

### Missing Constraints
```bash
# Bad
claude "Add a database" --files src/

# Good
claude "Add SQLite database with SQLAlchemy ORM, include migration support" --files src/
```

## 💡 Advanced Techniques

### Chain of Thought
```bash
claude "Debug this performance issue step by step:
1. First, analyze the code structure and identify potential bottlenecks
2. Then, examine database queries and their efficiency
3. Next, check for memory usage patterns
4. Finally, provide specific optimization recommendations
Walk through each step with your reasoning." --files src/slow_module.py
```

### Role-Based Prompting
```bash
claude "Act as a senior security engineer reviewing this authentication system. 
Focus on enterprise security standards and compliance requirements.
Identify vulnerabilities that could impact a financial services application." --files src/auth/
```

### Comparative Analysis
```bash
claude "Compare these two implementations of the same feature:
1. Analyze the trade-offs of each approach
2. Consider performance, maintainability, and readability
3. Recommend which to use and why
4. Suggest improvements for the chosen approach" --files src/impl_a.py src/impl_b.py
```

---

**Related:** [Development Workflows](workflow.md) • [MCP Configurations](../mcp/configurations.md) • [Claude Desktop](../../claude-desktop/index.md)