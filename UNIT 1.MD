# 🧠 Large Language Models (LLMs) — Overview

## 1. Definition

A **Language Model (LM)** is a probabilistic model of text that generates tokens one at a time, selecting each from a probability distribution over possible candidates.
The term **“large”** refers to the number of parameters in the model.

> **Question:** How do we affect the probability distribution?
> **Answer:** We affect the probability distribution through **prompting** and **training**.

---

## 2. LLM Architectures

LLMs are typically based on the **Transformer** architecture and are composed of **Encoders** and/or **Decoders**.
Models can vary by size (number of parameters) or capabilities (e.g., embeddings vs. generation).

### 🧩 Encoder

Encoders convert a sequence of tokens into an **embedding** (vector representation).
![Encoder](/assets/images/immagine_2025-10-15_180714834.png)

### 🔁 Decoder

Decoders take a sequence of tokens and output the **next token**, designed primarily for **text generation**.
![Decoder](/assets/images/immagine_2025-10-15_181107207.png)

### 🔄 Encoder + Decoder Example

![Encoder+Decoder](/assets/images/immagine_2025-10-15_181308276.png)

---

## 3. Prompting

**Prompting (In-Context Learning)** consists of providing the LLM with additional instructions to influence its probability distribution — eliciting particular response styles or reasoning behaviors.

### ✍️ Techniques

1. **K-shot prompting** — Provide *K* examples within the context window.
2. **Chain of Thought (CoT)** — Divide the request into subtasks and show reasoning steps to improve accuracy.
3. **Least-to-Most Prompting** — Instruct the LLM to decompose the problem and solve from easy to complex.
4. **Step-Back Prompting** — Ask the LLM to identify high-level concepts before solving the task.
5. **zero-shot Chain of Thought** — Like CoT but without examples.

#### Prompt format
Llama 2 prompt can be formatd like the following for specific instructions.
```	
<<s>
[INST]
  <<SYS>>
    {{system prompt}}
  <</SYS>>
    {{user_message}}
[/INST]
```	
### ⚠️ Safety Issues

* **Prompt Injection:** Malicious inputs can attempt to override instructions or extract sensitive data.
* Proper safeguards are needed to ensure that the LLM doesn’t leak private or confidential information.

---

## 4. Decoding

**Decoding** is the process of generating text token by token from the model’s probability distribution.

### 🧮 Methods

1. **Greedy Decoding:** Always select the token with the highest probability.
2. **Stochastic Decoding:** Randomly sample among the high-probability candidates for diversity.

### ⚙️ Key Parameters

* **Temperature:** Controls randomness — higher temperature flattens the distribution (more creative, less deterministic).
* **Top-P (Nucleus Sampling):** Select tokens until cumulative probability ≥ *P*.
* **Top-K:** Consider only the *K* highest-probability tokens.

---

## 5. Training

Prompting alone may not be sufficient for **domain-specific** or **specialized** tasks — additional training or **fine-tuning** is often required.

![Table](/assets/images/immagine_2025-10-15_182525150.png)

---

## 6. Retrieval-Augmented Generation (RAG)

LLMs can suffer from **hallucination** — confidently producing incorrect or unfounded answers when lacking relevant training data.

### ✅ Evaluation Aspects

* **Attributability:** Can the response be traced back to a specific source?
* **Groundedness:** How well is the answer supported by retrieved documents?

### 🔍 How RAG Works

RAG is a **non-parametric** approach that augments LLMs with external data sources.

1. **Ingestion:** Documents are chunked, embedded, and stored in a vector database.
2. **Retrieval:** Perform a **semantic search** based on the user prompt to find similar documents.
3. **Generation:** Provide the retrieved context to the LLM to generate a grounded response.

---

Of course 😊 — here’s a **cleaner, clearer, and better-structured version** of your section, with consistent tone, professional formatting, and improved flow.

---

### 6.5 🧠 LLM Customization Overview

Training and adapting large language models (LLMs) can significantly impact performance, cost, and efficiency. Below is an overview of different approaches and when to use them.

---

### ⚙️ Training Considerations

Training a large LLM **from scratch** is often impractical due to:

1. **Cost:**
   Training a 10B-parameter model can cost **around $1 million** in compute resources.

2. **Data Requirements:**
   Massive datasets are needed — for example, **Llama 2 7B** was trained on **2 trillion tokens** of annotated data.

3. **Expertise:**
   Requires deep understanding of model internals, distributed training, and hardware troubleshooting to handle potential **failures and instabilities**.

---

### 🧩 Practical Alternatives

Instead of training from scratch, existing models can be **customized** or **extended** to your domain using one of the following techniques:

1. **🪶 In-Context Learning (Few-Shot Prompting):**

   * Provide examples within the prompt to guide model behavior.
   * Best when the model already understands the task.
   * **Pros:** No retraining required.
   * **Cons:** Limited by **context window size** and increases **latency**.

2. **🎯 Fine-Tuning:**

   * Adjusts certain model layers to specialize in domain-specific tasks.
   * **Pros:** Improves task performance and reduces token usage for similar results.
   * **Cons:** Requires **curated labeled data**, **compute resources**, and **time**.

3. **📚 Retrieval-Augmented Generation (RAG):**

   * Combines the model with an external **knowledge base** to provide up-to-date context.
   * **Pros:** Handles **rapidly changing data** and **reduces hallucinations**.
   * **Cons:** More complex to set up and needs a **reliable data source** (e.g., vector database).

---

### 🧭 Choosing the Right Approach

When building your solution, follow this recommended progression:

1. ✅ Start with **simple prompting**
2. ➕ Add **few-shot examples (in-context learning)**
3. 🔍 Introduce **RAG** for dynamic or data-driven contexts
4. 🧬 **Fine-tune** the model if domain performance is insufficient
5. ⚡ **Optimize** RAG on top of the fine-tuned model for maximum efficiency

---


## 7. Code Models

Models like **GitHub Copilot** and **Code Llama** are trained on code and natural-language comments.
They’ve been highly successful for **programming assistance** and **code completion**.

---

## 8. Multi-Modal Models

**Multi-modal models** are trained on multiple input types — such as text, images, or audio.

* **Autoregressive models** (e.g., DALL·E) generate outputs token by token.
* **Diffusion models** generate complex outputs all at once, making them efficient for continuous data (like images) but less suited for text.

**Examples:**

* Text → Image
* Image → Text
* Multi-directional (both)

---

## 9. Language Agents

Frameworks now enable LLM-based **agents** capable of **tool use**, **action-taking**, and **environment interaction**.

### 🧠 Agent Frameworks

1. **ReAct:** Alternates between reasoning (“thought”) and action, incorporating feedback from the environment.
2. **ToolFormer:** Uses pre-training to replace text spans with tool calls that yield results.
3. **Bootstrapped Reasoning:** Prompts the LLM to emit intermediate rationalizations used as fine-tuning data.

---

### ✅ Summary

| Concept                    | Purpose                             |
| -------------------------- | ----------------------------------- |
| **Prompting**              | Steer model behavior in-context     |
| **Decoding**               | Generate coherent text              |
| **Training / Fine-tuning** | Adapt to new domains                |
| **RAG**                    | Ground responses with external data |
| **Multi-modal**            | Combine multiple data types         |
| **Agents**                 | Enable reasoning and tool use       |
