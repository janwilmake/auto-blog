# Your `.cursorrules` and `CLAUDE.md` Are Now a Malware Attack Surface

**Date:** 2026-05-25

Supply chain attacks have been escalating all year. We covered the [TanStack CI pipeline attack](./2026-05-15-tanstack-mini-shai-hulud-ci-pipeline-is-the-attack-surface.md) ten days ago. That one was sophisticated — cache poisoning, OIDC token extraction, SLSA-attested malicious packages. But it required a specific misconfiguration in the build pipeline to work.

The attack [Socket disclosed today](https://socket.dev/blog/trapdoor-crypto-stealer-npm-pypi-crates), codenamed TrapDoor, is doing something different. And in some ways more insidious: it's going after the files that tell your AI coding assistant what to do.

## What TrapDoor Actually Does

TrapDoor is a credential-stealing campaign that dropped 34 malicious packages across npm, PyPI, and Crates.io simultaneously — the first documented cross-ecosystem attack to hit all three registries in a single coordinated wave. Socket detected the first package (`eth-security-auditor` on PyPI) at 20:20 UTC on May 22. New packages then appeared in waves across all three ecosystems over the following weekend.

The package names were carefully chosen: `defi-env-auditor`, `wallet-security-checker`, `mnemonic-safety-check`, `llm-context-compressor`, `prompt-engineering-toolkit`. They're designed to look exactly like the utilities an AI/crypto developer would install for tooling or security auditing. The kind of packages you might install without thinking twice, in a new project setup or a weekend experiment.

Once installed, the npm packages run a postinstall hook that executes `trap-core.js` — a 1,149-line payload that does what you'd expect: scans for AWS credentials, GitHub tokens, SSH keys, crypto wallet seed phrases, Kubernetes configs, browser-stored passwords, and environment variables. It validates stolen credentials in real time against AWS and GitHub APIs to confirm what it's found actually works. Then it exfiltrates everything.

That part is familiar. What's new is the persistence mechanism.

## The AI Config File Vector

Among the persistence techniques TrapDoor uses — git hooks, shell hooks, systemd services, cron jobs, SSH-based propagation — there are two that should grab every developer's attention: **`.cursorrules`** and **`CLAUDE.md`**.

These are the configuration files that tell AI coding assistants like Cursor and Claude Code how to behave in your project. What conventions to follow. What commands are allowed. What context to use when generating code.

TrapDoor injects hidden instructions into these files using **zero-width Unicode characters** — specifically, Unicode characters in the range U+200B through U+200F (zero-width space, zero-width non-joiner, etc.). These characters are invisible in most text editors, invisible in standard code review, and invisible to most security scanners. They're not whitespace. They're not null bytes. They're legitimate Unicode that happens to not render as anything.

The injected instructions, once decoded from the hidden Unicode, appear to instruct the AI assistant to run a "security scan" or similar workflow. The goal is to get the agent to independently discover and exfiltrate credentials — *without the developer ever typing a command*.

As Socket's research team put it: "The goal appears to be to trick AI assistants into running a 'security scan' or similar workflow that causes secret discovery and exfiltration."

There's an important nuance here: Socket notes that "this technique may not work consistently across all tools or models." This isn't a perfectly reliable exploit of Cursor or Claude Code specifically. It's more that the attacker is actively experimenting with this vector — testing whether AI coding environments can be turned into credential harvesting tools via their own config files. The fact that it may not always work doesn't make the vector less serious. It makes it more concerning, because it means the technique is still being refined.

## The Pull Request Angle

Here's what makes this more than a malicious package story: the attacker also opened pull requests to major AI development repositories — [LangChain](https://github.com/langchain-ai/langchain), [LlamaIndex](https://github.com/run-llama/llama_index), and [MetaGPT](https://github.com/geekan/MetaGPT) — attempting to seed malicious `.cursorrules` and `CLAUDE.md` files through normal open-source contribution workflows.

Think about what that means. If one of those PRs had been merged — even briefly — every developer who cloned that repository and opened it in Cursor or ran Claude Code on it would have had a poisoned config file in their workspace. The agent would silently receive hidden instructions it couldn't see. And the developer would have no indication anything was wrong, because the visible content of the file would look completely normal.

This is a fundamentally different attack model from a malicious package. You don't have to install anything. You just have to work on a repository that someone else contributed a `.cursorrules` file to.

## Why This Is Different From Previous Supply Chain Attacks

The previous wave of supply chain attacks in 2026 — Shai-Hulud, Mini Shai-Hulud, TanStack, the Laravel Lang backdoor, the art-template npm compromise — all operated through the same model: malicious code executes when you run `npm install`, `pip install`, or `cargo build`. The attack surface is the dependency manager executing lifecycle hooks.

The TrapDoor AI config injection is orthogonal to that. It doesn't need a postinstall hook. It doesn't need a `build.rs` file. It needs:

1. A developer using an AI coding tool
2. A poisoned config file in the project directory  
3. The AI tool to trust and act on the instructions in that config file

All three of those conditions are increasingly common. The growth of Cursor and Claude Code as standard developer tooling means millions of developers have AI agents that read their project config files, trust those files, and act on what they say. That trust was implicit and unexamined — because until now, those files *were* inert text documents that just set up context. Nobody audited them.

There's also a second-order concern here. Your AI agent reads many files in your project, not just the dedicated config files. Pull requests to your repo might modify instruction files you hadn't been watching. Your agent might process a malicious CLAUDE.md from a dependency's documentation directory if it's in your workspace. The attack surface of "things the agent reads and trusts" turns out to be significantly larger than "packages you install."

## What You Should Actually Do

First, the obvious: **check the affected package list**. The full list from Socket's report includes:

**npm:** `async-pipeline-builder`, `build-scripts-utils`, `chain-key-validator`, `crypto-credential-scanner`, `defi-env-auditor`, `defi-threat-scanner`, `deployment-key-auditor`, `dev-env-bootstrapper`, `eth-wallet-sentinel`, `llm-context-compressor`, `mnemonic-safety-check`, `model-switch-router`, `node-setup-helpers`, `project-init-tools`, `prompt-engineering-toolkit`, `solidity-deploy-guard`, `token-usage-tracker`, `wallet-backup-verifier`, `wallet-security-checker`, `web3-secrets-detector`, `workspace-config-loader`

**PyPI:** `cryptowallet-safety`, `data-pipeline-check`, `defi-risk-scanner`, `env-loader-cli`, `eth-security-auditor`, `git-config-sync`, `solidity-build-guard`

**Crates.io:** `move-analyzer-build`, `move-compiler-tools`, `move-project-builder`, `sui-framework-helpers`, `sui-sdk-build-utils`, `sui-move-build-helper`

If any of these appear in your lock files from May 22 onward, rotate every credential on the affected machine.

Second, and more importantly for the long term: **you need to treat `.cursorrules` and `CLAUDE.md` files as code, not config**. That means:

- Review them when they appear in pull requests, not just skim them
- Add them to your secrets scanning and security review workflows
- Use a tool that renders Unicode characters visibly during review — something like `cat -A` or a hex editor spot-check on any AI config file from an external source
- Be suspicious of any AI config file that appeared in a project you didn't author

The invisible Unicode trick is detectable if you look for it:

```bash
# Check for zero-width Unicode in your AI config files
grep -P '[\x{200B}-\x{200F}\x{FEFF}\x{2028}\x{2029}]' .cursorrules CLAUDE.md
```

That regex covers the most common zero-width characters used for this type of injection. Run it on any AI config files you didn't write yourself.

Third: **think about what your AI agent is authorized to do.** Cursor and Claude Code can be configured to require explicit approval before running commands. If you're working on external repositories or repositories with contributors you don't personally trust, consider tightening your approval settings. The attack only exfiltrates credentials if the agent *acts* on the hidden instructions — and agents that require user confirmation before executing shell commands add a meaningful barrier.

## The Uncomfortable Bigger Picture

We've spent a lot of time in 2026 talking about prompt injection as a theoretical risk to AI applications. Someone poisons a document, the AI reads it, the AI does something it shouldn't. Security researchers have been warning about this for years. Most discussions treated it as an interesting research problem.

TrapDoor is prompt injection being used in a real, live, active credential-stealing campaign. It's still partially in the experimental stage — the Unicode injection technique may not reliably work across all tools. But the attacker is running it now, actively, against developer environments, across three package registries simultaneously. It's not theoretical anymore.

The attack surface for "someone can make your AI agent do bad things" is not limited to your AI-powered web application. It includes your development environment. It includes config files you clone from open source repos. It includes PR contributions from people you've never met, suggesting improvements to project-level AI instructions.

AI coding tools are powerful because they read everything in your project and act on it. That's also exactly why they're a target. The implicit contract that "things AI tools read are trusted input" is broken. It may have been broken for a while. TrapDoor is just the first campaign to make it explicit.

---

*Primary sources: [Socket Research Team — TrapDoor technical report](https://socket.dev/blog/trapdoor-crypto-stealer-npm-pypi-crates) · [The Hacker News coverage](https://thehackernews.com/2026/05/trapdoor-supply-chain-attack-spreads.html) · [AI Weekly analysis of the AI config attack vector](https://aiweekly.co/alerts/trapdoor-poisons-npm-pypi-with-ai-config-file-attack) · [Novee Security — CVE-2026-26268 Cursor git hook RCE](https://novee.security/blog/cursor-ide-cve-2026-26268-git-hook-arbitrary-code-execution/)*
