# Workshop: Rapid Application Development using Large Language Models

*Based on NVIDIA DLI materials "Rapid Application Development using Large Language Models"*
*Adapted for Google Colab (T4 GPU) and local environments*
*Format: 3 × 90 minutes | 2 presenters: Theory (30 min) + Practical (60 min)*

---

## Part 1 — From Deep Learning to LLMs and Encoders (90 min)

### Theory (30 min)

- From image classification to language: the feature extractor + task head paradigm
- Language as an ordered sequence of discrete tokens
- Tokenization: from raw text to input IDs
- Word embeddings and positional encoding
- Attention mechanism intuition: why tokens need to "look at each other"
- Transformer blocks: multi-head attention, layer norm, residual connections
- BERT architecture: masked language modeling pretraining, bidirectional reasoning
- When encoder-only models are the right choice
- Overview of encoder task types: sequence classification, token classification, zero-shot

### Practical (60 min)

- Setting up the Colab environment: `transformers`, `datasets`, `torch`
- Loading a multilingual BERT model via HuggingFace `pipeline`
- Exploring tokenizer output: tokens, input IDs, attention masks
- Visualizing word embeddings with cosine similarity
- Visualizing attention matrices
- Exercise 1: Multilingual sentiment analysis
- Exercise 2: Named entity recognition (NER)
- Exercise 3: Zero-shot text classification via natural language inference (NLI)

---

## Part 2 — Generative Models and Multimodality (90 min)

### Theory (30 min)

- Why encoders are insufficient for generation: the variable-length output problem
- Autoregressive decoding: predicting the next token step by step
- GPT-style decoder-only models: unidirectional reasoning and training parallelization
- Encoder-decoder models (T5): grounding the decoder with encoded context
- Instruction fine-tuning: moving toward zero-shot task generalization
- What is a modality and why do they differ technically
- CLIP: learning a joint text-image embedding space
- Cross-attention as a bridge between modalities: ViT + GPT-2, Whisper
- Brief conceptual introduction to diffusion models

### Practical (60 min)

- GPT-2: text generation and experimenting with sampling parameters (temperature, top-k, top-p)
- T5-base: machine translation and abstractive summarization
- CLIP: computing and comparing text-image similarity scores
- Image captioning with ViT + GPT-2
- Speech-to-text with Whisper-small
- *(Optional, T4-dependent)* Text-to-image with a lightweight diffusion model

---

## Part 3 — LLM Servers, Tool Use, and Agentic Systems (90 min)

### Theory (30 min)

- Production challenges: latency, concurrency, GPU memory management
- Inference servers: what they solve and how they work (vLLM, NVIDIA NIM)
- The OpenAI-compatible API as today's de facto standard
- Structured outputs: enforcing JSON schema in LLM responses
- Tool use and function calling: giving the model access to external actions
- The ReAct loop: Reason + Act as a general agent architecture
- Multi-agent systems: compartmentalization, context limits, modularity
- LangGraph: modeling agent behavior as an explicit state graph

### Practical (60 min)

- Two deployment modes shown side by side:
  - Cloud: Groq API (free tier) via `openai` client
  - Local: Ollama on Windows/Linux via `openai` client (identical code, only `base_url` changes)
- Streaming responses in a conversational loop
- Structured JSON output with schema enforcement
- Building a simple multi-tool agent (calculator + document search)
- Implementing a ReAct loop manually from scratch
- Recreating the same loop in LangGraph: explicit nodes, edges, and state
- Mini project: a content pipeline where a vision-language model describes an input image and an LLM synthesizes a short text based on the description

---

*Credits: Workshop materials adapted from NVIDIA Deep Learning Institute (DLI) course "Rapid Application Development using Large Language Models". Original materials © NVIDIA Corporation.*
