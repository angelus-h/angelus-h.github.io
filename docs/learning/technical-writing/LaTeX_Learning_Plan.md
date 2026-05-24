# LaTeX Quick Reference

**Level:** Beginner to Advanced
**Prerequisites:** Basic text editing, command-line familiarity

---

## Overview

This comprehensive learning plan covers LaTeX document preparation system, from basic document structure to advanced mathematical typesetting, presentations, and publication-quality graphics.

**What You'll Learn:**
- Document structure and formatting
- Mathematical typesetting (equations, symbols, matrices)
- Tables, figures, and cross-references
- Bibliography management (BibTeX, biblatex)
- Presentation slides (Beamer)
- Graphics and diagrams (TikZ, PGFPlots)
- Custom packages and macros
- Collaborative workflows (Overleaf, Git)

---

## Week 1-2: LaTeX Fundamentals

### Module 1: Getting Started

**Topics:**
- Installing LaTeX (TeX Live, MiKTeX, MacTeX)
- LaTeX editors (TeXstudio, Overleaf, VS Code with LaTeX Workshop)
- Basic document structure
- Compilation process (pdflatex, xelatex, lualatex)

**Basic Document Structure:**
```latex
\documentclass[12pt,a4paper]{article}

% Preamble - packages and settings
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{lmodern}
\usepackage{amsmath}
\usepackage{graphicx}

\title{My First LaTeX Document}
\author{Your Name}
\date{\today}

\begin{document}

\maketitle

\section{Introduction}
This is a paragraph of text. LaTeX handles line breaking
and justification automatically.

\section{Main Content}
LaTeX uses markup commands like \textbf{bold} and
\textit{italic} for formatting.

\end{document}
```

**Hands-On Practice:**
1. Install LaTeX distribution on your system
2. Create a simple CV or résumé
3. Compile using different engines (pdflatex vs xelatex)
4. Experiment with document classes (article, report, book)

### Module 2: Text Formatting and Structure

**Topics:**
- Sections, chapters, paragraphs
- Lists (itemize, enumerate, description)
- Fonts and text styles
- Special characters and symbols
- Page layout and margins

**Document Hierarchy:**
```latex
\documentclass{report}

\begin{document}

\chapter{First Chapter}

\section{Introduction}
Section text here.

\subsection{Background}
Subsection content.

\subsubsection{Details}
Even deeper structure.

\paragraph{Small Heading} Inline paragraph heading.

\end{document}
```

**Lists and Formatting:**
```latex
% Unordered list
\begin{itemize}
 \item First item
 \item Second item
 \begin{itemize}
 \item Nested item
 \end{itemize}
\end{itemize}

% Numbered list
\begin{enumerate}
 \item First step
 \item Second step
 \item Third step
\end{enumerate}

% Description list
\begin{description}
 \item[Term 1] Definition of first term
 \item[Term 2] Definition of second term
\end{description}

% Text styles
\textbf{Bold text}, \textit{Italic text},
\underline{Underlined}, \texttt{Monospace},
\textsc{Small Caps}
```

**Hands-On Practice:**
1. Create a technical report with chapters and sections
2. Format a glossary using description lists
3. Create a multi-level outline
4. Experiment with custom fonts (fontspec package with xelatex)

---

## Week 3-4: Mathematical Typesetting

### Module 3: Math Mode Basics

**Topics:**
- Inline vs display math
- Basic operators and symbols
- Fractions, roots, exponents
- Greek letters and mathematical symbols

**Math Modes:**
```latex
% Inline math (within text)
The equation $E = mc^2$ is Einstein's famous formula.

% Display math (centered, numbered)
\begin{equation}
 \int_{-\infty}^{\infty} e^{-x^2} \, dx = \sqrt{\pi}
\end{equation}

% Display math (unnumbered)
\[
 f(x) = \frac{1}{\sigma\sqrt{2\pi}} e^{-\frac{1}{2}\left(\frac{x-\mu}{\sigma}\right)^2}
\]
```

**Common Mathematical Constructs:**
```latex
% Fractions
\frac{numerator}{denominator}
\frac{a + b}{c - d}

% Roots
\sqrt{x}
\sqrt[n]{x}

% Exponents and subscripts
x^2, x^{n+1}, x_i, x_{i,j}

% Greek letters
\alpha, \beta, \gamma, \Delta, \Omega

% Operators
\sum_{i=1}^{n} x_i
\int_{a}^{b} f(x) \, dx
\lim_{x \to \infty} f(x)
\prod_{i=1}^{n} x_i
```

