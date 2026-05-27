# MkDocs with Static Site - Complete Guide

**Version:** 1.0
**Last Updated:** 2026-03-20
**Author:** Learning Plans Collection

---

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Quick Start](#quick-start)
4. [Project Setup](#project-setup)
5. [Static Site Configuration](#gitlab-pages-configuration)
6. [CI/CD Pipeline](#cicd-pipeline)
7. [Theme Customization](#theme-customization)
8. [Advanced Features](#advanced-features)
9. [Domain Configuration](#domain-configuration)
10. [Best Practices](#best-practices)
11. [Troubleshooting](#troubleshooting)

---

## Introduction

**MkDocs** is a fast, simple static site generator designed for building project documentation. Combined with **Static Site**, you can automatically deploy beautiful documentation websites directly from your GitLab repository.

### Why MkDocs + Static Site?

- **Markdown-based** - Write docs in plain Markdown
- **Automatic deployment** - Push to GitLab, site updates automatically
- **Free hosting** - Static Site is free for public and private repos
- **Version control** - All documentation in Git
- **Material theme** - Professional, responsive design
- **Search built-in** - Full-text search out of the box
- **GitLab CI/CD** - Automated builds and deployments

### What You'll Build

A professional documentation site with:
- Responsive Material Design theme
- Full-text search
- Navigation sidebar
- Table of contents
- Code syntax highlighting
- Dark/light mode toggle
- Custom branding (colors, logo)

---

## Prerequisites

### Required Tools

```bash
# Python 3.7+
python --version

# pip (Python package manager)
pip --version

# Git
git --version
```

### GitLab Account

- GitLab.com account (free tier works)
- Or GitLab self-hosted instance (e.g., gitlab.company.com)

### Basic Knowledge

- Markdown syntax
- Git basics (commit, push)
- YAML syntax (for configuration)

---

## Quick Start

### 1. Install MkDocs

```bash
# Create virtual environment (recommended)
python -m venv venv
source venv/bin/activate

# Install MkDocs with Material theme
pip install mkdocs mkdocs-material

# Verify installation
mkdocs --version
```

### 2. Create New Project

```bash
# Create new MkDocs project
mkdocs new my-project
cd my-project

# Directory structure created:
# my-project/
# docs/
# index.md
# mkdocs.yml
```

### 3. Preview Locally

```bash
# Start development server
mkdocs serve

# Open browser to http://127.0.0.1:8000
```

### 4. Build Static Site

```bash
# Build site (output to site/ directory)
mkdocs build

# Build with strict mode (fail on warnings)
mkdocs build --strict
```

---

## Project Setup

### Directory Structure

```
my-docs/
docs/ # Documentation source files
index.md # Homepage
getting-started.md # Other pages
advanced.md
assets/ # Images, CSS, JS
images/
stylesheets/
javascripts/
mkdocs.yml # MkDocs configuration
.gitlab-ci.yml # GitLab CI/CD pipeline
requirements.txt # Python dependencies
.gitignore # Git ignore file
README.md # Project README
```

### Basic mkdocs.yml

```yaml
site_name: My Documentation
site_description: Comprehensive documentation for my project
site_url: https://your-username.gitlab.io/my-project/

theme:
name: material

nav:
- Home: index.md
- Getting Started: getting-started.md
- Advanced: advanced.md
```

### requirements.txt

```txt
mkdocs>=1.5.0
mkdocs-material>=9.4.0
```

### .gitignore

```
# MkDocs build directory
site/

# Python virtual environment
venv/
env/
.venv/

# Python cache
__pycache__/
*.py[cod]
*$py.class

# IDE files
.vscode/
.idea/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db
```

---

## Static Site Configuration

### Repository Setup

1. **Create GitLab repository:**
- Go to GitLab → New Project
- Name: `my-documentation`
- Visibility: Public or Private

2. **Initialize repository:**

```bash
cd my-docs
git init
git remote add origin git@gitlab.com:username/my-documentation.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

### Static Site URL

Your site will be published at:

**GitLab.com:**
```
https://<username>.gitlab.io/<project-name>/
```

**Self-hosted GitLab:**
```
https://<your-group>.pages.gitlab.company.com/<project-name>/
```

**Example:**
```
https://jdoe.gitlab.io/my-documentation/
```

### Update mkdocs.yml for Static Site

```yaml
site_name: My Documentation
site_url: https://your-username.gitlab.io/my-project/

# Important: Set base URL for assets
use_directory_urls: true

theme:
name: material
```

---

## CI/CD Pipeline

### .gitlab-ci.yml (Basic)

Create `.gitlab-ci.yml` in repository root:

```yaml
image: python:3.11-slim

# Cache pip packages
cache:
paths:
- .cache/pip

# Static Site job
pages:
stage: deploy
script:
- pip install -r requirements.txt
- mkdocs build --strict --verbose
- mv site public # Static Site expects 'public' directory
artifacts:
paths:
- public
rules:
- if: $CI_COMMIT_BRANCH == "main"
```

### .gitlab-ci.yml (Advanced)

```yaml
image: python:3.11-slim

variables:
PIP_CACHE_DIR: "$CI_PROJECT_DIR/.cache/pip"

stages:
- test
- deploy

cache:
paths:
- .cache/pip
- venv/

before_script:
- python -m venv venv
- source venv/bin/activate
- pip install -r requirements.txt

# Test build (runs on all branches)
test:
stage: test
script:
- mkdocs build --strict --verbose
rules:
- if: $CI_PIPELINE_SOURCE == "merge_request_event"
- if: $CI_COMMIT_BRANCH && $CI_COMMIT_BRANCH != "main"

# Deploy to Static Site (only on main branch)
pages:
stage: deploy
script:
- mkdocs build --strict --verbose
- mv site public
artifacts:
paths:
- public
expire_in: 1 week
rules:
- if: $CI_COMMIT_BRANCH == "main"

# Optional: Lint markdown files
markdown-lint:
stage: test
image: node:18-alpine
before_script:
- npm install -g markdownlint-cli
script:
- markdownlint docs/**/*.md
allow_failure: true
rules:
- if: $CI_PIPELINE_SOURCE == "merge_request_event"
```

### Pipeline Workflow

1. **Push to any branch** → Test build runs
2. **Push to main branch** → Test build + Deploy to Static Site
3. **Merge request** → Test build runs, shows preview

### Verify Deployment

1. Go to GitLab → Settings → Pages
2. Check deployment status
3. Click the site URL to view published site

---

## Theme Customization

### Material Theme Configuration

```yaml
site_name: My Documentation
site_description: Comprehensive project documentation
site_url: https://username.gitlab.io/my-project/

theme:
name: material
logo: assets/images/logo.svg
favicon: assets/images/favicon.ico

# Color scheme
palette:
# Dark mode
- scheme: slate
primary: indigo
accent: cyan
toggle:
icon: material/brightness-4
name: Switch to light mode
# Light mode
- scheme: default
primary: indigo
accent: cyan
toggle:
icon: material/brightness-7
name: Switch to dark mode

# Typography
font:
text: Roboto
code: Roboto Mono

# Features
features:
- navigation.tabs # Top-level tabs
- navigation.tabs.sticky # Sticky tabs
- navigation.sections # Section headings in sidebar
- navigation.expand # Auto-expand sections
- navigation.top # Back-to-top button
- navigation.footer # Footer navigation
- search.suggest # Search suggestions
- search.highlight # Highlight search terms
- search.share # Share search results
- toc.follow # TOC follows scroll
- toc.integrate # Integrate TOC in sidebar
- content.code.copy # Copy button for code blocks
- content.code.annotate # Code annotations
- content.tabs.link # Link content tabs

# Search plugin
plugins:
- search:
lang: en
separator: '[\s\-\.]+'

# Markdown extensions
markdown_extensions:
- admonition # Call-out blocks
- pymdownx.details # Collapsible blocks
- pymdownx.superfences # Nested code blocks
- pymdownx.tabbed: # Tabbed content
alternate_style: true
- tables # Markdown tables
- attr_list # Add HTML attributes
- md_in_html # Markdown in HTML
- pymdownx.emoji: # Emoji support
emoji_index: !!python/name:material.extensions.emoji.twemoji
emoji_generator: !!python/name:material.extensions.emoji.to_svg
- pymdownx.highlight: # Code highlighting
anchor_linenums: true
- pymdownx.inlinehilite # Inline code highlighting
- pymdownx.snippets # Include external files
- pymdownx.keys # Keyboard keys
- pymdownx.tasklist: # Task lists
custom_checkbox: true
- toc:
permalink: true
toc_depth: 3

# Social links
extra:
social:
- icon: fontawesome/brands/gitlab
link: https://gitlab.com/username/project
name: GitLab Repository
- icon: fontawesome/brands/slack
link: https://your-org.slack.com
name: Slack Channel
- icon: fontawesome/solid/envelope
link: mailto:team@example.com
generator: false # Hide "Made with MkDocs Material"

# Copyright notice
copyright: Copyright &copy; 2026 Your Organization

# Custom CSS
extra_css:
- assets/stylesheets/extra.css

# Custom JavaScript
extra_javascript:
- assets/javascripts/extra.js
```

### Custom CSS (docs/assets/stylesheets/extra.css)

```css
/* Custom color overrides */
:root {
--md-primary-fg-color: #0066cc;
--md-accent-fg-color: #ff6600;
}

/* Custom styling for admonitions */
.md-typeset .admonition.note {
border-left: 4px solid #0066cc;
}

/* Code block styling */
.highlight pre {
border-radius: 4px;
}

/* Custom footer */
.md-footer {
background-color: #1a1a1a;
}
```

### Company Branding Example

```yaml
theme:
name: material
logo: assets/images/company-logo.svg
favicon: assets/images/company-logo.svg

palette:
# Dark mode (Company black)
- scheme: slate
primary: custom
accent: red
toggle:
icon: material/brightness-4
name: Switch to light mode
# Light mode (White background, Company red)
- scheme: default
primary: custom
accent: red
toggle:
icon: material/brightness-7
name: Switch to dark mode

font:
text: Company Text
code: Company Mono

extra_css:
- assets/stylesheets/company.css
```

**docs/assets/stylesheets/company.css:**

```css
/* Company color scheme */
:root {
--md-primary-fg-color: #ee0000;
--md-accent-fg-color: #ee0000;
}

[data-md-color-scheme="slate"] {
--md-default-bg-color: #0f0f0f;
--md-default-fg-color: #ffffff;
}
```

---

## Advanced Features

### 1. Navigation Structure

```yaml
nav:
- Home: index.md

- Getting Started:
- Installation: getting-started/installation.md
- Configuration: getting-started/configuration.md
- First Steps: getting-started/first-steps.md

- User Guide:
- Overview: user-guide/overview.md
- Basic Usage: user-guide/basic-usage.md
- Advanced: user-guide/advanced.md

- API Reference:
- Authentication: api/authentication.md
- Endpoints: api/endpoints.md

- Development:
- Contributing: development/contributing.md
- Testing: development/testing.md

- About:
- License: about/license.md
- Changelog: about/changelog.md
```

### 2. Code Block Features

**With line numbers and highlighting:**

````markdown
```python linenums="1" hl_lines="2 3"
def hello_world():
name = "World"
print(f"Hello, {name}!")
return True
```
````

**With title:**

````markdown
```python title="example.py"
print("Hello, World!")
```
````

**With annotations:**

````markdown
```yaml
key: value # (1)!
```

1. This is an annotation explaining the key-value pair.
````

### 3. Admonitions (Call-out Blocks)

```markdown
!!! note
This is a note.

!!! warning
This is a warning.

!!! danger
This is dangerous information.

!!! tip
This is a helpful tip.

!!! info
This is informational.

??? question "Collapsible question"
This starts collapsed and can be expanded.
```

### 4. Tabbed Content

```markdown
=== "Python"

```python
print("Hello, World!")
```

=== "JavaScript"

```javascript
console.log("Hello, World!");
```

=== "Go"

```go
fmt.Println("Hello, World!")
```
```

### 5. Diagrams (Mermaid)

Install plugin:

```bash
pip install mkdocs-mermaid2-plugin
```

**mkdocs.yml:**

```yaml
plugins:
- search
- mermaid2
```

**In Markdown:**

````markdown
![Diagram: technologies-technical-writing-MkDocs_GitLab_Pages_Guide-64072d8a](../../images/diagrams/technologies-technical-writing-MkDocs_GitLab_Pages_Guide-64072d8a.png)

````

### 6. Include External Files

**mkdocs.yml:**

```yaml
markdown_extensions:
- pymdownx.snippets:
base_path: docs
```

**In Markdown:**

```markdown
--8<-- "snippets/api-example.md"
```

### 7. Search Boosting

```yaml
plugins:
- search:
lang: en
separator: '[\s\-\.]+'
pipeline:
- stemmer
- stopWordFilter
- trimmer
```

**Boost specific pages:**

```markdown
---
search:
boost: 2
---

# Important Page

This page is more important in search results.
```

### 8. Multi-language Support

```yaml
plugins:
- search
- i18n:
default_language: en
languages:
en: English
hu: Magyar
nav_translations:
hu:
Home: Főoldal
Getting Started: Kezdő lépések
```

---

## Domain Configuration

### Custom Domain Setup

#### 1. GitLab.com Custom Domain

**In GitLab:**
1. Go to Settings → Pages
2. Click "New Domain"
3. Enter domain: `docs.example.com`
4. Add TLS certificate (Let's Encrypt or custom)

**DNS Configuration:**

| Type | Name | Value |
|------|------|-------|
| A | docs | 35.185.44.232 |
| AAAA | docs | 2600:1f18:2148:bc01:c0fe:e3d::1 |
| TXT | _gitlab-pages-verification-code.docs | gitlab-pages-verification-code=xxx |

#### 2. Self-hosted GitLab

**GitLab instance specific configuration:**

```
https://docs.example.com
```

**DNS:**

| Type | Name | Value |
|------|------|-------|
| CNAME | docs | pages.gitlab.example.com |

#### 3. Update mkdocs.yml

```yaml
site_url: https://docs.example.com/
```

### HTTPS/SSL

Static Site provides:
- Automatic Let's Encrypt certificates
- Custom certificate upload
- Automatic HTTP → HTTPS redirect

---

## Best Practices

### 1. Content Organization

```
docs/
index.md # Homepage
getting-started/ # Section directories
installation.md
configuration.md
user-guide/
overview.md
advanced.md
api/
authentication.md
endpoints.md
assets/ # Static assets
images/
stylesheets/
javascripts/
```

### 2. Writing Good Documentation

**Use descriptive titles:**

```markdown
# Installing on Ubuntu 22.04

Not: # Install
```

**Add metadata:**

```markdown
---
title: Installation Guide
description: Step-by-step installation instructions
---
```

**Include examples:**

```markdown
## Configuration

Configure the application by editing `config.yaml`:

```yaml
server:
host: 0.0.0.0
port: 8080
```
```

**Link between pages:**

```markdown
See the [Configuration Guide](../configuration.md) for details.
```

### 3. Version Control Best Practices

```bash
# Feature branch workflow
git checkout -b feature/add-api-docs
# Make changes
git add docs/api/
git commit -m "Add API authentication documentation"
git push origin feature/add-api-docs
# Create merge request
```

### 4. Review Process

**Use merge requests:**
- All changes via merge requests
- Require approvals before merge
- Run CI pipeline on MR

**Preview builds:**

Add to `.gitlab-ci.yml`:

```yaml
review:
stage: deploy
script:
- mkdocs build --strict
- mv site public
artifacts:
paths:
- public
environment:
name: review/$CI_COMMIT_REF_NAME
url: https://$CI_PROJECT_ROOT_NAMESPACE.gitlab.io/-/$CI_PROJECT_NAME/-/jobs/$CI_JOB_ID/artifacts/public/index.html
rules:
- if: $CI_PIPELINE_SOURCE == "merge_request_event"
```

### 5. Performance Optimization

**Optimize images:**

```bash
# Install imagemin
npm install -g imagemin-cli

# Optimize images
imagemin docs/assets/images/* --out-dir=docs/assets/images/
```

**Lazy load images:**

```markdown
![Image](image.png){ loading=lazy }
```

**Minify CSS/JS:**

Use MkDocs plugins:

```yaml
plugins:
- minify:
minify_html: true
minify_js: true
minify_css: true
```

### 6. Accessibility

- Use semantic headings (H1 → H2 → H3)
- Add alt text to images
- Use descriptive link text
- Test with screen readers
- Ensure color contrast

### 7. SEO Optimization

```yaml
# mkdocs.yml
site_name: My Documentation
site_description: Comprehensive documentation for my project
site_author: Your Name
site_url: https://docs.example.com/

# Per-page meta
```

**In Markdown:**

```markdown
---
title: Installation Guide
description: Step-by-step installation guide for Ubuntu, macOS, and Windows
---
```

---

## Troubleshooting

### Problem: Pages Not Deploying

**Check:**

1. Pipeline status in CI/CD → Pipelines
2. Job logs for errors
3. Artifacts contain `public/` directory
4. Branch is `main` (or configured branch)

**Solution:**

```yaml
# Ensure artifacts are correct
pages:
script:
- mkdocs build
- mv site public # Must be 'public'
artifacts:
paths:
- public # Must be 'public'
```

### Problem: 404 Errors on Subpages

**Issue:** Base URL misconfigured.

**Solution:**

```yaml
# mkdocs.yml
site_url: https://username.gitlab.io/project-name/
use_directory_urls: true
```

### Problem: Images Not Loading

**Issue:** Incorrect image paths.

**Solution:**

```markdown
# Correct (relative to current file)
![Logo](../assets/images/logo.png)

# Or absolute (from docs root)
![Logo](/assets/images/logo.png)
```

### Problem: Search Not Working

**Issue:** Search plugin not configured.

**Solution:**

```yaml
plugins:
- search:
lang: en
```

### Problem: Build Fails with "Config Error"

**Issue:** Invalid `mkdocs.yml` syntax.

**Solution:**

```bash
# Test configuration locally
mkdocs build --strict --verbose

# Validate YAML syntax
python -c "import yaml; yaml.safe_load(open('mkdocs.yml'))"
```

### Problem: Slow Build Times

**Solution:**

1. **Use caching:**

```yaml
cache:
paths:
- .cache/pip
```

2. **Optimize images:**

```bash
imagemin docs/assets/images/* --out-dir=docs/assets/images/
```

3. **Limit search indexing:**

```yaml
plugins:
- search:
prebuild_index: python # Pre-build search index
```

### Problem: Theme Not Applied

**Issue:** Theme not installed.

**Solution:**

```bash
pip install mkdocs-material
```

**requirements.txt:**

```
mkdocs-material>=9.4.0
```

### Problem: Custom CSS Not Loading

**Issue:** Path incorrect.

**Solution:**

```yaml
# mkdocs.yml
extra_css:
- assets/stylesheets/extra.css # Path relative to docs/
```

**File location:**

```
docs/assets/stylesheets/extra.css
```

---

## Complete Example

### Full Project Structure

```
my-documentation/
.gitlab-ci.yml
.gitignore
mkdocs.yml
requirements.txt
README.md
docs/
index.md
getting-started/
installation.md
configuration.md
user-guide/
overview.md
advanced.md
api/
authentication.md
endpoints.md
assets/
images/
logo.svg
favicon.ico
stylesheets/
extra.css
javascripts/
extra.js
```

### Complete mkdocs.yml

```yaml
site_name: My Documentation
site_description: Comprehensive project documentation
site_url: https://username.gitlab.io/my-documentation/
site_author: Your Name

theme:
name: material
logo: assets/images/logo.svg
favicon: assets/images/favicon.ico

palette:
- scheme: slate
primary: indigo
accent: cyan
toggle:
icon: material/brightness-4
name: Switch to light mode
- scheme: default
primary: indigo
accent: cyan
toggle:
icon: material/brightness-7
name: Switch to dark mode

font:
text: Roboto
code: Roboto Mono

features:
- navigation.tabs
- navigation.tabs.sticky
- navigation.sections
- navigation.expand
- navigation.top
- navigation.footer
- search.suggest
- search.highlight
- search.share
- toc.follow
- toc.integrate
- content.code.copy
- content.code.annotate

plugins:
- search:
lang: en

markdown_extensions:
- admonition
- pymdownx.details
- pymdownx.superfences
- pymdownx.tabbed:
alternate_style: true
- tables
- attr_list
- md_in_html
- pymdownx.emoji:
emoji_index: !!python/name:material.extensions.emoji.twemoji
emoji_generator: !!python/name:material.extensions.emoji.to_svg
- pymdownx.highlight:
anchor_linenums: true
- pymdownx.inlinehilite
- pymdownx.snippets
- toc:
permalink: true

extra:
social:
- icon: fontawesome/brands/gitlab
link: https://gitlab.com/username/my-documentation
- icon: fontawesome/brands/slack
link: https://your-org.slack.com
generator: false

copyright: Copyright &copy; 2026 Your Organization

extra_css:
- assets/stylesheets/extra.css

nav:
- Home: index.md
- Getting Started:
- Installation: getting-started/installation.md
- Configuration: getting-started/configuration.md
- User Guide:
- Overview: user-guide/overview.md
- Advanced: user-guide/advanced.md
- API Reference:
- Authentication: api/authentication.md
- Endpoints: api/endpoints.md
```

### Complete .gitlab-ci.yml

```yaml
image: python:3.11-slim

variables:
PIP_CACHE_DIR: "$CI_PROJECT_DIR/.cache/pip"

stages:
- test
- deploy

cache:
paths:
- .cache/pip

before_script:
- pip install -r requirements.txt

test:
stage: test
script:
- mkdocs build --strict --verbose
rules:
- if: $CI_PIPELINE_SOURCE == "merge_request_event"
- if: $CI_COMMIT_BRANCH && $CI_COMMIT_BRANCH != "main"

pages:
stage: deploy
script:
- mkdocs build --strict --verbose
- mv site public
artifacts:
paths:
- public
rules:
- if: $CI_COMMIT_BRANCH == "main"
```

---

## Additional Resources

### Official Documentation

- **MkDocs:** https://www.mkdocs.org/
- **Material for MkDocs:** https://squidfunk.github.io/mkdocs-material/
- **Static Site:** https://docs.gitlab.com/ee/user/project/pages/

### Themes

- **Material:** https://squidfunk.github.io/mkdocs-material/
- **ReadTheDocs:** https://www.mkdocs.org/user-guide/choosing-your-theme/#readthedocs
- **Third-party:** https://github.com/mkdocs/mkdocs/wiki/MkDocs-Themes

### Plugins

- **Awesome MkDocs:** https://github.com/mkdocs/mkdocs/wiki/MkDocs-Plugins
- **Material Plugins:** https://squidfunk.github.io/mkdocs-material/plugins/

### Examples

- **Material Reference:** https://squidfunk.github.io/mkdocs-material/reference/
- **MkDocs Examples:** https://github.com/mkdocs/mkdocs/wiki/MkDocs-Projects

---

## Quick Reference

### Essential Commands

```bash
# Create new project
mkdocs new my-project

# Start dev server
mkdocs serve

# Build site
mkdocs build

# Build with strict mode
mkdocs build --strict

# Deploy to GitHub Pages (alternative)
mkdocs gh-deploy
```

### Static Site Deployment

```yaml
# Minimal .gitlab-ci.yml
pages:
script:
- pip install mkdocs-material
- mkdocs build
- mv site public
artifacts:
paths:
- public
only:
- main
```

### Material Theme Setup

```bash
pip install mkdocs-material
```

```yaml
theme:
name: material
```

---

**Happy Documenting!**

This guide covers everything needed to create professional documentation sites with MkDocs and Static Site. For more advanced features, consult the [Material for MkDocs documentation](https://squidfunk.github.io/mkdocs-material/).
