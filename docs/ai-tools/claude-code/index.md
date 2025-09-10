# Claude Code

← [**Voltar para AI Tools**](../index.md) | [**🏠 Home**](../../index.md)

> **Command-line AI assistant for development workflows**

Claude Code is Anthropic's official CLI tool that brings Claude's capabilities directly to your terminal. It excels at file editing, code analysis, and terminal-based development tasks with deep understanding of codebases.

## 🎯 Why Claude Code?

- **Direct file manipulation** - Edit multiple files simultaneously
- **Codebase understanding** - Analyzes entire project contexts
- **Terminal integration** - Works seamlessly with existing workflows
- **Multi-step tasks** - Handles complex development workflows
- **Git integration** - Automated commit messages and PR creation

## 🔧 Installation & Setup

### Prerequisites
- macOS or Linux (Windows support via WSL)
- Node.js 18+ (for some features)
- Git configured with your identity

### Install Claude Code

```bash
# Install via npm (recommended)
npm install -g @anthropic-ai/claude-code

# Or via curl
curl -fsSL https://claude.ai/install.sh | sh

# Verify installation
claude --version
```

### Authentication Setup

```bash
# Launch setup wizard
claude auth

# Or set API key manually
export ANTHROPIC_API_KEY="your-api-key-here"
```

## 🔍 Quick Verification

Test your Claude Code installation:

```bash
# Check version and auth status
claude --version
claude auth status

# Test basic functionality
claude "What's in this directory?" --files .

# Test file analysis
claude "Analyze this Python file for potential improvements" --files *.py

# Test with specific file
echo "def hello(): print('world')" > test.py
claude "Add docstring and type hints to this function" --files test.py
rm test.py
```

## 🚀 Core Features

### File Analysis & Editing
```bash
# Analyze specific files
claude "Review this code for bugs" --files src/main.py

# Edit multiple files
claude "Add error handling to all functions" --files src/*.py

# Project-wide refactoring
claude "Rename variable 'data' to 'user_data' across the project" --files src/
```

### Code Generation
```bash
# Generate new files
claude "Create a FastAPI endpoint for user authentication" --output src/auth.py

# Generate tests
claude "Create unit tests for the User class" --files src/models.py --output tests/test_user.py

# Generate documentation
claude "Create README with setup instructions" --files package.json --output README.md
```

### Git Integration
```bash
# Automated commit messages
git add .
claude git commit

# Create pull request
claude git pr "Add user authentication feature"

# Analyze git diff
claude "Explain the changes in this diff" --git-diff
```

## 📋 Common Workflows

### Code Review Process
1. **Stage your changes**: `git add .`
2. **Get AI review**: `claude "Review these changes for issues" --git-diff`
3. **Apply suggestions**: `claude "Fix the issues you identified" --files src/`
4. **Commit with AI message**: `claude git commit`

### Bug Investigation
1. **Analyze error logs**: `claude "Analyze this error" --files logs/error.log`
2. **Review relevant code**: `claude "Find potential causes" --files src/problematic_module.py`
3. **Apply fixes**: `claude "Fix the identified issues" --files src/`
4. **Generate tests**: `claude "Create tests to prevent this bug" --output tests/`

### Feature Development
1. **Plan architecture**: `claude "Design a user authentication system"`
2. **Generate scaffolding**: `claude "Create the basic structure" --output src/auth/`
3. **Implement features**: `claude "Implement JWT authentication" --files src/auth/`
4. **Add tests**: `claude "Create comprehensive tests" --files src/auth/ --output tests/auth/`

## ⚙️ Advanced Configuration

### Project-Specific Setup
Create `.claude.json` in your project root:

```json
{
  "include": ["src/**/*.py", "tests/**/*.py", "*.md"],
  "exclude": ["venv/", "node_modules/", "*.pyc"],
  "instructions": "Follow PEP 8 style guide. Use type hints. Write docstrings for all functions.",
  "context_files": ["requirements.txt", "pyproject.toml"]
}
```

### Custom Prompts
```bash
# Save frequently used prompts
claude config set-prompt "code-review" "Analyze this code for performance, security, and maintainability issues. Provide specific suggestions."

# Use saved prompts
claude code-review --files src/main.py
```

## 🔗 Integration Guides

- **[Development Workflows](guides/workflow.md)** - End-to-end development patterns
- **[Prompting Patterns](guides/prompting.md)** - Effective prompt strategies
- **[MCP Configurations](mcp/configurations.md)** - Model Context Protocol setups
- **[CI/CD Integration](guides/cicd.md)** - Automated code review in pipelines

## 🐛 Troubleshooting

### Common Issues

**Authentication errors:**
```bash
# Clear and reset auth
claude auth logout
claude auth
```

**File permission issues:**
```bash
# Check file permissions
ls -la problematic-file.py

# Fix permissions if needed
chmod 644 problematic-file.py
```

**Context too large:**
```bash
# Use specific files instead of directories
claude "task" --files specific-file.py

# Use .claude.json to exclude large files
```

## 📚 Related Documentation

- **[Claude Desktop](../claude-desktop/index.md)** - Desktop app for planning and architecture
- **[Git Workflows](../../git/index.md)** - Git integration patterns
- **[Python Development](../../python/index.md)** - Python-specific workflows
- **[VS Code Integration](../../toolkit/vscode-cursor.md)** - Editor integration

## 🔐 Security Best Practices

- **Never share API keys** in prompts or commit them to repositories
- **Review generated code** before committing, especially security-related changes
- **Use local models** (Ollama) for proprietary or sensitive codebases
- **Exclude sensitive files** in `.claude.json` configuration
- **Audit AI-generated changes** before deploying to production

---

**Next:** Start with [Development Workflows](guides/workflow.md) to learn practical patterns, then explore [Prompting Patterns](guides/prompting.md) for more effective AI interactions.