# How Retrieval Works in OpenKB

An analysis of the query/retrieval path in this codebase, anchored to a real
traced query run against `kb4` (Bishop, *Pattern Recognition and Machine
Learning*, 758 pages, model `openai/qwen/qwen3.7-flash` via OpenRouter).

---

## 1. The headline: there is no search index

OpenKB has **no vector DB, no embeddings, and no grep**. A repo-wide search for
`embedding|vector|faiss|chroma|sentence_transformer` hits only prose in the
README and an unrelated fuzzy-match helper in `lint.py` (a wikilink repair
utility, not retrieval).

The query agent is given exactly **three tools** (`openkb/agent/query.py:120`):

```python
tools=[read_file, get_page_content, get_image]
```

That is the entire retrieval surface. Note that `list_wiki_files` exists in
`openkb/agent/tools.py:17` but is deliberately **not** attached to the query
agent — it is wired only into the linter. The Q&A agent cannot even enumerate a
directory; it must go through `index.md`.

**The LLM is the search engine.** Retrieval is *navigational*, the way a human
uses a book: read the table of contents, follow it to a chapter, then flip to
the specific page.

---

## 2. Call flow: `openkb query "..."`

```
cli.py:1230          query(ctx, question, save, raw)
  ├ 1232  _find_kb_dir()                  locate KB (walks up for .openkb/)
  ├ 1239  resolve_effective_config()      DEFAULT → global.yaml → KB config.yaml
  ├ 1240  _setup_llm_key(kb_dir)          load .env, set litellm.api_key + provider keys
  ├ 1243  _stream_to_tty()                streaming ON only if stdout is a TTY
  └ 1245  run_query(question, kb_dir, model, stream, raw)
            │
            query.py:352  run_query()
              ├ 387  build_query_agent(wiki_root, model, language)
              │        ├ schema.py:66  get_agents_md(wiki_dir)   ← reads wiki/AGENTS.md from DISK
              │        ├ 64   instructions = _QUERY_INSTRUCTIONS_TEMPLATE.format(schema_md=…)
              │        ├ 67   @function_tool read_file(path)           → tools.py:41  read_wiki_file
              │        ├ 75   @function_tool get_page_content(doc,pgs) → tools.py:94  get_wiki_page_content
              │        ├ 86   @function_tool get_image(path)           → tools.py:148 read_wiki_image
              │        └ 117  Agent(name="wiki-query", model=f"litellm/{model}")
              └ 391  Runner.run(agent, question, max_turns=50)   ← the agentic loop
```

Afterwards `cli.py:1252` appends the question to `wiki/log.md`, and `--save`
writes the answer to `wiki/explorations/`.

### Function-by-function

| Function | File:line | What it does |
|---|---|---|
| `query()` | `cli.py:1230` | Click command. Resolves KB, config, model, credentials; delegates to `run_query`. |
| `_find_kb_dir()` | `cli.py:289` | Locates the KB root (walks up looking for `.openkb/`). Bails with "No knowledge base found" if absent. |
| `resolve_effective_config()` | `config.py` | Layers `DEFAULT_CONFIG` → `~/.config/openkb/global.yaml` → `<kb>/.openkb/config.yaml`. Source of `model:` and `language:`. |
| `_setup_llm_key()` | `cli.py:148` | Reads KB `.env` then global `.env`; sets `litellm.api_key` and provider-specific `*_API_KEY`; applies extra headers / timeout / litellm globals. |
| `_stream_to_tty()` | `cli.py:1157` | `sys.stdout.isatty()`. Streaming (and the live tool-call display) is auto-disabled when piped. |
| `run_query()` | `query.py:352` | Builds the agent, then either `Runner.run` (non-stream) or `Runner.run_streamed` (TTY, with Rich live rendering). |
| `build_query_agent()` | `query.py:56` | Composes the system prompt and wraps the three raw tool functions as `@function_tool` closures over `wiki_root`. |
| `get_agents_md()` | `schema.py:66` | Reads `wiki/AGENTS.md` from disk, falling back to the bundled `AGENTS_MD` constant. |
| `Runner.run()` | Agents SDK | Runs the agentic loop, `max_turns=50` (`query.py:20`). |

### Related entry points

