# The Curl Security Report Flood Got Worse. Not Because the Reports Got Worse — Because They Got Better.

*May 31, 2026*

Daniel Stenberg has been maintaining curl for nearly thirty years. He does it for fifty hours a week, including late nights and weekends, because it's his job and his passion simultaneously. curl runs in five billion installations. It's in your phone, your car, your streaming device, probably your toaster.

On May 26th, he published a post called ["The pressure"](https://daniel.haxx.se/blog/2026/05/26/the-pressure/) that deserves more attention than it's getting.

---

Here's the short version: AI-powered security tools are now generating real, valid, confirmed vulnerability reports for curl at roughly 4-5 times the rate they were in 2024. Not slop — actual bugs. And it's destroying the project's ability to function.

This is not the story everyone expected. For most of 2024 and 2025, the AI-flooding-open-source narrative was about garbage: hallucinated CVEs, fake vulnerabilities, bug reports that read like they were written by an LLM that had never actually run the code. Stenberg wrote about ["death by a thousand slops"](https://daniel.haxx.se/blog/2025/07/14/death-by-a-thousand-slops/). curl shut down its bug bounty entirely in January 2026. The problem was junk volume.

Then something changed in March 2026.

As Stenberg documented in ["High-Quality Chaos"](https://daniel.haxx.se/blog/2026/04/22/high-quality-chaos/) last month: the slop stopped. The confirmed vulnerability rate went back up to the pre-AI 15-16% range, which is actually good — it means the signal-to-noise ratio is healthy. But the volume doubled again. So you now have twice as many reports, each one of which is probably a real issue that requires real human expertise to verify, patch, and document.

The math is simple and brutal: if each legitimate security report takes two to four hours to properly triage (understand the claim, verify it, write a patch, figure out when the bug was introduced, draft a CVE advisory, communicate with the researcher), and the rate of legitimate reports doubles, you've just doubled the time required to keep the project secure. Except Stenberg still has the same twenty-four hours in a day.

He writes: *"I spend almost all my days right now working through the list of reported security issues that we have on Hackerone."* For a one-of-a-kind infrastructure maintainer who previously spent his time building things, that sentence is an alarm bell.

---

## Why This Pattern Is Structurally Different From "AI Slop"

When AI generated garbage security reports, the fix was relatively tractable: build better filters, educate platforms, charge a small fee to submit, require a reproducible proof-of-concept. Stenberg tried all of these. They worked — they eliminated the slop.

But the new situation isn't a slop problem. You can't filter your way out of it, because the reports are real. The researchers using AI tools to find vulnerabilities are doing legitimate security research. They deserve responses. The vulnerabilities are real. Ignoring them is not an option for a library running in five billion devices.

What AI has done is industrialize the vulnerability *discovery* side of the pipeline while leaving the vulnerability *remediation* side completely manual. A skilled researcher with Claude or Mythos can now systematically probe a codebase and find real bugs at a rate that would have taken years without AI assistance. That's genuinely good for security! But the human on the receiving end — who has to understand each bug in context, write a quality fix, and ship a coordinated disclosure — isn't going faster. If anything, the bar for a "good" advisory has gone up because the researchers submitting reports are now producing more thorough writeups.

You've created a system where the demand side is AI-accelerated and the supply side is human-constrained. Every rate-limited resource in a pipeline becomes a bottleneck when you pour more throughput in. curl's bottleneck is Daniel Stenberg's calendar.

---

## The 1-Bit Bonsai Problem Doesn't Apply Here

There's a tempting response: "use AI to help triage the reports." And to some degree, yes, AI tools can help filter duplicates, summarize reports, and flag obvious false positives. Stenberg's team does use tooling.

But the core of the problem is irreducible. You cannot use an AI to determine whether a security bug is exploitable in a way that justifies a CVE without a human who deeply understands the codebase making that call. That's not a failure of current AI — it's a function of the nature of security work. The judgment about severity, the weighing of exploitation difficulty against real-world impact, the decision about whether to file under CVE or just ship a quiet fix: these require someone who can be held accountable, who understands curl's users and deployments at a level that no AI has been trained on.

What AI *has* done is make the easy part (finding potential bugs) cheap and fast, while leaving the hard part (responsible disclosure, coordinated patching, user communication) exactly as expensive as before. You could even argue it got more expensive, because now you're fielding reports with more technical depth that require more technical depth to respond to.

---

## The Structural Problem Nobody Is Fixing

Stenberg isn't the only one in this position. The curl story is prominent because he writes about it publicly and eloquently, and because curl is unusually critical infrastructure. But the same dynamic is playing out across every well-maintained open source project. Maintainers of OpenSSL, SQLite, the Linux kernel networking stack, and hundreds of smaller but widely-deployed libraries are all in variations of the same bind.

The [OpenSSF's CTO predicted earlier this month](https://www.youtube.com/watch?v=qOK1E9Ud85o) that a major AI-assisted breach of open source infrastructure is statistically likely before the end of 2026 — not because AI will magically break security, but because maintainers will eventually burn out and stop being able to respond to the volume. That's the actual attack vector: exhaustion.

The EU's Cyber Resilience Act adds another wrinkle. It requires commercial manufacturers to submit security patches upstream, which means that when a CVE lands in a widely-used component, thousands of companies will simultaneously send pull requests. The intent is good. The effect on maintainer bandwidth is another multiplier on the same problem.

This is an infrastructure funding problem wearing a technology costume. Open source security has always relied on a small number of deeply skilled people who happen to care enough to work for far below their market rate. AI has now made it possible to generate legitimate demand for their time at machine speed. The supply hasn't changed.

---

## What Actually Needs to Happen

The honest answer is that "curl should have more maintainers" is correct but insufficient. You can't just hire five people to be Daniel Stenberg — the institutional knowledge, the accumulated context about why every design decision was made, the network of trust with the security research community, that's not duplicable by adding headcount.

A few things that would actually help:

**Better coordinated disclosure infrastructure.** Platforms like HackerOne need to invest in tooling that reduces the cognitive overhead per report — not AI that triages for you, but workflow improvements that make it faster to do the human work. Structured report formats with mandatory reproduction steps, automated duplicate detection, integration with patch automation for known bug classes.

**Funded maintainership.** The companies shipping five billion curl integrations — Google, Apple, Microsoft, Meta, Amazon — collectively generate hundreds of billions of dollars on the back of this code. One of them funding a small dedicated security response team for curl would be trivial for them and transformative for the project. Some of this exists through the Open Source Security Foundation, but not at the scale the problem now requires.

**AI-assisted patch generation.** The hardest part of the bottleneck is not triage, it's remediation. If an AI tool could propose a patch for a reported vulnerability — not as a final answer but as a starting point that a maintainer can review and modify — that would meaningfully reduce the per-report time cost. Some of this exists. It needs to be better.

What won't help: adding more friction to vulnerability submissions. That was the slop fix, and it worked for slop. The new reports are legitimate. Penalizing researchers who found real bugs because the project can't handle the volume is the wrong approach.

---

Stenberg ends "The Pressure" on an honest note: he's okay, he's not quitting, he loves the work. But he's also transparently exhausted in a way that should be a five-alarm signal for everyone who ships software that depends on curl. Which is, to a first approximation, everyone.

AI found the vulnerabilities. Humans still have to fix them. Until that changes, every advance in AI security research creates a corresponding debt on someone's calendar — and right now, that someone is a single maintainer with a thirty-year relationship with a critical piece of the internet.

---

*Primary sources: Daniel Stenberg's ["The Pressure"](https://daniel.haxx.se/blog/2026/05/26/the-pressure/) (May 26, 2026) and ["High-Quality Chaos"](https://daniel.haxx.se/blog/2026/04/22/high-quality-chaos/) (April 22, 2026). Background: [Techzine on open source security threats in 2026](https://www.techzine.eu/blogs/security/141699/why-open-source-faces-its-biggest-security-threat-in-2026/).*
