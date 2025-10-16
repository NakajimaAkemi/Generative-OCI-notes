# ☁️ OCI Generative AI Service

The **OCI Generative AI Service** is a **fully managed service** that provides a selection of customizable **large language models (LLMs)** available via API for building AI-powered applications.

---

## 🚀 Main Features

* **Choice of Models:** High-performing pretrained models from *Meta* and *Cohere*.
* **Flexible Fine-Tuning:** Adapt models to domain-specific data.
* **Dedicated AI Clusters:** GPU-backed clusters hosting both fine-tuning and inference workloads.

---

## 🗣️ Pretrained Foundational Chat Models

| Model                           | Provider | Context Window | Output Tokens | Description                                                                                                                      |
| ------------------------------- | -------- | -------------- | ------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| **command-r-plus**              | Cohere   | Up to 128K     | Up to 4K      | Powerful model for Q&A, information retrieval, and sentiment analysis. Higher cost and latency.                                  |
| **command-r-16k**               | Cohere   | Up to 16K      | Up to 4K      | Entry-level use cases. Optimized for speed and cost efficiency.                                                                  |
| **llama 3-70b-instruct / 405b** | Meta     | Up to 128K     | —             | Instruction-tuned models for conversational AI. The 405B model is the largest and best suited for enterprise-scale applications. |

---

## 🔡 Embedding Models

Embedding models convert tokens into **numerical vector representations** for tasks like semantic search, classification, or clustering.

| Model                              | Provider | Dimensionality | Max Tokens | Language Support        | Notes                   |
| ---------------------------------- | -------- | -------------- | ---------- | ----------------------- | ----------------------- |
| **embed-english-v3.0**             | Cohere   | 1024           | 512        | English                 | High-quality embeddings |
| **embed-multilingual-v3.0**        | Cohere   | 1024           | 512        | 100+ languages          | Multilingual support    |
| **embed-english-light-v2.0**       | Cohere   | 1024           | 512        | English                 | Lightweight version     |
| **light (English / Multilingual)** | Cohere   | 384            | 512        | English or Multilingual | Compact and efficient   |

---

## 🔍 Semantic vs. Lexical Search

* **Lexical Search:** Focuses on matching **keywords**.
* **Semantic Search:** Focuses on **meaning**, using vector similarity rather than literal matches.

### 🧮 Semantic Similarity

Similarity between embeddings can be computed using:

* **Cosine similarity**
* **Dot product**

Numerically similar vectors correspond to semantically similar meanings.

### 🧱 Sentence Embeddings

Entire sentences can be embedded — semantically similar sentences will have embeddings that are **close in vector space**.

---

### 🧠 Embedding Use Case (RAG Flow)

```mermaid
flowchart LR
    A[User Question] --> B[Vector Database VDB]
    B --> C[Retrieve Relevant Content]
    C --> D[Send Content to LLM]
    D --> E[LLM Generates Informed Answer]
    E --> A
```

---

## 🧩 Fine-Tuning

Fine-tuning improves model performance for **specific tasks** using **custom data**.

1. **T-Few (Cohere):** Efficient fine-tuning method that updates only a small subset of parameters.
2. **Vanilla Fine-Tuning:** Updates most or all layers for maximum adaptability.

---

## ⚙️ AI Clusters

Dedicated **AI clusters** are GPU-based compute resources for **custom fine-tuning** and **inference**.

* Built on **exclusive RDMA cluster networks** for low-latency GPU communication.
* Each customer’s GPU pool is **isolated** for performance and security.

![Cluster](/assets/images/immagine_2025-10-16_093903827.png)

---

## 📊 OCI Generative AI Dashboard

Accessible through the **Analytics & AI service**, the GenAI dashboard allows customization of model and embedding parameters.

### 🔧 Model Parameters

