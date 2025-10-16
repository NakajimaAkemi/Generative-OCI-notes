# OCI Generative service
It's a fully managed service, meaning that it provides a set of customizable LLM available via API to build AI apps.
Main features:
 - Choiche of Models: high performing pretrained models Meta and Cohere
 - Flexible fine tuning of these models
 - Dedicated AI cluster with their GPUs that host fine-tuning and inference workloads

## Pretrained foundational chat models
 1. command-r-plus (cohere): more powerful and expensive 128k tokens
 2. command-r-16k (cohere): 16k tokens, entry level use cases 
 3. llama 3-70b-instruct (meta)

The chat models keep track of the conversation and are instruction tuned models.

## Embedding models
 1. embded-english-v3.0 (cohere):
 2. embded-multilingual-v3.0 (cohere): 16k tokens, entry level use cases 

Semantinc vs Lexical, in the first we focus on the meaning instead of keywords.

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
4. Preamble override
5. Frequency penalty
6. Presence penalty

For the embedding models we have
1. Truncate

### Model finetuning
In the create model we can create our fine tuned model by giving a name, specify the model, method and 
cluster if not already done. After doing so we need an endpoint for traffic, providing the cluster, model.

### Dedicated AI cluster