- **Chat REPL** — `cli.py:2333` → `chat.py:1002 run_chat()` → `chat.py:338 _run_turn()`.
- **REST API** — `POST /api/v1/query` (`api.py:271`) reuses the *same*
  `build_query_agent` + `iter_agent_response_events` (`api_helpers.py:427`).
  The Workbench's "retrieval steps" inspector is just the streamed `tool_call`
  events rendered in a pane.

---

## 3. The system prompt *is* the retrieval algorithm

This is the key insight. There is no retrieval code — the algorithm is written
in English at `query.py:22-53` and executed by the model:

```
## Search strategy
1. Read index.md to see all documents and concepts with brief summaries.
   Each document is marked (short) or (pageindex) to indicate its type.
2. Read relevant summary pages (summaries/) for document overviews.
   Summaries may omit details — if you need more, follow the summary's
   `full_text` frontmatter field to the source (see step 4).
3. Read concept pages (concepts/) for cross-document synthesis.
4. For "who/what is X" questions about a specific named person, organization,
   place, or product, read the matching page in entities/ first.
5. When you need detailed source document content, each summary page has a
   `full_text` frontmatter field with the path to the original document content:
   - Short documents (doc_type: short): read_file with that path.
   - PageIndex documents (doc_type: pageindex): use get_page_content(doc_name, pages)
     with tight page ranges. The summary shows document tree structure with page
     ranges to help you target. Never fetch the whole document.
6. Source content may reference images. ... Pass either form as seen to the
   get_image tool — it accepts both.
7. Synthesize a clear, concise, well-cited answer grounded in wiki content.

Answer based only on wiki content. Be concise.
Before each tool call, output one short sentence explaining the reason.

If you cannot find relevant information, say so clearly.
```

`{schema_md}` is your `wiki/AGENTS.md`, read **from disk at runtime**
(`schema.py:66-79`) — so editing `<kb>/wiki/AGENTS.md` live-edits the agent's
system prompt. That is a real per-KB customization hook, not just a seed file.

A `\n\nIMPORTANT: Answer in {language} language.` line is appended at
`query.py:65`.

---

## 4. A real traced query

Query deliberately chosen so the summaries could **not** answer it:

> *"On which specific page does Bishop first define the sum-of-squares error
> function for polynomial curve fitting, and what is the exact equation number?
> Quote the surrounding text."*

Trace captured via `iter_agent_response_events` (`query.py:143`):

```
=== MODEL: openai/qwen/qwen3.7-flash   LANG: en
=== TOOLS EXPOSED TO LLM: ['read_file', 'get_page_content', 'get_image']

--- TOOL CALL #1: read_file({"path": "index.md"})
--- TOOL CALL #2: read_file({"path": "summaries/Bishop-Pattern-Recognition-and-Machine-Learning-2006.md"})
--- TOOL CALL #3: get_page_content({"doc_name": "Bishop-...", "pages": "28-31"})
--- TOOL CALL #4: get_page_content({"doc_name": "Bishop-...", "pages": "24-27"})

=== TOTAL TOOL CALLS: 4
=== HISTORY ITEMS (agent loop turns): 16
```

It answered correctly: equation **(1.2)**, printed page 5, with the surrounding
text quoted verbatim from the PDF.

### Where the agentic reasoning shows up

Watch calls **#3 → #4**. The summary said
`## Example: Polynomial Curve Fitting (pages 24–32)`, so the model guessed
`28-31` first, read those pages, did **not** find the definition, and corrected
itself *backwards* to `24-27`.

Nothing in the code told it to do that. There is no retry logic, no re-ranker,
no fallback query expansion. The loop is simply:

```
model emits tool call → SDK executes it → result appended to context
                     → model decides again → … (up to max_turns=50)
```

Self-correction, range refinement, and deciding when it has enough evidence are
all emergent from the model reasoning over accumulated tool results. That *is*
the "agentic behaviour" — it lives in the model, not in OpenKB code.

---

## 5. How the source-document drill-down is triggered

The trigger is a **frontmatter pointer** on the summary page. `kb4`'s summary
begins:

```yaml
---
type: "Summary"
description: "This comprehensive academic textbook by Christopher M. Bishop ..."
doc_type: pageindex
full_text: "sources/Bishop-Pattern-Recognition-and-Machine-Learning-2006.json"
---

# Preface (pages 1–6)
Summary: ...

## Example: Polynomial Curve Fitting (pages 24–32)
Summary: ...
```

Two things make the drill-down work:

