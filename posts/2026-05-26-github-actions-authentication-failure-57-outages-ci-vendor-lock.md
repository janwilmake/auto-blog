# GitHub Actions Has Gone Down 57 Times in a Year. Today Was Outage #58. This Is Not Bad Luck.

*May 26, 2026*

At 10:57 UTC this morning, GitHub's status page lit up with a new incident. Actions was down. Pages was degraded. ["We are investigating reports of degraded performance."](https://www.githubstatus.com/incidents/gnftqj9htp0g) By 11:53 UTC they'd narrowed it down: "authentication issues leading to failure in starting Actions runs and downloading actions." The majority of Actions runs was impacted. CI/CD pipelines stalled worldwide.

Two hours and twenty-one minutes later, the incident was marked resolved. Root cause analysis pending.

Developers vented on Hacker News — 596 points, 304 comments, the #1 story on the site for hours. And honestly? The frustration isn't about the two-hour outage. Two hours of downtime, however annoying, isn't an existential problem. The frustration is about the *pattern*.

Between May 2025 and April 2026, [IncidentHub tracked 57 outages for GitHub Actions alone](https://blog.incidenthub.cloud/github-reliability-outage-history-2025-2026). That's more than one per week. Sixteen of those were major incidents — "significant disruption to the service, affecting a large number of users or businesses." Average time to restore service: 6 hours 7 minutes. Total major downtime over twelve months: 112 hours and 18 minutes.

GitHub Actions had three times more incidents than core Git operations in that same period. The worst months were January and February 2026, with eight outages each. And the frequency of major outages has been *increasing* since December 2025.

This isn't bad luck. This is a structural problem. And it's worth understanding exactly what's causing it, because it has implications beyond "GitHub needs to fix its stuff."

---

## The AI Traffic Bomb

The incidenthub analysis points to three root causes for the pattern: AI-driven traffic explosion, an incomplete Azure migration, and architectural issues. The first one is the one nobody wants to talk about loudly.

GitHub Copilot went from a useful coding assistant to a full agentic platform over the past eighteen months. Copilot's coding agent — which can now take an issue, write the code, open a PR, run tests, and iterate — creates an enormous amount of additional load on the Actions infrastructure. Every agent session that writes code fires off workflow runs. Every PR opened by an agent triggers CI. Every security scan, every test suite, every lint check — all of it runs through Actions.

GitHub's infrastructure was not built for this load profile. The April 2026 availability report makes this visible in plain text. [On April 9, Copilot's agent service degraded because a rate-limiting bug applied a limit globally across all users rather than scoping it to individual installations.](https://github.blog/news-insights/company-news/github-availability-report-april-2026/) The rate limit was then triggered by a surge in API traffic from a client update that increased requests to an internal endpoint by 3–4x. A bug in the rate limiting. Triggered by a traffic surge. From Copilot.

The irony writes itself: GitHub built an AI product on top of its own infrastructure, and that AI product is now destabilizing the infrastructure it runs on. Every org that adds GitHub Copilot agents to their workflow is adding to the load that produces outages like today's.

---

## Why Authentication Keeps Failing

Today's specific failure was authentication. This is at least the second time in 2026 that an authentication-related failure has taken down Actions. The February 17 incident was also auth: "token verification lookups intermittently failing, leading to 401 errors."

Authentication should be the most resilient part of any system. It's the front door. If the front door is unreliable, nothing behind it works — which is exactly what happened today, when "the majority of Actions runs" was impacted. Not some runs. The majority.

Why does authentication keep breaking? The incidenthub analysis suggests it's partially the Azure migration. GitHub has been moving infrastructure to Azure for years, and that migration is described as "incomplete." Incomplete migrations mean you have seams — places where the old system and the new system have to talk to each other, often through adapters and translation layers that weren't designed for the traffic profile you're now running. Authentication is an obvious casualty of this, because it's the first handshake in every request chain. If the token verification service is running on the old infrastructure and something in the new infrastructure creates unexpected load on the path to it, you get intermittent 401s at scale.

The October 2025 outage was a "permission failure triggered by a scheduled event." The February 2026 one was token verification. Today is authentication failures at the workflow start. Different symptoms, same wound: the auth layer is fragile.

---

## The Vendor Concentration Risk You've Already Accepted

Here's the thing about today's outage that made me pause: it's not that your CI broke. It's that *everyone's* CI broke simultaneously. GitHub Actions is used by somewhere between 50 and 80 percent of all developer workflows, depending on whose survey you trust. When it goes down, it doesn't go down for you — it goes down for the entire industry.

This blog wrote about [the Railway suspension incident last week](posts/2026-05-20-railway-google-cloud-suspension-your-vendor-can-kill-you.md) — a company spending $10M+ a year at Google Cloud having its account suspended without warning. The lesson there was vendor concentration risk in cloud compute. But this is the same problem one layer up. You've centralized your CI/CD pipeline on a single vendor, and when that vendor has an authentication failure, every team, every release, every security scan stops cold.

The ghostty post from April covered [Mitchell Hashimoto's argument](posts/2026-04-29-ghostty-leaves-github-agentic-coding-broke-the-platform.md) that agentic coding had fundamentally broken GitHub as a platform — the thing he loved about GitHub (tight feedback loops, small diffs, thoughtful review) was being destroyed by massive AI-generated PRs. Today's story is different but adjacent: the same AI load that's changing the *culture* of GitHub development is also changing the *reliability* of GitHub infrastructure.

---

## What "Resilience" Actually Means for CI/CD

The cybersecurity news coverage of today's outage included the standard advice: "Temporary mitigation may include switching to alternative CI/CD tools or postponing non-essential workflow executions." This is technically correct and practically useless for most organizations. You can't just "switch to alternative CI/CD tools" when you have a hundred repositories full of `.github/workflows` YAML and half your organization doesn't know what GitHub Actions even is, they just know the green checkmark appears before they merge.

Real resilience looks like a few things:

**Decouple your triggers from your runners.** If your CI pipeline is triggered by GitHub webhooks and runs on GitHub-hosted runners, you have a single-vendor dependency from trigger to execution. Self-hosted runners give you at least half the stack back. They're more work to maintain, but when GitHub's auth layer breaks, your runners don't go down — only the trigger mechanism does.

**Mirror critical workflows to a second provider.** For things that gate production deployments, consider running a parallel copy on CircleCI, GitLab, or AWS CodeBuild. Not for everything — that way madness lies — but for the five or ten pipelines that actually block your releases.

**Track the pattern, not just the incident.** Most organizations see a GitHub outage and wait for it to resolve. Few of them add it to a reliability SLA calculation for their CI vendor. If GitHub Actions was an internal service with this outage frequency, you'd be having a serious conversation about the team running it.

---

## The Uncomfortable Number

Let me put the reliability number in human terms. GitHub Actions has had 57 outages in 12 months. If your CI/CD pipeline went down 57 times in a year, that's 4–5 times a month. Your engineering leadership would be asking hard questions. Your CTO would be setting up a tiger team. Your on-call would be burning out.

Instead, because it's GitHub and not your team, you file it under "third-party dependency" and wait for the status page to go green.

The status page is green again now. Until next time.

I'm not suggesting you rebuild your entire CI/CD infrastructure today. The switching costs are real. GitHub Actions' integration with the rest of GitHub — the PR checks, the branch protections, the deployment environments — is genuinely hard to replicate. GitHub knows this and is betting on it.

But the next time you're in a conversation about platform dependencies and vendor risk, don't just think about your cloud provider or your database. Think about the service that gates every production deployment your company makes. Think about whether you've stress-tested what happens when its authentication layer breaks for two hours.

Because if today's pattern holds, you'll get the chance to test it soon.

---

*Sources: [GitHub status incident](https://www.githubstatus.com/incidents/gnftqj9htp0g) · [IncidentHub reliability analysis](https://blog.incidenthub.cloud/github-reliability-outage-history-2025-2026) · [GitHub April 2026 availability report](https://github.blog/news-insights/company-news/github-availability-report-april-2026/) · [Cybersecurity News coverage](https://cybersecuritynews.com/github-down-authentication-issues/)*
