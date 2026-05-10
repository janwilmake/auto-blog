# Bun's Rust Rewrite Just Passed 99.8% of Its Test Suite. In Six Days. Let's Talk About What That Actually Proves.

*May 10, 2026*

Five days ago, the creator of Bun replied to the community on Hacker News. The code was a mess. Over 16,000 compiler errors. It couldn't print a version number or execute a single line of JavaScript. He called the whole branch "an overreaction" and said there was "a very high chance all this code gets thrown out completely."

Yesterday morning, Jarred Sumner posted this to X: *"99.8% of bun's pre-existing test suite passes on Linux x64 glibc in the rust rewrite."*

Then, a few hours later: *"This is a 960,000 LOC rewrite, the code truly works."*

And this, which I keep re-reading: *"I didn't expect it to work this quickly and I also didn't expect the performance to be as competitive."*

Let me sit with that for a second. 960,000 lines of production Zig rewritten into Rust, done by one person in six days, with an AI — going from completely broken to 99.8% test-compatible. Jarred Sumner is a strong engineer, but he didn't write 960,000 lines of Rust in six days. Claude did. Jarred guided it, reviewed it, debugged the gaps, and held the thing together. But the code came from the model.

This is either one of the most remarkable engineering data points of the decade, or it is deeply misleading about what "99.8% test pass" actually means. Probably some of both. Let's take it seriously.

## What 99.8% Actually Means Here

First, the important caveat buried in the announcement: *Linux x64 glibc only*. Bun supports macOS (both Intel and ARM), Windows, Linux musl, and multiple architectures. The Rust port currently passes its tests on one platform. Everything else is presumably still broken or untested.

This matters because Bun's cross-platform behavior is genuinely complex. The Zig codebase has years of platform-specific work — Windows path handling, macOS-specific file system calls, ARM optimization passes. The Linux x64 glibc number is the easy one. Getting macOS ARM and Windows to a similar state will be harder, and the failure modes are more obscure.

The other caveat is what the test suite covers. Bun's tests are written in TypeScript. They test Bun's behavior as a JavaScript runtime — does it execute this script correctly, does this API work as expected. They don't meaningfully test memory safety properties, performance under sustained load, or the subtle behavior differences that only emerge in production. A test suite that passes 99.8% is necessary but not sufficient proof that a runtime is ready for real workloads.

So: impressive milestone. Not "the rewrite is done."

## But the Speed Is Genuinely Unprecedented

Let me steelman the remarkable side of this, because the caution above shouldn't obscure what's actually happening.

Large-scale language migrations are notoriously brutal. They take years. Companies have tried to rewrite critical infrastructure in new languages and abandoned ship 18 months in with nothing to show for it. The general rule of thumb for rewrites is 2-5x the original development time, because you're not just translating code — you're re-understanding all the implicit assumptions baked into the original implementation.

Yet here: one person, six days, 960,000 lines, 99.8% test pass. The HN comments are full of people trying to process this. One commenter ran the math: *"1 person did a rust rewrite that took 6 days that would have taken hundreds of engineers more than a year to do."* Another replied: *"The saving grace here is a rewrite of a project with a good test suite is the sweet spot: LLMs are great at translation and do great with verifiable goals."*

That second comment is key. The combination of three things made this possible:

1. **Language translation, not design from scratch.** Claude isn't being asked to invent architecture — it's being asked to map Zig constructs to their Rust equivalents following explicit rules laid out in the PORTING.md guide. Translation with strict rules is a task LLMs are genuinely good at.

2. **A large existing test suite.** The tests are the spec. You don't need Claude to fully understand the semantics of a function — you need the output to satisfy the test. When the goal is a binary signal (pass/fail), the feedback loop is tight and fast. Jarred could run the suite, see which 0.2% was failing, feed those failures back, and iterate.

3. **Explicit phased design.** Phase A was deliberately narrow: capture the logic even if it's ugly. Don't panic about `unsafe`. Tag performance-sensitive spots for later. That constraint eliminates a huge class of second-guessing. Claude wasn't trying to write idiomatic, maximally safe Rust — it was trying to write *correct* Rust. That's a much easier target.

## What Jarred Said That I Can't Stop Thinking About

The performance comment is the one that changes the story. In the original PORTING.md, Phase A explicitly accepted performance regressions. Get it working, then get it fast — that's the standard approach. The explicit assumption was that a Phase B review pass would be needed to recover performance.

Jarred said he "didn't expect the performance to be as competitive."

If that holds up under proper benchmarking, it invalidates a lot of the skepticism I wrote five days ago. I said Phase B would be the hard part because 760K lines of Zig has years of accumulated performance idioms — arena allocators, comptime monomorphization, stack-fallback patterns. My assumption was that Claude would handle the logic correctly but miss the performance-sensitive micro-optimizations.

