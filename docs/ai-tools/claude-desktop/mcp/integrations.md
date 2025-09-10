# MCP Integrations

> **Extending Claude Desktop with Model Context Protocol**

Claude Desktop supports MCP (Model Context Protocol) integrations that provide access to external tools, databases, and services directly within conversations. This dramatically expands what you can accomplish without leaving the chat interface.

## 🔧 What MCP Enables

**Direct Data Access:**
- Query databases in real-time during conversations
- Browse and manipulate files on your system
- Connect to APIs and external services
- Execute code and see results inline

**Seamless Workflows:**
- Analyze database performance while discussing optimization
- Review git history while planning architecture changes
- Execute scripts and debug issues in real-time
- Generate reports from live data

## 📦 MCP Configuration

### Desktop MCP Setup

Claude Desktop looks for MCP configurations in:

**macOS:**
```
~/Library/Application Support/Claude/mcp_servers.json
```

**Windows:**
```
%APPDATA%\Claude\mcp_servers.json
```

**Linux:**
```
~/.config/claude/mcp_servers.json
```

### Basic Configuration Structure

```json
{
  "mcpServers": {
    "server-name": {
      "command": "command-to-run",
      "args": ["arg1", "arg2"],
      "env": {
        "ENV_VAR": "value"
      }
    }
  }
}
```

## 🗄️ Database Integrations

### MySQL Integration

**Configuration:**
```json
{
  "mcpServers": {
    "mysql": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/claude-mcp-mysql"],
      "env": {
        "MYSQL_HOST": "localhost",
        "MYSQL_PORT": "3306", 
        "MYSQL_USER": "your_username",
        "MYSQL_PASSWORD": "your_password",
        "MYSQL_DATABASE": "your_database"
      }
    }
  }
}
```

**Usage in Conversations:**
```
You: Can you analyze the performance of my user queries?

Claude: I'll analyze your MySQL database performance. Let me start by examining the current query patterns:

[MCP executes: SHOW PROCESSLIST and EXPLAIN for common queries]

Based on the analysis of your database:

**Current Performance Issues:**
1. **Slow Query on users table**: The login query is doing a full table scan
   - Missing index on email column
   - Query time: 2.3 seconds average

2. **High Connection Count**: 47/50 connections in use
   - Several long-running queries blocking others

**Recommended Optimizations:**
```sql
-- Add index for login queries
CREATE INDEX idx_users_email ON users(email);

-- Add composite index for user search
CREATE INDEX idx_users_search ON users(email, status, created_at);
```

Would you like me to analyze the impact of adding these indexes?
```

### PostgreSQL Integration

**Configuration:**
```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx", 
      "args": ["-y", "@anthropic-ai/claude-mcp-postgres"],
      "env": {
        "POSTGRES_URL": "postgresql://username:password@localhost:5432/dbname"
      }
    }
  }
}
```

**Advanced Analysis:**
```
You: My PostgreSQL database is running slowly. Can you help diagnose the issue?

Claude: I'll perform a comprehensive PostgreSQL performance analysis:

[Executes diagnostic queries through MCP]

**Database Health Summary:**
- **Size**: 15.3 GB with 2.1M users, 8.4M posts
- **Connections**: 23/100 active connections
- **Slow Queries**: 12 queries averaging >1 second

**Critical Issues Found:**

1. **Bloated Tables** (High Impact):
   ```sql
   Table: user_sessions (89% bloat, 2.1GB wasted space)
   Recommendation: VACUUM FULL user_sessions;
   ```

2. **Missing Indexes** (High Impact):
   ```sql
   -- Query: SELECT * FROM posts WHERE user_id = ? ORDER BY created_at DESC
   -- Execution time: 1,847ms
   -- Recommendation: Add composite index
   CREATE INDEX idx_posts_user_created ON posts(user_id, created_at DESC);
   ```

3. **Lock Contention** (Medium Impact):
   - Long-running transactions holding exclusive locks
   - Average lock wait time: 340ms

**Immediate Actions:**
1. Create the missing indexes (5 min downtime)
2. Schedule VACUUM FULL during maintenance window
3. Optimize the top 3 slowest queries

Should I show you the specific query optimizations and help create a maintenance plan?
```

