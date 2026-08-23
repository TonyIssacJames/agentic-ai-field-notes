# OpenKB sample usage and setting

Two side-by-side runs of `openkb add` on the same PDF, in two different knowledge bases (`kb4` and `kb3`), with two different LLM backends — one via OpenRouter, one via OpenAI directly.

```bash
(.venv) D:\git_repos\OpenKB\kb4>openkb add ..\examples\docs\Bishop-Pattern-Recognition-and-Machine-Learning-2006.pdf
Adding: Bishop-Pattern-Recognition-and-Machine-Learning-2006.pdf
  Long document detected — indexing with PageIndex...
start find_toc_pages
toc found
start detect_page_index
index found
process_toc_with_page_numbers
start_index: 1
start toc_transformer
start toc_index_extractor
Document validation: 758 pages, max allowed index: 758
start verify_toc
check all items
pageindex.index.utils WARNING: Retrying async LLM completion (1/10)
pageindex.index.utils ERROR: Error: litellm.RateLimitError: RateLimitError: OpenAIException - Provider returned error
pageindex.index.utils WARNING: Retrying async LLM completion (1/10)
pageindex.index.utils ERROR: Error: litellm.RateLimitError: RateLimitError: OpenAIException - Provider returned error
accuracy: 99.65%
start fix_incorrect_toc
Fixing 1 incorrect results
start fix_incorrect_toc with 1 incorrect results
pageindex.index.utils WARNING: Retrying async LLM completion (1/10)
pageindex.index.utils ERROR: Error: litellm.RateLimitError: RateLimitError: OpenAIException - Provider returned error
pageindex.index.utils WARNING: Retrying async LLM completion (1/10)
pageindex.index.utils ERROR: Error: litellm.RateLimitError: RateLimitError: OpenAIException - Provider returned error
pageindex.index.utils WARNING: Retrying async LLM completion (1/10)
pageindex.index.utils ERROR: Error: litellm.RateLimitError: RateLimitError: OpenAIException - Provider returned error
  Compiling long doc (doc_id=f8e618fa-e671-4575-9ba7-ebfbd0ab47d5)...
    overview.................... 20.6s (in=59497, out=3167)
    concepts-plan............ 12.2s (in=60953, out=2321, cached=58112)
    Generating 3 concept(s) (concurrency=3)...
    Generating 7 entity(ies) (concurrency=3)...
    concept: probabilistic-graphical-models... 19.8s (in=60866, out=2965)
    concept: latent-variable-models... 22.7s (in=60864, out=3848)
    concept: bayesian-inference... 28.1s (in=60866, out=5274, cached=58112)
    entity: christopher-m-bishop... 15.3s (in=60902, out=2795, cached=59264)
    entity: pattern-recognition-and-machine-learning... 17.7s (in=60903, out=2839)
    entity: springer... 14.1s (in=60899, out=2056)
    entity: microsoft-research... 11.5s (in=60900, out=1797, cached=59264)
    entity: michael-jordan... 11.5s (in=60900, out=2004, cached=59264)
    entity: jon-kleinberg... 10.7s (in=60901, out=1172)
    entity: bernhard-schölkopf... 13.0s (in=60903, out=2136)
  [OK] Bishop-Pattern-Recognition-and-Machine-Learning-2006.pdf added to knowledge base.


(.venv) D:\git_repos\OpenKB\kb4>openkb list
Documents (1):
  Name                                     Type         Pages
  ---------------------------------------- ------------ --------
  Bishop-Pattern-Recognition-and-Machine-Learning-2006.pdf pageindex

Summaries (1):
  - Bishop-Pattern-Recognition-and-Machine-Learning-2006

Concepts (3):
  - bayesian-inference
  - latent-variable-models
  - probabilistic-graphical-models

Entities (7):
  - bernhard-schölkopf
  - christopher-m-bishop
  - jon-kleinberg
  - michael-jordan
  - microsoft-research
  - pattern-recognition-and-machine-learning
  - springer

(.venv) D:\git_repos\OpenKB\kb4>


(.venv) D:\git_repos\OpenKB\kb4>openkb status
Knowledge base: D:\git_repos\OpenKB\kb4

Knowledge Base Status:
  Directory            Files
  -------------------- ----------
  sources              0
  summaries            1
  concepts             3
  entities             7
  reports              0
  raw                  1

  Total indexed: 1 document(s)
  Last compile:  2026-08-23 08:18:38

D:\git_repos\OpenKB\kb4\.env:

LLM_API_KEY=sk-or-v1
OPENAI_API_BASE=https://openrouter.ai/api/v1/


D:\git_repos\OpenKB\kb4\.openkb\config.yaml:
language: en
model: openai/qwen/qwen3.7-flash
pageindex_threshold: 20
concurrency: 3


API Key used: OpenKB_testing
Usage: $0.453 (for using it two times for indexing the same Bishop-Pattern-Recognition-and-Machine-Learning-2006)
=========================================================
(.venv) D:\git_repos\OpenKB\kb3>openkb add ..\examples\docs\Bishop-Pattern-Recognition-and-Machine-Learning-2006.pdf
openkb.locks WARNING: Rolled back interrupted add journal 6740a28392454f3ba3c2f340bc4a01db.json.
Adding: Bishop-Pattern-Recognition-and-Machine-Learning-2006.pdf
  Long document detected — indexing with PageIndex...
start find_toc_pages
toc found
start detect_page_index
index found
process_toc_with_page_numbers
start_index: 1
start toc_transformer
start toc_index_extractor
Document validation: 758 pages, max allowed index: 758
start verify_toc
check all items
accuracy: 99.65%
start fix_incorrect_toc
Fixing 1 incorrect results
start fix_incorrect_toc with 1 incorrect results
  Compiling long doc (doc_id=66c25175-ad03-497e-8e66-3bf4cbc7e980)...
    overview........ 8.3s (in=55941, out=618)
    concepts-plan. 1.8s (in=57012, out=119, cached=56448)
    Generating 3 concept(s) (concurrency=2)...
    concept: mixture-models... 5.3s (in=56870, out=514, cached=56320)
    concept: bayesian-methods... 5.6s (in=56869, out=550, cached=56704)
    concept: variational-inference... 26.0s (in=56871, out=415, cached=56320)
  [OK] Bishop-Pattern-Recognition-and-Machine-Learning-2006.pdf added to knowledge base.

(.venv) D:\git_repos\OpenKB\kb3>openkb list
Documents (1):
  Name                                     Type         Pages
  ---------------------------------------- ------------ --------
  Bishop-Pattern-Recognition-and-Machine-Learning-2006.pdf pageindex

Summaries (1):
  - Bishop-Pattern-Recognition-and-Machine-Learning-2006

Concepts (3):
  - bayesian-methods
  - mixture-models
  - variational-inference


(.venv) D:\git_repos\OpenKB\kb3>openkb status
Knowledge base: D:\git_repos\OpenKB\kb3

Knowledge Base Status:
  Directory            Files
  -------------------- ----------
  sources              0
  summaries            1
  concepts             3
  entities             0
  reports              0
  raw                  1

  Total indexed: 1 document(s)
  Last compile:  2026-08-22 14:53:44





(.venv) D:\git_repos\OpenKB\kb3>

D:\git_repos\OpenKB\kb3\.env:

LLM_API_KEY=sk-proj-

D:\git_repos\OpenKB\kb3\.openkb\config.yaml:
language: en
model: gpt-4o-mini
pageindex_threshold: 20
concurrency: 2


API Key used: llmfn
Usage: $0.52 (for indexing the Bishop-Pattern-Recognition-and-Machine-Learning-2006)
```

