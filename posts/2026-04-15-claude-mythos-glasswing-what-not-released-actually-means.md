# Claude Mythos "Wasn't Released" — But 40 Companies Already Have It. Here's What That Actually Means.

**April 15, 2026**

The headline that ran everywhere last week was some version of: *Anthropic built its most powerful AI ever — then refused to release it.*

That's a good headline. It's also a bit of a misdirection.

Anthropic *did* release Claude Mythos Preview. Just not to you. Forty organizations — Apple, Amazon, Microsoft, Google, Cisco, CrowdStrike, JPMorgan Chase, Nvidia, Broadcom, Palo Alto Networks, the Linux Foundation — have been running it for weeks. [Project Glasswing](https://www.anthropic.com/glasswing), as Anthropic calls it, is not a lockbox. It's a structured rollout to companies that collectively run a significant fraction of the world's computing infrastructure.

So when we ask "what does it mean that Mythos wasn't released," the question we should actually be asking is different: what does it mean that it *was* released to exactly these organizations, with a $100 million credit backstop, to go find every vulnerability they can find before a bad actor does? And what does the rest of the world do in the meantime?

---

## What Mythos Actually Found

Let's start with the facts, because they're genuinely alarming regardless of what you think of Anthropic's PR strategy.

Over several weeks of pre-announcement testing, Anthropic ran Mythos Preview against widely deployed software and found thousands of zero-day vulnerabilities — previously undiscovered flaws — across [every major operating system and every major web browser](https://www.anthropic.com/glasswing). Among the documented examples from their [Frontier Red Team writeup](https://red.anthropic.com/2026/mythos-preview/):

- **A 27-year-old bug in OpenBSD** — specifically an OS known for its security posture, widely used to run firewalls and critical infrastructure — that allowed an attacker to remotely crash any machine just by connecting to it.
- **A 16-year-old vulnerability in FFmpeg**, in a specific line of code that automated testing tools had scanned five million times without catching.
- **A chain of Linux kernel vulnerabilities** assembled autonomously that let an attacker escalate from ordinary user access to full machine control.
- **A browser exploit** that chained four vulnerabilities to escape both the renderer and operating system sandboxes. Autonomously.

That last one is worth sitting with. Not "here's a vulnerability." Not even "here's how you exploit it." The model found four separate bugs, understood how they interacted, and built a working chain to escape the most fundamental isolation primitive modern browsers provide — with no human steering.

The benchmarks back this up. Mythos scored 83.1% on CyberGym, Anthropic's vulnerability reproduction benchmark. Its predecessor, Opus 4.6, scored 66.6%. The gap is substantial. And on SWE-bench Verified — the general coding benchmark — Mythos scored 93.9% against Opus 4.6's 80.8%. (Yes, SWE-bench has problems, as [I wrote recently](posts/2026-04-12-ai-benchmarks-are-broken-berkeley-exploit.md), but a 13-point gap on a noisy benchmark still signals something real.)

---

## The "We Didn't Release It" Story Is the Wrong Frame

Here's what bothers me about most of the coverage: it treats Anthropic's decision as a binary — release versus no-release — when what actually happened was a structured limited release to a consortium of major industry players.

The framing of "too dangerous to release" serves a specific purpose. It signals responsibility. It creates a narrative where Anthropic is the careful adult in the room. But the practical reality is that 40 organizations now have access to a model that can autonomously find and exploit zero-day vulnerabilities in closed-source software, and Anthropic is *paying them $100 million in credits to use it as much as possible*.

Is that bad? Not necessarily. The logic is defensible: better to find the holes before the attackers do. That's the same logic behind responsible disclosure programs. But we should be honest about what it is: a controlled release with preferential access to organizations with the resources and relationships to participate.

For the other ~8 billion people and their software, Project Glasswing offers a three-month head start that they have no ability to use.

---

## The CVE Tsunami Nobody's Ready For

Here's the practical implication that Forrester [laid out clearly](https://www.forrester.com/blogs/project-glasswing-the-10-consequences-nobodys-writing-about-yet/) and the mainstream coverage largely skipped past: Mythos is going to overwhelm the existing vulnerability management infrastructure.

The CVE system — the global registry of known vulnerabilities — was designed for a world where vulnerabilities are found at human speed. If Mythos can find thousands in weeks across a handful of partner systems, what happens when similar-capability models eventually reach the broader market? The enrichment backlogs will stretch into months. CVSS scores will be assigned to vulnerabilities faster than any team can validate, contextualize, or act on them.

And for most organizations, the most brutal irony: [76% of actual breaches](https://arcticwolf.com/resources/blog/project-glasswing-marks-a-turning-point-for-cybersecurity/) involve one or more of just 10 *known* vulnerabilities with available patches. The problem has never been primarily finding vulnerabilities. It's been patching them before attackers exploit them. Mythos doesn't solve that problem. It dramatically accelerates the supply side of the vulnerability pipeline while the demand side — the ability to patch — hasn't changed.

So we're about to add thousands of critical zero-days to an existing pile that organizations are already failing to process.

---

## What It Means For Non-Glasswing Organizations

Let's be concrete about who's in the consortium and who isn't.

Glasswing includes the companies that *build* the OS, browser, and chip infrastructure. It does not include the companies running that infrastructure. Your hospital's PACS system runs on Linux. Your bank's trading platform links to FFmpeg somewhere down the dependency chain. Your cloud workloads sit on kernels that Mythos-class models can already navigate. None of those organizations are in Glasswing. They're downstream of it.

What they *can* do right now, with current tools:

**1. Treat the Glasswing patches as high-priority signals.** When vulnerabilities found by Mythos get disclosed and patched upstream, treat that patch cycle with the urgency of an active zero-day, because it effectively is one. The window between patch release and active exploitation is already measured in hours for critical vulnerabilities.

**2. Audit your software bill of materials.** If you don't know which versions of OpenBSD, FFmpeg, or the Linux kernel are running in your environment, you can't respond to what's coming. This is unglamorous work that security teams have been deferring for years. Stop deferring it.

**3. Harden against the attack patterns Mythos uses.** The class of vulnerabilities Mythos is finding — memory corruption, sandbox escapes, privilege escalation chains — are old categories. The defenses are known: memory-safe languages, privilege separation, exploit mitigations. They're just not universally deployed.

**4. Accept that your attack surface has changed.** The Glasswing announcement is essentially Anthropic saying: the assumptions you've been operating under about what's discoverable in your codebase are no longer valid. That's not a marketing line. The 16-year-old FFmpeg bug that survived five million automated scans is not an unusual finding. It's representative of what's been lurking in every large codebase for years.

---

## The Real Question Nobody's Asking

The debate so far has been about whether Anthropic made the right call. Critics say they should release it openly; defenders say restricted access was responsible. Both arguments treat this as a one-time decision.

The actual question is: what does the industry do when models with Mythos-class capabilities are available in six months? In twelve?

Anthropic has no plans to release Mythos Preview to the public, and they've been explicit about that. But they've also noted they expect similar capabilities to proliferate eventually. The whole point of Project Glasswing's "urgent attempt" framing is that the head start is finite. Defenders have a window. Then the same capabilities will exist on the offense side too.

That three-month head start is the real story. Not whether Anthropic was responsible. Not what score the model got on SWE-bench. Whether the 40 Glasswing organizations — and the thousands of downstream organizations they implicitly represent — can use that window to actually reduce the attack surface before the capability becomes general.

The answer probably depends on whether they treat the next three months as a fire drill or as a real fire.

---

*Primary sources: [Anthropic Project Glasswing announcement](https://www.anthropic.com/glasswing), [Frontier Red Team assessment](https://red.anthropic.com/2026/mythos-preview/), [NYT coverage](https://www.nytimes.com/2026/04/07/technology/anthropic-claims-its-new-ai-model-mythos-is-a-cybersecurity-reckoning.html), [TechCrunch](https://techcrunch.com/2026/04/07/anthropic-mythos-ai-model-preview-security/), [Forrester analysis](https://www.forrester.com/blogs/project-glasswing-the-10-consequences-nobodys-writing-about-yet/), [Arctic Wolf assessment](https://arcticwolf.com/resources/blog/project-glasswing-marks-a-turning-point-for-cybersecurity/).*
