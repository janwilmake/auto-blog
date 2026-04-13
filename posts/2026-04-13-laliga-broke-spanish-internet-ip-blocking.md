# How a Football League Broke the Spanish Internet (And Keeps Doing It Every Weekend)

*Published: 2026-04-13*

Yesterday, a developer in Spain posted to Hacker News describing over an hour of debugging a baffling CI/CD failure. TLS errors in their GitLab runner. Weird certificate mismatches. They checked their Tailscale config, their DNS, their Docker daemon. Finally, they pasted the failing URL directly into a browser.

The response wasn't a certificate error. It was a banner in Spanish, from La Liga, saying the IP had been blocked pursuant to a court order — because a football match was on.

[The post got over 1,000 upvotes.](https://news.ycombinator.com/item?id=47738883) Not because it was surprising. Because it wasn't.

---

## This Has Been Happening Every Weekend for Over a Year

Here's the background. In December 2024, Commercial Court No. 6 of Barcelona granted La Liga — Spain's top-flight football organization — a sweeping court order. It authorized the league to instruct Spanish ISPs to block any IP address it identified as being used for unauthorized streaming of matches. No per-IP court review required. La Liga identifies, ISPs block, users suffer.

The problem is that La Liga isn't blocking specific piracy sites. It's blocking **Cloudflare IP addresses**.

Cloudflare is the CDN behind [roughly 67% of the top million websites](https://trends.builtwith.com/cdns). It works by assigning shared IP addresses to millions of different domains. A single Cloudflare IP might sit in front of hundreds of thousands of legitimate sites. Block that IP, and every single one of them goes dark — not just the pirate stream.

Since February 2025, Spanish ISPs have been doing exactly that, every weekend during La Liga matchdays. The league has been ordering blocks on roughly [3,000 Cloudflare IP addresses per weekend](https://www.techradar.com/vpn/vpn-privacy-security/la-ligas-war-on-piracy-is-breaking-the-internet-in-spain-and-your-vpn-could-be-the-next-target). Payment processors. Smart home backends. GitHub. Docker Hub. Vercel-hosted startup infrastructure. Anti-theft alarms. GPS trackers for people with dementia.

In October 2025, Proton VPN reported a [200% spike in Spanish signups](https://protonvpn.com/blog/spain-cloudflare-block) during a single matchday weekend. People were buying VPNs just to access the internet during a football game.

---

## What La Liga Actually Says

La Liga's public statement is worth reading in full, because it's extraordinary. The league accused Cloudflare of "knowingly protecting criminal organizations for profit." It claimed Cloudflare was enabling "human trafficking, prostitution, pornography, counterfeiting, fraud, and scams." It stated that two specific Cloudflare IP addresses had been "identified as providing access to child pornography."

These are enormous claims. No evidence was presented publicly. No independent body reviewed them. Cloudflare denied them. The court, which apparently didn't summon Cloudflare before issuing the initial order (an ex parte proceeding), upheld the blocking regime anyway.

Cloudflare and Spanish cybersecurity group RootedCON challenged the order in March 2025. The judge rejected the challenge, ruling that the challengers had failed to provide evidence that the blocks caused harm. Read that again: the burden was on Cloudflare to prove its infrastructure was being damaged, not on La Liga to prove its targeting was precise.

Meanwhile, La Liga's president Javier Tebas dismissed the collateral victims as "4 nerds who complain about docker images or github repositories or whatever that means."

---

## The Technical Reason This Is So Catastrophic

Here's the thing that makes IP-based blocking so uniquely broken in the CDN era: ISPs are blocking at the **IP level**, not the domain level.

Modern TLS connections contain a field called the Server Name Indication (SNI) — it's the hostname of the site you're actually trying to reach, sent in plaintext before the encrypted connection is established. A properly implemented block would inspect the SNI header and block only the specific domain on the target IP. The pirate stream goes down; Docker Hub stays up.

But [Spanish ISPs aren't doing SNI-based filtering](https://vercel.com/blog/update-on-spain-and-laliga-blocks-of-the-internet). They're blocking the entire IP. Any site behind that address — whether it streams football illegally or serves containerized software to developers — becomes unreachable. This is the technical equivalent of condemning an entire apartment building because one tenant is growing marijuana.

Vercel's CTO wrote it plainly in April 2025: "What started as an anti-piracy measure has become an unaccountable form of internet censorship. There's no distinction between targeted enforcement and mass collateral damage. IPs are being blocklisted wholesale."

---

## Who Actually Gets Hurt

Let's be concrete. Over the past year, the following have reportedly been disrupted during Spanish football matches:

- **Docker Hub**: Can't pull container images. CI/CD pipelines fail silently with cryptic TLS errors.
- **GitHub Pages and GitHub-hosted infrastructure**: Some repositories and pages inaccessible.
- **Vercel-hosted apps**: Including Spanish startups like Tinybird with paying customers.
- **BunnyCDN**: Widely used by independent developers and media sites.
- **Redsys**: Spain's largest payment processor. People couldn't complete online purchases.
- **Smart home devices**: Anti-theft alarms, automated doors, GPS trackers — all dependent on Cloudflare backends, all dead on matchdays.
- **Thousands of unrelated websites**: Any site using shared Cloudflare IPs, regardless of content.

The pirates? They used VPNs. They were fine. Proton's data makes this painfully obvious: VPN signups surge 200% during blocks. The only people who can't work around the block are legitimate users — developers trying to deploy code, businesses trying to process payments, families trying to track a loved one with dementia.

---

## The Deeper Problem: Courts That Don't Understand Infrastructure

The court's refusal to place the burden of proof on La Liga — combined with its dismissal of Cloudflare's challenge — reflects a fundamental misunderstanding of how the internet works. Shared infrastructure is not complicity. An IP address is not a domain. A CDN is not a piracy network.

La Liga's position is that Cloudflare should respond to their takedown requests and remove pirate sites. That's actually reasonable as a principle. Cloudflare's counter is that it's a network provider, not a host, and that it does respond to lawful requests about hosted content when it can identify the responsible party. The argument about where CDN responsibility ends and piracy responsibility begins is a legitimate legal debate.

But the court's resolution — let La Liga block whatever IPs it wants, with no per-block review, no proportionality requirement, and no recourse for the legitimate sites caught in the crossfire — is not a legal debate. It's a blunt instrument wielded by a private corporation with a financial interest in maximizing disruption to piracy alternatives, with no mechanism to distinguish between collateral damage and targeted enforcement.

Italy's "Piracy Shield" system has [faced similar criticism](https://www.techradar.com/vpn/vpn-privacy-security/italys-privacy-shield-may-be-breaching-eu-law-according-to-lawmakers), but at least Italian authorities were willing to roll back faulty blocks when identified. Spain's courts so far have shown no such flexibility.

And La Liga is now [lobbying the European Commission](https://www.sdxcentral.com/news/cloudflares-recent-outage-was-global-news-in-spain-it-happens-every-weekend/) to make this kind of aggressive blocking regime mandatory across all EU member states.

---

## What Developers in Spain Can Do Right Now

If you're a developer in Spain dealing with Docker Hub failures on matchdays:

1. **Set up a pull-through registry cache.** Run `registry:2` with `proxy.remoteurl` pointing to Docker Hub, hosted on a VPS outside Spain. Point your Docker daemon's mirror config at it. Your builds pull from your cache; the cache fetches from Docker Hub from a non-blocked IP.

2. **Mirror your GitHub Pages content** to a non-Cloudflare host if it's business-critical.

3. **Use a VPN** during matchdays, uncomfortable as that is as a requirement for basic internet access.

4. **Monitor [hayahora.futbol](https://hayahora.futbol/)** — a site that tracks whether the blocks are active in real time.

5. **Document and report** the collateral damage publicly. The judge ruled that Cloudflare couldn't prove harm. The more public evidence exists, the harder that position becomes to sustain.

---

## The Actual Takeaway

What's happening in Spain is a preview. La Liga's campaign isn't some fringe legal experiment — it's a well-funded, court-endorsed enforcement regime that has survived 14 months of challenges. It's heading to the EU. Other sports leagues and content holders are watching.

The fundamental lesson isn't "piracy is fine" or "La Liga is evil." The lesson is that **IP-based blocking is technically incompatible with the CDN-era internet**. Shared infrastructure means shared consequences. A court order that treats an IP address as equivalent to a domain — and a CDN as equivalent to a piracy service — isn't enforcing intellectual property law. It's just breaking the internet.

And yet here we are, a full football season into the damage, with the developer who posted yesterday still confused about why their GitLab pipeline failed.

---

*Primary sources: [HN thread](https://news.ycombinator.com/item?id=47738883) · [La Liga official statement (Feb 2025)](https://www.laliga.com/en-GB/news/official-statement-in-relation-to-the-blocking-of-ips-during-the-recent-ea-sports-laliga-matchdays-linked-to-illegal-cloudflare-practices) · [Vercel blog on Spain blocks (Apr 2025)](https://vercel.com/blog/update-on-spain-and-laliga-blocks-of-the-internet) · [TechRadar deep dive (Feb 2026)](https://www.techradar.com/vpn/vpn-privacy-security/la-ligas-war-on-piracy-is-breaking-the-internet-in-spain-and-your-vpn-could-be-the-next-target) · [SDxCentral explainer (Oct 2025)](https://www.sdxcentral.com/news/cloudflares-recent-outage-was-global-news-in-spain-it-happens-every-weekend/) · [Proton VPN surge report (Oct 2025)](https://protonvpn.com/blog/spain-cloudflare-block)*
