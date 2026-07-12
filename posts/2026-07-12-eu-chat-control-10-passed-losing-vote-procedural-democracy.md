# EU Chat Control 1.0 Passed Yesterday. More MEPs Voted Against It Than For It.

**Date:** 2026-07-12

The vote count was 276 in favour, 314 against, 17 abstentions. Chat Control 1.0 passed anyway.

If that sounds backwards to you, you're not confused — that's just EU parliamentary procedure doing something most people would not call democracy. And it's worth understanding exactly what happened, because the story is being told in two incompatible ways simultaneously, and both are partially wrong.

---

## What Chat Control 1.0 Actually Is

Chat Control 1.0 is not a law requiring message scanning. It's a *derogation* — a temporary exemption from the EU's ePrivacy Directive that *allows* online platforms to voluntarily scan private messages for child sexual abuse material (CSAM). Without it, platforms like Instagram, Discord, Snapchat, Skype, and Gmail would technically be violating EU privacy law by running their existing content-moderation systems.

The derogation expired. The Parliament rejected extending it in March. Then it came back.

The "Chat Control 1.0 passed" framing implies that EU citizens are now being mass-surveilled in a new way. That's not quite right. The derogation returning mostly restores the status quo that already existed — US tech companies can keep doing the scanning they were already doing before the derogation lapsed.

The "nothing to worry about, E2EE is protected" framing is also not quite right. More on that below.

---

## The Procedural Trick

Here's what actually happened, because it's genuinely remarkable and worth understanding clearly.

The European Parliament had already rejected this extension in March. The vote failed. Done — or so everyone thought.

In late June, Parliament President Roberta Metsola reopened the file, citing a "dangerous gap in child protection." She sent it to the Council. The Council sent it back to Parliament at the start of summer recess, timed carefully. This is the trick.

When a law comes back to the Parliament on a "second reading," the threshold to reject it changes. Instead of a simple majority of *those present and voting*, you now need an absolute majority of *all seated MEPs* — which is 361 votes. Not 361 of the people who show up; 361 of the total membership, regardless of attendance.

The result: 276 MEPs voted for it. 314 voted against it. The "against" side won the vote and lost the outcome. Because 314 < 361, the law advanced.

The timing wasn't accidental. Parliament was heading into summer recess. Attendance drops. Getting 361 bodies in the room to unanimously vote no is extremely hard. The Council and Commission knew that.

Patrick Breyer, the civil rights activist and former MEP who's been fighting this bill for years, described it plainly: "As long as EU governments can use procedural loopholes to continually extend their comfortable status quo of voluntary, indiscriminate mass scanning, they have zero incentive to engage with the Parliament's targeted, legally sound, and far more effective child protection strategy."

---

## The E2EE Amendment: Symbolic Victory, or Something More?

Privacy advocates did win one amendment. The RENEW group pushed through language that would "exclude communications to which end-to-end encryption is, has been, or will be applied" from the scope of the law.

This sounds significant. But read it carefully.

First: the platforms that actually do the CSAM scanning are largely *not* end-to-end encrypted. Instagram DMs, Gmail, Discord — none of these use E2EE by default. WhatsApp does. Signal does. iMessage does (sort of). The amendment exempts the platforms that weren't doing the scanning anyway.

