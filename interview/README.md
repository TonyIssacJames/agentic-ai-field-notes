# Interview

Interview-prep images saved into the repo itself. Click a file name to view or download it on GitHub.

> ## 🎤 10 question cards saved so far
>
> _Last added: FDE Interview — Questions 1–50 (10 cards) — 2026-10-24_

These are files, not links, so they do **not** appear in [history.md](../history.md) and do not count toward the artifact total.

---

## Forward Deployed Engineer (FDE) interview — 50 questions

A 10-card slide set of 50 interview questions for a forward deployed engineer role (customer-facing AI/LLM engineering): latency and cost, RAG, agents and safety, evals and monitoring, data engineering, deployment constraints, and handling customers. The cards are numbered 1/10 to 10/10 and point to a longer bundle "in the caption" of the original post, which was not saved, so the author is not recorded here. The files are named by upload order; the table below is in question order.

| # | Card | Topics | Added |
|---:|------|--------|-------|
| 1 | [FDE Interview — Questions 1–5](1792483947570.jpg) | LLM app too slow (five latency changes and what they cost), prompting vs RAG vs fine-tuning, architectures when no PII may reach an external API, why LLMs hallucinate, KV and prefix caching | 2026-10-24 |
| 2 | [FDE Interview — Questions 6–10](1792483947185.jpg) | 10× cost reduction, evals that catch silent regressions after a model upgrade, a multi-model router or cascade, self-hosting an open-weight model vs paying an API, growing a RAG proof of concept from 50 to 5M docs | 2026-10-24 |
| 3 | [FDE Interview — Questions 11–15](1792483947074.jpg) | Diagnosing a RAG pilot that is 60% correct, hybrid retrieval (BM25, vectors, reranker), RAG over 10M documents with per-user access control, right passage but wrong answer, when not to build an agent | 2026-10-24 |
| 4 | [FDE Interview — Questions 16–20](1792483947436.jpg) | Prompt injection on an agent that reads email and issues refunds, safe retries and idempotency after side effects, a $2,000 overnight bill and kill-switches, text-to-SQL numbers wrong 1 in 5 times, the production improvement loop | 2026-10-24 |
| 5 | [FDE Interview — Questions 21–25](1792483946898.jpg) | Scaled dot-product attention and top-p sampling from scratch, near-duplicate documents in a 1TB corpus, a skewed Spark join, a CDC pipeline from an ERP into a lakehouse, bias–variance in a real deployment | 2026-10-24 |
| 6 | [FDE Interview — Questions 26–30](1792483947012.jpg) | A model that dropped from 95% in the pilot to 70% in production, turning "an accurate model" into a measurable requirement, classical ML vs an LLM, debugging 30 customer environments you can't SSH into, pilot to a 99.9% SLA | 2026-10-24 |
| 7 | [FDE Interview — Questions 31–35](1792483947121.jpg) | LLM observability, tracing a multi-step agent that is plausibly wrong, a four-hour provider outage against a 99.9% SLA, deploying into an air-gapped audited environment, a vague "design a fraud detection system" | 2026-10-24 |
| 8 | [FDE Interview — Questions 36–40](1792483947163.jpg) | A long-running LLM job queue, sizing GPUs without seeing the environment, turning a vague request into a plan, a bank VP saying "we need a chatbot", a CTO saying "AI doesn't work" | 2026-10-24 |
| 9 | [FDE Interview — Questions 41–45](1792483947144.jpg) | Customer-facing situations: "guarantee 100% accuracy?", a failed live demo, a pilot that missed its success criteria ten days before go/no-go, a sales promise for a capability that doesn't exist, your first 30/60/90 days | 2026-10-24 |
| 10 | [FDE Interview — Questions 46–50](1792483947015.jpg) | A model that looked good offline but failed live, defense-in-depth for a refund-issuing support agent, an agent with 18 tools picking the wrong one, a POC that tests the riskiest assumptions, integrations that are safe and traceable when an operation repeats | 2026-10-24 |

There is also a closing slide, [Could you answer all 50?](1792483948188.jpg), which is only a call to like, repost and follow, so it is not counted as a card.

---

## How to add an image

1. Drop the image into this folder.
2. Add a row to the table, numbered `previous + 1`, and bump the count in the banner.
3. Do **not** add a row to `history.md` — that file is for links.

---

[← Home](../README.md)
