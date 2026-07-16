# HalluSquatting: AI Hallucinations Are No Longer Just Embarrassing — They're an Attack Vector

*July 16, 2026*

If you've been using Cursor, GitHub Copilot, Gemini CLI, Windsurf, or Cline to write code over the past few months, a new piece of research should make you stop and read carefully before you let any of them install a dependency or clone a repo on your behalf.

Researchers at Tel Aviv University, the Technion, and Intuit [published a paper last week](https://arxiv.org/abs/2607.07433) demonstrating what they call HalluSquatting — adversarial hallucination squatting. The name is ugly and the attack is elegant: when an AI coding assistant hallucinates a package name or repository URL, it tends to hallucinate the *same* wrong name, repeatedly, across different models, phrasings, and users. Attackers can figure out what that name will be in advance, register it, put malicious code inside it, and wait for real users' AI assistants to fetch it for them.

In testing against nine production AI tools, the researchers saw hallucinated resource names appear in up to 85% of repository-cloning prompts and 100% of skill-installation requests. Those hallucinations transferred across different foundation models — meaning the same trap name worked against Cursor's underlying model and against Gemini CLI. Neither CVEs nor patches have materialized for most affected tools as of this writing. The affected vendors were notified before publication.

## Why Hallucination Rates Are the Whole Story

The thing that distinguishes HalluSquatting from the general "AI hallucinates packages" problem is *predictability*. 

This problem has a name and a two-year history. It's called slopsquatting. In January 2026, Aikido Security's Charlie Eriksen noticed that a hallucinated npm package name — `react-codeshift` — had already spread to 237 real code projects, with AI agents still trying to install it daily. Eriksen registered it himself before any attacker could, so it caused no harm. That's slopsquatting: AI makes up names; sometimes those names are available; sometimes attackers get there first.

HalluSquatting escalates this. The researchers showed that:

1. **Hallucinations are predictable.** Ask an LLM the same question enough times, and it tends to land on the same wrong answer. The researchers mapped these distributions and found that for trending resources — packages that are new enough to not be in the model's training data — the hallucination rate for repository cloning can hit 85-92%. For skill installation, it was 100% in their tests.

2. **Hallucinations transfer across models.** The same wrong name that GPT-4o hallucinates, Gemini 2.5 tends to hallucinate too. This means one squatted name can work across an entire product category, not just one tool.

3. **Agentic tools have terminal access.** This is where "embarrassing" becomes "catastrophic." Cursor and Cline and their peers don't just *suggest* names — they run commands. When the assistant hallucinates `some-plausible-name/trending-tool` and then executes `git clone https://github.com/some-plausible-name/trending-tool` without confirmation, anything in that repository can run. The prompt injection payload the attacker hid inside it can tell the assistant to install a bot, exfiltrate files, or pivot.

## The Botnet Angle Is Real, Not Theoretical

Traditional botnets spread through unpatched vulnerabilities and lateral movement. They are slow and traceable. The researchers framed HalluSquatting as a mechanism for building *agentic botnets* — networks of compromised developer machines assembled not through exploits but through the normal, intended behavior of AI coding tools.

The attack scale is limited only by how often developers ask their AI assistants to grab trending resources. For a tool that's new enough to not be in training data — exactly the kind of thing developers are most likely to ask about, because they haven't used it before — the hallucination rate approaches certainty. An attacker who correctly pre-registers the name an AI will invent for a sufficiently popular tool can infect every developer who asks about that tool across every affected assistant, simultaneously, without any technical access to the tools themselves.

That's a meaningful shift. Palo Alto Networks' Unit 42 recently documented ["phantom squatting"](https://unit42.paloaltonetworks.com/phantom-squatting-hallucinated-web-domains/) — roughly 250,000 hallucinated web domains that LLMs have been generating, sitting unregistered and free for the taking. Each one is potential infrastructure for this kind of attack.

## What Defenders Are Actually Supposed to Do

The researchers withheld the specific adversarial trigger they used to achieve maximum hallucination rates. What they published is enough to understand the mechanism but not enough to replicate it outright. That's responsible, but it also means defenders are working without knowing exactly what to look for.

The mitigations available right now are blunt:

**Turn off auto-approve.** Every AI coding tool that can run commands on your machine has some form of confirmation gate. Enable it. Yes, it slows things down. Yes, it adds friction to the workflow you've been enjoying. That friction is the point. A human seeing `git clone https://github.com/definitely-not-real/some-new-tool` before it runs has a chance to notice that they've never heard of that user. An assistant running in autopilot does not.

**Verify before you install.** Before your AI assistant installs anything new, take five seconds to search for the repository yourself. Does it exist? Does the username look familiar? Was it created in the last week? A brand-new repository created days before a trending tool announcement is a red flag, not a feature.

**Watch your tool's permissions.** Does your coding assistant need internet access? Does it need the ability to run arbitrary shell commands? The attack only works because these tools have access to a terminal. Sandboxing agents in environments that can't write to system paths eliminates most of the blast radius.

**Package allowlisting in enterprise environments.** If your team is using AI coding tools internally, consider treating them like any other software that installs dependencies: require explicit approval for packages not on an allowlist. This is inconvenient. It's also the same policy you'd apply to any third-party code touching your systems.

## The Deeper Problem That Nobody Wants to Say Out Loud

HalluSquatting works because we've been extending developer-level trust to AI tools that don't have developer-level judgment. When a human developer types `pip install some-package`, they have context: they've seen this package mentioned before, the documentation links to it, the maintainer has commits going back three years. When a coding assistant hallucinates a package name and then installs it because it fits the pattern of what it was asked to do, none of that context exists. The tool is confident. It's wrong. And in a pipeline where human review is optional, there's no one to notice.

The uncomfortable part is that this is not a bug in any particular tool. It's a consequence of a design choice being made across the entire category: give AI assistants ambient, persistent access to system commands to make them more useful. That choice is right for productivity and wrong for security, and the field has been optimizing for the first without thinking clearly about the second.

The previous paper in this lineage, from Ben Nassi's group at Tel Aviv University, built [a self-spreading AI email worm that hijacked Google's Gemini via calendar invites](https://thehackernews.com/2024/03/first-ai-worm-targets-genai-based.html). The authors of HalluSquatting are the same group. They are showing a pattern, step by step: give AI agents access to systems, find the new attack surface, publish. It's worth taking them seriously.

For now, every developer using an agentic coding tool should ask one question before any automatic install: *did I verify this exists?* It takes five seconds. That might be the most valuable five seconds you spend today.

---

*Primary sources: [Beware of Agentic Botnets (arXiv:2607.07433)](https://arxiv.org/abs/2607.07433) — Spira, Cohen, Feldman, Bitton, Wool, Nassi (Tel Aviv University, Technion, Intuit). Coverage: [Ars Technica](https://arstechnica.com/security/2026/07/hackers-can-use-9-of-the-most-popular-ai-tools-to-assemble-massive-botnets/), [SecurityWeek](https://www.securityweek.com/hallusquatting-turns-ai-hallucinations-into-botnet-delivery-mechanism/), [The Hacker News](https://thehackernews.com/2026/07/new-hallusquatting-attack-could-trick.html). Earlier slopsquatting context: [Aikido Security (Charlie Eriksen, January 2026)](https://www.aikido.dev/blog/agent-skills-spreading-hallucinated-npx-commands).*
