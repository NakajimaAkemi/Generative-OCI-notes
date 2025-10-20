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

> 💡 **Q&A:**
> 🟦 **Q:** What should you do if a model is deprecated?
> 💬 **A:** Plan ahead to replace it — you must migrate to a supported model version.
>
> 🟦 **Q:** Which fine-tuning methods does `cohere.command-r-08-2024` support?
> 💬 **A:** It supports **T-Few** and **LoRA** fine-tuning methods.

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

> 💡 **Q&A:**
> 🟦 **Q:** What kind of relationships does vector search focus on?
> 💬 **A:** **Semantic relationships** — it captures meaning, not just keywords.

---

### 🧱 Sentence Embeddings

Entire sentences can be embedded — semantically similar sentences will have embeddings that are **close in vector space**.

---

## 🧩 Fine-Tuning

Fine-tuning improves model performance for **specific tasks** using **custom data**.

1. **T-Few (Cohere):** Efficient fine-tuning method that updates only a small subset of model's weights.
   It’s a *Parameter Efficient Fine Tuning (PEFT)* approach inserting additional lightweight layers (~0.01% of base model size).
2. **Vanilla Fine-Tuning:** Updates most or all layers’ weights for maximum adaptability.

> 💡 **Q&A:**
> 🟦 **Q:** What’s the difference between fine-tuning and PEFT?
> 💬 **A:** **Fine-tuning** trains the whole model, while **PEFT** (like T-Few) updates only a small subset of parameters.
>
> 🟦 **Q:** Does fine-tuning reduce token usage?
> 💬 **A:** Yes — a fine-tuned model often needs fewer tokens to produce accurate results.
>
> 🟦 **Q:** How can total training steps be calculated?
> 💬 **A:** `totalTrainingSteps = (epochs × datasetSize) / batchSize`

---

## ⚙️ AI Clusters

Dedicated **AI clusters** are GPU-based compute resources for **custom fine-tuning** and **inference**.

* Built on **exclusive RDMA cluster networks** for low-latency GPU communication.
* Each customer’s GPU pool is **isolated** for performance and security.

> 💡 **Q&A:**
> 🟦 **Q:** How many endpoints can an AI cluster host?
> 💬 **A:** Up to **50 endpoints** per cluster.
>
> 🟦 **Q:** Can we specify the serving mode for generative models?
> 💬 **A:** Yes — models can be configured for **on-demand** or **continuous** serving.

---

## 🧠 Model Fine-Tuning Workflow

1. **Create a Fine-Tuned Model**
2. **Deploy an Endpoint**

---

## 🧰 Inference API Integration

The OCI Generative AI Service exposes an **Inference API** for programmatic access.

> 💡 **Q&A:**
> 🟦 **Q:** How are chatbot transactions billed?
> 💬 **A:** Transactions are based on the **number of prompt characters + number of response characters** processed dynamically.
>
> 🟦 **Q:** What happens to the probability distribution of the vocabulary during prompting and training?
> 💬 **A:** It is **modified** — prompting and fine-tuning adjust token probabilities to influence model outputs.

---

## 🔒 OCI Security

The **security and privacy** of generative AI workloads are foundational OCI principles.

* GPU clusters are **isolated per customer** — no resource sharing between tenancies.
* Each cluster handles **only the customer’s models**, ensuring full data segregation.
* Both **base** and **fine-tuned** endpoints share the same GPU cluster for performance and efficiency.

---

✅ **Summary:**
OCI’s Generative AI architecture provides an **end-to-end managed environment** — from model fine-tuning and deployment to secure hosting — ensuring **performance, efficiency, and compliance** across enterprise workloads.

---

## ✅ Integrated Test Answers Recap

| Concept                                   | Answer                             |
| ----------------------------------------- | ---------------------------------- |
| Cluster endpoints                         | Up to 50                           |
| Training steps formula                    | (epochs × datasetSize) / batchSize |
| Chatbot billing                           | Prompt chars + Response chars      |
| Model deprecation                         | Plan for model migration           |
| Vocabulary distribution                   | Modified via prompting/training    |
| Serving mode                              | On-demand or Continuous            |
| Supported fine-tuning (command-r-08-2024) | T-Few & LoRA                       |
| Fine-tuning vs PEFT                       | Full model vs Partial weights      |
| Token efficiency                          | Fine-tuning reduces token need     |
| Vector search focus                       | Semantic relationships             |



