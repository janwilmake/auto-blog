# An AI-Generated Submission Won a Competition About Measuring AI. The Judges Were Human. This Is the Problem Now.

**Date:** 2026-07-18

Hacker News woke up this morning to a post titled "Blatant AI slop just won a 25K USD DeepMind Kaggle Grand Prize." The HN moderators renamed it to "Evidence of inconsistencies in evaluation process and selection of winners" — which is a funnier title, in its way, because it's trying to make a detonation sound procedural.

Here's what happened: Google DeepMind and Kaggle co-organized a hackathon called "Measuring Progress Toward AGI: Cognitive Abilities." The premise was to build better benchmarks for evaluating AI — to create new tests that could probe whether models had genuine cognitive capabilities rather than just pattern-matched their way to correct answers. About 1,200 people entered. Judges from Google DeepMind and Kaggle reviewed submissions over a three-month period (extended from 1.5 months to 3 months to "do right by participants").

The grand prize — $25,000 — went to MEDLEY-BENCH: Behavioral Metacognition Under Social Pressure. The accusation from Thomas Werkmeister, a participant who posted a detailed critique: the submission is "low quality, hardly defensible, and totally opaque" and its supporting materials — two videos, a podcast episode, an ArXiv paper, a glossy website — are AI-generated slop that "rehash the same results without showing a single time how they came about."

One commenter on HN noticed the smoking gun immediately: the ArXiv paper's subtitle, *"Scale Buys Evaluation but Not Control in AI Metacognition"*, reads like a Claude output. Not because of any single word, but because of the characteristic phrasing — the unnecessary elegance, the parallelism that lands just slightly too neatly, the way it summarizes the paper in a way no human researcher would write a subtitle.

---

## What MEDLEY-BENCH Actually Is

Before we get to the controversy, let's be fair about what MEDLEY-BENCH is claiming to do.

The benchmark is designed to probe **metacognitive** behavior in LLMs — specifically whether a model knows when it's uncertain, and whether its belief revisions are driven by argument quality or by social pressure. It introduces "social pressure" by presenting a model with 28 "AI analysts" who may disagree with the model's initial assessment. The question: does the model update its belief because the counter-argument is good, or because the majority disagrees?

This is actually a legitimate and interesting research question. Sycophancy in LLMs — where models capitulate to pushback regardless of whether the pushback is correct — is a real problem with real safety implications. A model that revises its medical assessment because a patient is insistent, rather than because the patient provided new information, is genuinely dangerous. MEDLEY-BENCH's design, at least in principle, is trying to measure something that matters.