### SQLite Integration

**Configuration:**
```json
{
  "mcpServers": {
    "sqlite": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/claude-mcp-sqlite", "/path/to/database.db"]
    }
  }
}
```

**Development Workflow:**
```
You: I'm building a local analytics tool with SQLite. Help me design the schema and test some queries.

Claude: I'd be happy to help design your SQLite analytics schema! Let me examine your current database structure:

[Analyzes existing tables through MCP]

**Current Schema Analysis:**
Your database has 3 tables but I see some opportunities for optimization:

**Proposed Analytics Schema:**
```sql
-- Events table (fact table)
CREATE TABLE events (
    id INTEGER PRIMARY KEY,
    user_id TEXT NOT NULL,
    event_type TEXT NOT NULL,
    timestamp INTEGER NOT NULL,  -- Unix timestamp for efficient queries
    properties JSON,             -- Flexible event data
    session_id TEXT
);

-- Users table (dimension table) 
CREATE TABLE users (
    id TEXT PRIMARY KEY,
    created_at INTEGER NOT NULL,
    properties JSON
);

-- Sessions table (for session analytics)
CREATE TABLE sessions (
    id TEXT PRIMARY KEY,
    user_id TEXT NOT NULL,
    started_at INTEGER NOT NULL,
    ended_at INTEGER,
    properties JSON,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**Optimized Indexes for Analytics:**
```sql
-- Time-based queries (most common in analytics)
CREATE INDEX idx_events_timestamp ON events(timestamp);
CREATE INDEX idx_events_type_time ON events(event_type, timestamp);

-- User behavior analysis
CREATE INDEX idx_events_user_time ON events(user_id, timestamp);
CREATE INDEX idx_sessions_user_started ON sessions(user_id, started_at);
```

Let me create these tables and test with some sample data:

[Executes schema creation through MCP]

Schema created successfully! Now let me insert some test data and show you common analytics queries:

[Inserts test data and demonstrates queries]

**Sample Analytics Queries:**
```sql
-- Daily active users
SELECT DATE(timestamp, 'unixepoch') as date, COUNT(DISTINCT user_id) as dau
FROM events 
WHERE timestamp >= strftime('%s', 'now', '-7 days')
GROUP BY date;

-- Event funnel analysis
SELECT event_type, COUNT(*) as count,
       COUNT(*) * 100.0 / SUM(COUNT(*)) OVER() as percentage
FROM events 
WHERE timestamp >= strftime('%s', 'now', '-1 day')
GROUP BY event_type;
```

The queries are running efficiently! Would you like me to help you implement more complex analytics like cohort analysis or session flow tracking?
```

## 📁 File System Integration

### File Operations MCP

**Configuration:**
```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/claude-mcp-filesystem"],
      "args": ["/Users/yourname/projects", "/Users/yourname/documents"]
    }
  }
}
```

**Code Analysis Workflow:**
```
You: Can you analyze my project structure and suggest improvements?

Claude: I'll analyze your project structure using the file system integration:

[Browses project files through MCP]

**Project Structure Analysis:**

**Current Structure:**
```
my-project/
├── src/
│   ├── components/     (47 files)
│   ├── utils/         (23 files)
│   ├── services/      (12 files)
│   └── styles/        (31 files)
├── tests/             (3 files - concerning!)
├── docs/              (1 file)
└── scripts/           (8 files)
```

**Issues Identified:**

1. **Test Coverage Gap**: Only 3 test files for 82 source files
   - Recommendation: Add tests for each major component
   - Target: At least 80% test coverage