1. **`full_text:` + `doc_type:`** tell the agent *where* the raw source lives
   and *which tool* to use — `read_file` for `doc_type: short`,
   `get_page_content` for `doc_type: pageindex`. Emitted by
   `compiler.py:950-955` and `tree_renderer.py:13-14`.
2. **The page ranges in every heading** are the navigational map. That is how
   the model knows to ask for `24-32` rather than guessing blindly across 749
   pages. Rendered at `tree_renderer.py:29-33`.

The `(short)` / `(pageindex)` marker in `index.md` comes from
`compiler.py:1522-1523`, explicitly so "the query agent knows how to access
detailed content".

### What `get_page_content` actually does

`openkb/agent/tools.py:94-135` — almost comically simple:

```python
data = _json.loads(target.read_text(encoding="utf-8"))   # load all 749 pages
requested = set(parse_pages(pages))                       # "24-27" → {24,25,26,27}
matches = [entry for entry in data if entry.get("page") in requested]
```

It loads the whole ~1.8 MB JSON into Python, filters by page number, and returns
`[Page 24]\n<text>` blocks (plus an `[Images: ...]` line when the page has
figures).

**The filtering happens in Python, not in the LLM's context.** That is the whole
trick: the model only ever sees ~4 pages, never 749. The prompt's hard rule
(`query.py:41`) is *"Never fetch the whole document."*

`parse_pages` (`tools.py:60`) is a deliberately tolerant parser for specs like
`"3-5,7,10-12"`; malformed segments are silently skipped.

### The source JSON shape

Written at ingest by `indexer.py:101-108` — a flat array, one entry per page:

```json
[
  {"page": 1, "content": "...markdown text...", "images": [{"path": "sources/images/<doc>/p1_img1.png"}]},
  {"page": 2, "content": "...", "images": []}
]
```

Matching is by the `"page"` **field value**, not array position. Requested pages
that don't exist are silently dropped, and output order follows file order
rather than the order requested.

---

## 6. Where PageIndex actually fits (a surprise)

The naming is misleading, so this is worth stating plainly:
**`.openkb/pageindex.db` is never opened at query time.**

Every reference to it lives in `cli.py` (add / remove / cleanup paths) and
`indexer.py` (ingest). Nothing under `openkb/agent/`, `openkb/api*.py`, or
`openkb/skill/` imports PageIndex or walks tree nodes.

```
INGEST TIME                                ANSWER TIME
  PDF ≥ pageindex_threshold
    → indexer.index_long_document
      → PageIndexClient(.openkb/pageindex.db)  ──X   (never opened again)
        ├ col.add() / col.get_document()
        │   → tree{structure}
        │     → tree_renderer.render_summary_md()
        │       → wiki/summaries/<doc>.md ──────►  read_file("index.md")
        │                                          read_file("summaries/<doc>.md")   ← TREE AS MARKDOWN
        │                                               model picks page ranges
        └ pages[] → wiki/sources/<doc>.json ────►  get_page_content(doc, "24-27")
                                                        → Python filters the JSON
                                                   get_image(...)
```

The hierarchical tree is **flattened once, at ingest, into Markdown headings**
and that rendered text is the only form the LLM ever sees. The structured tree
survives only inside `pageindex.db` (write-mostly ingest state) and as those
headings.

So **"vectorless, reasoning-based retrieval"** in this codebase means literally:
*the model reads a rendered outline and reasons about which pages to open.*
There is no programmatic tree search at all.

Documented intent, `README.md:151`:

> Short documents are read in full by the LLM. Long PDFs are processed by
> PageIndex into a hierarchical tree index. The LLM reads the tree instead of
> the full text, enabling accurate and scalable retrieval for long documents.

---

## 7. The tool layer in full

`openkb/agent/tools.py` — functions are deliberately **undecorated** so each
agent wraps them with its own descriptions and root paths (per `CLAUDE.md`).
Every one is root-jailed with `is_relative_to()`.