**Hands-On Practice:**
1. Typeset 10 equations from your field (physics, statistics, etc.)
2. Create a formula sheet with aligned equations
3. Write a mathematical proof with numbered equations
4. Reproduce equations from a published paper

### Module 4: Advanced Math (amsmath, amssymb)

**Topics:**
- Matrices and arrays
- Multi-line equations (align, gather, split)
- Theorem environments
- Custom operators and commands

**Matrices:**
```latex
\usepackage{amsmath}

% Matrix
\[
A = \begin{pmatrix}
 a_{11} & a_{12} & a_{13} \\
 a_{21} & a_{22} & a_{23} \\
 a_{31} & a_{32} & a_{33}
\end{pmatrix}
\]

% Determinant
\[
\begin{vmatrix}
 1 & 2 \\
 3 & 4
\end{vmatrix}
\]

% Bmatrix (square brackets)
\[
\begin{bmatrix}
 1 & 0 \\
 0 & 1
\end{bmatrix}
\]
```

**Aligned Equations:**
```latex
% Multiple aligned equations
\begin{align}
 f(x) &= x^2 + 2x + 1 \\
 &= (x + 1)^2 \\
 &= (x + 1)(x + 1)
\end{align}

% Cases (piecewise functions)
\[
f(x) = \begin{cases}
 x^2 & \text{if } x \geq 0 \\
 -x^2 & \text{if } x < 0
\end{cases}
\]
```

**Theorem Environments:**
```latex
\usepackage{amsthm}

\newtheorem{theorem}{Theorem}[section]
\newtheorem{lemma}[theorem]{Lemma}
\newtheorem{definition}{Definition}[section]

\begin{theorem}[Pythagorean Theorem]
 In a right triangle, $a^2 + b^2 = c^2$.
\end{theorem}

\begin{proof}
 Proof content here.
\end{proof}
```

**Hands-On Practice:**
1. Create a linear algebra cheat sheet with matrix operations
2. Write a mathematical proof document with theorems and lemmas
3. Typeset a system of equations with solution steps
4. Create a probability distribution reference

---

## Week 5: Tables, Figures, and Cross-References

### Module 5: Tables and Tabular Data

**Topics:**
- Basic tables (tabular environment)
- Professional tables (booktabs package)
- Multi-page tables (longtable)
- Column formatting and alignment

**Basic Table:**
```latex
\begin{table}[h]
 \centering
 \caption{Sample Data}
 \label{tab:sample}
 \begin{tabular}{lcc}
 \hline
 Item & Value 1 & Value 2 \\
 \hline
 A & 10 & 20 \\
 B & 15 & 25 \\
 C & 30 & 40 \\
 \hline
 \end{tabular}
\end{table}
```

**Professional Table (booktabs):**
```latex
\usepackage{booktabs}

\begin{table}[h]
 \centering
 \caption{Performance Metrics}
 \label{tab:metrics}
 \begin{tabular}{lrrr}
 \toprule
 Method & Accuracy & Precision & Recall \\
 \midrule
 Method A & 0.92 & 0.89 & 0.91 \\
 Method B & 0.95 & 0.93 & 0.94 \\
 Method C & 0.88 & 0.85 & 0.87 \\
 \bottomrule
 \end{tabular}
\end{table}
```

**Hands-On Practice:**
1. Create a comparison table for 3 technologies
2. Format a financial report with aligned numbers
3. Build a multi-page table with longtable
4. Create a wide table with landscape orientation

### Module 6: Figures and Graphics

**Topics:**
- Including external images (graphicx)
- Figure placement and captions
- Subfigures and multiple images
- Wrapping text around figures

**Basic Figure:**
```latex
\usepackage{graphicx}

\begin{figure}[htbp]
 \centering
 \includegraphics[width=0.8\textwidth]{image.png}
 \caption{System Architecture Diagram}
 \label{fig:architecture}
\end{figure}

% Reference: See Figure~\ref{fig:architecture}
```

**Multiple Subfigures:**
```latex
\usepackage{subcaption}

\begin{figure}[htbp]
 \centering
 \begin{subfigure}{0.45\textwidth}
 \includegraphics[width=\textwidth]{image1.png}
 \caption{Before optimization}
 \label{fig:before}
 \end{subfigure}
 \hfill
 \begin{subfigure}{0.45\textwidth}
 \includegraphics[width=\textwidth]{image2.png}
 \caption{After optimization}
 \label{fig:after}
 \end{subfigure}
 \caption{Comparison of results}
 \label{fig:comparison}
\end{figure}
```

