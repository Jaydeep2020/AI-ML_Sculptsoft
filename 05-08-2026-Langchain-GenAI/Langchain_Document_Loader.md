# Document Loaders in LangChain

## What are Document Loaders?

Document Loaders are used to **load data from different sources** (files, websites, databases, cloud storage, etc.) into LangChain.

They convert raw data into **Document** objects, which can then be split, embedded, stored in a vector database, or passed to an LLM.

---

# Why use Document Loaders?

- Load data from multiple file formats
- Standard interface for all data sources
- Automatically creates `Document` objects
- Preserves metadata (source, page number, etc.)
- First step in most RAG pipelines

---

# Document Object

Every loader returns one or more `Document` objects.

```python
Document(
    page_content="Actual text...",
    metadata={
        "source": "file.pdf",
        "page": 0
    }
)
```

- **page_content** → Text extracted from the source
- **metadata** → Additional information about the document

---

# Basic Workflow

```text
File / Website / Database
            │
            ▼
    Document Loader
            │
            ▼
   List of Documents
            │
            ▼
    Text Splitter
            │
            ▼
      Embeddings
            │
            ▼
     Vector Database
            │
            ▼
         Retriever
            │
            ▼
            LLM
```

---

# Common Methods

## load()

Loads all documents into memory.

```python
docs = loader.load()
```

Returns:

```python
List[Document]
```

---

## lazy_load()

Loads one document at a time.

```python
for doc in loader.lazy_load():
    print(doc.page_content)
```

Useful for very large datasets.

---

# Common Document Loaders

## 1. TextLoader

Loads plain text (`.txt`) files.

```python
from langchain_community.document_loaders import TextLoader

loader = TextLoader("notes.txt")
docs = loader.load()
```

Use when working with:
- Notes
- Logs
- Plain text documents

---

## 2. PyPDFLoader

Loads PDF documents.

```python
from langchain_community.document_loaders import PyPDFLoader

loader = PyPDFLoader("book.pdf")
docs = loader.load()
```

Each page becomes a separate `Document`.

Metadata example:

```python
{
    "source": "book.pdf",
    "page": 5
}
```

---

## 3. PyMuPDFLoader

Fast PDF loader.

```python
from langchain_community.document_loaders import PyMuPDFLoader

loader = PyMuPDFLoader("report.pdf")
docs = loader.load()
```

Advantages:
- Faster than many PDF loaders
- Better text extraction
- Good for large PDFs

---

## 4. CSVLoader

Loads CSV files.

```python
from langchain_community.document_loaders import CSVLoader

loader = CSVLoader("employees.csv")
docs = loader.load()
```

Each row becomes one `Document`.

---

## 5. JSONLoader

Loads JSON files.

```python
from langchain_community.document_loaders import JSONLoader

loader = JSONLoader(
    file_path="data.json",
    jq_schema=".[]",
    text_content=False
)

docs = loader.load()
```

Useful for structured JSON data.

---

## 6. DirectoryLoader

Loads multiple files from a directory.

```python
from langchain_community.document_loaders import DirectoryLoader

loader = DirectoryLoader(
    "documents/",
    glob="**/*.txt"
)

docs = loader.load()
```

Can also use other loaders:

```python
loader = DirectoryLoader(
    "pdfs/",
    glob="**/*.pdf",
    loader_cls=PyPDFLoader
)
```

---

## 7. WebBaseLoader

Loads content from websites.

```python
from langchain_community.document_loaders import WebBaseLoader

loader = WebBaseLoader(
    "https://example.com"
)

docs = loader.load()
```

Useful for:
- News articles
- Documentation
- Blogs

---

## 8. BSHTMLLoader

Loads local HTML files.

```python
from langchain_community.document_loaders import BSHTMLLoader

loader = BSHTMLLoader("page.html")
docs = loader.load()
```

---

## 9. UnstructuredFileLoader

Supports many document formats.

```python
from langchain_community.document_loaders import UnstructuredFileLoader

loader = UnstructuredFileLoader("presentation.pptx")
docs = loader.load()
```

Supports:
- PDF
- DOCX
- PPTX
- HTML
- TXT
- Markdown
- Images (OCR)

---

## 10. NotionDirectoryLoader

Loads exported Notion workspaces.

```python
from langchain_community.document_loaders import NotionDirectoryLoader

loader = NotionDirectoryLoader("NotionExport/")
docs = loader.load()
```

---

## 11. GithubFileLoader

Loads files from a GitHub repository.

```python
from langchain_community.document_loaders import GithubFileLoader

loader = GithubFileLoader(
    repo="owner/repo",
    access_token="YOUR_GITHUB_TOKEN"
)

docs = loader.load()
```

Useful for:
- Source code
- Documentation
- README files

---

## 12. UnstructuredMarkdownLoader

Loads Markdown (`.md`) files.

```python
from langchain_community.document_loaders import UnstructuredMarkdownLoader

loader = UnstructuredMarkdownLoader("notes.md")
docs = loader.load()
```

---

# Choosing the Right Loader

| Data Source | Recommended Loader |
|-------------|--------------------|
| TXT | TextLoader |
| PDF | PyPDFLoader |
| Large PDF | PyMuPDFLoader |
| CSV | CSVLoader |
| JSON | JSONLoader |
| Folder | DirectoryLoader |
| Website | WebBaseLoader |
| HTML | BSHTMLLoader |
| DOCX / PPTX | UnstructuredFileLoader |
| Markdown | UnstructuredMarkdownLoader |
| Notion Export | NotionDirectoryLoader |
| GitHub Repository | GithubFileLoader |

---

# Advantages

- Supports many data sources
- Standard API (`load()`)
- Automatically generates `Document` objects
- Preserves metadata
- Easy integration with RAG pipelines

---

# Limitations

- Some loaders require extra packages
- PDF extraction quality depends on document structure
- Scanned PDFs may require OCR
- Loading very large files with `load()` can consume significant memory

---

# Quick Revision

| Loader | Purpose |
|--------|---------|
| TextLoader | Load `.txt` files |
| PyPDFLoader | Load PDF files |
| PyMuPDFLoader | Fast PDF parsing |
| CSVLoader | Load CSV files |
| JSONLoader | Load JSON files |
| DirectoryLoader | Load multiple files from a folder |
| WebBaseLoader | Load web pages |
| BSHTMLLoader | Load local HTML files |
| UnstructuredFileLoader | Load multiple document formats |
| NotionDirectoryLoader | Load Notion exports |
| GithubFileLoader | Load GitHub repository files |
| UnstructuredMarkdownLoader | Load Markdown files |

---

# RAG Pipeline with Document Loaders

```text
Documents
(PDF, TXT, CSV, Website)
        │
        ▼
Document Loader
        │
        ▼
Document Objects
        │
        ▼
Text Splitter
        │
        ▼
Embeddings
        │
        ▼
Vector Database
        │
        ▼
Retriever
        │
        ▼
LLM
        │
        ▼
Answer
```