# Let's Encrypt Just Made Explicit What Was Always True: "Universal Free TLS" Has a US Government Asterisk

**June 10, 2026**

On June 4, Let's Encrypt published version 1.7 of its Subscriber Agreement. Buried in Section 3.1 is a new clause:

> *You are not a person or entity that is: (a) located in, organized under the laws of, or ordinarily resident in any country or territory that is the target of comprehensive U.S. sanctions; (b) a prohibited or restricted party under U.S. or other applicable sanctions and export control laws and regulations; or (c) owned or controlled by or acting on behalf of anyone described in (a) or (b).*

That's [344 points and 279 comments](https://news.ycombinator.com/item?id=48453275) on Hacker News as of this morning, making it the second-biggest story of the week behind the Claude Fable 5 release.

The headline writes itself: Let's Encrypt, the nonprofit that promised to bring TLS to the entire web for free, is explicitly excluding users in US-sanctioned countries. People are angry. The Let's Encrypt team member who showed up in the HN thread (`jaas`) said the quiet part out loud: *"This subscriber agreement update was intended to better reflect our legal requirements."* Not a new policy. A more honest articulation of an existing policy.

That's the story, and it's more interesting than the outrage framing suggests.

---

## What Actually Changed

Nothing changed in practice. Let's Encrypt has always complied with US sanctions law — it's a US nonprofit incorporated in San Francisco, legally required to do so. The ISRG's existing CP/CPS ([v6.1](https://letsencrypt.org/documents/isrg-cp-cps-v6.1/), last updated May 2026) already contained sanctions compliance language.

