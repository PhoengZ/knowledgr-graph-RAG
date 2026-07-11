# Knowledge Graph for RAG (Deeplearning.AI)

This project contains practical lessons and exercises from the **Knowledge Graphs for RAG** course by Deeplearning.AI. The main objective of this project is to learn about graph databases and how to leverage them in a Retrieval-Augmented Generation (RAG) pipeline to improve retrieval efficiency, maintain semantic context, and reduce LLM query costs.

---

## 🗺️ Component & Directory Mapping

Here is the structure of the project repository:

### 1. Jupyter Notebooks
* **Directory:** `.` (Root)
* **Purpose:** Contains interactive python notebooks demonstrating knowledge graph queries, indexing, text chunking, metadata propagation, and RAG workflows.
* **Key Files:**
  - `L2-query_with_cypher.ipynb`: Demonstrates how to connect to a Neo4j database using LangChain, query the movie knowledge graph with the Cypher query language (filtering, relationship matching, aggregation), and modify database records (creating, merging, and deleting nodes/relationships).
  - `L3-prep_text_for_RAG.ipynb`: Demonstrates how to create a vector index on Neo4j node properties, populate it by calculating vector embeddings via OpenAI API integration (using Cypher's `genai.vector.encode`), and run similarity search queries against the index.
  - `L4-construct_kg_from_text.ipynb`: Parses SEC Form 10-K JSON filings, chunks text sections (Item 1, 1a, 7, 7a) using LangChain's `RecursiveCharacterTextSplitter` with metadata (cik, cusip, source), inserts them into Neo4j as `:Chunk` nodes, indexes them, and builds a complete RAG question-answering workflow (`RetrievalQAWithSourcesChain`) with `ChatOpenAI`.

---

## 🏗️ System Architecture

The following diagram illustrates how the Python runtime, LangChain RAG pipeline, the Neo4j Graph Database, and OpenAI APIs communicate:

```mermaid
graph TD
    %% Define Node Styles
    classDef component fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff;
    classDef database fill:#1e3a8a,stroke:#3b82f6,stroke-width:2px,color:#fff;
    classDef config fill:#111827,stroke:#10b981,stroke-width:2px,color:#fff;
    classDef api fill:#4b5563,stroke:#10b981,stroke-width:2px,color:#fff;
    
    %% Flow nodes
    Notebook["Jupyter Notebook (L2, L3 & L4)"] -->|Loads credentials| Env[".env Config"]
    Notebook -->|Splits & Prepares Docs| TextChunks["SEC 10-K Text Chunks"]
    TextChunks -->|Inserts Chunks & Metadata| DB[("Neo4j Database Instance")]
    
    Notebook -->|Instantiates| LangChain["LangChain Neo4jVector / QA Chain"]
    LangChain -->|Retrieves context from| DB
    LangChain -->|Generates Answer| OpenAI_LLM["OpenAI Chat LLM (GPT-3.5/4)"]
    DB -->|Encodes text chunks via Cypher| OpenAI_Embeddings["OpenAI Embeddings API"]
    
    %% Apply styles
    class Notebook,LangChain component;
    class DB database;
    class Env,TextChunks config;
    class OpenAI_LLM,OpenAI_Embeddings api;
```

---

## 🚀 Getting Started & Installation

Follow these steps to set up and run this project locally.

### Prerequisites
- Python >= 3.9
- Neo4j Database (e.g., a local Neo4j Desktop instance, Docker container, or Neo4j AuraDB instance)
- OpenAI API Key (required for vector embedding encoding and LLM generation)
- Jupyter Notebook / JupyterLab or VS Code with Jupyter extension

### Installation Steps

1. **Clone the repository:**
   ```bash
   git clone <repo_url>
   cd Knowledge_graph_for_Rag
   ```

2. **Create and activate a virtual environment:**
   ```bash
   # Create environment
   python -m venv venv

   # Activate environment (Windows)
   .\venv\Scripts\activate

   # Activate environment (macOS/Linux)
   source venv/bin/activate
   ```

3. **Install dependencies:**
   ```bash
   pip install langchain-community langchain-openai neo4j python-dotenv ipykernel
   ```

4. **Configure Environment Variables:**
   Create a `.env` file in the root directory:
   ```bash
   # Create the file (Windows PowerShell)
   New-Item .env -ItemType File
   
   # Or copy an example
   # cp .env.example .env
   ```
   Open the `.env` file and configure it as shown in the section below.

5. **Start Jupyter and run the notebook:**
   ```bash
   jupyter notebook
   ```
   Open the notebooks and execute the cells.

---

## ⚙️ Environmental Configuration (.env)

The application requires the following environment variables to connect to your Neo4j instance and OpenAI. Add these to your `.env` file:

```env
# ==========================================
# Neo4j Database Configurations
# ==========================================
# [Required] Connection URI for the Neo4j instance (e.g., neo4j+s:// or bolt://)
NEO4J_URI=bolt://localhost:7687

# [Required] Username for database authentication
NEO4J_USERNAME=neo4j

# [Required] Password for database authentication
NEO4J_PASSWORD=your_neo4j_password

# [Optional] Target database name (defaults to 'neo4j')
NEO4J_DATABASE=neo4j

# ==========================================
# OpenAI Configurations
# ==========================================
# [Required] API Key for OpenAI to run embeddings and chat models
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxxxxxx

# [Required] Base API URL (e.g., if using custom base endpoints)
OPENAI_BASE_URL=https://api.openai.com/v1
```