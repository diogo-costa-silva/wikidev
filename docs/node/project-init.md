# Project Init

> **npm init, package.json configuration, and Node.js project structure.**

Setting up a new Node.js project properly from the start saves time and ensures consistency. This guide covers project initialization, structure, and configuration for different types of Node.js applications.

## 🎯 Goals

- **Proper project initialization** with npm init
- **Understand package.json** configuration
- **Set up project structure** for different app types
- **Configure essential tools** and dependencies

## 🔍 Quick Verification

Test project initialization:

```bash
# Create and initialize a new project
mkdir my-node-app && cd my-node-app
npm init -y

# Verify package.json was created
cat package.json

# Install a dependency
npm install express

# Check node_modules and package-lock.json
ls -la
```

## 🚀 Project Initialization

### Interactive npm init
```bash
# Interactive setup (asks questions)
npm init

# Questions it asks:
# - package name: (my-node-app) 
# - version: (1.0.0) 
# - description: 
# - entry point: (index.js) 
# - test command: 
# - git repository: 
# - keywords: 
# - author: 
# - license: (ISC) 
```

### Quick Setup with Defaults
```bash
# Use defaults for everything
npm init -y

# Or set defaults first
npm config set init-author-name "Your Name"
npm config set init-author-email "your.email@example.com"
npm config set init-license "MIT"
npm config set init-version "0.1.0"

# Then npm init -y will use these defaults
npm init -y
```

### Custom Project Templates
```bash
# Using create- packages for quick setup
npx create-express-app my-api
npx create-react-app my-frontend
npx create-next-app my-next-app

# Or use npm init with package name
npm init express-generator-typescript my-ts-api
npm init svelte@next my-svelte-app
```

## 📦 Understanding package.json

### Basic package.json Structure
```json
{
  "name": "my-node-app",
  "version": "1.0.0",
  "description": "A sample Node.js application",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "jest",
    "lint": "eslint . --ext .js,.ts",
    "build": "tsc"
  },
  "keywords": ["node", "express", "api"],
  "author": "Your Name <your.email@example.com>",
  "license": "MIT",
  "dependencies": {
    "express": "^4.18.2",
    "dotenv": "^16.3.1"
  },
  "devDependencies": {
    "nodemon": "^3.0.1",
    "jest": "^29.6.2",
    "eslint": "^8.44.0"
  }
}
```

### Essential Fields Explained
```json
{
  "name": "my-app",                    // Package name (lowercase, no spaces)
  "version": "1.0.0",                 // Semantic versioning
  "description": "App description",    // Brief description
  "main": "index.js",                 // Entry point
  "type": "module",                   // Use ES modules (optional)
  "engines": {                        // Node.js version requirements
    "node": ">=18.0.0",
    "npm": ">=8.0.0"
  },
  "repository": {                     // Git repository
    "type": "git",
    "url": "https://github.com/user/repo.git"
  },
  "bugs": {                          // Issue tracker
    "url": "https://github.com/user/repo/issues"
  },
  "homepage": "https://github.com/user/repo#readme"
}
```

### Script Configuration
```json
{
  "scripts": {
    // Development
    "dev": "nodemon index.js",
    "dev:debug": "nodemon --inspect index.js",
    "dev:watch": "nodemon --watch src index.js",
    
    // Production
    "start": "node index.js",
    "start:prod": "NODE_ENV=production node index.js",
    
    // Build (for TypeScript projects)
    "build": "tsc",
    "build:watch": "tsc --watch",
    "prebuild": "rm -rf dist",
    
    // Testing
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    
    // Linting and formatting
    "lint": "eslint . --ext .js,.ts",
    "lint:fix": "eslint . --ext .js,.ts --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    
    // Database (if applicable)
    "db:migrate": "knex migrate:latest",
    "db:seed": "knex seed:run",
    
    // Utilities
    "clean": "rm -rf node_modules package-lock.json",
    "reinstall": "npm run clean && npm install"
  }
}
```

## 📁 Project Structure Templates

