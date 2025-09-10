# MCP Configurations

> **Model Context Protocol integrations for Claude Code**

Claude Code supports MCP (Model Context Protocol) integrations that extend its capabilities with specialized tools and data sources. This guide covers available MCPs and how to configure them.

## 🔧 What is MCP?

MCP (Model Context Protocol) allows Claude Code to integrate with external tools, databases, and services. MCPs provide:

- **Database access** - Query MySQL, PostgreSQL, SQLite directly
- **File system operations** - Advanced file manipulation capabilities  
- **API integrations** - Connect to REST APIs, GraphQL endpoints
- **Development tools** - Git operations, code analysis, testing frameworks
- **Cloud services** - AWS, Google Cloud, Azure integrations

## 📦 Available MCPs

### Database MCPs

#### MySQL MCP
```json
{
  "mcps": {
    "mysql": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-mysql"],
      "env": {
        "MYSQL_HOST": "localhost",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "your_user",
        "MYSQL_PASSWORD": "your_password",
        "MYSQL_DATABASE": "your_database"
      }
    }
  }
}
```

**Usage:**
```bash
# Query database through Claude Code
claude "Show me all users created in the last 7 days" --mcp mysql

# Analyze database schema
claude "Analyze the database schema and suggest optimizations" --mcp mysql

# Generate database migration
claude "Create a migration to add email verification to users table" --mcp mysql
```

#### PostgreSQL MCP
```json
{
  "mcps": {
    "postgres": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-postgres"],
      "env": {
        "POSTGRES_URL": "postgresql://user:pass@localhost:5432/dbname"
      }
    }
  }
}
```

#### SQLite MCP  
```json
{
  "mcps": {
    "sqlite": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-sqlite"],
      "args": ["path/to/database.db"]
    }
  }
}
```

### Development Tool MCPs

#### Git MCP
```json
{
  "mcps": {
    "git": {
      "command": "npx", 
      "args": ["@anthropic-ai/claude-mcp-git"],
      "cwd": "/path/to/your/repo"
    }
  }
}
```

**Usage:**
```bash
# Advanced git operations
claude "Create a feature branch and cherry-pick commits abc123 and def456" --mcp git

# Repository analysis  
claude "Analyze commit history for code quality trends" --mcp git

# Automated workflows
claude "Create a pre-commit hook that runs tests and linting" --mcp git
```

#### GitHub MCP
```json
{
  "mcps": {
    "github": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-github"],
      "env": {
        "GITHUB_TOKEN": "your_github_token"
      }
    }
  }
}
```

**Usage:**
```bash
# Repository management
claude "Create an issue for the bug we just found" --mcp github

# Pull request automation
claude "Review open PRs and provide feedback" --mcp github --files src/

# Project insights  
claude "Analyze repository activity and contributor metrics" --mcp github
```

### File System MCPs

#### Advanced File Operations MCP
```json
{
  "mcps": {
    "filesystem": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-filesystem"],
      "args": ["/allowed/path1", "/allowed/path2"]
    }
  }
}
```

**Usage:**
```bash
# Bulk file operations
claude "Rename all .js files to .ts and update imports" --mcp filesystem

# File organization
claude "Organize these files into a logical folder structure" --mcp filesystem

# Code migration
claude "Move all utility functions to a shared utils directory" --mcp filesystem
```

### Cloud Service MCPs

#### AWS MCP
```json
{
  "mcps": {
    "aws": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-aws"],
      "env": {
        "AWS_ACCESS_KEY_ID": "your_access_key",
        "AWS_SECRET_ACCESS_KEY": "your_secret_key", 
        "AWS_REGION": "us-east-1"
      }
    }
  }
}
```

**Usage:**
```bash
# Infrastructure management
claude "List all EC2 instances and their status" --mcp aws

# Deployment operations
claude "Deploy this application to Lambda" --files src/ --mcp aws

# Cost analysis
claude "Analyze AWS costs for the last month" --mcp aws
```

## ⚙️ Configuration Setup

### Global Configuration

Create `~/.config/claude/mcp.json`:

```json
{
  "mcps": {
    "mysql": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-mysql"],
      "env": {
        "MYSQL_HOST": "localhost",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "dev_user",
        "MYSQL_PASSWORD_FILE": "/path/to/password/file"
      }
    },
    "git": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-git"]
    }
  }
}
```

### Project-Specific Configuration

Create `.claude.json` in your project:

```json
{
  "mcps": {
    "project-db": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-sqlite"],
      "args": ["./data/app.db"]
    },
    "project-git": {
      "command": "npx", 
      "args": ["@anthropic-ai/claude-mcp-git"],
      "cwd": "."
    }
  },
  "default_mcps": ["project-db", "project-git"]
}
```

### Environment-Specific Setup

```bash
# Development environment
export CLAUDE_MCP_ENV=development

# Production environment  
export CLAUDE_MCP_ENV=production
```

