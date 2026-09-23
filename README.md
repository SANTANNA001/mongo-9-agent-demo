# MongoDB 9.0 AI Agent Demo

Three Jupyter notebooks demonstrating how AI agents can investigate a real MongoDB 9.0 upgrade support case — from simple RAG to a two-agent workflow. Everything runs locally with [Ollama](https://ollama.com). No cloud, no API keys.

## The three notebooks

| Notebook | What it shows | Time |
|---|---|---|
| `1_rag.ipynb` | **RAG** — embed docs with `nomic-embed-text`, retrieve relevant sections, generate answers | ~6 min |
| `2_agent.ipynb` | **Single Agent** — model chooses from 7 diagnostic tools, investigates autonomously | ~7 min |
| `3_multi_agent.ipynb` | **Two Agents** — Investigator gathers evidence, TS Reviewer validates + produces report | ~4 min |

## Files

| File | Purpose |
|---|---|
| `SPEAKER_NOTES.md` | Presenter notes — what to show and say for each section (~20 min) |
| `9.0_notes.md` | MongoDB 9.0 release notes (clean markdown) |
| `9.0_compat.md` | MongoDB 9.0 compatibility changes |
| `9.0 Upcoming.pdf` | Release notes PDF (visual reference for audience) |
| `Compatibility changes.pdf` | Compatibility PDF (visual reference) |
| `9.0 Changelog.pdf` | Changelog PDF |
| `requirements.txt` | Python dependencies |

## Prerequisites

### 1. Install Ollama

**macOS:** Download from [ollama.com](https://ollama.com) or use Homebrew:
```bash
brew install ollama
```

**Linux:**
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

**Windows:** Download from [ollama.com](https://ollama.com)

### 2. Pull the models

```bash
# Chat model (1.9 GB)
ollama pull qwen2.5:3b

# Embedding model (274 MB)
ollama pull nomic-embed-text
```

**Model size tip:** If `qwen2.5:3b` is too heavy, try these smaller options (change `CHAT_MODEL` in each notebook):
- `qwen2.5:1.5b` (986 MB)
- `llama3.2:3b` (2.0 GB)
- `phi3:mini` (2.3 GB)

### 3. Python setup

```bash
# Clone the repo
git clone <this-repo-url>
cd AgentDemo

# Create virtual environment
python3 -m venv .venv
source .venv/bin/activate   # macOS/Linux
# .venv\Scripts\activate    # Windows

# Install dependencies
pip install -r requirements.txt
```

### 4. Verify Ollama is running

```bash
ollama list
```

You should see `qwen2.5:3b` and `nomic-embed-text` listed.

## Running the notebooks

```bash
# Start Jupyter (if installed)
jupyter notebook

# Or open directly in VS Code
code .
```

Open notebooks in order: `1_rag.ipynb` → `2_agent.ipynb` → `3_multi_agent.ipynb`

Run each cell top to bottom with `Shift+Enter`.

### For the full presentation

Open these side by side:
- `SPEAKER_NOTES.md` — follow the cues
- `9.0 Upcoming.pdf` — visual reference for audience
- The notebook you're running

## The scenario

> A customer upgraded their sharded Atlas cluster to MongoDB 9.0. They report:
> - Slow aggregations
> - Memory errors on writes (error codes 146 and 292)
> - Change-stream lag

The notebooks simulate how a TS engineer would investigate this using AI agents.

## How the 3 parts connect

```
Part 1 (RAG)        →  Ask a question, get an answer from docs
                        ↓
Part 2 (Agent)       →  Model chooses which tools to run, investigates on its own
                        ↓
Part 3 (Multi-agent) →  Investigator gathers + Reviewer validates = safer output
```

Each part introduces one new idea. Each one builds on the last.

## Tech stack

- **Ollama** — local LLM inference (qwen2.5:3b for chat, nomic-embed-text for embeddings)
- **scikit-learn** — TF-IDF (used in agent tools for document search)
- **numpy** — vector math for cosine similarity
- **requests** — live weather API (Open-Meteo, no API key)

## Production path

The demo uses local models for simplicity. For production:
- Swap `nomic-embed-text` for [MongoDB Voyage AI](https://www.voyageai.com) embeddings (higher quality, Atlas Vector Search integration)
- Connect to real MongoDB clusters instead of sample data
- Add more tools: log analysis, metrics dashboards, ticket creation
