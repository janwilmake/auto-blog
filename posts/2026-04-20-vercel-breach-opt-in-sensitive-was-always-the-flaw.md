# The Vercel Breach Started With a Roblox Exploit — But the Real Flaw Was Always the "Sensitive" Checkbox

**Date:** 2026-04-20

A Vercel breach disclosure landed yesterday, and by now you've probably seen the headlines: [ShinyHunters offering Vercel data for $2 million on BreachForums](https://www.securityweek.com/next-js-creator-vercel-hacked/), CEO Guillermo Rauch posting an incident thread on X, a mysterious AI tool called Context.ai named as the entry point. The coverage has focused heavily on the drama — the $2M asking price, the "highly sophisticated" attacker characterization, Mandiant being brought in.

But the more you read the primary sources, the more a quieter story emerges. The hack itself is almost secondary. The real issue was a platform design decision that's been sitting there for years, hiding in plain sight.

Let's reconstruct what actually happened.

## The Attack Chain, Simplified

According to [Vercel's incident bulletin](https://vercel.com/kb/bulletin/vercel-april-2026-security-incident), [Context.ai's security update](https://context.ai/security-update), and [Hudson Rock's infostealer analysis](https://www.infostealers.com/article/breaking-vercel-breach-linked-to-infostealer-infection-at-context-ai/), the chain looks like this:

1. **A Context.ai employee downloaded Roblox "auto-farm" scripts.** These are notorious Lumma Stealer delivery vectors. The employee's corporate credentials — Google Workspace logins, AWS keys, Supabase tokens, even the `support@context.ai` account — were harvested and eventually sold.

2. **Context.ai's AWS environment was compromised** sometime around March 2026. Inside that environment sat OAuth tokens that users had granted to Context.ai's Google Workspace app — the one embedded in their now-defunct Chrome extension, which had requested "full read access to all Google Drive files."

3. **A Vercel employee had signed up for Context.ai's AI Office Suite using their Vercel enterprise Google account** and clicked "Allow All." Vercel is not, and was never, a Context.ai customer. This was a personal trial by one employee.

4. **The attacker used the stolen OAuth token to pivot into Vercel's Google Workspace**, and from there into Vercel's internal systems. Vercel's CEO confirmed: "the attacker got further access through their enumeration." What they enumerated were **environment variables not marked as "sensitive."**

The [Trend Micro timeline](https://www.trendmicro.com/en_us/research/26/d/vercel-breach-oauth-supply-chain.html) puts the initial OAuth compromise as far back as June 2024. The attacker maintained persistent access for roughly a year before anything was detected — and only came to light because someone posted about it on a forum.

## Everyone Is Talking About the Wrong Thing

The supply chain narrative is real. An AI tool's consumer Chrome extension, used by one employee in their personal capacity, opened a door into one of the most widely-deployed developer platforms in the world. That's legitimately terrifying, and it should make every security team reassess their "shadow AI" exposure.

But here's what I keep coming back to: **Vercel's own design made this significantly worse than it needed to be.**

Vercel's environment variable model has two tiers:
- **Sensitive** — encrypted at rest, not readable after creation via the dashboard or API, never surfaced in build logs.
- **Non-sensitive** — readable via the dashboard and API post-creation, can appear in build logs and preview deployments.

The "sensitive" flag is **opt-in**. You have to consciously choose to mark a variable as sensitive when you create it. The default is non-sensitive.

And this matters enormously because of what developers actually store in non-sensitive variables. Not secrets, in theory. In practice? Database URIs. Webhook tokens. Third-party API endpoints. Auth configuration. Stuff that's "not a secret" in the sense that it's a configuration value, but absolutely is a secret in the sense that an attacker with it can do serious damage.

One Reddit thread from yesterday put it cleanly: *"The sensitive checkbox is opt-in rather than on-by-default. This has been a platform design discussion for years. This incident is the concrete case study. Does opt-in hold up for a platform at this scale, or should the default be the most restrictive setting with the escape hatch being explicit?"*

That question has now been answered empirically.

## The Inversion Problem

Every major platform eventually faces what I'd call the inversion problem: the default behavior that made the product easy to adopt becomes the behavior that creates catastrophic risk at scale.

For Vercel, frictionless deployment was always the point. You push code, it works, you don't have to think too hard about configuration. Making env vars "sensitive" required an extra deliberate step that most users — especially in early-stage projects where the habit isn't formed — skipped. That's not a failure of users. That's a failure of defaults.

Compare this to how AWS IAM handles secrets: the default posture is restrictive, and you explicitly grant permissions. It's annoying. It generates endless StackOverflow threads. And it's the right call, because the cost of granting too much by accident is asymmetrically worse than the cost of being denied when you expected access.

Vercel CEO Guillermo Rauch has confirmed that environment variables marked as "sensitive" were not accessed. The sensitive-flagged vars held. The non-sensitive ones didn't. That's not the attacker being stymied by good design — that's the attacker getting everything that wasn't explicitly protected, by design.

## What You Should Do Right Now

If you run production workloads on Vercel, the practical checklist:

**Immediate:**
- Check for the Context.ai OAuth app in your Google Workspace admin console (`110671459871-30f1spbu0hptbs60cb4vsmv79i7bbvqj.apps.googleusercontent.com` is the IOC Vercel published). Revoke it if present.
- Rotate every non-sensitive environment variable across all your projects. Not just the ones you think matter — all of them.
- **Revoke the old credentials at the upstream service**, not just replace them in Vercel. Rotation without revocation means the exposed value still works.

**Immediately after:**
- Audit which variables are currently marked sensitive. Anything that would hurt if read by an unauthorized party should be sensitive.
- Mark the rotated replacements as sensitive when you create them.
- Review deploy logs and team access for unexpected activity.

**Platform design going forward:**
- Treat "sensitive" as the default for any new variable, regardless of whether you *think* it needs to be.
- Periodic env var audits should be part of your security runbook, not a reaction to incidents.

## The Bigger Pattern

This isn't really about Vercel specifically. It's about what happens when developer experience and security defaults pull in opposite directions, and DX wins at scale.

The Context.ai Chrome extension got removed on March 27, 2026. The breach was first detected publicly around April 10 when OpenAI notified a Vercel customer about a leaked API key. Vercel disclosed on April 19. That's a roughly six-week window between removal and disclosure — and a year-plus of attacker access before that.

[OX Security, who did the MCP vulnerability research published last week](https://www.ox.security/blog/vercel-context-ai-supply-chain-attack-breachforums/), noted that the Vercel breach fits the same pattern as the Axios npm supply chain attack and the Trivy backdoor: AI-based systems are being shipped faster than their security review capabilities. Breaches propagate through chains of trust, OAuth connections, and overly permissive app installations that organizations didn't even know existed.

The "sophisticated" label that Vercel applied to the attacker is doing a lot of work here. Yes, the operational velocity was impressive. Yes, the understanding of Vercel's systems was detailed. But the attacker didn't need zero-days or nation-state tools. They needed a compromised employee at a small AI startup, an OAuth token with excessive permissions, and a target where the default configuration made it easy to enumerate valuable data.

That's not sophisticated. That's just patient.

---

*Primary sources: [Vercel security bulletin](https://vercel.com/kb/bulletin/vercel-april-2026-security-incident) · [Context.ai security update](https://context.ai/security-update) · [TechCrunch report](https://techcrunch.com/2026/04/20/app-host-vercel-confirms-security-incident-says-customer-data-was-stolen-via-breach-at-context-ai/) · [SecurityWeek deep-dive](https://www.securityweek.com/next-js-creator-vercel-hacked/) · [Trend Micro attack timeline](https://www.trendmicro.com/en_us/research/26/d/vercel-breach-oauth-supply-chain.html) · [Hudson Rock infostealer analysis](https://www.infostealers.com/article/breaking-vercel-breach-linked-to-infostealer-infection-at-context-ai/) · [OX Security supply chain context](https://www.ox.security/blog/vercel-context-ai-supply-chain-attack-breachforums/) · [HN discussion](https://news.ycombinator.com/item?id=47824463)*