Create environment-specific configs:

```json
// mcp.development.json
{
  "mcps": {
    "db": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-sqlite"],
      "args": ["./dev.db"]
    }
  }
}

// mcp.production.json  
{
  "mcps": {
    "db": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-postgres"],
      "env": {
        "POSTGRES_URL_FILE": "/secure/db-url"
      }
    }
  }
}
```

## 🚀 Common MCP Workflows

### Database Development
```bash
# Analyze database performance
claude "Find slow queries and suggest indexes" --mcp mysql --files src/models/

# Data migration
claude "Create migration to normalize user addresses" --mcp postgres

# Schema evolution
claude "Update database schema to match these models" --files src/models.py --mcp sqlite
```

### Repository Management
```bash
# Code quality analysis
claude "Analyze commit patterns and suggest workflow improvements" --mcp git

# Automated issue creation
claude "Create GitHub issues for all TODO comments in the codebase" --mcp github --files src/

# Release preparation
claude "Prepare changelog and tag release v2.1.0" --mcp git --mcp github
```

### File System Operations
```bash
# Project restructuring
claude "Reorganize this monorepo into microservices structure" --mcp filesystem --files .

# Bulk refactoring
claude "Update all import paths after moving modules" --mcp filesystem --files src/

# Asset optimization
claude "Optimize all images in assets directory" --mcp filesystem
```

## 🔒 Security Configuration

### Credential Management

Use environment files for sensitive data:

```bash
# .env file (never commit to git)
MYSQL_PASSWORD=your_secure_password
GITHUB_TOKEN=your_github_token
AWS_SECRET_ACCESS_KEY=your_aws_secret
```

Load in MCP config:
```json
{
  "mcps": {
    "mysql": {
      "env": {
        "MYSQL_PASSWORD": "${MYSQL_PASSWORD}"
      }
    }
  }
}
```

### Access Control

Limit MCP capabilities:
```json
{
  "mcps": {
    "restricted-fs": {
      "command": "npx",
      "args": ["@anthropic-ai/claude-mcp-filesystem"],
      "args": ["./src", "./tests"],
      "permissions": ["read", "write"],
      "exclude": ["*.secret", "*.key"]
    }
  }
}
```

### Audit Logging

Enable MCP operation logging:
```json
{
  "logging": {
    "level": "info",
    "mcp_operations": true,
    "output": "./logs/claude-mcp.log"
  }
}
```

## 🐛 Troubleshooting MCPs

### Common Issues

**MCP not found:**
```bash
# Check MCP installation
npm list @anthropic-ai/claude-mcp-mysql

# Install missing MCP
npm install -g @anthropic-ai/claude-mcp-mysql
```

**Connection errors:**
```bash
# Test MCP connection
claude --debug "List available tables" --mcp mysql

# Check environment variables
env | grep MYSQL
```

**Permission issues:**
```bash
# Check file permissions
ls -la ~/.config/claude/mcp.json

# Fix permissions
chmod 600 ~/.config/claude/mcp.json
```

### Debug Mode

Enable debug logging:
```bash
# Debug specific MCP
claude --debug --mcp-debug mysql "Show database status"

# Verbose logging
CLAUDE_LOG_LEVEL=debug claude "Query users table" --mcp mysql
```

## 📚 Custom MCP Development

### Creating a Custom MCP

Basic MCP structure:
```javascript
// custom-mcp.js
const { MCPServer } = require('@anthropic-ai/mcp-sdk');

const server = new MCPServer({
  name: 'custom-tool',
  version: '1.0.0'
});

server.addTool({
  name: 'custom-operation',
  description: 'Performs a custom operation',
  handler: async (params) => {
    // Your custom logic here
    return { result: 'success' };
  }
});

server.start();
```

Use custom MCP:
```json
{
  "mcps": {
    "custom": {
      "command": "node",
      "args": ["./path/to/custom-mcp.js"]
    }
  }
}
```

### MCP Development Best Practices

1. **Clear naming** - Use descriptive MCP and tool names
2. **Error handling** - Implement robust error handling
3. **Documentation** - Include usage examples and parameter descriptions
4. **Security** - Validate inputs and limit access appropriately
5. **Performance** - Optimize for common use cases

## 📖 Related Documentation

- **[Development Workflows](../guides/workflow.md)** - Using MCPs in development workflows
- **[Prompting Patterns](../guides/prompting.md)** - Effective prompts with MCP data
- **[Claude Desktop MCPs](../../claude-desktop/mcp/integrations.md)** - Desktop MCP integrations
- **[Database Tools](../../databases/index.md)** - SQL workflows with database MCPs

---

**Next:** Explore [Development Workflows](../guides/workflow.md) to see how MCPs enhance your development process, or check [Available MCP Registry](https://github.com/anthropics/claude-mcp-registry) for more integrations.