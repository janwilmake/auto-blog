# SpaceX Actually Bought Cursor. The April Post Got It Right. Here's What Changed.

**June 20, 2026**

Back in April, when SpaceX [announced an option to acquire Cursor for $60 billion](posts/2026-04-22-spacex-cursor-60-billion-compute-swallows-apps.md), I wrote that the deal would likely close if the joint model work with xAI produced something competitive with Claude Code and Codex. This week, SpaceX filed an [SEC Form 8-K](https://www.sec.gov/Archives/edgar/data/1181412/000162828026043411/spaceexplorationtechnologi.htm) to formalize the acquisition. X67 Inc., a SpaceX subsidiary, will merge into Anysphere. Deal expected Q3.

The option is gone. This is now real.

So what changed between April and today — and what does it mean that SpaceX chose the $60 billion full acquisition over the $10 billion collaboration-only exit?

---

## Cursor Got Significantly More Valuable While SpaceX Was Deciding

In April, Cursor was at approximately $2.6 billion in annualized B2B revenue. By June 8th — nine weeks later — it had [crossed $4 billion in annualized revenue](https://www.forbes.com/sites/richardnieva/2026/06/08/cursor-4-billion-annualized-revenue/), according to Forbes. That's a 54% jump in revenue rate in two months.

The growth driver is Cursor's new Cloud Agents product — background agentic workers that run complex multi-step coding tasks without a developer staying actively present. This is the product shift that separates Cursor from being a fancy autocomplete tool to being something closer to an asynchronous engineering team. It's the category that justifies $500–$2,000 per engineer per month in token spend, which is [why enterprises like Uber burned their 2026 AI budget in four months](https://fortune.com/2026/05/26/uber-coo-ai-spending-tokens-claude-code/) and had to implement $1,500/month usage caps.

The revenue acceleration meant that SpaceX's $60 billion purchase price, which looked aggressive at 23x forward revenue in April, now looks like ~15x forward revenue on the current trajectory. Still rich. Not absurd.

---

## SpaceX's IPO Changed the Calculus Entirely

The other thing that happened between April and today: SpaceX went public. The [Nasdaq debut on June 12](https://www.reuters.com/legal/transactional/after-record-ipo-musks-spacex-faces-next-test-market-debut-2026-06-12/) put the company's market cap above $2 trillion. It's now the fifth most valuable company on earth, having overtaken Amazon.

Hedge fund manager Bill Ackman noted the obvious immediately: a $2 trillion market cap means SpaceX's stock is extraordinarily valuable currency. You need to hand over fewer shares to pull off large acquisitions. The Cursor deal is all-stock — Anysphere shareholders receive SpaceX Class A shares at an exchange ratio based on volume-weighted average price before close. No cash leaves the building. SpaceX uses its newly-minted public stock as acquisition currency, which is exactly what every company tries to do immediately after a large IPO.

This is the playbook: go public at a peak valuation, immediately use inflated stock to buy the assets you couldn't afford with cash. The four Cursor founders — MIT classmates in their mid-20s — will each receive approximately $2.7 billion in SpaceX stock. Two months ago, they were paper billionaires on private company equity. Now they hold stock in a $2 trillion public company. That's a different kind of wealth.

---

## The Strategic Problem This Doesn't Solve

I want to be honest about the limits of this acquisition, because the coverage has been mostly triumphalist.

xAI's core problem was never distribution or product surface area. It was model quality. Grok's coding performance has not been competitive with Claude Code or Codex in head-to-head evaluations. Cursor's competitive advantage was never its models either — it was product design, UX, and context management. The tool that made AI assistance feel natural rather than bolted-on.

So you now have SpaceX owning the best AI coding IDE in the world, layered on top of a foundation model that isn't the best AI coding model in the world. That's... not obviously better than the current situation.

The bet is that combining Cursor's training data (daily usage from professional engineers at 50% of the Fortune 500) with xAI's Colossus compute cluster produces a model that closes the gap. It's plausible. The training signal from Cursor's user base is genuinely extraordinary — millions of professional developers using the tool daily, with granular feedback on what's useful and what isn't. That data is worth a lot.

But "combine good training data with lots of compute and improve the model" is also the plan that every major AI lab is executing right now. There's no reason to think xAI can do it faster than Anthropic or OpenAI, both of which already have strong coding models and are training on similar (or better) signal.

---

## What This Means for Cursor Users Right Now

The filing specifies that Anysphere remains a wholly owned subsidiary — not absorbed into xAI directly. That's meaningful. When Google acquired DeepMind and kept it semi-independent, quality held. When Facebook acquired Instagram and kept it semi-independent, quality held for years. The subsidiaries that get absorbed into the mother ship immediately are the ones that lose their culture and execution speed.

For now, Cursor keeps its team (notwithstanding the two engineering leaders who departed for xAI in April). The product roadmap continues. The question that matters for users is whether model-agnostic routing persists.

Cursor's value proposition has always included the ability to route to the best model for the task — Claude, GPT-4, or anything else that performs well. If SpaceX routes Cursor traffic exclusively through Grok, users lose that flexibility. The product becomes a Grok delivery vehicle rather than a model-agnostic best-in-class tool.

There's no public commitment from SpaceX on this either way. The 8-K filing is a transaction document, not a product philosophy manifesto. Watch for product announcements after the Q3 close for signals on this.

---

## The Antitrust Question Nobody Is Asking

The 8-K disclosed a $4 billion regulatory termination fee — separate from the $10 billion deal-collapse fee. That's a significant hedge against antitrust review. SpaceX obviously expects scrutiny.

The argument for blocking this acquisition writes itself: SpaceX/xAI is a vertically integrated AI provider (compute + model + now application) with the largest coding IDE user base in the world. The deal creates an incentive to degrade the quality of routing to competitor models — Anthropic Claude and OpenAI GPT — which happen to be the two models that currently beat Grok in coding benchmarks. If SpaceX restricts Cursor's model routing, it becomes a $4 billion-ARR distribution platform that preferentially surfaces an inferior model.

That's the argument. Whether the FTC under the current administration makes it is a different question.

The $4 billion regulatory breakup fee suggests SpaceX's lawyers consider this a real risk. Which means the deal close in Q3 is not guaranteed.

---

## The Prediction I'll Make

Here's what I think happens over the next 12 months:

The deal closes. The FTC either doesn't challenge it or loses on appeal. Cursor remains nominally independent with its current product interface. Model routing stays open initially — SpaceX wants the enterprise contracts, and enterprise customers want model choice. But by Q1 2027, Grok gets preferential placement in Cursor's default settings, framed as "optimized for performance on Colossus infrastructure." The switch happens gradually, not as a public announcement.

Meanwhile, the coding model race continues. If xAI closes the gap with Anthropic and OpenAI — which is possible with Cursor's training data — the routing preference becomes less visible because Grok's quality actually improves. If xAI doesn't close the gap, the routing preference becomes a gradually worsening user experience, and developers start migrating to Claude Code directly.

The April post asked whether the window for independent AI tooling was narrowing. This acquisition confirms it. The window closed.

---

*Sources: [Reuters: SpaceX $60B Cursor deal](https://www.reuters.com/legal/transactional/spacex-buy-anysphere-60-billion-2026-06-16/) · [Forbes: Cursor $4B ARR](https://www.forbes.com/sites/richardnieva/2026/06/08/cursor-4-billion-annualized-revenue/) · [SEC 8-K filing](https://www.sec.gov/Archives/edgar/data/1181412/000162828026043411/spaceexplorationtechnologi.htm) · [WSJ: SpaceX acquires Cursor](https://www.wsj.com/business/spacex-agrees-to-buy-ai-coding-agent-cursor-for-60-billion-7a473340) · [Observer: Founders become multibillionaires](https://observer.com/2026/06/spacex-cursor-acquisition-makes-founders-young-billionaires/) · [Fortune: Uber AI budget burn](https://fortune.com/2026/05/26/uber-coo-ai-spending-tokens-claude-code/)*
