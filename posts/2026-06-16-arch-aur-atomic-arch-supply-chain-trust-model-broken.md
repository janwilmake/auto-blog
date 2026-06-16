# "Atomic Arch" Hit 1,500 AUR Packages. The Number Isn't the Story — the Trust Model Is.

**Published: 2026-06-16**

Last Thursday, Arch Linux users woke up to the biggest supply-chain attack the AUR has ever seen. By the end of the day, developers had confirmed more than 1,579 packages compromised. The next morning, a second, more sophisticated wave hit — this one with obfuscated code specifically designed to evade the detection scripts the community had built overnight.

As of today, the incident is still unfolding. The Arch Linux team has locked down new account creation, package adoption, and updates while they work on "a more permanent solution." The AUR's own news page says nothing beyond "we're working on it."

Here's what actually happened, what the malware does, and what this says about the broader state of open-source package security.

## How the Attack Worked

The "Atomic Arch" campaign — named by Sonatype researchers who discovered it — did not exploit a bug in the AUR's software. There was no zero-day, no server breach, no clever PKGBUILD parser vulnerability. The attack was simpler and more insidious than that.

The attackers targeted **orphaned packages**: software whose original maintainers had moved on, leaving the package officially unmaintained. The AUR allows anyone to adopt these packages, and that's where the attack began. Researchers later confirmed the attackers also used **commit forgery** to impersonate at least one legitimate maintainer — a KDE developer with a long and trusted history — to expand their reach beyond only orphaned packages.

Once they controlled a package, the attackers made a small, surgical change: they added a line to the PKGBUILD or install hooks to run `npm install atomic-lockfile` (or variants like `lockfile-js`). The npm packages themselves contained a `preinstall` hook that executed a bundled binary called `deps`.

The trap sat in the recipe. The software itself was exactly what users meant to install. Only the build instructions had changed.

## What the Payload Actually Does

