# VS Code & Cursor - Modern Code Editors

A comprehensive guide to Visual Studio Code and Cursor, two powerful code editors that enhance development productivity through extensions, AI integration, and customizable workflows.

## Editor Overview

### VS Code (Visual Studio Code)
- **Free, open-source** editor by Microsoft
- **Lightweight** yet powerful
- **Extensive marketplace** with thousands of extensions
- **Integrated terminal**, Git support, and debugging
- **Multi-platform** (Windows, macOS, Linux)

### Cursor
- **AI-first** code editor built on VS Code
- **Native AI integration** for code generation and assistance
- **Compatible** with VS Code extensions
- **Advanced AI features** like code prediction and refactoring
- **Privacy-focused** AI options

## Installation

### VS Code Installation
```bash
# macOS (Homebrew)
brew install --cask visual-studio-code

# Windows (Winget)
winget install Microsoft.VisualStudioCode

# Linux (Snap)
sudo snap install code --classic

# Linux (Debian/Ubuntu)
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /etc/apt/trusted.gpg.d/
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/trusted.gpg.d/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code
```

### Cursor Installation
```bash
# macOS
# Download from: https://cursor.so/
# Or via Homebrew
brew install --cask cursor

# Windows
# Download installer from: https://cursor.so/

# Linux
# Download AppImage from: https://cursor.so/
chmod +x cursor.appimage
./cursor.appimage
```

### Verify Installation
```bash
# VS Code
code --version

# Cursor  
cursor --version

# Open editor
code .        # Open current directory in VS Code
cursor .      # Open current directory in Cursor
```

## Essential Extensions

### Programming Languages

**Python Development**
```bash
# Install via command palette (Ctrl+Shift+P → Extensions: Install Extensions)
# Or via command line:
code --install-extension ms-python.python
code --install-extension ms-python.pylint
code --install-extension ms-python.black-formatter
code --install-extension charliermarsh.ruff
```

**JavaScript/TypeScript**
```bash
code --install-extension esbenp.prettier-vscode
code --install-extension dbaeumer.vscode-eslint
code --install-extension bradlc.vscode-tailwindcss
code --install-extension ms-vscode.vscode-typescript-next
```

**Web Development**
```bash
code --install-extension ritwickdey.liveserver
code --install-extension formulahendry.auto-rename-tag
code --install-extension christian-kohler.path-intellisense
code --install-extension ms-vscode.live-server
```

**Database & SQL**
```bash
code --install-extension mtxr.sqltools
code --install-extension mtxr.sqltools-driver-pg
code --install-extension mtxr.sqltools-driver-mysql
code --install-extension cweijan.vscode-database-client2
```

### Development Productivity

**Essential Tools**
```bash
code --install-extension ms-vscode-remote.remote-ssh
code --install-extension ms-vscode-remote.remote-containers
code --install-extension ms-vscode.remote-explorer
code --install-extension eamodio.gitlens
code --install-extension mhutchie.git-graph
```

**Code Quality**
```bash
code --install-extension streetsidesoftware.code-spell-checker
code --install-extension christian-kohler.path-intellisense
code --install-extension formulahendry.code-runner
code --install-extension aaron-bond.better-comments
```

**AI-Powered Extensions**
```bash
# GitHub Copilot
code --install-extension github.copilot
code --install-extension github.copilot-chat

# TabNine
code --install-extension tabnine.tabnine-vscode

# Codeium (Free alternative)
code --install-extension codeium.codeium
```

### UI & Themes

**Popular Themes**
```bash
code --install-extension zhuangtongfa.material-theme
code --install-extension wesbos.theme-cobalt2
code --install-extension dracula-theme.theme-dracula
code --install-extension pkief.material-icon-theme
code --install-extension vscode-icons-team.vscode-icons
```

## Configuration & Settings

