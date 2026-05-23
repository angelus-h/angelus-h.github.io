# Markdown Reference and Guide

**Author:** Claude (Anthropic)
**Version:** 1.0
**Last Updated:** 2026-03-18
**Language:** English 🇬🇧

---

##  Table of Contents

1. [What is Markdown?](#what-is-markdown)
2. [Basic Syntax](#basic-syntax)
3. [Text Formatting](#text-formatting)
4. [Headers](#headers)
5. [Lists](#lists)
6. [Links and Images](#links-and-images)
7. [Code Blocks](#code-blocks)
8. [Tables](#tables)
9. [Blockquotes](#blockquotes)
10. [Horizontal Rules](#horizontal-rules)
11. [GitHub Flavored Markdown](#github-flavored-markdown)
12. [Advanced Features](#advanced-features)
13. [Best Practices](#best-practices)
14. [Common Mistakes](#common-mistakes)
15. [Tools and Editors](#tools-and-editors)

---

## What is Markdown?

**Markdown** is a lightweight markup language that you can use to add formatting elements to plaintext text documents.

### Advantages

-  Simple and easy to learn
-  Human-readable even in plain text
-  Platform-independent
-  Widely supported (GitHub, GitLab, Slack, Discord, etc.)
-  Fast and efficient for documentation

### Use Cases

-  README files
-  Documentation
- 📧 Emails (on some platforms)
- 💬 Chat messages (Slack, Discord)
- 📓 Notes and wikis
- 📖 Blog posts

---

## Basic Syntax

### Text Formatting

| Format | Markdown | Result |
|--------|----------|--------|
| **Bold** | `**text**` or `__text__` | **text** |
| *Italic* | `*text*` or `_text_` | *text* |
| ***Bold + Italic*** | `***text***` | ***text*** |
| ~~Strikethrough~~ | `~~text~~` | ~~strikethrough~~ |
| `Code` | `` `code` `` | `code` |

**Example:**

```markdown
This is **bold** and this is *italic* text.
You can combine them: ***bold and italic***.
```

**Result:**

This is **bold** and this is *italic* text.
You can combine them: ***bold and italic***.

---

## Headers

```markdown
# H1 - Largest header
## H2 - Second level header
### H3 - Third level header
#### H4 - Fourth level header
##### H5 - Fifth level header
###### H6 - Smallest header
```

**Alternative syntax (H1 and H2 only):**

```markdown
H1 Header
=========

H2 Header
---------
```

### Best Practices

-  Use only one H1 per document (title)
-  Don't skip levels (H1 → H2 → H3, not H1 → H3)
-  Add blank lines before and after headers

---

## Lists

### Unordered Lists

```markdown
- Item 1
- Item 2
  - Sub-item 2.1
  - Sub-item 2.2
- Item 3

* Also works with asterisks
+ And with plus signs
```

**Result:**

- Item 1
- Item 2
  - Sub-item 2.1
  - Sub-item 2.2
- Item 3

### Ordered Lists

```markdown
1. First item
2. Second item
3. Third item
   1. Sub-item 3.1
   2. Sub-item 3.2
```

**Result:**

1. First item
2. Second item
3. Third item
   1. Sub-item 3.1
   2. Sub-item 3.2

### Task Lists (GitHub)

```markdown
- [ ] Incomplete task
- [x] Completed task
- [ ] Another task
```

**Result:**

- [ ] Incomplete task
- [x] Completed task
- [ ] Another task

---

## Links and Images

### Links

```markdown
[Link text](https://example.com)
[Link with tooltip](https://example.com "Tooltip text")
[Reference link][reference-id]

[reference-id]: https://example.com "Optional tooltip"
```

**Automatic link:**

```markdown
<https://example.com>
```

### Images

```markdown
![Alt text](image.jpg)
![Alt text](image.jpg "Tooltip")
![Image with link](image.jpg)
```

**Image as link:**

```markdown
[![Alt text](image.jpg)](https://example.com)
```

### Relative Paths

```markdown
![Logo](../images/logo.png)
[Documentation](./docs/README.md)
```

---

## Code Blocks

### Inline Code

```markdown
This is an `inline code` example.
```

**Result:** This is an `inline code` example.

### Code Blocks

**Backtick method (recommended):**

````markdown
```python
def hello_world():
    print("Hello, World!")
```
````

**Result:**

```python
def hello_world():
    print("Hello, World!")
```

### Supported Languages (Syntax Highlighting)

````markdown
```bash
echo "Bash code"
```

```python
print("Python code")
```

```javascript
console.log("JavaScript code");
```

```json
{
  "name": "value"
}
```

```yaml
key: value
```

```sql
SELECT * FROM users;
```

```dockerfile
FROM alpine:latest
RUN apk add --no-cache python3
```
````

### Diff Highlighting

````markdown
```diff
- Removed line
+ Added line
  Unchanged line
```
````

---

## Tables

### Basic Table

```markdown
| Header 1 | Header 2 | Header 3 |
|----------|----------|----------|
| Row 1    | Data     | Data     |
| Row 2    | Data     | Data     |
```

**Result:**

| Header 1 | Header 2 | Header 3 |
|----------|----------|----------|
| Row 1    | Data     | Data     |
| Row 2    | Data     | Data     |

### Alignment

```markdown
| Left     | Center   | Right    |
|:---------|:--------:|---------:|
| Left     | Center   | Right    |
| Text     | Text     | Text     |
```

**Result:**

| Left     | Center   | Right    |
|:---------|:--------:|---------:|
| Left     | Center   | Right    |
| Text     | Text     | Text     |

### Complex Table

```markdown
| Feature | Description | Status |
|---------|-------------|--------|
| **Feature A** | Important feature |  Done |
| *Feature B* | In development | 🚧 WIP |
| ~~Feature C~~ | Removed |  Removed |
```

---

## Blockquotes

### Simple Blockquote

```markdown
> This is a blockquote.
> It can span multiple lines.
```

**Result:**

> This is a blockquote.
> It can span multiple lines.

### Nested Blockquotes

```markdown
> First level quote
>
> > Second level quote
> >
> > > Third level quote
```

**Result:**

> First level quote
>
> > Second level quote
> >
> > > Third level quote

### Blockquote with Formatting

```markdown
> **Important:**
>
> This is an *especially* important note.
>
> ```python
> # Works with code too
> print("Hello")
> ```
```

---

## Horizontal Rules

```markdown
---

***

___
```

**All three produce the same result:**

---

---

## GitHub Flavored Markdown

### Emojis

```markdown
:smile: :heart: :+1: :rocket: :fire:
```

**Result:** 😊 ❤   🔥

**Full emoji list:** https://github.com/ikatyang/emoji-cheat-sheet

### Mentions

```markdown
@username
#123 (issue/PR reference)
```

### Auto-linking

```markdown
GitHub automatically links:
- URLs: https://github.com
- Issues: #123
- Commits: abc123def
```

### Table Extras

```markdown
| Action | Keyboard Shortcut |
|--------|-------------------|
| Copy | <kbd>Ctrl</kbd> + <kbd>C</kbd> |
| Paste | <kbd>Ctrl</kbd> + <kbd>V</kbd> |
```

**Result:**

| Action | Keyboard Shortcut |
|--------|-------------------|
| Copy | <kbd>Ctrl</kbd> + <kbd>C</kbd> |
| Paste | <kbd>Ctrl</kbd> + <kbd>V</kbd> |

---

## Advanced Features

### HTML Elements

You can use HTML in Markdown:

```markdown
<details>
<summary>Click to expand</summary>

This content is initially hidden.

</details>
```

**Result:**

<details>
<summary>Click to expand</summary>

This content is initially hidden.

</details>

### Footnotes

```markdown
This is text with a footnote[^1].

[^1]: This is the footnote text.
```

### Definition Lists

```markdown
Markdown
: Simple text formatting language

HTML
: HyperText Markup Language
```

### Table of Contents Generation

```markdown
## Table of Contents

- [Chapter 1](#chapter-1)
- [Chapter 2](#chapter-2)

## Chapter 1

Content...

## Chapter 2

Content...
```

---

## Best Practices

### File Structure

```markdown
# Project Name

One-sentence description.

## Table of Contents

- [Installation](#installation)
- [Usage](#usage)
- [Examples](#examples)
- [License](#license)

## Installation

...

## Usage

...
```

### Line Length

-  Wrap around 80-100 characters (readability)
-  One blank line = new paragraph
-  Two spaces at line end = line break

### Consistency

-  Choose one style (e.g., `-` or `*` for lists)
-  Use the same method for headers
-  Maintain indentation (2 or 4 spaces)

### README.md Template

```markdown
# Project Name

[![Build Status](badge-url)](link)
[![License](badge-url)](link)

One-sentence project description.

## ✨ Features

- Feature 1
- Feature 2
- Feature 3

##  Installation

\`\`\`bash
npm install package-name
\`\`\`

##  Usage

\`\`\`javascript
const package = require('package-name');
\`\`\`

## 📖 Documentation

Full documentation: [link](url)

## 🤝 Contributing

Pull requests are welcome! See [CONTRIBUTING.md](CONTRIBUTING.md).

##  License

[MIT](LICENSE)
```

---

## Common Mistakes

###  Missing Blank Lines

**Wrong:**
```markdown
# Header
This text is too close to the header.
```

**Right:**
```markdown
# Header

This text is properly separated.
```

###  Wrong Indentation in Lists

**Wrong:**
```markdown
- Item 1
 - Wrong indentation
```

**Right:**
```markdown
- Item 1
  - Correct indentation (2 spaces)
```

###  Missing Space After # in Headers

**Wrong:**
```markdown
#Header (no space)
```

**Right:**
```markdown
# Header (with space)
```

###  URL Parentheses

**Wrong:**
```markdown
[Link](https://example.com (wrong))
```

**Right:**
```markdown
[Link](https://example.com "Tooltip")
```

---

## Tools and Editors

### Online Markdown Editors

- **Dillinger:** https://dillinger.io
- **StackEdit:** https://stackedit.io
- **HackMD:** https://hackmd.io

### Desktop Editors

- **Typora:** https://typora.io (WYSIWYG)
- **Mark Text:** https://marktext.app (Open Source)
- **Obsidian:** https://obsidian.md (Notes + Markdown)
- **Visual Studio Code:** Built-in Markdown preview

### VS Code Extensions

```bash
# Markdown Preview Enhanced
ext install shd101wyy.markdown-preview-enhanced

# Markdown All in One
ext install yzhang.markdown-all-in-one

# Markdown Lint
ext install DavidAnson.vscode-markdownlint
```

### CLI Tools

```bash
# Markdown → HTML
npm install -g marked
marked README.md > output.html

# Markdown linter
npm install -g markdownlint-cli
markdownlint *.md

# Pandoc (universal converter)
sudo apt install pandoc
pandoc README.md -o output.pdf
```

---

## Quick Reference

### Syntax Summary

| Element | Markdown |
|---------|----------|
| Header 1 | `# H1` |
| Bold | `**bold**` |
| Italic | `*italic*` |
| Link | `[text](url)` |
| Image | `![alt](url)` |
| Code | `` `code` `` |
| List | `- item` |
| Quote | `> quote` |
| Line | `---` |

### Common Shortcuts (VS Code)

| Action | Keyboard Shortcut |
|--------|-------------------|
| Bold | `Ctrl + B` |
| Italic | `Ctrl + I` |
| Preview | `Ctrl + Shift + V` |
| Preview Side-by-Side | `Ctrl + K V` |

---

## Resources and Further Learning

### Official Documentation

- **Markdown Guide:** https://www.markdownguide.org
- **CommonMark Spec:** https://commonmark.org
- **GitHub Flavored Markdown:** https://github.github.com/gfm/

### Interactive Learning Tools

- **Markdown Tutorial:** https://www.markdowntutorial.com
- **Learn Markdown:** https://commonmark.org/help/tutorial/

### Cheat Sheets

- **Markdown Cheatsheet:** https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet
- **GitHub Markdown:** https://guides.github.com/features/mastering-markdown/

---

## Frequently Asked Questions (FAQ)

### How do I create a line break?

Two methods:
1. Two spaces at end of line + Enter
2. Blank line (new paragraph)

```markdown
Line 1
Line 2 (after two spaces)

New paragraph (after blank line)
```

### How do I use special characters?

Use backslash (`\`) for escaping:

```markdown
\* Asterisk literally (not list)
\# Hash literally (not header)
\[Not a link\]
```

### Does HTML work in Markdown?

Yes, but use carefully:

```markdown
<span style="color:red">Red text</span>

<div align="center">
Centered content
</div>
```

### How do I create multi-line code blocks?

````markdown
```languagename
code
multiple lines
```
````

---

## Summary

 **Markdown is a simple, effective tool for documentation**

**Key Points:**
- Easy to learn, human-readable
- Widely supported
- Platform-independent
- Fast and productive

**Next Steps:**
1. Practice basic syntax
2. Use in daily work (README, notes)
3. Explore GitHub Flavored Markdown extras
4. Automate documentation generation

---

**Last Updated:** 2026-03-18
**Version:** 1.0
**License:** CC BY 4.0
**Author:** Claude (Anthropic)
