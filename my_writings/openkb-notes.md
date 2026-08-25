# OpenKB notes

Everything I have written about [OpenKB](https://github.com/VectifyAI/OpenKB) while
running it locally — how it indexes documents, how it answers questions, and what
went wrong along the way.

I keep these as separate pieces rather than one long page: each one stands on its
own and is linked from [My Writings](README.md) directly, so they can be read in
any order. This page is just the way in when I want the whole OpenKB picture.

---

## Pieces

| # | Piece | What it covers |
|---:|------|----------------|
| 1 | [OpenKB sample usage and setting](openkb-sample-usage-and-settings.md) | Two side-by-side `openkb add` runs on the same 758-page PDF (`kb3` vs `kb4`), OpenRouter vs. OpenAI backends, and what each cost. |
| 2 | [OpenKB troubleshooting notes — Bishop PRML compile across kb2/kb3/kb4](openkb-qa-notes.md) | Why concept/entity counts start low in a fresh KB, what `accuracy: 99.65%` / `fix_incorrect_toc` actually means, and the `litellm.RateLimitError` retries. |
| 3 | [How retrieval works in OpenKB](openkb-retrieval-explained.md) | The query path traced end to end: no vector DB, no embeddings — three tools and a system prompt that makes the LLM the search engine. |

---

## The short version

- **Indexing.** Long documents go through PageIndex — TOC detection, page-number
  verification (`verify_toc`), then a compile pass that writes an overview,
  concepts and entities into the wiki. See piece 1 for a real run, piece 2 for
  what the log lines mean.
- **Retrieval.** There is no search index at all. The query agent gets
  `read_file`, `get_page_content` and `get_image`, and navigates `index.md` the
  way a person uses a book's table of contents. See piece 3.
- **Variance is expected.** The same book compiled with the same model on two
  different runs produced different concept names and entity counts — the
  concepts-plan step is an LLM judgment call, not deterministic extraction.

---

## Things to investigate

- [ ] Whether concept/entity richness really does keep growing as a KB fills up, or plateaus.
- [ ] How the navigational retrieval holds up on a KB with many documents, where `index.md` gets long.
- [ ] Whether `list_wiki_files` being withheld from the query agent is a deliberate cost control or a correctness one.

---

[← My Writings](README.md) | [← Home](../README.md)