---

## Comments

- Same PDF, same PageIndex pipeline (TOC detection → page-index verification → long-doc compile), two different backends: `kb4` runs `openai/qwen/qwen3.7-flash` through OpenRouter (`OPENAI_API_BASE` pointed at `openrouter.ai`), `kb3` runs `gpt-4o-mini` directly against OpenAI.
- `kb4`'s run hit `litellm.RateLimitError` retries repeatedly during TOC verification and entity extraction, yet still finished with `[OK]` — the retry logic absorbed the rate limiting rather than failing the add. `kb3`'s run shows no retries at all.
- `kb4` extracted both concepts (3) and entities (7) from the document; `kb3` extracted only concepts (3) and zero entities. Same source PDF, same `pageindex_threshold: 20` — the difference in what gets extracted looks like it's coming from the model, not the pipeline.
- Cost: $0.453 for two indexing runs on `kb4` (OpenRouter/Qwen) vs $0.52 for one run on `kb3` (OpenAI/gpt-4o-mini) — so the OpenRouter route was roughly 4x cheaper per run here, though this is one document, not a controlled benchmark.
- `kb3`'s log shows `Rolled back interrupted add journal ...json` at the start — evidence OpenKB journals in-progress adds and can recover/rollback a previously interrupted one before proceeding.

---

[← My Writings](README.md) | [← Home](../README.md)