### User Settings (settings.json)
```json
{
    // Editor appearance
    "editor.fontSize": 14,
    "editor.fontFamily": "'JetBrains Mono', 'Cascadia Code', Consolas, monospace",
    "editor.fontLigatures": true,
    "editor.lineHeight": 1.5,
    "editor.minimap.enabled": false,
    
    // Code formatting
    "editor.formatOnSave": true,
    "editor.formatOnPaste": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.codeActionsOnSave": {
        "source.organizeImports": true,
        "source.fixAll.eslint": true
    },
    
    // File handling
    "files.autoSave": "afterDelay",
    "files.autoSaveDelay": 1000,
    "files.trimTrailingWhitespace": true,
    "files.insertFinalNewline": true,
    
    // Terminal
    "terminal.integrated.fontSize": 13,
    "terminal.integrated.fontFamily": "'JetBrains Mono', monospace",
    "terminal.integrated.defaultProfile.osx": "zsh",
    
    // Git
    "git.enableSmartCommit": true,
    "git.confirmSync": false,
    "git.autofetch": true,
    
    // Language-specific settings
    "[python]": {
        "editor.defaultFormatter": "ms-python.black-formatter",
        "editor.rulers": [88],
        "editor.tabSize": 4
    },
    "[javascript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.tabSize": 2
    },
    "[typescript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.tabSize": 2
    }
}
```

### Keybindings (keybindings.json)
```json
[
    // Custom keybindings
    {
        "key": "ctrl+shift+d",
        "command": "editor.action.duplicateSelection"
    },
    {
        "key": "ctrl+shift+k",
        "command": "editor.action.deleteLines"
    },
    {
        "key": "alt+up",
        "command": "editor.action.moveLinesUpAction"
    },
    {
        "key": "alt+down",
        "command": "editor.action.moveLinesDownAction"
    },
    // Terminal shortcuts
    {
        "key": "ctrl+`",
        "command": "terminal.focus"
    },
    {
        "key": "ctrl+shift+`",
        "command": "workbench.action.terminal.new"
    }
]
```

## Cursor-Specific Features

### AI Code Generation
```typescript
// Cursor's AI can generate code from comments
// Type a comment and press Ctrl+K to generate code

// Generate a React component
// AI: Create a responsive login form component with validation

// Generate utility functions  
// AI: Create a function that debounces API calls with TypeScript

// Generate test cases
// AI: Create Jest tests for the above function
```

### AI Chat Integration
```bash
# Open AI chat panel (Ctrl+L in Cursor)
# Ask questions about your codebase
# Get explanations for complex code
# Request refactoring suggestions

# Example prompts:
"Explain this function"
"How can I optimize this code?"
"Add error handling to this API call"
"Convert this to TypeScript"
```

### Codebase Understanding
```bash
# Cursor can understand your entire codebase
# Ask questions about:
# - Project architecture
# - Function relationships  
# - Best practices for your stack
# - Code patterns and conventions
```

## Development Workflows

### Python Development Setup
```python
# 1. Install Python extension pack
# 2. Set up virtual environment
python -m venv .venv
source .venv/bin/activate  # Linux/Mac
# .venv\Scripts\activate   # Windows

# 3. Configure Python interpreter
# Ctrl+Shift+P → Python: Select Interpreter

# 4. Configure linting and formatting
# settings.json for Python:
{
    "python.linting.enabled": true,
    "python.linting.pylintEnabled": true,
    "python.formatting.provider": "black",
    "python.linting.lintOnSave": true
}

# 5. Debug configuration (.vscode/launch.json)
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Current File",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal"
        }
    ]
}
```

### JavaScript/Node.js Setup
```javascript
// 1. Install Node.js extension pack
// 2. Initialize project
npm init -y
npm install --save-dev eslint prettier @types/node

// 3. Configure ESLint (.eslintrc.js)
module.exports = {
    env: {
        node: true,
        es2021: true
    },
    extends: [
        'eslint:recommended',
        '@typescript-eslint/recommended'
    ],
    rules: {
        'no-console': 'warn',
        'no-unused-vars': 'error'
    }
};

// 4. Configure Prettier (.prettierrc)
{
    "semi": true,
    "trailingComma": "es5",
    "singleQuote": true,
    "tabWidth": 2
}

// 5. Add npm scripts (package.json)
{
    "scripts": {
        "lint": "eslint .",
        "format": "prettier --write .",
        "dev": "nodemon server.js"
    }
}
```

### Git Integration
```bash
# VS Code has excellent Git integration
# Source Control panel (Ctrl+Shift+G)

# Stage changes
git add .

# Commit with message
git commit -m "feat: add user authentication"

# GitLens extension provides advanced features:
# - Line-by-line blame annotations
# - Commit history visualization
# - Repository insights
# - File history tracking
```

### Remote Development
```bash
# SSH Remote Development
# 1. Install Remote-SSH extension
# 2. Configure SSH connection
# Ctrl+Shift+P → Remote-SSH: Connect to Host

