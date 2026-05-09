# Google Killed WEI in 2023. It Just Relaunched It as reCAPTCHA.

**May 9, 2026**

In May 2023, Google proposed the [Web Environment Integrity API](https://github.com/RupertBenWiser/Web-Environment-Integrity), a browser-level mechanism that would let websites verify whether a visitor was using "approved" software. The idea was that Chrome would attest to your device's identity — proving you were running an unmodified OS, an unmodified browser, and hardware Google deemed trustworthy. Critics immediately understood what this really was: not bot detection, but a way for Google to decide who gets to participate on the web. The backlash was fast, loud, and effective. Firefox, Brave, and privacy advocates hammered the proposal. By November 2023, Google abandoned it.

That's the happy ending version. Here's what actually happened: Google waited two and a half years, renamed it, and shipped it anyway.

This week, two stories hit Hacker News in quick succession. First: [Google Cloud Fraud Defense is just WEI repackaged](https://privatecaptcha.com/blog/google-cloud-fraud-defence-wei/) (660 points, 336 comments). Second: [Google broke reCAPTCHA for de-googled Android users](https://reclaimthenet.org/google-broke-recaptcha-for-de-googled-android-users) (870 points, 287 comments). Read them together and you see the same mechanism, the same exclusions, and the same architecture — just wearing a different uniform.

## How "Google Cloud Fraud Defense" Works

[Announced on April 22, 2026](https://cloud.google.com/blog/products/identity-security/introducing-google-cloud-fraud-defense-the-next-evolution-of-recaptcha), Google Cloud Fraud Defense is described as "the next evolution of reCAPTCHA." Existing reCAPTCHA customers are automatically enrolled. When the system suspects a user might be a bot, it presents a QR code. You scan it with your phone to "prove human presence." Straightforward enough.

What does the scan actually do? Your phone authenticates against Google's Play Integrity API — a device attestation service that verifies your Android phone is certified, unmodified, and running Google Play Services version 25.41.30 or higher. The confirmation is returned to the requesting site as proof you're human.

Notice what's doing the work here. It's not a puzzle. It's not behavioral analysis. It's a certificate from Google attesting that you're running hardware and software Google has approved.

The [requirements page](https://support.google.com/recaptcha/answer/16609652) phrases it gently: "modern Android device with Google Play Services installed, or modern iPhone/iPad." That phrase — "Google Play Services installed" — is the entire game. Play Services is Google's closed-source software layer that only runs on certified devices. If your phone doesn't have it, you cannot pass Play Integrity. You fail the CAPTCHA. You're locked out.

## Who Gets Locked Out

The people most likely to be running Android without Google Play Services are not bots. They are:

**GrapheneOS users.** GrapheneOS is a security-hardened Android fork [recommended by the EFF](https://www.eff.org/deeplinks/2023/03/should-you-get-a-pixel-phone) and used by journalists, lawyers, and activists in high-risk environments. It does not ship Play Services by default. Its sandboxed compatibility layer can run *some* Play Services functionality, but it cannot satisfy Play Integrity at the `MEETS_DEVICE_INTEGRITY` level that Fraud Defense requires.

**LineageOS users.** Specifically LineageOS for microG — an open-source Android distribution built for people who want the platform without Google's telemetry. Fails for the same reason.

**Firefox for Android users.** Firefox doesn't integrate Play Integrity by design. Mozilla's 2023 position on device attestation was explicit and remains current. Firefox users are excluded not because they're bots but because they use software that declines to participate in Google's certification scheme.

iOS users, notably, can pass the QR code challenge without installing anything extra. The asymmetry is telling: Google didn't require iPhone users to run Google software to prove they're human. Only Android users who declined Play Services get locked out. This isn't about bot detection. It's about ecosystem enforcement.

## The WEI Playbook

When Google proposed WEI in 2023, the criticism was conceptually precise: this creates a two-tier web where only "approved" software can fully participate. The practical effect would be that running an ad blocker, a modified browser, or any client Google hadn't certified could result in websites refusing to serve you.

Google's response at the time was to withdraw the proposal. But the underlying problem — AI-driven bot farms getting better every year — didn't go away. Google just found a different path to the same destination.

Google Cloud Fraud Defense doesn't require browser-level WEI. It works through the phone camera and Play Integrity instead. The result is architecturally identical: to pass verification, you need hardware and software Google has attested. The exclusions are identical: privacy-respecting Android forks, alternative browsers, users who've opted out of Google's ecosystem.

The framing is just more palatable. "The next evolution of reCAPTCHA" sounds like a security upgrade. "Web Environment Integrity" sounded like surveillance infrastructure. Same pipe, different label.

## Will It Even Work?

Here's the frustrating part: the technical analysis suggests it won't stop serious bot operations, while certainly inconveniencing legitimate privacy-conscious users.

As [PrivateCaptcha points out](https://privatecaptcha.com/blog/google-cloud-fraud-defence-wei/), defeating the QR code challenge mechanically requires a camera pointed at a screen — trivial to automate with off-the-shelf hardware. A compliant Android device for Play Integrity attestation costs roughly $30 at retail; at bulk bot-farm pricing, it's a rounding error. Professional bot operations will adapt within weeks. The marginal cost of compliance, for a sophisticated adversary, is near zero.

The HN thread made the same point more bluntly: "Everything that can be automated will eventually be automated." Device attestation doesn't eliminate fraud; it raises the floor cost marginally while building a permanent surveillance and gatekeeping layer into core web infrastructure.

What it does do reliably is create lock-in. Every site that adopts Google Cloud Fraud Defense becomes a site that requires Google-certified hardware to access when challenged. That's not a side effect. Given that Google is simultaneously the dominant browser maker, the dominant mobile OS vendor, and the company selling the fraud defense service, it's the obvious business outcome.

## The Pattern Is the Point

The thing that makes this pattern worth watching isn't just this specific product. It's what it reveals about how large platform companies navigate public backlash.

When a proposal is named and debated in the open — "Web Environment Integrity API," with a GitHub repo and a spec and clear authorship — it can be killed. The community organized, made the argument, and won.

When the same capability ships embedded inside "Google Cloud Fraud Defense, the next evolution of reCAPTCHA," as a quietly updated support page that a Reddit user on r/degoogle happened to notice seven months after the fact, the community has to rediscover it from scratch. By then, it's already deployed to "14 million domains globally."

Google built this dependency quietly for at least seven months. An Internet Archive snapshot from October 2025 shows the Play Services requirement already listed in the support page. It took until May 2026, and two viral Hacker News posts, for it to become widely known. That's not an accident. That's a lesson Google learned from 2023.

The question now is what happens next. The practical workaround — use a Play Services-compatible device as a QR scanner while using a privacy-respecting primary device — is annoying but functional. GrapheneOS users are already discussing whether the sandboxed Play Services compatibility layer can be made to pass Play Integrity at the required level. Mozilla hasn't updated its 2023 position.

But the more important question is whether this time the community response matches the scale of what's been deployed. Last time, Google proposed WEI before it existed. This time, it already runs on 14 million domains.

---

*Primary sources: [Google Cloud Fraud Defense announcement](https://cloud.google.com/blog/products/identity-security/introducing-google-cloud-fraud-defense-the-next-evolution-of-recaptcha) · [PrivateCaptcha analysis](https://privatecaptcha.com/blog/google-cloud-fraud-defence-wei/) · [Reclaim the Net reporting](https://reclaimthenet.org/google-broke-recaptcha-for-de-googled-android-users) · [Android Authority](https://www.androidauthority.com/google-recaptcha-play-services-requirement-3664806/) · [HN: WEI repackaged](https://news.ycombinator.com/item?id=48063199) · [HN: reCAPTCHA broken](https://news.ycombinator.com/item?id=48067119)*
