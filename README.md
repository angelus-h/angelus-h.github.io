# Angelus-H's Athenaeum

**Iter meum, opus vestra** - My Journey Your Resource

A professional documentation site built with MkDocs Material, featuring engineering insights and learning resources for SRE professionals.

🌐 **Live Site:** https://angelus-h.github.io/compendium/

---

## Local Development Setup

### Prerequisites

- Python 3.8+ installed
- Basic familiarity with terminal/command line

### Initial Setup

```bash
# Clone or navigate to project directory
cd ~/projects/compendium

# Create and activate virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install mkdocs-material
```

### Running Locally

```bash
# Activate venv (if not already active)
source venv/bin/activate

# Start local development server
mkdocs serve

# Open browser to: http://localhost:8000
# Live reload: changes to docs/*.md auto-refresh browser!
```

### Building for Production

```bash
# Build static site
mkdocs build

# Output: site/ directory (HTML/CSS/JS)
# This is what gets deployed to GitHub Pages
```

---

## Project Structure

```
compendium/
├── mkdocs.yml              # Site configuration
├── docs/                   # Documentation source (Markdown)
│   ├── index.md           # Homepage
│   ├── learning/          # Learning plans
│   │   ├── python/
│   │   ├── vault/
│   │   ├── claude/
│   │   └── windows/
│   ├── engineering/       # Engineering guides
│   │   ├── sre/
│   │   ├── kubernetes/
│   │   └── observability/
│   └── assets/            # Images, files
├── venv/                  # Python virtual environment (local only)
└── site/                  # Built site (generated, excluded from git)
```

---

## Adding New Content

### 1. Create Markdown File

```bash
# Example: New learning plan
touch docs/learning/new-topic/learning-plan.md
```

### 2. Write Content

Use standard Markdown with MkDocs Material extensions:

```markdown
# Title

!!! note "Important Info"
    This is an admonition box (note, tip, warning, danger)

## Section

Regular markdown content here.

### Code Example

```python
def hello_world():
    print("Hello, world!")
```

### Lists

- Item 1
- Item 2
  - Nested item

### Tables

| Column 1 | Column 2 |
|----------|----------|
| Value 1  | Value 2  |
```

### 3. Add to Navigation

Edit `mkdocs.yml`, add to `nav:` section:

```yaml
nav:
  - Technologies:
      - New Topic:
          - Guide: learning/new-topic/learning-plan.md
```

### 4. Test Locally

```bash
mkdocs serve
# Check: http://localhost:8000
```

---

## MkDocs Material Features

### Admonitions (Callout Boxes)

```markdown
!!! note "Title"
    Content here

!!! tip "Pro Tip"
    Helpful advice

!!! warning "Caution"
    Be careful with this

!!! danger "Critical"
    Don't do this!

!!! success "Done"
    Task completed

!!! info "FYI"
    Additional information
```

### Code Blocks with Syntax Highlighting

````markdown
```python
def function():
    return "highlighted"
```

```bash
echo "Bash highlighting"
```

```yaml
key: value
nested:
  key: value
```
````

### Tabbed Content

```markdown
=== "Tab 1"
    Content for tab 1

=== "Tab 2"
    Content for tab 2
```

### Task Lists

```markdown
- [x] Completed task
- [ ] Pending task
- [ ] Another task
```

### Keyboard Keys

```markdown
Press ++ctrl+alt+del++ to restart
```

### Icons and Emojis

```markdown
:material-check: Material icon
:fontawesome-brands-github: FontAwesome icon
🚀 Unicode emoji
```

---

## Deployment to GitHub Pages

### Initial Setup (when ready)

```bash
# Initialize git repository
git init
git add .
git commit -m "Initial commit"

# Create GitHub repo (via web UI or gh CLI)
gh repo create compendium --public

# Add remote
git remote add origin https://github.com/angelus-h/compendium.git

# Push to GitHub
git push -u origin main
```

### Automated Deployment (GitHub Actions)

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy MkDocs to GitHub Pages

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: 3.x
      - run: pip install mkdocs-material
      - run: mkdocs gh-deploy --force
```

Enable GitHub Pages in repo settings:
- Settings → Pages → Source: "Deploy from a branch"
- Branch: `gh-pages`

### Manual Deployment

```bash
# Build and deploy to gh-pages branch
mkdocs gh-deploy

# Site live at: https://angelus-h.github.io/compendium/
```

---

## Maintenance

### Updating Dependencies

```bash
source venv/bin/activate
pip install --upgrade mkdocs-material
pip freeze > requirements.txt  # Save versions
```

### Checking for Broken Links

```bash
mkdocs build --strict  # Fail on warnings
```

### Searching Content

Use built-in MkDocs search or:

```bash
grep -r "search term" docs/
```

---

## Troubleshooting

### `mkdocs serve` fails

```bash
# Ensure venv is activated
source venv/bin/activate

# Reinstall dependencies
pip install mkdocs-material
```

### Changes not showing in browser

- Hard refresh: ++ctrl+shift+r++ (Chrome/Firefox)
- Check terminal for build errors
- Ensure file is saved (auto-save?)

### Navigation not updating

- Edit `mkdocs.yml` → `nav:` section
- Restart `mkdocs serve`

### Sanitization script errors

```bash
# Check Python version
python3 --version  # Need 3.8+

# Run in dry-run mode first
python sanitize.py --check docs/
```

---

## Style Guide

### File Naming

- Lowercase with hyphens: `learning-plan.md`
- Descriptive names: `sre-interview-prep.md` not `prep.md`
- Consistent structure: `topic/subtopic/page.md`

### Headers

- One H1 (`#`) per page (title)
- Use H2-H4 (`##`, `###`, `####`) for structure
- No skipping levels (H2 → H4 is bad)

### Code Examples

- Always specify language for syntax highlighting
- Include comments for clarity
- Test examples before publishing
- Use realistic (but anonymized) examples

### Links

- Internal: `[Text](../relative/path.md)`
- External: `[Text](https://example.com)`
- Anchor: `[Text](#section-heading)`

---

## Contributing

### Before Adding Content

1. ✅ Sanitize all company-specific info
2. ✅ Test locally with `mkdocs serve`
3. ✅ Check for broken links/images
4. ✅ Follow style guide
5. ✅ Review for typos/clarity

### Content Types

- **Learning Plans:** Step-by-step skill development
- **Engineering Guides:** Production-tested patterns
- **Tools & Scripts:** Reusable automation
- **MCP Servers:** AI-integration documentation

---

## Resources

- **MkDocs:** https://www.mkdocs.org/
- **Material for MkDocs:** https://squidfunk.github.io/mkdocs-material/
- **Markdown Guide:** https://www.markdownguide.org/
- **MCP Protocol:** https://modelcontextprotocol.io/

---

## License

Content: Free for personal/professional learning  
Code: MIT License (unless otherwise noted)

---

**Ready to start?** Run `mkdocs serve` and open http://localhost:8000! 🚀