2. **Component Organization**: 47 files in flat components directory
   - Recommendation: Group by feature or function
   ```
   components/
   ├── common/          (buttons, inputs, layouts)
   ├── user/           (profile, settings, auth)
   ├── dashboard/      (widgets, charts, analytics)
   └── admin/          (user management, system config)
   ```

3. **Missing Configuration Files**:
   - No ESLint configuration
   - No TypeScript configuration
   - No CI/CD setup

**Proposed Improvements:**
Let me create a better structure and move files appropriately:

[Executes file operations through MCP]

**Restructured Project:**
- Created feature-based component organization
- Added missing configuration files
- Set up proper test directory structure
- Added documentation templates

**Next Steps:**
1. Review the new structure
2. Update import paths in components
3. Add ESLint and Prettier configurations
4. Set up automated testing pipeline

Would you like me to help update the import statements throughout your codebase to match the new structure?
```

## 🐙 Git Integration

### Git History Analysis

**Configuration:**
```json
{
  "mcpServers": {
    "git": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/claude-mcp-git"],
      "cwd": "/path/to/your/repo"
    }
  }
}
```

**Repository Analysis:**
```
You: Can you analyze our team's commit patterns and suggest improvements to our Git workflow?

Claude: I'll analyze your Git repository to understand commit patterns and workflow effectiveness:

[Analyzes Git history through MCP]

**Repository Analytics (Last 90 Days):**

**Commit Patterns:**
- **Total Commits**: 347
- **Contributors**: 5 developers
- **Average commits/day**: 3.9
- **Peak activity**: Tuesdays and Wednesdays

**Code Quality Indicators:**
1. **Commit Message Analysis**:
   - 23% follow conventional commits format
   - 41% are single-word messages (concerning)
   - Most common: "fix", "update", "changes"

2. **File Change Patterns**:
   - `src/utils.js` modified in 67% of commits (potential hotspot)
   - Large commits (>50 files): 12 instances
   - Binary file commits: 23 (should use Git LFS)

**Branch Strategy Analysis:**
- **Main branch**: 89 direct commits (should be protected)
- **Feature branches**: Average lifespan 4.3 days
- **Merge strategy**: Mix of merge commits (67%) and rebases (33%)

**Problematic Patterns:**
1. **Hotspot Files** (high change frequency):
   ```
   src/utils.js        - 234 changes
   src/config.js       - 189 changes  
   package.json        - 156 changes
   ```
   
2. **Large Commits** (refactoring candidates):
   - `commit abc123`: 127 files changed
   - `commit def456`: 89 files changed
   
3. **Merge Conflicts** (last 30 days): 18 instances
   - Most conflicts in: `src/components/`, `package-lock.json`

**Recommendations:**

1. **Improve Commit Quality**:
   ```bash
   # Add commit message template
   git config commit.template .gitmessage
   
   # Template content:
   # <type>(<scope>): <subject>
   # 
   # <body>
   # 
   # <footer>
   ```

2. **Protect Main Branch**:
   - Require PR reviews
   - Block direct pushes
   - Require status checks

3. **Address Hotspots**:
   - Refactor `utils.js` into smaller modules
   - Extract configuration to separate files
   - Consider architectural improvements

4. **Standardize Workflow**:
   - Use squash merges for feature branches
   - Implement conventional commits
   - Add pre-commit hooks for quality checks

Would you like me to help implement any of these improvements, or analyze specific aspects of your codebase in more detail?
```

## ☁️ Cloud Service Integrations

### AWS Integration

**Configuration:**
```json
{
  "mcpServers": {
    "aws": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/claude-mcp-aws"],
      "env": {
        "AWS_ACCESS_KEY_ID": "your-access-key",
        "AWS_SECRET_ACCESS_KEY": "your-secret-key",
        "AWS_REGION": "us-east-1"
      }
    }
  }
}
```

**Infrastructure Management:**
```
You: Can you help me optimize my AWS costs? My bill has been increasing steadily.

