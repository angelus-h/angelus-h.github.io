# Pandoc Guide and Quick Reference

**Version:** 1.0
**Last Updated:** 2026-03-20
**Author:** Learning Plans Collection

---

## Table of Contents

1. [Introduction](#introduction)
2. [Installation](#installation)
3. [Basic Usage](#basic-usage)
4. [Common Conversions](#common-conversions)
5. [Document Metadata](#document-metadata)
6. [Advanced Features](#advanced-features)
7. [Quick Reference](#quick-reference)
8. [Practical Examples](#practical-examples)
9. [Tips & Tricks](#tips--tricks)
10. [Troubleshooting](#troubleshooting)

---

## Introduction

**Pandoc** is a universal document converter that can transform files between numerous markup and document formats. Created by John MacFarlane, it's often called the "Swiss Army knife" of document conversion.

### What Can Pandoc Do?

- Convert **Markdown** to PDF, DOCX, HTML, LaTeX, EPUB
- Convert **DOCX** to Markdown, HTML, PDF
- Convert **LaTeX** to DOCX, HTML, Markdown
- Convert **HTML** to Markdown, DOCX, PDF
- Supports **60+ formats** including reStructuredText, AsciiDoc, Org-mode, MediaWiki, and more

### Why Use Pandoc?

- **Write once, publish everywhere** - Single source, multiple output formats
- **Plain text workflow** - Work in Markdown, version control friendly
- **Automation** - Scriptable conversions for CI/CD pipelines
- **Academic writing** - Citations, bibliographies, LaTeX integration
- **Customizable** - Templates, filters, Lua scripting

---

## Installation

### Linux (Debian/Ubuntu)

```bash
sudo apt update
sudo apt install pandoc

# For PDF generation (LaTeX support)
sudo apt install texlive-latex-base texlive-fonts-recommended texlive-latex-extra
```

### Linux (Fedora/RHEL)

```bash
sudo dnf install pandoc

# For PDF generation
sudo dnf install texlive-scheme-basic texlive-collection-fontsrecommended
```

### macOS (Homebrew)

```bash
brew install pandoc

# For PDF generation
brew install --cask basictex
```

### Verify Installation

```bash
pandoc --version
```

Expected output:

```
pandoc 3.1.11
Features: +server +lua
Scripting engine: Lua 5.4
```

---

## Basic Usage

### Command Structure

```bash
pandoc [OPTIONS] [INPUT-FILES] -o OUTPUT-FILE
```

### Simplest Conversion

```bash
# Markdown to HTML
pandoc input.md -o output.html

# Markdown to PDF
pandoc input.md -o output.pdf

# Markdown to DOCX
pandoc input.md -o output.docx
```

### Format Detection

Pandoc auto-detects formats from file extensions, but you can specify explicitly:

```bash
pandoc -f markdown -t html input.md -o output.html
pandoc --from=markdown --to=html input.md -o output.html
```

### Input from stdin

```bash
echo "# Hello Pandoc" | pandoc -f markdown -t html
```

---

## Common Conversions

### 1. Markdown → PDF

```bash
# Basic PDF
pandoc document.md -o document.pdf

# With custom fonts and margins
pandoc document.md -o document.pdf \
--pdf-engine=xelatex \
-V geometry:margin=1in \
-V mainfont="DejaVu Sans"

# Include table of contents
pandoc document.md -o document.pdf --toc --toc-depth=3

# With syntax highlighting for code
pandoc document.md -o document.pdf --highlight-style=tango
```

### 2. Markdown → DOCX (Microsoft Word)

```bash
# Basic conversion
pandoc document.md -o document.docx

# With custom reference document (template)
pandoc document.md -o document.docx --reference-doc=custom-template.docx

# Include table of contents
pandoc document.md -o document.docx --toc
```

### 3. Markdown → HTML

```bash
# Standalone HTML with CSS
pandoc document.md -o document.html --standalone --css=style.css

# Self-contained HTML (embedded CSS/images)
pandoc document.md -o document.html --standalone --self-contained

# HTML5 with custom template
pandoc document.md -o document.html --standalone --template=template.html
```

### 4. Markdown → Reveal.js Presentation

```bash
# Slide deck
pandoc slides.md -o slides.html -t revealjs --standalone

# With theme
pandoc slides.md -o slides.html -t revealjs -V theme=moon --standalone
```

### 5. DOCX → Markdown

```bash
# Extract Markdown from Word document
pandoc document.docx -o document.md

# With image extraction
pandoc document.docx -o document.md --extract-media=./images
```

### 6. HTML → Markdown

```bash
pandoc webpage.html -o output.md
```

### 7. LaTeX → PDF

```bash
pandoc document.tex -o document.pdf
```

### 8. Multiple Input Files

```bash
# Combine multiple Markdown files
pandoc chapter1.md chapter2.md chapter3.md -o book.pdf

# Using wildcards
pandoc *.md -o combined.pdf
```

---

## Document Metadata

### YAML Front Matter

Add metadata at the top of your Markdown file:

```yaml
---
title: "My Document Title"
author: "John Doe"
date: "2026-03-20"
abstract: "This is a brief summary of the document."
keywords: [pandoc, markdown, conversion]
lang: en-US
---

# Chapter 1

Content starts here...
```

### Common Metadata Fields

| Field | Description | Example |
|-------|-------------|---------|
| `title` | Document title | `"Pandoc Guide"` |
| `author` | Author name(s) | `"John Doe"` or `["John", "Jane"]` |
| `date` | Publication date | `"2026-03-20"` |
| `abstract` | Summary | `"This document..."` |
| `keywords` | Keywords list | `[pandoc, guide]` |
| `lang` | Language code | `en-US`, `hu-HU` |
| `toc` | Table of contents | `true` or `false` |
| `toc-title` | TOC heading | `"Contents"` |
| `numbersections` | Number sections | `true` |
| `geometry` | Page margins | `margin=1in` |
| `fontsize` | Font size | `12pt` |
| `documentclass` | LaTeX class | `article`, `report`, `book` |

### Using Metadata in Command Line

```bash
pandoc document.md -o output.pdf \
--metadata title="My Report" \
--metadata author="Jane Smith" \
--metadata date="2026-03-20"
```

---

## Advanced Features

### 1. Table of Contents

```bash
# Add TOC
pandoc document.md -o output.pdf --toc

# Custom TOC depth
pandoc document.md -o output.pdf --toc --toc-depth=2

# Custom TOC title
pandoc document.md -o output.pdf --toc --toc-title="Contents"
```

### 2. Syntax Highlighting

```bash
# List available styles
pandoc --list-highlight-styles

# Apply style
pandoc code.md -o code.html --highlight-style=tango

# Styles: pygments, tango, espresso, zenburn, kate, monochrome, breezedark, haddock
```

### 3. Citations and Bibliography

Create a `references.bib` file:

```bibtex
@book{knuth1984,
author = "Donald E. Knuth",
title = "The TeXbook",
publisher = "Addison-Wesley",
year = 1984
}
```

In your Markdown:

```markdown
According to Knuth [-@knuth1984], TeX is great.

# References
```

Convert with citations:

```bash
pandoc document.md -o output.pdf --bibliography=references.bib --citeproc
```

### 4. Custom Templates

```bash
# List default template
pandoc -D latex > template.tex

# Use custom template
pandoc document.md -o output.pdf --template=template.tex
```

### 5. Variables

```bash
# Set LaTeX variables
pandoc document.md -o output.pdf \
-V geometry:margin=1in \
-V fontsize=12pt \
-V documentclass=report
```

### 6. Filters

Pandoc filters modify the document AST (Abstract Syntax Tree).

```bash
# Use Lua filter
pandoc document.md -o output.html --lua-filter=custom-filter.lua

# Use Python filter (pandocfilters package)
pandoc document.md -o output.html --filter=custom-filter.py
```

Example Lua filter (`custom-filter.lua`):

```lua
function Strong(elem)
-- Make bold text also red
return pandoc.Span(elem.content, {style="color: red; font-weight: bold;"})
end
```

### 7. Cross-References

```bash
# Install pandoc-crossref filter
pip install pandoc-crossref

# Use in document
pandoc document.md -o output.pdf --filter pandoc-crossref
```

In Markdown:

```markdown
See [@fig:diagram] for details.

![Diagram caption](image.png){#fig:diagram}
```

---

## Quick Reference

### Common Options

| Option | Short | Description | Example |
|--------|-------|-------------|---------|
| `--from` | `-f` | Input format | `-f markdown` |
| `--to` | `-t` | Output format | `-t html` |
| `--output` | `-o` | Output file | `-o output.pdf` |
| `--standalone` | `-s` | Standalone document | `--standalone` |
| `--toc` | | Table of contents | `--toc` |
| `--toc-depth` | | TOC depth | `--toc-depth=3` |
| `--number-sections` | `-N` | Number sections | `-N` |
| `--css` | `-c` | CSS stylesheet | `-c style.css` |
| `--self-contained` | | Embed resources | `--self-contained` |
| `--template` | | Custom template | `--template=t.html` |
| `--metadata` | `-M` | Set metadata | `-M author="John"` |
| `--variable` | `-V` | Set variable | `-V fontsize=12pt` |
| `--pdf-engine` | | PDF engine | `--pdf-engine=xelatex` |
| `--highlight-style` | | Code highlighting | `--highlight-style=tango` |
| `--bibliography` | | Bibliography file | `--bibliography=refs.bib` |
| `--citeproc` | | Process citations | `--citeproc` |
| `--filter` | | Apply filter | `--filter=filter.py` |
| `--lua-filter` | | Apply Lua filter | `--lua-filter=f.lua` |
| `--reference-doc` | | DOCX template | `--reference-doc=t.docx` |
| `--extract-media` | | Extract images | `--extract-media=./img` |

### Format Identifiers

| Format | Input | Output | Extension |
|--------|-------|--------|-----------|
| Markdown | | | `.md`, `.markdown` |
| HTML | | | `.html` |
| LaTeX | | | `.tex` |
| PDF | | | `.pdf` |
| DOCX | | | `.docx` |
| ODT | | | `.odt` |
| EPUB | | | `.epub` |
| reStructuredText | | | `.rst` |
| AsciiDoc | | | `.asciidoc` |
| Org-mode | | | `.org` |
| MediaWiki | | | `.wiki` |
| Textile | | | `.textile` |
| RTF | | | `.rtf` |
| PowerPoint | | | `.pptx` |
| Reveal.js | | | `.html` |

### PDF Engines

| Engine | Command | Features |
|--------|---------|----------|
| `pdflatex` | `--pdf-engine=pdflatex` | Default, fast, limited Unicode |
| `xelatex` | `--pdf-engine=xelatex` | Full Unicode, modern fonts |
| `lualatex` | `--pdf-engine=lualatex` | Lua scripting, full Unicode |
| `wkhtmltopdf` | `--pdf-engine=wkhtmltopdf` | HTML→PDF via WebKit |
| `weasyprint` | `--pdf-engine=weasyprint` | HTML→PDF, Python-based |
| `prince` | `--pdf-engine=prince` | Commercial, high-quality |
| `context` | `--pdf-engine=context` | ConTeXt typesetting |

---

## Practical Examples

### Example 1: Technical Documentation

```bash
pandoc README.md \
-o documentation.pdf \
--from markdown \
--template=eisvogel \
--listings \
--toc \
--toc-depth=2 \
--number-sections \
-V titlepage=true \
-V titlepage-color=06386e \
-V titlepage-text-color=FFFFFF \
-V logo=company-logo.png
```

### Example 2: Academic Paper with Citations

```bash
pandoc paper.md \
-o paper.pdf \
--bibliography=references.bib \
--citeproc \
--csl=chicago-author-date.csl \
--number-sections \
--toc \
-V geometry:margin=1in \
-V fontsize=12pt \
-V documentclass=article
```

### Example 3: HTML Blog Post

```bash
pandoc blog-post.md \
-o blog-post.html \
--standalone \
--self-contained \
--css=blog-style.css \
--template=blog-template.html \
--highlight-style=pygments \
--metadata title="My Blog Post" \
--metadata author="Jane Doe" \
--metadata date="2026-03-20"
```

### Example 4: Presentation Slides

```bash
pandoc slides.md \
-o slides.html \
-t revealjs \
--standalone \
-V theme=solarized \
-V transition=slide \
--slide-level=2
```

Markdown structure:

```markdown
% Presentation Title
% Author Name
% 2026-03-20

# Section 1

## Slide 1
- Point 1
- Point 2

## Slide 2
Content here

# Section 2

## Slide 3
More content
```

### Example 5: EPUB eBook

```bash
pandoc book.md \
-o book.epub \
--toc \
--toc-depth=2 \
--epub-cover-image=cover.jpg \
--epub-metadata=metadata.xml \
--css=ebook.css
```

### Example 6: Multi-Chapter Book

```bash
pandoc \
metadata.yaml \
chapters/intro.md \
chapters/chapter1.md \
chapters/chapter2.md \
chapters/conclusion.md \
-o book.pdf \
--toc \
--toc-depth=2 \
--number-sections \
--top-level-division=chapter \
-V documentclass=book \
-V geometry:margin=1in
```

### Example 7: GitHub-Flavored Markdown to HTML

```bash
pandoc document.md \
-f gfm \
-t html \
-o output.html \
--standalone \
--highlight-style=github
```

---

## Tips & Tricks

### 1. Default Options File

Create `~/.pandoc/defaults/pdf.yaml`:

```yaml
from: markdown
to: pdf
pdf-engine: xelatex
toc: true
toc-depth: 3
number-sections: true
highlight-style: tango
metadata:
fontsize: 12pt
geometry: margin=1in
```

Use it:

```bash
pandoc document.md -o output.pdf --defaults=pdf
```

### 2. Include Files

Use `!include` in Markdown (requires filter):

```markdown
# Main Document

!include chapters/intro.md

!include chapters/methods.md
```

Or use shell:

```bash
cat header.md content.md footer.md | pandoc -o output.pdf
```

### 3. Custom Syntax Highlighting

Create `custom.theme` file and use:

```bash
pandoc code.md -o code.html --highlight-style=custom.theme
```

### 4. Watch for Changes

Use `entr` for auto-conversion:

```bash
echo document.md | entr pandoc document.md -o output.pdf
```

Or `inotifywait`:

```bash
while inotifywait -e modify document.md; do
pandoc document.md -o output.pdf
done
```

### 5. Batch Conversion

```bash
# Convert all Markdown files to PDF
for file in *.md; do
pandoc "$file" -o "${file%.md}.pdf"
done
```

### 6. Mermaid Diagrams

Use `mermaid-filter`:

```bash
npm install -g mermaid-filter
pandoc document.md -o output.pdf --filter mermaid-filter
```

In Markdown:

````markdown
![Diagram: technologies-technical-writing-Pandoc_Guide_and_Reference-c2f6173d](../../images/diagrams/technologies-technical-writing-Pandoc_Guide_and_Reference-c2f6173d.png)

````

### 7. Mathematical Formulas

```markdown
Inline math: $E = mc^2$

Display math:

$$
\int_0^\infty e^{-x^2} dx = \frac{\sqrt{\pi}}{2}
$$
```

Converts to LaTeX/MathML/MathJax depending on output format.

### 8. Custom Title Page

For PDF with fancy title page:

```yaml
---
title: "My Amazing Document"
author: "John Doe"
titlepage: true
titlepage-color: "06386e"
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
---
```

Requires Eisvogel template:

```bash
wget https://raw.githubusercontent.com/Wandmalfarbe/pandoc-latex-template/master/eisvogel.tex
pandoc document.md -o output.pdf --template=eisvogel
```

---

## Troubleshooting

### Problem: PDF Generation Fails

**Error:** `pdflatex not found`

**Solution:** Install LaTeX distribution

```bash
# Ubuntu/Debian
sudo apt install texlive-latex-base texlive-fonts-recommended texlive-latex-extra

# Fedora
sudo dnf install texlive-scheme-basic
```

### Problem: Unicode Characters Not Displaying

**Solution:** Use XeLaTeX or LuaLaTeX instead of pdflatex

```bash
pandoc document.md -o output.pdf --pdf-engine=xelatex
```

### Problem: Images Not Showing in PDF

**Solution:** Check image paths are relative, or use absolute paths

```bash
# Relative path
![Image](./images/diagram.png)

# Or specify resource path
pandoc document.md -o output.pdf --resource-path=.:./images
```

### Problem: DOCX Formatting Lost

**Solution:** Use a reference document

```bash
# Extract default reference
pandoc -o custom-reference.docx --print-default-data-file reference.docx

# Modify custom-reference.docx in Word, then use it
pandoc document.md -o output.docx --reference-doc=custom-reference.docx
```

### Problem: Code Blocks Not Highlighting

**Solution:** Specify language

````markdown
```python
def hello():
print("Hello, World!")
```
````

### Problem: Table Too Wide for PDF

**Solution:** Use `longtable` or adjust column widths

```yaml
---
tables: true
header-includes: |
\usepackage{longtable}
---
```

Or in Markdown:

```markdown
| Short | Column |
|-------|--------|
| A | B |
```

### Problem: Bibliography Not Rendering

**Solution:** Ensure `--citeproc` flag and correct bibliography format

```bash
pandoc document.md -o output.pdf \
--bibliography=refs.bib \
--citeproc \
--csl=chicago.csl
```

---

## Additional Resources

### Official Documentation

- **Pandoc Manual:** https://pandoc.org/MANUAL.html
- **Pandoc GitHub:** https://github.com/jgm/pandoc
- **Demos:** https://pandoc.org/demos.html

### Templates

- **Eisvogel LaTeX Template:** https://github.com/Wandmalfarbe/pandoc-latex-template
- **Pandoc Templates:** https://github.com/jgm/pandoc-templates

### Filters

- **pandoc-crossref:** https://github.com/lierdakil/pandoc-crossref
- **pandoc-citeproc:** Built-in (use `--citeproc`)
- **Lua Filters:** https://github.com/pandoc/lua-filters

### Citation Styles

- **CSL Repository:** https://github.com/citation-style-language/styles
- **Zotero Style Repository:** https://www.zotero.org/styles

### Community

- **Pandoc Discussion:** https://github.com/jgm/pandoc/discussions
- **Stack Overflow:** [pandoc] tag

---

## Cheat Sheet Summary

```bash
# Basic conversions
pandoc input.md -o output.pdf # Markdown → PDF
pandoc input.md -o output.docx # Markdown → Word
pandoc input.md -o output.html -s # Markdown → HTML (standalone)

# With table of contents
pandoc input.md -o output.pdf --toc --toc-depth=3

# With citations
pandoc input.md -o output.pdf --bibliography=refs.bib --citeproc

# Custom template
pandoc input.md -o output.pdf --template=custom.tex

# Multiple inputs
pandoc ch1.md ch2.md ch3.md -o book.pdf

# Slide presentation
pandoc slides.md -o slides.html -t revealjs -s

# Extract images from DOCX
pandoc input.docx -o output.md --extract-media=./images

# Set metadata
pandoc input.md -o output.pdf -M title="My Title" -M author="John Doe"

# Custom PDF engine
pandoc input.md -o output.pdf --pdf-engine=xelatex

# Self-contained HTML
pandoc input.md -o output.html -s --self-contained

# Apply filter
pandoc input.md -o output.pdf --filter pandoc-crossref
```

---

**Happy Converting!**

This guide covers the essential and advanced features of Pandoc. For more specialized use cases, consult the [official Pandoc manual](https://pandoc.org/MANUAL.html).
