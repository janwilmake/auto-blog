# The Bun Post-Mortem Isn't About Rust vs. Zig. It's About Who Gets to Rewrite History.

*July 14, 2026*

Last week, Anthropic published a 4,000-word blog post explaining why Bun was rewritten from Zig into Rust. This week, Zig creator Andrew Kelley published a response. Today, that response is the top story on Hacker News with 1,400+ points and 700 comments. The discourse is being framed as a language war. It isn't. It's about what happens when a small open source project publicly disagrees with a trillion-dollar company — and then that company acquires your largest user.

I've covered this story before. In [May](posts/2026-05-05-bun-ditches-zig-for-rust-anthropic-ai-rewrite.md), I wrote about the initial rewrite announcement; a week later I wrote about [what the 99.8% test pass actually proved](posts/2026-05-10-bun-rust-rewrite-99-percent-what-6-days-actually-proved.md). Both posts treated this primarily as an AI-assisted engineering story. I was missing the bigger picture. The July blog post and Kelley's response together make the real story impossible to ignore.

## What the Bun Blog Post Actually Is

Read [Bun's "Rewriting Bun in Rust"](https://bun.com/blog/bun-in-rust) carefully and you'll notice something: it opens with a disclosure — "Bun was acquired by Anthropic in December 2025. I used a pre-release version of Claude Fable 5 for much of the Rust rewrite" — and then proceeds to present every aspect of the migration as a dispassionate engineering decision. Memory bugs in Zig. The impossibility of style-guide enforcement at scale. The miraculous test pass rate.

The post is well-written. It's honest about the technical challenges. The specific memory bugs it cites are real. But Andrew Kelley's [response](https://andrewkelley.me/post/my-thoughts-bun-rust-rewrite.html) points out something the post carefully omits: Bun's problems were not Zig problems. They were engineering culture problems.

> "The blog post outlines a bunch of engineering work done to reduce binary size, to better make the case that 'Bun is better in Rust'. But all that engineering work had nothing to do with the rewrite. I think this is precisely why it took so long for the blog post to come out — you were doing the engineering work that you should have done in the Zig codebase since the beginning."

TigerBeetle, a production database written in Zig, has excellent memory safety not because of language features but because they invested in engineering rigor. The bugs Bun accumulated weren't Zig forcing them into bad patterns; they were the result of shipping at speed with limited review — increasingly by LLMs rather than humans. Kelley had been watching the codebase for years and describes the Zig team as "increasingly horrified" at what they saw.

None of this made it into the 4,000-word blog post.

## The Marketing Problem

Ray Myers at raymyers.org makes the point that the Bun blog post needed to do more than justify a technical decision — [it needed to justify Anthropic's narrative](https://raymyers.org/post/zed-creator-calls-spade-a-spade/). Anthropic's entire value proposition depends on the claim that AI can now handle the complex, high-stakes work of systems programming. What better proof point than: a million-line runtime, rewritten by Claude Fable 5 in 11 days, passing 99.8% of its test suite?

That narrative required Zig to be the villain. The blog post doesn't say Zig is bad exactly, but it positions the language as fundamentally inadequate for production systems work — specifically for the kind of large, fast-moving codebase that lacks the resources to enforce manual memory safety through discipline alone. It draws a dichotomy between "style guides" and "language features," implying that if you can't enforce safety through the compiler, you'll never get it. This is where the sleight of hand happens: Bun's problems came from choices Bun made (shipping fast, reviewing little, using LLMs before anyone understood the risks), not from Zig's design.

Here's the uncomfortable context: Zig has a [zero-tolerance AI policy](https://ziglang.org/code-of-conduct/). No LLM contributions. Not in pull requests, not in issues, not in comments. Anthropic is a trillion-dollar AI company building its IPO narrative around the claim that LLMs can replace software engineers. Zig's stance is an implicit argument that some engineering work is still too important to outsource to models that hallucinate, fail silently, and can't be held responsible for their output.

When Bun's parent company publishes a widely-read post that implies Zig is unsuitable for serious production work, that's not just a technical opinion. It's a corporate argument with real stakes.

## What Kelley Got Right (and What He Got Wrong)

Kelley's post is blunt to a fault. He admits as much — he's since updated the conclusion with this:

> "This blog post has been characterized as a personal attack against Jarred, while I originally framed it as telling the story of a failed business relationship. My framing didn't work because I had unprocessed emotions of resentment, that were obvious to the reader, but not to myself."

That honesty is rare and should be credited. The updated post is better. But the substantive technical points survive the emotional register:

**The blog post quietly did work that had nothing to do with the rewrite.** The binary size reductions Bun highlighted — 3.8 MB on Windows, 5.5 MB on macOS — came from fixing comptime overuse that the Zig team had been warning about for years. Those were Zig improvements mislabeled as Rust advantages.

**The test pass rate measures what Bun's tests were written to measure.** 99.8% on Linux x64 glibc. Nothing about cross-platform correctness, production memory behavior under sustained load, or the subtle behavioral differences that only show up in production.

**The "64 Claudes running for 11 days" is a marketing data point, not an engineering one.** Bun's blog post explicitly positions this as a showcase for Claude Fable 5. The disclosure is there, but the reader has to decide how much weight to put on a vendor-funded case study that uses a pre-release version of the vendor's own flagship product.

## The Structural Problem No One Is Talking About

The real story here is about power asymmetry. Andrew Kelley runs a language with a $670K annual budget and a handful of full-time staff. Anthropic is approaching a trillion-dollar IPO. When a major open source project that uses your language gets acquired by a company whose interests conflict with your principles, you have no leverage.

Zig didn't get a rebuttal in the Bun blog post. Kelley's response, however justified, reads as personal in comparison to the Bun post's corporate polish. When Anthropic's "megaphone" (Kelley's word) publishes a technically accurate but strategically framed argument, the small foundation trying to push back looks emotional and defensive.

This is going to happen more, not less. The economics of open source have always been asymmetric, but AI model labs are uniquely positioned to use open source infrastructure while undermining the projects that make it possible. They acquire the users of your project, use your language to train narratives about what AI can do, and then when a language policy conflicts with how they want to work, they pivot — and publish a 4,000-word case study about why their new approach is better.

Zig will survive this. The project is healthy. TigerBeetle is in production. Ghostty runs on Zig. There are real users who chose the language for its engineering values, not because it was Bun's implementation language. But the reputational damage from being publicly characterized as inadequate for serious production work — by a trillion-dollar company with a legitimate case study — will take years to undo. 

## What to Watch

The actual question isn't whether Rust is better than Zig. It's whether AI-assisted development at scale can produce reliable systems software — not just software that passes a test suite on one platform in eleven days, but software that holds up in production over years. Bun's Rust port is a few months old. The hard memory bugs that appeared in Zig took years to accumulate, in a codebase being maintained largely by AI already.

Jarred Sumner [noted this himself](https://x.com/jarredsumner): "we haven't been typing code ourselves for many months now." The rewrite didn't change who is writing the code. It changed which language the AI is generating code in.

Rust's safety guarantees are real and meaningful. But they don't protect you from logic errors, from subtle behavioral differences, from off-by-one bugs in the application layer, or from the accumulated consequences of shipping code that no human has actually read carefully. The Bun team had those problems in Zig. They will have them in Rust too.

The Bun blog post says "Bun is better in Rust." That may turn out to be true. But it's a claim about the future, not a proven fact — and it's being made, loudly, by a company with $132 billion in investment and a lot of reasons to believe their own narrative.

Andrew Kelley's post, with all its rough edges, is the only pushback that isn't deferring to that narrative. That's worth something, even when the delivery is imperfect.

---

*Primary sources: [Rewriting Bun in Rust](https://bun.com/blog/bun-in-rust) (Jarred Sumner, July 8), [My Thoughts on the Bun Rust Rewrite](https://andrewkelley.me/post/my-thoughts-bun-rust-rewrite.html) (Andrew Kelley, July 9), [Zig Creator Calls Spade a Spade, Anthropic Blows Smoke](https://raymyers.org/post/zed-creator-calls-spade-a-spade/) (Ray Myers, July 13).*
