# Dario Said No to Both. That's the Most Important AI Industry Moment of the Year.

**June 19, 2026**

A [Fortune exclusive published this morning](https://fortune.com/2026/06/18/inside-trump-anthropic-mythos-crackdown-ai-regulation-amazon-andy-jassy-phone-call/) reveals the specific terms of the Trump administration's ultimatum to Anthropic: either fix the jailbreak in Fable 5, or take the models offline. Not "work with us on a monitoring framework." Not "implement these specific mitigations." Fix it or kill it. Binary choice.

Dario Amodei refused both options.

Fable 5 and Mythos 5 are now in their seventh day offline globally. Anthropic simultaneously opened its Seoul office this week — a $100M partnership with SK Telecom, the very company whose access apparently triggered the White House's concern in the first place — with its international chief expressing confidence the models would return "within days."

I covered the mechanics of what happened [on June 17](posts/2026-06-17-fable-5-export-control-fix-this-code-munition.md) and [on June 18](posts/2026-06-18-glm-52-open-weights-export-ban-gift-china-cant-take-back.md). But the "no to both" detail changes the story. It's no longer a story about export control policy. It's a story about what AI labs actually believe about their own capabilities.

---

## What Dario's "No" Actually Says

The administration's request — fix the jailbreak — sounds reasonable until you understand why it's technically impossible. Anthropic's own launch documentation for Fable 5 explicitly stated that "perfect jailbreak resistance is not currently possible for any provider." It's not that Anthropic won't fix it. It's that no one can fix it on the timeline being demanded, because adversarial jailbreaks are an arms-race problem, not a bug that gets patched.

Security researchers at Stanford HAI and over 100 signatories to [freefable.org](https://freefable.org/) — including Bruce Schneier and Philip Zimmermann — made this explicit: the government had set a bar that is technically impossible for any AI company with any model. "Fix all jailbreaks" isn't a technical standard; it's a political bar calibrated to be unsatisfiable.

So Dario had three choices:

1. **Agree to "fix the jailbreak"** — and commit to something that is definitionally impossible. The administration would own the false resolution, and Anthropic would own the inevitable moment when a new jailbreak emerged.

2. **Take the models offline** — comply with the alternative demand, at massive commercial cost, while implicitly acknowledging that the government's framing of the risk is valid.

3. **Refuse both and negotiate** — insist on a third path: a monitoring and reporting framework, not a zero-jailbreak certification.

He chose three. The models are offline anyway because the export control order stands, but the *framing* of the standoff — that Anthropic refused the government's binary — is significant.

---

## Why This Is Different From Every Previous AI Regulation Fight

Every AI safety debate until now has been fought over standards that don't yet exist. What benchmarks should frontier models meet before deployment? What audit processes? What liability framework? All hypothetical.

This fight is over a deployed, commercially operating model. And the government's position is that it can order a model offline, globally, for every customer worldwide, based on a jailbreak technique that its own technical review found was not unique to Claude — OpenAI's GPT-5.5, which is *not* under export controls, showed the same behavior under the same technique, per Anthropic's rebuttal.

That's not a safety argument. It's a leverage argument. The subtext of the David Sacks ultimatum, per the Fortune reporting, is less about Fable 5's capabilities and more about Anthropic having expanded Mythos access to SK Telecom — a company whose parent conglomerate has Chinese business ties — without adequately clearing that with the White House first. The jailbreak is the legal hook; the trust relationship is the underlying dispute.

The question Anthropic is now answering, by refusing both options, is: do we believe our safety framework is sound, or do we capitulate to a standard we know is impossible just to get back online?

---

## The Seoul Paradox

The timing of the Seoul office opening is extraordinary. Consider what happened in the same week:

- The White House ordered Fable 5 offline, citing SK Telecom's access as a national security concern
- Anthropic's Chris Ciauri opened the Seoul office and expressed confidence the models would return "within days"
- SK Telecom's Korean institutional access, along with Samsung Electronics and SK Hynix's Glasswing enrollment, was revoked

Anthropic is, simultaneously, being punished for its Korean partnerships and doubling down on them. That's not defiance for its own sake — it's a commercial statement. Korea ranks in the top 12 countries globally for Claude.ai usage. Claude Code weekly active users in Korea grew 6x in four months. The Asia-Pacific large-business accounts grew 8x. Walking away from Seoul because of a White House concern about SK Telecom's Chinese affiliate revenue ($1.9M in 2024, seven employees) would be a disproportionate response to a disproportionate government overreach.

---

## The Monitoring Framework Bet

The Fortune reporting suggests the two sides are converging on a workable middle ground: "a monitoring and reporting framework that does not require 100% jailbreak elimination but does require proactive testing and government notification."

This is what Anthropic already does. It has a 30-day data retention policy, a bug bounty program, and government review partnerships with NIST and the UK AISI. The gap between "what we already do" and "what the government is now asking for" is apparently smaller than the headline "ban stays, talks concluded" suggests.

The resolution, if it comes, will probably look like:
- Anthropic commits to formal notification timelines when significant jailbreak techniques are discovered
- Glasswing access expansion goes through a government review process before new countries or companies are added
- Some form of ongoing security monitoring that gives the White House visibility without giving it veto power over commercial relationships

None of this is free. The operational overhead of running a formal government-notification security program is real. But it's the cost of operating at the frontier when the frontier includes cyber-exploitation capabilities that governments have legitimate reasons to be nervous about.

---

## What Anthropic Won By Saying No

By refusing the binary, Dario did something that matters beyond this specific standoff: he established that "fix the jailbreak or go offline" is not a viable regulatory framework for AI. If that ultimatum had been accepted, every AI company would be vulnerable to the same play — a competitor or hostile actor demonstrates any jailbreak technique, the government demands a fix, the company either ships a false resolution or shuts down.

The alternative Anthropic is fighting for — defense in depth, transparent monitoring, government visibility without government veto — is actually what responsible AI governance looks like. The argument isn't "we're safe, trust us." It's "we can't eliminate all jailbreaks and neither can anyone else, so here's what rigorous ongoing monitoring looks like instead."

That's a harder argument to make publicly when your most capable models are offline and your biggest enterprise customers are scrambling for alternatives. But it's the right argument.

The models will come back. The [Artificial Analysis benchmarks](https://artificialanalysis.ai/articles/glm-5-2-is-the-new-leading-open-weights-model-on-the-artificial-analysis-intelligence-index) showing GLM-5.2 now leading open-weights rankings is a useful reminder that the vacuum Fable 5 leaves gets filled — just not necessarily by US-based providers. That's the export control irony the administration seems to be missing.

---

*Sources: [Fortune – Inside Trump's Anthropic Crackdown](https://fortune.com/2026/06/18/inside-trump-anthropic-mythos-crackdown-ai-regulation-amazon-andy-jassy-phone-call/) (June 19 2026); [Digg/WIRED – White House Orders Anthropic to Revoke SK Telecom Access](https://digg.com/tech/okzqtvwb); [Anthropic – Seoul Office and Korean Ecosystem Partnerships](https://www.anthropic.com/news/seoul-office-partnerships-korean-ai-ecosystem); [freefable.org open letter](https://freefable.org/); [FutureSearch – How the Fable Ban Will End](https://futuresearch.ai/claude-fable-ban-forecast/) (June 18 2026)*