### Basic Express API Structure
```bash
mkdir my-express-api && cd my-express-api

# Create directory structure
mkdir -p {src/{controllers,middleware,models,routes,utils,config},tests,docs,public,logs}

# Create initial files
touch {src/index.js,src/app.js,.env,.env.example,.gitignore,README.md}

# Directory structure:
# my-express-api/
# ├── src/
# │   ├── controllers/    # Route handlers
# │   ├── middleware/     # Custom middleware
# │   ├── models/         # Database models
# │   ├── routes/         # Route definitions
# │   ├── utils/          # Utility functions
# │   ├── config/         # Configuration files
# │   ├── app.js          # Express app setup
# │   └── index.js        # Server entry point
# ├── tests/              # Test files
# ├── docs/               # Documentation
# ├── public/             # Static files
# ├── logs/               # Log files
# ├── .env                # Environment variables
# ├── .env.example        # Environment template
# ├── .gitignore          # Git ignore rules
# ├── package.json        # Dependencies and scripts
# └── README.md           # Project documentation
```

### CLI Tool Structure
```bash
mkdir my-cli-tool && cd my-cli-tool

# Create structure for CLI tool
mkdir -p {src/{commands,utils,config},bin,tests,docs}
touch {src/index.js,bin/cli.js,.gitignore,README.md}

# CLI tool structure:
# my-cli-tool/
# ├── bin/
# │   └── cli.js          # Executable entry point
# ├── src/
# │   ├── commands/       # CLI commands
# │   ├── utils/          # Helper functions
# │   ├── config/         # Configuration
# │   └── index.js        # Main logic
# ├── tests/              # Tests
# ├── docs/               # Documentation
# ├── package.json
# └── README.md

# Configure package.json for CLI
cat > package.json << 'EOF'
{
  "name": "my-cli-tool",
  "version": "1.0.0",
  "description": "A useful CLI tool",
  "main": "src/index.js",
  "bin": {
    "my-tool": "bin/cli.js"
  },
  "scripts": {
    "start": "node bin/cli.js",
    "dev": "nodemon bin/cli.js",
    "test": "jest"
  },
  "keywords": ["cli", "tool"],
  "author": "Your Name",
  "license": "MIT"
}
EOF
```

### Library/Package Structure
```bash
mkdir my-library && cd my-library

# Create library structure
mkdir -p {src,lib,tests,examples,docs}
touch {src/index.js,tests/index.test.js,.gitignore,README.md}

# Library structure:
# my-library/
# ├── src/                # Source code (if using build step)
# ├── lib/                # Built/compiled code
# ├── tests/              # Test files
# ├── examples/           # Usage examples
# ├── docs/               # Documentation
# ├── package.json
# └── README.md

# Configure package.json for library
cat > package.json << 'EOF'
{
  "name": "my-library",
  "version": "1.0.0",
  "description": "A useful JavaScript library",
  "main": "lib/index.js",
  "module": "src/index.js",
  "types": "lib/index.d.ts",
  "files": ["lib", "src", "README.md"],
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch",
    "test": "jest",
    "prepublishOnly": "npm run build"
  },
  "keywords": ["library", "utility"],
  "author": "Your Name",
  "license": "MIT"
}
EOF
```

## 🔧 Essential Configuration Files

### .gitignore for Node.js
```bash
cat > .gitignore << 'EOF'
# Dependencies
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Runtime data
pids
*.pid
*.seed
*.pid.lock

# Coverage directory used by tools like istanbul
coverage/
.nyc_output

# Build outputs
dist/
build/
lib/

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# IDE files
.vscode/
.idea/
*.swp
*.swo
*~

# OS files
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# Logs
logs
*.log

# Temporary files
tmp/
temp/

# Optional npm cache directory
.npm

# Optional REPL history
.node_repl_history

# Output of 'npm pack'
*.tgz

# Yarn Integrity file
.yarn-integrity

# dotenv environment variables file
.env
EOF
```

### Environment Variables Setup
```bash
# Create .env.example template
cat > .env.example << 'EOF'
# Server Configuration
PORT=3000
NODE_ENV=development

# Database
DATABASE_URL=postgresql://username:password@localhost:5432/dbname
REDIS_URL=redis://localhost:6379

# API Keys
API_KEY=your-api-key-here
JWT_SECRET=your-jwt-secret-here

# External Services
EMAIL_SERVICE_API_KEY=your-email-api-key
STORAGE_BUCKET_NAME=your-s3-bucket
EOF

# Create actual .env file (add to .gitignore!)
cp .env.example .env

# Install dotenv for loading environment variables
npm install dotenv
```

