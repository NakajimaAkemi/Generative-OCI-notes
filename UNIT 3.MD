# 🤖 OCI Generative AI Integration

---

## 🔗 LangChain Integration

**LangChain** is a framework designed to simplify the creation of **LLM-powered applications** such as chatbots, RAG systems, and intelligent agents.
It enables seamless integration between **large language models (LLMs)**, **data sources**, and **application logic**.

![LangChain](/assets/images/immagine_2025-10-16_164313861.png)

---

### 🧩 Core Components

LangChain applications are composed of modular elements that can be connected into “chains” to create complex workflows:

1. **Model**
   The computational component that processes text — either a **Text Completion Model (LLM)** or a **Chat Model** designed for dialogue.

2. **Prompt**
   Defines how inputs are structured and passed to the model.

   * `PromptTemplate`: simple string-based prompt template.
   * `ChatPromptTemplate`: structured list of chat messages.

3. **Chain**
   Connects multiple components (e.g., model + prompt + memory).

   * **LCEL (LangChain Expression Language)** — declarative syntax for chaining.
   * **Legacy Python classes** — for traditional object-oriented composition.

> 💡 **Q&A:**
> 🟦 **Q:** What is LCEL in LangChain?
> 💬 **A:** **LCEL (LangChain Expression Language)** provides a **declarative** way to connect LLM apps with tools and logic — it’s the **modern alternative** to writing procedural chains in Python.

4. **Memory**
   Maintains conversational context between turns.

   * **Before execution:** Reads past interactions to provide context.
   * **After execution:** Writes the latest exchanges back to memory.

5. **Document Loaders**
   Import and preprocess external data for embedding or retrieval purposes.

![Template completion](/assets/images/immagine_2025-10-16_164835457.png)

---

### Memory

In the LangChain framework, memory serves as a dynamic repository for retaining and managing information throughout the system's operation. It allows the framework to maintain state and context, enabling chains to access, reference, and utilize past interactions and information in their decision-making processes.

> 💡 **Q&A:**
> 🟦 **Q:** Does OCI Generative AI store chat history or retrieved context for training?
> 💬 **A:** No. **OCI Generative AI does not preserve queries or retrieved context after a session** and **does not use them for training**, ensuring privacy and compliance.

---

### 📄 Document Loaders and Chunking

Document loaders extract content from sources such as **PDF, HTML, CSV, JSON, or Markdown** files into plain text.
After loading, documents are **split into smaller chunks**, a critical step that affects retrieval precision and model performance.

| Chunk Property    | Description                                                       |
| ----------------- | ----------------------------------------------------------------- |
| **Chunk Size**    | Must align with the LLM’s context window.                         |
| **Chunk Overlap** | Preserves semantic continuity across chunks.                      |
| **Splitter Type** | Defines how splits occur — by paragraph, sentence, or characters. |

LangChain provides built-in utilities such as **`RecursiveCharacterTextSplitter`**.

> 💡 **Q&A:**
> 🟦 **Q:** What does multimodal parsing enable when used on data sources?
> 💬 **A:** **Multimodal parsing** allows the AI to interpret **charts, graphs, and embedded visual data** in documents — not just text.

---

### 💻 Example: Loading and Splitting a PDF

```python
from langchain.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter

# Load the PDF document
pdf_loader = PyPDFLoader("document_path.pdf")
text = ""
for page in pdf_loader.load():
    text += page.page_content

# Split text into manageable chunks
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=2000,
    chunk_overlap=100
)
chunks = text_splitter.split_text(text)
```

---

## 🧠 Integration with OCI Generative AI & Oracle 23AI

LangChain includes integrations for **Oracle Generative AI** and **Oracle Database 23AI**, allowing use of OCI’s enterprise AI directly in LangChain workflows.

Developers can:

* Use OCI **chat** and **embedding** models (Cohere / Meta) via REST APIs.
* Store and query embeddings using **Oracle AI Vector Search**.
* Generate SQL queries via **Oracle 23AI Select AI**.

> 💡 **Q&A:**
> 🟦 **Q:** What are the supported **knowledge base data sources**?
> 💬 **A:**
>
> * **OCI Search** (OpenSearch)
> * **OCI Object Storage** (text/PDFs)
> * **Oracle 23AI Vector Search**
>   ❌ **Custom-built file systems** are *not* supported.

> 💡 **Q&A:**
> 🟦 **Q:** What happens if a data source is deleted?
> 💬 **A:** When deleted, **the agent no longer answers questions related to that source**, as its indexed embeddings and context are removed.

---

### 🧱 Vector Data Type in Oracle 23AI

Oracle Database 23AI introduces a **native vector data type**, storing embeddings as **BLOB-like vectors** in tables.

