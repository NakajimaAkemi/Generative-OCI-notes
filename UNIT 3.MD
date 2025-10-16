# 🤖 OCI Generative AI Integration

## 🔗 LangChain Integration

**LangChain** is a powerful framework that simplifies the creation of **chatbot applications** and **LLM-powered workflows**.
It allows developers to seamlessly connect large language models (LLMs), data sources, and application logic.

![LangChain](/assets/images/immagine_2025-10-16_164313861.png)

---

### 🧩 Core Components

LangChain applications are built from modular components that can be combined into “chains”:

1. **Model**
   The computational engine that processes text — either a **Text Completion Model (LLM)** or a **Chat Model** designed for conversational tasks.

2. **Prompt**
   Defines how inputs are structured and passed to the model.

   * `PromptTemplate`: plain string-based prompt template.
   * `ChatPromptTemplate`: structured list of chat messages.

3. **Chain**
   The pipeline connecting multiple components (e.g., model + prompt + memory).
   You can define chains using:

   * **LCEL (LangChain Expression Language)** — for declarative composition.
   * **Legacy Python classes** — for traditional object-oriented chaining.

4. **Memory**
   Stores and manages conversation history.

   * **Before execution:** The chain **reads** from memory to provide context.
   * **After execution:** The chain **writes** the latest user and model messages back to memory.

5. **Document Loaders**
   Handle loading and preprocessing of data from various sources to make it usable by the model.
![Template completion](/assets/images/immagine_2025-10-16_164835457.png)

---

### 📄 Document Loaders and Chunking

Document loaders import and process data from a variety of sources — such as **PDF, HTML, CSV, JSON, or Markdown** — into a text format suitable for embedding or prompt input.

Once documents are loaded, they are **split into smaller chunks**.
Chunking is a crucial step that directly affects performance and accuracy:

| Chunk Property    | Description                                                 |
| ----------------- | ----------------------------------------------------------- |
| **Chunk Size**    | Should align with the model’s context window.               |
| **Chunk Overlap** | Ensures semantic continuity between chunks.                 |
| **Splitter Type** | Can be paragraph-based, sentence-based, or character-based. |

LangChain provides built-in splitters such as **`RecursiveCharacterTextSplitter`**.

---

### 💻 Example: Loading and Splitting a PDF

```python
from langchain.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter

# Load the PDF
pdf_loader = PyPDFLoader("document_path.pdf")
text = ""
for page in pdf_loader.load():
    text += page.page_content

# Split text into chunks
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=2000,
    chunk_overlap=100
)
chunks = text_splitter.split_text(text)
```

---

## 🧠 Integration with OCI Generative AI & Oracle 23AI

LangChain includes community integrations for **Oracle Generative AI** and **Oracle Database 23AI**, enabling seamless use of OCI services within the LangChain ecosystem. We can use third party models for embedding or inside the Database23ai by downloading pre-trained embedding models, converting them in ONNX format.

**Capabilities include:**

1. **OCI Generative AI Models**

   * Access **chat** and **embedding** models from OCI via REST APIs.
   * Leverage Cohere and Meta models directly within LangChain pipelines.

2. **Oracle AI Vector Search**

   * Store and query vector embeddings efficiently.
   * Supports **automatic embedding generation** from unstructured data.
   * Provides utilities for **semantic similarity search** and **retrieval-augmented generation (RAG)**.

3. **Oracle 23AI (Select AI)**

   * Translate **natural language queries** into SQL using LLM reasoning.
   * Integrate with LangChain to build intelligent data agents that interact directly with Oracle databases.


### Vector data type
In the Oracle 23 ai database we store the vector in the table as BLOB.
We can connect to the db via python, with username, password, dns
Create chunks of the document adding metadata as rows (a dict with id, link and text) converting everything in document object
After doing so we connect to the OCIGenAIEmbeddings class, defining the model, endpoint, auth type and compartment id
At last we create a knoweledge base ORacleVS.from_documents(docs, embed_model, client, table name, distance strategy)
