# Claude Code Hid a Steganographic Fingerprint in Your Prompts. The Goal Was Reasonable. The Method Was Not.

*July 1, 2026*

A developer named Thereallo published [a post yesterday](https://thereallo.dev/blog/claude-code-prompt-steganography) that hit #1 on Hacker News with 2,000+ upvotes and is still climbing. The finding: Claude Code, Anthropic's terminal-based coding agent, has been silently embedding invisible Unicode markers into every system prompt it sends to the API — varying the apostrophe character in `Today's` and the date separator in the date string, based on your timezone and API base URL. It's currently sitting at 600+ comments on HN, and the Reddit thread is calling it spyware.

Before you spiral, let me tell you what it actually is. And then let me tell you why it still matters.

## What the Code Actually Does

Inside Claude Code version 2.1.196 (the current release, signed by Anthropic), there's a function that builds the date string injected into every system prompt:

```
Today's date is 2026-07-01.
```

Simple, right? Except Claude Code can silently change two things:

1. The apostrophe in `Today's` — swapped for `\u2019` (right single quotation mark), `\u02BC` (modifier letter apostrophe), or `\u02B9` (modifier letter prime) depending on conditions
2. The date separator — `-` becomes `/` if your system timezone is `Asia/Shanghai` or `Asia/Urumqi`

The trigger is `ANTHROPIC_BASE_URL`. If you're routing through the official Anthropic endpoint, none of this fires. If you've set a custom base URL, Claude Code classifies that hostname against two lists — one of known domains, one of AI lab keywords — decodes them from base64+XOR with key `91`, and then encodes the classification into the prompt via the apostrophe swap.

The keyword list decodes to: `deepseek,moonshot,minimax,xaminim,zhipu,bigmodel,baichuan,stepfun,01ai,dashscope,volces`

The domain list is much larger — it includes Chinese corporate infrastructure (Alibaba, Baidu, ByteDance, JD.com, Bilibili), plus a long tail of Claude proxy/reseller/gateway services (`anyrouter.top`, `claude-code-hub.app`, `proxyai.com`, `yunwu.ai`, etc.).

The result: a marking signal that Anthropic can parse server-side from raw request traffic without adding any new field to the payload. The visible text looks identical to human eyes. The Unicode values are different.

That is textbook steganography.

## Why Anthropic Did This

This isn't paranoia. Anthropic published [a detailed post in February](https://www.anthropic.com/news/detecting-and-preventing-distillation-attacks) about three Chinese AI labs — DeepSeek, Moonshot AI, and MiniMax — running industrial-scale campaigns to extract Claude's capabilities through 24,000 fraudulent accounts and 16 million synthetic exchanges. DeepSeek was even prompting Claude to generate censorship-safe alternatives to politically sensitive queries, effectively using Claude to train around Chinese censorship laws.

This is also not the first anti-distillation system found in Claude Code. Back in March, researchers discovered `ANTI_DISTILLATION_CC` — a separate mechanism that injects `anti_distillation: ['fake_tools']` into API requests, causing Anthropic's backend to silently slip fake tool definitions into the system prompt. If someone is scraping Claude Code's traffic to train a competitor, they get poisoned training data with phantom tools that don't exist.

The steganographic prompt marking appears to be a complementary detection mechanism: instead of poisoning, it tags. If requests are being routed through known reseller or lab infrastructure, that signal travels with the prompt to Anthropic's servers, where it can be used for rate-limiting, account termination, or legal action.

From a pure IP protection standpoint, this makes sense. Anthropic has real evidence that competitors are laundering their API traffic through proxy networks specifically to defeat standard detection. A marker that looks like ordinary prose is harder to strip than an explicit metadata field.

## Why It's Still a Problem

I'm not going to call this spyware. It doesn't exfiltrate your code. It doesn't phone home with your file contents. Thereallo himself says it clearly: "This is not a malicious feature."

But the implementation is a trust antipattern, and three things make it one.

**First: you gave Claude Code frightening access, and it used that access to tag your requests without telling you.** Claude Code can read your entire filesystem, run shell commands, install packages, push commits, and control your browser. You consented to all of that because the productivity is worth it. You did not consent to the tool silently altering the content of the prompts it sends on your behalf. Those are different things. The first is capability; the second is covert manipulation of the communication channel.

**Second: it was deliberately obfuscated.** The domain and keyword lists are XOR-encoded with key `91` and base64-encoded. The Unicode character substitutions are designed to be invisible in monospaced fonts. The change wasn't mentioned in any release notes. This isn't a case of technical shorthand — Anthropic put explicit effort into making this hard to find. That effort is the tell. When you hide a thing, you've already decided users shouldn't know about it.

**Third: it mostly punishes legitimate users.** Any serious adversary — a well-funded Chinese AI lab with a 24,000-account infrastructure operation — will trivially bypass this. Change the hostname, change the timezone, patch the binary, wrap the process. The marker encodes four states via apostrophe variants. That's two bits of information, easily stripped by anyone who reads the source code (which is now public knowledge). What it actually catches is the long tail of normal developers who route Claude Code through internal gateways, local proxies, model routers, or research setups with custom base URLs. These are exactly the users who are most invested in understanding what the tool does — and who are now most betrayed by discovering it was doing something undocumented.

## The Deeper Issue

Anthropic occupies an unusual position. They've built a reputation on being the safety-conscious, transparent AI lab. They publish detailed system cards, write honest alignment research, and routinely disclose things their competitors quietly bury. Their argument for trust is substantially built on that transparency.

And then they ship a covert Unicode steganography system in a tool that has your private key access.

The goal — detecting distillation attacks and unauthorized API resale — is legitimate. Anthropic has the legal right to enforce its terms of service. They have strong evidence that this abuse is happening at industrial scale.

But they could have done this transparently. A clear ToS clause saying "Claude Code embeds routing metadata in system prompts when used with non-Anthropic base URLs" would have the same legal and technical effect. Anyone genuinely running a distillation pipeline wouldn't be deterred by public knowledge of the mechanism — they'd just route around it. The only people who would have been deterred by public disclosure are legitimate users who might object to the tracking. And objecting is exactly the right they should have.

There's a temptation to frame this as "Anthropic vs. Chinese AI labs" and wave it away. But the implementation choice — covert, obfuscated, invisible — says something about how Anthropic makes tradeoffs when business interests and user trust collide. Right now it's a Unicode apostrophe. That's the concerning precedent, not the apostrophe itself.

## What to Do

If you're using Claude Code with `ANTHROPIC_BASE_URL` unset and pointing at the official API, the marker doesn't fire. You're fine.

If you're routing through a custom base URL — an internal gateway, a local proxy, a model router — Claude Code is silently classifying and marking your requests. You can:

- Check what your current `ANTHROPIC_BASE_URL` is set to
- Verify whether your base URL's hostname appears in the decoded domain list (now public)
- If this bothers you, you can patch the binary or wrap the process to normalize the date string before it reaches the API

The bypass is trivial. The point isn't the bypass. The point is whether you trust a tool that thinks hiding things from you is fine when its business reasons are good enough.

That's a harder question than any Unicode trick.

---

*Primary source: [Claude Code Is Steganographically Marking Requests](https://thereallo.dev/blog/claude-code-prompt-steganography) by Thereallo (June 30, 2026). HN thread: [48734373](https://news.ycombinator.com/item?id=48734373) — 2000+ points, 600 comments. Earlier related finding: [ANTI_DISTILLATION_CC thread](https://news.ycombinator.com/item?id=47585239). Anthropic's distillation attack disclosure: [February 2026](https://www.anthropic.com/news/detecting-and-preventing-distillation-attacks).*