Second: the Council has not agreed to this amendment yet. The file now goes to member states for formal sign-off. The Council's previous positions have included vague "statements about protecting privacy," but no actual technical debate about what it would mean to reconcile mass scanning with E2EE — because you [can't](https://www.schneier.com/blog/archives/2022/10/breaking-end-to-end-encryption.html). The Council is likely to reject the amendment or water it down.

Third: critics like the Internet Watch Foundation — which helped push for the emergency vote — are already [on record](https://ncmec.org/theissues/end-to-end-encryption) arguing that E2EE is the real obstacle to child protection. The E2EE exemption isn't a settled win; it's a line in the sand before a longer fight.

---

## What Actually Gets Scanned Now

From Patrick Breyer's [breakdown](https://www.patrick-breyer.de/en/eu-parliament-greenlights-chat-control-1-0-breyer-our-children-lose-out/):

**Coming back:** US tech companies can again scan private messages on Instagram, Discord, Snapchat, Skype, Xbox, Gmail, and iCloud without a warrant or prior suspicion.

**Always was legal, still is:** Scanning of public posts and cloud-stored files.

**Not scanned:** End-to-end encrypted messages on WhatsApp, Signal, iMessage. European messaging providers — notably — have never implemented Chat Control scanning at all.

That last point is interesting. European providers stayed out of this voluntarily. The companies scanning your messages are predominantly American. Which means the EU is, in practice, primarily authorizing US Big Tech to continue doing surveillance on EU citizens in the name of child protection. The German Federal Criminal Police (BKA) has noted that 48% of incoming alerts under the existing regime aren't criminally relevant. The EU Commission's own figures show that mass scanning of private chats accounted for only 36% of all abuse reports in 2024 — the majority came from public posts and cloud storage that were never affected by the derogation's lapse.

---

## September Is When It Actually Gets Decided

Chat Control 1.0 is a stopgap. The current extension runs until April 2028. What matters more is Chat Control 2.0 — the permanent framework — which is currently deadlocked in Council negotiations. That vote will happen in September.

Chat Control 2.0 is a different and more dangerous animal. It proposes *mandatory* (not voluntary) scanning obligations, and earlier drafts attempted to extend this to end-to-end encrypted communications through "client-side scanning" — a technical scheme where your device scans your messages before encryption and reports matches to authorities. Cryptographers [almost universally](https://www.iacr.org/archive/eurocrypt2021/12697001/12697001.pdf) describe this as "breaking E2EE from the inside." The European Data Protection Board has [called earlier versions of the proposal unlawful](https://edpb.europa.eu/our-work-tools/documents/our-documents/opinion/opinion-042022-european-commissions-proposal-regulation_en).

The procedural trick that just passed Chat Control 1.0 was designed, at least in part, to create a "working temporary law" that removes the urgency argument against the more sweeping 2.0. If 1.0 is in place, the argument goes, there's no crisis — take your time on 2.0. The privacy coalition needs to treat September as the main event.

---

## The Part Nobody Is Saying Clearly

The deepest problem here isn't the surveillance — it's the governance structure.

A majority of directly elected representatives voted no. The law passed anyway, because a procedural rule designed for different circumstances was exploited to change the effective threshold at a moment of low attendance.

This isn't unique to Chat Control. It's a feature of how the EU's three-institution legislative machine operates — Council, Commission, and Parliament can all route around each other in ways that feel democratic in individual steps and undemocratic in aggregate. The Council is not directly elected. The Commission is not directly elected. The Parliament is the only institution with a direct democratic mandate, and this vote just demonstrated exactly how easily that mandate can be outmaneuvered.

The Hacker News thread on this has [1,400+ upvotes and hundreds of comments](https://news.ycombinator.com/item?id=48843923) — more engagement than most major tech stories this week. The reaction isn't just about encryption. It's about watching a loss get ruled a win because someone rewrote the scoring rules mid-game.

If you use Signal, you're fine for now. If you use Gmail or Instagram DMs, the companies running them just regained legal cover to keep scanning. And if you're paying attention to September, the E2EE question is very much not settled.

---

*Primary sources: [Patrick Breyer's breakdown](https://www.patrick-breyer.de/en/eu-parliament-greenlights-chat-control-1-0-breyer-our-children-lose-out/) | [Euronews analysis](https://www.euronews.com/next/2026/07/10/chat-control-10-passed-the-european-parliament-through-the-back-door) | [Vote record](https://howtheyvote.eu/votes/195778) | [Tom's Hardware writeup](https://www.tomshardware.com/tech-industry/cyber-security/chat-control-1-0-sneaks-through-the-eu-parliament-letting-companies-scan-user-data-without-warrants-legal-tactic-used-to-force-a-majority-required-re-vote-on-eve-of-parliament-break)*
