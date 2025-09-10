# Gemini CLI

Google's Gemini CLI provides command-line access to Gemini AI models. It enables developers to integrate Gemini's capabilities directly into scripts, workflows, and development processes.

## Installation

**Install via npm:**
```bash
npm install -g @google/gemini-cli
```

**Install via curl (official installer):**
```bash
curl -fsSL https://gemini.google.dev/install.sh | bash
```

**Verify Installation:**
```bash
gemini --version
```

## Setup & Authentication

**Login with Google Account:**
```bash
gemini auth login
```

**Set API Key (alternative):**
```bash
export GEMINI_API_KEY="your-api-key-here"
# Add to ~/.bashrc or ~/.zshrc for persistence
```

**Verify Authentication:**
```bash
gemini auth status
```

## Basic Usage

### Simple Prompts
```bash
# Basic text generation
gemini "Explain how HTTP works"

# Code generation
gemini "Write a Python function to sort a list"

# File analysis
gemini "Analyze this code" --file src/main.py
```

### Model Selection
```bash
# Use specific model
gemini "Hello world" --model gemini-pro

# Available models
gemini models list
```

### Output Formats
```bash
# JSON output
gemini "What is Python?" --format json

# Markdown output
gemini "Create a README" --format markdown
```

## Advanced Features

### Batch Processing
```bash
# Process multiple files
gemini "Review these files for bugs" --file *.py

# Batch prompts from file
gemini --prompts-file batch_requests.txt
```

### Context Management
```bash
# Maintain conversation context
gemini chat start
gemini chat send "What is React?"
gemini chat send "Show me a component example"
gemini chat end
```

### File Operations
```bash
# Analyze images
gemini "Describe this image" --image screenshot.png

# Process documents
gemini "Summarize this document" --file report.pdf

# Generate from templates
gemini generate --template api-docs --input openapi.yaml
```

## Development Workflow Integration

### Git Hooks
```bash
# Pre-commit code review
#!/bin/bash
# .git/hooks/pre-commit
gemini "Review this commit for issues" --git-diff
```

### CI/CD Integration
```yaml
# GitHub Actions example
- name: AI Code Review
  run: |
    gemini "Review PR changes" \
      --file $(git diff --name-only HEAD~1)
```

### Development Scripts
```bash
# Generate test cases
gemini "Generate pytest tests for" --file src/utils.py \
  --output tests/test_utils.py

# Create documentation
gemini "Generate API docs from" --file api/routes.py \
  --format markdown > docs/api.md
```

## Configuration

### Config File (~/.gemini/config.yaml)
```yaml
default_model: "gemini-pro"
output_format: "text"
max_tokens: 1024
temperature: 0.7
auth:
  method: "api_key"
  key_file: "~/.gemini/api_key"
```

### Environment Variables
```bash
export GEMINI_MODEL="gemini-pro"
export GEMINI_MAX_TOKENS="2048"
export GEMINI_TEMPERATURE="0.3"
export GEMINI_OUTPUT_FORMAT="markdown"
```

## Common Use Cases

### Code Review & Analysis
```bash
# Security analysis
gemini "Check for security vulnerabilities" --file app.py

# Performance review
gemini "Suggest performance improvements" --file slow_function.py

# Code style review
gemini "Review code style and suggest improvements" --file *.js
```

### Documentation Generation
```bash
# README generation
gemini "Create README from code structure" --directory src/

# API documentation
gemini "Generate API docs" --file server.py --format markdown

# Changelog creation
gemini "Generate changelog from git log" --git-log --since "1 week ago"
```

### Development Tasks
```bash
# Generate boilerplate
gemini "Create Express.js server with auth" --template express-auth

# Database queries
gemini "Write SQL query to find top customers" --schema schema.sql

# Error debugging
gemini "Explain this error and suggest fix" --error "$(cat error.log)"
```

## Model Options

### Gemini Pro
```bash
# Best for complex reasoning
gemini "Design system architecture" --model gemini-pro
```

### Gemini Pro Vision
```bash
# For image analysis
gemini "Analyze UI mockup" --image design.png --model gemini-pro-vision
```

### Gemini Flash (if available)
```bash
# For quick responses
gemini "Quick code snippet" --model gemini-flash
```

## Tips & Best Practices

### Effective Prompting
```bash
# Be specific about requirements
gemini "Write Python function that takes list of integers, 
        returns sorted list in descending order, 
        handles empty lists and None input"

# Provide context
gemini "Fix this React component bug" --file Component.jsx \
       --context "Error: Cannot read property 'map' of undefined"
```

### Performance Optimization
```bash
# Use appropriate model for task
gemini "Simple calculation" --model gemini-flash     # Fast
gemini "Complex analysis" --model gemini-pro        # Thorough

# Batch similar requests
gemini "Review these files" --file *.py --batch-size 5
```

### Output Management
```bash
# Save responses
gemini "Generate tests" --file app.py > generated_tests.py

# Pipe to other tools
gemini "Extract function names" --file *.js | grep "function"
```

## Pricing & Limits

### Free Tier
- Limited requests per minute
- Basic model access
- Standard response times

### Paid Tier
- Higher rate limits
- Premium model access
- Priority processing
- Extended context windows

## Integration with Other Tools

### With GitHub
```bash
# PR review automation
gh pr diff | gemini "Review this PR for issues"
```

### With Development Tools
```bash
# ESLint integration
eslint --format json src/ | gemini "Explain these linting errors"

# Test result analysis
pytest --tb=short | gemini "Analyze test failures"
```

## Troubleshooting

### Common Issues
```bash
# Authentication problems
gemini auth logout
gemini auth login

# Rate limit errors
gemini --rate-limit 1 "Your prompt here"

# Large file handling
gemini "Analyze code" --file large_file.py --chunk-size 1000
```

### Debug Mode
```bash
# Enable verbose logging
gemini --debug "Test prompt"

# Check API status
gemini status check
```

## Related Tools

- **[ChatGPT](../chatgpt/)** - OpenAI's conversational AI
- **[Claude Code](../claude-code/)** - Anthropic's development assistant
- **[Ollama](../ollama/)** - Local AI model hosting

## Resources

**Official:**
- [Gemini CLI Documentation](https://gemini.google.dev/cli)
- [Google AI Studio](https://makersuite.google.com/)
- [Gemini API Reference](https://ai.google.dev/api)

**Community:**
- [Gemini CLI Examples](https://github.com/google/gemini-cli-examples)
- [Gemini Prompt Library](https://ai.google.dev/examples)