# LLVM Was Funded by a $5M NSF Grant Nobody Could Have Predicted Would Matter. The FY2027 Budget Cuts the NSF by 54%.

**Published: 2026-07-10**

On July 2nd, Chris Lattner and Vikram Adve — the two people who built LLVM — published a retrospective in *Communications of the ACM* laying out exactly how it happened. The article is part of CACM's special series on [federally funded academic research](https://cacm.acm.org/section/federal-funding-of-academic-research/). The timing is not subtle.

LLVM started in Fall 2000 as a Ph.D. project at the University of Illinois Urbana-Champaign. Adve was the professor. Lattner was the student. The goal was modest: design a flexible compilation strategy that could support static and dynamic compilation for arbitrary languages. The primary funding came from [an NSF CAREER grant](https://cacm.acm.org/federal-funding-of-academic-research/the-llvm-compiler-infrastructure/) — the kind of early-career faculty award that's explicitly designed to support "early-stage research with unknowable outcomes."

No one in 2000 predicted that this university project would become the compiler infrastructure for Rust, Swift, Clang, Julia, Python's numerical stack (Numba), CUDA (NVIDIA uses LLVM internals extensively), WebAssembly toolchains, and essentially every significant language runtime built in the last decade. You couldn't have predicted it. That's the entire point.

## What LLVM Actually Is Today

If you've ever compiled Rust code, you used LLVM. If you've ever written Swift on any Apple platform, you used LLVM. If you've ever run Clang (which is now the default C/C++ compiler on macOS and most modern Linux distributions), you used LLVM. If you've ever touched anything in the WebAssembly ecosystem, you're within two hops of LLVM. If you use PyTorch with GPU acceleration, some part of that pipeline touched LLVM.

This isn't hyperbole — it's infrastructure. The [LLVM project website](https://llvm.org/) lists Ruby, Python, Haskell, Rust, D, PHP, Pure, Lua, Julia, and Fortran as languages with external projects using LLVM components. That list is years out of date. The actual number is much larger.

The LLVM release schedule currently has version 22.1.x in maintenance and version 23.x branching in mid-July 2026. It is not a historical artifact. It is active, critical infrastructure being developed continuously.

## The Funding Story Is Uncomfortable Right Now

Here's where the CACM article becomes something other than a normal retrospective.

The [Trump administration's FY2027 budget proposal](https://www.aau.edu/newsroom/leading-research-universities-report/white-house-once-again-proposes-massive-cuts), released in April 2026, calls for a 54% cut to the National Science Foundation. Not a trim. Not a restructuring. A cut that would take the NSF from roughly $8.5 billion to $3.9 billion. The FY2027 NSF allocation for AI, quantum, and advanced manufacturing — which the White House considers priority areas — would still be cut substantially.

The CAREER grant program that funded LLVM is administered by the NSF. The [Brennan Center has documented](https://www.brennancenter.org/our-work/research-reports/cost-trump-administrations-attacks-research-funding) that the administration already cut or froze over $3 billion in previously approved research grants from NIH and NSF in 2025, with around $1.4 billion remaining frozen or canceled as of early 2026. The NSF awarded just 613 grants in the current fiscal year — roughly 20% of the pace set in each of fiscal years 2021 through 2024.

Lattner and Adve describe the funding situation plainly in their article: "NSF's strategy of funding early-stage research with unknowable outcomes was essential to making the LLVM academic research project and infrastructure possible." That sentence, published in July 2026, reads differently than it would have in 2020.

## The Argument Nobody Is Making Directly

There's a version of the argument about research funding that gets made constantly: "Federal research funding is good because it leads to innovation." This is true but also vague enough to be dismissible. The LLVM story is useful precisely because it's *specific* and *traceable*.

You can draw a direct line from a 2000 NSF CAREER grant to Rust's type-safe systems programming, to Swift's memory safety, to the toolchain powering modern mobile apps, to WebAssembly bringing near-native code execution to the browser. The line isn't speculative — it's documented in the git history.

The LLVM story isn't unique. The same CACM series includes retrospectives on GPU computing (NSF-funded work by Hanrahan and Dally), public key cryptography (NSF and DARPA-funded work that became RSA and Diffie-Hellman), OpenFlow and software-defined networking, and the original development of the internet itself. The internet was a DARPA project before it was infrastructure.

The challenge with the "unknowable outcomes" framing is that it cuts against the way the current administration thinks about research spending. If you can't specify in advance what a grant will produce, that looks wasteful to a certain kind of cost-benefit analysis. But it's precisely the unknowability that makes the system work. You can't plan your way to LLVM. You fund a graduate student working on a flexible compiler architecture, and twenty years later you're looking at the foundation of the modern software stack.

## The Actual Danger

I want to be careful not to overclaim here. The Congress largely rejected the most severe FY2026 cuts, holding non-defense R&D roughly stable. The FY2027 proposal faces the same political headwinds. The NSF will probably not be cut by 54%.

But the trend matters even if the worst proposals don't pass. The NSF has already been awarding grants at 20% of normal pace. Fewer new grants means fewer grad students, fewer research projects, and — crucially — fewer speculative bets on things with "unknowable outcomes." The 2000 version of the LLVM grant might not get funded today not because anyone decided LLVM was a bad idea, but because the NSF is running at 20% capacity and picking only the safest bets.

That's the subtler damage. Not a single cancelled project you can point to, but a quieter reduction in the surface area of what gets tried.

## What the Release Notes Bury

There's a parallel story worth noting. OpenSSH 10.4 [also shipped this week](https://www.openssh.com/txt/release-10.4), on July 6th, with an experimental composite post-quantum signature scheme: ML-DSA-44 combined with Ed25519. OpenSSH is not a commercial product. It was developed by the OpenBSD team — a project that [famously runs on donations](https://www.openbsd.org/donations.html) and has repeatedly been on the edge of running out of money.

OpenSSH is installed on every server on the internet. It secures more remote administration sessions than any other piece of software in existence. Its post-quantum work — including the `sntrup761x25519-sha512` hybrid key exchange that became the *silent default* for most SSH sessions in 2025 — happened because a small group of people with minimal institutional support cared about getting it right.

LLVM has the same character. It wasn't built to be critical infrastructure. It became critical infrastructure because it was technically excellent and the source code was open. The NSF didn't fund "the thing that will become the foundation of Rust." It funded a grad student's dissertation about compiler flexibility.

The CACM article is a polite argument. The argument is: you can't know in advance which bets will pay off like this, which is exactly why you need to keep making the bets. Cutting the institution that places those bets doesn't save money in any meaningful long-term sense. It just means you stop making the bets.

Lattner is now at Google DeepMind, building [Mojo](https://www.modular.com/mojo) and working on ML infrastructure. He could afford to be quiet about research funding. He wasn't. That's worth reading into.

---

*Primary sources: [LLVM retrospective in CACM](https://cacm.acm.org/federal-funding-of-academic-research/the-llvm-compiler-infrastructure/) by Vikram S. Adve and Chris Lattner (July 2, 2026). [AAU analysis of FY2027 budget proposal](https://www.aau.edu/newsroom/leading-research-universities-report/white-house-once-again-proposes-massive-cuts) (April 3, 2026). [Brennan Center report on research funding cuts](https://www.brennancenter.org/our-work/research-reports/cost-trump-administrations-attacks-research-funding) (May 14, 2026). [OpenSSH 10.4 release notes](https://www.openssh.com/txt/release-10.4) (July 6, 2026).*