### Basic Server Setup (Express)
```javascript
// src/app.js
const express = require('express');
const dotenv = require('dotenv');

// Load environment variables
dotenv.config();

const app = express();

// Middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Basic route
app.get('/', (req, res) => {
  res.json({ 
    message: 'API is running!',
    version: process.env.npm_package_version,
    environment: process.env.NODE_ENV 
  });
});

// Health check endpoint
app.get('/health', (req, res) => {
  res.status(200).json({ 
    status: 'OK',
    timestamp: new Date().toISOString(),
    uptime: process.uptime()
  });
});

// Error handling middleware
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ 
    error: 'Something went wrong!',
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
  });
});

// 404 handler
app.use('*', (req, res) => {
  res.status(404).json({ error: 'Route not found' });
});

module.exports = app;
```

```javascript
// src/index.js
const app = require('./app');

const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`🚀 Server running on port ${PORT}`);
  console.log(`📚 Environment: ${process.env.NODE_ENV || 'development'}`);
});
```

## 🧪 Testing Setup

### Jest Configuration
```bash
# Install Jest
npm install --save-dev jest supertest

# Create jest.config.js
cat > jest.config.js << 'EOF'
module.exports = {
  testEnvironment: 'node',
  testMatch: [
    '**/tests/**/*.test.js',
    '**/tests/**/*.spec.js'
  ],
  collectCoverageFrom: [
    'src/**/*.js',
    '!src/index.js'
  ],
  coverageDirectory: 'coverage',
  coverageReporters: ['text', 'lcov', 'html']
};
EOF
```

### Basic Test Example
```javascript
// tests/app.test.js
const request = require('supertest');
const app = require('../src/app');

describe('App', () => {
  test('GET / should return API info', async () => {
    const response = await request(app)
      .get('/')
      .expect(200);

    expect(response.body).toHaveProperty('message');
    expect(response.body.message).toBe('API is running!');
  });

  test('GET /health should return health status', async () => {
    const response = await request(app)
      .get('/health')
      .expect(200);

    expect(response.body).toHaveProperty('status', 'OK');
    expect(response.body).toHaveProperty('timestamp');
    expect(response.body).toHaveProperty('uptime');
  });

  test('GET /nonexistent should return 404', async () => {
    const response = await request(app)
      .get('/nonexistent')
      .expect(404);

    expect(response.body).toHaveProperty('error', 'Route not found');
  });
});
```

## 🛠️ Development Tools

### ESLint Configuration
```bash
# Install ESLint
npm install --save-dev eslint

# Initialize ESLint
npx eslint --init

# Or create .eslintrc.js manually
cat > .eslintrc.js << 'EOF'
module.exports = {
  env: {
    node: true,
    es2021: true,
    jest: true
  },
  extends: [
    'eslint:recommended'
  ],
  parserOptions: {
    ecmaVersion: 12,
    sourceType: 'module'
  },
  rules: {
    'no-console': 'warn',
    'no-unused-vars': 'error',
    'prefer-const': 'error',
    'no-var': 'error'
  }
};
EOF
```

### Prettier Configuration
```bash
# Install Prettier
npm install --save-dev prettier

# Create .prettierrc
cat > .prettierrc << 'EOF'
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false
}
EOF

# Create .prettierignore
cat > .prettierignore << 'EOF'
node_modules
dist
build
coverage
*.md
EOF
```

### Nodemon Configuration
```bash
# Install nodemon
npm install --save-dev nodemon

# Create nodemon.json
cat > nodemon.json << 'EOF'
{
  "watch": ["src"],
  "ext": "js,json",
  "ignore": ["tests/**/*", "node_modules/**/*"],
  "exec": "node src/index.js",
  "env": {
    "NODE_ENV": "development"
  }
}
EOF
```

## 📄 Documentation Templates

### README.md Template
```markdown
# Project Name

Brief description of what this project does and who it's for.

## Features

- ✨ Feature 1
- 🚀 Feature 2
- 🔒 Feature 3

## Installation

```bash
# Clone the repository
git clone https://github.com/username/project-name.git
cd project-name

