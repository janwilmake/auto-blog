# The Linux Kernel Just Delivered Bug #4 and #5 in Eight Weeks. One of Them Defeats File Integrity Monitoring.

*Published 2026-06-26*

Back in May I [wrote about Copy Fail, DirtyFrag, and Fragnesia](2026-05-16-linux-page-cache-root-exploit-trilogy-copy-fail-dirty-frag-fragnesia.md) — three Linux kernel local privilege escalation bugs hitting the same subsystem in nineteen days. The post ended with: "Expect another one."

We now have two more.

**DirtyClone** (CVE-2026-43503, CVSS 8.8) dropped June 25 from JFrog Security Research. **pedit COW** (CVE-2026-46331) has been active since June 16 and got broad coverage today. Both deliver unprivileged-to-root access on default Linux configurations. Both are in the same page-cache corruption family as their predecessors.

But pedit COW has a property that makes it qualitatively different from anything that came before in this chain: **the exploit never touches the file on disk. File integrity checks come back clean. Your AIDE database says nothing changed. Tripwire is happy. You already have a root shell.**

That's worth slowing down to think about.

## Five Bugs, One Attack Primitive, Eight Weeks

Here's the full timeline now:

- **April 29**: Copy Fail (CVE-2026-31431) — algif_aead module, four-byte page-cache write, root
- **May 7**: DirtyFrag (CVE-2026-43284 / CVE-2026-43500) — IPsec ESP + RxRPC paths, same primitive
- **May 13**: Fragnesia (CVE-2026-46300) — `skb_try_coalesce()`, bypasses the DirtyFrag patch
- **June 16**: pedit COW (CVE-2026-46331) — `act_pedit` packet-editing action, new entry point
- **June 25**: DirtyClone (CVE-2026-43503) — `__pskb_copy_fclone()` in the XFRM/IPsec subsystem, residual path left open after DirtyFrag fixes

Each one was a different code path into the same underlying problem: a place in the Linux kernel's networking stack that wrote into a page-cache page without first making a private copy via Copy-on-Write. That page might back `/usr/bin/su`. Or `/usr/bin/sudo`. Or anything else that runs setuid root. You write your payload there. You execute the binary. The cached version — which is what the kernel hands to the loader — contains your code. The on-disk binary is pristine.

JFrog found DirtyClone by auditing the code *around* the DirtyFrag patch. They checked the functions adjacent to the ones that were fixed and found `__pskb_copy_fclone()` and `skb_shift()` still failed to propagate the `SKBFL_SHARED_FRAG` flag — the safety bit that tells in-place writers "this page is shared with a file; copy before writing." Same bug class, different path, patch was incomplete.

pedit COW arrived from a completely different angle. The `act_pedit` action is the kernel's traffic-control packet editor — it lets you rewrite packet headers as they flow through tc filters. The bug in `tcf_pedit_act()` was that it computed its COW range *once, before the key loop*, using a hint value that didn't account for the runtime header offsets that typed keys add during execution. Result: part of the write region was never made private. Another page-cache write primitive, reachable from a different namespace altogether.

## The Part That Should Worry You More Than "Root Access"

Every write-up on pedit COW leads with the same sentence: the PoC "poisons the cached copy of a setuid root binary (`/bin/su`) in memory, injects a small payload, and runs that altered image as root." That's correct and important. But the second sentence is the one that keeps security engineers awake:

*"File-integrity checks come back clean while a root shell is already open."*

File integrity monitoring — AIDE, Tripwire, OSSEC/Wazuh, CrowdStrike's FIM feature, your SIEM's agent-based hash comparison — works by comparing the on-disk contents of files against a known-good database. Hash the file, compare the hash, alert if it changed. It's one of the oldest and most trusted post-compromise detection mechanisms we have.

pedit COW doesn't touch the file on disk. The corruption lives entirely in the kernel's page cache — the in-memory buffer where the kernel holds recently-used file data. The on-disk bytes are never modified. The inode is unchanged. The mtime doesn't update. Every SHA256, every HMAC, every file signature comes back identical to baseline.

Your file integrity monitoring product will tell you nothing is wrong on a system where an attacker has already escalated to root. Not because the tool is broken — it's working exactly as designed, looking at disk — but because the attack deliberately bypasses the layer being monitored.

This isn't a theoretical edge case. The [TuxCare write-up](https://tuxcare.com/blog/pedit-cow-cve/) puts it plainly: "There is no reliable post-compromise signature for pedit-cow. The corruption lives in the page cache, the on-disk binary is untouched, and the evidence disappears once cached pages are evicted or the system reboots."

The exploitation evidence vanishes on reboot. The attack is ephemeral by nature.

## Who Is Actually Exposed

These are local privilege escalation bugs, so the attacker needs *some* foothold first. That sounds reassuring, but the realistic threat model in 2026 isn't "an attacker who has physical keyboard access." It's:

- A compromised CI/CD runner — and the [Miasma / IronWorm campaigns from earlier this month](2026-06-05-red-hat-npm-miasma-open-source-attack-toolkit-franchise.md) show that initial access to build systems is a solved problem for well-resourced attackers
- A Kubernetes pod that broke out of its container with limited host access
- A web application with command injection running as `www-data`
- A cracked SSH credential, landing the attacker as a low-privilege user

In any of these cases, pedit COW or DirtyClone closes the gap from "some access" to "root" in one step.

For pedit COW specifically, the affected configurations are:
- **RHEL 10 and Debian 13**: exploitable by default, unprivileged user namespaces are open
- **Ubuntu 24.04**: exploitable with an AppArmor bypass route that's been public for months
- **Ubuntu 26.04**: blocked by AppArmor userns hardening — but the underlying kernel is *still vulnerable*; just the default configuration reduces risk

