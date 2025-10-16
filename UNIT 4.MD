# OCI Generative AI agent
This platform allows us to create AI agents, leveraging on knowledge bases.

The overall architecture
1. Interface that interacts with the user as message/command
2. Short/Long term memory for context 
3. LLM does Reasoning, Planning(multi step processes), Persona(tone style) and Acting
4. Tools: APIs, DBs etc..
5. Prompt: specific query provided by user
6. Knowledge base

## Agent concepts
1. Generative model: LLM
2. Agent: autonomous system that understands and generate human like text with high answerability and groundedness
3. Knowledge base: VDB to connect and ingest data from data source
4. Data source and store: Data sources provide connection information to the data store that an agent uses to generate responses.
5. Data ingestion: A process that extracts data from data source documents, converts into structured format suitable for analysis then store in the knowledge base.
6. Session: A series of exchanges where the user sends queries and the agent responds
7. Agent endpoint: specific endpoint in the network/system
8. Trace: Tracks conversations and agent responses.
9. Citation: Source of the information in RAG
10. Content moderation: filters abusive/harmful content from generated responses or user prompts in LLM

## Object store guideline
1. Data source: data for generative ai agents uploaded as Object Storage Buckets
2. Buckets: One bucket per data source
3. File type and size: Only PDF and txt files supported and no larger than 100MB.
4. PDF content: Files can have images, chats,, and reference tables but must not exceed 8MB
5. Chart: No special preparation is needed for charts as long they're two diemensional
6. Table: Use reference tables with several rows and columns
7. URLs: all hyperlinks in the PDF are extraced and displayed as hyperlinks in the chat response
8. Data not ready: if your data isn't yet available, create an empty folder for the data source and populate it later, so that we set it in advance

## Database requirements
Generative AI Agents don't manage the db they connect to it.

Required fields:
 1. DOCID
 2. BODY
 3. VECTOR
Optional:
 1. CHUNKID: id in case the document is larger than 512 tokens.
 2. URL of reference
 3. TITLE
 4. PAGE_NUMBERS
