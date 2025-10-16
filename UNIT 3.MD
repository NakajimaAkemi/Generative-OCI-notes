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

## 💬 RAG with OCI Knowledge Base and Chat Model

A typical **Retrieval-Augmented Generation (RAG)** flow in LangChain with OCI models involves:

1. Defining a **Vector Store (OracleVS)** for similarity search.
2. Configuring an **LLM** via `ChatOCIGenAI`.
3. Creating a **RetrievalQA** chain to combine the two.

```python
vs = OracleVS(
    embedding_function=embed_model,
    client=conn,
    table_name="knowledge_base",
    distance_strategy="COSINE"
)

retriever = vs.as_retriever(search_type="similarity", search_kwargs={"k": 3})

llm = ChatOCIGenAI(
    model_id="cohere.command-r-plus",
    service_endpoint=endpoint,
    compartment_id="ocid1.compartment.oc1..exampleid",
    auth_type="API_KEY",
    model_kwargs={"max_tokens": 200}
)

chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    return_source_documents=True
)

response = chain.invoke("Explain the benefits of fine-tuning a language model.")
```

To maintain **context awareness** during multi-turn conversations, LangChain also supports **conversational memory buffers** that retain dialogue history between user queries.

---

## 💬 Conversational RAG with Oracle Database 23AI


