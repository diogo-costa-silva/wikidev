# MkDocs

> **Install, serve, build, and deploy modern documentation sites.**

MkDocs is a fast, simple static site generator that's geared towards building project documentation. It uses Markdown for content and can create beautiful, responsive documentation sites.

## 🎯 Goals

- **Create professional documentation** with minimal setup
- **Modern responsive design** with Material theme
- **Easy content management** using Markdown
- **Automated deployment** to GitHub Pages or other hosting

## 🔍 Quick Verification

Test that MkDocs is working:

```bash
# Check installation
mkdocs --version

# Create test site
mkdocs new test-docs
cd test-docs

# Serve locally
mkdocs serve

# Should be available at http://127.0.0.1:8000
# Build static site
mkdocs build

# Check output
ls site/
```

## 📦 Installation

### Using uv (Recommended)
```bash
# Install globally with uv
uv tool install mkdocs[material]

# Or in virtual environment
uv venv docs-env
source docs-env/bin/activate
uv pip install mkdocs-material
```

### Using pip
```bash
# Basic MkDocs
pip install mkdocs

# With Material theme (recommended)
pip install mkdocs-material

# With additional plugins
pip install mkdocs-material[recommended]
```

### Using conda
```bash
conda install -c conda-forge mkdocs
conda install -c conda-forge mkdocs-material
```

## 🚀 Quick Start

### Create New Documentation Site
```bash
# Create new MkDocs project
mkdocs new my-docs
cd my-docs

# Directory structure created:
# my-docs/
# ├── docs/
# │   └── index.md
# └── mkdocs.yml
```

### Basic Configuration
Edit `mkdocs.yml`:

```yaml
site_name: My Documentation
site_description: A short description of my project
site_author: Your Name
site_url: https://yourusername.github.io/my-docs/

theme:
  name: material
  features:
    - navigation.tabs
    - navigation.sections
    - navigation.top
    - search.suggest
    - search.highlight
    - content.code.copy

markdown_extensions:
  - admonition
  - codehilite
  - toc:
      permalink: true

nav:
  - Home: index.md
  - Getting Started: getting-started.md
  - User Guide: user-guide.md
  - API Reference: api.md
```

### Development Workflow
```bash
# Start development server
mkdocs serve

# Development server starts at http://127.0.0.1:8000
# Auto-reloads when files change

# Build static site
mkdocs build

# Deploy to GitHub Pages
mkdocs gh-deploy
```

## 🎨 Material Theme Configuration

### Enhanced mkdocs.yml
```yaml
site_name: Professional Documentation
site_description: Modern documentation with Material Design
site_author: Your Name
site_url: https://yourusername.github.io/docs/

repo_name: username/repository
repo_url: https://github.com/username/repository
edit_uri: edit/main/docs/

theme:
  name: material
  language: en
  icon:
    logo: material/library
  favicon: assets/favicon.ico
  
  features:
    # Navigation
    - navigation.tabs
    - navigation.tabs.sticky
    - navigation.sections
    - navigation.top
    - navigation.tracking
    - navigation.instant
    
    # Search
    - search.suggest
    - search.highlight
    - search.share
    
    # Content
    - content.code.copy
    - content.code.select
    - content.code.annotate
    
    # Table of contents
    - toc.follow
    - toc.integrate

  palette:
    # Light mode
    - media: "(prefers-color-scheme: light)"
      scheme: default
      primary: blue grey
      accent: indigo
      toggle:
        icon: material/brightness-7
        name: Switch to dark mode
    
    # Dark mode
    - media: "(prefers-color-scheme: dark)"
      scheme: slate
      primary: blue grey
      accent: indigo
      toggle:
        icon: material/brightness-4
        name: Switch to light mode

plugins:
  - search:
      lang: en
  - tags
  - git-revision-date-localized:
      enable_creation_date: true
      type: timeago

markdown_extensions:
  - admonition
  - pymdownx.details
  - pymdownx.superfences:
      custom_fences:
        - name: mermaid
          class: mermaid
          format: !!python/name:pymdownx.superfences.fence_code_format
  - pymdownx.highlight:
      anchor_linenums: true
  - pymdownx.inlinehilite
  - pymdownx.tabbed:
      alternate_style: true
  - pymdownx.tasklist:
      custom_checkbox: true
  - attr_list
  - md_in_html
  - toc:
      permalink: true
      title: On this page

extra:
  social:
    - icon: fontawesome/brands/github
      link: https://github.com/username
    - icon: fontawesome/brands/twitter
      link: https://twitter.com/username
```