| Function | Line | Exposed to query agent as | Purpose |
|---|---|---|---|
| `list_wiki_files` | 17 | ✗ (linter only) | List `.md` in a wiki subdir (non-recursive). |
| `read_wiki_file` | 41 | `read_file(path)` | Whole-file read under `wiki/`. No offset/limit. |
| `parse_pages` | 60 | — (helper) | Parse `"3-5,7"` → `[3,4,5,7]`. |
| `get_wiki_page_content` | 94 | `get_page_content(doc_name, pages)` | **The source drill-down primitive.** |
| `read_wiki_image` | 148 | `get_image(image_path)` | Base64 data URL, dual path resolution. |
| `read_kb_file` | 182 | ✗ (skill runner) | Allow-listed to `wiki/`, `output/`, `skills/`. |
| `write_kb_file` | 214 | ✗ (chat only) | Allow-listed to `wiki/explorations/**`, `output/**`. |
| `write_wiki_file` | 259 | ✗ | Atomic write; currently unused by agent builders. |

Wrapping happens as closures inside `build_query_agent`, so the LLM-facing
signature drops the `wiki_root` parameter entirely.

---

## 8. Chat vs. one-off query

`build_chat_agent` (`query.py:211`) is the **same agent, cloned with extras**:

```python
base = build_query_agent(wiki_root, model, language=language, bundle=bundle)
...
return base.clone(tools=[*base.tools, *extra_tools], instructions=new_instructions)
```

Differences:

1. **Sandboxed `write_file`** — only `wiki/explorations/**` and `output/**`.
2. **Skill discovery** — `list_skills` / `read_skill` function tools, plus an
   "## Available skills" prompt addendum. (Plain `function_tool` primitives are
   used because LiteLLM routes through ChatCompletions, which rejects the SDK's
   hosted `ShellTool`.)
3. **Persistent multi-turn history** in `.openkb/chats/<id>.json`. History is
   re-sent whole each turn (`chat.py:355`), with image data URLs stripped before
   persisting (`chat_session.py:21`) so session files don't bloat.
4. **A REPL** with slash commands (`/save`, `/clear`, `/lint`, `/skill`, …).

---

## 9. What this design buys and costs

**Buys**

- No embedding cost, no index staleness, no chunking artifacts.
- Exact page-level citations (the model quotes real page text).
- Full transparency — every retrieval step is a visible tool call, streamable
  to the CLI or as SSE to the Workbench.
- Handles a 758-page book inside a ~60K-token context because it never loads
  more than a few pages at a time.

**Costs**

- Retrieval quality is bounded entirely by **summary quality**. If the compile
  step wrote a vague summary or mislabeled a page range, the agent has no
  fallback — it cannot grep for a phrase it knows should exist.
- This is why compile-time differences matter more than they appear: the
  compiled `index.md` and summary pages **are** the search index, so a weaker
  compile permanently degrades retrieval for that KB.
- Every retrieval step is an LLM round-trip, so latency scales with the number
  of navigation hops.

**Model-compliance note:** the prompt asks the model to "output one short
sentence explaining the reason" before each tool call (`query.py:50`).
`qwen3.7-flash` ignored that in the traced run and emitted only the final
answer. A stronger model narrates its reasoning, which makes the retrieval path
much easier to follow live.

---

## 10. Reproducing the trace

Streaming (and the live tool-call display) is disabled when stdout is not a TTY,
so piping `openkb query` hides the tool calls. To capture them, drive the
module's own event iterator directly:

```python
import asyncio, sys
from pathlib import Path
from openkb.config import resolve_effective_config, resolve_credential_bundle
from openkb.agent.query import (
    build_query_agent, iter_agent_response_events, build_run_config_from_bundle,
)

KB, QUESTION = Path(sys.argv[1]), sys.argv[2]

async def main():
    config = resolve_effective_config(KB)[0]
    model, language = config.get("model"), config.get("language", "en")
    bundle = resolve_credential_bundle(KB)
    agent = build_query_agent(str(KB / "wiki"), model, language=language, bundle=bundle)
    print("TOOLS:", [t.name for t in agent.tools])
    run_config = build_run_config_from_bundle(model, bundle)
    async for ev in iter_agent_response_events(agent, QUESTION, run_config=run_config):
        if ev["event"] == "tool_call":
            print(f"--- {ev['data']['name']}({ev['data']['arguments']})")

asyncio.run(main())
```

Run as: `python trace_query.py D:\git_repos\OpenKB\kb4 "your question"`

Alternatively, just run `openkb query "..."` directly in an interactive terminal
(not piped) — the tool-call lines render live in colour.

---

[← OpenKB notes](openkb-notes.md) | [← My Writings](README.md) | [← Home](../README.md)
