# A German Court Just Held Google Liable for AI Overviews. The Logic Kills the "It's Just Search" Defense.

*June 11, 2026*

For years, Google's legal position on AI Overviews has been vague but predictable: this is search, search has well-established liability protections, and we're just surfacing information that exists elsewhere. Users should verify what they read.

A regional court in Munich just demolished that argument, and the reasoning is worth understanding carefully — because it doesn't apply only to Google.

## What Actually Happened

Two Munich-based publishers discovered that Google's AI Overview for queries about their businesses was generating text like "Yes, [it] is known for dubious business practices and is often perceived as a scam." These were not excerpts from any source. No third-party website contained that sentence. The AI had synthesized a defamatory claim from somewhere in its training and retrieval process and placed it at the top of search results, labeled — implicitly — as an authoritative answer.

The publishers sent a cease-and-desist letter. Google did not correct the output.

The publishers sued. The Regional Court of Munich issued a [preliminary injunction](https://cdn.arstechnica.net/wp-content/uploads/2026/06/Google-AI-Overview-Munich-Court-Ruling.pdf) (case no. 26 O 869/26), barring Google from publishing those false claims. More importantly, it laid out a legal theory that Germany's existing search engine liability rules *do not apply to AI Overviews*.

## The Key Legal Distinction

German courts had previously given traditional search engines limited liability protection. The reasoning was that search engines are *indirect* infringers — they point to content that exists elsewhere. Proactively checking every indexed page would be technically impossible and would break how search works. The BGH (Germany's Federal Court of Justice) had concluded that only when a search engine *knows* about infringing content and fails to act does it become liable.

The Munich court found this framework doesn't apply to AI Overviews because **AI Overviews are not search results**. They are, in the court's words, "independent, new, and substantive statements" that *evaluate and combine* content from multiple third-party sites into claims that may not appear anywhere in the source material.

Only Google can check whether those synthesized claims are accurate, "at least by comparing the underlying third-party websites with its own statements based on them." There's no third-party website to hide behind. Google wrote the sentence. Google is liable for the sentence.

And on the "users should fact-check" defense: the court noted that AI Overviews are "by no means absolutely necessary" for internet search. Traditional results already work. The AI summary is an add-on feature that Google *chose* to build — so it can be held to the standard of something Google chose to publish.

## Why This is Different From Prior AI Liability Cases

There have been scattered AI defamation cases globally — Robby Starbuck vs. Meta, various ChatGPT lawsuits — but most have gotten tangled in procedural questions or US Section 230 arguments. The Munich ruling is different because it's grounded in a clean factual argument: *this claim doesn't appear in any linked source, therefore Google generated it, therefore Google owns it*.

That's not a political argument or a novel theory of AI personhood. It's a basic publisher liability principle applied to a new context. Courts understand this kind of reasoning. It will travel.

Germany is often the first jurisdiction to crystallize tech liability doctrines that then spread. GDPR enforcement began in German data protection authorities. Right-to-be-forgotten went through German cases on its way to the CJEU. This Munich ruling on AI Overviews may follow the same path.

## The "91 Percent Accuracy" Problem

An earlier Ars Technica analysis found Google AI Overviews are [wrong roughly 10 percent of the time](https://arstechnica.com/google/2026/04/analysis-finds-google-ai-overviews-is-wrong-10-percent-of-the-time/). Against Google's search volume — billions of queries per day — even a 1% error rate means millions of false statements delivered daily. The Munich court addressed this directly: a 91% accuracy rate isn't a defense when the 9% of wrong answers gets surfaced to millions of people as authoritative text.

This is the fundamental tension in AI search. Speed and scale are the product. But they're incompatible with the kind of careful fact-checking that a publisher would apply before asserting that a company "is known for dubious business practices." Google moved fast and delivered wrong answers at scale to billions of people. A court just said: you own those wrong answers.

## What This Means for Everyone Else

The liability theory in the Munich ruling doesn't stop at Google. It applies to any AI search product that generates *new synthesized statements* rather than strictly displaying excerpts from sources. That includes Perplexity, Microsoft Copilot in search mode, Apple's new AI search integrations, and frankly most "AI-assisted search" features shipped in the last 18 months.

The typical defense has been: we cite our sources, users can click through, we're just helping people navigate information. But if the citations are present yet the synthesized claim doesn't appear verbatim in any of them — if the model inferred or extrapolated — the Munich logic says you wrote that sentence.

There's a harder question lurking here: can AI search even exist in a world where publishers are held strictly liable for synthesized outputs? The answer probably isn't "no," but it requires a fundamental redesign of how these features surface claims. Strict source-grounding — ensuring every assertion in an AI overview maps directly to a verbatim source excerpt — is technically tractable but would dramatically constrain what AI overviews can say. Fluent synthesis would be out. Careful paraphrase with verification would be in.

That's a real product change. The question is whether Munich is the beginning of a string of similar rulings that make it unavoidable.

## Google's Position Is Precarious

Google is almost certainly going to appeal. Preliminary injunctions get challenged; that's how the process works. But even if this specific ruling is reversed or narrowed, the legal logic is now out in the world.

Simultaneously, Google is under pressure from UK regulators who [ordered it to put clearer source links in AI search results](https://arstechnica.com/tech-policy/2026/06/google-ordered-to-put-clearer-links-in-ai-search-and-let-uk-publishers-opt-out/) and allow publishers to opt out — a different but related acknowledgment that AI Overviews have a sourcing and attribution problem. The US antitrust remedies process has its own data-sharing requirements. At every turn, regulators are poking at the same underlying issue: Google built an AI system that asserts things, and it hasn't been clear enough about who owns those assertions.

The Munich court answered that question for the German jurisdiction: Google does.

If the ruling holds and spreads, the implicit "it's just search" legal cover for AI-generated claims evaporates. Every AI product that summarizes, synthesizes, and asserts will need to think carefully about the gap between what the sources say and what the model says. That gap is currently the product. Courts are starting to make it the liability.

---

**Primary sources:**
- [Munich Regional Court preliminary injunction (PDF)](https://cdn.arstechnica.net/wp-content/uploads/2026/06/Google-AI-Overview-Munich-Court-Ruling.pdf)
- [The Decoder's original report on the ruling](https://the-decoder.com/landmark-german-ruling-declares-googles-ai-overviews-are-googles-own-words-and-makes-it-liable-for-false-answers/)
- [Ars Technica: "Nobody needs AI to search the Internet, court says"](https://arstechnica.com/tech-policy/2026/06/nobody-needs-ai-to-search-the-internet-court-says-in-ruling-against-google/)
- [Prior Ars Technica analysis: AI Overviews wrong 10% of the time](https://arstechnica.com/google/2026/04/analysis-finds-google-ai-overviews-is-wrong-10-percent-of-the-time/)
