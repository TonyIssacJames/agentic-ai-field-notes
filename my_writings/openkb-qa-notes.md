# OpenKB troubleshooting notes — Bishop PRML compile across kb2/kb3/kb4

## Why concept/entity counts were low in `kb2` (first document in a fresh KB)

`kb2` compiled `Bishop-Pattern-Recognition-and-Machine-Learning-2006.pdf` as
its **first** document and produced only 3 concepts and 2 entities. This is
by design, not a bug or a model-quality issue.

The concepts-plan prompt (`openkb/agent/compiler.py`, `_CONCEPTS_PLAN_USER`,
around line 118) explicitly instructs the LLM:

> "For the first few documents, create 2-3 foundational concepts at most."
> "Roughly 5-15 entities per document is typical; fewer for sparse documents."

The compiler deliberately caps concept/entity creation for a near-empty wiki
so it doesn't fragment into dozens of tiny pages before there's a body of
documents to cross-link against. Since `kb2` had `Total indexed: 1
document(s)` at compile time, the strictest form of this cap applied.

**Confirmed by comparison:** in an earlier GPT-5-series run of the same
book, Bishop was **document #2** in that KB (a short, non-pageindexed doc
was added first). By the time Bishop compiled there, the concepts-plan call
already had `concept_briefs`/`entity_briefs` from document #1, and the
"first few documents" cap had loosened — producing a richer concept/entity
set. So the difference tracked **document position in the KB**, not the
model.

**Takeaway:** counts should grow organically as more documents are added to
a KB — new concepts get created more freely once there's an existing base,
and cross-document topics get `update`d/cross-linked rather than staying
siloed per document.

Separately, `kb2` (`qwen/qwen3.7-flash`, run 1) vs. `kb3`
(`gpt-4o-mini`) vs. `kb4` (`qwen/qwen3.7-flash`, run 2) all produced
**different** concept names and entity counts for the exact same book, even
kb2 vs. kb4 on the identical model. This is expected: the concepts-plan step
is an LLM judgment call ("what are this document's foundational ideas /
which entities are central"), not a deterministic extraction — different
models, and even different runs of the same model, will reasonably carve up
the same content differently (e.g. "bayesian-methods /
mixture-models / variational-inference" vs. "probabilistic-graphical-models
/ latent-variable-models / bayesian-inference" — different but overlapping
framings of the same material).

---

## What `accuracy: 99.65%` / `fix_incorrect_toc` means

Seen in both `kb3` and `kb4` runs (same book, same TOC structure, so it
shows up identically regardless of KB/model):

```
start verify_toc
check all items
accuracy: 99.65%
start fix_incorrect_toc
Fixing 1 incorrect results
start fix_incorrect_toc with 1 incorrect results
```

This is PageIndex's TOC-verification step
(`pageindex/index/page_index.py: verify_toc`), part of building the
hierarchical page-index tree for a long document:

- For every extracted table-of-contents entry, PageIndex asks the LLM to
  confirm that entry's assigned physical page number actually contains that
  section (catches TOC→PDF page-mapping errors — common around unnumbered
  front matter, roman-numeral pages, appendices).
- `accuracy: 99.65%` = (verified-correct entries / total entries). With a
  758-page book producing several hundred TOC entries, 1 wrong mapping
  yields ~99.65%. This is normal — a 750+ page book rarely verifies at a
  clean 100%.
- `fix_incorrect_toc` / `Fixing 1 incorrect results` — for the ~0.35% that
  failed verification, PageIndex re-asks the LLM specifically about those
  entries to correct the page number, then re-validates. This is a
  **self-healing step**, not a failure — the run continues regardless and
  completed successfully in both `kb3` and `kb4`.

---

## What the `litellm.RateLimitError` retry warnings mean (seen in `kb4`)

```
pageindex.index.utils WARNING: Retrying async LLM completion (1/10)
pageindex.index.utils ERROR: Error: litellm.RateLimitError: RateLimitError: OpenAIException - Provider returned error
```

This is a `429`-style throttling response from whichever backend was
actually serving `qwen/qwen3.7-flash` through OpenRouter (Alibaba's hosted
endpoint) — it rejected some requests because too many arrived too close
together. It comes from PageIndex's `llm_acompletion()`
(`pageindex/index/utils.py`), which wraps every call in a retry loop: up to
10 attempts, with a 1-second sleep between each. In this run it recovered
within 1-2 retries both times the error appeared — the run proceeded to
`accuracy: 99.65%` and then to a successful compile. Had all 10 retries
failed, PageIndex would have degraded that one step to an empty result
rather than crashing (same file's graceful-degradation behavior).

Why `kb4` hit this and the earlier `kb2` run of the same model didn't:
OpenRouter-routed rate limits are provider-side and can vary run to run
based on load on that backend at that moment — nothing in the KB's config
caused it.

**Speed/reliability comparison, same book, different provider paths:**

| KB | Model | `overview` | `concepts-plan` | Rate-limit errors |
|---|---|---|---|---|
| `kb3` | `gpt-4o-mini` (real OpenAI infra via OpenRouter) | 8.3s | 1.8s | none |
| `kb4` | `qwen/qwen3.7-flash` (OpenRouter → Alibaba host) | 20.6s | 12.2s | yes (recovered) |

Real OpenAI-hosted models routed through OpenRouter were both faster and
had zero rate-limit errors compared to an open-weight model routed to a
third-party host — consistent with OpenRouter passing OpenAI-brand models
straight through to OpenAI's own infrastructure, while other models depend
on whichever backend host OpenRouter picks and that host's own capacity.

---

[← OpenKB notes](openkb-notes.md) | [← My Writings](README.md) | [← Home](../README.md)
