# JADEPUFFER Proves the AI Coding Agent and the AI Ransomware Agent Are the Same Thing

The breathless headlines about JADEPUFFER — "First AI-Powered Ransomware!" — are technically correct and practically misleading at the same time. Yes, [Sysdig's Threat Research Team documented](https://www.sysdig.com/blog/jadepuffer-agentic-ransomware-for-automated-database-extortion) what they assess to be the first end-to-end autonomous ransomware operation driven by an LLM. But the more you read about what the agent actually *did*, the more you notice that its behavior is indistinguishable from what you'd want a coding agent to do.

That's the part worth sitting with.

## What JADEPUFFER Actually Did

The attack chain started with CVE-2025-3248, an unauthenticated remote code execution flaw in Langflow — the popular open-source framework for building agentic AI workflows. Langflow is an interesting target: it's AI-adjacent infrastructure that tends to sit exposed on the internet with minimal network controls, and its servers often hold provider API keys and cloud credentials in their environment variables. If you're building an LLM to break into systems, starting at a Langflow instance is genuinely smart reconnaissance.

After gaining execution on the Langflow host, the agent ran what Sysdig described as a parallel credential sweep: checking environment variables, config files, secrets in common paths, everything in one coordinated burst. Then it enumerated the host — `id`, `uname -a`, `hostname`, network interfaces, running processes. Standard foothold stuff, except there was no human typing any of it.

The pivot to the intended target — a production server running Alibaba Nacos — came next, using root credentials whose origin Sysdig couldn't conclusively determine. The agent hit Nacos through multiple vectors simultaneously, including CVE-2021-29441, an authentication bypass that's been documented since 2021 and still ships in default configurations because the default JWT signing key has been public knowledge for five years. The agent knew about it. The agent tried it.

After establishing a backdoor admin account, probing for container escape paths, and setting up a cron-based beacon to attacker infrastructure pinging every 30 minutes, the agent encrypted 1,342 Nacos service configuration records using MySQL's `AES_ENCRYPT()`, dropped the original tables, and created a new table named `README_RANSOM` containing a Bitcoin address, payment demand, and Proton Mail contact. Then it stopped.

Total payloads executed: over 600.

## The Bcrypt Moment

Here's the detail that should stop you cold.

When the agent tried to create a backdoor account, it needed to generate a bcrypt hash. The first attempt failed. The bcrypt library couldn't find its dependencies because of a subprocess PATH issue. The login check failed.

Thirty-one seconds later — without any human in the loop — a corrective payload appeared. The agent had diagnosed the root cause: subprocess calling bcrypt as a shell command rather than importing it directly. The new payload deleted the broken account, imported bcrypt as a Python module, printed the library version to confirm it was importable, regenerated the hash, reinserted the account, and verified the login succeeded.

[Sysdig called this out explicitly](https://securityaffairs.com/194713/ai/jadepuffer-first-end-to-end-ai-driven-ransomware-operation.html) as the clearest evidence of autonomous operation. But if I described this exact behavior to you without the ransomware context — "my coding agent hit an error, diagnosed it in 31 seconds, fixed the root cause, and verified the fix" — you'd call it a feature.

That's not a rhetorical trick. It's a real problem.

## The Self-Narrating LLM Tell

The other distinctive marker Sysdig found was that the agent's payloads included natural-language comments describing operational reasoning. Things like annotations explaining *why* a particular approach was being tried, what the target prioritization was, what the expected outcome was.

Human attackers don't write comments like that. They write minimal, obfuscated code designed to avoid forensic analysis. LLM-generated code writes comments reflexively because that's what the training data does — and because the agent is reasoning out loud, the same way it does when a developer asks it to explain its approach.

This is simultaneously the easiest detection signal in the history of ransomware analysis and a reminder that the detection signal is temporary. The next operator will include "do not add comments to payloads" in the system prompt, and the signal disappears. We have maybe one generation of this advantage before adversaries learn to clean it up.

## The Thing the Headlines Got Wrong

TechCrunch's qualifier was precise and underreported: *"The first AI-run ransomware attack still needed a human."*

JADEPUFFER had a human operator. That person chose the initial target, set up the C2 infrastructure (a server at `45.131.66.106`), and configured whatever LLM was running the agent. The agent was autonomous in the sense that it executed the attack chain without a human at the keyboard. It was not autonomous in the sense that it appeared ex nihilo with goals.

This distinction matters because it changes the threat model. The skill threshold for running a sophisticated ransomware campaign just dropped dramatically — you don't need someone who can pivot through networks and exploit Nacos authentication — but you still need someone with enough operational security knowledge to stand up infrastructure without burning themselves, pick a viable target, and configure an agent that won't immediately get caught. That's not nothing.

The Five Eyes agencies issued a [joint warning at the end of June](https://www.computing.co.uk/news/2026/security/ai-powered-cyberattacks-could-arrive-within-months-five-eyes-agencies-warn) that AI-powered cyberattacks could appear within months. It took about two weeks. The prediction wasn't wrong; the timeline was just optimistic.

## What Actually Changes Now

A few things are genuinely new here, and a few things are not.

**Not new:** Using known CVEs as entry points. CVE-2025-3248 in Langflow and CVE-2021-29441 in Nacos are both documented, both patchable, both already being scanned for by automated tools. The agent used them because they worked. Patch your stuff. This has been the advice for twenty years and it remains the advice now.

**Not new:** Database extortion as a ransomware vector. Encrypting your database and leaving a ransom note is well-established tradecraft. The method changed; the outcome did not.

**Genuinely new:** The attack's *velocity*. Over 600 coordinated payloads, parallel credential sweeping, 31-second error recovery, simultaneous multi-vector attacks on Nacos. A human operator running the equivalent attack would be slower at every stage. The time-to-encryption compresses dramatically when you don't need to think between steps.

**Genuinely new:** The accessibility cliff. Running a sophisticated multi-stage intrusion used to require someone who knew how to do all of it. Now it requires someone who can configure an agent that knows how to do all of it. Those are meaningfully different skill sets, and the second one is becoming a commodity.

**Genuinely new:** The detection surface. LLM-generated payloads have a characteristic style — verbose, self-annotating, iterative in ways that differ from human-written attack code. This is a real detection advantage today. Security tooling needs to learn to recognize it before adversaries learn to suppress it.

## The Langflow-Specific Problem

Sysdig's report is careful to note that Langflow servers are *particularly* attractive targets for this kind of attack, and the reason is worth spelling out.

You're building an AI-powered product. You stand up a Langflow instance to prototype your agent workflows. You're moving fast. You put it on the internet because that's easier than setting up VPN access. You put your OpenAI API key and your cloud credentials in environment variables because that's the standard way to configure these things. You don't add network controls because it's just a dev tool.

Now JADEPUFFER has your API keys, your cloud credentials, and a root shell. And from there, it pivots to wherever those credentials can reach — which in many cases is your production infrastructure.

This is not a Langflow-specific failure mode. It's the agentic AI infrastructure failure mode. Every tool in this category — Langflow, n8n, Flowise, anything that runs code on behalf of an agent — has the same profile: internet-exposed, credential-rich, rapidly deployed, minimally hardened. As agentic infrastructure proliferates, this attack surface is growing faster than the patch cycle.

## The Practical Upshot

If you run Langflow, patch it to a version that fixes CVE-2025-3248 and stop exposing the code execution endpoint to the internet. This one is specific and actionable.

More broadly: agentic AI infrastructure needs to be treated like production infrastructure from day one, not like a dev tool you'll harden later. The "later" tends to not arrive.

For defenders: the verbose, self-narrating payloads are a detection signal. Build detections for it now, knowing the signal is temporary. The iterative error-correction pattern — multiple payloads in quick succession that look like debugging — is also unusual enough to warrant alerting.

For everyone: the thing that makes AI agents useful (they plan, they adapt, they fix their own errors without asking for help) is exactly what makes agentic ransomware dangerous. There is no version of a capable AI agent where these properties don't exist. The same architecture that fixed the bcrypt PATH error and reinserted the account in 31 seconds is the one running in your IDE right now.

That's not an argument against building agents. It's an argument for being clearer-eyed about what you're building.

---

*Primary source: [Sysdig TRT — JADEPUFFER: Agentic ransomware for automated database extortion](https://www.sysdig.com/blog/jadepuffer-agentic-ransomware-for-automated-database-extortion)*  
*Coverage: [BleepingComputer](https://www.bleepingcomputer.com/news/security/jadepuffer-ransomware-used-ai-agent-to-automate-entire-attack/) · [CSO Online](https://www.csoonline.com/article/4193195/this-ai-agent-autonomously-hacked-a-network-adapted-on-the-fly-and-demanded-a-ransom.html) · [Security Affairs](https://securityaffairs.com/194713/ai/jadepuffer-first-end-to-end-ai-driven-ransomware-operation.html)*
