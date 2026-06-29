# Rapid Application Development using Large Language Models

A hands-on workshop based on NVIDIA Deep Learning Institute (DLI) materials,
adapted for Google Colab (T4 GPU) and cloud LLM APIs.

> **Credits:** Original course materials by NVIDIA Corporation / NVIDIA Deep Learning Institute.
> Workshop adapted and delivered by:
> - **Theory:** Domen Verber
> - **Practical:** Jani Dugonik
>
> Faculty of Electrical Engineering and Computer Science (FERI),
> University of Maribor, Slovenia

---

## Workshop Structure

3 parts, 90 minutes each (30 min theory + 60 min practical).

| Part | Topic | Key models and tools |
|---|---|---|
| 1 | From Deep Learning to LLMs and Encoders | BERT, DistilBERT, NLI models |
| 2 | Generative Models and Multimodality | GPT-2, T5, CLIP, ViT+GPT-2, Whisper |
| 3 | LLM Servers, Tool Use, and Agentic Systems | Groq API, LangChain, LangGraph |

See [`workshop_program.md`](workshop_program.md) for the full program with theory and practical topics per part.

---

## Repository Structure

```
├── notebooks/
│   ├── Part1_Encoders.ipynb
│   ├── Part2_Generative_Multimodal.ipynb
│   └── Part3_Servers_Agents.ipynb
├── workshop_program.md
├── LICENSE.md
└── README.md
```

---

## Requirements

### Parts 1 and 2

- Google Colab with **T4 GPU** (Runtime > Change runtime type > T4 GPU)
- No additional accounts needed

### Part 3

- Google Colab (GPU not required for Part 3)
- Free **Groq API key**: [console.groq.com](https://console.groq.com)

---

## How to Use

### Option A: Open directly in Google Colab

1. Go to [colab.research.google.com](https://colab.research.google.com)
2. Click **File > Open notebook > GitHub**
3. Paste this repository URL and select the notebook

### Option B: Download and upload manually

1. Download the `.ipynb` file from this repository
2. Go to [colab.research.google.com](https://colab.research.google.com)
3. Click **File > Upload notebook**

---

## Part 3: Groq API Key Setup

Part 3 uses the Groq API instead of local models.
The free tier is more than sufficient for the workshop.

1. Create a free account at [console.groq.com](https://console.groq.com)
2. Go to **API Keys** and click **Create API Key**
3. In Google Colab, click the **key icon** in the left sidebar (Secrets)
4. Add a new secret named `GROQ_API_KEY` and paste your key
5. Enable **Notebook access** for this secret

---

## License

Workshop materials adapted from NVIDIA DLI course
"Rapid Application Development using Large Language Models".
Original materials Copyright NVIDIA Corporation.

Adaptations for Google Colab and open API backends
by Domen Verber and Jani Dugonik, FERI, University of Maribor.
