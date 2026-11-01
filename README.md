# Learn LLM with Atariya

Hands-on notes and working code for LLM fundamentals — tokenization, embeddings,
the chat completion API, and a provider-agnostic wrapper over multiple model backends.

## Layout

```
foundation/
  token/                  tokenization and cost accounting with tiktoken
    token.md              notes
    token.py
    lost_in_the_middle_research_paper.pdf
  embeddings/             embeddings, similarity, ranking, clustering
    index.md              notes
    index.py              embed() + cosine_similarity() helpers
    similarity_ranking.py
    mini_sematic_search.py
    cluster_similar_text.py
    exercise_1.py
    exercise_2.py
  chat_completion_api/    the chat completions surface
    plain_chat_completion.py       blocking completion
    streaming_chat_completion.py   streamed completion + usage
    provider_agnostic_wrapper.py   provider abstraction (Groq, Ollama)
index.py                  Anthropic client experiments
```

## Setup

```bash
python -m venv .venv
.venv\Scripts\activate        # Windows
# source .venv/bin/activate   # macOS / Linux

pip install -r requirements.txt
cp .env.example .env          # then fill in GROQ_API_KEY
```

## Running

The scripts in `foundation/embeddings/` import their shared helpers as
`from index import ...`, so run them from inside that directory:

```bash
cd foundation/embeddings
python similarity_ranking.py
```

Others run from the repo root:

```bash
python foundation/chat_completion_api/provider_agnostic_wrapper.py
python foundation/token/token.py
```

Switch backends with `PROVIDER=groq` or `PROVIDER=ollama` in `.env`.

## Notes

The `.md` files next to the code are the learning notes — `foundation/token/token.md`
and `foundation/embeddings/index.md` are the two written up so far.