## 📝 Content Creation

### Markdown Basics
```markdown
# Page Title

## Section Header

Regular paragraph text with **bold** and *italic* text.

### Code Examples

Inline code: `print("hello")`

Code blocks:
```python
def hello_world():
    print("Hello, World!")
    return True
```

### Lists
- Bullet point 1
- Bullet point 2
  - Nested item
  - Another nested item

1. Numbered item 1
2. Numbered item 2

### Links and Images
[Link text](https://example.com)
[Internal link](../other-page.md)

![Image alt text](images/screenshot.png)
```

### Advanced Features

#### Admonitions
```markdown
!!! note
    This is a note admonition.

!!! warning
    This is a warning admonition.

!!! tip "Custom Title"
    This is a tip with custom title.

??? info "Collapsible Info"
    This is collapsible content.
```

#### Code Annotations
```markdown
```python title="example.py"
def process_data(data): # (1)
    """Process the input data."""
    return data.upper()  # (2)
```

1. Main processing function
2. Convert to uppercase
```

#### Tabs
```markdown
=== "Python"
    ```python
    print("Hello World")
    ```

=== "JavaScript"
    ```javascript
    console.log("Hello World");
    ```

=== "Bash"
    ```bash
    echo "Hello World"
    ```
```

## 🗂️ Site Structure

### Recommended Directory Layout
```
my-docs/
├── docs/                    # Documentation content
│   ├── index.md            # Homepage
│   ├── getting-started.md  # Getting started guide
│   ├── user-guide/         # User guide section
│   │   ├── index.md        # Section overview
│   │   ├── installation.md
│   │   └── configuration.md
│   ├── api/                # API documentation
│   │   ├── index.md
│   │   └── reference.md
│   ├── assets/             # Images, CSS, JS
│   │   ├── images/
│   │   └── stylesheets/
│   └── javascripts/        # Custom JavaScript
├── mkdocs.yml              # Configuration file
├── requirements.txt        # Python dependencies
└── .github/                # GitHub Actions (optional)
    └── workflows/
        └── docs.yml
```

### Navigation Configuration
```yaml
nav:
  - Home: index.md
  - Getting Started:
    - Installation: getting-started/installation.md
    - Quick Start: getting-started/quick-start.md
    - Configuration: getting-started/configuration.md
  - User Guide:
    - user-guide/index.md
    - Basic Usage: user-guide/basic-usage.md
    - Advanced Features: user-guide/advanced.md
  - API Reference:
    - api/index.md
    - Functions: api/functions.md
    - Classes: api/classes.md
  - About: about.md
```

## 🚀 Deployment

### GitHub Pages Deployment
```bash
# Automatic deployment
mkdocs gh-deploy

# Deploy from specific branch
mkdocs gh-deploy --config-file mkdocs.yml

# Deploy to custom domain
# (Add CNAME file to docs/ directory)
echo "docs.example.com" > docs/CNAME
mkdocs gh-deploy
```

### GitHub Actions Workflow
Create `.github/workflows/docs.yml`:

```yaml
name: Deploy Documentation

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.x'
      
      - name: Install dependencies
        run: |
          pip install mkdocs-material
          pip install -r requirements.txt
      
      - name: Build documentation
        run: mkdocs build
      
      - name: Setup Pages
        uses: actions/configure-pages@v3
      
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./site
      
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

### Other Deployment Options
```bash
# Deploy to custom server via SCP
mkdocs build
scp -r site/ user@server:/var/www/docs/

