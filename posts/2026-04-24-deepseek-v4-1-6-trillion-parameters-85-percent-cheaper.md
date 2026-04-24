# DeepSeek V4 Is 85% Cheaper Than GPT-5.5. Here's What You're Actually Buying.

*April 24, 2026*

DeepSeek dropped V4 this morning and [it's the top story on Hacker News](https://news.ycombinator.com/item?id=47882714) with 836 points and nearly 500 comments. The headline numbers are striking: 1.6 trillion total parameters, 1 million token context as the default, near-frontier benchmark performance, and an API price roughly 85% cheaper than GPT-5.5.

Before you immediately switch your entire stack over to `deepseek-v4-pro`, here's what's actually going on, what's real, and what deserves scrutiny.

## The Architecture: Why 1.6T Parameters Doesn't Mean What It Sounds Like

DeepSeek V4-Pro has 1.6 trillion total parameters with **49 billion active** per token. V4-Flash has 284 billion total with **13 billion active**.

If you've read the [Qwen3.6-35B-A3B coverage from last week](../posts/2026-04-18-qwen36-35b-a3b-active-parameters-matter.md), you already know the relevant frame: in a Mixture-of-Experts model, total parameters is a marketing number. Active parameters is the compute number. The 1.6T figure means DeepSeek has organized an enormous pool of specialist "experts" that get selectively activated — each inference call only touches 49B of them. Your actual inference compute cost is more like a 49B dense model, not a 1.6T one.

That's how V4-Pro can charge $0.145 per million input tokens and $3.48 per million output tokens while claiming near-frontier performance. It's not magic — it's efficient routing. [The technical report](https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro/blob/main/DeepSeek_V4.pdf) goes into detail on the architectural improvements: token-wise compression, DeepSeek Sparse Attention (DSA), and a redesigned attention mechanism that makes 1M context windows practical rather than theoretical.

For context on what 1M tokens actually means: the entire codebase of a mid-sized application, multiple long documents, months of conversation history. The V3.2 series had 128K context. Going to 1M as the default is a meaningful jump — not "fits two more PDFs" meaningful, but "reorganizes what's possible for agentic coding" meaningful.

## The Pricing: Genuinely Disruptive

Let's put the numbers side by side for the API:

| Model | Input ($/M tokens) | Output ($/M tokens) |
|---|---|---|
| DeepSeek V4-Pro | $0.145 | $3.48 |
| DeepSeek V4-Flash | $0.14 | $0.28 |
| GPT-5.5 | ~$1.10 | ~$22+ |
| Claude Opus 4.7 | ~$1.50 | ~$20+ |
| Gemini 3.1 Pro | ~$1.25 | ~$10+ |

DeepSeek V4-Flash output at $0.28/M tokens is cheaper than most competitors' *input* prices. If you're running a high-volume agentic workflow — lots of tool calls, long context, many output tokens — V4-Flash at those prices is a fundamentally different budget conversation.

The V4-Pro pricing is more nuanced. Output at $3.48/M is competitive with frontier closed-source models, but not as dramatic as V4-Flash. What you're getting for that price is the model most likely to approach GPT-5.5 and Opus 4.7 quality. Whether it actually does depends heavily on your task.

## The Benchmarks: "Closes the Gap" Needs Qualification

DeepSeek claims V4-Pro "rivals top closed-source models" and that both V4 models are "comparable to GPT-5.4" on coding competition benchmarks. They say V4-Pro trails only Gemini 3.1 Pro in world knowledge benchmarks.

These claims are plausible but haven't been fully validated externally yet. The model dropped today. On [Artificial Analysis](https://artificialanalysis.ai/leaderboards/models) and the [Arena leaderboard](https://arena.ai/leaderboard), V4 currently lags behind the top frontier models — though that's expected to shift as evaluators run more tests and the community benchmarks against real tasks.

A few things to hold in mind when reading the DeepSeek benchmark tables:

**The benchmark setup matters.** DeepSeek, like every AI lab, runs their own evals. Their reported reasoning benchmark numbers look excellent. Third-party confirmation lags by days to weeks. The [Berkeley benchmark-gaming incident](../posts/2026-04-12-ai-benchmarks-are-broken-berkeley-exploit.md) is a reminder that self-reported eval numbers from releasing labs deserve scrutiny.

**Agentic performance is harder to evaluate than static benchmarks.** DeepSeek says V4 is "open-source SOTA in Agentic Coding benchmarks." The specific benchmark and scaffold matter enormously here. Agentic tasks are long-horizon, multi-step, context-dependent. A model that scores well on an agentic benchmark with a specific tool harness may not perform equally well in your actual workflow.

**"Preview" means not final.** DeepSeek explicitly released this as a preview. The models are available and the API is live, but V4 is not a finalized release. This is similar to how GPT-4 had months of refinement between initial and stable releases.

## What's Actually New

Beyond the scale increase, a few things in V4 are worth paying attention to:

**DeepSeek Sparse Attention (DSA).** The architectural innovation that makes 1M context practical. DSA allows the model to selectively attend to tokens across a very long context without the quadratic compute scaling of full attention. This is the real engineering story — not the parameter count, but how they made long context cheap.

**Native thinking/non-thinking modes.** Both V4-Pro and V4-Flash support thinking mode (extended chain-of-thought reasoning) and non-thinking mode. You can set `reasoning_effort` to `low`, `medium`, or `high`. This replaces the separate `deepseek-chat` and `deepseek-reasoner` model split — now it's one model with switchable modes. The legacy models will be retired July 24, 2026.

**Claude Code and agentic integration.** DeepSeek explicitly says V4 integrates with Claude Code, OpenClaw, and OpenCode. This is significant: DeepSeek is optimizing V4 specifically to work well as the backend model for agent frameworks developed by Anthropic and others. You can swap `deepseek-v4-pro` into an existing Claude Code workflow without changing anything else. (Given yesterday's Claude Code postmortem, the timing is either ironic or deliberate.)

**MIT license.** Open weights, MIT licensed. The V4-Pro weights are 865GB; V4-Flash is 160GB. For researchers and organizations running their own inference, this is a workable local deployment at scale. For most developers, the hosted API is the practical choice.

## Who Should Actually Switch

The practical question is: does any of this change what you should use today?

**For high-volume production workloads where cost is the primary constraint:** V4-Flash at $0.14/$0.28 per million tokens deserves serious evaluation right now. If you're running thousands of agent calls a day, the cost difference from GPT-5.5 or Opus 4.7 is enormous. Run your actual workload against V4-Flash for a week before committing.

**For agentic coding with complex tasks:** V4-Pro is worth benchmarking against Opus 4.7 and GPT-5.5 on your real tasks, not lab benchmarks. Expect to spend a week doing eval work before you'll know if V4-Pro is a drop-in improvement or a lateral move with cost savings.

**For users who want the best absolute quality on demanding tasks:** Wait two to four weeks for external eval data to accumulate. The models just dropped. The self-reported benchmarks are impressive, but "closes the gap" claims from releasing labs always look better on day one than after independent validation.

**For anyone in a data-sovereignty context:** The Chinese jurisdiction question hasn't changed. DeepSeek is a Hangzhou-based company. Using the hosted API means your data goes through their infrastructure. For some organizations, this is a hard constraint regardless of benchmark performance. The open weights let you self-host — but 865GB of model weights is a real infrastructure commitment.

## The Bigger Picture

DeepSeek's release cadence over the past year has been relentless. R1 shocked the market with reasoning performance at a fraction of the cost. V3.2 closed gaps further. V4 is the pattern continuing: larger scale, better architecture, lower cost, MIT license.

The narrative that leading open-weight models trail closed-source leaders by a year is increasingly inaccurate. DeepSeek says V4 trails state-of-the-art closed models by three to six months — and those are self-reported numbers from a lab with obvious motivation to claim parity. But the independent benchmarks over the next few weeks will tell us more precisely.

What's already clear is that the pricing pressure DeepSeek applies every time it releases a model is real. OpenAI cut GPT-5.4 pricing last month. Google has been adjusting Gemini pricing. This is what competitive pressure looks like — not from another closed-source lab, but from an open-weight model that anyone can run, fine-tune, or deploy without paying per token.

The "1M context is now the default" framing in the DeepSeek release notes might be the most underrated part. Six months ago, 1M context was a premium feature. Now it's table stakes. When DeepSeek says "welcome to the era of cost-effective 1M context length," they're not just describing their product — they're describing what every serious API provider is going to have to offer within the next six months.

---

*Sources: [DeepSeek V4 release announcement](https://api-docs.deepseek.com/news/news260424) · [DeepSeek V4 technical report (Hugging Face)](https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro/blob/main/DeepSeek_V4.pdf) · [TechCrunch](https://techcrunch.com/2026/04/24/deepseek-previews-new-ai-model-that-closes-the-gap-with-frontier-models/) · [CNBC](https://www.cnbc.com/2026/04/24/deepseek-v4-llm-preview-open-source-ai-competition-china.html) · [DataCamp analysis](https://www.datacamp.com/blog/deepseek-v4) · [Hacker News thread](https://news.ycombinator.com/item?id=47882714)*
