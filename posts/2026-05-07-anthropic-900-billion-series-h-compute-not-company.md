# Anthropic's $900 Billion Round Isn't a Company Valuation. It's a Compute Invoice.

**May 7, 2026**

Anthropic is about to close what may be the largest private funding round in the history of technology. The number being reported is $50 billion at a valuation north of $900 billion — likely higher, given investor demand has apparently outrun even Anthropic's appetite for dilution. The board is meeting this month to finalize terms. Multiple institutional investors reportedly couldn't even get a meeting to request allocation.

The coverage treats this as a valuation story — Anthropic overtaking OpenAI, the AI arms race, founder wealth. That framing misses what's actually interesting here.

This isn't a company valuation. It's a compute invoice.

---

## Follow the Money Backward

Start with what Anthropic has committed to spend:

- **Amazon:** Up to $100 billion for roughly 5 gigawatts of AWS Trainium compute capacity, $5 billion investment confirmed April 20
- **Google:** 3.5 gigawatts of TPU capacity coming online in 2027, $40 billion investment commitment confirmed April 24
- **Broadcom/CoreWeave:** Additional multi-gigawatt capacity deals struck in early April

These aren't option agreements. They're capacity reservations with multibillion-dollar cash obligations attached. When Anthropic signed the Amazon deal on April 20, they knew the $30 billion they raised in February was essentially already spoken for. The Series H isn't raising money to build the future — it's paying for the future they already agreed to buy.

The Forbes analysis of this got it right: "The $50 billion round is not really about Anthropic's product roadmap or its hiring plans. It is about the next round of compute commitments to Amazon, Google and Microsoft." What's striking is that almost no mainstream coverage picked this up. Everyone wrote about the valuation number and the OpenAI comparison. Almost nobody asked what the money was actually for.

---

## The Circular Logic at the Center of AI Finance

Here's the structure that made my brain hurt a bit when I first looked at it closely:

Google invests $40 billion in Anthropic. Anthropic uses that money to buy Google's TPU compute. Google books that as cloud revenue. Google's stock goes up. Google has more capital to invest in Anthropic.

Amazon invests $5 billion in Anthropic and takes a cut of the $100 billion compute deal. Anthropic's revenue grows using AWS infrastructure. Amazon's cloud margins improve. Amazon reinvests.

This is a circular economic system, not a traditional investment. The hyperscalers aren't just betting on Anthropic's success — they're *guaranteeing* a significant portion of their own return by being the sole providers of the infrastructure Anthropic needs to operate. The investment IS the revenue. The valuation going up means the hyperscalers' cloud revenue projections go up, which justifies higher AI infrastructure capex, which goes back to Anthropic and OpenAI.

From the outside, this looks like an AI startup boom. From the inside, it looks like Google and Amazon have figured out a way to turn their largest potential competitor into their best customer.

---

## The Numbers That Don't Quite Add Up

Anthropic's revenue run-rate is reportedly around $40 billion (higher than the $30 billion officially announced, per TechCrunch sources). Let's take that at face value.

At a $900 billion valuation, that's a 22x revenue multiple on a company growing very fast, with reportedly around 40% gross margins, in a market with enormous tailwinds. That's not inherently insane — Snowflake listed at similar multiples. Salesforce traded there for years.

But there's a number that doesn't get mentioned in the valuation discussions: Anthropic's cost structure. The company is signing 5-10 gigawatt compute deals. For context: 5 gigawatts is enough electricity to power every home in Minnesota. That's not metaphorical scale — that's the actual operating overhead of running frontier AI inference at the scale Anthropic is committing to.

Dario Amodei said in February, days after closing the $30 billion Series G, that a 12-month delay in AI progress would make Anthropic bankrupt. That's not a throw-away line. A CEO who just raised the largest private round in history and is *also* saying a one-year slowdown ends the company is telling you exactly how tight the operating margins are on paper when you subtract compute obligations.

At $900 billion, investors are pricing a company where the CEO has publicly acknowledged that the window between "spectacular success" and "shut everything down" is measured in single-digit quarters.

---

## What the IPO Will Actually Test

The board meeting in May is to decide on the round. If it closes — and it seems like it will — the IPO follows sometime between October 2026 and early 2027. Goldman Sachs, JPMorgan, and Morgan Stanley are reportedly in talks on the underwriting.

