# Node.js

← [**🏠 Home**](../index.md)

> **Set up Node for small scripts, CLIs, and basic servers.**

Node.js excels at building lightweight scripts, command-line tools, and simple servers. This section covers the essential patterns for Node development without the complexity of large frameworks.

## 🎯 Goals

- **Clean setup** with `nvm` for version management
- **Simple project structure** that scales from scripts to small applications
- **Practical patterns** for CLI tools, environment variables, and basic servers
- **Modern JavaScript** with ES modules and current Node features

## 🔍 Quick Verification

Test your Node.js environment:

```bash
# Check Node version management
nvm --version
nvm list

# Check current Node and npm
node --version
npm --version

# Test basic functionality
node -e "console.log('Node.js is working!')"

# Check package creation
npm init -y
ls package.json && rm package.json
```

## 📚 Essential Guides

### Setup & Fundamentals
- **[Install & Setup](install-setup.md)** - `nvm` installation and Node version management
- **[Project Init](project-init.md)** - `npm init`, minimal project structure and configuration
- **[NPM Scripts](npm-scripts.md)** - Task automation with package.json scripts

### Development Patterns
- **[Environment Variables](environment-vars.md)** - Using `dotenv` for configuration management
- **[Simple Server](simple-server.md)** - Basic HTTP servers with native Node or minimal Express
- **[CLI Scripts](cli-scripts.md)** - Building command-line tools and utilities

### Advanced (Optional)
- **TypeScript Intro** - Adding types to Node projects *(coming soon)*
- **Testing** - Vitest or Jest for Node testing *(coming soon)*

## 🚀 Common Workflows

### New Script Project
1. **Create directory** → `mkdir my-script && cd my-script`
2. **Initialize package** → [Project Init](project-init.md)
3. **Set up environment** → [Environment Variables](environment-vars.md)
4. **Write and test** → Start coding your script
5. **Add npm scripts** → [NPM Scripts](npm-scripts.md)

### Typical Project Structure
```
my-node-project/
├── .env                # Environment variables (local)
├── .env.example        # Environment template
├── .gitignore          # Node-specific ignores
├── .nvmrc              # Node version for nvm
├── package.json        # Project configuration and scripts
├── package-lock.json   # Dependency lock file
├── README.md           # Project documentation
├── src/               # Source code
│   ├── index.js       # Main entry point
│   ├── lib/           # Reusable modules
│   └── bin/           # CLI executables
└── scripts/           # Build and utility scripts
```

## 🔗 Related Sections

- **[Python](../python/index.md)** - Compare with Python for scripting needs
- **[Git](../git/index.md)** - Version control for Node projects
- **[AI Tools](../ai-tools/index.md)** - Using AI tools with JavaScript/Node development
- **[Toolkit](../toolkit/index.md)** - VS Code setup and Node tooling

## ⚡ Quick References

**Project Initialization:**
```bash
# Quick project setup
mkdir my-project && cd my-project
echo "18" > .nvmrc  # Pin Node version
nvm use
npm init -y
npm install dotenv

# Basic gitignore
echo "node_modules/\n.env\n*.log" > .gitignore
```

**Package.json Scripts:**
```json
{
  "scripts": {
    "start": "node src/index.js",
    "dev": "node --watch src/index.js", 
    "test": "node --test",
    "build": "echo 'No build step needed'"
  }
}
```

**Environment Variables:**
```javascript
// Load environment variables
import 'dotenv/config';

// Use environment variables
const port = process.env.PORT || 3000;
const dbUrl = process.env.DATABASE_URL || 'sqlite://./dev.db';

console.log(`Server starting on port ${port}`);
```

**Simple HTTP Server:**
```javascript
import http from 'http';

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World!\n');
});

const port = process.env.PORT || 3000;
server.listen(port, () => {
  console.log(`Server running at http://localhost:${port}/`);
});
```

**CLI Script Template:**
```javascript
#!/usr/bin/env node

import { readFileSync } from 'fs';
import { fileURLToPath } from 'url';
import { dirname, join } from 'path';

// Get package.json for version info
const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
const packageJson = JSON.parse(
  readFileSync(join(__dirname, '../package.json'), 'utf8')
);

// Simple argument parsing
const args = process.argv.slice(2);
if (args.includes('--version') || args.includes('-v')) {
  console.log(packageJson.version);
  process.exit(0);
}

if (args.includes('--help') || args.includes('-h')) {
  console.log('Usage: my-cli [options]');
  console.log('  --version, -v    Show version');
  console.log('  --help, -h       Show help');
  process.exit(0);
}

// Main CLI logic
console.log('Hello from CLI!');
```

**Modern JavaScript Patterns:**
```javascript
// ES modules (use "type": "module" in package.json)
import fs from 'fs/promises';
import path from 'path';

// Async/await for file operations
try {
  const data = await fs.readFile('config.json', 'utf8');
  const config = JSON.parse(data);
  console.log('Config loaded:', config);
} catch (error) {
  console.error('Failed to load config:', error.message);
}

// Template literals for strings
const message = `
Hello ${name}!
Current time: ${new Date().toISOString()}
`;
```

---

**Start here:** If you're new to Node.js, begin with [Install & Setup](install-setup.md) to get `nvm` working, then follow [Project Init](project-init.md) for creating your first Node project.