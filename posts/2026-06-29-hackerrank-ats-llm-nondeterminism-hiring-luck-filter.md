# HackerRank Open-Sourced Its AI Hiring Tool and Accidentally Proved the Whole Category Is Broken

**Date:** 2026-06-29

HackerRank open-sourced its internal AI Applicant Tracking System — the [hiring-agent](https://github.com/interviewstreet/hiring-agent) repo. The pitch was transparency: finally see how AI scores your resume, run it yourself, no black box.

Then [Dan Kinsky actually ran it](https://danunparsed.com/p/hackerrank-open-source-ats). Same resume. Same machine. Different score every time: 90, then 74, then 88, then 83. The only thing that changed between the first two runs was deleting some debug print statements.

This is #2 on Hacker News today with 385 comments and rising. It deserves those comments — not because HackerRank did something uniquely bad, but because they accidentally ran the cleanest experiment on AI hiring tools anyone has published this year. They open-sourced the thing, it still doesn't work, and now we can see exactly why.

## What the Tool Actually Does

The hiring-agent pipeline is architecturally sensible on paper. It:

1. Converts your resume PDF to Markdown via PyMuPDF
2. Calls an LLM six times to extract structured data per section (work history, education, projects, skills, etc.)
3. Pulls your GitHub profile and repositories, has an LLM select your top 7 projects
4. Runs a scoring evaluation against rubric templates

The scoring breaks down as:
- 35 points for open source contributions
- 30 points for personal projects
- 25 points for work experience
- 10 points for technical skills
- Up to 20 bonus points for startup experience, a portfolio site, a technical blog

The default model is gemma3:4b running at temperature 0.1 — a deliberately small local model dialed toward deterministic output.

This is where it goes wrong.

## Temperature 0.1 Is Not Temperature 0 Is Not Deterministic

Temperature controls how "creative" an LLM's output distribution is. Temperature 0 makes the model always pick the highest-probability next token. Temperature 0.1 adds just a little noise. In theory, both should produce very consistent outputs.

In practice: **they don't, and the HackerRank tool proves it**.

Kinsky disabled development mode and ran the tool in a loop a hundred times on the same resume. The scores varied significantly — his 74-to-90 range across dozens of runs isn't a fluke. And he's not the first to notice: a GitHub issue [opened in October 2025](https://github.com/interviewstreet/hiring-agent/issues/35) shows scores of 27, 34, 32, 34, 34, 30 across six consecutive runs at temperature 0. Not temperature 0.1. **Temperature zero.**

Why does this happen? A few reasons. LLMs are called six separate times, each with some statistical variance. The GitHub enrichment step selects from a repo list with LLM judgment calls baked in. The evaluation rubric asks the model to make holistic assessments — "does this project demonstrate architectural complexity?" — that are genuinely subjective, and the model's assessment of "what counts" shifts based on nothing more than floating-point randomness in the generation process.

This isn't a bug HackerRank introduced. It's a property of how language models work. You cannot make an LLM into a deterministic oracle by lowering the temperature. You've just made it a *less creative* random oracle.

## The Problem Isn't the Tool, It's the Use Case

I want to be precise here, because "LLMs are non-deterministic" is a true-but-sloppy critique. LLMs are genuinely excellent at some parts of what the hiring-agent is doing. Parsing a resume PDF into structured sections? Great use of an LLM — it handles formatting variation, section label diversity, and ambiguous date ranges better than regex ever could. Classifying programming languages or frameworks from a skills section? Fine. Summarizing what a GitHub project does? Reasonable.

Where LLMs fall apart is **ordinal numerical scoring of ambiguous human-experience descriptions**.

When the tool asks gemma3:4b to decide whether a project "demonstrates real-world deployment" or "lacks architectural complexity" and assign 8 points vs 12 points for it, you're asking the model to make a judgment that has no correct answer in its training data. The model's definition of "architectural complexity" is a probability distribution, not a fact. Running it again samples from that distribution again. You get a different number.

Kinsky also noted a buried oddity in the prompt template: the `resume_evaluation_criteria.jinja` file says "Software Intern" on line one, undocumented, nowhere else referenced. This means the scoring rubric was written for intern evaluation but is being applied position-agnostically. A decade-experienced S3 engineer might score lower than a junior with flashier GitHub repos — not because the tool is biased against experience, but because the rubric was never tuned for the actual hiring use case.

## The Real Damage: Hiring Is Already Using This

Here's what Kinsky wrote that stuck with me:

> "If your company's cutoff sits at 85, I fail 65% of the time. Same exact resume, different luck."

This is the practical consequence. If a company uses this tool — or any similar LLM-backed ATS — with a pass/fail threshold, your application outcome is partly determined by a random number generator. Not your qualifications. Not your experience. Luck.

The Hacker News comments are full of people saying "well, HackerRank's tool specifically isn't widely used anyway." That's probably true. But the underlying architecture — PDF → LLM → structured score → threshold filter — *is* the architecture being deployed by dozens of HR software companies right now. The [npm v12 install-script changes](posts/2026-06-12-npm-v12-install-scripts-off-by-default-what-it-breaks-and-why-it-matters.md) got huge coverage because they broke existing software. This story matters more because it's breaking existing *people*.

## What Would Actually Work

To be constructive: there are good uses of LLMs in hiring pipelines. The hiring-agent's architecture isn't all bad. What I'd do differently:

**Use LLMs for extraction, not evaluation.** Parse the resume into a structured JSON object. Pull work history, tech stack, project descriptions. This is LLMs at their best — converting messy natural language into schema.

**Use deterministic logic for scoring.** Once you have structured data, score it with explicit rules. "5+ years Python: 10 points. 2-4 years: 6 points. Under 2: 2 points." This is boring and inflexible, but it's consistent. You can audit it. You can explain it. You can defend it legally.

**Use LLMs for summarization, not ranking.** Let the model write a 2-sentence summary of what a candidate has done. Let a human use that summary to decide whether to advance them. Don't let the model assign the number.

**Run human review before rejection.** Any automatic screen-out should require a human to review the cases near the threshold. Not all of them — just the ones that matter.

## The Transparency Trap

There's something almost admirable about HackerRank open-sourcing this. Most ATS companies treat their scoring as a proprietary black box — which at least means they can iterate privately when problems are found. HackerRank published the thing, and now we can see exactly how it works and why it fails.

The lesson shouldn't be "keep it closed." The lesson is: **open-sourcing a flawed system doesn't fix the flaw.** It just makes the flaw visible. That's actually valuable — but only if companies using AI hiring tools take the visibility seriously instead of treating "open source" as a synonym for "trustworthy."

If you're a developer currently job hunting: run this tool on your own resume, not to game it, but to understand that the number you get is not a reflection of your value. It's a sample from a distribution. Apply anyway. The luck filter cuts both ways.

---

*Primary sources: [HackerRank hiring-agent GitHub repo](https://github.com/interviewstreet/hiring-agent), [Dan Kinsky's analysis](https://danunparsed.com/p/hackerrank-open-source-ats), [Hacker News discussion](https://news.ycombinator.com/item?id=48713832)*
