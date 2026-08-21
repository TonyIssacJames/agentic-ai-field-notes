# Notes

Random notes and things worth remembering. Newest first.

These are my own writing, not collected links — so they do **not** appear in [history.md](history.md) and do not count toward the artifact total.

---

## 2026-08-22 — together.ai, and what Hassan El Mghari builds there

Interesting line of work. [together.ai](https://www.together.ai/) is an **AI native cloud platform** for running, deploying and training open source models. Three things it makes easy:

1. **Run open source AI models** (Inference API)
   - GLM 5.2 — chat & coding models
   - Nano Banana — image models
   - Whisper — audio models
   - Kimi K2.7 — vision models
2. **Finetune models** on your own data — SFT, DPO and RL
3. **Launch GPU clusters** (H100s / B200s / B300s) on demand, to train your own models or run your own inference

**Trusted by:** Cursor, Pika, Cartesia, LG AI Research, Salesforce, The Washington Post

His line that stuck with me:

> In a world of slop, building AI apps that look great is a huge competitive advantage.

Worth taking seriously — model access is a commodity now, so craft and polish are what separate one AI app from the next.

He built a **skill that de-slops a design**: it scores how much AI slop is in a UI and gives concrete suggestions to fix it. (Skills are just folders — see [how to create one](tutorials.md).)

### His tips for getting non-slop UI out of an agent

1. **Learn the AI tells** so you can call them out — you cannot fix what you cannot name.
2. **Save your design taste and specific preferences in an `AGENTS.md`** file, so they apply every run instead of being retyped.
3. **Use references and screenshots, not imagination** — paste a ton of screenshots. Showing beats describing.
4. **Write longer, more specific prompts** and give the agent more UX context.
5. **Break iteration into steps** — many queued messages beat one huge message.
6. **Iterating with a cheaper open model can beat using closed models for everything.**
7. **Iterate and refine continuously.** Treat the agent's output as a base to expand on, not a finished result.
8. **Always compare the cost** of doing the work on a cheaper model against OpenAI/Claude — pairs with 6: check whether the expensive model actually earned its price on that task.

The through-line: 2, 3 and 4 are all the same move — give the agent your taste as *context* rather than hoping it guesses. That is [harness engineering](articles.md) applied to design.

Why it is worth watching: point 3 is the GPU/performance side of this repo meeting the agentic AI side — same company, open models on rented H100s/B200s. Also note that [Retrieval Augmented Generation (RAG)](courses.md) on my course list is taught by Zain Hasan, a different Together.ai person — the company puts a lot into developer education.

---

## 2026-08-21 — agentskills.io is a good jumping-off point

The [Agent Skills home page](https://agentskills.io/home#adoption) is more than an overview:

- It links to a [Quickstart](https://agentskills.io/skill-creation/quickstart) — an actual tutorial on how to create a skill. Builds a `roll-dice` skill end to end, one file under 20 lines.
- It also has a [Client Showcase](https://agentskills.io/clients) listing ~45 agentic tools that support the skills format, each with setup instructions and source links. **An awesome place to check many tools** — worth returning to when hunting for new agent tooling.

See also: [Tutorials](tutorials.md), [Agentic Tools & Skills](agentic-tools.md)

---

[← Home](README.md)
