# LLM-Powered Document Search API

This project provides a backend API for uploading documents (PDF, DOCX, TXT) and performing semantic search on their content using LLM embeddings. It allows users to find relevant information within their documents based on meaning, not just keyword matching, and can optionally generate a synthesized answer using a local LLM.

## Features

*   **Document Upload:** Supports PDF, DOCX, and TXT file uploads.
*   **Text Extraction:** Automatically extracts text content from uploaded documents.
*   **Text Chunking:** Splits extracted text into manageable chunks.
*   **Embedding Generation:** Uses Sentence Transformers (`all-MiniLM-L6-v2` by default) to create vector embeddings for text chunks.
*   **Vector Storage:** Stores embeddings and chunks in ChromaDB for efficient similarity search.
*   **Semantic Search:** Allows querying the document content based on semantic similarity.
*   **LLM-Powered Answers (RAG):** Uses a local GGUF LLM (e.g., Phi-3-mini, TinyLlama) via `llama-cpp-python` to generate natural language answers based on retrieved context.
*   **Configurable:** Settings managed via a `.env` file.
*   **Structured Logging:** For better monitoring and debugging.
*   **FastAPI Backend:** Built with FastAPI for high performance and automatic API documentation (Swagger UI & ReDoc).

## Project Structure

```bash

llm_doc_search_project/
├── CHANGELOG.md
├── chroma_data
│   ├── 4b98565f-4033-46e9-a1a8-0e0f90fab849
│   └── chroma.sqlite3
├── env.example
├── llm_doc_search_api
│   ├── chunking.py
│   ├── config.py
│   ├── database.py
│   ├── embedding_generator.py
│   ├── __init__.py
│   ├── llm_handler.py
│   ├── main.py
│   ├── models.py
│   ├── __pycache__
│   ├── routes.py
│   ├── text_extractor.py
│   └── vector_store.py
├── llm_models
│   └── Phi-3-mini-4k-instruct-Q4_K_M.gguf
├── pyproject.toml
├── README.md
└── requirements.txt

```

## Prerequisites

*   Python 3.11+
*   Build essentials (for compiling `llama-cpp-python`):
    *   On Debian/Ubuntu: `sudo apt install build-essential`
    *   On Fedora: `sudo dnf groupinstall "Development Tools"`
*   (Optional) Git for cloning if sourced from a repository.

## Setup and Installation

1.  **Clone the Repository (if applicable):**
    ```bash
    git clone https://github.com/harshad208/llm_doc_search_project.git
    cd llm_doc_search_project
    ```

2.  **Create and Activate Virtual Environment:**
    ```bash
    python3.11 -m venv venv
    source venv/bin/activate
    ```

3.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **Set up Configuration:**
    *   Copy the example environment file (we'll create this next):
        ```bash
        cp env.example .env
        ```
    *   Edit the `.env` file to configure paths and settings, especially `LLM_MODEL_PATH`.

5.  **Download an LLM Model:**
    *   Create the `llm_models/` directory if it doesn't exist:
        ```bash
        mkdir -p llm_models
        ```
    *   Download a GGUF-formatted LLM (e.g., Phi-3-mini or TinyLlama) and place it in the `llm_models/` directory. For example, to download `Phi-3-mini-4k-instruct-Q4_K_M.gguf`:
        ```bash
        wget -O llm_models/Phi-3-mini-4k-instruct-Q4_K_M.gguf "https://huggingface.co/bartowski/Phi-3-mini-4k-instruct-GGUF/resolve/main/Phi-3-mini-4k-instruct-Q4_K_M.gguf"
        ```
    *   Ensure the `LLM_MODEL_PATH` in your `.env` file points to this downloaded model.

## Running the Application

Once setup is complete, run the FastAPI application:
```bash
python -m llm_doc_search_api.main
```


- The API will be accessible at http://localhost:8000 (or your configured host/port).
- Interactive API documentation (Swagger UI) will be at http://localhost:8000/docs.

- API Endpoints
- GET /: Health check.
- POST /upload: Upload a document (PDF, DOCX, TXT).
- File: The document file.
- POST /search: Perform semantic search and get an LLM-generated answer.
Request Body:
```
{
  "query": "Your question about the documents",
  "doc_id": "optional_document_id_to_search_within",
  "top_n": 3
}
```
- GET /docs: List metadata of all uploaded documents.
- GET /docs/{doc_id}: Get metadata for a specific document.
- GET /docs/{doc_id}/text: Get the full extracted text of a specific document.
- DELETE /docs/{doc_id}: Delete a document, its file, and its data from the vector store.