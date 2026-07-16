# Workflow: arXiv Papers → Obsidian Vault

A step-by-step guide to building a research reading workflow where you convert arXiv PDFs into fully-rendered Markdown notes inside Obsidian — complete with formulas, tables, and figures.

---

## Why Obsidian + Markdown for Research Papers?

- **Rendered formulas:** Obsidian supports KaTeX math rendering natively
- **Bidirectional links:** Link paper notes to concept notes with `[[wikilinks]]`
- **Full-text search:** Find any formula or paragraph across all your papers instantly
- **Zotero integration:** Combine with the Zotero plugin for citation management

---

## Prerequisites

- [Obsidian](https://obsidian.md) installed with a vault set up
- Obsidian settings: **Editor → Enable LaTeX math** (on by default)
- A paper you want to read (arXiv PDF)

---

## Method 1: Using paper2md (Recommended — No Setup)

**Best for:** Researchers who want clean, ready-to-use output in 60 seconds.

### Step 1 — Convert the PDF

1. Go to [paper2md.com](https://paper2md.com) and sign up (free, no credit card)
2. Upload your arXiv PDF
3. Wait for conversion (~10–30 seconds for a typical 12-page paper)
4. Click **Download ZIP** (includes `document.md` + `images/` folder)

### Step 2 — Import into Obsidian

```
YourVault/
└── Papers/
    └── ResNet-2015/
        ├── document.md      ← rename to "ResNet - Deep Residual Learning.md"
        └── images/
            ├── img-0.jpeg
            ├── img-1.jpeg
            └── ...
```

1. Extract the ZIP
2. Copy the entire folder into your Obsidian vault's `Papers/` directory
3. Open the `.md` file in Obsidian — formulas render immediately ✨

### Step 3 — Add Metadata (Optional)

Add YAML frontmatter at the top of the note:

```yaml
---
title: "Deep Residual Learning for Image Recognition"
authors: "He, Zhang, Ren, Sun"
year: 2015
arxiv: "1512.03385"
tags: [paper, deep-learning, computer-vision]
---
```

### Result

- All `$$...$$` formulas render as proper equations
- Tables display with correct alignment
- Figures show inline with `![caption](images/img-0.jpeg)` syntax
- Ready for `[[wiki-linking]]` to concept notes

---

## Method 2: Using Marker (Local, Open Source)

**Best for:** Privacy-conscious users or those converting many papers offline.

### Step 1 — Install Marker

```bash
pip install marker-pdf
```

> **Note:** For best formula accuracy, install with LLM support and use `--use_llm` flag. Requires an OpenAI API key.

### Step 2 — Convert

```bash
marker_single arxiv-paper.pdf ./output/ --use_llm
```

This produces `output/arxiv-paper/arxiv-paper.md` + an `images/` folder.

### Step 3 — Fix Math Delimiters for Obsidian

Marker sometimes uses `\(...\)` for inline math instead of `$...$`. Obsidian requires `$...$`. Run this fix:

```python
import re

def fix_obsidian_math(text):
    # \(...\) → $...$
    text = re.sub(r'\\\((.+?)\\\)', r'$\1$', text)
    # \[...\] → $$...$$
    text = re.sub(r'\\\[(.+?)\\\]', r'$$\n\1\n$$', text, flags=re.DOTALL)
    return text

with open("paper.md", "r") as f:
    content = f.read()

with open("paper_fixed.md", "w") as f:
    f.write(fix_obsidian_math(content))
```

### Step 4 — Import into Obsidian

Same as Method 1, Step 2.

---

## Pro Tips

### Tip 1: Combine with Zotero
Install the [Zotero Integration](https://github.com/mgmeyers/obsidian-zotero-integration) plugin for Obsidian. Use paper2md or Marker for the content, Zotero for citation metadata. Link them via the arXiv ID.

### Tip 2: Bulk Conversion with Marker
```bash
# Convert an entire folder of PDFs
marker /path/to/pdfs/ /path/to/output/ --workers 4 --use_llm
```

### Tip 3: Use the Obsidian "Reading View"
Switch to Reading View (`Ctrl+E`) to see formulas and tables rendered. Edit view shows the raw Markdown.

### Tip 4: Set Up a Template
Create an Obsidian template for paper notes so new imports automatically get the right frontmatter and structure.

---

## Related Workflows

- [Building a RAG pipeline from academic papers](../examples/pdf-to-rag-pipeline.md) *(coming soon)*
- [Batch converting a Zotero library](../examples/zotero-batch-export.md) *(coming soon)*