# Docker Container Development
# 1. Install Remote-Containers extension
# 2. Create .devcontainer/devcontainer.json
{
    "name": "Python Dev",
    "image": "python:3.11",
    "customizations": {
        "vscode": {
            "extensions": [
                "ms-python.python",
                "ms-python.black-formatter"
            ]
        }
    },
    "postCreateCommand": "pip install -r requirements.txt"
}

# 3. Open in container
# Ctrl+Shift+P → Remote-Containers: Reopen in Container
```

## Productivity Tips

### Essential Shortcuts

**Navigation:**
```bash
Ctrl+P          # Quick open file
Ctrl+Shift+P    # Command palette
Ctrl+G          # Go to line
Ctrl+T          # Go to symbol in workspace
F12             # Go to definition
Alt+F12         # Peek definition
Shift+F12       # Find all references
```

**Editing:**
```bash
Ctrl+D          # Select word (repeat to select next occurrence)
Ctrl+Shift+L    # Select all occurrences
Alt+Click       # Multiple cursors
Ctrl+Shift+K    # Delete line
Alt+Up/Down     # Move line up/down
Shift+Alt+Up/Down # Duplicate line
Ctrl+/          # Toggle comment
```

**Panel Management:**
```bash
Ctrl+B          # Toggle sidebar
Ctrl+`          # Toggle terminal
Ctrl+Shift+E    # Explorer
Ctrl+Shift+F    # Search
Ctrl+Shift+G    # Git
Ctrl+Shift+D    # Debug
Ctrl+Shift+X    # Extensions
```

### Multi-Cursor Editing
```javascript
// Select multiple instances of a word
// 1. Place cursor on word
// 2. Press Ctrl+D repeatedly to select more instances
// 3. Edit all instances simultaneously

// Example: Rename all variables
const userName = 'john';
const userAge = 30;
const userEmail = 'john@example.com';

// Select 'user' and replace with 'customer' in all instances
```

### Code Snippets
```json
// Create custom snippets
// File → Preferences → Configure User Snippets

// JavaScript snippets example:
{
    "console.log": {
        "prefix": "log",
        "body": "console.log('$1');",
        "description": "Console log statement"
    },
    "function": {
        "prefix": "func",
        "body": [
            "function ${1:functionName}(${2:parameters}) {",
            "    ${3:// code}",
            "    return ${4:value};",
            "}"
        ],
        "description": "Function template"
    },
    "React Component": {
        "prefix": "rfc",
        "body": [
            "import React from 'react';",
            "",
            "const ${1:ComponentName} = () => {",
            "    return (",
            "        <div>",
            "            ${2:content}",
            "        </div>",
            "    );",
            "};",
            "",
            "export default ${1:ComponentName};"
        ]
    }
}
```

## Debugging

### Debug Configuration
```json
// .vscode/launch.json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Node.js Debug",
            "type": "node",
            "request": "launch",
            "program": "${workspaceFolder}/server.js",
            "env": {
                "NODE_ENV": "development"
            },
            "runtimeArgs": ["--inspect"]
        },
        {
            "name": "Python Debug",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "args": ["--verbose"]
        },
        {
            "name": "Attach to Chrome",
            "type": "chrome",
            "request": "attach",
            "port": 9222,
            "webRoot": "${workspaceFolder}/public"
        }
    ]
}
```

### Debugging Workflow
```bash
# 1. Set breakpoints (click line number or F9)
# 2. Start debugging (F5)
# 3. Use debug controls:
#    - Continue (F5)
#    - Step Over (F10)
#    - Step Into (F11)
#    - Step Out (Shift+F11)
#    - Restart (Ctrl+Shift+F5)
#    - Stop (Shift+F5)

# 4. Inspect variables in:
#    - Variables panel
#    - Watch panel
#    - Debug console
#    - Hover over variables
```

## Testing Integration

### Jest (JavaScript/TypeScript)
```javascript
// Install Jest extension
// Configure Jest in package.json
{
    "scripts": {
        "test": "jest",
        "test:watch": "jest --watch",
        "test:coverage": "jest --coverage"
    },
    "jest": {
        "testEnvironment": "node",
        "collectCoverageFrom": [
            "src/**/*.{js,ts}",
            "!src/**/*.test.{js,ts}"
        ]
    }
}

// Run tests from VS Code:
// - Click "Run Test" above test functions
// - Use Test Explorer panel
// - Keyboard shortcut: Ctrl+; T
```

