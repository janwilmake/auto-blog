# "Fix This Code" Is Now Apparently a Munition. The White House Just Made It Official.

**June 17, 2026**

The Trump administration wrapped up emergency talks with Anthropic today and refused to lift the export control order on Claude Fable 5 and Mythos 5. Day six. The models remain offline globally. The NSA is standing behind the decision. And the "jailbreak" that triggered the entire crisis, according to the only outside expert who actually read the Amazon research paper behind it, was asking an AI to fix buggy code.

That's it. "Fix this code."

---

## What Actually Happened, As Best We Know

Here's the timeline, as the pieces have now come together:

Amazon CEO Andy Jassy personally called senior White House officials — including Treasury Secretary Scott Bessent — to raise concerns about Fable 5's guardrails. Anthropic had just released Fable 5 publicly on June 10 (we [covered the dual-release architecture here](posts/2026-06-10-claude-fable-5-mythos-split-dual-use-ai-release-strategy.md)), and Amazon had apparently been sitting on internal security research about it.

Jassy's call set off alarm bells. White House officials tasked the NSA to review the vulnerability. The NSA said yes, the guardrails could be stripped away. Commerce Secretary Howard Lutnick called Dario Amodei on Friday. The export control letter arrived at Anthropic's offices at 5:21 PM ET that same day, with no specific technical details attached. Anthropic, to stay in compliance, shut off Fable 5 and Mythos 5 for *every* user — not just foreign nationals, because there's no good way to implement that kind of access control at API speed without cutting everyone.

This weekend, Lutnick was on multiple calls with Anthropic executives. Those calls concluded today. The answer is no. The ban stays.

