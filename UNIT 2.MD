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

### Steps

1. **Set Up Authentication:**

   * Retrieve the OCI **config file** and setup the **compartment ID**.

2. **Define Endpoint:**

   * Specify the model endpoint for inference.

3. **Create a Client:**

   ```python
   from oci.generative_ai_inference import GenerativeAIInferenceClient
   client = GenerativeAIInferenceClient(config, endpoint, retry_strategy, timeout)
   ```

4. **Create Request Objects:**

   * `ChatDetails`: Holds request-level information.
   * `CohereChatRequest`: Defines the prompt, parameters (Top K, etc.), and model settings.

5. **Send Request:**

   ```python
   chat_response = client.chat(chat_details)
   ```

---

### 🗂️ OCI Config File Structure

| Key             | Description                    |
| --------------- | ------------------------------ |
| **user**        | User OCID                      |
| **fingerprint** | Key fingerprint                |
| **tenancy**     | Tenancy OCID                   |
| **region**      | OCI region                     |
| **key_file**    | Path to the private `.pem` key |

> ⚙️ **Setup Tip:**
> Go to *User Profile → API Keys → Add API Key* to generate a key pair and download your config file.

---

## 🧪 Embedding Models Demo

*(To be completed)*

---

## 🖥️ Dedicated AI Cluster

*(To be completed)*