### pytest (Python)
```python
# Install Python Test Explorer
# Configure in settings.json:
{
    "python.testing.pytestEnabled": true,
    "python.testing.pytestArgs": [
        "tests"
    ],
    "python.testing.autoTestDiscoverOnSaveEnabled": true
}

# Run tests:
# - Click "Run Test" above test functions
# - Use Test Explorer
# - Command palette: Python: Run All Tests
```

## Performance Optimization

### Editor Performance
```json
// Optimize VS Code for large projects
{
    "files.watcherExclude": {
        "**/node_modules/**": true,
        "**/.git/**": true,
        "**/dist/**": true,
        "**/build/**": true
    },
    "search.exclude": {
        "**/node_modules": true,
        "**/dist": true,
        "**/build": true,
        "**/.git": true
    },
    "files.exclude": {
        "**/node_modules": true,
        "**/.git": true,
        "**/dist": true
    },
    // Disable heavy extensions for large codebases
    "typescript.preferences.includePackageJsonAutoImports": "off",
    "typescript.suggest.autoImports": false
}
```

### Memory Management
```bash
# Monitor VS Code performance
# Help → Process Explorer

# Reduce memory usage:
# - Close unused tabs
# - Disable unnecessary extensions
# - Use workspace-specific settings
# - Split large projects into workspaces
```

## Team Collaboration

### Workspace Configuration
```json
// .vscode/settings.json (workspace settings)
{
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "python.defaultInterpreterPath": ".venv/bin/python",
    "eslint.workingDirectories": ["frontend", "backend"],
    
    // Recommended extensions for the team
    "extensions.recommendations": [
        "ms-python.python",
        "esbenp.prettier-vscode",
        "dbaeumer.vscode-eslint",
        "eamodio.gitlens"
    ]
}
```

### Live Share (Real-time collaboration)
```bash
# Install Live Share extension
code --install-extension ms-vsliveshare.vsliveshare

# Start collaboration session:
# 1. Sign in with GitHub/Microsoft account
# 2. Click "Live Share" in status bar
# 3. Share session link with team
# 4. Collaborators can edit, debug, and share terminals
```

## Integration with External Tools

### Docker Integration
```dockerfile
# .devcontainer/devcontainer.json for consistent dev environment
{
    "name": "Full Stack Dev",
    "dockerComposeFile": "docker-compose.yml",
    "service": "app",
    "workspaceFolder": "/workspace",
    "customizations": {
        "vscode": {
            "extensions": [
                "ms-python.python",
                "esbenp.prettier-vscode"
            ],
            "settings": {
                "python.defaultInterpreterPath": "/usr/local/bin/python"
            }
        }
    }
}
```

### CI/CD Integration
```yaml
# .github/workflows/vscode-check.yml
name: Code Quality Check

on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm ci
      - name: Run ESLint
        run: npm run lint
      - name: Check Prettier formatting
        run: npm run format:check
```

## Troubleshooting

### Common Issues
```bash
# Extension conflicts
# Disable extensions one by one to identify conflicts
# Use different profiles for different projects

# Performance issues
# Check CPU/memory usage in Process Explorer
# Disable TypeScript/IntelliSense for large projects
# Use workspace-specific settings

# Git integration problems
# Reset Git credentials
# Check Git configuration
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Reset and Recovery
```bash
# Reset VS Code settings
# Delete or rename:
# Windows: %APPDATA%\Code\User\settings.json
# macOS: ~/Library/Application Support/Code/User/settings.json
# Linux: ~/.config/Code/User/settings.json

# Reset all extensions
code --disable-extensions

# Safe mode
code --disable-extensions --disable-gpu
```

## Related Resources

**Official Documentation:**
- [VS Code Documentation](https://code.visualstudio.com/docs)
- [Cursor Documentation](https://cursor.so/docs)
- [VS Code Marketplace](https://marketplace.visualstudio.com/)

**Learning Resources:**
- [VS Code Tips and Tricks](https://code.visualstudio.com/docs/getstarted/tips-and-tricks)
- [Keyboard Shortcuts](https://code.visualstudio.com/docs/getstarted/keybindings)

**Related Tools:**
- **[Git](../git/)** - Version control integration
- **[Python Development](../python/)** - Language-specific workflows
- **[Node.js Development](../node/)** - JavaScript/TypeScript workflows