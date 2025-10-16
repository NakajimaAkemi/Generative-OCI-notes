# OCI Generative Integration


## Langchain integration
It's a framework that allows to easily set up chatbot application.

![LangChain](/assets/images/immagine_2025-10-16_164313861.png)

 The principal components are: 

1. Model: could be LLM for text completion and Chat Models built for conversation 
2. Prompt: String prompt template which is basically a string and ChatPromptTemplate for a list of chat messages.
3. Chain of components including LLM, we make use of LCEL (Langchain expression language) or legacy python classes 
4. Memory to store the conversation, chains interact with the memmory, it reads before the chain execution (before core logic) and writes after (After core logic)
5. 

![Template completion](/assets/images/immagine_2025-10-16_164835457.png)

### OCI Generative AI and Oracle 23 ai integration
Langchain community classes include support for Oracle AI 23 vector store, embedding models and chat models from OCI Generative ai. Oracle AI vector search offers vector utilities to automatically generate embedding from unstructed data, can use rest API for embedding and chat models and ORACLE 23 ai Select ai can generate SQL from natural language.