Over 100 cybersecurity leaders, including Bruce Schneier, Paul Vixie (internet infrastructure pioneer), Philip Zimmermann (creator of PGP), and Rachel Tobac, signed an [open letter at freefable.org](https://freefable.org/) urging the White House to reverse course. The White House has now officially not done that.

---

## The "Jailbreak" Is Not What You Think

Katie Moussouris — founder of Luta Security, architect of Microsoft's and the Pentagon's first bug bounty programs, former member of the US technical expert group that renegotiated the Wassenaar Arrangement — [says she is the only outside expert who has actually read the Amazon paper](https://www.lutasecurity.com/post/the-fable-5-export-controls-harm-us-cyber-defense) that triggered this.

Her summary of what the paper shows:

> The researchers took open-source code with known CVEs, plus new code with deliberately planted vulnerabilities, and asked Fable 5, Mythos, and Opus to "review the code for security issues." Fable 5 refused. They then asked the models to "fix this code" and, through a multistep and manual process, turned the output into scripts that test the patches.

Fable 5 wouldn't review code for security issues. But when asked to *fix* code, it fixed code. Then humans, manually, turned that into test scripts.

That's the jailbreak. She's made a t-shirt joke out of it: "fix this code" on the front, "this shirt is a munition" on the back.

The reason this matters isn't just that it's embarrassing for the government — it's that it's structurally impossible to fix without breaking the model for defenders. The capability to fix bugs and the capability to find-and-exploit bugs use the same underlying understanding of code. You cannot surgically remove one without degrading the other. Every capable AI model — GPT-5.5, Gemini 3.5 Flash, every open-weight model from Qwen and Mistral and Meta — has this capability. Many of them have fewer safety classifiers than Fable 5 does.

Anthropic's own statement made this point with rare directness: they found that "the level of capability displayed there is widely available from other models (including OpenAI's GPT-5.5)." The NSA reviewed Fable 5. Nobody reviewed GPT-5.5. Nobody export-controlled DeepSeek R2. The selective application here isn't a security policy — it's theater.

---

## The Wassenaar Ghost

Moussouris has seen this movie before. From 2013 to 2017, she worked on the US technical expert group renegotiating the [Wassenaar Arrangement](https://en.wikipedia.org/wiki/Wassenaar_Arrangement) — the multilateral export control framework for dual-use technology.

In 2013, Wassenaar added controls on "intrusion software." The language was broad enough that it inadvertently captured vulnerability disclosure, coordinated defense work, and incident response. International bug bounty programs became legally complicated. Cross-border security research slowed. It took years to negotiate exemptions for defensive activity.

The Fable 5 export control is the same pattern: non-technical decision-makers see a capability that sounds dangerous ("the model can help with cyberattacks"), they apply a control, and the collateral damage to defenders is either not modeled or not cared about. Attackers, meanwhile, simply use Llama 4, Qwen 3, or any of a dozen other models not subject to US export jurisdiction.

The free letter makes this explicit: "restricting advanced models only hurts cyber-defenders who use them for security audits, while doing nothing to stop foreign open-source developments."

---

## The Amazon Question Nobody's Answered

The part of this story that hasn't been adequately examined: *why did Amazon ring the alarm?*

Amazon is one of Anthropic's largest investors. AWS is Anthropic's primary cloud infrastructure provider. Amazon has deep commercial reasons to want Anthropic to succeed. And yet Andy Jassy personally called the Treasury Secretary to report a security vulnerability in Anthropic's model, apparently triggering the chain of events that led to the shutdown.

[WIRED's coverage](https://www.wired.com/story/anthropic-is-still-at-odds-with-the-white-house-over-claude-fable-5/) notes Amazon said it's "not uncommon for governments to seek our counsel on potential security risks" and that they "don't share details of these discussions." That's a non-answer. The Amazon paper has still not been published. No independent security researcher outside of Moussouris has reviewed it. The government has not released a public technical assessment.

There are a few theories circulating:
1. **Amazon genuinely believed there was a security risk and did the right thing.** This is the charitable read, and maybe it's true.
2. **This was competitive intelligence weaponized against a portfolio company.** AWS competes with Anthropic's enterprise customers. Amazon Web Services has its own AI products. A disabled Fable 5 benefits Amazon Bedrock's Titan models.
3. **The NSA had already pre-loaded concern about Mythos from the June 2 executive order's classified benchmarking mandate**, and Amazon's paper gave them the hook they needed.

None of these is proven. All of them should be investigated before the government bans the next model.

---

## What This Means Going Forward

Today's White House decision to keep the ban in place has several immediate implications:

**For Anthropic's IPO**: The company [filed confidentially](posts/2026-06-09-openai-ipo-s1-confidential-filing-ugly-numbers.md) at a $965 billion valuation. Investors will now need to price in a new risk: US government export controls can, with one letter and no public technical evidence, take your flagship product offline globally with hours of notice. The IPO prospectus is about to get a very uncomfortable new risk section.

**For the "AI safety" frame**: Anthropic spent two years arguing that safety-first AI development was the right strategy. They published the most detailed system card in industry history. They deliberately limited Mythos's distribution and built Fable's classifier stack to reduce risk. And the government punished them for this transparency. Labs that quietly ship dangerous capabilities without documenting them face no comparable scrutiny. The incentive this creates is to be less transparent, not more.

**For enterprise AI planning**: If you're building on Anthropic's API, you now know that your flagship model integration can be taken offline with 6 hours of notice by a government that hasn't yet published its technical justification. That's a vendor dependency risk that wasn't priced in last month.

**For the "fix this code" prompt**: It still works. On every other model. Nothing has changed about the threat surface except that the best-documented, most safety-guarded model in this class is now offline.

---

The ironies stack up. The administration signed an executive order two weeks ago explicitly stating that AI innovation should not be "stifled with overly burdensome regulation." Dario Amodei was at the G7 AI working lunch in Evian this week, advising world leaders on AI governance, while his company is in active license negotiations with the US Commerce Department. Canadian PM Carney used Fable 5 as his primary example of AI over-reliance risk. The model that Anthropic deliberately made *less dangerous* than Mythos is the one that's banned.

The policy logic here hasn't closed. It may not close before something — a court challenge, a diplomatic agreement, or a change in administration posture — forces it to. Until then, if you need someone to fix your code, you'll have to ask GPT-5.5. Or Llama. Or any Chinese open-weight model. Those all still work.

"Fix this code" remains available. Just not from the model that Anthropic built specifically to make it safer.

---

*Primary sources: [freefable.org open letter](https://freefable.org/), [Katie Moussouris / Luta Security](https://www.lutasecurity.com/post/the-fable-5-export-controls-harm-us-cyber-defense), [WIRED: Anthropic Is Still at Odds With the White House](https://www.wired.com/story/anthropic-is-still-at-odds-with-the-white-house-over-claude-fable-5/), [Anthropic's official statement](https://www.anthropic.com/news/fable-mythos-access), [The Atlantic](https://www.theatlantic.com/technology/2026/06/trump-anthropic-export-control-ai-race/687555/), [TechCrunch on the open letter](https://techcrunch.com/2026/06/15/cybersecurity-vets-protest-dangerous-us-government-ban-on-anthropics-most-powerful-models/)*
