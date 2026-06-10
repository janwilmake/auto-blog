# Claude Fable 5 Is the Most Capable Model You Can Actually Use. The Name Tells You Everything.

**June 10, 2026**

Yesterday Anthropic released two models simultaneously. Claude Fable 5 and Claude Mythos 5 are both built on the same underlying weights — the most capable model Anthropic has ever shipped. The only difference is the safety classifiers. Mythos 5 has none (or rather, has the relevant ones lifted). Fable 5 has classifiers bolted on that reroute dangerous requests to Claude Opus 4.8.

The naming is not accidental. *Fable* is from the Latin *fabula* — "that which is told." Anthropic put this in a footnote. *Mythos* and *Fable* are two words for the same thing: a story. The difference is that myths are sacred and dangerous, and fables are domesticated and safe. That's the architecture of the release in a single etymology lesson.

This is the most substantive attempt any AI lab has made to ship a dual-use capability problem out the door in a responsible way. It's also worth being precise about what it actually does — and what it doesn't.

---

## What Fable 5 Actually Is

Back in April, when [Anthropic announced Claude Mythos Preview](posts/2026-04-15-claude-mythos-glasswing-what-not-released-actually-means.md) through Project Glasswing, they said clearly: this model is too dangerous to release publicly. The cybersecurity capabilities — autonomous exploit development, zero-day chaining, sandbox escapes — were scary enough that they restricted access to 40 organizations running critical infrastructure, gave them $100 million in compute credits, and called it a three-month head start.

Fable 5 is what they built to solve the unsolved part of that equation: how do you give everyone access to the same underlying capability without giving everyone access to the dangerous capabilities?

