# Marp - Markdown Presentations Guide

**Created:** 2026-03-22
**Language:** English
**Level:** Beginner → Advanced

---

## What is Marp?

**Marp (Markdown Presentation Ecosystem)** is an open-source tool that enables creating professional presentations in Markdown format.

### Philosophy

```
Code (Markdown) → Presentation (PDF/HTML/PPTX)
```

**Traditional method:**
- PowerPoint/Google Slides: GUI clicking
- Lots of time on formatting, less on content
- Version control difficult (binary files)

**Marp method:**
- Markdown file: Plain text, version trackable
- Git-friendly, diffable
- Fast, efficient, automatable

---

## Why Use Marp?

###  Advantages

1. **Git-friendly version control**
   ```bash
   git diff DEMO-SLIDES.md
   # See exactly what changed
   ```

2. **Fast editing**
   - Markdown: every developer knows it
   - No clicking, just typing
   - Focus on content, not formatting

3. **Automation**
   ```bash
   # In CI/CD pipeline
   marp slides.md --pdf
   # Auto-generated PDF with every commit
   ```

4. **Multiple output formats**
   - PDF (for presentations)
   - HTML (interactive, browser-based)
   - PPTX (PowerPoint compatible)

5. **Customizability**
   - Full control with CSS
   - Custom themes (e.g., Company branding)
   - Built-in code syntax highlighting

6. **Portable**
   - Plain text files
   - No vendor lock-in
   - Editable anywhere (vim, VS Code, etc.)

###  Disadvantages

