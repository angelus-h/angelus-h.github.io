# MkDocs Complete Guide — Install, Run Locally & Deploy (GitHub & GitLab Pages)

**Version:** 2.0
**Last Updated:** 2026-08-15
**Author:** Learning Plans Collection

> **What's new in v2.0:** Cross-platform installation and local development (Linux **and** Windows), plus a full **GitHub Pages** deployment section (the setup this very site uses), alongside the original GitLab Pages workflow.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Quick Start](#quick-start)
4. [Installation & Local Development (Linux & Windows)](#installation-local-development-linux-windows)
5. [Project Setup](#project-setup)
6. [GitLab Pages Configuration](#static-site-configuration)
7. [GitLab CI/CD Pipeline](#cicd-pipeline)
8. [GitHub Pages Deployment](#github-pages-deployment)
9. [Theme Customization](#theme-customization)
10. [Advanced Features](#advanced-features)
11. [Domain Configuration](#domain-configuration)
12. [Best Practices](#best-practices)
13. [Troubleshooting](#troubleshooting)

---

## Introduction

**MkDocs** is a fast, simple static site generator designed for building project documentation. You write in Markdown, and MkDocs turns it into a polished, searchable website that you can host for free on **GitHub Pages** or **GitLab Pages**. This guide covers the full lifecycle: install it, run it locally on **Linux or Windows**, then deploy it to whichever platform you use.

### Why MkDocs?

- **Markdown-based** - Write docs in plain Markdown
- **Automatic deployment** - Push to GitHub/GitLab, the site updates itself via CI/CD
- **Free hosting** - GitHub Pages and GitLab Pages are free for public (and private) repos
- **Version control** - All documentation lives in Git
- **Material theme** - Professional, responsive design
- **Search built-in** - Full-text search out of the box
- **Portable** - The same `mkdocs.yml` and `docs/` work on any platform; only the CI file differs

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

## Installation & Local Development (Linux & Windows)

The Quick Start above assumes Linux/macOS. This section gives **platform-specific, copy-paste** instructions for both Linux and Windows, using an isolated virtual environment so MkDocs never pollutes your system Python.

> **Golden rule:** always work inside a virtual environment (`venv`). It keeps `mkdocs`, `mkdocs-material` and plugins pinned per project and avoids "works on my machine" surprises.

### 1. Install Python

=== "Linux (Debian/Ubuntu)"

    ```bash
    sudo apt update
    sudo apt install -y python3 python3-venv python3-pip git
    python3 --version   # expect 3.8+
    ```

    On Fedora/RHEL: `sudo dnf install -y python3 python3-pip git`

=== "Windows"

    Option A — winget (built into Windows 10/11):

    ```powershell
    winget install Python.Python.3.12
    winget install Git.Git
    ```

    Option B — download the installer from <https://www.python.org/downloads/> and
    **tick "Add python.exe to PATH"** during setup.

    Verify (open a new terminal so PATH refreshes):

    ```powershell
    py --version        # the 'py' launcher, recommended on Windows
    python --version    # alternative
    git --version
    ```

### 2. Create & activate a virtual environment

=== "Linux / macOS (bash/zsh)"

    ```bash
    cd my-docs
    python3 -m venv venv
    source venv/bin/activate
    # prompt now shows (venv)
    ```

=== "Windows (PowerShell)"

    ```powershell
    cd my-docs
    py -m venv venv
    .\venv\Scripts\Activate.ps1
    # prompt now shows (venv)
    ```

    If activation is blocked by execution policy, run once:

    ```powershell
    Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
    ```

=== "Windows (cmd.exe)"

    ```bat
    cd my-docs
    py -m venv venv
    venv\Scripts\activate.bat
    ```

### 3. Install MkDocs + Material (+ plugins)

Works identically on every platform once the venv is active:

```bash
pip install --upgrade pip
pip install mkdocs mkdocs-material
mkdocs --version
```

For **reproducible** installs, pin dependencies in `requirements.txt` and install from it:

```txt
mkdocs>=1.6.0
mkdocs-material>=9.5.0
mkdocs-meta-descriptions-plugin>=3.0.0
```

```bash
pip install -r requirements.txt
```

> This is exactly what CI does later — same file, same result locally and in the cloud.

### 4. Run the live preview server

```bash
mkdocs serve
```

Open <http://127.0.0.1:8000> — the site **auto-reloads** on every save.

Useful flags:

```bash
mkdocs serve -a 0.0.0.0:8080   # expose on your LAN / change port
mkdocs serve --strict          # treat warnings (bad links, etc.) as errors
mkdocs serve --dirtyreload     # faster reloads on very large sites
```

### 5. Build the static output

```bash
mkdocs build --strict     # generates the site/ directory
```

`site/` is what gets published. It's already in `.gitignore` — never commit it; let CI build it.

### Everyday workflow

```bash
# activate venv  →  edit docs  →  preview  →  build check  →  commit  →  push
source venv/bin/activate        # (Windows: .\venv\Scripts\Activate.ps1)
mkdocs serve                    # write & preview
mkdocs build --strict           # catch broken links before pushing
git add . && git commit -m "docs: update"
git push
# deactivate the venv when done:
deactivate
```

### Common local gotchas

| Symptom | Fix |
|---|---|
| `mkdocs: command not found` | The venv isn't active — re-run the activate command |
| `Activate.ps1 cannot be loaded` (Windows) | `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` |
| Port 8000 already in use | `mkdocs serve -a 127.0.0.1:8001` |
| Plugin `X` not found | Add it to `requirements.txt` and `pip install -r requirements.txt` |
| Edits not showing | Hard-refresh the browser (Ctrl/Cmd+Shift+R); check the terminal for build errors |

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

## GitHub Pages Deployment

GitHub Pages is the other free host for MkDocs sites — and it's what **this very site** (`angelus-h.github.io`) runs on. There are two ways to deploy: a one-command manual push, or automated GitHub Actions on every push.

### Where your site will live

| Repository type | Repo name | Published URL |
|---|---|---|
| **User/Org site** | `<username>.github.io` | `https://<username>.github.io/` |
| **Project site** | any other name | `https://<username>.github.io/<repo>/` |

Set `site_url` in `mkdocs.yml` accordingly:

```yaml
# User/org site (root):
site_url: https://username.github.io/

# Project site (subpath) — the trailing slash matters:
site_url: https://username.github.io/my-project/
```

> For **project sites**, keep `use_directory_urls: true` (the default) so internal links resolve under the subpath.

### Method 1 — `mkdocs gh-deploy` (simplest, manual)

MkDocs has this built in. It builds the site and force-pushes it to a `gh-pages` branch in one command:

```bash
# from your project root, venv active
mkdocs gh-deploy --clean
```

Then, one-time, enable Pages: **GitHub repo → Settings → Pages → Build and deployment → Source: "Deploy from a branch" → Branch: `gh-pages` / `(root)` → Save.**

- ✅ Fastest way to get online.
- ⚠️ Deploys **from your machine** — whatever you have locally goes live. No review, no CI checks. Good for solo/small sites.

### Method 2 — GitHub Actions (automated, recommended)

Let GitHub build and deploy on every push. Create **`.github/workflows/deploy.yml`**:

```yaml
name: Deploy MkDocs to GitHub Pages

on:
  push:
    branches:
      - master   # or 'main' — match your default branch
  workflow_dispatch:   # allows manual runs from the Actions tab

permissions:
  contents: write   # needed to push to the gh-pages branch

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'

      - name: Install dependencies
        run: pip install -r requirements.txt   # or: pip install mkdocs-material

      - name: Build site
        run: mkdocs build --strict

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./site
          publish_branch: gh-pages
          force_orphan: true   # keep gh-pages as a single clean commit
```

Then enable Pages once: **Settings → Pages → Source: "Deploy from a branch" → Branch: `gh-pages` / `(root)`.**

Workflow after that:

1. Push to `master` (or `main`) → Actions builds the site → pushes to `gh-pages` → live in ~1 minute.
2. Watch progress under the repo's **Actions** tab; a red run means the build failed (check the log).

> **This site's real setup** uses exactly this pattern: trigger on `master`, `pip install mkdocs-material mkdocs-meta-descriptions-plugin`, `mkdocs build`, then `peaceiris/actions-gh-pages` publishing `./site` to the `gh-pages` branch with `force_orphan: true`. Keep the workflow's plugin list in sync with `requirements.txt`.

### Alternative: the official Pages artifact action

Instead of pushing a `gh-pages` branch, you can upload the build as a Pages artifact (set **Settings → Pages → Source: "GitHub Actions"**):

```yaml
permissions:
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: '3.x' }
      - run: pip install -r requirements.txt
      - run: mkdocs build --strict
      - uses: actions/upload-pages-artifact@v3
        with: { path: ./site }
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment: github-pages
    steps:
      - uses: actions/deploy-pages@v4
```

Both approaches work; the `peaceiris` + `gh-pages` branch method is battle-tested and is what this repo uses.

### Custom domain on GitHub Pages

1. **Settings → Pages → Custom domain** → enter `docs.example.com` → Save (this writes a `CNAME` file).
2. To make it survive rebuilds, also keep the file at `docs/CNAME` containing just the domain.
3. DNS: `CNAME docs → <username>.github.io` (subdomain), or apex `A` records to GitHub's IPs (`185.199.108-111.153`).
4. Tick **Enforce HTTPS** once the certificate is issued.

### GitHub vs GitLab Pages — quick comparison

| | GitHub Pages | GitLab Pages |
|---|---|---|
| CI file | `.github/workflows/deploy.yml` | `.gitlab-ci.yml` |
| Publish target | `gh-pages` branch **or** Pages artifact | `public/` artifact from a `pages` job |
| One-command deploy | `mkdocs gh-deploy` | — (CI only) |
| Root user site | `<user>.github.io` | `<user>.gitlab.io` |
| Enable in UI | Settings → Pages → pick source | Automatic once `pages` job succeeds |

The **content is identical** — same `docs/` and `mkdocs.yml`. Only the CI file and publish mechanism change, so you can host the same repo on both if you want.

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