# Deploy to Netlify
mkdocs build
# Upload site/ directory to Netlify

# Deploy to Vercel
# Add vercel.json configuration
mkdocs build
vercel --prod
```

## 🔧 Useful Plugins

### Essential Plugins
```bash
# Install commonly used plugins
pip install mkdocs-git-revision-date-localized-plugin
pip install mkdocs-minify-plugin
pip install mkdocs-redirects
pip install mkdocs-section-index
```

### Plugin Configuration
```yaml
plugins:
  - search:
      lang: en
  - git-revision-date-localized:
      enable_creation_date: true
      type: timeago
  - minify:
      minify_html: true
  - redirects:
      redirect_maps:
        'old-page.md': 'new-page.md'
  - section-index
  - tags:
      tags_file: tags.md
```

## 📊 SEO and Analytics

### SEO Configuration
```yaml
# mkdocs.yml
site_name: My Documentation
site_description: Comprehensive guide for my project
site_author: Your Name
site_url: https://docs.example.com

extra:
  generator: false  # Remove "Made with MkDocs" footer
  
  # Open Graph / Social Media
  social:
    - icon: fontawesome/brands/github
      link: https://github.com/username/project
  
# Custom meta tags
extra_css:
  - stylesheets/extra.css

extra_javascript:
  - javascripts/extra.js
```

### Google Analytics
```yaml
# mkdocs.yml
extra:
  analytics:
    provider: google
    property: G-XXXXXXXXXX
    
  # Cookie consent
  consent:
    title: Cookie consent
    description: >- 
      We use cookies to recognize your repeated visits and preferences.
```

## 🎯 Best Practices

### Content Organization
```markdown
# Use clear hierarchical structure
- Keep navigation shallow (max 3 levels)
- Use descriptive page titles
- Include "On this page" navigation
- Cross-reference related content

# Writing style
- Write in active voice
- Use short paragraphs
- Include code examples
- Provide context for examples
```

### Performance Optimization
```yaml
# mkdocs.yml
plugins:
  - minify:
      minify_html: true
      minify_js: true
      minify_css: true
  - optimize:
      enabled: !ENV [CI, false]

# Optimize images
# Use WebP format when possible
# Compress images before including
# Use appropriate image sizes
```

## 🔗 Related Sections

- **[Python Project Structure](../python/project-structure.md)** - Organizing documentation in Python projects
- **[Git Basic Workflow](../git/basic-workflow.md)** - Version control for documentation
- **[GitHub CLI](../git/github-cli.md)** - Automating GitHub Pages deployment

## ⚡ Quick Reference

**Project Setup:**
```bash
mkdocs new my-docs          # Create new project
cd my-docs
mkdocs serve               # Start development server
mkdocs build               # Build static site
mkdocs gh-deploy           # Deploy to GitHub Pages
```

**Essential Commands:**
```bash
mkdocs --help              # Show help
mkdocs new PROJECT         # Create new project
mkdocs serve               # Development server
mkdocs build               # Build static site
mkdocs gh-deploy           # Deploy to GitHub Pages
mkdocs --version           # Show version
```

**Configuration Essentials:**
```yaml
site_name: My Documentation
theme:
  name: material
  features:
    - navigation.tabs
    - content.code.copy
plugins:
  - search
markdown_extensions:
  - codehilite
  - toc:
      permalink: true
```

**Development Workflow:**
1. `mkdocs new project-name` - Create project
2. Edit `mkdocs.yml` - Configure site
3. Write content in `docs/` - Add Markdown files
4. `mkdocs serve` - Test locally
5. `mkdocs gh-deploy` - Deploy to web

---

**Next:** Explore other development tools in the [Toolkit](index.md), or learn about [GitHub CLI](../git/github-cli.md) for automation and deployment workflows.