# When the Bug Report Goes Nowhere: Microsoft's Defender Zero-Day Disaster Is a Disclosure Story First

**Published: 2026-04-17**

Three working exploit tools for Windows Defender. All three actively used by real attackers in the wild. Two of them still unpatched as of today. And the entire situation was preventable — if Microsoft had simply responded to a researcher's disclosure attempt.

This is the BlueHammer/RedSun/UnDefend story, and if you're only reading it as a security advisory, you're missing what's actually interesting.

## What Actually Happened, in Order

Here's the timeline, which matters:

- **April 3**: A researcher going by "Chaotic Eclipse" (also "Nightmare-Eclipse") publishes a full working proof-of-concept exploit on GitHub for a Windows Defender privilege escalation flaw. They call it **BlueHammer**. Their message to MSRC leadership: *"I was not bluffing Microsoft, and I'm doing it again. Unlike previous times, I'm not explaining how this works; y'all geniuses can figure it out. Also, huge thanks to MSRC leadership for making this possible."*
- **April 7**: Security firms confirm the exploit works. It escalates an unprivileged local user to full SYSTEM on patched Windows 10 and 11.
- **April 10**: Huntress observes BlueHammer being used in real attacks. The attacker is running reconnaissance commands — `whoami /priv`, `cmdkey /list`, `net group` — classic signs of a human operator, not automated malware.
- **April 14**: Microsoft finally patches BlueHammer as **CVE-2026-33825** (CVSS 7.8) in Patch Tuesday. Notably, the CVE credits two *different* researchers — Zen Dodd and Yuanpei Xu — not Chaotic Eclipse.
- **April 16**: Chaotic Eclipse drops two more: **RedSun** (another Defender LPE) and **UnDefend** (disables Defender's signature update mechanism). Both are zero-days with no patches.
- **April 16, same day**: Huntress observes RedSun and UnDefend also being used in the wild.

As of today, April 17, 2026, RedSun and UnDefend have no fixes. The next Patch Tuesday is weeks away.

## The Vulnerabilities Are Clever, But the Story Is Bigger

Let's briefly explain the technical angle, because it's genuinely impressive engineering — even if you wish it were published differently.

**BlueHammer** (CVE-2026-33825) abuses a race condition in how Defender's threat remediation engine handles file cleanup. When Defender detects malware and quarantines it, it performs privileged file operations — it runs as SYSTEM. The flaw is a time-of-check to time-of-use (TOCTOU) problem: Defender validates the file path at one moment but writes to it later. An attacker can use an [opportunistic lock (oplock)](https://learn.microsoft.com/en-us/windows/win32/fileio/opportunistic-locks) to pause Defender mid-operation, swap in an NTFS junction point redirecting to `C:\Windows\System32`, and then let Defender — still running as SYSTEM — overwrite a legitimate binary with an attacker-supplied payload.

What's diabolical: this isn't a kernel bug or a memory corruption exploit. It chains together entirely legitimate Windows features — Volume Shadow Copy, Cloud Files callbacks, oplocks, NTFS junctions — all of which work exactly as designed. The vulnerability is an emergent property of how they interact. That makes signature-based detection nearly useless; detecting the compiled PoC binary tells you nothing about modified variants.

**RedSun** appears to be a second LPE via a different code path in the same Defender remediation subsystem — the details are still being analyzed by security researchers. **UnDefend** is a different beast: it exploits Defender's update mechanism to block definition updates or (if Microsoft pushes a major Defender update) disable the product entirely. A local user can effectively neuter the system's primary antimalware product without admin rights. Pair that with either of the LPEs and you have a complete local privilege escalation + defense evasion combo chain.

Will Dormann, a well-known vulnerability analyst, [confirmed RedSun works](https://infosec.exchange/deck/@wdormann/116412019416916182). By April 16, attackers were already using it.

## Why This Keeps Happening: The MSRC Problem

Microsoft's Security Response Center has a requirements document for vulnerability submissions. Among them: a video demonstration of the exploit in action. According to [Bleeping Computer's reporting](https://www.bleepingcomputer.com/news/security/disgruntled-researcher-leaks-bluehammer-windows-zero-day-exploit/), this requirement was reportedly a friction point in Chaotic Eclipse's disclosure attempt. The researcher apparently submitted a report; MSRC's response apparently left them furious.

There's a long-running tension between Microsoft and the security research community on this exact issue. Microsoft has been criticized for:
- Downgrading vulnerability severity ratings to avoid paying larger bounties
- Slow patch timelines, sometimes stretching 6-12 months from report to fix
- The video requirement, which adds substantial work for researchers without materially improving Microsoft's understanding of the bug
- Crediting different researchers in CVEs when multiple people report the same flaw

Chaotic Eclipse's statement — *"I'm just really wondering what was the math behind their decision, like you knew this was going to happen and you still did whatever you did"* — reads like someone who went through the proper channels, got burned, and decided to let the world know.

Microsoft's public response was the usual boilerplate: they support coordinated vulnerability disclosure, they investigate reported issues, etc. They didn't address what specifically went wrong in this interaction.

## The Uncomfortable Question

Was Chaotic Eclipse right to release these without coordinated disclosure?

The practical security community is split, and has been for years on this question. The purist argument: responsible disclosure exists to protect users. By releasing full working exploits before a patch exists, you guarantee real people get compromised — which is exactly what happened here. The end doesn't justify the means.

The pragmatist argument: coordinated disclosure only works if both parties coordinate. If Microsoft silently dismisses valid reports, or drags their feet for months, then the social contract of responsible disclosure is already broken before the researcher publishes anything. Researchers get nothing — no bounty, no credit, no acknowledgment — while Microsoft gets months of free security labor. The only leverage a researcher has is the threat of public disclosure. When they exercise it, they're described as "disgruntled" rather than the vendor being described as unresponsive.

The evidence that this story is systemic: Microsoft *did* eventually patch BlueHammer — crediting someone else. Two other researchers presumably submitted the same vulnerability through whatever process MSRC preferred, got credited, and the flaw got fixed. What happened between Chaotic Eclipse's April report and those other researchers' submission is unknown. But Chaotic Eclipse clearly believed they had priority and got nothing.

## What You Should Do Right Now

If you run Windows systems, the practical action items are clear:

1. **Apply the April 2026 Patch Tuesday updates immediately** — this patches BlueHammer (CVE-2026-33825). If you haven't, you're exposed to an LPE that's actively being exploited.

2. **Watch for behavioral indicators**, not just signature matches. Defender detects the original PoC binary as `Exploit:Win32/DfndrPEBluHmr.BB`, but that detection is trivially bypassed by recompiling with minor modifications. The real tell is post-compromise enumeration: sequences of `whoami /priv`, `cmdkey /list`, `net group` in close temporal succession should be treated as high-severity incidents.

3. **For RedSun and UnDefend: there is no patch yet.** Microsoft's options are an out-of-band emergency patch (likely, given active exploitation) or waiting for the next Patch Tuesday. Monitor [MSRC advisories](https://msrc.microsoft.com/update-guide/) for updates.

4. **Assume any machine that received a SYSTEM shell via these exploits is fully compromised.** BlueHammer reads the SAM database and can decrypt NTLM hashes. An attacker with those hashes can move laterally across your network via pass-the-hash, even after you patch.

## The Real Lesson

The BlueHammer story is a disclosure systems failure wearing a vulnerability story as a costume. Microsoft's security response program had one job when this researcher came to them: make the process smooth enough that researchers prefer to report privately. They apparently failed at that, and the result is three actively exploited zero-days, two of which still don't have patches, hitting real organizations right now.

The researcher's anger didn't materialize out of nowhere. "I was not bluffing Microsoft, and I'm doing it again" implies a history — earlier disclosures, earlier unsatisfying responses. The security research community is not a charity. People do this work for recognition, bounties, the thrill of discovery, and (yes) to make the world more secure. When the system that's supposed to channel that into patches instead ignores them, closes their reports, or credits someone else — the disclosure dam breaks eventually.

Microsoft spent years building an image as a security-forward company through initiatives like their Secure Future Initiative. A researcher dropping three consecutive zero-days on them in two weeks, all exploited within days of release, is a pretty direct grade on how that's going.

---

*Primary sources: [Help Net Security](https://www.helpnetsecurity.com/2026/04/17/microsoft-defender-zero-days-exploited/) · [SOCRadar technical breakdown](https://socradar.io/blog/bluehammer-redsun-undefend-windows-defender-0days/) · [Bleeping Computer original report](https://www.bleepingcomputer.com/news/security/disgruntled-researcher-leaks-bluehammer-windows-zero-day-exploit/) · [Cyderes technical analysis](https://www.cyderes.com/howler-cell/windows-zero-day-bluehammer) · [Dark Reading](https://www.darkreading.com/vulnerabilities-threats/bluehammer-windows-exploit-microsoft-bug-disclosure-issues) · [CVE-2026-33825 MSRC](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2026-33825)*
