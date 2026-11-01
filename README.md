<div align="center">

# Learn LLM with Atariya

**Learn how LLMs actually work by building the pieces yourself — one concept, one runnable file, one set of notes at a time.**

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Topic: LLM](https://img.shields.io/badge/Topic-LLM%20Engineering-8A2BE2)](#the-learning-path)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](#contributing)

</div>

---

No framework magic. No `pip install langchain` and hope. Every topic here is a short
explainer written in plain language, next to code you can run and break. If you are a
backend engineer who keeps hearing "embeddings", "context window", "RAG" and wants the
real mental model instead of vibes — start at the top and work down.

## Why this repo exists

Most LLM tutorials hand you a framework and a working demo. You ship something, but you
cannot answer *why* it works, so you cannot debug it or cost it. This repo goes the other
way: understand the primitive, then build the abstraction over it.

Every folder follows the same shape:

```
<topic>/
  *.md    the explainer — the mental model, the engineering takeaway
  *.py    runnable code that proves it
```

## The learning path

Work through it in this order. Each step assumes the one above.

| # | Topic | Read | Run | What you walk away knowing |
|---|-------|------|-----|----------------------------|
| 1 | **Tokens** | [`foundation/token/token.md`](foundation/token/token.md) | [`token.py`](foundation/token/token.py) | Why a token isn't a word, how BPE works, and why context windows, cost and latency are all the same problem |
| 2 | **Embeddings** | [`foundation/embeddings/index.md`](foundation/embeddings/index.md) | [`index.py`](foundation/embeddings/index.py) | How meaning becomes geometry, cosine similarity, and the two very different places embeddings show up |
| 3 | **Similarity & search** | ↑ same notes | [`similarity_ranking.py`](foundation/embeddings/similarity_ranking.py) · [`mini_sematic_search.py`](foundation/embeddings/mini_sematic_search.py) | Ranking text by meaning, and a semantic search engine in ~40 lines |
| 4 | **Clustering** | ↑ same notes | [`cluster_similar_text.py`](foundation/embeddings/cluster_similar_text.py) | Grouping documents with no labels and no training |
| 5 | **Chat Completions** | — | [`plain_chat_completion.py`](foundation/chat_completion_api/plain_chat_completion.py) | The actual request/response shape underneath every chatbot |
| 6 | **Streaming** | — | [`streaming_chat_completion.py`](foundation/chat_completion_api/streaming_chat_completion.py) | Token-by-token output and reading real usage numbers |
| 7 | **Provider abstraction** | — | [`provider_agnostic_wrapper.py`](foundation/chat_completion_api/provider_agnostic_wrapper.py) | Swap Groq ↔ Ollama without your app knowing. Adding a provider = one subclass, one factory line |
| 8 | **Production patterns** | — | [`index.py`](index.py) | Caching, retry with backoff, structured JSON output, cost tracking, fallbacks |

## A taste of the notes

> A token is not a word, not a character, but something in between.
>
> — and the leading space matters. `" is"` and `"is"` are different tokens.
> That one fact explains a surprising number of "why is the model being weird" bugs.

> `vector("king") - vector("man") + vector("woman") ≈ vector("queen")`
>
> Meaning is encoded as geometric proximity. Nobody programmed that — it emerges
> from training. Once you can map text → vector → similarity score, a dozen product
> features stop needing a custom ML model.

> "It fits" is the weakest reason to put data in a prompt.
> Cost, latency and retrieval quality all argue for sending *less*, not more.

Also in here: [the *Lost in the Middle* paper](foundation/token/lost_in_the_middle_research_paper.pdf),
which is the research behind why stuffing your whole corpus into a huge context window
produces *worse* answers than retrieving five good chunks.

## Quickstart

```bash
git clone https://github.com/DeepakAtariya/learn-llm-with-atariya.git
cd learn-llm-with-atariya

python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # macOS / Linux

pip install -r requirements.txt

cp .env.example .env            # then add your GROQ_API_KEY
```

A [Groq](https://console.groq.com/) key is free and is all you need for the chat
sections. Steps 2–4 run fully offline — `sentence-transformers` downloads a small
model once and embeds locally, no key, no API cost.

### Run something

```bash
# tokenization + cost math
python foundation/token/token.py

# a streaming chat REPL, provider-swappable
python foundation/chat_completion_api/provider_agnostic_wrapper.py
PROVIDER=ollama python foundation/chat_completion_api/provider_agnostic_wrapper.py
```

The embedding scripts share helpers via `from index import ...`, so run them from
inside their own directory:

```bash
cd foundation/embeddings
python similarity_ranking.py
```

## Repo map

```
foundation/
  token/                            1 · how text becomes numbers
    token.md                          the explainer
    token.py                          tiktoken, counting, cost
    lost_in_the_middle_research_paper.pdf
  embeddings/                       2-4 · how numbers carry meaning
    index.md                          the explainer
    index.py                          embed() + cosine_similarity()
    similarity_ranking.py             rank text by meaning
    mini_sematic_search.py            a tiny semantic search engine
    cluster_similar_text.py           KMeans over embeddings
    exercise_1.py, exercise_2.py      practice drills
  chat_completion_api/              5-7 · how you talk to a model
    plain_chat_completion.py          blocking request
    streaming_chat_completion.py      streamed + usage accounting
    provider_agnostic_wrapper.py      the provider abstraction
index.py                            8 · production patterns end to end
```

## Configuration

Everything is read from `.env` — see [`.env.example`](.env.example).

| Variable | Purpose | Default |
|----------|---------|---------|
| `PROVIDER` | Which backend to use: `groq` or `ollama` | `groq` |
| `GROQ_API_KEY` | Groq API key | — |
| `GROQ_MODEL` | Model served over Groq | `openai/gpt-oss-120b` |
| `OLLAMA_MODEL` | Local model via Ollama | `llama3.2` |
| `INPUT_PRICE` / `OUTPUT_PRICE` | USD per 1M tokens, for cost tracking | — |

`index.py` additionally reads `ANTHROPIC_API_KEY` from the environment.

## Roadmap

- [x] Tokenization and cost
- [x] Embeddings, similarity, ranking, clustering
- [x] Chat Completions — blocking and streaming
- [x] Provider-agnostic wrapper
- [ ] Chunking strategies
- [ ] Vector databases
- [ ] RAG end to end — hybrid search, re-ranking
- [ ] Tool use / function calling
- [ ] Agents and evaluation

## Contributing

Corrections to the notes are especially welcome — if an explainer is wrong or unclear,
that's a bug. Open an issue or a PR.

## License

[MIT](LICENSE) © Deepak Atariya