Claude: I'll analyze your AWS infrastructure and identify cost optimization opportunities:

[Connects to AWS through MCP and analyzes resources]

**AWS Cost Analysis Summary:**

**Current Monthly Spending**: $2,847
**Trend**: +23% increase over last 3 months

**Top Cost Drivers:**
1. **EC2 Instances**: $1,245/month (44%)
   - 12 t3.large instances running 24/7
   - CPU utilization average: 23% (overprovisioned)

2. **RDS Databases**: $678/month (24%)
   - Production: db.r5.xlarge (necessary)
   - Staging: db.r5.large (can downsize)
   - Development: db.t3.medium (can shut down nights/weekends)

3. **S3 Storage**: $432/month (15%)
   - 15TB in Standard storage
   - 8TB hasn't been accessed in 90+ days (IA candidate)

4. **Data Transfer**: $289/month (10%)
   - High inter-AZ transfer costs
   - CloudFront might reduce costs

**Optimization Recommendations:**

**Immediate Actions** (Est. savings: $890/month):

1. **Right-size EC2 Instances**:
   ```bash
   # Current: 12 × t3.large ($67/month each)
   # Recommended: 8 × t3.medium + 4 × t3.small
   # Savings: $312/month
   ```

2. **Implement Auto Scheduling**:
   ```bash
   # Development/staging environments
   # Run only during business hours (9AM-6PM, Mon-Fri)
   # Savings: $245/month
   ```

3. **S3 Storage Optimization**:
   ```bash
   # Move infrequently accessed data to IA
   # 8TB × $0.0125/GB vs $0.023/GB
   # Savings: $102/month
   ```

4. **RDS Optimization**:
   - Downsize staging: db.r5.large → db.t3.large
   - Schedule dev environment: 40 hours/week vs 168
   - **Savings**: $231/month

**Medium-term Actions** (Est. savings: $450/month):

1. **Reserved Instances**: Purchase 1-year RIs for production
2. **CloudFront**: Reduce data transfer costs
3. **EBS Optimization**: Convert unused volumes to gp3

**Implementation Plan:**
Let me help you implement these changes safely:

1. **Week 1**: Resize non-production instances
2. **Week 2**: Implement auto-scheduling
3. **Week 3**: S3 lifecycle policies
4. **Week 4**: Purchase reserved instances

Would you like me to help create the specific scripts and policies for any of these optimizations?
```

## 🔧 Custom MCP Development

### Building Custom Integrations

**Simple File Processor MCP:**
```javascript
// custom-file-processor.js
const { spawn } = require('child_process');

class FileProcessorMCP {
  constructor() {
    this.tools = [
      {
        name: 'analyze_code_quality',
        description: 'Analyze code quality metrics for a file',
        handler: this.analyzeCodeQuality.bind(this)
      },
      {
        name: 'generate_docs',
        description: 'Generate documentation for code files',
        handler: this.generateDocs.bind(this)
      }
    ];
  }

  async analyzeCodeQuality(params) {
    const { filePath } = params;
    
    // Run ESLint on the file
    const eslintResult = await this.runESLint(filePath);
    
    // Calculate complexity metrics
    const complexity = await this.calculateComplexity(filePath);
    
    return {
      file: filePath,
      lintErrors: eslintResult.errors,
      complexity: complexity,
      recommendations: this.generateRecommendations(eslintResult, complexity)
    };
  }

  async generateDocs(params) {
    const { filePath } = params;
    
    // Extract function signatures and comments
    const functions = await this.extractFunctions(filePath);
    
    // Generate documentation
    const docs = functions.map(fn => ({
      name: fn.name,
      parameters: fn.parameters,
      description: fn.comment || 'No description available',
      examples: this.generateExamples(fn)
    }));
    
    return { documentation: docs };
  }
}

