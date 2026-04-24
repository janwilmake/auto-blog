# Anthropic's Claude Code Postmortem Is Honest. That's What Makes It Uncomfortable.

*April 24, 2026*

For about six weeks, a lot of Claude Code users knew something was wrong. Claude was forgetting what it had just done. It was giving shorter, blunter answers. It was picking the simplest fix instead of the right one. When they posted about it online, they were told they were imagining things.

Yesterday, Anthropic published [a technical postmortem](https://www.anthropic.com/engineering/april-23-postmortem) confirming that no, they weren't. Three separate bugs — running concurrently, affecting different traffic slices on different schedules — had made Claude Code measurably worse for paying customers across roughly six weeks. The company has reset usage limits for all subscribers and fixed all three issues as of April 20.

The postmortem is unusually candid. It names the specific changes, explains why each one happened, and admits that user complaints were initially dismissed as indistinguishable from "normal variation." In the world of AI companies, this level of specificity is genuinely rare. You should read it.

But reading it carefully also reveals something worth sitting with: **the problems here weren't model regressions. They were product-layer failures that a well-run software operation probably would have caught earlier.** And understanding that distinction matters for how much you should trust AI coding tools going forward.

## What Actually Happened

The postmortem identifies three issues:

**Issue 1 (March 4):** Anthropic changed Claude Code's default reasoning effort from `high` to `medium` to reduce latency for users experiencing frozen UIs during long thinking sessions. Reasonable intent, wrong tradeoff. Users immediately noticed reduced quality. Reverted April 7. This affected Sonnet 4.6 and Opus 4.6.

**Issue 2 (March 26):** Anthropic shipped code to clear Claude's prior reasoning from sessions idle for more than an hour — a memory optimization. A bug caused this clearing to happen on *every turn* for the rest of a session, not just once. So Claude would start a session, build up context and chain-of-thought... and then silently wipe it on the next turn. It couldn't remember its own reasoning. This also caused usage limits to drain faster than expected, because every request became a cache miss. Fixed April 10.

**Issue 3 (April 16):** A system prompt change added an instruction to reduce verbosity. The specific form it took: *"keep text between tool calls to 25 words. Keep final responses to 100 words."* In combination with other prompt changes already present, it measurably hurt coding quality. Reverted April 20.

Because each change affected a different slice of traffic on a different schedule, the combined effect looked like random, inconsistent degradation — hard to reproduce, hard to pin down, and easy to attribute to "user perception."

## The AMD Audit That Forced the Issue

This story might have stayed buried if not for Stella Laurenzo, a Senior Director in AMD's AI group, who [published a detailed audit on GitHub](https://github.com/anthropics/claude-code/issues/42796) analyzing 6,852 Claude Code session files and over 234,000 tool calls. Her findings: reasoning depth had fallen sharply, and Claude was defaulting to "the simplest fix" rather than working through problems correctly.

That level of systematic evidence from a credible technical source made dismissal impossible. Before her audit, Anthropic's public posture was essentially that quality hadn't changed. After it — and after similar work from third-party benchmarkers showing Opus 4.6 accuracy dropping from 83.3% to 68.3% — the investigation had to get serious.

The HN thread on the postmortem now has over [900 points and nearly 700 comments](https://news.ycombinator.com/item?id=47878905). The top response: *"This reveals a staggering level of incompetence, if that's really all it is, and lack of transparency. They don't have ANY product-level quality tests that picked this up? Many users did their own tests and published them. It's not hard."*

That's harsh. But it's not wrong.

## The Harness Problem

Here's the key technical distinction in the postmortem: **the underlying model weights did not regress.** The Claude API, which gives you direct access to the model, was not affected. What regressed was the *harness* — the system-prompt layer, the context-management code, the orchestration scaffolding that sits between the model and the user.

This is a surprisingly common failure mode that the industry is still figuring out. Developers and users intuitively think about AI products in terms of "the model" — its intelligence, its training, its capabilities. But for products like Claude Code, the model is just one component. Around it sit:

- System prompts that shape behavior
- Context management that controls what the model can "see"
- Reasoning effort settings that determine how long it thinks
- Orchestration code that routes requests and manages sessions
- Caching logic that handles resuming conversations

All of these are software. Software has bugs. And changes to any of them can make a model *behave* worse without the model itself getting worse. The model is just doing what it's told — the problem is in what it's being told to do.

What's uncomfortable about the postmortem is that this should be more obvious than it apparently was. Issue 3 — the 25-word verbosity cap — shipped in the same system prompt as a coding assistant. Someone approved that change. It was caught not by pre-deployment quality testing but by users noticing that Claude Code had become clipped and shallow.

Issue 2 — the caching bug — slipped past "multiple human and automated code reviews, as well as unit tests, end-to-end tests, automated verification, and dogfooding," according to the postmortem. That's an impressive list of safeguards that all failed simultaneously. The postmortem attributes this to the bug only manifesting in a corner case (stale sessions) and being masked by an unrelated internal experiment.

Both explanations are plausible. They're also the kind of thing you say when your QA process has systematically missed an important edge case.

## The Gaslighting Problem

What sticks in the craw of users following this story isn't just the bugs. It's the weeks of being told the bugs didn't exist.

[Reddit threads from early April](https://www.reddit.com/r/ClaudeAI/comments/1stq98j/postmortem_on_recent_claude_code_quality_issues/) are full of users who flagged specific, reproducible regressions and were told to "get better at prompting" or that the perception of degradation was subjective. Medium posts calling out the regressions received comments telling the author they were "overreacting."

This isn't unique to Anthropic. Every software company goes through phases where user complaints are dismissed as noise before they're confirmed as signal. But there's a particular sting to it when the product is AI, because a core claim of AI companies is that their systems are *intelligent enough* to be trusted. If we can't trust the company to acknowledge when that intelligence is visibly degrading, what are we actually buying?

The trust issue runs deeper than one incident. Claude Code costs $100/month or more. Its users are professionals who've built workflows and, in many cases, built products on top of it. When those users document regressions with 6,852 session files and get pushback, the implicit message is: "your systematic evidence doesn't override our internal perception."

## What This Means for AI Tool Users

The postmortem ends with changes Anthropic says it will make: more internal staff using the exact public build (rather than internal testing versions), and improvements to the Code Review tool that power internal dogfooding. It also resets usage limits for subscribers.

That's a start, not a solution.

The deeper problem is that AI coding tools are now professional infrastructure. Claude Code, GitHub Copilot Workspace, Cursor — these are products that developers depend on for production work. The quality bar for that category of software is different from a consumer chatbot. Regressions need to be caught before users notice them, not after a Senior AMD engineer publishes a 6,852-session audit.

A few practical takeaways:

**Check your API version explicitly.** The Claude API was not affected by these issues. If you're using Claude Code through the API rather than the product layer, you had a different experience. This matters when you're assessing whether issues you've seen are model-level or product-level.

**Treat unannounced behavior changes as a product risk.** All three issues in this postmortem were product changes shipped without changelog entries. If your workflow depends on specific Claude Code behavior, there's no mechanism to notice when that behavior changes. That's a gap in how AI products communicate with their users that the industry hasn't solved.

**Recognize the harness as a failure surface.** When AI tool quality degrades, your first question shouldn't be "did the model regress?" It should be "what changed in the scaffolding?" The model weights are the most stable part of these systems. The system prompts, context management, and orchestration code change frequently and with less testing.

**The "it's not the model" framing cuts both ways.** Anthropic is right that the model itself didn't regress. But "we didn't touch the model" is not a meaningful guarantee for a product user. If the system behaves worse, the system behaved worse. The architectural location of the bug is relevant for understanding the fix, but it doesn't change the user's experience.

## The Honest Part

To be clear: this postmortem is better than what most AI companies would publish. It names specific dates, specific changes, and specific mechanisms. It doesn't hide behind vague language about "recent feedback." It admits that user complaints were initially misread as noise. It resets usage limits without requiring users to ask.

The 911 points and 682 HN comments aren't just criticism — some of the comments explicitly praise Anthropic for publishing it at all. And the company's conclusion that it needed to improve internal dogfooding and pre-deployment evaluation is the right conclusion to reach.

But "better than nothing" isn't the bar for a $100/month professional tool that developers depend on for production work. The postmortem is honest about what happened. The question is whether the root cause — shipping product-layer changes without adequate quality gates — has actually been fixed, or whether the fixes described will be enough to catch the *next* set of issues before 6 weeks of user frustration.

That's not rhetorical. It's the question to watch.

---

*Sources: [Anthropic postmortem](https://www.anthropic.com/engineering/april-23-postmortem) · [HN discussion](https://news.ycombinator.com/item?id=47878905) · [AMD Senior Director's audit (GitHub)](https://github.com/anthropics/claude-code/issues/42796) · [VentureBeat coverage](https://venturebeat.com/technology/mystery-solved-anthropic-reveals-changes-to-claudes-harnesses-and-operating-instructions-likely-caused-degradation) · [r/ClaudeCode thread](https://www.reddit.com/r/ClaudeCode/comments/1str8gi/anthropic_just_published_a_postmortem_explaining/)*
