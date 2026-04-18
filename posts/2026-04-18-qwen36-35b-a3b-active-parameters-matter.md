# Qwen3.6-35B-A3B: The Model That Proves "Active Parameters" Are the Only Number That Matter

*April 18, 2026*

This week Alibaba quietly redrew the line between local AI and frontier AI — and most of the coverage is missing the real story.

On April 16, the Qwen team dropped [Qwen3.6-35B-A3B](https://qwen.ai/blog?id=qwen3.6-35b-a3b): a sparse Mixture-of-Experts model with 35 billion total parameters and **3 billion active parameters per token**. It scored 73.4% on SWE-bench Verified and 51.5% on Terminal-Bench 2.0 — comfortably ahead of Google's Gemma 4-31B (52.0% and 42.9% respectively). Simon Willison [ran it on his M5 MacBook Pro](https://simonw.substack.com/p/qwen36-35b-a3b-on-my-laptop-drew) and found it drawing better SVGs than Claude Opus 4.7. It's Apache 2.0 licensed. You can pull it with Ollama right now.

The headline numbers are impressive, but the thing worth actually thinking about is the architectural reason *why* they're impressive — because it changes how you should evaluate every model going forward.

## "35B Parameters" Is Basically a Lie (A Useful One)

When a dense model says it has 31 billion parameters, it means every single inference step activates all 31 billion of them. Every token you generate, every completion request you send, burns through the full 31B. That's why Gemma 4-31B needs roughly 62–70GB of memory to run at full precision.

When Qwen says "35B-A3B," the A3B means *active 3 billion*. It's a MoE model — the 35B parameters are organized into a large pool of specialized experts, and each token routes to only a subset of them. At inference time, you're activating about 3 billion parameters per token, not 35 billion.

The consequence: the compute cost of running Qwen3.6-35B-A3B per token is roughly comparable to running a 3B dense model — not a 35B one. But you have access to the breadth of knowledge encoded across all 35B parameters, because different inputs route to different experts. You get small-model inference economics with large-model knowledge.

Quantized to 4-bit via Unsloth's GGUF, the model fits in about 23GB of memory. A Mac Mini M4 with 32GB unified memory runs it. An M2 or M3 Max MacBook Pro with 36–48GB handles it comfortably. [The 2-bit version runs in 13GB](https://www.reddit.com/r/unsloth/comments/1sndis4/2bit_qwen3635ba3b_gguf_is_amazing_made_30/), which gets you to an M3 Pro at 18GB. The full F16 weights are 72GB — not consumer hardware — but you don't need those.

This is the relevant frame: **the question is no longer "how many parameters does this model have?" It's "how many active parameters does it need to beat the task I care about?"**

## What 73.4% on SWE-bench Actually Means

SWE-bench Verified is a benchmark where the model is given real GitHub issues from open-source projects and has to produce working patches. "Verified" means a subset of problems that human evaluators confirmed are unambiguous. It's hard. It requires understanding repository context, not just writing syntactically correct code.

For reference:
- Qwen3.6-35B-A3B: **73.4%**
- Google Gemma 4-31B (all 31B active): **52.0%**
- Claude Sonnet 4.5: roughly competitive on agentic coding at this tier

That's a 21-point gap over a dense model that activates more than 10x the parameters. And the MoE model runs locally on hardware you might already own.

The Terminal-Bench 2.0 numbers (51.5 vs 42.9) tell a similar story. This benchmark evaluates agentic bash/filesystem tasks — the kind of multi-step terminal work that coding agents actually do. Qwen3.6 wins there too.

Now, benchmarks have problems. [We've covered this.](../posts/2026-04-12-ai-benchmarks-are-broken-berkeley-exploit.md) Self-reported numbers from the releasing lab should be read skeptically, and Alibaba's eval setup (specific scaffold, temperature settings, context window) may not generalize to your workload. But when multiple independent testers — including Willison running his own side tests specifically to check for overfitting — find the model genuinely competitive, you have to take it seriously.

## The Apache 2.0 Part Is Not a Footnote

Closed-model vendors have been trying to make "open weights" mean as little as possible — releasing weights under restrictive licenses, capping commercial usage, requiring special agreements for anything interesting. Apache 2.0 is the opposite of that.

Apache 2.0 means: use it commercially, modify it, redistribute it, build products on top of it, fine-tune it, deploy it behind your API, ship it in your SaaS. No licensing fees. No usage limits. No callback to Alibaba. This is the same license that lets you use most of the open-source software infrastructure your company runs on.

For developers building AI-powered products, this matters enormously. You can run this model on your own metal, keep data fully on-premise, and not pay per-token API fees to anyone. At Alibaba Cloud's expected pricing of roughly $0.29 per million input tokens, the hosted version is [about 15x cheaper than Opus 4.7](https://medium.com/@borislavbankov/your-mac-just-became-a-frontier-ai-workstation-again-51696ea6a014). But the local route costs you only your hardware.

## How to Actually Run It

Three paths, in order of increasing complexity:

**Ollama (simplest — five seconds to running):**
```bash
ollama run qwen3.6:35b-a3b
```
Ollama handles quantization selection and memory management automatically. This is the right starting point for most people.

**LM Studio (GUI, no terminal required):**  
Download LM Studio, search for `Qwen3.6-35B-A3B-GGUF` in the model browser, select the `UD-Q4_K_S` variant. Downloads, loads, runs. This is what Willison used.

**llama.cpp (fastest, most configurable):**
```bash
./llama.cpp/llama-cli \
    -hf unsloth/Qwen3.6-35B-A3B-GGUF:UD-Q4_K_XL \
    --temp 0.6 \
    --top-p 0.95 \
    --top-k 20 \
    --min-p 0.00
```
The `UD-Q4_K_XL` from Unsloth uses their Dynamic 2.0 quantization, which calibrates important layers at higher precision and is noticeably better than naive Q4_K_M at equivalent file sizes.

**Hardware guide:**  
- 13GB unified memory: Q2_K_XL (good for basic tasks, some quality loss)  
- 24GB VRAM/unified: Q4_K_M, comfortable  
- 32–36GB unified: UD-Q4_K_XL, recommended  
- 48GB+: closer to full quality  

For coding agents, pair it with [Qwen Code](https://qwen.ai/qwencode) (the Alibaba-developed terminal agent, analogous to Claude Code) or use it as the backend for any OpenAI-compatible tool — Open WebUI, Continue.dev, etc.

## What This Changes

Six months ago, "run a frontier-competitive coding model locally" meant either accepting significant quality tradeoffs or needing a multi-GPU workstation. Now it means owning a reasonably modern Mac.

The MoE architecture is the mechanism. But the broader shift is this: the open-source ecosystem — Unsloth quantizing the model within hours of release, the llama.cpp community adding support, the Reddit threads benchmarking it against paid APIs — is becoming fast enough to make "just use the API" a less obvious default. The infrastructure work that used to take weeks happens in a day.

Qwen3.6-35B-A3B is not the final word. Qwen will release more models in the 3.6 family, and other labs are not standing still. But it's a clear data point: the active parameter count is the number that predicts your inference cost and hardware requirements, and "total parameters" is increasingly just marketing. The model that wins on your laptop is the one that makes smart routing decisions, not the one that activates the most weights.

That's a different way of thinking about the AI landscape. Adjust your benchmarks accordingly.

---

*Sources: [Qwen3.6-35B-A3B official release](https://qwen.ai/blog?id=qwen3.6-35b-a3b) · [Simon Willison's hands-on test](https://simonw.substack.com/p/qwen36-35b-a3b-on-my-laptop-drew) · [The Decoder benchmark analysis](https://the-decoder.com/alibabas-open-model-qwen3-6-leads-googles-gemma-4-across-agentic-coding-benchmarks/) · [Unsloth local run guide](https://unsloth.ai/docs/models/qwen3.6) · [HuggingFace model card](https://huggingface.co/Qwen/Qwen3.6-35B-A3B)*
