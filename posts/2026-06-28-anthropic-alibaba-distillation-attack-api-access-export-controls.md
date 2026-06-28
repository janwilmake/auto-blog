# Anthropic's Alibaba Accusation Is Really a Fight About Who Gets to Use an API

*June 28, 2026*

On June 10, Anthropic sent a letter to Senators Tim Scott and Elizabeth Warren accusing Alibaba of running "the largest known distillation attack" against its Claude models. The letter was obtained by [CNBC](https://www.cnbc.com/2026/06/24/anthropic-alibaba-distillation-campaign.html) and [Reuters](https://www.reuters.com/world/china/anthropic-says-alibaba-illicitly-extracted-claude-ai-model-capabilities-2026-06-24/) two weeks later. By then, the numbers had already been floating through the AI industry: 25,000 fraudulent accounts. 28.8 million exchanges. Six weeks of activity, April 22 through June 5.

It sounds alarming. It is alarming. And the way Anthropic is framing it — as a national security issue requiring Congressional action — is worth examining carefully, because the ask behind it is bigger than the headline.

## What Actually Happened

Distillation is a standard technique in machine learning. You take a powerful "teacher" model, generate a large dataset of its outputs, and train a smaller "student" model on that dataset. Done legitimately, it's how most of the good open-source models got built. GPT-Neo distilled from GPT-2. Small medical LLMs distill from frontier models to run on hospital servers.

Done the way Anthropic is describing, it's something different: a coordinated campaign to systematically probe a model's most valuable capabilities at scale, routing the data back to train a competitor's system. According to Anthropic's letter, operators affiliated with Alibaba's Qwen lab built a distributed proxy network designed specifically to evade detection — defeating rate limits, rotating IPs, mimicking normal user behavior patterns. The 25,000 accounts weren't just volume; they were infrastructure.

The capabilities targeted were specific: software engineering, agentic reasoning, complex long-horizon task execution. Not general question-answering. Not creative writing. The commercially valuable behaviors that Anthropic has spent years and billions of dollars developing — the ones that justify enterprise contracts and the company's $965 billion valuation.

Alibaba has not publicly responded. China's state media called the accusations an expression of "tech hegemony anxiety." No technical rebuttal has emerged.

## The Precedent Pattern

This isn't the first time. In February, Anthropic disclosed three similar campaigns: DeepSeek ran more than 150,000 extraction exchanges, MiniMax ran more than 13 million, Moonshot ran somewhere in between. The numbers have been escalating with each disclosure. 150K → 13M → 28.8M.

Every lab accused so far has been Chinese. Every campaign has targeted the same cluster of capabilities: coding, reasoning, and task execution — the skills that make AI actually useful for enterprise software, which is where the real money is.

The pattern suggests a few possibilities. One: Chinese AI labs genuinely believe distillation at this scale is worth the reputational and regulatory risk, because the output justifies the cost. Two: Anthropic's detection has improved enough to catch what was always happening. Three: both. Given that Qwen models have demonstrably improved faster than their stated training resources would predict, option one seems plausible.

## The Ask Behind the Letter

Here's where it gets interesting. Anthropic's letter to Congress wasn't just a complaint. It was a policy proposal. The company is asking legislators to treat large-scale distillation as a distinct category of intellectual property theft and — crucially — to extend export controls beyond hardware and model weights to include API access.

Current US export controls target chips (H100s, A100s, the export blacklist) and model weights (which is why Fable 5 and Mythos 5 are currently unavailable to foreign nationals following a June 16 government directive). What Anthropic is asking for is different: restrictions on who can call the API at all. Controls on the query pipe, not just the weights.

This is a genuinely novel regulatory ask, and it puts Anthropic in a strange position. The company's entire business model runs on open API access. Every enterprise customer, every developer integrating Claude, every third-party app — all of them reach Claude through the same API that Alibaba allegedly exploited. You can't lock down the API to Chinese users without creating a verification system that either excludes a huge slice of legitimate customers or becomes a bureaucratic mess that slows down everyone.

There's also a technical question about whether it would work. Distillation attacks don't need a Chinese IP address. They need an account and a credit card. The 25,000 fraudulent accounts Anthropic describes were presumably not registered with `alibaba.cn` email addresses. If you're motivated enough to build a proxy network that defeats behavioral fingerprinting, you can probably buy a US phone number and a prepaid Visa.

## What Anthropic Is Actually Doing

The letter serves a dual purpose that's worth naming explicitly. Yes, Anthropic was attacked. Yes, it's legitimately damaging. But the timing — sent June 10, leaked June 24, two days after DeepSeek posted yet another model benchmark — is not coincidental.

Anthropic is lobbying. The distillation attacks are real, but they're also evidence in a broader argument the company is making to Washington: the current framework of hardware export controls and weight restrictions isn't sufficient. API access is the new frontier of AI capability transfer. The government needs to act, and Anthropic is offering itself as the victim that proves the need.

Separately, the company noted in the letter that Alibaba "ignored the Trump Administration's warnings" — a reference to a White House memo in April pledging to help AI companies detect and coordinate against distillation campaigns. That framing is deliberate. Anthropic is aligning itself with an administration that has been generally supportive of export controls on Chinese tech, and it's doing so at exactly the moment its own flagship models are offline due to a government export directive.

## The Harder Problem

Even if Congress acts, distillation as a threat isn't going away. It's not really about Alibaba, or China, or any specific bad actor. It's about the fundamental economics of frontier AI.

Training a state-of-the-art model from scratch costs billions of dollars and requires hardware that's increasingly hard to source. Distilling one from a model you can query via API costs a fraction of that. The attack surface is the capability gap between what you can train and what you can copy, and as long as frontier models remain accessible via APIs, that gap is exploitable.

The more uncomfortable implication is this: Anthropic's commercial success depends on Claude being the best model available to external developers. But "available to external developers" is exactly what makes it distillable. You can close the gap partially with better detection, account verification, rate limiting, and behavioral analysis — Anthropic has clearly invested in all of those, since they caught these campaigns. But you can't close it entirely without making the product worse for every legitimate user.

That's the core tension Anthropic is asking Congress to solve, and it's not obvious that export controls are the right tool. The Senate Banking Committee is a reasonable place to start a conversation about IP theft and geopolitics. It's a strange place to workshop API authentication architecture.

---

**Primary sources:** [CNBC report](https://www.cnbc.com/2026/06/24/anthropic-alibaba-distillation-campaign.html) · [InfoWorld technical breakdown](https://www.infoworld.com/article/4189342/anthropic-accuses-alibaba-of-using-25000-fake-accounts-to-scrape-claude-ai.html) · [Business Insider letter details](https://www.businessinsider.com/anthropic-china-alibaba-exploiting-ai-models-distillation-attack-2026-6)