What changed is that the language moved from the technical Certificate Policy document — a dense, compliance-oriented document that almost nobody reads — into the Subscriber Agreement that every user technically accepts when they issue a certificate. The new v1.7 Subscriber Agreement with the future effective date sits on [Let's Encrypt's repository page](https://letsencrypt.org/repository/) right next to the old v1.6, which is still marked "CURRENT."

So: more transparency, not new restriction. `jaas` confirmed directly in the thread: *"Most of our sanctions-related blocks apply only to the governments of certain sanctioned countries, not their general population. [...] It does not reflect a major change in the service we provide."*

This is probably true. The sanctions regime that applies to Let's Encrypt isn't primarily aimed at individual developers in Iran, Russia, Cuba, or North Korea trying to get a TLS certificate. It's aimed at government entities, state-owned enterprises, and designated individuals. OFAC's [recently sanctioned entities list](https://ofac.treasury.gov/recent-actions) is full of specific organizations and people, not blanket prohibitions on everyone who lives within a country's borders.

---

## But Let's Be Honest About What This Reveals

Here's the problem with the "nothing changed" framing: the fact that something was always true doesn't mean it's not worth knowing.

Let's Encrypt's stated mission, per its own website: *"Our mission is to create a more secure, privacy-respecting Web by promoting the widespread adoption of HTTPS."* Its promotional materials describe it as "for the whole internet." EFF co-founded it. The entire pitch is that TLS should be universally available, free, and automatic — no bureaucracy, no gating, no geopolitics.

The practical reality has always been: Let's Encrypt is a US nonprofit, issued under roots trusted by US-domiciled browser vendors, running on servers subject to US jurisdiction, and therefore subject to US export control and sanctions law. That's not a criticism of Let's Encrypt. It's a structural fact. You cannot simultaneously be a US-chartered organization and be immune to US law.

The clarified Subscriber Agreement makes that structural fact legible to users who didn't already know it. That's a good thing, even if the community is reacting to the visibility of the constraint rather than the constraint itself.

The harder question — and the one the HN thread mostly avoided — is whether the current configuration of the internet's certificate infrastructure should be this dependent on the legal jurisdictions of a small number of organizations.

---

## The Single-Point-of-Jurisdictional-Failure Problem

Let's Encrypt issues certificates for roughly half the HTTPS websites on the internet. The next-largest free CA is ZeroSSL, which runs out of Austria and has a smaller but meaningful share. Buypass Go SSL — another free option — [shut down in October 2025](https://mysites.guru/blog/lets-encrypt-issuance-halted-2026-05-08/). (If you have old tutorials linking to `api.buypass.com/acme/directory`, they're now stale.)

For the vast majority of websites that automate certificate issuance through certbot or similar ACME clients, Let's Encrypt is effectively the default. Most deployments don't have a configured fallback. When Let's Encrypt [halted all issuance for 2.5 hours on May 8](https://mysites.guru/blog/lets-encrypt-issuance-halted-2026-05-08/) because of a cross-signed cert problem with their Generation Y root, everyone who hadn't automated a CA failover felt it.

The sanctions language is a different kind of single point of failure: not an outage, but a jurisdiction risk. Today the policy is "governments of sanctioned countries, not their general populations." But the Subscriber Agreement says what it says. If US sanctions policy expanded its scope — say, to include all persons resident in a particular country — Let's Encrypt's legal obligations under that policy could expand accordingly.

I'm not predicting this happens. But the technical architecture of "one US nonprofit issues half the world's TLS certificates" has always been fragile in this dimension. The new Subscriber Agreement language doesn't create that fragility. It just names it.

---

## The RISC-V Parallel

One commenter in the HN thread made a comparison that stuck with me: RISC-V International moved to Switzerland in 2020, explicitly to reduce geopolitical dependency. Their stated reason: *"We have heard concerns from around the world that investment in RISC-V must come with IP access continuity to ensure a long-term strategic investment."*

RISC-V is an open instruction set architecture. The comparison to an open certificate authority protocol (ACME) issuing from a US-chartered entity is imperfect. But the underlying problem is the same: when infrastructure that the global open-source community treats as a common good is legally chartered in a single jurisdiction, that jurisdiction's politics eventually show up in the infrastructure.

You could make the same observation about GitHub, npm, PyPI, AWS, or a dozen other systems that the open internet implicitly treats as neutral commons. They're not neutral. They're chartered in specific places with specific legal obligations. Most of the time this doesn't matter. Occasionally it does.

Let's Encrypt making its US legal obligations explicit in the Subscriber Agreement is, in this context, arguably more honest than RISC-V moving to Switzerland — which relocated the legal fiction without really addressing the underlying dependency question.

---

## What Developers Should Actually Do

For most developers in most places, this doesn't change anything practical. You can keep using Let's Encrypt, certbot, and your existing automation. The process of getting a certificate hasn't changed.

A few things worth doing regardless:

**Configure a CA fallback in your ACME client.** Whether it's a sanctions enforcement scenario or a technical outage like May 8's 2.5-hour halt, having ZeroSSL as a backup issuer in your certbot or similar configuration costs nothing and has already been useful multiple times. [ZeroSSL](https://zerossl.com/) is ACME-compatible and free for standard use cases.

**Understand who your current issuer is.** If you're using certbot's defaults, you're probably on Let's Encrypt. Check your certificates. Know what you're dependent on.

**If you're building something that specifically serves users in sanctioned countries** — privacy tools, VPNs, anonymization services — you've been implicitly relying on a US-chartered entity's legal interpretation of who counts as a "government entity" vs. a general user. That interpretation has been favorable. It may not always be. Design your certificate issuance with that in mind.

**Read the actual OFAC list if you're uncertain.** [OFAC's recent actions page](https://ofac.treasury.gov/recent-actions) shows what's actually being sanctioned. It's specific organizations and individuals, not blanket country prohibitions in most cases. The Subscriber Agreement language sounds broader than OFAC's actual current enforcement posture.

---

## One More Thing Worth Noting

The HN headline's phrasing — "bans certificate usage in any US sanctioned territory" — generated confusion in the thread. Several people initially thought Let's Encrypt had banned usage in *US territories* like Puerto Rico. Someone else thought Let's Encrypt had been sanctioned *by* the US. The contronym problem: "sanction" means both to permit and to penalize, depending on context.

The confusion is worth flagging because it reflects a broader communicative problem with how the internet infrastructure community talks about geopolitical constraints. Most developers are not fluent in US sanctions law. When a CA publishes a Subscriber Agreement update in legal language, the people who understand it are compliance lawyers. Everyone else reads headlines.

Let's Encrypt acknowledging in the thread that they have "more work to do to make that text more understandable" is the right response. The gap between what the Subscriber Agreement says and what the actual policy is isn't just a PR problem — it's a trust problem. If users can't tell from the agreement language whether their certificate will be revoked, that's a design flaw in how the policy is communicated.

The real lesson from this week's HN thread: the people building the infrastructure of the open web need to get much better at explaining its actual constraints. Not to avoid them — the constraints are real — but so developers can architect around them with accurate information rather than discovering them at the worst possible moment.

---

*Sources: [Let's Encrypt Subscriber Agreement v1.7 (PDF)](https://letsencrypt.org/documents/LE-SA-v1.7-June-04-2026-diff.pdf), [Let's Encrypt Policy Repository](https://letsencrypt.org/repository/), [Hacker News discussion](https://news.ycombinator.com/item?id=48453275), [OFAC Recent Actions](https://ofac.treasury.gov/recent-actions), [Let's Encrypt May 2026 issuance halt postmortem](https://mysites.guru/blog/lets-encrypt-issuance-halted-2026-05-08/).*
