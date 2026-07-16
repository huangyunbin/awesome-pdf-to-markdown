# Workflow: PDF Academic Papers → RAG Pipeline

A practical guide to building a **Retrieval-Augmented Generation (RAG)** pipeline that ingests academic PDF papers as clean, LLM-friendly Markdown.

---

## Why Convert PDFs to Markdown First?

LLMs perform significantly better on structured Markdown than on raw PDF text extraction:

| Input Format | Chunking Quality | Formula Handling | Table Handling |
|---|---|---|---|
| Raw PDF text | Poor (garbled layout) | None | None |
| PDF → naive text | Medium | None | None |
| PDF → **clean Markdown** | **Excellent** | LaTeX preserved | GFM tables |

With clean Markdown:
- Headers (`#`, `##`) enable **semantic chunking** by section
- LaTeX formulas are preserved so the LLM can reason about them
- Tables are structured data the LLM can extract from

---

## Architecture

```
arXiv PDFs
    │
    ▼
┌──────────────────┐
│   PDF → Markdown │  ← paper2md / Marker / MinerU
│    Conversion    │
└──────────────────┘
    │
    ▼  document.md (clean Markdown)
┌──────────────────┐
│  Semantic        │  ← LangChain / LlamaIndex
│  Chunking        │     chunk by heading or fixed-size
└──────────────────┘
    │
    ▼  chunks[]
┌──────────────────┐
│  Embedding       │  ← OpenAI text-embedding-3-small
│  Generation      │     or sentence-transformers
└──────────────────┘
    │
    ▼  vectors[]
┌──────────────────┐
│  Vector Store    │  ← Chroma / Pinecone / Weaviate
└──────────────────┘
    │
    ▼
  Query → Retrieve → LLM Answer
```

---

## Step-by-Step Implementation

### Step 1 — Convert PDFs to Markdown

**Option A: Web-based (paper2md) — best quality, no setup**

```python
# If using paper2md API (coming soon), or use the web UI:
# https://paper2md.com → Upload → Download .md
```

**Option B: Marker — local batch processing**

```bash
pip install marker-pdf

# Single paper
marker_single paper.pdf ./output/ --use_llm

# Batch (folder of PDFs)
marker ./papers/ ./output/ --workers 4
```

### Step 2 — Load and Chunk the Markdown

Using **LlamaIndex** (recommended for research paper RAG):

```python
from llama_index.core import SimpleDirectoryReader, VectorStoreIndex
from llama_index.core.node_parser import MarkdownNodeParser

# Load converted Markdown files
documents = SimpleDirectoryReader(
    input_dir="./output/",
    required_exts=[".md"]
).load_data()

# Parse with Markdown-aware chunker (respects headers)
parser = MarkdownNodeParser()
nodes = parser.get_nodes_from_documents(documents)

# Build index
index = VectorStoreIndex(nodes)
```

Using **LangChain**:

```python
from langchain.document_loaders import DirectoryLoader, UnstructuredMarkdownLoader
from langchain.text_splitter import MarkdownHeaderTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_community.vectorstores import Chroma

# Load Markdown files
loader = DirectoryLoader("./output/", glob="**/*.md", 
                          loader_cls=UnstructuredMarkdownLoader)
docs = loader.load()

# Split by Markdown headers (preserves section context)
splitter = MarkdownHeaderTextSplitter(
    headers_to_split_on=[
        ("#", "title"),
        ("##", "section"),
        ("###", "subsection"),
    ]
)
chunks = []
for doc in docs:
    chunks.extend(splitter.split_text(doc.page_content))

# Embed and store
vectorstore = Chroma.from_documents(
    chunks,
    OpenAIEmbeddings(model="text-embedding-3-small")
)
```

### Step 3 — Query

```python
# LlamaIndex
query_engine = index.as_query_engine()
response = query_engine.query(
    "What is the skip connection formula in ResNet?"
)
print(response)

# LangChain
retriever = vectorstore.as_retriever(search_kwargs={"k": 5})
relevant_chunks = retriever.get_relevant_documents(
    "What is the skip connection formula in ResNet?"
)
```

---

## Tips for Academic Paper RAG

### Tip 1: Chunk by Section, Not Fixed Size
Academic papers have meaningful section structure (Introduction, Methods, Results, Discussion). Use header-aware chunking to preserve this context.

### Tip 2: Include LaTeX in Embeddings
Don't strip LaTeX formulas before embedding. The formula text itself carries semantic meaning. `\frac{\partial L}{\partial w}` is meaningfully different from just "gradient".

### Tip 3: Add Paper Metadata to Each Chunk
```python
# Add metadata to preserve paper identity
for chunk in chunks:
    chunk.metadata.update({
        "paper_title": "Deep Residual Learning",
        "arxiv_id": "1512.03385",
        "authors": "He, Zhang, Ren, Sun",
        "year": 2015
    })
```

### Tip 4: Use a Higher `k` for Literature Review Tasks
When querying across many papers for a literature review, set `k=10` or higher to surface more relevant chunks.

---

## Tool Recommendations by Scale

| Scale | Recommended Conversion | Chunking | Vector Store |
|---|---|---|---|
| < 50 papers | [paper2md](https://paper2md.com) (web) | LlamaIndex MarkdownNodeParser | Chroma (local) |
| 50–500 papers | Marker (local batch) | LangChain MarkdownHeaderSplitter | Chroma / Pinecone |
| 500+ papers | MinerU (GPU batch) | Custom chunker | Pinecone / Weaviate |

---

*Related: [arXiv → Obsidian Workflow](arxiv-to-obsidian.md)*
