---
name: czx-paper-to-arxiv
description: >
  Convert an ACL/EMNLP-style paper project to arxiv submission format.
  Use when the user wants to prepare a paper for arxiv upload.
  Triggers: "转arxiv", "arxiv格式", "上传arxiv", "prepare for arxiv", or explicit /czx-paper-to-arxiv invocation.
---

# Paper to ArXiv Converter

## Language Rule

**All communication with the author must be in Chinese (中文).** LaTeX content remains in English.

## Workflow

### Step 1: Identify Source Project

Determine the source paper directory from the current working directory (e.g., `/path/to/paper/iterinject-emnlp`).

Derive the arxiv output directory by replacing the venue suffix with `-arxiv` (e.g., `iterinject-emnlp` → `iterinject-arxiv`). The output directory is created at the same parent level.

### Step 2: Copy Project

Copy the entire source directory to the new arxiv directory. Exclude non-essential files:
- `.git/`, `__pycache__/`, `*.pyc`
- `README.md`, `README`
- `*.pdf` files that are reference papers (not figures), identified by filenames containing author names or paper titles
- `architecture.md`, `docs/`, `*.md` (project docs, not tex)
- Any `.txt` files that are not bibliography (keep `.bib` and `.bst`)

Keep:
- All `.tex` files
- All files in `figures/` directory
- `.bib` and `.bst` files
- `.sty` files
- All image files (`.pdf`, `.png`, `.jpg`, `.eps`) inside `figures/`

### Step 3: Modify main.tex for ArXiv

Apply these changes to `main.tex` in the arxiv directory:

1. **Remove review mode**: Change `\usepackage[review]{acl}` → `\usepackage{acl}`
2. **Ask for author info**: Prompt the user for author names, affiliations, and emails. Replace `\author{Anonymous}` with the provided author block. Split authors across multiple lines (3-4 per line) with `\\` for readability, and add `\\[6pt]` before affiliations:
   ```latex
   \author{
     First Author\textsuperscript{1} \quad
     Second Author\textsuperscript{2} \quad
     Third Author\textsuperscript{1} \\
     Fourth Author\textsuperscript{1} \quad
     Fifth Author\textsuperscript{2}\thanks{Corresponding author.} \\[6pt]
     \textsuperscript{1}Affiliation One \quad
     \textsuperscript{2}Affiliation Two \\
     \texttt{email@example.com}
   }
   ```

### Step 4: Clean Up References

- If `references.bib` exists, keep it as-is
- Remove any `.bib.txt` files (like `anthology.bib.txt`) — these are not needed for arxiv

### Step 5: Flatten (Optional)

If the user requests, inline all `\input{chapter/...}` into a single `main.tex`. By default, keep the chapter structure as arxiv supports it.

### Step 6: Compress to ZIP

Create a zip file named `<project-name>-arxiv.zip` (e.g., `iterinject-arxiv.zip`) at the parent directory level containing all files in the arxiv directory.

The zip should be structured so that extracting it produces the arxiv directory with all files at the top level (no nested folder).

```bash
cd <arxiv-dir> && zip -r ../<project-name>-arxiv.zip .
```

### Step 7: Generate ArXiv Metadata

Extract metadata from the paper and output in copy-paste ready format for the arxiv submission form. Each field as a separate code block for easy copying:

- **Title**: from `\title{...}`, remove LaTeX line breaks (`\\`) and commands
- **Authors**: comma-separated plain names, from `\author{...}`
- **Abstract**: from `\begin{abstract}...\end{abstract}`, expand `\newcommand` macros (e.g., `\oursys` → `IterInject`), remove LaTeX formatting commands
- **Comments**: venue and year (e.g., `EMNLP 2026 submission`)
- **Primary Category**: infer from paper topic (e.g., `cs.CR`, `cs.CL`, `cs.AI`, `cs.LG`)
- **Cross-list Categories**: suggest 1-2 related arxiv categories

### Step 8: Report

Print a summary:
- Output directory path
- ZIP file path
- List of key changes made
- Remind: upload the zip to https://arxiv.org/submit
