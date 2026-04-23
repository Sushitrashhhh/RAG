# RAG Document Q&A Project

This repository implements an end-to-end Retrieval-Augmented Generation (RAG) workflow:

1. Load local documents.
2. Split them into chunks.
3. Convert chunks to embeddings.
4. Store vectors for similarity search.
5. Retrieve relevant context and generate answers with an LLM.

## What Is Included

- Multi-format document loading from `data/`:
  - PDF (`.pdf`)
  - TXT (`.txt`)
  - CSV (`.csv`)
  - Excel (`.xlsx`)
  - Word (`.docx`)
  - JSON (`.json`)
- Chunking with `RecursiveCharacterTextSplitter`.
- Embedding generation with `sentence-transformers` (`all-MiniLM-L6-v2`).
- Vector search with FAISS (module pipeline under `src/`).
- LLM answer generation through Groq (`langchain_groq`).
- Notebook experiments including ChromaDB-based variants.

## Repository Layout

```text
RAG/
├─ data/
│  ├─ pdf/
│  ├─ text_files/
│  └─ vector_store/
├─ notebook/
│  ├─ document.ipynb
│  └─ pdf_loader.ipynb
├─ src/
│  ├─ __init__.py
│  ├─ data_loader.py
│  ├─ embedding.py
│  ├─ vectorstore.py
│  └─ search.py
├─ Document Q&A with RAG.ipynb
├─ requirements.txt
└─ README.md
```

## Prerequisites

- Python 3.10 or newer
- Windows PowerShell (or equivalent shell)
- A Groq API key

## Setup

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install -r requirements.txt
```

Create a `.env` file in project root:

```env
GROQ_API_KEY=your_groq_api_key
```

## Quick Start

### Step 1: Add your documents

Put files into `data/` (for example `data/pdf/` and `data/text_files/`).

### Step 2: Build FAISS index

```powershell
python src\vectorstore.py
```

Expected result:
- Documents are loaded and chunked.
- Embeddings are generated.
- Index and metadata are saved to `faiss_store/`.

### Step 3: Ask a question

```powershell
python src\search.py
```

Expected result:
- Existing FAISS index is loaded (or created if missing).
- Top relevant chunks are retrieved.
- Groq LLM returns a context-grounded summary.

## Module Overview

- `src/data_loader.py`
  - Resolves data paths safely.
  - Loads supported document types into LangChain `Document` objects.
- `src/embedding.py`
  - Splits loaded docs into chunks.
  - Generates dense embeddings for each chunk.
- `src/vectorstore.py`
  - Builds, saves, loads, and queries a FAISS index.
- `src/search.py`
  - Connects vector retrieval with Groq LLM summarization.

## Notebook Workflow

Use notebooks for experimentation and step-by-step debugging:

- `notebook/document.ipynb` for ingestion/loader checks.
- `notebook/pdf_loader.ipynb` for full PDF-to-RAG experimentation.

Note:
- `src/` currently uses FAISS.
- Some notebook cells use ChromaDB.
- These are separate storage flows.

## Troubleshooting

- `GROQ_API_KEY not found`
  - Confirm `.env` exists in project root and contains a valid key.
- Loader errors for specific formats
  - Verify optional parser dependencies are installed.
- Empty or weak retrieval results
  - Check documents actually loaded.
  - Increase `top_k`.
  - Lower score threshold.
  - Tune chunk size and overlap.

## Suggested Next Improvements

- Add tests for loaders, chunking, and retrieval quality.
- Unify vector database choice across notebooks and modules.
- Add metadata-aware filtering (`source`, `page`, `type`).
- Add CLI or lightweight web app (for example, Streamlit) for interactive Q&A.
