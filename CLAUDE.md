# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A personal, hand-maintained knowledge base of links, articles, videos, repositories, keywords, and topic notes — primarily about agentic AI, plus GPU/performance, digital verification, and computer architecture. It is **Markdown only**: there is no source code, no build, no test suite, no linter, and no package manifest. Do not add tooling, CI, or dependencies unless explicitly asked.

**This repo is a bookmarking system**, deliberately built on plain files in git so it can be read and edited from any machine without installing or signing into anything. Two consequences that should drive every decision here:

- **The GitHub web UI is the primary reader and editor.** Pages must be legible and navigable with no clone, no build, and no local tooling — hence plain Markdown, relative links, and the footer back-links that substitute for a file tree. Never propose a static-site generator, a search index, or anything requiring a checkout.
- **Capture is often rushed**, sometimes from a restricted machine, which is what `inbox.md` is for. A low-friction append beats correct filing at capture time; filing happens later.

The owner adds material continuously and commits often. Most requests will be about *filing new material* or *reorganizing existing pages*, not writing code.

## Structure and information flow

Two axes cross each other, and every page is one cell in that grid:

- **By material type** (root level): `inbox.md`, `links.md`, `articles.md`, `blogs.md`, `youtube.md`, `books.md`, `courses.md`, `tutorials.md`, `agentic-tools.md`, `repos.md`, `benchmarks.md`, `keywords.md`, `history.md`
- **By subject** (`topics/<topic>/`): `gpu`, `ai`, `dv`, `architecture`

The same four subject headings — `AI / LLM`, `GPU / Performance`, `Digital Verification`, `Computer Architecture` — repeat as `##`/`###` sections inside the root type pages. Keep that heading set and its wording consistent; it is what makes the grid navigable. `links.md` and `repos.md` add a `Tools` / `Tools / Documentation` section, and `links.md` a `Miscellaneous` one.

Intended flow, per `README.md`:

1. New material lands in `inbox.md` under **To Review**.
2. It graduates to the matching root type page, under the matching subject heading.
3. When a subject grows large, it gets a page under `topics/`.

So an item can legitimately appear both in a root type page and in a topic page — that is duplication by design, not an error to clean up.

### `history.md` — always update it

`history.md` is the one page that cuts across every type and topic: **every link added anywhere in this repo gets exactly one row there**, in the order it was added. It exists to make the collection's growth visible, so it is motivational, not just an index — the running count matters to the owner.

Adding a link is therefore always a **three-part edit**, and it is not done until all three are made:

1. The row on the destination page (`articles.md`, `youtube.md`, `repos.md`, `links.md`, or a topic page).
2. A new row appended at the **bottom** of the `history.md` table, numbered `previous + 1`.
3. The banner near the top of `history.md` — bump `N artifacts added so far` **and** set `_Last added:_` to the newest entry.

Never renumber or reorder existing rows; the log is append-only. **Check for a duplicate before appending** (below). Use `YYYY-MM-DD` dates and the `Type` / `Topic` vocabularies listed in that file. When adding a batch, append them all, then bump the count once by the batch size. If a milestone in that file is reached, tick its checkbox.

### Duplicate detection

**Check `history.md` and only `history.md`.** It holds exactly one row per artifact, so a repeat there is a genuine duplicate. Never sweep across all pages: the same link legitimately appears on a type page *and* a topic page, so a repo-wide comparison is nothing but false positives.

**A duplicate is the same resource reached by a different URL** — a YouTube link with and without `&t=`, the same book on `amazon.in` and `amazon.com`, a page with and without a trailing slash or `?utm_source=`. **Two items from the same site or author are NOT duplicates**; compare the full normalized path, never the domain. `…/prompting-techniques/` and `…/harness-engineering/` are separate articles that happen to share a host.