# Install dependencies
npm install

# Copy environment variables
cp .env.example .env
# Edit .env with your values

# Start development server
npm run dev
```

## Usage

```bash
# Development
npm run dev

# Production
npm start

# Testing
npm test
npm run test:coverage

# Linting
npm run lint
npm run lint:fix
```

## API Documentation

### Endpoints

#### GET /
Returns API information.

#### GET /health
Returns health status.

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `PORT` | Server port | `3000` |
| `NODE_ENV` | Environment | `development` |
| `DATABASE_URL` | Database connection string | - |

## Contributing

1. Fork the project
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
```

## 🚀 Quick Setup Scripts

### Project Creation Script
```bash
#!/bin/bash
# create-node-project.sh

PROJECT_NAME=$1
PROJECT_TYPE=${2:-"api"}

if [ -z "$PROJECT_NAME" ]; then
    echo "Usage: $0 <project-name> [api|cli|library]"
    exit 1
fi

echo "🚀 Creating Node.js project: $PROJECT_NAME ($PROJECT_TYPE)"

# Create project directory
mkdir "$PROJECT_NAME" && cd "$PROJECT_NAME"

# Initialize npm
npm init -y

# Update package.json
npm pkg set name="$PROJECT_NAME"
npm pkg set version="0.1.0"
npm pkg set description="A Node.js $PROJECT_TYPE project"
npm pkg set main="src/index.js"

# Install common dependencies
npm install dotenv

# Install dev dependencies
npm install --save-dev nodemon jest eslint prettier

# Create basic structure based on type
case $PROJECT_TYPE in
    "api")
        npm install express
        mkdir -p src/{controllers,routes,middleware,utils}
        echo 'console.log("Express API starting...");' > src/index.js
        ;;
    "cli")
        npm install commander chalk
        mkdir -p {bin,src/commands}
        npm pkg set bin.$(echo $PROJECT_NAME | sed 's/-//g')="bin/cli.js"
        echo '#!/usr/bin/env node' > bin/cli.js
        echo 'console.log("CLI tool ready!");' >> bin/cli.js
        chmod +x bin/cli.js
        ;;
    "library")
        mkdir -p {src,lib,examples}
        npm pkg set main="lib/index.js"
        echo 'module.exports = { hello: () => "Hello World!" };' > src/index.js
        ;;
esac

# Create common files
touch {.env,.env.example,.gitignore,README.md}

# Create basic .gitignore
cat > .gitignore << 'EOF'
node_modules/
.env
dist/
coverage/
*.log
.DS_Store
EOF

echo "✅ Project $PROJECT_NAME created successfully!"
echo "📁 Next steps:"
echo "   cd $PROJECT_NAME"
echo "   npm run dev"
```

Usage:
```bash
chmod +x create-node-project.sh
./create-node-project.sh my-api api
./create-node-project.sh my-tool cli
./create-node-project.sh my-lib library
```

## 🔗 Related Sections

- **[Install & Setup](install-setup.md)** - Node.js and nvm installation
- **[NPM Scripts](npm-scripts.md)** - Advanced npm script patterns
- **[Environment Variables](environment-vars.md)** - Managing configuration
- **[Simple Server](simple-server.md)** - Building basic HTTP servers

## ⚡ Quick Reference

**Project Initialization:**
```bash
mkdir my-project && cd my-project
npm init -y                         # Quick init with defaults
npm install express dotenv          # Install dependencies
npm install --save-dev nodemon jest # Install dev dependencies
```

**Essential Files:**
```bash
touch {src/index.js,.env,.env.example,.gitignore,README.md}
mkdir -p {src,tests,docs}
```

**Package.json Scripts:**
```json
{
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js",
    "test": "jest",
    "lint": "eslint . --fix"
  }
}
```

**Project Structure:**
```
my-project/
├── src/           # Source code
├── tests/         # Test files
├── docs/          # Documentation
├── .env           # Environment variables (not in git)
├── .env.example   # Environment template
├── .gitignore     # Git ignore rules
├── package.json   # Dependencies and scripts
└── README.md      # Project documentation
```

---

**Next:** Learn about [NPM Scripts](npm-scripts.md) for advanced build automation, or explore [Environment Variables](environment-vars.md) for configuration management.