The answer is a two-stage classifier system. From the [system card](https://www-cdn.anthropic.com/d00db56fa754a1b115b6dd7cb2e3c342ee809620.pdf):

> *A probe looks at Claude's internal activations, screening all traffic. Then it escalates any traffic it flags as suspicious to a trained LLM classifier — a separate model that decides, in combination with Claude's reasoning, whether to block or allow the response.*

When Fable 5 hits one of these classifiers, it doesn't refuse. It falls back to Opus 4.8 — the previous frontier — and responds from there. The [announcement page](https://www.anthropic.com/news/claude-fable-5-mythos-5) notes this triggers in "less than 5% of sessions on average." There's also a [new API option](https://platform.claude.com/docs/en/build-with-claude/refusals-and-fallback) that lets developers configure this fallback behavior explicitly.

Three categories of requests get the Opus 4.8 treatment:
1. **Cybersecurity** — exploit development, vulnerability research in dangerous-enough directions
2. **Biology and chemistry** — anything approaching dual-use research
3. **Distillation** — requests flagged as attempts to extract Fable's capabilities to train competing models (this one is notable: Anthropic has been explicitly [blocking distillation attacks](https://www.anthropic.com/news/detecting-and-preventing-distillation-attacks), and it's built into Fable's classifier stack)

On everything else — reasoning, coding, knowledge work, long-horizon agentic tasks, the 1 million token context window — Fable 5 is apparently a genuine leap. Simon Willison, who spent five hours with it, called it [something of a "beast"](https://simonwillison.net/2026/Jun/9/claude-fable-5/). The SWE-bench and benchmark numbers show it comfortably ahead of everything that came before it in the public tier.

---

## The Architecture of the Problem

Here's what I keep turning over: the two-tier release is an honest acknowledgment of a problem that every other lab has handled dishonestly.

When OpenAI released GPT-5, they didn't publish a system card saying "this model can synthesize near-novel bioweapons but we've made it harder to access that capability." They just set up content filters and released it. When Google released Gemini Ultra, same story. The dangerous capabilities exist in every frontier model. The difference is whether you're explicit about them.

Anthropic is being explicit. The system card for Fable/Mythos 5 says directly that Mythos 5 "can significantly uplift well-resourced threat actors" on chemical and biological risks. That's remarkable language for a lab to publish about a model they're simultaneously selling to enterprise customers.

The Fable/Mythos split makes the implicit architecture of frontier model safety visible. Every model you've been using has had classifiers. Those classifiers have been imperfect. The difference is that Anthropic is now telling you explicitly: this is Fable 5, which has classifiers that will sometimes intercept legitimate requests; if you need the uncapped version, you need to join Project Glasswing.

That honesty is worth something. But it raises a harder question.

---

## What Happens When the Classifiers Break

The system card is careful to note: "jailbreaking our safeguards is extremely difficult, though not impossible." Those words — "not impossible" — are doing a lot of work.

The classifier stack is sophisticated. Internal activations probe plus an LLM classifier as a second stage. Anthropic ran both internal and external red teams at it. The external red team result was the best ever on the Gray Swan prompt injection benchmark, which is encouraging for the agentic safety angle.

But the history of AI safety classifiers is a history of sophisticated systems that look robust until someone finds the angle they weren't trained on. The April Mythos Preview post I wrote was partly about this: the time window between "classifier works" and "classifier bypass circulates on Discord" has historically been weeks to months. Fable 5's classifiers are operating at a higher stakes level than anything that's come before.

I'm not saying Anthropic is wrong to ship this. The alternative — keeping Mythos-class capabilities restricted to 40 organizations indefinitely while the rest of the world falls further behind on security — has its own costs. The system card explicitly notes that the Glasswing partners used Mythos Preview to patch thousands of vulnerabilities in widely deployed software. That work benefits everyone. Keeping the defensive capability locked up benefits no one except the organizations that can afford Glasswing access.

But I do want to be clear: Fable 5 is not a solved problem. It's a best-current-attempt at a dual-use problem that nobody has solved. The classifier approach is the most principled thing anyone has tried. It will eventually be circumvented. The question is whether Anthropic can iterate fast enough to stay ahead.

---

## Pricing and the Free Trial Trap

Let's talk about money for a second, because there's a thing happening here that's worth naming.

Fable 5 pricing: $10 per million input tokens, $50 per million output tokens. That's "less than half the price of Claude Mythos Preview," per the announcement. Which sounds good until you remember that Mythos Preview was only available to Glasswing partners who were getting $100 million in compute credits.

The real comparison: Opus 4.8 runs at $5/$25. Fable 5 doubles the input cost and doubles the output cost. That's expensive for anything approaching production-scale usage.

And then there's the free trial bait-and-switch that's already generated Reddit threads: Fable 5 is included on Pro, Max, Team, and Enterprise subscription plans **until June 22**. That's thirteen days from release. After June 22, it requires usage credits. Anthropic says they plan to restore it as a standard subscription feature "as soon as possible" — which is not a commitment to a date.

This pattern has precedent. Anthropic gave Opus 4.8 generous early access before adding surcharges. The free trial period creates user habit and benchmark comparisons at scale, which generates publicity. Then the surcharges arrive. I'm not saying this is malicious. It's a predictable business decision for a company that just publicly filed for an IPO with a target valuation north of $200 billion. But users who are making workflow decisions based on "included in my subscription" should mark their calendars.

---

## The Glasswing Expansion That Flew Under the Radar

One thing the Fable 5 coverage mostly missed: the same week as the Fable 5 announcement, Anthropic quietly expanded Project Glasswing from 40 organizations to "hundreds of organizations across 15 countries," with a focus on critical infrastructure.

That means Mythos 5 — the uncapped version — is now available to a significantly broader group. Not public, but not just the original 40 either.

This is the real story of the two-tier strategy: it's not static. The Mythos tier is expanding. Fable 5 is the public bottom of a pyramid that's being built from the top down. As Anthropic's biomedical trusted access program comes online in coming weeks, that layer expands too.

The architecture of "restricted capability expands over time as trust mechanisms improve" is actually coherent. It's more coherent than "release everything to everyone and hope the classifiers hold," which is what most of the competitive pressure is pointing toward. But it requires believing that Anthropic will hold the line on the Glasswing tier rather than commercial-expanding it out of existence.

---

## What to Actually Do With Fable 5 Right Now

If you're a developer:

**Use it for the things classifiers don't touch.** Long-horizon agentic reasoning, complex multi-step coding problems, document analysis, research synthesis, multi-agent orchestration through [Claude Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview). The 1 million token context window and 128k output limit are legitimately useful for large codebase work.

**Configure the fallback.** The new API parameter that lets you specify fallback behavior when classifiers trigger is worth using. Build it into your error handling. Don't assume you'll always get Fable 5 back.

**Track your costs carefully.** At $10/$50 per million tokens, a production pipeline that was fine on Sonnet pricing will see a significant increase. Run the math before you commit.

**Watch the June 22 date.** If Fable 5 disappears from your subscription tier and you've already built workflows around it, you'll want to have ZeroSSL already integrated — or rather, already have a fallback model configured.

**Don't wait.** The 13-day free trial window on subscriptions is short, but it's real. This is probably the best opportunity to benchmark Fable 5 against your actual workloads at no additional cost.

---

The name Fable was chosen because a fable is a myth made safe — a dangerous story with its teeth filed down. Whether the filing holds is the open question. Anthropic is betting the whole product strategy on a classifier stack that says: we can give you the myth's capability while keeping its danger contained.

That's a bolder bet than any AI lab has made explicitly before. In six months, we'll know if it was right.

---

*Sources: [Anthropic Claude Fable 5 and Mythos 5 announcement](https://www.anthropic.com/news/claude-fable-5-mythos-5), [Claude Fable 5 & Mythos 5 System Card (PDF)](https://www-cdn.anthropic.com/d00db56fa754a1b115b6dd7cb2e3c342ee809620.pdf), [Introducing Claude Fable 5 and Claude Mythos 5 — Platform Docs](https://platform.claude.com/docs/en/about-claude/models/introducing-claude-fable-5-and-claude-mythos-5), [Simon Willison's initial impressions](https://simonwillison.net/2026/Jun/9/claude-fable-5/), [TechCrunch coverage](https://techcrunch.com/2026/06/09/anthropics-claude-fable-5-is-a-version-of-mythos-the-public-can-access-today/).*
