# Anthropic's "First Profit" Is Real. The SpaceX S-1 Accidentally Explains Why It Won't Last.

**May 22, 2026**

Wednesday was a big day for AI financial theater. The Wall Street Journal [reported](https://www.wsj.com/tech/ai/mind-blowing-growth-is-about-to-propel-anthropic-into-its-first-profitable-quarter-7edbf2f4) that Anthropic is on track for its first-ever profitable quarter — $10.9 billion in Q2 revenue against an operating profit of $559 million. The same day, SpaceX [filed its S-1](https://www.sec.gov/Archives/edgar/data/1181412/000162828026036936/spaceexplorationtechnologi.htm) with the SEC. In the fine print of that filing, buried in the customer disclosures, was a number that makes the Anthropic profit story considerably more complicated.

Anthropic has agreed to pay SpaceX **$1.25 billion per month** through May 2029 for access to the Colossus I and Colossus II data centers in Memphis. That's $15 billion a year — to a single compute vendor. To Elon Musk's company, specifically, which also owns xAI, Anthropic's direct competitor.

This is not a secret anymore. It's in a public SEC filing.

---

## The Headline Is Technically True

Let's be fair first. The WSJ didn't lie. Anthropic *is* tracking toward an operating profit of $559 million in Q2 2026. That's EBITDA-style profitability — revenue minus direct operating costs before interest, depreciation, and capital expenditure. Going from burning cash to generating operating profit is a legitimate milestone. Anthropic's revenue growth is genuinely extraordinary: $4.8 billion in Q1, doubling to $10.9 billion in Q2, growing faster than Zoom at pandemic peak and Google ahead of its IPO. Those are the raw numbers.

Ed Zitron [called this a "swindle"](https://www.wheresyoured.at/anthropics-profitability-swindle/) yesterday, which is a bit too sharp. It's not a swindle. But it does require some unpacking.

The word "profitable" in tech press has a specific, optimistic meaning: EBITDA or operating income. This strips out the capital expenditures that are, for an AI company in 2026, the entire ballgame. The WSJ article itself acknowledges Anthropic "may not remain profitable throughout the year due to the large compute costs it's scheduled to incur." That's a polite way of saying: the profit window is narrow and the spending commitments coming online are enormous.

---

## What "Compute Costs Scheduled to Incur" Actually Means

Here's where the SpaceX filing earns its keep as a primary source.

Anthropic announced the SpaceX compute deal [on its own blog](https://www.anthropic.com/news/higher-limits-spacex) a couple weeks ago. They framed it as good news for users — 220,000 additional NVIDIA GPUs, more capacity for Claude Pro subscribers, improved reliability. The financial terms weren't disclosed. Now they are.

$1.25 billion per month. Ramping through May and June 2026. Running through May 2029.

For context: Anthropic's Q1 2026 revenue was $4.8 billion. The SpaceX deal alone costs $3.75 billion per quarter — roughly 78% of their Q1 revenue going to a single vendor. Even at $10.9 billion in Q2 revenue, the SpaceX contract represents 34% of quarterly income.

And that's just SpaceX. Anthropic's [compute announcement page](https://www.anthropic.com/news/higher-limits-spacex) lists the full picture:
- Up to 5 gigawatts from Amazon (Project Rainier), nearly 1 GW online by end of 2026
- 5 GW from Google and Broadcom, coming online starting 2027
- $30 billion of Azure capacity in a Microsoft/NVIDIA partnership
- $50 billion in Fluidstack infrastructure

These deals haven't fully kicked in yet. Some of the biggest commitments — the Google/Broadcom 5 GW — don't start until 2027. Which is exactly why Q2 2026 shows an operating profit: you're in the window *between* the revenue arriving and the full weight of the compute commitments landing.

The $559 million Q2 operating profit is real. It is also, almost certainly, a peak that will be pressured for the next three years as this infrastructure comes online.

---

## This Doesn't Mean the Business Is Bad

The instinct here is to say "see, AI companies will never be profitable," but that's too quick. There's a version of this story where Anthropic's compute commitments are exactly right-sized — where the capacity they're buying now generates the revenue to justify itself.

The math requires that to happen. Anthropic's revenue is currently growing at a rate that would take it from $4.8B in Q1 to some number much larger by 2027 when the Google/Broadcom capacity arrives. If you believe the growth curve continues — if the $30B run rate Anthropic was tracking toward in February [per their CFO](https://www.anthropic.com/news/google-broadcom-partnership-compute) materializes — then the compute bill, while enormous, is proportional.

The bet is: massive supply of compute generates massive demand for Claude. More GPUs mean better models, higher rate limits, enterprise customers who've been constrained by capacity limits, which drives revenue, which services the compute debt. The circularity is intentional. It's how cloud providers built themselves.

The risk is that this model requires sustained, compounding growth with no miss. A quarter of slower-than-expected revenue while carrying $1.25B/month in SpaceX commitments plus the coming Amazon and Google bills is not a fun earnings call.

---

## The Weirder Story: Anthropic's Biggest Compute Vendor Is Also Its Competitor

The SpaceX filing notes that Grok 5 is currently being trained at Colossus II — the same data center Anthropic is leasing. xAI and Anthropic are sharing infrastructure that one of them (Anthropic) is paying $15 billion a year for while the other one (xAI) is using it to build a competing model.

SpaceX/xAI gets to have it both ways: collect $15B/year from Anthropic, use any remaining capacity for their own models, and terminate the contract in 90 days if they need it back. Anthropic gets the GPUs it needs but no real security of tenure.

This is a strange dynamic. Anthropic is effectively subsidizing its competitor's infrastructure in exchange for compute access that can be revoked with three months notice. The "either party can terminate upon 90 days' notice" clause in the S-1 filing sounds like a standard out, but for a company that's organized its production capacity around a facility, 90 days is not a lot of runway to replace 220,000 GPUs.

The charitable read: Anthropic's team is smart enough to know this, and this is a bridge contract while the Amazon and Google capacity scales up. The Colossus access gives them near-term GPU availability that they phase out as more permanent infrastructure comes online.

The less charitable read: Anthropic agreed to pay $15 billion a year to Musk's company for compute they don't control, in a deal that could be terminated by the other side 90 days before they'd even see it coming in revenue forecasts.

---

## What This IPO Wave Actually Means

The real story this week isn't Anthropic's operating profit in isolation. It's that we now have three companies — Anthropic, OpenAI, and SpaceX — simultaneously racing toward trillion-dollar public market valuations, with their financials entangled.

SpaceX's S-1 is only public because SpaceX is going public. The $1.25B/month Anthropic deal wasn't disclosed to you until Elon Musk needed public investors to understand SpaceX's revenue base. OpenAI is reportedly [confidentially filing its IPO](https://www.cnbc.com/2026/05/20/openai-ipo-filing.html) now. When those S-1 disclosures arrive, expect similar reveals about the financial architecture that connects these companies in ways the press releases don't show.

AI company profitability in 2026 is real. The numbers are real. The growth is extraordinary. But the financial plumbing that makes it work — who owes whom how much, which compute commitments land when, what happens if any one counterparty changes their mind — is only now becoming legible to anyone outside the inner circle.

One footnote in a rocket company's IPO filing changed the story. There will be more footnotes.

---

*Sources: [WSJ](https://www.wsj.com/tech/ai/mind-blowing-growth-is-about-to-propel-anthropic-into-its-first-profitable-quarter-7edbf2f4) · [SpaceX S-1 (SEC)](https://www.sec.gov/Archives/edgar/data/1181412/000162828026036936/spaceexplorationtechnologi.htm) · [Anthropic compute announcement](https://www.anthropic.com/news/higher-limits-spacex) · [The Verge on $15B/year deal](https://www.theverge.com/science/935229/spacex-anthropic-ipo-ai-capacity-deal-colossus) · [CNBC on Anthropic Q2](https://www.cnbc.com/2026/05/20/anthropic-revenue-explosive-growth-ipo-profitable-quarter.html) · [Ed Zitron](https://www.wheresyoured.at/anthropics-profitability-swindle/)*