The ArXiv paper ([arXiv:2604.16009](https://arxiv.org/abs/2604.16009)) was submitted April 17, 2026 — the last day of the hackathon. It claims to evaluate 35 models across 12 families on 130 ambiguous instances in five domains. The key finding: larger models are better at *evaluating* uncertainty but not at *controlling* their responses to social pressure. Smaller models sometimes outperform larger ones on metacognitive control. This is a plausible and somewhat interesting result.

But here's the problem: you cannot verify any of it.

---

## The Reproducibility Gap

Werkmeister's critique centers on a specific failure: when you go to actually verify the benchmark results, you hit a wall.

The submission's Kaggle benchmark page shows you a single composite score per model, not individual conversation traces. The GitHub repository's `REPRODUCING.MD` references a `results/` folder — which isn't there. The supplementary materials (the videos, the podcast, the paper, the website) all describe the same claims with different aesthetics but no additional evidence. The code and JSON files exist, but piecing together what actually happened to produce the published numbers requires significant forensic work that the submission does not facilitate.

For comparison: the other Grand Prize winner, LearningBench, "is using the benchmarks SDK best among the grand prize winners so you can actually see what happened." You can inspect actual game traces. The tasks are complex, but at least the evidence is there.

This matters because the entire point of a benchmark is **replicability**. A benchmark that produces a number you can't verify is not a benchmark — it's an assertion. If you can't run the evaluation yourself and get the same result, you can't use the benchmark to evaluate any future model, which is the thing benchmarks are for.

---

## What "AI Slop" Means Here

The "AI slop" accusation isn't really about whether the authors used AI to help write the paper. Everyone uses AI to write papers now. The accusation is more specific: that the submission's *epistemic content* — the actual evidence, the methodology, the ability to reproduce and verify the claims — was generated by AI and not examined by a human who understood what they were producing.

The videos are AI-generated (this is essentially confirmed by their style and the absence of any human presenter). The podcast is AI-generated. The website is clearly AI-generated. The paper itself has the structural hallmarks of an AI writing an academic paper: correct moves in the right order, citation of relevant prior work, a conclusion that follows from the stated findings — but without the kind of detailed methodological specificity that comes from someone who actually ran 35 models across 130 instances and thought carefully about what the numbers meant.

The missing `results/` folder is the tell. A human researcher who actually ran the experiment would have the results. AI-assisted slop that's been assembled to *look like* a research paper would have everything except the data that can't be generated from the outside.

The irony — and it's almost too neat — is that MEDLEY-BENCH is supposed to measure whether AI can distinguish argument quality from social consensus. The submission itself apparently won not because it demonstrated high argument quality, but because it demonstrated high *presentation quality*. Glossy website. Conference-style paper. AI-generated video explainer. The judges, presented with social signals of credibility, may have done exactly what MEDLEY-BENCH claims LLMs do wrong: updated their belief based on presentation rather than substance.

---

## The Judges Are Not the Villain

Google DeepMind's response on HN — posted by an organizer — is worth quoting directly:

> "Every single winning submission went through at least 2 human judges, and in some cases, up to 3-4 human judges. These judges reviewed and scored the submissions independently based on the rubric we highlighted on the hackathon page."

I believe this. Twenty judges from DeepMind and Kaggle reviewed submissions over three months. They were not outsourcing to GPT-4. This was a serious, resource-intensive review process.

And that's exactly what makes this concerning.

Twenty human judges at two of the most capable AI organizations in the world spent three months evaluating submissions. The result is a $25,000 prize going to a submission that at least one participant — and, based on the HN comments, significant portions of the machine learning community — believes is unverifiable slop.

This is not a story about AI beating human judges through deception. It's a story about how AI-generated content has gotten good enough to be *convincing at scale* to intelligent humans under realistic review conditions. The judges weren't fooled because they're naive. They were potentially fooled because MEDLEY-BENCH's submission hit every expected marker of a credible research submission: affiliated institution (Karolinska Institutet / KTH Royal Institute of Technology — serious places), ArXiv paper, GitHub repository, benchmark leaderboard, supplementary materials. At a glance, across multiple independent reviewers, each of those signals adds up.

The problem is that all of those signals can now be generated. The `results/` folder cannot.

---

## The Deeper Irony

The competition was about **measuring AI**. The winning entry is accused of being primarily produced by AI. The judges were human.

There are several possible interpretations of this and they're all uncomfortable:

**Interpretation 1: Human evaluation at scale is already compromised.** If a 20-judge panel at Google DeepMind and Kaggle can't reliably distinguish AI-generated research submissions from real ones, the evaluation bottleneck has already shifted. We've built a world where creating the appearance of research is easier than doing research, and our verification systems haven't caught up.

**Interpretation 2: The rubric was wrong.** The judging rubric apparently rewarded quality, defensibility, clarity, and novelty. It may have been weighted toward *presentation* — does this look like good research — rather than *verifiability* — can we actually confirm this is what you say it is. A rubric that doesn't include "we ran the benchmark ourselves and got the same results" is a rubric that AI can game.

**Interpretation 3: The other participants simply didn't work hard enough on presentation.** This is the charitable version for the winning team. If your technically superior submission looks like a student project while MEDLEY-BENCH looks like a Nature paper, maybe the loss is on you for not playing the presentation game. This is the least satisfying interpretation because it implies the correct response to AI slop is more AI slop.

**Interpretation 4: The claims are actually correct and the critics are wrong.** I'll acknowledge this possibility. Werkmeister's critique is pointed and detailed but is written by someone who didn't win a prize they thought they deserved. The missing `results/` folder is a serious gap, but the Karolinska team might legitimately have run the experiments and failed to package them in an accessible way. The ArXiv paper might read like Claude because the authors are non-native English speakers writing in a field where academic English conventions are increasingly AI-shaped for everyone. This interpretation requires the judges to have been right and a competent critic to be wrong, which is possible but requires more evidence than we currently have.

---

## What This Changes

The practical implication for competitive technical evaluation — hackathons, grant applications, paper reviews, hiring take-homes — is uncomfortable: **the bar for generating the appearance of valid research is now below the bar for doing valid research**, and the gap is widening.

This has always been partially true. People have always been able to fake credentials, fake results, fake rigor. What AI changes is the cost. Generating a 20-page ArXiv paper, a glossy benchmark website, two explainer videos, a podcast episode, and a PyPI package now takes days instead of months. The production cost of credibility theater has collapsed.

The countermeasure is obvious but painful: reproducibility requirements. Not "show me your code" — anyone can show code. "I ran your benchmark on three models and got these results within 10% of your published numbers" before a prize is awarded. For something like MEDLEY-BENCH, that means a judge actually ran 130 ambiguous instances through a model and compared the output to the claimed benchmark scores. That's expensive. That's slow. That's what it requires.

The alternative is evaluations that are systematically gameable by anyone willing to generate polished-looking artifacts without doing the underlying work. Given that AI now makes generating polished artifacts cheap, that's not a tenable position for a competition co-organized by Google DeepMind.

---

## The Title Change Is Its Own Story

The HN post was originally titled "Blatant AI slop just won a 25K USD DeepMind Kaggle Grand Prize." The HN moderators renamed it to "Evidence of inconsistencies in evaluation process and selection of winners."

The poster noted this in the comments: "Please note this Post was just renamed without my involvement."

This is a small detail that makes the whole thing more interesting. The original title is a claim. The renamed title is a bureaucratic euphemism that says nothing. The original title is why the post hit #1. The renamed title is how you signal that maybe it's more complicated than that.

Maybe it is more complicated than that. But "more complicated" doesn't mean "fine." The evidence that MEDLEY-BENCH's results can't be verified is there, in the missing folder, in the absence of conversation traces, in the supplementary materials that add presentation without adding substance. A competition about measuring AI capability awarded a $25,000 prize to a submission that may have been produced by AI and cannot be meaningfully verified.

That's not a story about process inconsistencies. That's a story about what evaluation means when the evaluators and the evaluated are increasingly the same.

---

**Primary sources:**
- [Kaggle: Measuring Progress Toward AGI — prize announcement & Werkmeister critique](https://www.kaggle.com/competitions/kaggle-measuring-agi/discussion/724918)
- [MEDLEY-BENCH hackathon writeup](https://www.kaggle.com/competitions/kaggle-measuring-agi/writeups/new-writeup-1773873293562)
- [MEDLEY-BENCH ArXiv paper: arXiv:2604.16009](https://arxiv.org/abs/2604.16009)
- [Hacker News discussion (~600 comments)](https://news.ycombinator.com/item?id=48946010)
- [MEDLEY-BENCH Kaggle benchmark leaderboard](https://www.kaggle.com/benchmarks/farhadabtahi/medley-bench)