Normalize before comparing: drop the protocol, `www.`, trailing slashes and tracking params (`utm_*`, `ref`, `si`, `fbclid`, `gclid`, YouTube's `t`); lowercase. For hosts with a stable ID, that ID *is* the identity — YouTube `v=`, Amazon `/dp/<ASIN>`, GitHub `owner/repo`, arXiv paper ID. Keep meaningful fragments: the O'Reilly `#sigil_toc_id_137` marks a specific chapter.

**Before adding one link**, grep the distinctive token — the last path segment, or the ID for a known host:

```bash
grep -i "harness-engineering" history.md     # slug
grep -i "ff3W8SM4ScA"        history.md     # YouTube ID
grep -i "B0DYB2QCS7"         history.md     # Amazon ASIN
```

**Full sweep** (run occasionally, or after a bulk import) — prints one line per duplicated resource, nothing when clean:

```bash
grep -oE '\]\(https?://[^)]+\)' history.md | sed -E 's/^\]\(//; s/\)$//' |
  sed -E 's#^https?://##; s/^www\.//; s#/+$##;
          s/[?&](utm_[^&]*|ref|si|fbclid|gclid|t)=[^&]*//g;
          s#(youtube\.com/watch).*[?&]v=([A-Za-z0-9_-]+).*#\1/\2#;
          s#.*(amazon\.[a-z.]+)/.*/dp/([A-Z0-9]+).*#amazon/dp/\2#' |
  tr 'A-Z' 'a-z' | sort | uniq -d | sed 's/^/DUPLICATE: /'
```

**With no shell** (the owner adding from a restricted office machine): open `history.md` on GitHub and Ctrl+F the distinctive slug or ID — not the domain, which would match every sibling article.

**On a hit**, do not append a new row. Improve the existing entry instead — better description, a topic page it was missing from — and say which row number it already occupies.

## Conventions to follow when editing

- **Never display a raw URL.** Link text is a short human-readable label — the title, plus the author or source where it disambiguates (`[Renu's Blog (arshren)](…)`, `[AI Agents in Action — Ch. 10, Agent reasoning and evaluation](…)`). The owner wants something clickable and recognizable at a glance, never the URL itself, and never a bare `https://…` in body text.
- **Get the real title.** Do not infer a title from a URL slug. Fetch the page (or search for it) and use the actual title; if it can't be retrieved, say so rather than inventing one. Descriptions say what the thing *is* or why it's worth returning to — not a restatement of the title.
- **A new material type means a new root page**, not a subsection of an existing one — `books.md`, `blogs.md`, `benchmarks.md` and `courses.md` were each created this way. Give it the same four subject headings, a footer back-link, and an entry in the README's Quick Access list.
- **A page whose content is entirely one subject may group by kind instead.** `agentic-tools.md` uses Agent Harnesses / MCP Servers / Skills / IDE Extensions / Frameworks / Evaluation rather than the four subject headings, because everything on it is AI/LLM and three of the four headings would sit empty. This is the exception, not the pattern — the four subject headings remain the default for a new root page.
- **Narrower groupings go under a subject heading, not beside it.** `courses.md` has `### RAG` nested inside `## AI / LLM`. The four subject headings stay the top level on every page; a theme like RAG, Agents or Evals is an `###` within one of them.
- **Strip tracking parameters from a URL before saving it** — `utm_*`, `aff`, `hsa_*`, `ref`, `fbclid`, `si`. Keep meaningful ones (YouTube `v=`, a `#tableofcontents` anchor the owner deliberately linked to).
- **Entry format is per-page.** Match the placeholder rows already in the file being edited rather than importing a format from another page. `links.md` / `youtube.md` use `- [Title](url) — Short description`; `repos.md` uses a multi-line block with bold `**Why useful:**` / `**Look at:**` labels (two trailing spaces produce the line breaks — preserve them); `keywords.md` uses `- **Term** — notes`.
- **Reading/watching lifecycle.** `articles.md` and `youtube.md` are staged: `To Read` / `Watch Later` (unchecked `- [ ]`) → `Currently Reading` / `Currently Watching` (with Notes / Key idea / Follow-up sub-bullets) → `Worth Keeping` (permanent, filed by subject) → `Finished` (checked `- [x]` with the main takeaway). Move entries between stages instead of duplicating them.
- **Placeholder rows** (`[Example](https://example.com)`, `[Video title](https://youtube.com/)`, `owner/repository`, `Add notes`, `- [ ] Keyword`) are templates showing the shape of an entry. Replace them when a section gets its first real entry; leave the last one in an otherwise-empty section so the format stays visible.
- **Footer nav links are mandatory.** Every page ends with a `---` and relative back-links: root pages `[← Home](README.md)`; `topics/README.md` `[← Home](../README.md)`; topic pages `[← Topics](../README.md) | [← Home](../../README.md)`; topic subpages lead with `[← <Topic>](README.md)`. Copy the pattern from a sibling at the same depth.
- **New pages must be registered.** A new topic goes in `topics/README.md` (Main Topics) *and* `README.md` (Topic Areas). A new subpage goes in its topic `README.md` under Subtopics — replacing the `_placeholder_` line if one exists for it — and, for prominent ones, nested under the topic in `README.md`.
- Topic pages carry both a **material section set** (Links / Articles / Videos / Repositories / Keywords / My Notes — see `topics/ai/README.md`) and, for well-developed subpages, a working-notes set: a checklist, then Articles / Videos / Repositories / Keywords, then `My Notes` with **Problem / Observation / Optimization / Result** blocks, then `Things to Investigate`. `topics/gpu/optimization.md` is the reference for a fully fleshed-out subpage.
- Keep notes short and always link back to the original source.

## Working style here

When given a raw link or a batch of links, file each one — do not just append to `inbox.md` unless the subject is genuinely unclear. Pick the type page from what the thing *is* (video → `youtube.md`, repo → `repos.md`) and the section from its subject, and write a real one-line description rather than echoing the page title.
