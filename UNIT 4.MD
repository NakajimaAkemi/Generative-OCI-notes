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

Generative AI Agents **do not manage the database** directly—they connect to existing databases.

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

A **user-defined SQL function** performs **vector search** over the embeddings stored in the knowledge base.

### **Parameters**

* **`p_query`** — Query string input
* **`top_k`** — Number of top results to return

### **Requirements**

1. The **embedding model** used in the query must be the same model used to generate the document vectors.
2. The **function output fields** must match the table’s required fields (`DOCID`, `BODY`, `VECTOR`).
3. If your field names differ, use **aliases** to ensure consistency.

### **Example SQL Function**

Below is an example function definition for vector search:

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

* The function converts the query string into an embedding vector using OCI’s **embedText API**.
* It calculates **VECTOR_DISTANCE** between the query vector and the document vectors stored in the database.
* Results are **sorted by similarity score** and **limited to the top K** most relevant entries.


---

## **6. Knowledge Base Definition**

Steps to create a knowledge base:

1. **Define Data Source** — Select one or more Object Storage buckets containing the data.
2. **Set Vector Search Type** — Choose the vector retrieval configuration.
3. **Create Agent Welcome Message** — Define the greeting and tone.
4. **Enable Hybrid Search (Optional)** — Combine keyword and vector-based retrieval.
5. **Enable Oracle 23AI Support (Optional)** — For enhanced vector search.
6. **Select Search Function** — Assign the vector search SQL function.

---

## **7. Agent Definition**

When defining an agent:

1. Provide **name**, **compartment**, **welcome message**, and **instructions**.
2. Add one or more **knowledge bases** as needed.
3. Configure **persona** and **behavioral parameters**.

---

## **8. Endpoint Definition**

| Parameter              | Description                                         |
| ---------------------- | --------------------------------------------------- |
| **Name / Description** | Identifier for the endpoint.                        |
| **Session Timeout**    | Maximum idle duration for a session.                |
| **Content Moderation** | Enable for input, output, or both.                  |
| **Trace & Citation**   | Optionally enable for debugging and explainability. |

---

## **9. Tenancy Limits**

| Item                                          | Paremeter name                          | Limit per Tenancy |
| --------------------------------------------- | --------------------------------------- | ----------------- |
| **Agents**                                    | `agent-count`                           | 2                 |
| **Knowledge Bases**                           | `knowledge-base-count`                  | 3                 |
| **Endpoints per Agent**                       | `agent-endpoint-per-agent-count`        | 3                 |
| **Files per Data Source (per ingestion job)** | `number-of-files-per-data-source-count` | 1,000             |

---

## Chatbot with Object Store
The process of agent setup is the following
1. Create a Knowledge base (define the object storage type), we can togle the hybrid search (lexical + semantical) it does lexical first and then re ranks with semantical otherwise it only does lexical, define the data source)
2. Define the data source, we can choose multimodal parsing for charts parsing, we can select the bucket and decide which document to use in the bucket or more, we can then toggle the automatic ingestion process, if it fails we can restart the job, the successfull ones are skipped (injection jobs are creating state then to active once ready) 
2. Setup the agent
3. Setup the endpoint

At any point we can specify the compartment type for our policies

> [!NOTE]
> When looking at Knowledge bases, we can see the data source and check the status and logs of injection jobs