**Cross-References:**
```latex
% Labels and references
As shown in Table~\ref{tab:results} and
Figure~\ref{fig:plot}, the performance improved
significantly. See Section~\ref{sec:methods} for details.

% Automatic numbering with cleveref
\usepackage{cleveref}
\Cref{tab:results} shows... % Outputs: "Table 1 shows..."
```

**Hands-On Practice:**
1. Create a document with 5+ figures and cross-references
2. Build a comparison figure with 4 subfigures
3. Include plots exported from Python/R/MATLAB
4. Create a figure-heavy technical report

---

## Week 6: Bibliography and Citations

### Module 7: BibTeX and Bibliography Management

**Topics:**
- BibTeX basics (.bib files)
- Citation styles (numeric, author-year, IEEE, APA)
- Bibliography management tools (JabRef, Zotero)
- Modern biblatex package

**BibTeX Workflow:**
```latex
% main.tex
\documentclass{article}
\usepackage{natbib}

\begin{document}

According to \citet{knuth1984}, LaTeX is excellent.
Many researchers agree \citep{lamport1994,goossens1994}.

\bibliographystyle{plainnat}
\bibliography{references} % references.bib file

\end{document}
```

**BibTeX Database (references.bib):**
```bibtex
@book{knuth1984,
 author = {Donald E. Knuth},
 title = {The TeXbook},
 publisher = {Addison-Wesley},
 year = {1984},
 isbn = {0-201-13447-0}
}

@article{lamport1994,
 author = {Leslie Lamport},
 title = {LaTeX: A Document Preparation System},
 journal = {Addison-Wesley Professional},
 year = {1994},
 volume = {2}
}

@inproceedings{goossens1994,
 author = {Michel Goossens and Frank Mittelbach},
 title = {The LaTeX Companion},
 booktitle = {Addison-Wesley},
 year = {1994}
}
```

**Modern biblatex:**
```latex
\usepackage[backend=biber,style=ieee]{biblatex}
\addbibresource{references.bib}

\begin{document}

According to \textcite{knuth1984}, we can see that...
Multiple citations \cite{lamport1994,goossens1994}.

\printbibliography

\end{document}
```

**Hands-On Practice:**
1. Create a bibliography with 10+ references
2. Try different citation styles (APA, IEEE, Chicago)
3. Export references from Google Scholar to BibTeX
4. Write a literature review with 20+ citations

---

## Week 7: Presentations and Graphics

### Module 8: Beamer Presentations

**Topics:**
- Beamer document class
- Slides, frames, and overlays
- Themes and customization
- Transitions and animations

**Basic Beamer Presentation:**
```latex
\documentclass{beamer}
\usetheme{Madrid}
\usecolortheme{default}

\title{My Presentation}
\author{Your Name}
\date{\today}

\begin{document}

\frame{\titlepage}

\begin{frame}
 \frametitle{Outline}
 \tableofcontents
\end{frame}

\section{Introduction}

\begin{frame}
 \frametitle{Background}
 \begin{itemize}
 \item<1-> First point appears
 \item<2-> Second point appears
 \item<3-> Third point appears
 \end{itemize}
\end{frame}

\begin{frame}
 \frametitle{Key Results}
 \begin{columns}
 \column{0.5\textwidth}
 Content in left column
 \column{0.5\textwidth}
 Content in right column
 \end{columns}
\end{frame}

\end{document}
```

**Overlay Specifications:**
```latex
% Progressive reveal
\begin{frame}
 \frametitle{Incremental Display}
 \begin{itemize}
 \item<1-> Always visible
 \item<2-> Appears on slide 2
 \item<3-4> Visible only on slides 3-4
 \item<4-> Appears from slide 4 onward
 \end{itemize}

 \only<2>{Content visible only on slide 2}
 \uncover<3->{Content revealed from slide 3}
\end{frame}
```

**Hands-On Practice:**
1. Create a 15-slide presentation on a technical topic
2. Try 3 different Beamer themes (Madrid, Berlin, Copenhagen)
3. Add progressive reveals and animations
4. Include code listings and mathematical equations

### Module 9: TikZ Graphics and Diagrams

**Topics:**
- TikZ basics (nodes, paths, shapes)
- Flowcharts and block diagrams
- Plots and graphs (PGFPlots)
- Network diagrams

**Basic TikZ Drawing:**
```latex
\usepackage{tikz}
\usetikzlibrary{shapes,arrows,positioning}

\begin{tikzpicture}
 % Nodes
 \node[draw, rectangle] (A) {Input};
 \node[draw, circle, right=of A] (B) {Process};
 \node[draw, rectangle, right=of B] (C) {Output};

 % Arrows
 \draw[->] (A) -- (B);
 \draw[->] (B) -- (C);
\end{tikzpicture}
```