Independent researcher [Whanos reverse-engineered the `deps` binary](https://ioctl.fail/preliminary-analysis-of-aur-malware/) and found a Rust credential stealer that is, to be blunt, purpose-built to compromise developer workstations and CI systems.

It collects:

- **Cookies and session tokens** from every Chromium-based browser (Chrome, Edge, Brave, and more)
- **Slack, Discord, and Microsoft Teams session data** from their Electron app storage
- **GitHub, npm, and HashiCorp Vault tokens** — the keys to your repositories, packages, and secrets
- **OpenAI/ChatGPT bearer tokens and account metadata** (this is new, and it's notable)
- **SSH keys and `known_hosts`** — your entire authenticated network graph
- **Docker and Podman credentials and VPN profiles**
- **Shell histories** — which often contain credentials passed as environment variables

Everything stolen goes out over HTTP to `temp.sh`. Command and control runs through a Tor onion service. Early reporting oversold the eBPF rootkit component — it's optional and only activates when the binary already has root — but when it does run, it uses pinned BPF maps (`hidden_pids`, `hidden_names`, `hidden_inodes`) to hide the malware's own processes and sockets from `ps`, `ss`, and any tool that reads `/proc`. It also kills attempts to attach a debugger.

There's also a staged second file, flagged as a possible cryptominer, tied to `monero-wallet-gui` that hasn't been fully analyzed yet.

For persistence, it installs a `systemd` service with `Restart=always`. With root, it copies itself to `/var/lib/` and writes a unit under `/etc/systemd/system/`. As a normal user, it uses `~/.config/systemd/user/`. Either way, it wants to survive reboots.

## Why "Remove the Package" Is Not Enough

This is the part most coverage is underselling.

Once the `deps` binary has executed — which happens during package installation, before you see any output, before you can inspect anything — the host is compromised. The AUR package is just the delivery mechanism. The payload is already running.

Removing the package removes the files the package manager knows about. It cannot prove the machine is clean after a rootkit-capable payload has had a chance to execute. The eBPF rootkit, if activated, is actively hiding itself from the tools you'd use to look for it.

The right response if you installed or updated an AUR package between June 11 and now:

1. **Don't scan the machine and call it done.** A credential scanner on a rootkit-affected host gives you false confidence.
2. **Rotate all credentials immediately.** SSH keys, GitHub tokens, npm tokens, cloud API keys, Vault tokens — everything. Invalidate browser sessions. Assume every token the machine could have touched is burned.
3. **Treat self-hosted CI runners on Arch as fully compromised.** If you run Arch on your CI pipeline, every secret ever injected into that pipeline should be considered exposed.
4. **Rebuild from clean media.** Yes, it's painful. Yes, it's the right call.

Sonatype is tracking the original campaign as **Sonatype-2026-003775** (CVSS 8.7). The SHA-256 of the main payload is `6144d433f8a0316869877b5f834c801251bbb936e5f1577c5680878c7443c98b`. The community has a [detection script repo](https://github.com/lenucksi/aur-malware-check) if you need to check whether a package you use was in the affected list.

## The Second Wave Makes This Worse

Just as the community was breathing relief — developers had identified and deleted 1,579 malicious commits, and Arch Linux announced they believed the incident was under control — a second wave hit on June 14.

This round was different. The first attack used straightforward `npm install` calls in install hooks. The second used obfuscated code, wrapped around Bun commands, specifically designed to evade the detection logic that had just been built. Nicolas Boichat, who found this second wave, did it using a local Gemma E2B model — an AI-powered static analysis approach, not a signature scanner. The fact that he needed AI assistance to catch it is the tell: the attackers adapted to the community's defenses in under 48 hours.

At this point, Phoronix made the observation that's hard to argue with: *"It's a bit surprising they don't completely shut down AUR until they can better verify the security and safety of this user-supplied repository."*

## What This Says About the AUR's Trust Model

The AUR wiki page has said it for years: **AUR packages are user-supplied. There are no safety guarantees. Review every PKGBUILD before building.** That advice is technically correct and practically ignored by almost everyone.

The AUR's trust system is built on package names and histories. A package with 50,000 installs and a 4-year-old maintainer has implicit credibility in the community's mind. AUR helpers like `yay` and `paru` present "updates available" notifications that look and feel identical to official Arch package updates. The UX does not signal that you are about to execute arbitrary shell scripts from a stranger's git commit.

This attack didn't invent a new vulnerability. It weaponized the assumption that has always been baked into the model: that a package's name and history signal safety about its future behavior. Adopt an orphaned package, inherit the trust, inject the payload.

The same pattern has appeared before — a [2018 PDF viewer attack](https://thehackernews.com/2018/07/arch-linux-aur-malware.html) used the same adoption tactic — but at a scale of a handful of packages, not 1,500+. The difference now is the sophistication of the payload (eBPF rootkit, developer-targeted credentials, obfuscation) and the speed of the second wave adaptation.

## What Should Actually Change

None of this means "don't use Arch" or "the AUR is irreparably broken." But some structural changes would meaningfully reduce the attack surface:

**Require two-factor authentication for package adoption and updates.** The AUR currently does not require this. The barrier to injecting malicious code into 400 packages is a handful of git commits with forged author data.

**Surface recently-changed install scripts more aggressively.** AUR helpers should flag packages where the PKGBUILD or .install file has changed in the last 30 days, especially if the maintainer changed recently. This is information that exists and is not surfaced.

**Treat package adoption as a higher-trust event than a routine update.** Newly adopted packages — or packages that recently changed hands — should get a review delay, or at least a prominent warning in AUR helpers.

**Consider sandboxed builds by default.** Tools like `aurutils` with a build chroot exist, but most users don't use them. Makepkg running in a chroot doesn't prevent credential theft (the stealer runs at build time, not install time), but it limits the rootkit's access to the real system.

The Arch Linux team is working on the permanent solution. What it actually is remains to be announced. Given that this is the same model that produced a malware incident in 2018, failed to prevent 1,500 compromised packages in 2026, and watched a second wave adapt to defenses in real time, the permanent solution probably needs to be more than an alert threshold on new adoptions.

The AUR is a feature of Arch that distinguishes it from every other major distribution. The question is whether the community is finally ready to add some friction to a system that currently has almost none.

---

*Primary sources: [Arch Linux official news](https://archlinux.org/news/active-aur-malicious-packages-incident/), [The Hacker News technical breakdown](https://thehackernews.com/2026/06/over-400-arch-linux-aur-packages.html), [Whanos reverse engineering analysis](https://ioctl.fail/preliminary-analysis-of-aur-malware/), [Sonatype Atomic Arch campaign page](https://safedep.io/ti/campaigns/atomic-arch), [StepSecurity supply-chain analysis](https://www.stepsecurity.io/blog/400-aur-packages-hijacked-atomic-arch-campaign), [community detection repo](https://github.com/lenucksi/aur-malware-check), [second wave Phoronix report](https://www.phoronix.com/news/Arch-Linux-AUR-More-Malware)*
