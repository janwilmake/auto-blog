# Kimi K3 Is the Largest Open-Weights Model Ever. You Cannot Run It.

**Date:** 2026-07-17

Moonshot AI shipped Kimi K3 today. It is, by a wide margin, the largest open-weights model ever released: 2.8 trillion total parameters, a 1-million-token context window, native multimodal input, and benchmark numbers that land it comfortably between Claude Opus 4.8 and Claude Fable 5 on most agentic coding suites. The weights are promised by July 27th, making it the first open 3-trillion-parameter-class model in history.

The r/LocalLLaMA reaction was immediate and electric: "we're eating good this week."

And they're not wrong — but they're also not entirely right. Let's be specific about what "open" means when the model is 2.8 trillion parameters.

---

## What Kimi K3 Actually Is

The architecture is legitimately interesting. Kimi built K3 on two components they've been developing and publishing research on: **Kimi Delta Attention (KDA)**, a hybrid linear attention mechanism that replaces standard quadratic attention for long sequences and cuts KV cache usage by up to 75% compared to full attention, and **Attention Residuals**, which replace the standard layer-by-layer residual connections with a learned, input-dependent attention mechanism over all prior layer outputs. The idea is that later layers can selectively pull from earlier representations instead of just accumulating everything blindly.

The MoE architecture also got more aggressive: 16 active experts out of 896 total per token, up from 32-of-~384 in the K2 series. This "Stable LatentMoE" framing keeps active compute surprisingly lean — only about 50 billion parameters activate per forward pass despite the 2.8 trillion total. That's the number that drives inference cost, which is why the API price ($3/M input, $15/M output) is competitive with Claude Sonnet 5.

On benchmarks, Moonshot's own numbers put K3 ahead of everything else on Program Bench, SWE Marathon, BrowseComp, SpreadsheetBench 2, and Automation Bench. It trails Claude Fable 5 (the restricted, US-government-gated model) on FrontierSWE and GPT-5.6 Sol on DeepSWE. Artificial Analysis pegs its intelligence score at 57, comparable to Opus 4.8 and GPT-5.5, behind Fable 5 and Sol.

Moonshot also did something unusual for a launch post: they flagged their own gaps. There's a "noticeable UX gap" versus Fable 5 and GPT-5.6 Sol, sensitivity to thinking-history handling, and excessive proactiveness in agent mode. That level of candor earns some credibility for the benchmark charts.

---

## The Number Nobody Is Saying Loudly Enough

To run Kimi K3 locally, you need approximately **3 terabytes of VRAM**.

That's not a typo. That works out to roughly 32× H200 GPUs (each with 141 GB of HBM3e), or about 16× B200s. A 32-H200 cluster rents for somewhere between $300 and $800 per hour depending on provider and availability.

r/LocalLLaMA figured this out within the first hour of launch. The top comment in one thread: "you need about 3TB of VRAM for that. About 32×H200 or about 16×B200 needed." The humor was gentle but pointed — the same community that cheerfully runs Llama 3.1 8B on an RTX 3060 understands immediately that "K3 Distill" is not something they're installing next weekend.

For comparison: the K2 series (1T total, 32B active) required about 630 GB of VRAM at INT4, which is already an 8× H100 job — beyond consumer hardware but theoretically within reach for a well-funded startup or research lab. K3 is 5× larger than K2. The jump from "expensive but conceivable" to "requires a small GPU cluster just for the model weights" is not incremental. It's categorical.

No INT4 or GGUF quantization for K3 exists yet. When it does arrive — and it will, within days of the weight release — even the most aggressive quant will likely land north of 700–800 GB just for the weights, before KV cache. At 1 million token context windows, the KV cache alone is enormous.

---

## What "Open Weights" Actually Means at This Scale

The GLM-5.2 post here from June made an argument worth repeating: the value of open weights at this scale is **sovereignty**, not local inference. You can't run K3 on your MacBook. You *can* download K3 to your data center and run it without Moonshot knowing anything about your queries, without the Chinese National Intelligence Law applying to your inference workload, and without the model being turned off by the US Commerce Department if geopolitical relations deteriorate.

