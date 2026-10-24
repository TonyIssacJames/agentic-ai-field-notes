# Tutorials

Step-by-step guides and how-tos.

## AI / LLM

- [Agent Skills — how to create a skill](https://agentskills.io/home#adoption) — The open Agent Skills standard: what a `SKILL.md` folder contains, progressive disclosure (discovery → activation → execution), and which agents support it. Originally from Anthropic, now an open spec ([my notes](notes.md))
- [How to Build and Deploy a Multi-Agent AI System with Python and Docker — Balajee Asish Brahmandam (freeCodeCamp)](https://www.freecodecamp.org/news/build-and-deploy-multi-agent-ai-with-python-and-docker/) — Long hands-on handbook: a four-agent Python pipeline that turns daily inputs into a Markdown digest, each agent in its own Docker container, wired together with Docker Compose; OpenAI SDK with an optional local Ollama model, tested with pytest
- [16x Faster, 70% Smaller: Quantizing Qwen2.5-Coder for High-Throughput CPU Inference — Souvick R.](https://www.linkedin.com/pulse/16x-faster-70-smaller-quantizing-qwen25-coder-cpu-inference-roy-sr4oc/) — Converts a fine-tuned 1.5B coding model to a 4-bit GGUF build served through Ollama so it runs on an ordinary CPU, with the reported speed and memory gains, then drives a LangChain agent through multi-step tool calls
- [Train the agent, not just the model — Sergio Paniego Blanco (Hugging Face slides)](https://huggingface.co/spaces/sergiopaniego/train-the-agent-slides) — Slide deck from PyTorch Conference NA (21 Oct 2026) on how agents are trained in 2026 with TRL and OpenEnv: SFT on agent traces, distillation, GRPO, RL in environments and training through the harness, one method per episode of the Training Agents live series
  - Companion series: [Post-training Agents (Hugging Face playlist)](https://www.youtube.com/playlist?list=PLo2EIpI_JMQvQZm-kVlz4wY1vWF0LBcf5) — the live "Training Agents" sessions the deck's episodes come from ([also in YouTube](youtube.md))

---

[← Home](README.md)