**Flowchart:**
```latex
\begin{tikzpicture}[
 node distance=2cm,
 block/.style={rectangle, draw, text width=5em, text centered, rounded corners, minimum height=3em},
 decision/.style={diamond, draw, text width=4.5em, text centered, inner sep=0pt}
]

\node [block] (start) {Start};
\node [decision, below of=start] (decide) {Decision?};
\node [block, below of=decide, node distance=3cm] (yes) {Yes Branch};
\node [block, right of=decide, node distance=4cm] (no) {No Branch};
\node [block, below of=yes] (end) {End};

\draw[->] (start) -- (decide);
\draw[->] (decide) -- node[left] {Yes} (yes);
\draw[->] (decide) -- node[above] {No} (no);
\draw[->] (yes) -- (end);
\draw[->] (no) |- (end);

\end{tikzpicture}
```

**PGFPlots:**
```latex
\usepackage{pgfplots}
\pgfplotsset{compat=1.18}

\begin{tikzpicture}
 \begin{axis}[
 xlabel=$x$,
 ylabel=$f(x)$,
 title={Function Plot}
 ]
 \addplot[blue, thick] {x^2};
 \addplot[red, thick] {2*x + 1};
 \legend{$x^2$, $2x+1$}
 \end{axis}
\end{tikzpicture}
```

**Hands-On Practice:**
1. Create a system architecture diagram with TikZ
2. Draw a 5-step flowchart for a process
3. Plot 3 mathematical functions with PGFPlots
4. Create a network topology diagram

---

## Week 8: Advanced Topics

### Module 10: Custom Commands and Packages

**Topics:**
- Defining custom commands (\newcommand)
- Custom environments
- Creating your own .sty package
- Class files (.cls)

**Custom Commands:**
```latex
% Simple command
\newcommand{\RR}{\mathbb{R}} % Real numbers
\newcommand{\CC}{\mathbb{C}} % Complex numbers

% Command with arguments
\newcommand{\vect}[1]{\mathbf{#1}}
\newcommand{\norm}[1]{\left\| #1 \right\|}

% Optional arguments
\newcommand{\derivative}[2][x]{\frac{d #2}{d #1}}

% Usage:
The vector $\vect{x} \in \RR^n$ has norm $\norm{\vect{x}}$.
\derivative{f} % df/dx
\derivative[t]{f} % df/dt
```

**Custom Environments:**
```latex
\newenvironment{warning}
 {\begin{quotation}\textbf{Warning:}}
 {\end{quotation}}

% Usage:
\begin{warning}
 This is a warning message.
\end{warning}
```

**Creating a Package (.sty):**
```latex
% mypackage.sty
\ProvidesPackage{mypackage}[2024/01/01 My Custom Package]

\RequirePackage{amsmath}
\RequirePackage{graphicx}

\newcommand{\mylogo}{\includegraphics[width=2cm]{logo.png}}
\newcommand{\companyname}{Acme Corporation}

\endinput
```

**Hands-On Practice:**
1. Create 10 custom commands for your field
2. Build a custom package with your frequently used macros
3. Create a custom document class for reports
4. Set up a personal LaTeX template repository

### Module 11: Collaborative LaTeX

**Topics:**
- Git version control for LaTeX
- Overleaf collaborative editing
- LaTeX diff tools (latexdiff)
- Continuous integration (GitHub Actions for LaTeX)

**Git Workflow:**
```bash
# Initialize repository
git init
git add main.tex references.bib figures/
git commit -m "Initial draft"

# Collaborate
git branch feature-section3
git checkout feature-section3
# Make changes
git commit -m "Add methodology section"
git checkout main
git merge feature-section3
```

**Latexdiff:**
```bash
# Generate diff PDF between versions
latexdiff old.tex new.tex > diff.tex
pdflatex diff.tex
```

**GitHub Actions (.github/workflows/latex.yml):**
```yaml
name: Build LaTeX
on: [push]
jobs:
 build:
 runs-on: ubuntu-latest
 steps:
 - uses: actions/checkout@v3
 - name: Compile LaTeX
 uses: xu-cheng/latex-action@v2
 with:
 root_file: main.tex
 - name: Upload PDF
 uses: actions/upload-artifact@v3
 with:
 name: PDF
 path: main.pdf
```

**Hands-On Practice:**
1. Set up a LaTeX project on GitHub with CI/CD
2. Collaborate with a colleague on Overleaf
3. Use latexdiff to track changes between versions
4. Create a template repository for future projects

