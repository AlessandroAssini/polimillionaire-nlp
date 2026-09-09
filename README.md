# 🎯 PoliMillionaire

An open-weight LLM chatbot that plays *Who wants to be a PoliMillionaire?* — combining prompting, retrieval, math tool use, and speech.

## 🌟 Highlights

- **Answers quiz questions with locally run, open-weight LLMs** — no external LLM API keys required.
- **Retrieval-Augmented Generation (RAG)** grounds answers in external context instead of relying on model memory alone.
- **Math tool use** offloads numerical/symbolic reasoning to a calculator instead of the LLM itself.
- **Full speech interface**: questions can be transcribed from audio and answers read back with text-to-speech.
- **Runs on Colab or locally** (GPU recommended), talking to the quiz through the course-provided `millionaire_client` API.

## ℹ️ Overview

PoliMillionaire is an experimental chatbot built to play the *Who wants to be a PoliMillionaire?* quiz game automatically. It was developed for the Natural Language Processing course at Politecnico di Milano (2025/26) to explore how far locally run, open-weight language models can go when answering trivia-style questions without access to a paid LLM API.

The project addresses a concrete constraint: quiz questions must be answered reliably and quickly, but only open-weight models running on commodity/Colab GPUs are available. To tackle this, the system combines prompting strategies, retrieval augmentation for factual grounding, a symbolic/numeric tool for math questions, and an optional speech interface for audio-based rounds. It is aimed at students and researchers interested in practical, resource-constrained LLM system design rather than at production deployment.

## 🧩 How It Works

1. The client authenticates against the quiz API via `millionaire_client` and starts (or resumes) a game session.
2. The current question is fetched from the API; if the round is audio-based, it is first transcribed with a speech-to-text model.
3. The question is routed through the answering pipeline: plain prompting, RAG retrieval, or the math tool, depending on the question type.
4. The selected option is submitted back through the API, and the resulting game state (score, next question) is retrieved.
5. If required, the answer or feedback is optionally read aloud with text-to-speech.

The full integrated pipeline is assembled in [`notebooks/PoliMillionaire.ipynb`](notebooks/PoliMillionaire.ipynb); each building block (RAG, math, audio) is developed and evaluated independently in its own notebook.

## 🚀 Usage

The most common way to use the project is to open [`notebooks/PoliMillionaire.ipynb`](notebooks/PoliMillionaire.ipynb) and run it end to end (locally or in Colab). It plays a full game session using the integrated pipeline.

The underlying API client can also be used directly to script custom answering logic:

```python
from millionaire_client import MillionaireClient

client = MillionaireClient("http://localhost:4000")
client.login("username", "password")

# Start a game
game = client.game.start(competition_id=1)

# Answer questions
while game.in_progress:
    question = game.current_question
    print(f"Q: {question.text}")
    for opt in question.options:
        print(f"  {opt.id}: {opt.text}")

    # Your answering logic here
    result = game.answer(option_id=1)
    print(f"Correct: {result.correct}")
```

🎥 Watch the presentation demo: https://youtu.be/oRMR3RBtOAA

## ⬇️ Installation

### Requirements

- Python 3.10+ (GPU recommended for local runs)
- The `millionaire_client` package provided separately by the course (not included in this repository)
- Access credentials for the quiz API

### Setup

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

### Configuration

Place the course-provided `millionaire_client` package in `src/millionaire_client/`; the notebooks already add `src/` to `sys.path`. In Colab Secrets, set `username`, `poli-millionaire`, and `api-url`. The API URL must use HTTPS. Never commit passwords, tokens, model weights, or logs.

### Run

Open any notebook in `notebooks/` and run it top to bottom; models are downloaded automatically through Hugging Face on first use.

## 📁 Project Structure

```
Assignment/
├── notebooks/
│   ├── PoliMillionaire.ipynb   # Main notebook: integrated system and analysis
│   ├── RAG.ipynb               # RAG architectures and retrieval experiments
│   ├── Math.ipynb              # Mathematical reasoning / tool-use experiments
│   └── Audio.ipynb             # Speech-to-text and text-to-speech evaluation
├── results/                    # Reproducible tables and figures
├── media/                      # Demo video link (video itself kept local)
├── docs/                       # Project documentation
└── requirements.txt
```

## 🛠️ Technologies

- **Language:** Python
- **LLMs & Machine Learning:** PyTorch, Transformers, Accelerate, BitsAndBytes, Sentence-Transformers, FAISS
- **Retrieval-Augmented Generation:** LangChain Community, DuckDuckGo Search, ddgs, json-repair
- **Math & Reasoning:** SymPy
- **Speech:** OpenAI Whisper, Librosa, SoundFile, resampy, gTTS, edge-tts, Kokoro
- **Data & Utilities:** NumPy, Pandas, Matplotlib, Requests

## ✍️ Authors

Developed by **MyAiAcademia** for the Natural Language Processing course at Politecnico di Milano (2025/26):

- Alessandro Assini
- Alberto Bertazza
- Riccardo Antonelli
- Angelo Baturi

## 🤝 Contributing

This is an academic group assignment, but feedback is welcome. If you spot a bug or have a suggestion, please open an issue or a pull request.

## 📄 License

This project is licensed under the [MIT License](LICENSE). The separately distributed course-provided `millionaire_client` package is not part of this repository or covered by this license.

## 📚 Documentation

- [`docs/README.md`](docs/README.md) — note about the course assignment brief
- [`results/README.md`](results/README.md) — conventions for published tables and figures
- [`media/README.md`](media/README.md) — demo video notes