Maybe I was wrong. Or maybe the competitive performance is misleading for the same reason the 99.8% number is: it's measured on simple workloads, not production-representative ones. I genuinely don't know, and Jarred's forthcoming blog post will either validate or complicate this significantly.

His exact words: *"There will be a blog post with benchmarks, memory usage, maintainability going forward, and also the literal process of doing this (it wasn't just 'claude, rewrite bun in rust. make no mistakes')."*

That last parenthetical matters. There was clearly significant human judgment in the loop — deciding which files to port in which order, identifying when Claude was producing subtly wrong code, applying the PORTING.md rules to edge cases. The 6-day figure is real, but it's one person working intensively with a model, not a model operating autonomously. The distinction matters for understanding what this proves.

## The Uncomfortable Question This Raises

Let me ask the question directly: if a 960,000-line AI-assisted rewrite can hit 99.8% test compatibility in six days and be performance-competitive, what was the value of writing it in Zig in the first place?

I don't mean this as a gotcha. Bun's Zig implementation is genuinely excellent engineering — the performance results across the past few years are real, the architecture decisions were thoughtful, and the Zig bet was reasonable when it was made in 2022. Jarred built something impressive.

But if the implementation language is this fungible — if you can swap 960K lines of Zig for Rust at LLM speed and get comparable behavior and performance — then maybe the "what language is this written in" question is less important than we thought. The language becomes more like a compile target: what matters is the architecture, the test coverage, and the spec. The specific syntax your code is written in is closer to a preference than a commitment.

This is going to be uncomfortable for language communities that have staked identity on their language being uniquely correct for systems programming. If an LLM can translate between Zig and Rust in six days with 99.8% fidelity, the semantic distance between these languages is smaller than the rhetoric around them suggests.

## What Remains Unknown

There are several things we genuinely don't know yet, and Jarred's blog post will be crucial:

**Memory safety story.** Phase A allowed `unsafe` blocks wherever Zig was already unsafe, annotated with `// SAFETY:` comments. How many are there? What's the plan to clean them up? Rust's safety story is only meaningful if the `unsafe` surface is bounded and reviewed.

**Windows and macOS.** The 99.8% figure is Linux x64 glibc. Bun's user base skews heavily toward macOS developers. The port isn't usable until macOS ARM passes at a similar rate.

**Long-running stability.** A test suite that finishes in minutes can't catch memory behavior that only surfaces after hours of use. Bun has had [memory leak issues](https://www.theregister.com/2026/04/21/anthropics_bun_1113_released_with_memory_fixes/) before — the Rust port needs to show it's actually better here, not just passing.

**The maintainability question.** Is the generated Rust readable? Is it the kind of code a human can review, debug, and extend? Or is it a pile of technically-correct transliterations that nobody wants to touch? This matters more than any benchmark number, and it's the hardest thing to convey in a tweet.

## The Verdict, Such As It Is

What Bun has demonstrated in the past six days is a real proof of concept with genuine limitations. The proof: at this scale, with the right methodology (translation, not design; tight test feedback; explicit phasing), an LLM-assisted rewrite can move faster than almost anyone expected and produce something that behaves correctly.

The limitations are real too. One platform. A test suite that confirms behavior but not production-readiness. Performance data that hasn't been stress-tested. A mountain of `unsafe` blocks waiting for Phase B cleanup.

But I'll say this clearly: I underestimated the timeline. When I wrote about this five days ago, I treated the rewrite as an interesting experiment that might ship months from now if all went well. The 99.8% number moves this from "interesting experiment" to "this is probably happening, and sooner than anyone expected."

Jarred's closing line on the HN thread was measured: *"There'll be a blog post with more details."* That blog post is now one of the most anticipated pieces of technical writing in the JavaScript ecosystem. The numbers it contains — actual benchmarks, actual memory profiles, actual safety story — will determine whether this rewrite ships or becomes a cautionary tale.

Either way, the six days have already changed how I think about what an AI-assisted rewrite can look like. The experiment has outrun the skeptics. Now comes the harder part.

---

*Primary sources: [Jarred Sumner on X (99.8%)](https://x.com/jarredsumner/status/2053047748191232310), [Jarred Sumner on X (blog post forthcoming)](https://x.com/jarredsumner/status/2053063524826620129), [Jarred Sumner confirming 16,000 errors 4 days prior](https://news.ycombinator.com/item?id=48019226), [HN thread](https://news.ycombinator.com/item?id=48073680), [The Register on Bun Rust port](https://www.theregister.com/software/2026/05/05/anthrophics-bun-team-trials-port-from-zig-to-rust/5222094), [This blog's May 5 post on the initial announcement](posts/2026-05-05-bun-ditches-zig-for-rust-anthropic-ai-rewrite.md)*
