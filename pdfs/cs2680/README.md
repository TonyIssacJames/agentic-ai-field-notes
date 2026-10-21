# CS2680 Modern AI Systems — Lecture Slides

Slides from [CS2680 Modern AI Systems: Agents and System Optimizations](https://cs2680.com/) (Harvard SEAS, Fall 2026) taught by Juncheng Yang. The course starts with one agentic system at the agent loop and works down through the serving stack. Part I covers using, designing, evaluating and cost-optimizing agents. Part II covers LLM serving: batching, KV-cache, routing, quantization and speculative decoding.

> ## 📄 9 decks saved so far
>
> _Last added: all Part I slides, downloaded 2026-10-21. Part II slides are not posted yet._

The files are numbered in lecture order. Click a file name to open it on GitHub, or use **Source** for the original on the course site.

---

## Part I — Introduction to LLMs and agents

| # | File | Lecture | Date | What it covers | Pages | Source |
|---:|------|---------|------|----------------|------:|--------|
| 1 | [1_intro.pdf](1_intro.pdf) | Course overview and logistics | 2026-09-02 | How the course is structured: five assignments, the project, grading, and the policy on using AI. How AI systems differ from classic systems in compute, non-determinism, memory/storage, fast-moving models, reliability and power. Why agents are a systems problem, the agentic systems stack, and observability as a first-class concern. Agents vs. assistants vs. bots | 50 | [original](https://cs2680.com/slides/1_intro.pdf) |
| 2 | [2_agent_use_1.pdf](2_agent_use_1.pdf) | Agent use I — How Claude Code works | 2026-09-09 | The agentic loop (gather context → propose action → harness executes → result folds back), context before you type, permissions, compaction. Memory: CLAUDE.md vs. auto memory, `.claude/rules/`, the MEMORY.md index. Core tools. Skill vs. MCP vs. hook vs. plugin and where each attaches in the loop. Optional: MCP primitives, MCP security, MCP gateways | 53 | [original](https://cs2680.com/slides/2_agent_use_1.pdf) |
| 3 | [3_agent_use_2.pdf](3_agent_use_2.pdf) | Agent use II — Multi-agent and best practice | 2026-09-14 | Case studies of the Superpowers and Caveman plugins (skills, hooks, MCP, token compression). Scaling out with git worktrees, subagents, agent teams, `/goal` and `/loop`. "Programming / Software 3.0" and living with non-determinism. Field evidence from the June and Sept 2026 Codex usage reports, and the delegation ladder. Optional: how other coding agents differ | 72 | [original](https://cs2680.com/slides/3_agent_use_2.pdf) |
| 4 | [4_agent_design_1.pdf](4_agent_design_1.pdf) | Agent design I — Context management and tool design | 2026-09-16 | The ReAct loop and the components of a coding agent. Tool design for search/read (glob/grep, semantic search, extrinsic signals, LSP), edit (whole-file, diff, search-replace, AST) and shell execution. Prompting techniques (few-shot, CoT, ToT, self-consistency, chaining, meta-prompting). Context engineering: window management, compaction, five ways context fails. *Agent = Model × Harness* | 56 | [original](https://cs2680.com/slides/4_agent_design_1.pdf) |
| 5 | [5_agent_design_2.pdf](5_agent_design_2.pdf) | Agent design II — Multi-agent systems | 2026-09-23 | Eight multi-agent architectures: supervisor–worker, hierarchical, pipeline/DAG, blackboard, committee/debate, market, swarm and dynamic. Task decomposition and its failure modes. Communication medium, channels and message format. Why multi-agent is a distributed-systems problem. A2A (agent card, task lifecycle) vs. MCP. Design principles | 46 | [original](https://cs2680.com/slides/5_agent_design_2.pdf) |
| 6 | [6_agent_design_3.pdf](6_agent_design_3.pdf) | Agent design III — Memory and reliability | 2026-09-28 | Agent memory design: when to write, retrieve, generalize and forget, with validity time and provenance, ownership, dedicated memory systems, a design checklist, and how to evaluate memory. Reliability: where faults originate (task spec, harness, model, tool, environment) and designing for failure | 31 | [original](https://cs2680.com/slides/6_agent_design_3.pdf) |
| 7 | [7_agent_design_4.pdf](7_agent_design_4.pdf) | Agent design IV — Evaluation | 2026-09-30 | Evaluation objectives, metrics and the benchmark loop. A tour of GAIA, BFCL, WebArena, OSWorld, SWE-bench, τ-bench, Terminal-Bench and TheAgentCompany. Data leakage across the agent stack, benchmarks that saturate quickly, and why agent evaluation is still evolving | 37 | [original](https://cs2680.com/slides/7_agent_design_4.pdf) |
| 8 | [8_agent_design_5.pdf](8_agent_design_5.pdf) | Agent design V — Cost-effective agents | 2026-10-07 | Where agent cost comes from: turns per session, steps per turn, tokens per step, and cache reads. Levers for each: lean context, output schemas and effort, caching and batching, model routing, per-task budgets. Making agents faster: serving, orchestration, tool calls, parallelism, speculative actions. Monolithic vs. modular vs. microkernel harnesses, and what is unique in eight harnesses (Codex CLI, OpenCode, OpenClaw, Hermes Agent and others) | 54 | [original](https://cs2680.com/slides/8_agent_design_5.pdf) |
| 9 | [8_assignment3.pdf](8_assignment3.pdf) | Assignment 3 briefing — MadsOpt harness competition | 2026-10-07 | A short deck shown with lecture 8. It covers the 26 tasks across 3 container types, with the agent and task in separate containers talking over HTTP. Also: offline evaluation, limits, code structure, leaderboard submission and grading | 7 | [original](https://cs2680.com/slides/8_assignment3.pdf) |

The 2026-10-05 guest lecture (Snowflake) and the 2026-10-14 student-led session on agent safety have no slides on the site.

---

## Part II — Systems for LLMs and agents

_No slides posted yet (as of 2026-10-21)._ According to the [course schedule](https://cs2680.com/), the following lectures are coming:

- Efficient LLM serving basics I–III (Oct 19, 21, 28)
- Speculative decoding (Nov 2)
- Scheduling and prefix caching for agentic systems (Nov 4)
- Pruning and quantization (Nov 11)
- KV-cache optimization and model architectures (Nov 16)
- Routing and load balancing (Nov 23)
- Efficient agentic systems: others (Nov 30)

To check for new decks, look for `[slide]` links in the schedule table on the course home page.

---

## Related material on the course site

- [Assignments](https://cs2680.com/assignments/index.html): full specs for Mads-Lens, Mads-Loop, Mads-Opt, Mads-Serve and Mads-Stack
- [Agent evaluation and benchmarks](https://cs2680.com/notes/agent-evaluation.html): reference notes that go with deck 7
- [Cost-effective agents](https://cs2680.com/notes/cost-effective-agents.html): reference notes that go with deck 8
- Per-lecture reading lists are linked from the "Readings" column of the schedule

---

[← Home](../../README.md)