That last point matters more in July 2026 than it would have a year ago. The Fable 5 export control incident in June — where Anthropic was forced to cut off access to non-US users with less than 24 hours' notice — was a demonstration of exactly what "your AI provider can disappear overnight" looks like in practice. K3's MIT license (expected, based on Moonshot's prior K2 licensing) means that once the weights are out, they're out. No government directive makes the weights on your storage array vanish.

This is real and meaningful. For a European enterprise, a Korean tech company, or a Latin American developer who watched Fable 5 disappear, "can't be revoked" has material value.

But let's not confuse "sovereign" with "accessible." The open-weights movement has always sold itself on two things simultaneously: you can run it yourself (democratization), and you control what happens to your data (sovereignty). K3 delivers the second thing. It does not, in any realistic sense for the vast majority of developers, deliver the first.

The r/LocalLLaMA hype treats these as the same thing. They're not.

---

## The Distillation Question

The more interesting question isn't "can I run K3 locally" — obviously you can't. It's: **what does K3 enable at smaller scales?**

The Kimi K2.7 Code model, which *is* runnable by well-equipped individuals, was already the best open-weights coding model for most practical purposes when K3 launched. K3's job isn't to replace K2.7 Code for individual developers. K3's job is to be the teacher model that trains the next generation of distilled models.

DeepSeek's entire competitive advantage was built on this pattern: frontier-scale reasoning models get distilled into practical-scale models that beat models 10× their size. DeepSeek R1's 7B and 14B distillations are running on consumer hardware because a frontier reasoning model was used to generate the training data. K3 at 2.8T is the kind of model that makes the next K2 significantly better.

The community excitement about K3 is misplaced if it's excitement about *running K3*. It should be excitement about what K3's existence means for models that come six months from now.

---

## The Real Benchmark Story Is Vendor-Reported

One more thing worth flagging: every benchmark in the K3 launch post is vendor-reported at release time.

Moonshot used their own evaluation harnesses for the coding-heavy benchmarks where K3 leads. SWE Marathon and Automation Bench aren't standardized third-party evaluations — they're task sets Moonshot built and measured. Program Bench and SpreadsheetBench 2 are also proprietary. The benchmarks where K3 trails (FrontierSWE and DeepSWE) are from other labs' evaluation frameworks.

This doesn't mean the numbers are fraudulent. Moonshot's prior models (K2.x) held up reasonably well under independent evaluation. But "leads all tested models on X, Y, and Z" means exactly as much as the evaluation design, which is "we designed X, Y, and Z."

The independent numbers will come from the AI arena and Artificial Analysis once the weights are actually public and researchers can run controlled comparisons. The current 57-point Artificial Analysis score (Opus 4.8-class) is the most trustworthy external signal available, and it tells a more measured story than Moonshot's launch material.

Wait for Epoch AI or METR to run K3 through their standard agentic evaluation suites before deciding whether it's really better than Claude Fable 5 on anything that matters for your use case.

---

## The Bottom Line

K3 is a genuinely impressive engineering achievement. 2.8 trillion parameters, novel architecture that actually has good reasons to exist (KDA's KV cache reduction matters for the 1M context window to be practical), competitive API pricing, and a credible open-weights commitment with a specific date.

It is not a model you're going to run locally. It is not democratizing AI inference for individual developers. It is a frontier-scale research and API model from a Beijing-based startup that has, over the past 12 months, been one of the most consistent pushers of the open-weights frontier. The MIT license on the weights, once released, gives enterprises and governments a credible alternative to US-controlled AI infrastructure.

That's worth something. But it's a different thing than what the "we're eating good" headlines suggest.

---

**Primary sources:**
- [Kimi K3 technical blog (Moonshot AI)](https://www.kimi.com/blog/kimi-k3)
- [Kimi Platform API documentation](https://platform.kimi.ai/docs/guide/kimi-k3-quickstart)
- [Latent Space AINews: Kimi K3 2.8T-A50B](https://www.latent.space/p/ainews-kimi-k3-28t-a50b-the-largest)
- [Reuters: China's Moonshot unveils world's largest open AI model](https://www.reuters.com/world/china/chinas-moonshot-unveils-worlds-largest-open-ai-model-closing-us-rivals-2026-07-17/)
- [r/LocalLLaMA: What do I need to get Kimi 3 locally?](https://www.reddit.com/r/LocalLLaMA/comments/1uyh7sc/what_do_i_need_to_get_kimi_3_locally_reliably/)
- [Artificial Analysis score and intelligence index](https://x.com/ArtificialAnlys/status/2077832874183860404)
- [Digital Applied: Kimi K3 release analysis](https://www.digitalapplied.com/blog/kimi-k3-open-frontier-model-release-2026)
