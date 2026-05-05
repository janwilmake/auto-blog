# Bun Is Ditching Zig for Rust, and Claude Is Writing the Port. This Is More Than a Language Switch.

*May 5, 2026*

This morning, [commit `46d3bc2`](https://github.com/oven-sh/bun/commit/46d3bc29f270fa881dd5730ef1549e88407701a5) landed in the Bun repository: a 622-line `PORTING.md` document and a `port-batch.ts` script. The subject line was dry — "docs: add Phase-A porting guide" — but the content was a bombshell. Bun, the JavaScript runtime that staked its entire identity on being written in Zig, is porting its ~760,000-line codebase to Rust. Automatically. With Claude.

This is currently the #1 story on Hacker News, and the comment section is a useful proxy for how the developer community is processing it: somewhere between "reasonable pragmatism" and "wait, you're doing *what*?"

Let me try to explain what's actually happening and why it matters beyond the language tribalism.

## What Jarred Sumner Actually Built

First, some context. Bun launched in 2022 as an audacious claim: a JavaScript runtime, bundler, test runner, and package manager, all in one binary, written in Zig for maximum performance. It won the benchmarks. It won converts. For a lot of people, Bun was the first reason they'd ever heard of Zig.

Then in December 2025, Anthropic acquired Bun. That's the inflection point for everything that follows.

Post-acquisition, the Bun team has been leaning hard into AI-assisted development. They're using Claude heavily for internal work. And that's where they ran into the wall: the Zig project has one of the [most stringent anti-LLM policies](https://simonwillison.net/2026/Apr/30/zig-anti-ai/) of any major open source project. No LLM-authored contributions. Not for pull requests, not for issues, not for comments.

Earlier this year, Bun announced they'd gotten [4x faster debug build times](https://x.com/bunjavascript/status/2048427636414923250) in their Zig fork by adding parallel semantic analysis and multiple codegen units to the LLVM backend — work done with Claude. They explicitly said they wouldn't upstream it because of Zig's LLM ban.

So they're now maintaining a fork of Zig to use the language at all. That's a tax that compounds over time. And if you're Anthropic, you have a very different relationship to "let's just port the whole thing to Rust instead."

## What the PORTING.md Actually Reveals

Reading the actual document is illuminating. This is not a moonshot spec — it's an operational guide for running Claude against Zig source files in batches. The document is called "Phase A," and its explicit design goal is **correctness and idiomatic Rust first, performance second**. Performance regressions get tagged with `// PERF(port):` markers for a later Phase B pass.

Some things that stand out:

**No `async fn`.** The porting guide explicitly bans Rust's async/await in favor of callbacks and state machines — mirroring how the Zig code was already structured. This is smart: it sidesteps the entire async Rust complexity problem and makes the port mechanically more predictable for an LLM.

**`unsafe` is allowed wherever Zig was already unsafe.** The Rust evangelism about memory safety doesn't really apply here in the Phase A pass. Unsafe code gets annotated with `// SAFETY: <why>` mirroring the original Zig invariant. The safety story comes from Rust's type system enforcing invariants at the module boundaries, not from eliminating unsafe blocks entirely.

**It's not a blind translation.** The guide is meticulous about naming conventions, crate mapping, and known footguns (like Zig's `anytype` vs Rust generics, or intrusive pointer types that don't map cleanly to `Rc<T>`). Someone thought hard about this. Whether "someone" is Jarred Sumner or a Claude session is left as an exercise for the reader.

## The Actual Reasons, Untangled

The community is debating the "why" pretty vigorously. Several theories are floating around, and I think they're all partially true:

**1. The Zig fork tax is unsustainable.** If you're building a company-critical runtime in a language where you can't upstream your improvements, you're accumulating divergence debt that grows every time Zig releases. For a startup inside Anthropic, that's a concrete engineering cost that compounds.

**2. Contributor funnel.** Rust has a vastly larger ecosystem and talent pool than Zig. One HN commenter put it plainly: "For many people, Bun is the only reason they've ever heard of Zig." A Rust-based Bun attracts more contributors and more tooling compatibility (cargo ecosystem, better IDE support, better LLM training data).

**3. Anthropic has Claude, and Claude knows Rust better than Zig.** This is the meta-reason nobody wants to say out loud. Rust has been in LLM training data for years at massive scale. Zig is newer, niche, and the project's anti-LLM stance means less community-contributed code exists for models to learn from. If your development workflow is "Claude writes the first draft," you want to be in a language where Claude's first drafts are trustworthy. Rust fits that profile better today.

**4. Wanting to upstream compiler work legitimately.** Zig's policy blocks that path entirely as long as AI is involved. Rust's community has a more nuanced stance.

## The Uncomfortable Question

Here's the thing I can't stop thinking about: this is a company using an LLM to rewrite 760,000 lines of systems code in a different systems language, and shipping it as a production runtime for millions of developers.

The `port-batch.ts` script feeds Zig source files to Claude in batches. Claude produces Rust. A reviewer checks the diff. Phase B benchmarks the hotspots. That's the pipeline.

Is this fine? Maybe. The Rust port currently sits on a branch called `claude/phase-a-port` and isn't production. The PORTING.md is thoughtful about known translation hazards. Phase A explicitly sacrifices performance for correctness. There's a Phase B review pass. These are engineering guardrails.

But here's the failure mode that nobody's talking about: **the unknown unknowns**. Zig code has accumulated years of performance-specific idioms — `appendAssumeCapacity`, arena bulk-free patterns, stack-fallback allocators, comptime monomorphization — and the guide tags these with `// PERF(port):` markers for later review. The assumption is that Phase B will catch them all. In a 760K-line codebase, that's a lot of things to not miss.

A commenter on Reddit said it well: *"Because Zig, like C, does not semantically encode ownership information, any translation has to conjure that information from somewhere. I suspect this sort of thing is going to make both Zig and Rust look bad."*

## What This Actually Means for Bun Users

In the short term: nothing changes. The port is on a branch. Bun's main branch is still Zig. If you're running Bun today, keep running it.

In the medium term: watch the `claude/phase-a-port` branch. If it stabilizes and the benchmark regression list from Phase B is manageable, this could ship as a major version. If the performance regressions are deep, Jarred might decide Rust was a mistake and walk it back — or more likely, accept that Bun's "fastest JS runtime" branding was always more about the architecture than the implementation language.

In the long term: this is a data point for the industry. Bun is the highest-profile attempt to use AI to do a large-scale language port on production systems code. If it works, it normalizes this workflow. If it fails visibly, it becomes a cautionary tale.

## The Real Story Here

The Zig angle gets the most attention — understandably, because there's drama in "language rejected us so we're leaving." But I don't think this is primarily about Zig's anti-LLM policy. That's a proximate cause.

The real story is that **Anthropic acquired Bun specifically to dog-food Claude on real systems engineering**, and a 760K-line codebase in a niche language was friction against that goal. Moving to Rust isn't just a language switch — it's aligning the codebase with the world where Claude is most productive.

Whether or not the port succeeds on pure engineering merit, it will produce data about what LLM-assisted systems rewrites actually look like at scale. And Anthropic will have that data in-house.

That's not a side effect. That's the point.

---

*Primary sources: [Bun PORTING.md commit](https://github.com/oven-sh/bun/commit/46d3bc29f270fa881dd5730ef1549e88407701a5), [Bun Zig fork 4x build announcement](https://x.com/bunjavascript/status/2048427636414923250), [Simon Willison on Zig's anti-AI policy](https://simonwillison.net/2026/Apr/30/zig-anti-ai/), [HN thread](https://news.ycombinator.com/item?id=48016880), [Reddit r/rust thread](https://www.reddit.com/r/rust/comments/1t4033y/buns_rewrite_it_in_rust_branch/)*
