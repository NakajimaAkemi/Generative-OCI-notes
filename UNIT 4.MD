# **OCI Generative AI Agent Overview**

Oracle Cloud Infrastructure (OCI) Generative AI Agents enable the creation of intelligent agents that leverage knowledge bases for context-aware, grounded, and explainable responses.

---

## **1. Overall Architecture**

| Component                  | Description                                                                                                    |
| -------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Interface**              | Handles user interactions through messages or commands.                                                        |
| **Memory**                 | Maintains short-term and long-term context across sessions.                                                    |
| **LLM (Generative Model)** | Performs reasoning, planning (multi-step tasks), persona shaping (tone/style), and acting (executing actions). |
| **Tools**                  | APIs, databases, and external systems connected for agent actions.                                             |
| **Prompt**                 | The specific user query or instruction.                                                                        |
| **Knowledge Base**         | The structured, retrievable data source grounding the agent’s responses.                                       |

---

## **2. Core Concepts**

| Concept                      | Description                                                                                         |
| ---------------------------- | --------------------------------------------------------------------------------------------------- |
| **Generative Model (LLM)**   | The large language model that generates human-like responses.                                       |
| **Agent**                    | The autonomous system that understands and generates text with high answerability and groundedness. |
| **Knowledge Base (VDB)**     | Vector database that stores structured data embeddings for retrieval.                               |
| **Data Source / Data Store** | The location from which the agent ingests and accesses data.                                        |
| **Data Ingestion**           | The process of extracting, transforming, and loading data into the knowledge base.                  |
| **Session**                  | A continuous exchange of messages between a user and the agent.                                     |
| **Agent Endpoint**           | The specific network endpoint used to interact with the agent.                                      |
| **Trace**                    | A tracking mechanism that logs conversation history and agent reasoning.                            |
| **Citation**                 | References the source of retrieved information in RAG (Retrieval-Augmented Generation).             |
| **Content Moderation**       | Filters and removes harmful or inappropriate content from inputs or outputs.                        |

---

## **3. Object Store Guidelines**

| Item                     | Requirement                                                       |
| ------------------------ | ----------------------------------------------------------------- |
| **Data Source**          | Each data source is stored as an Object Storage bucket.           |
| **Buckets**              | One bucket per data source.                                       |
| **Supported File Types** | `.pdf` and `.txt` only.                                           |
| **File Size Limit**      | Maximum 100 MB per file.                                          |
| **PDF Content Rules**    | Images, charts, and tables allowed; total file size ≤ 8 MB.       |
| **Charts**               | Two-dimensional charts supported without special preparation.     |
| **Tables**               | Use reference tables with multiple rows and columns.              |
| **URLs**                 | Hyperlinks in PDFs are extracted and displayed in chat responses. |
| **Empty Data Sources**   | You can create empty folders for future data ingestion.           |

---

## **4. Database Requirements**

Generative AI Agents **do not manage the database** directly — they connect to existing ones.

### **Required Fields**

* `DOCID`
* `BODY`
* `VECTOR`

### **Optional Fields**

* `CHUNKID` — for splitting large documents (>512 tokens)
* `URL` — reference source
* `TITLE` — document title
* `PAGE_NUMBERS` — location of extracted text

---

## **5. Function Query**

A **user-defined SQL function** performs **vector search** over embeddings stored in the knowledge base.

### **Parameters**

* **`p_query`** — Query string input
* **`top_k`** — Number of top results to return

### **Requirements**

1. The **embedding model** used in the query must be the same as the one used to generate the document vectors.
2. The **function output fields** must match the table’s required fields (`DOCID`, `BODY`, `VECTOR`).
3. If field names differ, use **aliases** for consistency.

---

### **Example SQL Function**

```sql
-- Create function from vector table
-- When returning results, rename (alias) the record ID as ‘DOCID’, 
-- the content column as ‘BODY’, and the VECTOR_DISTANCE as ‘SCORE’

CREATE OR REPLACE FUNCTION retrieval_func_ai (
    p_query IN VARCHAR2,
    top_k IN NUMBER
) RETURN SYS_REFCURSOR
IS
    v_results SYS_REFCURSOR;
    query_vec VECTOR;
BEGIN
    -- Generate vector embedding for the query
    query_vec := vector_utl.to_embedding(
        json('{
            "provider": "OCIGenAI",
            "credential": "OCI_CRED",
            "url": "https://inference.generativeai.eu-frankfurt-1.oci.oraclecloud.com/20231113/actions/embedText",
            "model": "oracle.embed-multilingual-v3.0"
        }'),
        p_query
    );

    -- Perform vector search
    OPEN v_results FOR
        SELECT DOCID, BODY, VECTOR_DISTANCE(text_vec, query_vec) AS SCORE
        FROM my_vector_table
        ORDER BY SCORE
        FETCH FIRST top_k ROWS ONLY;

    RETURN v_results;
END;
/
```

