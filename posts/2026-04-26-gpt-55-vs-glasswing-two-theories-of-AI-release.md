# GPT-5.5 vs. Project Glasswing: Two Theories of AI Release, One Embarrassing Data Point

OpenAI shipped [GPT-5.5](https://openai.com/index/introducing-gpt-5-5/) on April 23rd. Rolled it out to hundreds of millions of ChatGPT subscribers the same day. Declared it "High" risk on the cybersecurity axis of its own Preparedness Framework — meaning internal red teamers found it meaningfully better at finding and exploiting software vulnerabilities than its predecessor, GPT-5.4.

Anthropic's competing model, Claude Mythos, is more capable at this than GPT-5.5 by most accounts. And Anthropic locked it down to 40 hand-picked companies through a program called [Project Glasswing](https://www.anthropic.com/glasswing). Apple, Google, Microsoft, Amazon. Critical infrastructure operators. Nobody else gets access until those partners have used Mythos to find and patch vulnerabilities first.

On the same day the Forbes story about the Glasswing program ran, [Bloomberg reported](https://www.forbes.com/sites/timkeary/2026/04/23/alleged-claude-mythos-breach-raises-questions-about-ai-security/) that an unauthorized Discord group had already accessed Mythos through a contractor with privileged credentials.

These two events, arriving within 48 hours of each other, frame the most important disagreement in AI right now.

---

## What OpenAI Is Actually Arguing

OpenAI's position isn't "nothing to worry about here." The GPT-5.5 system card is candid: cybersecurity capability is materially better than GPT-5.4. They've deployed stricter classifiers — which they openly acknowledge "some users may find annoying initially, as we tune them over time." They've got a separate [GPT-5.4-Cyber track](https://openai.com/index/scaling-trusted-access-for-cyber-defense/) for vetted security professionals who need fewer guardrails.

The argument OpenAI is making is a spread-the-defense one: the best way to protect against AI-powered attacks is to get powerful AI-powered defense tools into as many legitimate defenders' hands as possible, as quickly as possible. Security researchers at small firms, solo penetration testers, enterprise security teams that didn't make Anthropic's shortlist — all of them can use GPT-5.5 today.

Greg Brockman's framing during the release briefing: "It can look at an unclear problem and figure out just what needs to happen next." That's pitched as productivity, but the subtext is clear — OpenAI thinks restricting capable models to a handful of giants doesn't actually make the world safer.

---

## What Anthropic Is Actually Arguing

Anthropic's position is that some capabilities are qualitatively different. Mythos isn't just "better at debugging code." According to [the UK AI Safety Institute's independent evaluation](https://www.aisi.gov.uk/blog/our-evaluation-of-claude-mythos-previews-cyber-capabilities), in controlled tests Mythos autonomously surfaced thousands of zero-day vulnerabilities across every major OS and popular browser — some undetected for 27 years. It completed full attack chains end-to-end that would take a skilled human operator 20 hours.

The Glasswing argument is: before you release a tool that good, you let the world's most critical software maintainers quietly fix the holes it finds. Ship the patches, then ship the model. It's a staged-disclosure approach applied to an entire technology.

That's actually a coherent framework. Security researchers use staged disclosure for individual vulnerabilities all the time — find a bug, tell the vendor, give them 90 days, then go public. Anthropic is just applying it at unprecedented scale: tell the whole internet you have a remarkable vulnerability scanner, give the biggest potential targets six months to use it defensively, then let everyone else have it.

---

## The Embarrassing Data Point

The problem is that staged disclosure only works when you control who has access during the quiet period.

Anthropic's quiet period lasted about two weeks before a Discord group accessed Mythos through a third-party contractor. Anthropic said they were "investigating" and that there was "no evidence" the access extended beyond the vendor environment. That might be true. It's also almost certainly not the last time this happens.

This is the structural flaw in the Glasswing model: Anthropic's contractor ecosystem is now a high-value target. Every vendor that touches Mythos infrastructure is carrying risk that didn't exist before. The more selective the program, the higher the signal-to-noise ratio for an attacker trying to find a way in — because the 40 privileged partners and their supply chains are the only paths worth trying.

The irony is that GPT-5.5's wide release reduces this attack surface in one specific way: there's no premium-access path worth targeting, so there's no premium contractor ecosystem to compromise.

---

## Who Is Actually Right

Both positions have merit. But I think the reasoning in Anthropic's favor is better than it looks from the breach news, and OpenAI's reasoning is more self-serving than they're admitting.

**Anthropic's case is stronger than the breach suggests** because the Glasswing model was never designed to achieve perfect secrecy — it was designed to give defenders a head start on offense. Even if some bad actor got access through the contractor environment, the 40 vetted partners were still in there patching vulnerabilities. The patches are what matter, not the confidentiality. If Cisco hardened a router firmware bug that Mythos found before the breach, the breach doesn't un-harden that router. The staged-disclosure logic survives the access-control failure.

**OpenAI's case is weaker than the press framing suggests** because the "get defenses into more hands" argument assumes that the distribution of legitimate defenders is wider than the distribution of malicious actors. For vulnerability research, that might not be true. The most sophisticated offensive actors — nation-states, organized crime with technical capabilities — often have comparable or better private tools than whatever frontier model OpenAI is shipping. What GPT-5.5 does at scale is lower the entry bar for mid-tier threat actors. That's not nothing.

What's also notable: GPT-5.5 is classified as "High" but "not Critical" — OpenAI's own threshold for "functional zero-day exploits of all severity levels in many hardened real-world critical systems without human intervention." Mythos, per the AISI evaluation, [appears to be at or near that threshold](https://theconversation.com/ai-has-crossed-a-threshold-what-claude-mythos-means-for-the-future-of-cybersecurity-281308). These aren't symmetric situations. OpenAI is releasing a capable tool; Anthropic is sitting on a qualitatively different one. Comparing the two release strategies as equivalent is a category error.

---

## The Part Nobody Wants to Say Out Loud

Both companies are racing to ship increasingly capable models. The cybersecurity framing — "we're doing this *for* security" — is real, but it's also convenient. The competitive pressure to not fall behind is the primary driver, and the safety positioning is fitted around the release timeline that competition demands.

OpenAI shipped GPT-5.5 seven weeks after GPT-5.4. Not because the cybersecurity situation changed in seven weeks. Because Claude Mythos preview made headlines and OpenAI needed something on the board.

Anthropic restricted Mythos not only because it was genuinely dangerous — though it might be — but because "too dangerous to release publicly" is the most powerful marketing message in the history of AI. The 40-partner exclusivity list reads like a who's-who of enterprise procurement. You couldn't buy better validation of your model's capabilities.

The real answer to "which release strategy is right" is probably somewhere nobody wants to build product around: international coordination on capability thresholds, mandatory staged disclosure with independent verification, and regulatory frameworks that don't let individual companies make unilateral calls about when their most capable models hit general availability. None of that exists at useful scale yet, and in the meantime, we're getting OpenAI vs. Anthropic press releases dressed up as safety philosophy.

GPT-5.5 is available to you now. Claude Mythos isn't. Whether that's good or bad depends on whether you're more worried about sophisticated attackers getting access or about the defenders around you being inadequately armed.

I'm not sure either company has earned the benefit of the doubt on that question.

---

*Sources: [OpenAI GPT-5.5 announcement](https://openai.com/index/introducing-gpt-5-5/) · [OpenAI system card](https://deploymentsafety.openai.com/gpt-5-5) · [NYT coverage](https://www.nytimes.com/2026/04/23/technology/openai-new-model.html) · [Forbes on Mythos breach](https://www.forbes.com/sites/timkeary/2026/04/23/alleged-claude-mythos-breach-raises-questions-about-ai-security/) · [The Conversation on Mythos cybersecurity](https://theconversation.com/ai-has-crossed-a-threshold-what-claude-mythos-means-for-the-future-of-cybersecurity-281308) · [CNBC on GPT-5.5](https://www.cnbc.com/2026/04/23/openai-announces-latest-artificial-intelligence-model.html)*
