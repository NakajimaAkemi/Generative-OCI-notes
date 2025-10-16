# 🤖 OCI Generative AI Integration

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

### 📄 Document Loaders and Chunking

Document loaders extract content from sources such as **PDF, HTML, CSV, JSON, or Markdown** files into plain text.
After loading, documents are **split into smaller chunks**, a critical step that affects retrieval precision and model performance.

| Chunk Property    | Description                                                       |
| ----------------- | ----------------------------------------------------------------- |
| **Chunk Size**    | Must align with the LLM’s context window.                         |
| **Chunk Overlap** | Preserves semantic continuity across chunks.                      |
| **Splitter Type** | Defines how splits occur — by paragraph, sentence, or characters. |

LangChain provides built-in utilities such as **`RecursiveCharacterTextSplitter`**.

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

LangChain includes community integrations for **Oracle Generative AI** and **Oracle Database 23AI**, allowing you to use OCI’s enterprise AI capabilities directly within LangChain workflows.

These integrations enable developers to:

* Use OCI’s **chat** and **embedding models** (Cohere / Meta) via REST APIs.
* Store and query embeddings efficiently using **Oracle AI Vector Search**.
* Generate SQL queries from natural language via **Oracle 23AI Select AI**.

> You can also integrate third-party pretrained embedding models into Oracle 23AI by converting them to **ONNX** format and deploying them directly within the database.

---

### 🧱 Vector Data Type in Oracle 23AI

Oracle Database 23AI introduces native **vector data type support**, storing embeddings efficiently as **BLOBs** in database tables.

A typical integration workflow includes:

1. **Connect to the database** using Python (username, password, and DSN).
2. **Create document chunks** with metadata (e.g., ID, source, and text).
3. **Convert** these into `Document` objects.
4. **Generate embeddings** using the `OCIGenAIEmbeddings` class, specifying:

   * Model ID (e.g., `cohere.embed-english-v3.0`)
   * Endpoint and authentication type
   * Compartment OCID
5. **Create a knowledge base** using `OracleVS.from_documents(docs, embed_model, client, table_name, distance_strategy)`.

---

### 📐 Distance Measures

When comparing embeddings, similarity can be measured using:

1. **Dot Product** – Measures the projection magnitude of one vector onto another.
   *Higher values indicate greater semantic similarity.*

2. **Cosine Similarity** – Measures the angle between two vectors.
   *Smaller angles (i.e., higher cosine values) imply closer meanings.*

---

### 🧭 Vector Indexing

Vector indexes accelerate retrieval operations using advanced structures such as:

* **HNSW (Hierarchical Navigable Small World graphs)**
* **IVF (Inverted File Indexes)**

These techniques reduce search space, enabling fast and scalable similarity lookups across millions of vectors.
---

## 💬 Conversational RAG with Oracle Database 23AI

The **Conversational RAG (Retrieval-Augmented Generation)** architecture combines **OCI Generative AI** with **Oracle Database 23AI**, enabling AI agents to answer questions using both **structured** (SQL) and **unstructured** (documents, embeddings) data — while maintaining conversational context across multiple interactions.

---

### 🏗️ Step 1 — Create the Autonomous Database

1. In the **Oracle Cloud Console**, navigate to **Autonomous Database** → **Create Autonomous Database**.
2. Provide:

   * **Database name** and **display name**
   * **Compartment**
   * **Workload type:** *Data Warehouse*
   * **Deployment type:** *Serverless*
3. Set admin credentials and network access rules.
4. Once provisioned, open **Access Control** → **Database Connections** to retrieve **connection strings** and **wallet files** for client connections (Python, SQL Developer, etc.).

---

### ⚙️ Step 2 — Connect via Python

After provisioning the database, establish a connection using the wallet credentials.

```python
import oracledb

# Connect to Oracle 23AI
connection = oracledb.connect(
    user="admin",
    password="your_password",
    dsn="your_tns_entry_from_wallet"
)
print("✅ Connected to Oracle 23AI Database")
```

