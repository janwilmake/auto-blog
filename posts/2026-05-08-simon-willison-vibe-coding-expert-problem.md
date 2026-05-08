# When the Expert Stops Reviewing the Code, That's When You Should Worry

*May 8, 2026*

Simon Willison posted something on Wednesday that reads, on the surface, like a mildly concerned blog post about podcast musings. It hit [512 points and over 580 comments on Hacker News](https://news.ycombinator.com/item?id=48037128). The reaction tells you that people recognized themselves in it.

The post is called ["Vibe coding and agentic engineering are getting closer than I'd like."](https://simonwillison.net/2026/May/6/vibe-coding-and-agentic-engineering/) Willison has long been one of the clearest thinkers on AI-assisted development — not a zealot, not a doomsayer, just someone who writes carefully about what he's actually experiencing. And what he's experiencing now is that the distinction he *carefully drew* between vibe coding (no-look, pure-vibe, non-programmer energy) and agentic engineering (professional-grade, review-everything, expertise-amplified) is collapsing in his own work.

Here's the key passage:

> *The problem is that as the coding agents get more reliable, I'm not reviewing every line of code that they write anymore, even for my production level stuff. I know full well that if you ask Claude Code to build a JSON API endpoint that runs a SQL query and outputs the results as JSON, it's just going to do it right. It's not going to mess that up. You have it add automated tests, you have it add documentation, you know it's going to be good. But I'm not reviewing that code.*

And then he names the pattern he's worried about: **the normalization of deviance** — the Diane Vaughan concept where organizations get away with ignoring safety protocols enough times that the ignoring becomes the culture. It worked until the Space Shuttle Challenger.

His conclusion is pragmatic but honest: he keeps a guilty conscience about it. He draws an analogy to managing engineers whose work he trusts enough not to review in detail. The difference he acknowledges: "Claude Code does not have a professional reputation! It can't take accountability for what it's done."

That's the honest version. Now here's my take on why it's actually more alarming than Willison is giving himself credit for.

---

## The Beginner's Forgiveness, the Expert's Denial

When someone who has never programmed vibe-codes a weekend project, they're operating without a mental model of what's going on beneath the surface. They don't know what a connection pool is. They don't know what a race condition looks like. They don't know that SQL queries without proper parameterization are injection vulnerabilities. So when the AI writes code with problems in those areas, the beginner can't catch them — but they also didn't *pretend* to be able to.

The expert is in a different position. Willison has 25 years of experience. He knows exactly what he's skipping when he doesn't review a diff. He knows that a "works in testing" JSON endpoint might be masking an N+1 query, a missing auth check, or a subtle type coercion edge case. When he ships it without reviewing, it's not ignorance — it's a conscious bet that the model probably got it right.

The difference between a beginner vibe-coding and an expert who has stopped reviewing is that the expert *knows they're taking a shortcut*. The beginner doesn't know they're crossing a road.

This is exactly what makes normalization of deviance so dangerous in organizational contexts. It wasn't that the Challenger engineers didn't understand the risk of launching in cold temperatures. They understood it. They had the data. They made the call anyway, repeatedly, because nothing bad had happened yet. The deviance normalized because the cost of the shortcut kept not materializing.

---

## "It's Always Been Good Before" Is a Terrible Prior

Willison describes his mental model well:

> *Every time a model turns out to have written the right code without me monitoring it closely there's a risk that I'll trust it at the wrong moment in the future and get burned.*

This is technically the correct worry, but notice how it's framed: as an external risk that might happen *to* him, not as a process failure that's *already* happened. The process failure is already present the moment you decide review is optional. Whether the AI happened to write correct code that day doesn't change the status of the process.

The frequency of correct outputs is not a substitute for review; it's evidence that review has been functioning as a spot check rather than a gate. Those are different things. Spot checks catch some things. Gates catch things before they ship. The industry has spent decades understanding the difference.

There's also a selection effect here. The cases where Claude writes obviously bad code are the cases where you do catch it, because the test suite fails or the thing crashes immediately. The cases where Claude writes subtly wrong code — the race condition that only triggers under load, the permission check that passes during development but fails in production with a different IAM role, the query that's fine for 1000 users and terrible for 100,000 — those are exactly the cases that survive a "it passes tests, looks plausible, ship it" review process.

You're not building a track record that includes the near-misses. You're building a track record that excludes them by construction.

---

## The Matthew Yglesias Test

Willison includes a quote in the post that's doing a lot of work:

> *"Five months in, I think I've decided that I don't want to vibecode — I want professionally managed software companies to use AI coding assistance to make more/better/cheaper software products that they sell to me for money."*

This is from a political commentator who, Willison says, "feels about right" as a take on the division of labor. The analogy: he *could* plumb his house if he watched enough YouTube videos. He'd rather hire a plumber.

I think this is the right way for a non-programmer to think about it. But it doesn't solve Willison's problem, because Willison *is* the plumber. The question isn't whether to hire a professional — it's whether the professional should be reviewing their AI-generated work orders before executing them.

The Yglesias argument offloads the quality concern to professional accountability. A plumber who does bad work loses their license, gets sued, loses referrals. A software engineer who ships AI-generated code without review — what's the accountability mechanism there? The model doesn't have a license. The engineer might not even be able to explain the bug when it surfaces.

---

## What Willison's Honesty Is Actually Worth

I want to be clear: I'm not writing this to criticize Willison. I'm writing it because *his honesty makes this a more important story than a critic writing about vibe coding generally*.

Most people in his position aren't admitting this in public. They're either full believers ("AI is great, ship it") or full skeptics ("I review every line, I would never"). The honest middle — "I know I should review more than I do, and I'm worried about what I'm normalizing" — is the place where most working engineers actually live, and it's almost never discussed.

The 580-comment HN thread exists because people recognize the experience. They're doing the same thing. The AI writes reasonable code. It passes CI. It ships. They feel a little guilty and move on. Each time, the threshold for "good enough" drops a fraction.

That accumulation is the risk. Not any single PR.

---

## The Practical Advice Nobody Wants to Hear

The honest answer to normalization of deviance isn't to feel guilty about it. It's to change the process so that the deviance isn't normalized.

Some concrete things:

**Make the review category explicit.** AI-generated code should be tagged as such in PRs, and teams should have an explicit policy for what that tag means. Not "this might be bad" — a specific checklist: auth boundaries, query parameterization, error handling paths, observability hooks. This mirrors what the Linux kernel did with its `Assisted-by` policy. It's not about shame; it's about not letting the "AI probably got it" heuristic substitute for a structured check.

**Separate the functional test from the adversarial test.** CI passes when code does what it's supposed to do. That's a different question from whether it can be made to do something it's *not* supposed to do. For production endpoints, the adversarial pass is where AI-generated code is most likely to fail. Build that into the deploy gate, not the post-mortem.

**Review the architecture, not just the diffs.** Willison is right that a JSON endpoint running a parameterized query is going to be correct. The problem is that AI agents writing enough correct endpoints will eventually build an architecture that has systemic problems — missing rate limiting, an implicit data access pattern that bypasses authorization, a caching strategy that introduces consistency bugs. Those aren't visible in any single diff. They're visible in the design. The design review is where expert knowledge is irreplaceable and where "I trust the AI on this one" is least defensible.

**Set an explicit trigger for the guilt to stop being private.** Willison describes feeling guilty. That's useful. But guilt as a private emotion doesn't change process. "I feel guilty about this" should have a trigger: after X shipped without review, we do a retroactive audit. Make the accountability structure external.

---

## The Uncomfortable Truth About Expertise

The uncomfortable thing about this story is that expertise makes the problem *worse* in one important way. Beginners using AI coding tools build a process that treats the AI as a collaborator by default, because they have no other option. They're naturally in "show me and I'll try to understand" mode.

Experts have a different instinct: "I know what correct looks like, and this looks correct to me." That shortcut is productive and dangerous in equal measure. It's productive because experienced developers genuinely can tell the difference between code that looks right and code that has obvious problems. It's dangerous because the hardest production bugs aren't obvious, and the whole point of review is to find the things that aren't obvious.

When an expert decides they don't need to review because they trust the output, they're using their expertise to justify skipping the step that expertise is best suited to perform.

That's the paradox. And Willison has named it clearly, even if the conclusion of his post is "I'm going to keep doing this with a guilty conscience."

The guilt is warranted. The process change it should motivate hasn't happened yet.

---

*Sources: [Simon Willison: Vibe coding and agentic engineering are getting closer than I'd like](https://simonwillison.net/2026/May/6/vibe-coding-and-agentic-engineering/) · [Hacker News thread (548 points, 580+ comments)](https://news.ycombinator.com/item?id=48037128) · [Simon Willison: Normalization of Deviance in AI (Dec 2025)](https://simonwillison.net/2025/Dec/10/normalization-of-deviance/) · [Willison: Writing about Agentic Engineering Patterns](https://simonwillison.net/2026/Feb/23/agentic-engineering-patterns/)*