> 💡 **Q&A:**
> 🟦 **Q:** What column type is used to store embeddings?
> 💬 **A:** Embeddings are stored in a **VECTOR** column type.

A typical workflow:

1. Connect to the database
2. Create document chunks
3. Convert to `Document` objects
4. Generate embeddings via `OCIGenAIEmbeddings`
5. Create a vector store with `OracleVS.from_documents(...)`

---

### 📐 Distance Measures

When comparing embeddings:

1. **Dot Product** – measures projection magnitude.
2. **Cosine Similarity** – measures angular distance.
   *Smaller angles → higher similarity.*

---

### 🧭 Vector Indexing

Vector indexes speed up retrieval using:

* **HNSW (Hierarchical Navigable Small World graphs)**
* **IVF (Inverted File Indexes)**

> 💡 **Q&A:**
> 🟦 **Q:** Which database ports are used by Oracle DB for vector search and connectivity?
> 💬 **A:** Oracle DB typically uses **ports 1521–1522**.

---

## 💬 Conversational RAG with Oracle Database 23AI

This combines **OCI Generative AI** with **Oracle Database 23AI**, allowing agents to answer using **structured** (SQL) and **unstructured** (document) data, maintaining conversation context.

---

### 🏗️ Step 1 — Create the Autonomous Database

Provision an **Autonomous Database** via the OCI Console, then configure access credentials and wallet files.

---

### ⚙️ Step 2 — Connect via Python

```python
import oracledb

connection = oracledb.connect(
    user="admin",
    password="your_password",
    dsn="your_tns_entry_from_wallet"
)
print("✅ Connected to Oracle 23AI Database")
```

> 💡 **Q&A:**
> 🟦 **Q:** What port range must be open for Oracle 23AI client connections?
> 💬 **A:** Ports **1521–1522**.

---

### 🧠 Step 3 — Configure OCI Generative AI Models

Set up a **Chat model** and an **Embedding model** via the **OCI Generative AI Dashboard**.
Models can be accessed through REST or LangChain (`ChatOCIGenAI`, `OCIGenAIEmbeddings`).

> 💡 **Q&A:**
> 🟦 **Q:** What is an endpoint in OCI Generative AI?
> 💬 **A:** The **endpoint** is the **URL** where requests to OCI’s AI services are sent.

> 💡 **Q&A:**
> 🟦 **Q:** How can we provision models for different usage modes?
> 💬 **A:** You can specify **on-demand** (pay-per-use) or **continuous** (persistent) deployments for generative models.

> 💡 **Q&A:**
> 🟦 **Q:** What happens if a model is deprecated?
> 💬 **A:** Teams must **plan to switch to a newer model**, as deprecated ones are **eventually retired** from the service.

---

### 📄 Step 4 — Ingest and Embed Documents

(Example code retained — omitted here for brevity.)

---

### 💬 Step 5 — Build the Conversational RAG Chain

(Example code retained — omitted here for brevity.)

> 💡 **Q&A:**
> 🟦 **Q:** How is billing calculated for a chatbot interaction?
> 💬 **A:** OCI bills by **character count** —
> `#prompt characters + #response characters = total billed units`.

---

### 🔄 Step 6 — End-to-End Workflow Overview

```mermaid
flowchart LR
    A[Upload PDF] --> B[Extract Text]
    B --> C[Chunk Text]
    C --> D[Generate Embeddings via OCI Model]
    D --> E[Store in Oracle Vector Search]
    E --> F[Retrieve Relevant Chunks]
    F --> G[Chat Model OCI Generative AI]
    G --> H[Answer with Context]
    H --> I[Store Conversation in Memory]
    I --> A
```

---

### ✅ Summary

With this pipeline, you can build **conversational, context-aware AI systems** that:

* Ingest & embed **PDFs/unstructured data**,
* Store embeddings in **Oracle Vector Search**,
* Generate **context-grounded answers** with OCI models, and
* Maintain continuity via **LangChain Memory**.

---

## ✅ Integrated Q&A Recap

| Concept                | Answer                                               |
| ---------------------- | ---------------------------------------------------- |
| Accuracy model         | Measures how many predictions are correct out of all |
| Billing                | Based on `#prompt chars + #response chars`           |
| Model deprecation      | Requires migration to updated models                 |
| Multimodal parsing     | Enables charts & graphs understanding                |
| Data source deletion   | Removes agent access to that knowledge               |
| Endpoint               | URL for OCI AI service                               |
| Model deployment       | On-demand or continuous                              |
| Knowledge base sources | OCI Search, Object Storage, 23AI Vector Search       |
| Embedding column       | VECTOR type                                          |
| Oracle DB ports        | 1521–1522                                            |
| Privacy                | No session or query data preserved for training      |
| LCEL                   | Declarative syntax connecting LLMs & tools           |

