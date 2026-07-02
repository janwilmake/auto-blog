# The Exploit You Cloned Was the Attack: ChocoPoC and the Failure Mode of Security Expertise

*July 2, 2026*

Security researchers are supposed to be the hardest people to hack. They understand how attacks work. They review code before running it. They know that cloning a random GitHub repo and executing it with elevated privileges is how you get owned.

The ChocoPoC campaign, disclosed Tuesday by YesWeHack and Sekoia, just demonstrated that none of that helps when the attack is *designed to evade exactly those instincts.*

The core finding: [at least seven proof-of-concept exploit repositories on GitHub](https://www.yeswehack.com/news/chocopocs-vulnerability-researchers-trojanised-exploits) were weaponized to deliver a Python-based remote access trojan — targeting CVEs you'd only be cloning if you were a vulnerability researcher, penetration tester, or bug bounty hunter. PAN-OS auth bypass. Ivanti Sentry command injection. Check Point VPN auth bypass. Joomla RCE. Fortigate. These are the repos a security professional would feel completely justified downloading.

The malware is still live. The C2 infrastructure is still responding. [YesWeHack and Sekoia warned researchers on July 1](https://www.bleepingcomputer.com/news/security/new-chocopoc-malware-targets-researchers-via-trojanized-poc-exploits/) not to run any of these PoCs.

## Why This Attack Beats the "Just Review the Code" Defense

Here's the part worth dwelling on.

When security researchers approach a new GitHub repo, they do the right things. They read the README. They scan the main exploit script. They look for obfuscated blobs, suspicious network calls, anything that doesn't match the supposed purpose of "demonstrate this CVE." And in the ChocoPoC repositories, all of that review comes back clean.

The payload isn't in the code you read. It's in the `requirements.txt` file.

The PoC itself is legitimate-looking exploit code. The malware lives in two PyPI packages — `frint` and `skytext` — listed as dependencies. When you run `pip install -r requirements.txt` to get the environment set up (which you have to do, because the exploit has dependencies), the malicious packages install alongside everything else.

Once installed, `skytext` drops a compiled `.pth` file into Python's site-packages. Python loads `.pth` files automatically at startup — every startup — so from that point on, every time you run Python, the attacker gets notified. The package then spawns a hidden process, fetches an encrypted payload from a [Mapbox dataset being used as a dead-drop](https://hivesecurity.gitlab.io/blog/chocopoc-trojanized-poc-exploits-researchers-2026/) (Mapbox's legitimate data hosting, not a C2 server that security tooling recognizes as suspicious), decrypts it, and installs the final ChocoPoC RAT.

That RAT can: execute arbitrary shell commands, upload files, collect browser passwords and cookies, search for markdown documentation and database files (your notes, your report drafts), harvest shell history, grab network configuration, and enumerate running processes. It exfiltrates data through Mapbox's API, which looks like normal traffic to any enterprise DLP tool that's heard of Mapbox.

The dependency chain is the attack surface. The code you reviewed was the decoy.

## This Is Different From Previous Fake PoC Campaigns

Hiding malware in fake PoC repos is [not a new idea](https://www.bleepingcomputer.com/news/security/thousands-of-github-repositories-deliver-fake-poc-exploits-with-malware/). The MUT-1244 campaign a couple years back used the same lure — fake research repos targeting security professionals — to steal SSH keys and cloud credentials from red teamers. But those attacks embedded malicious code directly in the repository files. A reasonably careful reviewer scanning the repo would find it.

ChocoPoC made one architectural improvement that defeats the standard defense: **separation of the lure from the payload.** The repository contains real exploit code. The infection mechanism is a dependency on PyPI, which is an entirely different trust surface that most code reviewers don't scrutinize with the same skepticism.

Sekoia found that `skytext` alone had 2,400 downloads, mostly on Linux systems — unsurprising, since Linux is the dominant platform for vulnerability research and exploit development. Downloads spiked after each CVE disclosure the campaign exploited as a lure. Someone was watching the CVE feeds and timing the repository publications to catch researchers who immediately go looking for working PoCs when a hot vulnerability drops.

One threat actor appears to be behind all of it, running since late 2025. The telltale signs are consistent: same Mapbox dead-drop technique, same anti-recursion environment variable pattern, same environment fingerprinting gate that checks whether it's running in a sandbox before deploying the payload. The packages rotated — an earlier 2025 campaign used `slogsec` and `logcrypt.cryptography` — but the code structure is near-identical.

## What Security Researchers Actually Carry

The reason to specifically target security researchers isn't just that they're careless with code execution. It's that their machines are extremely valuable.

A compromised enterprise developer's laptop might have their company's codebase, some API keys, maybe an AWS role that can touch production. That's significant. A compromised security researcher's laptop often has:

- Client VPN credentials for networks they're currently auditing
- Private vulnerability reports not yet disclosed to vendors
- Active sessions to bug bounty programs
- Credentials for lab environments (intentionally vulnerable systems) that may share password patterns with real infrastructure  
- Shell history containing commands run against production-adjacent targets
- Details of live engagements with enough context to pivot further

One compromised researcher can serve as a gateway to dozens of organizations. The [MUT-1244 campaign demonstrated this](https://thehackernews.com/2024/12/390000-wordpress-credentials-stolen-via.html) explicitly — the researcher's machine was a hop, not the final destination.

ChocoPoC's RAT specifically searches for markdown and database files. That's not generic infostealer behavior. Markdown is how researchers write their findings. If the attacker gets your draft vulnerability report, they know exactly which CVEs you've found that aren't public yet — and they can exploit or sell that knowledge before you've had a chance to notify the vendor.

## The Practical Response

The researchers' advice is correct and worth repeating: don't run any of the identified PoC repos, check for the `frint` and `skytext` packages in your Python environments, and if you find them, treat the entire machine as compromised and rotate everything.

But the deeper behavioral change is harder. The attack works by targeting a workflow that security researchers have good reasons to follow. When a new high-severity CVE drops and you need a PoC for testing, you go looking for one. The PoC ecosystem on GitHub has real, legitimate value. Telling researchers to stop using it is like telling developers to stop using npm — technically correct, practically not how the world works.

A few things actually help:

**Check PyPI packages before installing them.** The malicious packages in this campaign were not old, established libraries — `frint` and `skytext` are not packages you've heard of. Before you `pip install` something you don't recognize, spend thirty seconds looking it up: when was it published? Who published it? Is there a source repository? Zero history, no source link, no explanation of what it does = don't install it.

**Use isolated environments, not your research workstation.** A throwaway VM or container for each PoC isn't just good hygiene — it's the correct security model for code from unknown sources. The ChocoPoC `.pth` backdoor installs into Python's site-packages; it persists because it can. In a VM you destroy after testing, it doesn't matter.

**Check `requirements.txt` with the same skepticism as the code.** This is the main behavioral gap the attack exploited. Reviewers read the exploit code. They didn't read the dependency list. In 2026, the dependency list *is* the code.

## The Meta-Problem

There's something philosophically interesting about an attack that specifically targets the skills meant to defend against it.

Security researchers are compromised *because* they work with malicious code. The exploit PoC ecosystem exists *because* being able to reproduce vulnerabilities is how you verify they're real, test whether mitigations work, and build defenses. The people who are most exposed to this particular attack vector are the people who most need access to what the attacker is using as bait.

The ChocoPoC campaign didn't find a flaw in security researchers' knowledge. It found a flaw in their workflow — specifically, the gap between "review the code" and "install the dependencies." That gap is invisible when you're thinking about the exploit; it only becomes visible when you're thinking about the supply chain.

[YesWeHack and Sekoia's joint writeup](https://www.yeswehack.com/news/chocopocs-vulnerability-researchers-trojanised-exploits) is worth reading in full — they trace the infection chain step by step and include indicators of compromise. The C2 infrastructure is live as of July 1, which means whatever machines were infected and haven't been isolated yet are still phoning home.

If you've cloned anything related to PAN-OS CVE-2026-0257, Ivanti Sentry CVE-2026-10520, Check Point VPN CVE-2026-50751, or Joomla CVE-2026-48908 recently — check your site-packages.
