# 🧠 Large Language Models (LLMs) — Overview

---

## 1. Definition

A **Language Model (LM)** is a probabilistic model of text that generates tokens one at a time, selecting each from a probability distribution over possible candidates.
The term **“large”** refers to the number of parameters in the model.

> 💡 **Q&A:**
> 🟦 **Q:** How do we affect the probability distribution?
> 💬 **A:** We affect the probability distribution through **prompting** and **training**.

---

## 2. LLM Architectures

LLMs are typically based on the **Transformer** architecture and are composed of **Encoders** and/or **Decoders**.
Models can vary by size (number of parameters) or capabilities (e.g., embeddings vs. generation).

### 🧩 Encoder

Encoders convert a sequence of tokens into an **embedding** (vector representation).
![Encoder](/assets/images/immagine_2025-10-15_180714834.png)

> 💡 **Q&A:**
> 🟦 **Q:** What does a cosine distance of 0 indicate between two embeddings?
> 💬 **A:** It means the embeddings are **identical in direction**, i.e., **highly similar** in meaning.

---

### 🔁 Decoder

Decoders take a sequence of tokens and output the **next token**, designed primarily for **text generation**.
![Decoder](/assets/images/immagine_2025-10-15_181107207.png)

---

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
5. **Zero-Shot Chain of Thought** — Like CoT but without examples.

#### Prompt Format

Llama 2 prompts can be formatted like this:

```
<<s>
[INST]
  <<SYS>>
    {{system prompt}}
  <</SYS>>
    {{user_message}}
[/INST]
```

> 💡 **Q&A:**
> 🟦 **Q:** When is **Soft Prompting** ideal?
> 💬 **A:** **Soft prompting** is ideal when **full fine-tuning is impractical** but customization is still needed.
> It learns a **small set of continuous embeddings** that guide model behavior **without modifying original parameters**, providing an efficient adaptation method requiring minimal compute.

---

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

> 💡 **Q&A:**
> 🟦 **Q:** How can we ensure identical outputs for all users when generating text?
> 💬 **A:** Set a **fixed seed value**. If the seed is `None`, the model will **generate diverse, random responses**.

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

> 💡 **Q&A:**
> 🟦 **Q:** What type of model is RAG considered, and what is its main advantage?
> 💬 **A:** **RAG** is **non-parametric**, meaning it doesn’t retrain the model.
> It can **answer about any external corpus** by retrieving and grounding relevant documents.

---

## 6.5 🧠 LLM Customization Overview

Training and adapting large language models (LLMs) can significantly impact performance, cost, and efficiency.

---

### ⚙️ Training Considerations

Training a large LLM **from scratch** is often impractical due to cost, data, and expertise requirements.

---

### 🧩 Practical Alternatives

1. **🪶 In-Context Learning (Few-Shot Prompting)**
2. **🎯 Fine-Tuning**
3. **📚 Retrieval-Augmented Generation (RAG)**

---

### 🧭 Choosing the Right Approach

Follow this progression:

1. ✅ Start with **simple prompting**
2. ➕ Add **few-shot examples**
3. 🔍 Introduce **RAG**
4. 🧬 **Fine-tune** if necessary
5. ⚡ Combine **RAG + fine-tuning** for best performance

---

## 7. Code Models

Models like **GitHub Copilot** and **Code Llama** are trained on code and natural-language comments.
They’re widely used for **programming assistance** and **code completion**.

---

## 8. Multi-Modal Models

**Multi-modal models** are trained on multiple input types — text, images, or audio.

* **Autoregressive models** (e.g., DALL·E) generate outputs token by token.
* **Diffusion models** generate outputs all at once — great for images, less for text.

---

## 9. Language Agents

Frameworks enable LLM-based **agents** capable of **tool use**, **reasoning**, and **environment interaction**.

### 🧠 Agent Frameworks

1. **ReAct:** Alternates between reasoning and action.
2. **ToolFormer:** Inserts tool calls during pre-training.
3. **Bootstrapped Reasoning:** Uses model-generated rationales for fine-tuning.

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

---

## ✅ Integrated Test Answers Recap

| Concept                  | Answer                                               |
| ------------------------ | ---------------------------------------------------- |
| Probability distribution | Modified with prompting & training                   |
| Cosine distance = 0      | Vectors identical in direction (highly similar)      |
| Seed behavior            | Fixed seed = identical outputs; None = random        |
| RAG nature               | Non-parametric; answers any corpus                   |
| Soft prompting           | Efficient for light customization without retraining |