---

### 🧠 Step 3 — Configure OCI Generative AI Models

In the **OCI Generative AI Dashboard**, set up:

1. A **Chat model** (e.g., `cohere.command-r-plus`) for natural conversation.
2. An **Embedding model** (e.g., `cohere.embed-english-v3.0`) to generate vector representations of documents.

These can be accessed via the **OCI Generative AI REST API** or through **LangChain integrations** like `ChatOCIGenAI` and `OCIGenAIEmbeddings`.

---

### 📄 Step 4 — Ingest and Embed Documents

We now upload a **PDF document**, extract its text, **chunk it**, create **embeddings**, and **store them in Oracle Vector Search** as our knowledge base.

```python
from langchain_community.embeddings import OCIGenAIEmbeddings
from langchain_community.vectorstores import OracleVS
from langchain.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter

# Load and read the PDF document
pdf_loader = PyPDFLoader("knowledge_base.pdf")
pages = pdf_loader.load()
text = " ".join([page.page_content for page in pages])

# Split the text into chunks
text_splitter = RecursiveCharacterTextSplitter(chunk_size=2000, chunk_overlap=100)
chunks = text_splitter.split_text(text)

# Convert chunks into LangChain Document objects
from langchain.schema import Document
docs = [Document(page_content=chunk, metadata={"source": "knowledge_base.pdf"}) for chunk in chunks]

# Initialize embedding model
embed_model = OCIGenAIEmbeddings(
    model_id="cohere.embed-english-v3.0",
    service_endpoint=endpoint,
    compartment_id=compartment_id
)

# Create Oracle Vector Store and insert document embeddings
vs = OracleVS.from_documents(
    docs=docs,
    embedding=embed_model,
    client=connection,
    table_name="knowledge_base_vectors",
    distance_strategy="COSINE"
)
print("📚 Knowledge base created and indexed in Oracle 23AI")
```

---

### 💬 Step 5 — Build the Conversational RAG Chain

Once the embeddings are stored, we can connect the **vector store** and **chat model** to create a **conversational retrieval pipeline** using LangChain.

```python
from langchain_community.llms import ChatOCIGenAI
from langchain.chains import ConversationalRetrievalChain
from langchain.memory import ConversationBufferMemory

# Initialize chat model
llm = ChatOCIGenAI(
    model_id="cohere.command-r-plus",
    service_endpoint=endpoint,
    compartment_id=compartment_id,
    model_kwargs={"max_tokens": 200}
)

# Build retriever from vector store
retriever = vs.as_retriever(search_type="similarity", search_kwargs={"k": 3})

# Create memory for multi-turn dialogue
memory = ConversationBufferMemory(memory_key="chat_history", return_messages=True)

# Build the Conversational RAG chain
rag_chain = ConversationalRetrievalChain.from_llm(
    llm=llm,
    retriever=retriever,
    memory=memory
)

# Ask a question
query = "Summarize the main insights from the knowledge base PDF."
response = rag_chain.invoke({"question": query})

print("🤖", response["answer"])
```

---

### 🔄 Step 6 — End-to-End Workflow Overview

```mermaid
flowchart LR
    A[Upload PDF] --> B[Extract Text]
    B --> C[Chunk Text]
    C --> D[Generate Embeddings via OCI Model]
    D --> E[Store in Oracle Vector Search]
    E --> F[Retrieve Relevant Chunks]
    F --> G[Chat Model (OCI Generative AI)]
    G --> H[Answer with Context]
    H --> I[Store Conversation in Memory]
    I --> A
```

---

### ✅ Summary

With this pipeline, you can build **conversational, context-aware AI systems** that:

* Automatically **ingest and embed PDFs or other unstructured data**,
* Store embeddings in **Oracle Vector Search** for efficient retrieval,
* Use **OCI Generative AI models** to generate context-grounded responses, and
* Maintain conversational continuity with **LangChain Memory**.


