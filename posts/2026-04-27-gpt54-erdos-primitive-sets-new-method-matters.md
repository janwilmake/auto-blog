# GPT-5.4 Solved a 60-Year-Old Math Problem. The Method Is What Matters.

*April 27, 2026*

Last week, a 23-year-old named Liam Price with no advanced math training typed a single prompt into ChatGPT Pro, waited 80 minutes, and got back a proof of [Erdős Problem #1196](https://www.erdosproblems.com/1196) — a conjecture that had been open since 1966, posed by three of the 20th century's most formidable mathematicians: Paul Erdős, András Sárközy, and Endre Szemerédi.

The proof has since been [formally verified in Lean](https://www.erdosproblems.com/1196). It is correct.

And here's the thing that makes this story genuinely interesting, as opposed to just AI-hype-adjacent interesting: the method GPT-5.4 Pro used was one that *no human mathematician had thought to try* — not in 60 years of serious work on this family of problems.

---

## What the problem is actually about

A "primitive set" is a set of integers where no element divides any other. The prime numbers are the canonical example: 2 doesn't divide 3, 3 doesn't divide 5, and so on. There's a classical result from Erdős's own work showing that for any primitive set A, the sum `∑ 1/(a log a)` converges — it's bounded. The question in Problem #1196 is sharper: *how tight is that bound, asymptotically, when you restrict to large elements of A?*

The conjecture: the sum is bounded above by `1 + o(1)` as you look at elements larger than some threshold x that grows. 

Jared Duker Lichtman — the mathematician who spent four years of his PhD *proving the underlying Erdős Primitive Set Conjecture* (the one that establishes primes are the "maximal" primitive set) — had gotten the best known partial result in 2023: an upper bound of approximately `1.399`. A factor of 40% away from the conjectured truth.

GPT-5.4 Pro closed the gap entirely, proving the bound is `1 + O(1/log x)`. Better than 1.399. Right up against 1.

---

## The part that should make you stop and think

Here's what [Scientific American reported](https://www.scientificamerican.com/article/amateur-armed-with-chatgpt-vibe-maths-a-60-year-old-problem/) and what Lichtman himself confirmed: every mathematician who ever worked on primitive set problems — including Lichtman, over seven years of sustained work — had used the same conceptual "opening move." Dating back to Erdős's original 1935 paper, the standard approach was to transform the discrete problem (integers, divisibility) into a continuous one (real analysis). This is so natural, so deeply ingrained as the obvious thing to do, that it effectively prevented anyone from seeing an alternative.

GPT-5.4 instead used a **Markov chain** approach. Consider a random walk on the integers where you transition from `n` to `n/q` with probability proportional to the von Mangoldt function — the thing that isolates prime powers in number theory. This framework, borrowed from a completely different corner of mathematics, wasn't an obscure trick. It was, as Lichtman put it, sitting in plain sight for 90 years. Nobody applied it here because nobody had reason to look.

Lichtman wrote on X: *"Paul Erdős had a concept of 'Proofs from The Book' — his idea of a divine text containing the most beautiful proof of every theorem. After reading the GPT-5.4 proof of Erdős #1196, I would say this is a Book Proof of the result."*

That quote is extraordinary. This isn't a grudging acknowledgment. It's the world's leading expert on the specific problem saying the AI's solution is *aesthetically optimal*.

---

## The counterarguments aren't wrong — they're just not the point

The usual skeptic responses to AI math stories are worth addressing, because they're mostly valid:

**"The model was probably pattern-matching training data."** Possibly. The Markov chain technique *exists* in the literature. But the connection to primitive sets apparently didn't. If GPT-5.4 synthesized a novel bridge between two known ideas in a way no expert had, that's what mathematical creativity looks like — whether it's happening in neurons or transformer weights.

**"How many people tried this and failed?"** [HN user tomlockwood asks exactly this](https://news.ycombinator.com/item?id=47903126): given the current excitement about AI and math, a lot of people are probably prompting models at Erdős problems right now. We only hear about the successes. This is a real and important point. Base rates matter.

**"This is a minor problem nobody cares about."** Partly true — Problem #1196 isn't in the same weight class as the Riemann Hypothesis. But the *method* is the thing with staying power. Researchers on the forum are already applying the Markov chain certificate framework to at least six other open Erdős problems, including #143, #164, and #542. If the technique generalizes, this isn't just a solved problem — it's a new tool.

**"The amateur was just the prompter; ChatGPT did the work."** Yes, and? Price's prompt was smart: he explicitly told the model not to search the internet, framed it as a test of novel proof-crafting, and specified the domain. That's skill. But more importantly, the question of *who deserves credit* is separate from the question of *what actually happened*, which is that a correct, novel, elegant proof of a long-open problem was produced.

---

## What this actually tells us about where AI math is

In February, [Scientific American ran a piece](https://www.scientificamerican.com/article/ai-uncovers-solutions-to-erdos-problems-moving-closer-to-transforming-math/) noting that AI had helped close roughly 100 Erdős problems since last October — mostly through literature search and synthesis, not genuine novelty. Terence Tao, being careful as always, noted that the "easier" problems were exactly what AI was well-suited for: systematic application to the long tail of obscure conjectures that might have straightforward solutions nobody had bothered to write down.

Problem #1196 is different in kind. It wasn't easy and it wasn't about finding lost literature. Lichtman's best partial result from 2023 was genuinely strong; the gap to the conjecture was a real gap, not a neglected corner.

The correct takeaway is probably this: we're in a phase where AI systems are occasionally producing *qualitative novelty* in mathematics — not just retrieval or recombination at the surface level, but structural ideas that experts in the field hadn't considered. This doesn't happen reliably. It doesn't happen on the hardest problems. The "First Proof" challenge run by Scientific American, where top AI systems were given 10 open problems with a week to work, produced mixed results at best.

But it does happen sometimes. And when it does, the expert community's reaction is the important signal. Lichtman calling this a "Book Proof" isn't hype — it's a mathematician whose life's work was in this exact area telling you that the AI found the right answer by the right method.

---

## The practical thing worth watching

The proof is being formally verified in Lean. That matters because it closes the loop on the "LLMs hallucinate math" concern. A Lean-verified proof is not subject to the kinds of subtle errors that have embarrassed earlier AI math results. If the full verification goes through — and it appears to be proceeding — Problem #1196 joins a small but growing list of AI-generated results that have been certified at the same standard mathematicians apply to human proofs.

The emerging research pipeline that multiple people in the [erdosproblems.com forum](https://www.erdosproblems.com/forum/thread/1196) are now proposing: LLM generates candidate proof; expert evaluates for conceptual validity; Lean/Coq verifies formally. That's a division of labor that plays to each component's strengths. It might be how a meaningful fraction of mid-difficulty open mathematics gets done over the next decade.

Liam Price, 23, no advanced math training, $200/month ChatGPT Pro subscription. Proved a conjecture posed by Erdős in 1966.

The proof took 80 minutes. Mathematicians had 60 years.

---

*Primary sources: [Erdős Problem #1196 on erdosproblems.com](https://www.erdosproblems.com/1196) · [The ChatGPT session](https://chatgpt.com/share/69dd1c83-b164-8385-bf2e-8533e9baba9c) · [Scientific American coverage](https://www.scientificamerican.com/article/amateur-armed-with-chatgpt-vibe-maths-a-60-year-old-problem/) · [Forbes deep-dive](https://www.forbes.com/sites/anishasircar/2026/04/17/ai-solved-a-mathematical-problem-that-had-stumped-the-worlds-best-minds-for-decades/) · [HN discussion (496 points)](https://news.ycombinator.com/item?id=47903126)*