---

## Week 8+: Specialized Topics (Optional)

### Module 12: Domain-Specific Applications

**Academic Writing:**
- Dissertation templates (memoir class)
- Journal article formatting (elsarticle, IEEEtran)
- Conference proceedings (ACM, Springer templates)

**Technical Documentation:**
- Code listings (listings, minted packages)
- API documentation (doxygen integration)
- Syntax highlighting for multiple languages

**Scientific Posters:**
- beamerposter package
- Conference poster templates
- Large-format printing considerations

**Books and Long Documents:**
- Book class and memoir
- Multi-file projects (\include, \input)
- Index generation (makeindex)
- Glossaries and acronyms

---

## Real-World Projects

**Project 1: Academic Paper**
Create a complete research paper with:
- Abstract and keywords
- Multi-column layout
- 20+ citations
- Tables and figures
- Appendix with supplementary material

**Project 2: Technical Documentation**
Build comprehensive technical documentation with:
- Code listings with syntax highlighting
- API reference tables
- Architecture diagrams (TikZ)
- Cross-referenced sections

**Project 3: Presentation Deck**
Create a professional Beamer presentation with:
- Title slide with logo
- Table of contents
- Progressive reveals
- Data visualization plots
- References slide

**Project 4: Book Chapter**
Write a book chapter including:
- Chapter introduction
- Multiple sections and subsections
- Theorem environments
- Bibliography
- Index entries

---

## Essential Packages Reference

| Category | Package | Purpose |
|----------|---------|---------|
| **Math** | amsmath, amssymb | Mathematical symbols and environments |
| **Graphics** | graphicx | Including external images |
| **Tables** | booktabs | Professional-looking tables |
| **Colors** | xcolor | Text and background colors |
| **Diagrams** | tikz, pgfplots | Vector graphics and plots |
| **Code** | listings, minted | Syntax-highlighted code |
| **Bibliography** | biblatex, natbib | Reference management |
| **Fonts** | fontspec, lmodern | Font selection and configuration |
| **Hyperlinks** | hyperref | Clickable cross-references |
| **Layouts** | geometry | Page margins and dimensions |

---

## Troubleshooting Guide

**Common Errors:**

1. **Undefined control sequence**
 - Missing package import
 - Typo in command name
 - Solution: Check package requirements

2. **Missing $ inserted**
 - Math mode used outside $ or \[ \]
 - Special characters (%, &, _) not escaped
 - Solution: Enclose in $ or use \textbackslash

3. **File not found**
 - Image path incorrect
 - Bibliography file missing
 - Solution: Check file paths and extensions

4. **Package conflicts**
 - Incompatible package versions
 - Package load order matters
 - Solution: Load hyperref last (usually)

**Best Practices:**
- Compile frequently to catch errors early
- Use meaningful labels (\label{fig:architecture}, not \label{fig1})
- Keep .bib file organized and validated
- Version control with Git
- Use package documentation (texdoc <packagename>)

---

## Resources

**Official Documentation:**
- LaTeX Project: https://www.latex-project.org/
- CTAN (Package repository): https://ctan.org/
- Overleaf Documentation: https://www.overleaf.com/learn

**Books:**
- "The LaTeX Companion" by Mittelbach et al.
- "LaTeX Beginner's Guide" by Stefan Kottwitz
- "The Not So Short Introduction to LaTeX" (Free PDF)

**Online Tools:**
- Overleaf: https://www.overleaf.com
- Detexify (Symbol lookup): http://detexify.kirelabs.org/
- TableGenerator: https://www.tablesgenerator.com/

**Communities:**
- TeX Stack Exchange: https://tex.stackexchange.com/
- r/LaTeX subreddit
- LaTeX Discord servers

---

## Assessment Checklist

By the end of this learning plan, you should be able to:

- [ ] Create structured documents (reports, papers, theses)
- [ ] Typeset complex mathematical equations
- [ ] Design professional tables and include figures
- [ ] Manage bibliographies with 50+ references
- [ ] Create presentation slides with Beamer
- [ ] Draw diagrams and plots with TikZ/PGFPlots
- [ ] Write custom commands and macros
- [ ] Collaborate using Git and Overleaf
- [ ] Debug common LaTeX errors
- [ ] Use appropriate packages for specific tasks

---

**Next Steps:**
- Join LaTeX community forums
- Contribute to LaTeX package development
- Create reusable templates for your organization
- Mentor others learning LaTeX
- Explore advanced topics (ConTeXt, LuaTeX)