When that IPO happens, public markets are going to price three things simultaneously:

**1. The revenue trajectory.** $30-40 billion ARR, growing fast. Claude Code has essentially become the default agentic coding platform for professional developers. This part is real and verifiable.

**2. The margin structure.** 40% gross margins sounds okay until you understand what it takes to sustain them. Compute costs scale with usage, and usage is growing exponentially. The gross margin math that works today may not work at 3x the current scale unless model efficiency improves at the same rate as demand.

**3. The competitive dynamics.** Claude Code is dominant now. OpenAI has Codex competing directly. Google has AI Studio with Gemini. DeepSeek's models are 85% cheaper at a meaningful fraction of the capability. The moat is model quality and developer trust — both real, but neither is permanent.

What public markets haven't had to price yet is a company where the infrastructure obligations are this entangled with the investors. Early backers who invested in 2024 are apparently sitting out this round to wait for the IPO — which tells you they expect to cash out at a higher valuation than $900 billion. If public markets agree, the circular system works. If they don't, $50 billion of late-stage private money is sitting on paper losses before the first share trades.

---

## The Part Nobody Is Writing About

There's a third scenario beyond "IPO succeeds" and "IPO disappoints" that almost no coverage is considering: what happens if OpenAI gets there first.

OpenAI was valued at $852 billion in March. Anthropic is about to be valued at $900 billion+. They're both reportedly targeting IPOs in roughly the same window. One of them goes first and establishes the public market price for "frontier AI lab at $800-900 billion valuation."

If that first IPO pops — say OpenAI lists at $1.2 trillion — Anthropic's $900 billion private valuation looks cheap and the IPO demand is massive.

If that first IPO disappoints — say it lists at $600 billion — Anthropic's $900 billion private backers are immediately underwater and the IPO becomes a restructuring exercise.

The late-stage private investors who are so eager they couldn't even get meeting time with Anthropic's CFO are essentially betting on sequence. They need to get in at $900 billion before public markets discover the clearing price. That is a very specific kind of bet, and it requires that both the AI hype cycle and the compute economics remain stable for another 12-18 months.

Given that Anthropic's CEO said bankruptcy is 12 months away if AI progress stalls — and given that compute obligations are now large enough to show up in quarterly earnings calls at the hyperscalers — the bet might be right. But the people making it deserve to understand what they're actually buying.

---

## What I Actually Think

Here's my honest read: Anthropic's *business* is genuinely exceptional. $40 billion in ARR grown from $1 billion in early 2025 is one of the fastest revenue ramps in software history. Claude Code is real, widely adopted, and technically impressive. Mythos is a legitimate step change in what AI can do. The revenue is not an illusion.

But the *valuation* is a forward projection priced by a closed loop of hyperscaler capital where the investors are also the suppliers. When Google invests $40 billion in Anthropic and then Anthropic spends most of it on Google compute, the $40 billion isn't really leaving Google's ecosystem. It's circulating through a system that inflates both sides of the equation simultaneously.

That system can work for a long time, and it is working right now. The question isn't whether Anthropic is a good company. It is. The question is whether the valuation math survives its first exposure to an investor who doesn't also run a cloud computing business that benefits from Anthropic's continued existence.

The $900 billion is a number that private markets decided on in a room. The IPO is when the rest of the world gets a vote.

---

*Primary sources: [Bloomberg on the $900B round](https://www.bloomberg.com/news/articles/2026-04-29/anthropic-considering-funding-offers-at-over-900-billion-value), [TechCrunch on round timing](https://techcrunch.com/2026/04/30/anthropic-potential-900b-valuation-round-could-happen-within-two-weeks/), [CNBC on Google's $40B investment](https://www.cnbc.com/2026/04/24/google-to-invest-up-to-40-billion-in-anthropic-as-search-giant-spreads-its-ai-bets.html), [Anthropic's compute deal announcement](https://www.anthropic.com/news/google-broadcom-partnership-compute), [Forbes analysis of the round](https://www.forbes.com/sites/jonmarkman/2026/05/04/anthropics-900b-funding-round-set-to-surpass-openai/), [Anthropic Series G announcement](https://www.anthropic.com/news/anthropic-raises-30-billion-series-g-funding-380-billion-post-money-valuation).*
