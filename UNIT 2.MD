# OCI Generative service
It's a fully managed service, meaning that it provides a set of customizable LLM available via API to build AI apps.
Main features:
 - Choiche of Models: high performing pretrained models Meta and Cohere
 - Flexible fine tuning of these models
 - Dedicated AI cluster with their GPUs that host fine-tuning and inference workloads

## Pretrained foundational chat models
 1. command-r-plus (cohere): more powerful and expensive prompt up to 128k tokens, q&a, information retrival and sentiment analysi, response up to 4k tokens.
 2. command-r-16k (cohere): 16k tokens, entry level use cases, response up to 4k tokens. For speed and cost.
 3. llama 3-70b-instruct or 405b (meta): prompt response up to 128k tokens. The chat models keep track of the conversation and are instruction tuned models, the 405b model is the largest available and suited for enterprise apps.

## Embedding models
These models convert to tokens to machine understandable vectors.
 1. embded-english-v3.0 (cohere):
 2. embded-multilingual-v3.0 (cohere): 16k tokens, entry level use cases 

Semantinc vs Lexical, in the first we focus on the meaning instead of keywords.
### Sematinc similarity
Cosine and dot product similarity can be used to compute numerical similarity, numerically similar are semantically similar.
### Sentence embeddings
We can embed sentences where as likentokens, similar sentences are closer.
### Embedding use case 
```mermaid
flowchart TD
    A[User Question] --> B[Vector Database (VDB)]
    B --> C[Retrieve Relevant Content]
    C --> D[Send Content to LLM]
    D --> E[LLM Generates Informed Answer]
    E --> A[User Receives Answer]

```
## Finetuning
We use finetuning for improving the model on specific tasks with our custom data and efficiency.
 1. T-Few: fine tuning (cohere) for fast and efficient customization, selectes only a fraction
 2. Vanilla finetuning: updates most/all the layers.

## AI clusters
Dedicated AI clusters are GPU based compute resource for custom fine tuning and inference.
GenAI service establishes a dedicated AI cluster, whith GPUs and exclusive RDMA cluster network for connecting GPUs.
GPUs pools of a customer are isolated.
![Cluster](/assets/images/immagine_2025-10-16_093903827.png)

## OCI Generative AI
In the service Analytics and AI service we find the Gen AI dashboard, we can customize the parameters:
1. Top P
2. Top K
3. Temperature
4. Preamble override: Intial guideline/context
5. Frequency penalty
6. Presence penalty: works the same but it penalizes regardless of the frequency.
7. Max tokens.

For the embedding models we have
1. Truncate

### Model finetuning
In the create model we can create our fine tuned model by giving a name, specify the model, method and 
cluster if not already done. After doing so we need an endpoint for traffic, providing the cluster, model.

### Dedicated AI cluster

### Inference API
We can dowload the code that connects to the model's API, connecting with the OCI sdk.
1. Setup Auth params: we fetch the config file, and setup the compartment id
2. define the endpoint
3. Create the GenerativeAIInferenceClient, which is an object for connect to the Inference API, passing the confing, endpoint, retry strategy and timeout
4. create ChatDetails for the preserving the request detail
5. CohereChatRequest that specifies the input for gen ai model, defining the message and all the params (top k,..)
6. Define the model in the ChatDetail Service mode, then pass the request and compartment id
7. chat_response=generative_ai_inference_client.chat(chat_detail) 
#### Config
The config file contains
1. User
2. fingerpring
3. tenancy
4. region
5. key_file    <-- directory of the private key .pem file (without it fails)

For the setup we go to the user profile and find the api keys section, we click add api key, generate it
private key and generate the config file