1. **Top P:** Controls cumulative probability cutoff for token sampling.
2. **Top K:** Limits sampling to top *K* most likely tokens.
3. **Temperature:** Adjusts creativity/randomness of generation.
4. **Preamble Override:** Provides initial context or style guidelines.
5. **Frequency Penalty:** Reduces repetition of frequent tokens.
6. **Presence Penalty:** Penalizes token reuse regardless of frequency.
7. **Max Tokens:** Limits response length.

### 📈 Embedding Model Parameters

* **Truncate:** Manages token overflow by truncating input text.

---

## 🧠 Model Fine-Tuning Workflow

1. **Create a Fine-Tuned Model:**

   * Assign a name and select the base model.
   * Choose the fine-tuning method (e.g., *T-Few* or *Vanilla*).
   * Specify or create a dedicated AI cluster.

2. **Deploy an Endpoint:**

   * Define endpoint details for routing inference traffic.
   * Associate with the desired model and cluster.

---

## 🧰 Inference API Integration

The OCI Generative AI Service exposes an **Inference API** for programmatic access.

---

## ⚙️ Configuration Setup (Shared)

Before using either the **chat (inference)** or **embedding** APIs, you must first configure the OCI SDK client and specify the inference endpoint.

### 📝 Explanation

This setup:

* Loads your OCI authentication credentials from the local config file (`~/.oci/config`).
* Defines the **Generative AI inference endpoint** corresponding to your OCI region.
* Creates a client instance to interact with the Generative AI service.

```python
import oci

# Load basic configuration
CONFIG_PROFILE = "DEFAULT"
config = oci.config.from_file("~/.oci/config", CONFIG_PROFILE)

# Define the Generative AI inference endpoint
endpoint = "https://inference.generativeai.us-chicago-1.oci.oraclecloud.com"

# Create a Generative AI Inference client
generative_ai_inference_client = oci.generative_ai_inference.GenerativeAIInferenceClient(
    config=config,
    service_endpoint=endpoint
)
```

---

## 💬 Inference API Demo

### 📝 Explanation

This example sends a **prompt** to a Cohere chat model (`command-r-plus`) and retrieves a generated response.
You can adjust parameters such as:

* **`temperature`** for randomness,
* **`max_tokens`** for controlling output length,
* **`model_id`** to choose a specific chat model.

### 💻 Example

```python
from oci.generative_ai_inference.models import ChatDetails

# Define the chat request details
chat_detail = ChatDetails(
    model_id="cohere.command-r-plus",
    input="What is the capital of France?",
    temperature=0.7,
    max_tokens=200
)

# Send request and print result
chat_response = generative_ai_inference_client.chat(chat_detail)
print("Chat Result:\n", chat_response.data)
```

---

## 🔡 Embedding Models Demo

### 📝 Explanation

This section demonstrates how to **generate text embeddings** — numerical vector representations of text useful for:

* Semantic search
* Clustering
* Similarity detection (e.g., "find texts with similar meaning")

The script:

1. Defines a list of text inputs (questions about capitals).
2. Specifies an embedding model (`cohere.embed-english-v3.0`).
3. Sends the request to OCI Generative AI.
4. Returns embeddings as arrays of floating-point numbers.

### 💻 Example

```python
import oci

# Using the same configuration and client from above
inputs = [
    "What is the capital of France?",
    "What is the capital of Sweden?",
    "What is the capital of Canada?"
]

# Configure embedding request
embed_text_detail = oci.generative_ai_inference.models.EmbedTextDetails()
embed_text_detail.serving_mode = oci.generative_ai_inference.models.OnDemandServingMode(
    model_id="cohere.embed-english-v3.0"
)
embed_text_detail.inputs = inputs
embed_text_detail.truncate = "NONE"
embed_text_detail.compartment_id = "ocid1.compartment.oc1..aaaaaaaexamplecompartmentid"

# Generate embeddings
embed_text_response = generative_ai_inference_client.embed_text(embed_text_detail)

# Print the embedding vectors
print("************************* Embed Texts Result *************************")
print(embed_text_response.data)
```

---

## 🖥️ Dedicated AI Cluster

(todo)