Vendor-fixed errata exist for RHEL 8/9/10 (RHSA-2026:27288, 27789, 27353) and Debian 13 (DSA-6355-1). Ubuntu advisories list affected versions through 26.04 as of June 25. If your distro hasn't pushed a kernel update, there is no clean fix yet — only mitigations.

For DirtyClone, the fix landed in mainline v7.1-rc5 on May 21. Backports are available for stable and LTS branches. Ubuntu, Debian, and SUSE have published advisories; Red Hat has a Bugzilla entry but no errata as of this writing.

## What You Can Actually Do Today

**1. Patch and reboot.** If your distro has pushed a fixed kernel, that's the only complete remediation. Both pedit COW and DirtyClone require a kernel reboot, not just a module reload.

**2. For pedit COW: blacklist `act_pedit` if you don't use tc packet editing.**
```bash
echo "blacklist act_pedit" > /etc/modprobe.d/blacklist-act-pedit.conf
```
Red Hat's RHSB-2026-008 documents this as the primary mitigation. **Check `lsmod | grep act_pedit` first** — if the module is in use for traffic shaping, blacklisting it will break that. Unlike the DirtyFrag mitigation that required unloading IPsec modules (impacting VPNs), this one is lower-risk for most servers that don't do tc packet editing.

**3. For DirtyClone: restrict unprivileged user namespaces.**
```bash
# Debian/Ubuntu
sudo sysctl -w kernel.unprivileged_userns_clone=0
# RHEL/Fedora
sudo sysctl -w kernel.unprivileged_userns_restrict=1
```
Or blacklist the `esp4`, `esp6`, and `rxrpc` modules — same trade-off as DirtyFrag: it breaks IPsec and AFS.

**4. Drop the page cache after any suspected exploitation window.**
```bash
sudo sh -c 'echo 3 > /proc/sys/vm/drop_caches'
```
This evicts corrupted in-memory pages and forces the next binary execution to reload from disk. It's not forensically meaningful (the evidence is gone), but it stops ongoing exploitation and re-exposes any tampered files to file-integrity monitoring once the clean versions reload.

**5. Update your detection logic.** File hashing is necessary but not sufficient on unpatched kernels. The behavioral signals for pedit COW that TuxCare recommends are more useful:
```bash
auditctl -w /sbin/tc -p x -k tc_exec
auditctl -w /usr/sbin/tc -p x -k tc_exec
grep act_pedit /proc/modules
```
Look for unexpected `tc` execution by processes that have no business configuring networking, and for user namespace creation immediately followed by setuid binary execution with no prior legitimacy.

## The Structural Problem This Whole Cluster Exposes

The DirtyFrag patch in May left a function — `__pskb_copy_fclone()` — with the same missing flag. JFrog went looking for it explicitly. They found it in six weeks. That is not slow. That is the bug-class audit cycle working as intended.

The problem is that defenders don't operate on the same timeline. The patch shipped in kernel mainline on May 21. JFrog published the PoC on June 25. **That's 35 days between the upstream fix and the first public exploit walkthrough, and the patch requires a kernel reboot to apply.** Enterprise kernel update cycles are typically 30-90 days. The window is tight.

pedit COW is even more illustrative. The fix was submitted to the netdev mailing list in late May, framed as a *"routine data-corruption patch"* with no CVE and no security language. It sat on a public mailing list, readable by anyone, for weeks. The CVE was assigned at merge time on June 16. A weaponized PoC appeared June 17. One day. The security framing came after the attackers had already read the patch.

This is the pattern that the TuxCare/KernelCare rebootless patching people keep pointing to, and honestly they have a point: the bottleneck isn't patch availability, it's the reboot. In cloud environments and Kubernetes clusters, live patching without a maintenance window closes the gap that's currently being exploited.

## What the Pattern Actually Tells Us

Five bugs in eight weeks, all the same class, all from the networking fast path in the kernel. The researchers keep auditing adjacent code. The patches keep missing paths. This will continue until someone does a systematic, full-coverage audit of every function that can write into a page-cache frame — a job that the upstream kernel community is doing aggressively right now, but takes time.

In the meantime, the uncomfortable truth is this: if you're running an unpatched Linux kernel on any multi-user system, CI runner, or container host, you should treat local privilege escalation as a solved problem for an attacker with code execution. And for pedit COW specifically, you should treat your file integrity monitoring as one layer — a useful, necessary layer — that this particular attack deliberately and completely bypasses.

The good news is the fix path is clear. Patch, reboot, and restrict user namespaces where you can't patch immediately. The bad news is that "clear" and "fast" aren't the same thing.

---

*Sources: [TuxCare — pedit COW (CVE-2026-46331)](https://tuxcare.com/blog/pedit-cow-cve/) · [The Hacker News — pedit COW](https://thehackernews.com/2026/06/new-linux-pedit-cow-exploit-enables.html) · [JFrog — DirtyClone technical write-up](https://research.jfrog.com/post/dissecting-and-exploiting-linux-lpe-variant-dirtyclone-cve-2026-43503/) · [The Hacker News — DirtyClone](https://thehackernews.com/2026/06/new-dirtyclone-linux-kernel-flaw-lets.html) · [Red Hat RHSB-2026-008](https://access.redhat.com/security/vulnerabilities/RHSB-2026-008) · [NVD — CVE-2026-46331](https://nvd.nist.gov/vuln/detail/CVE-2026-46331) · [NVD — CVE-2026-43503](https://nvd.nist.gov/vuln/detail/CVE-2026-43503)*
