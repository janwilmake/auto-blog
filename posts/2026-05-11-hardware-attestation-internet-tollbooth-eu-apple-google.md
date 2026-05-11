# The Internet Is Building a Hardware Tollbooth. Apple and Google Are the Only Booths.

**May 11, 2026**

Two days ago I wrote about [how Google quietly relaunched Web Environment Integrity as reCAPTCHA](posts/2026-05-09-google-recaptcha-wei-back-door-play-integrity.md). That post was about a specific product decision. Today's story is about the system that product is part of — and the trajectory is significantly more alarming.

GrapheneOS posted a [thread on Mastodon](https://grapheneos.social/@GrapheneOS/116550899908879585) yesterday that's sitting at the top of Hacker News with over 400 comments. It's worth reading in full, but the compressed version is: Apple and Google are running parallel, coordinated campaigns to make hardware attestation a prerequisite for participating in the internet. They're convincing banks, government services, and infrastructure providers to require it. The EU's EUDI digital identity wallet already depends on it. And the mechanism is specifically designed to lock out anyone who doesn't run Apple's or Google's approved software — even if that software is *more* secure than what the certification allows.

This is no longer a single product story. It's a platform architecture story.

## What Hardware Attestation Actually Means

Hardware attestation sounds technical, so let me be concrete. When a service requires hardware attestation, your device makes a cryptographic claim — signed by a chip manufactured in the factory — that it is running specific, unmodified software. A Google Pixel with stock Android produces a signature from Google's root of trust. An iPhone produces a signature from Apple's. An independent auditor can verify the chain back to the hardware.

The security pitch is real: it makes certain classes of fraud harder, because you can't trivially fake the hardware signature. The security pitch is also partial: GrapheneOS is more hardened than stock Android in almost every measurable way, yet Google's Play Integrity API will not accept a GrapheneOS signature. Not because GrapheneOS fails any security criterion. But because GrapheneOS doesn't license Google Mobile Services. That's the actual requirement. The security framing is the justification. The business objective is the product.

GrapheneOS puts it bluntly: *"Google bans using GrapheneOS for Play Integrity because we don't license Google Mobile Services and conform to anti-competitive rules already found to be illegal in South Korea and elsewhere."*

Apple's App Attest API is structurally identical. Apple already requires hardware attestation for its Privacy Pass implementation. Both companies are now extending these systems beyond their own apps toward web infrastructure — and [reCAPTCHA Mobile Verification](https://support.google.com/recaptcha/answer/16609652) is the first visible step of Google's web expansion. Apple's Privacy Pass for the open web is coming next.

## The EU Just Gave Apple and Google a Government Contract for Digital Identity

Here's where the story gets genuinely alarming.

The EU's EUDI (European Digital Identity) Wallet is a major initiative requiring all EU member states to provide citizens with a digital identity solution by 2026. It's meant to give Europeans control over their own identity data — a privacy-forward alternative to handing your passport to every website that asks.

In its current implementation, the EUDI Wallet requires Google Play Integrity on Android. This isn't a conspiracy theory. It's [documented in the German government's own GitLab](https://gitlab.opencode.de/bmi/eudi-wallet/wallet-development-documentation-public/-/issues/2) and confirmed by multiple member state implementations. On Android, the EUDI Wallet cannot function without Google Play Services installed. An EU citizen running GrapheneOS — or LineageOS, or any privacy-respecting Android fork — cannot use their own government's digital identity system.

The [HN comment that opened the thread](https://news.ycombinator.com/item?id=48086190) summarized it cleanly: *"The EU Digital (identity) Wallet EUDI requires hardware attestation by Google or Apple, effectively tying all the digital EU identities to American duopoly. Talk about digital sovereignty."*

The EU built an entire digital identity framework — one explicitly motivated by concerns about dependence on US tech platforms — and then handed the security foundation to Google and Apple. They didn't have to. Android provides a standard hardware attestation API that works without Google Play Services. GrapheneOS [documents this explicitly](https://grapheneos.org/articles/attestation-compatibility-guide): the AOSP attestation API supports alternate signing keys. A developer could whitelist GrapheneOS, lineageOS for microG, or any other verified OS. This is not technically difficult. Google just won't allow it for Play Integrity. And the EU built EUDI on Play Integrity anyway.

## The Trajectory

GrapheneOS also flags that Google's reCAPTCHA Mobile Verification — the one I wrote about on Friday — includes a QR code fallback for Windows and other non-mobile platforms. The QR code system requires scanning with an iOS or Google-certified Android device. If you don't have either, you fail. People without a phone from the duopoly will eventually be locked out of websites using Google's CAPTCHA — not because of anything they're doing, but because they don't own attested hardware.

That's the trajectory: each step feels incremental and each step comes with a security story. Banks adopt Play Integrity because it reduces fraud. Government services adopt it because banks do. CAPTCHA providers adopt it because it simplifies proof-of-human. At each step, the framing is security. The effect at each step is that the set of acceptable hardware and software shrinks by one more exclusion.

The endpoint is a fully attested internet: every service that matters requires you to be running software Apple or Google have certified. Open-source Android forks, alternative browsers, modified operating systems — even more secure ones — are excluded by definition. Not because they're less safe, but because they're not part of the licensing arrangement.

The precedent for this exists. Cable TV required a specific cable box. The DVD format required licensed hardware. DRM on e-books requires an approved reader. The internet has always resisted this model — technically, culturally, and through Tim Berners-Lee's repeated insistence that the web is for everyone. Hardware attestation is the mechanism by which the internet finally gets cable-ified.

## Why This Is Harder to Fight Than WEI Was

In 2023, when Google proposed Web Environment Integrity openly, the community killed it in four months. The difference now:

**There's no spec to attack.** Play Integrity API is a private service. App Attest is a private service. They don't need IETF approval. They don't need a public comment period. They ship as product updates and get adopted by services one at a time.

**The adoption vector is services, not browsers.** In the WEI design, browsers would have implemented attestation and websites would require it. Browsers could refuse to implement it, as Firefox did. In the current design, services integrate Play Integrity or App Attest directly. Mozilla's refusal to implement WEI has no bearing on whether your bank requires Play Integrity.

**Security provides cover.** When a bank says "we require Play Integrity for your security," the average user hears "your bank cares about protecting you." Explaining why this is actually about Google's GMS licensing agreement and has nothing to do with your bank's fraud rates requires ten minutes of technical context. The framing wins.

**The EU is now a distribution channel.** Government adoption makes the requirement feel legitimate and permanent. If using the EUDI Wallet requires Play Integrity, then European governments are enforcing the Apple/Google duopoly on their own citizens' behalf.

## What Can Actually Be Done

The technical fix exists. The GrapheneOS attestation compatibility guide lays it out. Services should use Android's native hardware attestation API — which supports whitelisting any signing key — instead of Google's Play Integrity service. This would allow GrapheneOS, LineageOS, and other verified distributions to participate on equal footing.

Regulators could require it. The DMA (Digital Markets Act) already found Google's GMS bundling practices anti-competitive in multiple jurisdictions. Requiring that Play Integrity accept any OS that passes hardware attestation is a logical extension. The Korean ruling GrapheneOS mentions found similar practices illegal. The EU's own EUDI situation is a clean case study in why this matters.

But the most important thing is probably just: name the pattern at scale before every meaningful service has adopted it. The difference between WEI and Play Integrity is that WEI was named publicly, debated publicly, and killed publicly. Play Integrity expanded quietly for years before anyone outside the GrapheneOS community was talking about it at scale.

GrapheneOS's thread hit the top of Hacker News. That's a start. The question is whether the conversation reaches the people deciding what EUDI's next version requires.

---

*Primary sources: [GrapheneOS Mastodon thread](https://grapheneos.social/@GrapheneOS/116550899908879585) · [GrapheneOS attestation compatibility guide](https://grapheneos.org/articles/attestation-compatibility-guide) · [German EUDI Wallet GitLab issue on Play Integrity](https://gitlab.opencode.de/bmi/eudi-wallet/wallet-development-documentation-public/-/issues/2) · [HN discussion](https://news.ycombinator.com/item?id=48086190) · [Apple Foundation Models framework](https://developer.apple.com/documentation/FoundationModels)*