// Usage in Claude Desktop conversation
```

**Configuration:**
```json
{
  "mcpServers": {
    "custom-processor": {
      "command": "node",
      "args": ["./custom-file-processor.js"]
    }
  }
}
```

**Conversation Usage:**
```
You: Can you analyze the code quality of my main application file?

Claude: I'll use the custom file processor to analyze your code quality:

[Executes custom MCP tool]

**Code Quality Analysis Results:**

**File**: `src/main.js`

**ESLint Issues**:
- 3 errors, 7 warnings
- Most common: unused variables (4 instances)
- Security concerns: eval() usage detected

**Complexity Metrics**:
- Cyclomatic complexity: 23 (high)
- Lines of code: 847
- Function count: 34
- Average function length: 25 lines

**Recommendations**:
1. **Break down large functions**: 3 functions exceed 50 lines
2. **Remove unused code**: 4 unused variables detected
3. **Eliminate eval()**: Security risk on line 234
4. **Add error handling**: 12 functions lack try-catch blocks

**Priority Actions**:
1. Refactor the `processUserData` function (complexity: 15)
2. Remove eval() and use safer alternatives
3. Add JSDoc comments for public functions
4. Extract utility functions to separate modules

Would you like me to help implement these improvements or analyze another file?
```

## 🔐 Security Configuration

### Secure Credential Management

**Environment Variables:**
```json
{
  "mcpServers": {
    "secure-db": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/claude-mcp-postgres"],
      "env": {
        "POSTGRES_URL": "${DATABASE_URL}",
        "SSL_MODE": "require"
      }
    }
  }
}
```

**Credential Files:**
```bash
# Create secure credential storage
mkdir -p ~/.config/claude/credentials
chmod 700 ~/.config/claude/credentials

# Store database password
echo "your-secure-password" > ~/.config/claude/credentials/db-password
chmod 600 ~/.config/claude/credentials/db-password
```

Reference in configuration:
```json
{
  "mcpServers": {
    "secure-mysql": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/claude-mcp-mysql"],
      "env": {
        "MYSQL_PASSWORD_FILE": "/Users/yourname/.config/claude/credentials/db-password"
      }
    }
  }
}
```

### Access Control

**Restricted File Access:**
```json
{
  "mcpServers": {
    "restricted-fs": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/claude-mcp-filesystem"],
      "args": [
        "/Users/yourname/projects",
        "/Users/yourname/documents/work"
      ],
      "excludePatterns": [
        "*.key",
        "*.pem", 
        ".env*",
        "secrets/*"
      ]
    }
  }
}
```

## 🐛 Troubleshooting MCPs

### Common Issues

**MCP Server Not Starting:**
```bash
# Check if MCP package is installed
npm list -g @anthropic-ai/claude-mcp-mysql

# Test MCP directly
npx @anthropic-ai/claude-mcp-mysql
```

**Connection Issues:**
```bash
# Test database connection
mysql -h localhost -u username -p database_name

# Check environment variables
echo $MYSQL_PASSWORD
```

**Permission Problems:**
```bash
# Check file permissions
ls -la ~/.config/claude/mcp_servers.json

# Fix permissions
chmod 600 ~/.config/claude/mcp_servers.json
```

### Debug Mode

Enable verbose logging:
```json
{
  "mcpServers": {
    "debug-mysql": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/claude-mcp-mysql", "--debug"],
      "env": {
        "DEBUG": "mcp:*"
      }
    }
  }
}
```

## 📚 Related Documentation

- **[Conversation Patterns](../guides/conversations.md)** - Using MCPs in conversations
- **[Claude Code MCP](../../claude-code/mcp/configurations.md)** - Command-line MCP setup
- **[Database Workflows](../../databases/index.md)** - SQL development with MCPs
- **[Development Workflows](../../python/index.md)** - Python development integration

---

**Next:** Learn about [Conversation Patterns](../guides/conversations.md) to make the most of MCP-enhanced discussions, or explore specific MCP implementations for your development stack.