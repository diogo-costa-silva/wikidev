# AI Tools

← [**🏠 Home**](../index.md)

> **Effective and safe use of AI tools to improve development and documentation workflows.**

AI tools are transforming software development. This section provides comprehensive guides for using AI assistants effectively and safely, with practical workflows and integration patterns.

## 🎯 Goals

- **Effective prompting** patterns for development tasks
- **Safe practices** to avoid security risks and maintain code quality
- **Workflow integration** with existing development tools
- **Tool-specific guidance** for each major AI platform

## 🔍 Quick Verification

Test your AI tool setup:

```bash
# Check Claude Code (if installed)
claude --version

# Check GitHub CLI for Copilot
gh --version
gh copilot --version

# Check if you have API access
# (Specific commands depend on tools you use)

# Verify environment variables for API keys
echo "API keys configured:" 
env | grep -E "(OPENAI|ANTHROPIC|GEMINI)" | cut -d= -f1
```

## 🤖 AI Tools Overview

Each tool has its strengths and ideal use cases. Choose the right tool for your specific needs:

### 📝 [Claude Code](claude-code/index.md)
**Command-line AI assistant for development workflows**  
- **Best for:** File editing, code analysis, terminal tasks
- **Strengths:** Deep codebase understanding, multi-file operations
- **Setup:** Desktop app with command-line integration

### 💬 [Claude Desktop](claude-desktop/index.md)
**Conversational AI for planning and problem-solving**  
- **Best for:** Architecture discussions, debugging complex issues
- **Strengths:** Long-form conversations, visual content analysis
- **Setup:** Desktop application with file uploads

### 🔧 [ChatGPT](chatgpt/index.md)
**Web-based AI assistant with extensive plugin ecosystem**  
- **Best for:** Quick questions, code explanations, learning
- **Strengths:** Broad knowledge, fast responses, plugins
- **Setup:** Web interface or API integration

### 🏠 [Ollama](ollama/index.md)
**Local AI models for privacy-sensitive work**  
- **Best for:** Offline development, sensitive codebases
- **Strengths:** Complete privacy, customizable models
- **Setup:** Local installation and model management

### 🌟 [Gemini CLI](gemini-cli/index.md)
**Google's AI assistant with strong coding capabilities**  
- **Best for:** Code generation, multi-language projects
- **Strengths:** Code understanding, integrated with Google services
- **Setup:** CLI tool with API authentication

## 🚀 Common AI Workflows

### Code Review & Analysis
1. **Use Claude Code** for automated file analysis
2. **Use Claude Desktop** for discussing architecture decisions
3. **Use local models** for sensitive code review

### Learning & Documentation
1. **Use ChatGPT** for quick concept explanations
2. **Use Claude Desktop** for detailed documentation planning
3. **Use AI tools** to generate code examples and tutorials

### Development Tasks
1. **Code generation** → Start with ChatGPT or Gemini for boilerplate
2. **Debugging** → Use Claude Code for file-level analysis
3. **Refactoring** → Use Claude Code for multi-file operations

## 🔐 Safety & Best Practices

### Code Security
- **Never share** production credentials, API keys, or sensitive data
- **Review all generated code** before committing
- **Use local models** (Ollama) for proprietary codebases
- **Set up proper .gitignore** to avoid accidentally committing secrets

### Quality Assurance
- **Test generated code** thoroughly before deployment
- **Verify external dependencies** and security implications
- **Maintain coding standards** - AI should follow your conventions
- **Document AI-generated sections** for future maintainers

### Prompt Engineering
- **Be specific** about requirements and constraints
- **Provide context** about your codebase and goals
- **Iterate prompts** to get better results
- **Save effective prompts** for reuse

## 🔗 Related Sections

- **[Python](../python/index.md)** - Using AI tools with Python development
- **[Git](../git/index.md)** - AI-assisted Git workflows and commit messages
- **[Toolkit](../toolkit/index.md)** - Integration with VS Code and other development tools

## ⚡ Quick References

**Effective Prompting Patterns:**

```text
# Code Analysis
"Analyze this [language] file and identify potential issues with performance, security, and maintainability."

# Code Generation  
"Create a [language] function that [specific requirement]. Follow [coding standard] and include error handling."

# Debugging
"I'm getting [error message]. Here's the relevant code: [code]. What could be causing this issue?"

# Refactoring
"Refactor this function to be more readable and maintainable while preserving the exact same functionality."
```

**Security Checklist:**
- [ ] No API keys or credentials in prompts
- [ ] No production database URLs or sensitive endpoints
- [ ] Review all generated code before committing  
- [ ] Use local models for proprietary code
- [ ] Check generated dependencies for security issues

**Tool Selection Guide:**
- **Quick questions** → ChatGPT
- **File editing** → Claude Code  
- **Architecture planning** → Claude Desktop
- **Sensitive code** → Ollama (local)
- **Code generation** → Gemini CLI or ChatGPT
- **Long conversations** → Claude Desktop

## 📁 Directory Structure

Each tool has its own directory with comprehensive documentation:

```
ai-tools/
├── index.md                 # This overview page
├── claude-code/
│   ├── index.md            # Claude Code introduction
│   ├── guides/             # Practical workflows
│   └── mcp/                # MCP configurations  
├── claude-desktop/
│   ├── index.md            # Claude Desktop setup
│   ├── guides/             # Usage patterns
│   └── mcp/                # MCP integrations
├── chatgpt/
│   └── index.md            # ChatGPT workflows
├── ollama/
│   ├── index.md            # Local model setup
│   └── models.md           # Model recommendations
└── gemini-cli/
    └── index.md            # Gemini CLI usage
```

---

**Start here:** If you're new to AI-assisted development, begin with [Claude Code](claude-code/index.md) for practical development workflows, then explore [Claude Desktop](claude-desktop/index.md) for planning and architecture discussions.