### **Explanation**

* The query is converted into an embedding using the **OCI embedText API**.
* **VECTOR_DISTANCE** computes similarity between the query vector and stored document vectors.
* Results are ranked by similarity score and limited to the **top K** most relevant entries.

---

## **6. Knowledge Base Definition**

Steps to create a Knowledge Base:

1. **Define Data Source** — Select Object Storage buckets containing your data.
2. **Set Vector Search Type** — Configure the retrieval method.
3. **Create Welcome Message** — Define greeting and conversational tone.
4. **Enable Hybrid Search (Optional)** — Combine lexical and vector-based retrieval.
5. **Enable Oracle 23AI Support (Optional)** — For advanced vector search.
6. **Select Search Function** — Assign the SQL function for retrieval.

---

## **7. Agent Definition**

When defining an agent:

1. Provide **name**, **compartment**, **welcome message**, and **instructions**.
2. Add one or more **knowledge bases** as needed.
3. Configure **persona** and **behavioral parameters**.

---

## **8. Endpoint Definition**

| Parameter              | Description                              |
| ---------------------- | ---------------------------------------- |
| **Name / Description** | Identifier for the endpoint.             |
| **Session Timeout**    | Maximum idle duration for a session.     |
| **Content Moderation** | Enable for input, output, or both.       |
| **Trace & Citation**   | Enable for explainability and debugging. |

---

## **9. Tenancy Limits**

| Item                                          | Parameter Name                          | Limit per Tenancy |
| --------------------------------------------- | --------------------------------------- | ----------------- |
| **Agents**                                    | `agent-count`                           | 2                 |
| **Knowledge Bases**                           | `knowledge-base-count`                  | 3                 |
| **Endpoints per Agent**                       | `agent-endpoint-per-agent-count`        | 3                 |
| **Files per Data Source (per ingestion job)** | `number-of-files-per-data-source-count` | 1,000             |

---

## **10. Chatbot with Object Store**

### **Setup Process**

1. **Create a Knowledge Base** — Define Object Storage as the data source.

   * Toggle **Hybrid Search** (lexical + semantic).
   * Define the data source configuration.
2. **Define the Data Source**

   * Optionally enable **multimodal parsing** for charts.
   * Select the storage bucket and documents to include.
   * Enable **automatic ingestion** (failed jobs can be restarted).
   * Jobs move from *creating* → *active* when complete.
3. **Set Up the Agent**
4. **Set Up the Endpoint**

   * Content moderation is **off by default**.
   * Trace and citation are **enabled by default**.
   * Maximum session: **3600 seconds (1 hour)**, up to **7 days**.
   * Once defined, endpoint parameters cannot be edited later.

At any stage, specify the **compartment** for access and policy management.

> [!NOTE]
>
> * You can monitor Knowledge Base ingestion job status and logs.
> * Delete jobs that are *waiting* or *in progress* if needed.
> * Only **one data source** per Knowledge Base.
> * To delete a Knowledge Base, first remove associated data sources and agents.
> * Deletions are **irreversible**.
> * Agents cannot respond using deleted Knowledge Bases.

---

## **11. Chatbot with Oracle 23AI Data Store**

### **Database Definition**

1. In OCI, create an **Autonomous Database**.
2. Provide **compartment info**, **display name**, and **database name**.
3. Choose **Workload Type** — typically *Data Warehouse* (for analytics and vector search).
4. Select **Deployment Type** — *Serverless* (recommended) or *Dedicated*.
5. Use **Oracle 23AI version** (default is 19c).
6. Set **ECPU = 2**, **Storage = 1 TB**, and **Backup retention = 60 days**.
7. Define **Admin credentials**.
8. Configure **Network Access**:

   * Secure access via credentials, allowed IPs, or VCN.
   * Choose **Private Endpoint (OCI VCN)**.
9. Specify the **VCN** and **subnet** (mTLS not required).
10. Provide a **valid notification email**.

### **Connection Setup**

1. From the OCI dashboard, select your Autonomous DB and copy the **connection string** (ports 1521–1522).
2. Retrieve the **Private Endpoint IP**.
3. In **Database Tools**, create a **new connection**:

   * Name, compartment, and Autonomous DB.
   * Use **Admin credentials**.
4. Create a **password secret** in **OCI Vault**, matching the Autonomous DB password.
5. Retrieve the **connection strings** again.
6. Replace the host in the first connection string with the **Private Endpoint IP**.
7. When defining the **Knowledge Base**, link it to your **Oracle 23AI Autonomous Database**.

---
