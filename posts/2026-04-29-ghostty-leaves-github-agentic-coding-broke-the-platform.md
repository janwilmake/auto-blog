# GitHub User #1299 Is Leaving. The Reason Is Bigger Than Mitchell Hashimoto.

*April 29, 2026*

Mitchell Hashimoto — GitHub user number 1299, joined February 2008, co-founder of HashiCorp, creator of Vagrant, Terraform, Vault, and Ghostty — [published a post yesterday](https://mitchellh.com/writing/ghostty-leaving-github) saying his terminal emulator project is leaving GitHub. He says he cried writing it. Tears on the keyboard.

That's the headline. Here's the actual story.

## The symptoms Mitchell listed are not random

Hashimoto broke down exactly what's been happening to the Ghostty project in the HN thread. Not vague frustration. A numbered list:

1. GitHub Actions failing mid-run, producing partial CI results
2. PR review tools going dark during review windows
3. Issue tracker returning empty pages or 500s
4. Merge parties across maintainers in China, the US, and EU — if GitHub is down during their shared time slice, they lose the entire 24-hour window
5. An order-of-magnitude more issue comments, all affected by the above
6. CI flakiness that stalls branches maintained by contributors who can't run tests locally (Ghostty has platform-specific test coverage)
7. Multiple days in a 30-day window where Git *push* itself failed

The Ghostty maintainer channel, he says, has become "a stream of 'oh GH is down again.'"

This is not the GitHub of five years ago. Something has changed.

## GitHub told you exactly what changed

The same day Mitchell posted, GitHub published [their own mea culpa](https://github.blog/news-insights/company-news/an-update-on-github-availability/). It's an unusually candid document. A few highlights:

- April 23: A merge queue incident where affected repositories ended up with **incorrect default branch state**. Not performance degradation — *wrong state*. They couldn't repair every repo automatically.
- April 27: An Elasticsearch cluster powering search, pull requests, issues, and projects got **overloaded by a botnet attack** and stopped returning results. Parts of the UI showed nothing.
- Underlying cause threading through both: moving off MySQL for webhooks, redesigning session caching, redoing auth flows, pulling performance-sensitive code out of the Ruby monolith into Go — all of this modernization work happening *under load*.

The footnote in Mitchell's post: the timing of his announcement is coincidental with the April 27 outage. He wrote the piece a week earlier, and the decision to leave was finalized this week. So his breaking point wasn't the latest incident — it was the *pattern*.

## What's actually hammering GitHub's infrastructure

GitHub is not getting attacked by GitHub users. It's getting attacked by GitHub *agents*.

The agentic coding boom that exploded in late 2025 has a specific infrastructure signature: it creates massive amounts of pull request churn. Agents open dozens of PRs. They iterate. They close. They reopen. They comment. They trigger CI runs on every push. Merge queues — designed for human-paced development — are being hit with machine-paced traffic.

GitHub's own blog hints at this: "reducing unnecessary work, improving caching, isolating critical services, removing single points of failure, and moving performance-sensitive paths into systems designed for these workloads." The phrase "designed for these workloads" is doing a lot of work there. Because the current workload is not what GitHub was designed for.

Back in February, Hashimoto told the Pragmatic Engineer podcast that Git and GitHub "may not survive the agentic era in their current form." He said agents cause so much branch churn that repos balloon, merge queues become untenable, and the whole model strains. He compared what's needed to "Gmail's moment for email" — a fundamental rethink of the interface. He wasn't being provocative. He was describing what he was already experiencing with Ghostty's own contributors using agent-assisted workflows.

## Why the Ghostty departure resonates so hard

Ghostty is a popular, high-quality open-source project. It's not a fringe hobby repo. But it's also not a commercial product with GitHub Enterprise support and an account manager. It's exactly the kind of project that made GitHub GitHub — a serious open-source effort, multiple maintainers across timezones, community-driven, dependent on all the collaboration infrastructure: Actions, Issues, PRs, discussions.

And it's these projects — not the enterprises paying for premium SLAs — that are getting hammered. Enterprise customers get priority support. FOSS maintainers get the status page.

The Ghostty maintainer experience is probably not unique. It's just the one being named.

## The uncomfortable question for GitHub

GitHub's blog post frames this as a modernization story: they're fixing it, Azure migration is helping, they're pulling things apart, reducing blast radius. That's probably true. But it raises a question they don't ask out loud: *Why did it get this bad?*

GitHub has been under Microsoft ownership since 2018. Copilot launched in 2021. Copilot Workspace launched in 2024. GitHub is one of the most strategically important properties Microsoft owns in the AI era. It sits at the intersection of every developer's workflow and Microsoft's AI revenue ambitions.

Yet somehow, a botnet was able to overload an Elasticsearch cluster that powered pull request functionality. A merge queue bug corrupted default branch state across production repositories. A project's maintainers have had multiple days in a single month where `git push` failed.

One HN commenter made a sharp observation: "GitHub needs ads. If option A is downtime, ads are more favorable." That's dark humor, but there's a real cost accounting problem underneath it. GitHub has 100+ million users. Most of them free. Most of the infrastructure load is coming from them and their agents. The economics of "free hosting for all open source, always" are being stress-tested in real time.

## What's actually going to happen

Mitchell says he has a plan, is in discussions with multiple providers (commercial and FOSS), and will share details in coming months. A read-only mirror will stay on GitHub.

He's almost certainly not going to GitLab or Bitbucket. The leading candidate is something Forgejo/Gitea-based, possibly self-hosted or on Codeberg, with Actions replaced by some combination of Buildkite, Sourcehut CI, or a custom setup. That migration will take months and won't be smooth.

Here's the honest assessment: GitHub remains the default for most projects because the network effects are enormous. Switching means losing discoverability, losing the contributor funnel, losing integration with every tool that assumes GitHub. Most projects that say they're leaving don't, or don't fully.

But Ghostty will. Because Hashimoto has been through this before — he built HashiCorp on a culture of doing things properly even when it's harder, and Ghostty reflects that same sensibility. When he says there's a plan, there's a plan.

The more important question isn't where Ghostty goes. It's whether GitHub fixes the underlying problem before the next wave of agentic coding makes things worse. Right now, GitHub is both the platform enabling the agent revolution and one of its most visible casualties.

That's not a sustainable position.

**Primary sources:**
- [Ghostty is leaving GitHub — Mitchell Hashimoto](https://mitchellh.com/writing/ghostty-leaving-github)
- [An update on GitHub availability — GitHub Blog](https://github.blog/news-insights/company-news/an-update-on-github-availability/)
- [Ghostty HN thread](https://news.ycombinator.com/item?id=47939579)
