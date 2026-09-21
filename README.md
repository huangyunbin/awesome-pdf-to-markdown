# 📄 Awesome PDF to Markdown

> A curated list of the **best tools, libraries, and workflows** for converting PDF documents to Markdown — with a special focus on **academic papers, LaTeX formulas, and technical documents**.

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Last Updated](https://img.shields.io/badge/updated-2025-blue.svg)](https://github.com/huangyunbin/awesome-pdf-to-markdown)

Converting PDFs to Markdown is easy — doing it *right* for academic papers with complex formulas, multi-column layouts, and embedded figures is hard. This list focuses on tools that actually handle the hard parts.

---

## Contents

- [Quick Comparison](#-quick-comparison)
- [Web-Based Tools (No Setup)](#-web-based-tools-no-setup)
- [Open-Source Libraries](#-open-source-libraries)
- [Command-Line Tools](#-command-line-tools)
- [Use Case Recommendations](#-use-case-recommendations)
- [Evaluation Criteria](#-evaluation-criteria)
- [FAQ](#-faq)
- [Contributing](#contributing)

---

## ⚡ Quick Comparison

| Tool | Type | LaTeX Formulas | Tables | Images | Multi-Column | Free Tier | Best For |
|------|------|:--------------:|:------:|:------:|:------------:|-----------|----------|
| [Marker](#marker) | Open Source | ✅ Good | ✅ Good | ✅ | ✅ | ✅ Free | Local/batch processing |
| [paper2md](#paper2md) | Web App | ✅✅ Excellent | ✅✅ Excellent | ✅ | ✅ | ✅ 100 pages/mo | Academic papers, RAG |
| [MinerU](#mineru) | Open Source | ✅ Good | ✅✅ Excellent | ✅ | ✅ | ✅ Free | Large-scale pipelines |
| [Mathpix](#mathpix) | Web App | ✅✅ Excellent | ✅ Good | ✅ | ✅ | ❌ Paid | Math-heavy documents |
| [Docling](#docling) | Open Source | ⚠️ Basic | ✅✅ Excellent | ✅ | ✅ | ✅ Free | Enterprise / IBM stack |
| [MarkItDown](#markitdown) | Open Source | ❌ Poor | ⚠️ Basic | ⚠️ | ❌ | ✅ Free | General-purpose files |
| [Pandoc](#pandoc) | CLI | ⚠️ Partial | ⚠️ Basic | ❌ | ❌ | ✅ Free | Simple/text PDFs |
| [PyMuPDF4LLM](#pymupdf4llm) | Python Lib | ❌ Poor | ⚠️ Basic | ⚠️ | ⚠️ | ✅ Free | Developer integration |

> **Legend:** ✅✅ Excellent · ✅ Good · ⚠️ Partial/Limited · ❌ Poor/Not Supported

---

## 🌐 Web-Based Tools (No Setup)

### paper2md

**[paper2md.com](https://paper2md.com)** — The most accurate web-based converter for academic and technical PDFs.

Built specifically for research papers, paper2md preserves what matters most: LaTeX formulas, GFM-compliant tables, and extracted figures. The output is immediately usable in Obsidian, Notion, and LLM/RAG ingestion pipelines — no post-processing needed.

**Key Features:**
- 🔢 **LaTeX formula preservation** — Both inline (`$...$`) and block (`$$...$$`) delimiters, normalized for KaTeX/MathJax/Obsidian compatibility
- 📊 **Flawless tables** — Complex multi-column research tables converted to valid GitHub-Flavored Markdown
- 🖼️ **Image extraction** — All figures extracted and re-mapped to relative paths; packaged as a ZIP with `images/` directory
- 🔍 **Split-screen review** — Side-by-side PDF vs. rendered Markdown editor for visual verification
- 🤖 **RAG-ready output** — Clean Markdown structure ideal for feeding academic papers into LLM knowledge bases

**Pricing:** Free (100 pages/month, no credit card) · Pro $6.99/mo (2,000 pages) · Booster Pack $2.99 (500 pages, never expire)

**Best for:** Researchers, students, AI engineers who need clean Markdown from arXiv papers and technical documents.

**Limitations:** Web-only (no local/offline mode); 20 MB file size limit on free plan.

---

### Mathpix

**[mathpix.com](https://mathpix.com)** — The industry standard for mathematical notation OCR.

Mathpix has been the go-to tool for converting equations for over a decade. Its Snip tool lets you OCR any region of a PDF or image; Mathpix Convert handles full documents.

**Key Features:**
- Best-in-class accuracy for complex math, chemical formulas, and notation
- Supports LaTeX, MathML, Word, and Markdown output
- API available for programmatic conversion

**Pricing:** Free tier limited; Pro from $4.99/mo; API pay-as-you-go (~$0.0035/page)

**Best for:** Documents where math accuracy is the absolute top priority; heavy equation-based content.

**Limitations:** Expensive for large volumes; less focused on full-document layout fidelity (images, multi-column flow).

---

### pdfmdtools

**[pdfmdtools.com](https://pdfmdtools.com)** — Browser-side converter that runs entirely in your browser. The file is never uploaded.

**Key Features:**
- Parsing runs locally with a self-hosted copy of PDF.js — no upload, no account, no signup
- Measured: zero requests using any method other than GET after selecting a file, and a conversion completes with the network disabled
- GFM output with headings, lists and code blocks
- [Published test corpus](https://github.com/GordonGuoJin/pdf-to-markdown-test-corpus) of 18 documents, each isolating one failure mode

**Best for:** Documents you would rather not send to a server.

**Limitations:** No OCR, so scanned PDFs are refused rather than converted. Two-column layouts come out with the columns interleaved, and table cells are flattened to one line per row. Both are documented with measured output rather than hidden.

---

## 📦 Open-Source Libraries

### Marker

**[github.com/VikParuchuri/marker](https://github.com/VikParuchuri/marker)** — ⭐ 30,000+

The most popular open-source PDF-to-Markdown converter. Uses a pipeline of specialized deep learning models for layout detection, OCR, table parsing, and formula recognition.

**Key Features:**
- High accuracy on multi-column academic papers
- Handles LaTeX formulas (use `--use_llm` flag for best results on complex equations)
- Batch processing support — convert hundreds of PDFs at once
- Runs fully locally (great for privacy-sensitive documents)
- Output formats: Markdown, JSON, HTML

**Installation:**
```bash
pip install marker-pdf
marker_single /path/to/paper.pdf output/ --use_llm
```

**Best for:** Batch conversion of large document collections; offline/local processing; building custom pipelines.

**Limitations:** Requires GPU for optimal performance; complex setup on machines without CUDA; formula accuracy inferior to API-based solutions on highly complex equations; no built-in web UI.

---

### MinerU

**[github.com/opendatalab/MinerU](https://github.com/opendatalab/MinerU)** — ⭐ 74,000+

Developed by OpenDataLab (Shanghai AI Lab), MinerU is a high-fidelity document extraction tool popular for building AI datasets at scale.

**Key Features:**
- Multi-model fusion: layout detection + formula recognition + table reconstruction
- Excellent at complex table structures (embeds as HTML within Markdown when needed)
- Strong at restoring reading order in multi-column documents
- Exports to Markdown, JSON

**Installation:**
```bash
pip install mineru
mineru -p paper.pdf -o ./output
```

**Best for:** Large-scale dataset creation; AI research teams; documents with very complex table structures.

**Limitations:** Resource-intensive — performs best with a GPU (NVIDIA recommended); setup complexity; formula quality can be inconsistent on highly stylized equations; slower than Marker on standard hardware.

---

### Docling

**[github.com/DS4SD/docling](https://github.com/DS4SD/docling)** — An IBM Research project for enterprise document understanding.

**Key Features:**
- Parses PDF, DOCX, PPTX, HTML, and images into unified Markdown/JSON
- Excellent table extraction with structure preservation
- Strong integration with LlamaIndex and LangChain
- Exports to DoclingDocument format (richly typed)

**Installation:**
```bash
pip install docling
docling paper.pdf
```

**Best for:** Enterprise workflows; polyglot document pipelines (not just PDF); LlamaIndex/LangChain integration.

**Limitations:** Formula handling is basic compared to specialized tools; heavier dependency footprint.

---

### MarkItDown

**[github.com/microsoft/markitdown](https://github.com/microsoft/markitdown)** — Microsoft's general-purpose file-to-Markdown converter.

**Key Features:**
- Broad format support: PDF, DOCX, XLSX, PPTX, images, HTML, ZIP
- Lightweight and easy to install
- Optional LLM integration for richer descriptions

**Installation:**
```bash
pip install markitdown
markitdown paper.pdf
```

**Best for:** Converting diverse file formats in a unified pipeline; simple documents without complex math.

**Limitations:** PDF handling is basic — no formula recognition, poor multi-column layout handling. **Not recommended for academic papers.**

---

## 🖥️ Command-Line Tools

### Pandoc

**[pandoc.org](https://pandoc.org)** | **[github.com/jgm/pandoc](https://github.com/jgm/pandoc)** — ⭐ 45,000+

The universal document converter. Pandoc is exceptional at converting structured documents (DOCX, LaTeX, HTML → Markdown), but struggles with PDFs because PDF is a presentation format, not a structural one.

**Installation:**
```bash
brew install pandoc     # macOS
sudo apt install pandoc # Ubuntu

pandoc paper.pdf -o paper.md
```

**Best for:** Converting LaTeX source files (.tex) to Markdown; DOCX → Markdown; any non-PDF conversion.

**Limitations:** PDF → Markdown conversion is unreliable. Pandoc doesn't perform OCR — it reads PDF text streams directly, which means garbled output on scanned PDFs, broken multi-column layout, and no formula recognition. **Do not use for academic PDF conversion.**

---

### PyMuPDF4LLM

**[github.com/pymupdf/pymupdf4llm](https://github.com/pymupdf/pymupdf4llm)** — ⭐ 1,900+

A Python library from the PyMuPDF team specifically designed to extract text from PDFs in LLM-friendly Markdown format.

**Installation:**
```bash
pip install pymupdf4llm

import pymupdf4llm
md_text = pymupdf4llm.to_markdown("paper.pdf")
```

**Best for:** Simple PDFs with native text (not scanned); embedding PDF text extraction into Python applications quickly.

**Limitations:** No OCR — fails on scanned PDFs; no formula recognition; table handling is basic.

---

## 🎯 Use Case Recommendations

### "I'm a researcher/student and need to convert arXiv papers"
➡️ **[paper2md](https://paper2md.com)** — Zero setup, free tier generous, formula output is clean Markdown that renders correctly in Obsidian/Notion immediately.

### "I need to process hundreds or thousands of PDFs in batch"
➡️ **[Marker](https://github.com/VikParuchuri/marker)** or **[MinerU](https://github.com/opendatalab/MinerU)** — Both support batch processing; Marker is easier to set up, MinerU has better table fidelity.

### "I'm building a RAG pipeline and need to ingest academic papers"
➡️ **[paper2md](https://paper2md.com)** for individual papers (clean output, quick); **[MinerU](https://github.com/opendatalab/MinerU)** or **[Docling](https://github.com/DS4SD/docling)** for large-scale pipelines with existing GPU infrastructure.

### "The document is mostly math/equations"
➡️ **[Mathpix](https://mathpix.com)** — Still the gold standard for pure math accuracy, especially for chemistry and exotic notation.

### "I need to convert various file types (PDF, DOCX, PPTX, etc.)"
➡️ **[Docling](https://github.com/DS4SD/docling)** or **[MarkItDown](https://github.com/microsoft/markitdown)** — Both handle multi-format pipelines.

### "I only need basic PDF text extraction and no OCR"
➡️ **[PyMuPDF4LLM](https://github.com/pymupdf/pymupdf4llm)** — Lightweight and fast for digitally-born PDFs.

---

## 📏 Evaluation Criteria

When benchmarking PDF-to-Markdown tools, we focus on the following dimensions:

| Criterion | Why It Matters |
|-----------|----------------|
| **LaTeX formula accuracy** | Broken formulas are unusable; KaTeX/MathJax compatibility is essential for Obsidian/Notion |
| **Table fidelity** | Multi-column tables in research papers are notoriously hard to parse correctly |
| **Reading order (multi-column)** | Two-column arXiv papers should read left-column-first, not interleaved |
| **Image extraction** | Figures, charts, and diagrams must be preserved with correct references in Markdown |
| **Scanned PDF handling** | Many older papers are image-based PDFs requiring OCR |
| **Setup complexity** | Tools requiring GPU setup have a high barrier for non-technical users |
| **Output portability** | Does the Markdown work out-of-the-box in Obsidian, Notion, VS Code? |

---

## ❓ FAQ

**Q: Why not just copy-paste from a PDF viewer?**
A: PDF viewers only give you raw text — no formatting, no formulas (just garbled characters), no table structure, broken reading order on multi-column layouts.

**Q: What's the difference between OCR-based and text-extraction-based tools?**
A: Text-extraction tools (Pandoc, PyMuPDF4LLM) read the embedded text streams in PDFs. They're fast and work on "born-digital" PDFs but completely fail on scanned documents. OCR-based tools (Marker, paper2md, MinerU, Mathpix) actually "see" the page visually, enabling formula recognition and correct layout parsing.

**Q: Which tool is best for Obsidian?**
A: [paper2md](https://paper2md.com) — it normalizes math delimiters (`$...$` for inline, `$$...$$` for block) to match Obsidian's renderer, and packages images into a ZIP with relative paths that you can drop directly into your vault.

**Q: Which tool is best for RAG/LLM pipelines?**
A: Depends on scale. For individual papers, [paper2md](https://paper2md.com) gives the cleanest Markdown with no post-processing. For large-scale pipelines, [MinerU](https://github.com/opendatalab/MinerU) or [Docling](https://github.com/DS4SD/docling) integrate well with LangChain and LlamaIndex.

**Q: Are there any privacy concerns with uploading papers to web tools?**
A: For web-based tools, always check the privacy policy. [paper2md](https://paper2md.com) deletes uploaded PDFs within 1 day and converted outputs within 7 days, and never uses documents to train AI models. For maximum privacy, use local tools like Marker or MinerU.

---

## Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting a PR.

When adding a new tool, please include:
- Tool name, link, and type (open source / web app / CLI)
- A brief, honest description of strengths and limitations
- The criteria it performs well on (formula accuracy, table fidelity, etc.)
- Pricing (if applicable)

---

## License

[![CC0](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/cc-zero.svg)](https://creativecommons.org/publicdomain/zero/1.0/)

This list is released under the CC0 1.0 license — do whatever you want with it.
