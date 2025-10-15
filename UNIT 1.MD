# LLM basics

## LLM Definition
A language model is a probabilistic model of text, generating a token at a time selecting from a probability distribution of possible candidates. The term *large* refeers to the number of parameters.

> *Question*: "How do we affect the probability distribution?"
> *Answer*: "We affect the probability distribution trough prompting and training."

## LLM architectures
Typically models are composed by *Encoders* and *Decoders* given the fact that they are built upon the *Transformer* architecture. Model come in different sizes (# of parameters) or for capabilities (embedding/generation).

### Encoder
Encoder models convert a sequence of tokens to an embedding (vector representation).
![Encoder](/assets/images/immagine_2025-10-15_180714834.png)

### Decoder
Decoders take a sequence of tokens and ouput the next one, designed for text generation.
![Decoder](/assets/images/immagine_2025-10-15_181107207.png)


### Encoder + Decoder example
![Encoder+Decoder](/assets/images/immagine_2025-10-15_181308276.png)

## Prompting 
### Techniques
Prompting(In-Context learning) consists in providing the LLM additional instructions in order to exer control over the probability distribution, eliciting a particular style of responses. There two techniques worth noting:
1. K-shot promptinng: provide K examples in context window
2. Chain of thought: Divide the request into subtasks and provide the LLM each step in the context in order to increase the accuracy
3. Least to most: Instruct the LLM to decompose the problem and solve, easy first
4. Step back: prompt the LLM to identify the high-level concepts pertinent to the specific task

### Safety issues
One key risks of LLM is the *prompt injection* where LLM is deliberately provided with inputs that attempts to ignore instruction and/or behave contrary to the deployment. We need to make sure that the LLM does not leak personal data.

## Decoding
As we already know, decoding is the process where we predict the token step by step from the from the probability distribution of the vocabulary, there are many approaches:
1. The naive method is *greedy* decoding, where we alwys choose the token wiht highest probabillity. 
2. Non deterministic, we pick one random token between the high probability candidates.

### Factors
1. Temperature: This parameter modulates the distribution over the vocabulary, a higher temperature "flattens" the distribution as the use of varied tokens is promoted.
2. Top P: we select from the top and sum the probabilities until we reach p
3. Top K: we select the first K possible tokens


## Training
Prompting alone may not be enough for certain domain specific tasks, meaning that domain adaptation is needed
![Table](/assets/images/immagine_2025-10-15_182525150.png)


## Rag
When we make a domain specific request, the LLM rely solely on data that it has already seen, meaning if the LLM has not been trained on that domain it may say that it doesn't know or worse, be confidently wrong and provide factually unfounded replies, this phenomen is called *hallucination*. Each  generated text are evaluated in the following aspects:
- Attributability:  The ability to attribute the answer to a certain source. 
- Groundedness: How much it is grounded in a document. 