1. **Learning curve**
   - Markdown knowledge required (but it's basic)
   - CSS knowledge for customization

2. **Limited visual editing**
   - No drag-and-drop
   - Complex animations are difficult

3. **Image sizing**
   - Manual configuration needed
   - No WYSIWYG

4. **Collaboration**
   - Harder with non-technical people
   - No real-time collaboration (like Google Slides)

---

## Installation

### 1. Check Node.js

```bash
# Check if Node.js is installed
node --version
# v18.0.0 or newer required

npm --version
# 8.0.0 or newer
```

**If not installed:**
```bash
# Fedora/RHEL
sudo dnf install nodejs npm

# macOS
brew install node

# Or: use nvm (recommended)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install --lts
```

### 2. Install Marp CLI

```bash
# Global installation (recommended)
npm install -g @marp-team/marp-cli

# Verify
marp --version
# @marp-team/marp-cli v3.x.x
```

**Alternative: npx (no installation needed)**
```bash
# One-time use, no installation required
npx @marp-team/marp-cli slides.md --pdf
```

### 3. VS Code extension (optional)

1. Open VS Code
2. Extensions → "Marp for VS Code"
3. Install
4. Open `.md` file → automatic preview

---

## Basic Usage

### 1. First Presentation

**File:** `hello-marp.md`

```markdown
---
marp: true
---

# First Slide

This is the first slide.

---

# Second Slide

- Point 1
- Point 2
- Point 3

---

# Third Slide

**Bold text** and *italic text*.
```

### 2. Export

```bash
# PDF export
marp hello-marp.md --pdf
# Output: hello-marp.pdf

# HTML export
marp hello-marp.md --html
# Output: hello-marp.html (opens in browser)

# PowerPoint export
marp hello-marp.md --pptx
# Output: hello-marp.pptx
```

### 3. Live preview (watch mode)

```bash
# Auto-reload on edit
marp hello-marp.md --watch --html

# Open in browser: hello-marp.html
# Refreshes on every save
```

---

## Markdown Syntax (Marp-specific)

### Slide Separation

```markdown
# Slide 1

Content...

---

# Slide 2

More content...

---

# Slide 3
```

**Important:** `---` (3 hyphens) = new slide

### Frontmatter (configuration)

```markdown
---
marp: true
theme: default
paginate: true
size: 16:9
backgroundColor: #fff
---

# First slide after configuration
```

**Options:**
- `marp: true` - Enable Marp (required)
- `theme:` - Theme selection (default, gaia, uncover)
- `paginate: true` - Page numbering
- `size:` - Slide size (16:9, 4:3, A4)
- `backgroundColor:` - Background color

### Directives (slide-specific)

```markdown
---

<!-- _class: lead -->

# Centered Title Slide

This slide will be centered.

---

<!-- _backgroundColor: black -->
<!-- _color: white -->

# Black Background, White Text

Custom colors for this slide only.

---

# Normal Slide

Returns to defaults.
```

**Directive types:**
- `<!-- _directive -->` - Current slide only
- `<!-- directive -->` - All slides from here on

---

## Content Formatting

### Text Style

```markdown
**Bold text**
*Italic text*
~~Strikethrough text~~
`Inline code`

Normal text with **important** parts.
```

### Lists

```markdown
# Bullet List

- Item 1
- Item 2
  - Sub-item 2.1
  - Sub-item 2.2
- Item 3

# Numbered List

1. First step
2. Second step
3. Third step
```

### Code Blocks

````markdown
# Python Example

```python
def hello_marp():
    print("Hello from Marp!")
    return True

result = hello_marp()
```

# Bash Example

```bash
#!/bin/bash
echo "Marp is awesome"
marp slides.md --pdf
```
````

**Syntax highlighting:** Automatic, 100+ languages supported

### Links and Images

```markdown
# Links

[Company](https://company.com)
[Email me](mailto:user@company.com)

# Images

![Alt text](image.png)
![width:500px](image.png)
![height:300px](image.png)

# Background Image

![bg](background.jpg)
![bg right:40%](side-image.png)
```

### Tables

```markdown
# Table

| Name | Role | Email |
|------|------|-------|
| Alice | Dev | alice@example.com |
| Bob | SRE | bob@example.com |
| Carol | Manager | carol@example.com |

Alignment:
| Left | Center | Right |
|:-----|:------:|------:|
| L    | C      | R     |
```

---

## Themes

### Built-in Themes

Marp comes with 3 built-in themes:

**1. Default**
```markdown
---
marp: true
theme: default
---
```
- Classic, minimalist
- White background, black text

**2. Gaia**
```markdown
---
marp: true
theme: gaia
---
```
- Elegant, professional
- Blue accents

**3. Uncover**
```markdown
---
marp: true
theme: uncover
---
```
- Modern, bold
- Dark background, large text

### Theme Switching Per Slide

```markdown
---
theme: default
---

# Default theme slide

---

<!-- theme: gaia -->

# Gaia theme slide

---

<!-- theme: uncover -->

# Uncover theme slide
```

### Custom Theme Creation (CSS)

**File:** `custom-theme.css`

```css
/* @theme custom */

@import 'default';

section {
  background-color: #f0f0f0;
  font-family: 'Arial', sans-serif;
}

h1 {
  color: #0066cc;
  border-bottom: 3px solid #0066cc;
}

a {
  color: #cc0000;
}

code {
  background-color: #e0e0e0;
  color: #333;
}
```

**Usage:**
```bash
marp slides.md --theme custom-theme.css --pdf
```

Or in frontmatter:
```markdown
---
marp: true
theme: custom
---
```

---

## Advanced Features

### Inline CSS (quick customization)

```markdown
---
marp: true
style: |
  section {
    background: linear-gradient(to bottom, #667eea 0%, #764ba2 100%);
    color: white;
  }
  h1 {
    color: #ffcc00;
  }
---

# Gradient Background Slide

This applies to the entire presentation.
```

### Two-Column Layout

```markdown
# Two Columns

<div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px;">

<div>

## Left Side

- Point 1
- Point 2

</div>

<div>

## Right Side

- Point A
- Point B

</div>

</div>
```

### Background Images (special)

```markdown
# Background Image Examples

<!-- Full background -->
![bg](image.jpg)

<!-- Right side 40% -->
![bg right:40%](image.jpg)

<!-- Left side 30% -->
![bg left:30%](image.jpg)

<!-- Vertical layout -->
![bg vertical](image1.jpg)
![bg](image2.jpg)

<!-- Opacity setting -->
![bg opacity:0.5](image.jpg)
```

### Fragments (step-by-step reveal)

**Note:** Marp doesn't natively support PowerPoint-like animations, but it's achievable with JavaScript in HTML export.

**Workaround:**
```markdown
# Step-by-Step Presentation

---

# Step 1

First information

---

# Step 1 + 2

First information

**Second information**

---

# Step 1 + 2 + 3

First information

**Second information**

***Third information***
```

### Speaker Notes

```markdown
# Slide Title

Public content that the audience sees.

<!--
These are speaker notes.
Only you see them in presenter mode.
Reminders, timing, notes.
-->

---
```

**In HTML export:**
```bash
marp slides.md --html
# Open in browser
# Press 'P' key → Presenter mode
# You see the notes
```

---

## Company Branding Example

### Minimal Version

```markdown
---
marp: true
theme: gaia
style: |
  section {
    background-color: #FFFFFF;
    color: #3C3F42;
  }
  h1 {
    color: #EE0000;
    border-bottom: 4px solid #EE0000;
  }
  strong {
    color: #EE0000;
  }
---

# Company Presentation

**Infrastructure Team** - Site Reliability Production Engineering

---

# Contents

- Problem
- Solution
- Demo
- Conclusions
```

### Full Version (with fonts)

```markdown
---
marp: true
theme: gaia
style: |
  @import url('https://fonts.googleapis.com/css2?family=Red+Hat+Display:wght@400;600;700&family=Red+Hat+Text:wght@400;500&display=swap');

  section {
    font-family: 'Company Text', Arial, sans-serif;
    background-color: #FFFFFF;
    color: #3C3F42;
  }

  h1, h2, h3 {
    font-family: 'Company Display', Arial, sans-serif;
    color: #EE0000;
  }

  h1 {
    border-bottom: 4px solid #EE0000;
    padding-bottom: 20px;
  }

  strong {
    color: #EE0000;
    font-weight: 700;
  }

  pre {
    background-color: #3C3F42;
    color: #FFFFFF;
    border-left: 5px solid #EE0000;
  }

  section.lead {
    background: linear-gradient(135deg, #000000 0%, #3C3F42 100%);
    color: #FFFFFF;
    text-align: center;
  }

  section.lead h1 {
    color: #FFFFFF;
    border-bottom: 4px solid #EE0000;
  }
---

<!-- _class: lead -->

# AI-Powered Workflows

**Site Reliability Production Engineering**

Company

---

# Next slide...
```

---

## Practical Examples

### 1. Technical Deep Dive

```markdown
---
marp: true
theme: gaia
paginate: true
---

# Kubernetes Troubleshooting

**Infrastructure Team - 2026-03-22**

---

## Agenda

1. Problem Overview
2. Root Cause Analysis
3. Solution Architecture
4. Implementation Steps
5. Lessons Learned

---

## Problem: Pod CrashLoopBackOff

```bash
$ kubectl get pods -n production
NAME                     READY   STATUS             RESTARTS
app-xyz-7d9f8-abcde     0/1     CrashLoopBackOff   15
```

**Symptoms:**
- Service downtime since 09:00 UTC
- 500 errors on frontend
- Customer impact: High

---

## Root Cause

```yaml
# deployment.yaml (WRONG)
resources:
  limits:
    memory: "128Mi"  # Too low!
  requests:
    memory: "64Mi"
```

**Analysis:**
- Application heap size: 256Mi
- Container limit: 128Mi
- Result: **OOMKilled**

---

## Solution

```yaml
# deployment.yaml (FIXED)
resources:
  limits:
    memory: "512Mi"  # Increased
  requests:
    memory: "256Mi"  # Proper baseline
```

```bash
kubectl apply -f deployment.yaml
kubectl rollout status deployment/app-xyz
```

**Result:**  Pods healthy, service restored

---

## Lessons Learned

1. **Always set resource limits**
   - Based on actual metrics, not guesses

2. **Monitor memory trends**
   - Use Prometheus/Grafana dashboards

3. **Document baselines**
   - Save for future reference

---

# Questions?

**Contact:** team@company.com
```

### 2. Executive Status Update

```markdown
---
marp: true
theme: gaia
---

<!-- _class: lead -->

# Q1 2026 Infrastructure Review

**Infrastructure Team**

Miklos Greczi
2026-03-22

---

## Key Metrics

| Metric | Q4 2025 | Q1 2026 | Change |
|--------|---------|---------|--------|
| Uptime | 99.5% | 99.8% | +0.3% ↑ |
| MTTR | 45 min | 28 min | -38% ↓ |
| Incidents | 12 | 7 | -42% ↓ |
| Cost | $50k | $48k | -4% ↓ |

**Summary:** Significant improvements across all areas

---

## Major Achievements

 **AI-Powered Incident Response**
- 70-80% time savings
- Faster root cause analysis

 **Infrastructure Automation**
- 50% reduction in manual tasks
- GitOps deployment pipeline

 **Cost Optimization**
- Right-sized resources
- Spot instance utilization

---

## Q2 2026 Roadmap

1. **Multi-region failover** (April)
   - Active-active configuration
   - <5 min RTO

2. **Observability enhancement** (May)
   - Unified logging platform
   - Advanced alerting

3. **Security hardening** (June)
   - Zero-trust network
   - Automated compliance checks

---

<!-- _class: lead -->

# Thank You

**Questions?**
```

---

## Best Practices

### 1. One Idea - One Slide

 **Bad:** 20 bullet points on one slide
 **Good:** 3-5 points maximum

```markdown
# Bad Example

- Point 1
- Point 2
- Point 3
- Point 4
- Point 5
- Point 6
- ...
- Point 20

# Good Example

## Problem

- Jira tickets contain too many comments
- New engineers take hours to build context
- Managers lack quick summaries
```

### 2. Visualize

 **Bad:** Text only
 **Good:** Code, table, diagram

```markdown
# Architecture

```
┌─────────────┐
│   Frontend  │
└──────┬──────┘
       │
┌──────▼──────┐
│   Backend   │
└──────┬──────┘
       │
┌──────▼──────┐
│  Database   │
└─────────────┘
```
```

### 3. Consistent Style

```markdown
# Same formatting for all titles

## Also for subtitles

- List items too
- Same indentation
- Same spacing
```

### 4. Code Blocks: Always Specify Language

````markdown
 Bad:
```
kubectl get pods
```

 Good:
```bash
kubectl get pods
```
````

### 5. Timing

**Presentation length:**
- 1 slide = ~1 min (content-intensive)
- 1 slide = ~30 sec (simple)

**Example 10-minute demo:**
- Title: 0.5 min
- Problem: 1 min
- Demo 1: 2 min
- Demo 2: 2.5 min
- Safety: 1.5 min
- How-to: 2 min
- Wrap-up: 0.5 min
= **10 min, 12-15 slides**

---

## Workflow

### 1. Development

```bash
# Project init
mkdir my-presentation
cd my-presentation

# Create slides
vim slides.md

# Live preview
marp slides.md --html --watch
# Open in browser: slides.html
```

### 2. Version Control

```bash
# Git init
git init
git add slides.md
git commit -m "Initial presentation"

# Modifications
vim slides.md
git diff slides.md  # See what changed
git commit -am "Add safety rules section"

# Branch workflow
git checkout -b feature/add-demo-2
# Modifications...
git commit -am "Add Demo #2 slides"
git checkout main
git merge feature/add-demo-2
```

### 3. CI/CD Integration

**GitHub Actions example:**

`.github/workflows/build-slides.yml`

```yaml
name: Build Presentation

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install Marp CLI
        run: npm install -g @marp-team/marp-cli

      - name: Build PDF
        run: marp slides.md --pdf

      - name: Upload PDF
        uses: actions/upload-artifact@v3
        with:
          name: presentation
          path: slides.pdf
```

**Result:** Fresh PDF automatically after every commit

### 4. Review Process

```bash
# Pull Request workflow
git checkout -b review/q1-update
vim slides.md  # Modifications

git commit -am "Update Q1 metrics"
git push origin review/q1-update

# On GitHub: Create Pull Request
# Reviewer sees the diff
# Merge after approval
```

---

## Comparison with Other Tools

### Marp vs PowerPoint

| Feature | Marp | PowerPoint |
|---------|------|------------|
| Version control |  Excellent (Git) |  Poor |
| Collaboration | ⚠ Merge conflicts |  Good (OneDrive) |
| Automation |  Excellent |  Difficult |
| Visual editor |  None |  Yes |
| Learning curve | ⚠ Medium |  Low |
| Cost |  Free | ⚠ License needed |
| Portability |  Plain text |  Binary |
| Technical audience |  Ideal | ⚠ OK |

**Use Marp if:**
- Technical presentation
- Git workflow important
- Lots of code/terminal output
- Want to automate

**Use PowerPoint if:**
- Many visual elements
- Non-technical audience
- Complex animations needed
- Collaboration important

### Marp vs Google Slides

| Feature | Marp | Google Slides |
|---------|------|---------------|
| Real-time collaboration |  |  |
| Offline work |  | ⚠ |
| Version control |  | ⚠ |
| Customizability |  | ⚠ |
| Cost |  Free |  Free |
| Accessibility | ⚠ CLI needed |  Browser |

### Marp vs reveal.js

| Feature | Marp | reveal.js |
|---------|------|-----------|
| Simplicity |  Easy | ⚠ More complex |
| Interactivity | ⚠ Limited |  Excellent |
| PDF export |  Native | ⚠ Tricky |
| Markdown |  Native |  With plugin |
| Setup |  npm install | ⚠ Project setup |

---

## Tips & Tricks

### 1. Quick Prototypes

```bash
# Quick draft creation
cat > quick-draft.md <<EOF
---
marp: true
---

# Title

- Point 1
- Point 2

---

# Conclusion

Done!
EOF

marp quick-draft.md --html
open quick-draft.html
```

### 2. Slide Templates

**File:** `templates/title-slide.md`
```markdown
<!-- _class: lead -->

# {{TITLE}}

**{{SUBTITLE}}**

{{AUTHOR}}
{{DATE}}
```

**Usage:**
```bash
sed 's/{{TITLE}}/My Presentation/' templates/title-slide.md >> slides.md
```

### 3. Auto-Resize Images

```markdown
<!-- In CSS: -->
---
style: |
  img {
    max-width: 80%;
    max-height: 60vh;
    display: block;
    margin: auto;
  }
---

# Image

![](large-image.png)
<!-- Automatically fits -->
```

### 4. Footer on Every Slide

```markdown
---
marp: true
footer: 'Infrastructure Team - Company | 2026-03-22'
---

# Slide 1

Footer appears at bottom.

---

# Slide 2

Same footer.
```

### 5. QR Code Embedding

```bash
# Generate QR code
qrencode -o qr-code.png "https://red.ht/konflux"

# Use on slide
```

```markdown
# Contact

![width:200px](qr-code.png)

Scan for more info!
```

---

## Troubleshooting

### Problem: Marp Doesn't Export

**Error:**
```
Error: Cannot find module '@marp-team/marp-cli'
```

**Solution:**
```bash
# Clean reinstall
npm uninstall -g @marp-team/marp-cli
npm cache clean --force
npm install -g @marp-team/marp-cli

# Verify
which marp
marp --version
```

### Problem: Fonts Don't Appear

**Error:** Seeing Arial instead of Company font

**Solution 1: Internet connection**
```markdown
<!-- Loads from Google Fonts -->
@import url('https://fonts.googleapis.com/...');
```

**Solution 2: Local font**
```bash
# Download font
git clone https://github.com/RedHatOfficial/RedHatFont.git

# In CSS:
@font-face {
  font-family: 'Company Display';
  src: url('fonts/RedHatDisplay-Bold.woff2');
}
```

### Problem: PDF Too Large

**Error:** 20MB PDF (should be: <5MB)

**Solution:**
```bash
# Compress images first
mogrify -resize 1920x1080 -quality 80 *.jpg

# Or: HTML export (smaller)
marp slides.md --html
```

### Problem: Code Text Overflows

**Error:** Long line goes off slide

**Solution:**
```markdown
---
style: |
  pre {
    overflow-x: auto;
    font-size: 18px;  /* Smaller font */
  }
---
```

---

## Additional Resources

### Official Documentation

- **Marp Homepage:** https://marp.app/
- **Marp CLI GitHub:** https://github.com/marp-team/marp-cli
- **Marpit Framework:** https://marpit.marp.app/
- **Directives Reference:** https://marpit.marp.app/directives

### Community Content

- **Marp examples:** https://github.com/yhatt/marp/wiki/Sample-slide-deck
- **Theme collection:** https://github.com/topics/marp-theme
- **VS Code extension:** https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode

### Company Specific

- **Company Font:** https://github.com/RedHatOfficial/RedHatFont
- **Brand guidelines:** https://company.com/brand-guidelines
- **Color palette:** https://company.com/brand-guidelines/color

### Alternatives

- **reveal.js:** https://revealjs.com/ (HTML-based)
- **slidev:** https://sli.dev/ (Vue-based, Markdown)
- **mdx-deck:** https://github.com/jxnblk/mdx-deck (React components)
- **Deckset:** https://www.deckset.com/ (macOS app, paid)

---

## Practical Exercises

### Exercise 1: Simple Presentation (15 min)

Create a 5-slide introduction presentation:

1. Title slide (your name, role)
2. About me (3-5 bullet points)
3. Skills (table: Skill | Level)
4. Current project (code snippet)
5. Contact (email, LinkedIn)

```bash
marp about-me.md --pdf
```

### Exercise 2: Company Theming (30 min)

Take the previous presentation and add:
- Company colors
- Company fonts
- Footer with company name
- Lead slide class for title

### Exercise 3: Technical Presentation (60 min)

Create a 10-slide "How-To" guide:
- Topic: Kubernetes basics OR Jira workflow
- Include: code blocks, diagram, table
- Export to PDF and HTML formats
- Put in Git, make 3 commits

---

## Summary

### What Did You Learn?

 **Marp Basics**
- Markdown → Presentation
- Installation and usage
- Export (PDF, HTML, PPTX)

 **Customization**
- Using themes
- CSS customization
- Company branding

 **Advanced Techniques**
- Two-column layout
- Background images
- Speaker notes

 **Workflow**
- Git integration
- CI/CD automation
- Review process

### Next Steps

1. **Practice:** Create 3 short presentations on different topics
2. **Theme:** Create custom CSS theme for your team
3. **Automate:** Set up GitHub Actions
4. **Share:** Teach a colleague how to use Marp

### Useful Commands Collection

```bash
# Quick export
marp slides.md --pdf
marp slides.md --html
marp slides.md --pptx

# Watch mode
marp slides.md --watch --html

# Custom theme
marp slides.md --theme custom.css --pdf

# Multiple files
marp *.md --pdf

# Server mode (for presenting)
marp --server slides.md
# Open: http://localhost:8080
```

---

**Congratulations! You're now a Marp pro!** 

**Contact:**
- Questions: developer@company.com
- Examples: `/home/mgreczi/ai/konflux-lumino/investigations/DEMO-SLIDES.md`

**Version:** 1.0
**Last updated:** 2026-03-22
