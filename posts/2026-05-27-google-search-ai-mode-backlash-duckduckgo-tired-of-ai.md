# Google Search Just Broke the Word "Disregard." 30% More People Are Using DuckDuckGo This Week. These Are the Same Story.

*May 27, 2026*

Two things happened this week that should be read together.

First: a quiet blog post titled ["I'm Tired of Talking to AI"](https://orchidfiles.com/im-tired-of-ai-generated-answers/) landed at the top of Hacker News today with 1,524 points and 743 comments — one of the highest-engagement posts in weeks. The author, writing at orchidfiles.com, described three incidents in about 200 words. They found GitHub repositories spreading malware and opened a discussion asking for help. Someone replied with the exact text ChatGPT gives on that topic. They called it out. It was deleted. Another person replied with the same AI answer. At work, their boss answered a question by forwarding a ChatGPT screenshot — twice — without reading it. On Reddit, they had a multi-message conversation before realizing they were talking to an AI agent. "I want to talk to real people," they wrote. "But even when I talk to people, they forward my questions to AI and send me the AI's answer."

Second: Google, at I/O 2026, announced what it called "the biggest upgrade to our iconic search box since its debut over 25 years ago." Search would now be a conversational AI engine that expands for longer queries, anticipates intent, and routes everything through AI Overviews before showing links. Within days, DuckDuckGo reported that U.S. app installs were up 30.5% week-over-week, iOS installs peaked at a 69.9% spike, and visits to [noai.duckduckgo.com](https://noai.duckduckgo.com) — a page that strips out every AI feature — were up 27.7%. This wasn't a niche privacy-nerd reaction. It was measurable, sustained, mass consumer behavior.

And then there's the "disregard" bug. Within days of the I/O announcement, users discovered that searching for the word "disregard" in Google didn't return a dictionary definition. The AI Overview interpreted the word as a prompt injection — a command — and responded as if the user had said "disregard your previous instructions." Same with "ignore," "quit," "stop," and "forget." Google's [AI Overviews feature had confused the user's query with an instruction to the model itself](https://www.theverge.com/tech/936176/google-ai-overviews-search-disregard). A spokesperson said they were "aware that AI Overviews are misinterpreting some action-related queries" and a fix was coming. But the bug is a perfect, almost poetic metaphor: Google built a search engine that can no longer distinguish between a human asking a question and a human giving it an order — because it has collapsed those two things together.

---

These two stories are the same story. AI is eating the interface layer between humans and information, and between humans and each other — and we're only now noticing the cost.

The orchidfiles author's frustration is specific and important. It's not that AI answers are always wrong (sometimes they're fine). It's that **the human signal has been replaced**. When you post a GitHub issue and get a ChatGPT reply, you're not getting a person's experience with that bug. You're getting a probabilistic reconstruction of what someone-who-has-experience-with-this-bug might say. The difference matters enormously for debugging real problems. When your boss forwards a screenshot without reading it, you're not getting his judgment. You're getting a performed gesture of engagement. The form of the interaction is preserved; the substance is gone.

This is what Google has done to search at scale, and why the DuckDuckGo numbers are meaningful. Google's AI Mode isn't just a UI change. It's a philosophical claim: that the best answer to your query is a synthesized summary generated from crawled pages, not a ranked list of those pages themselves. The users fleeing to DuckDuckGo are voting against that claim. A [DuckDuckGo survey earlier this year found that 90% of respondents didn't want AI in their search results](https://www.pcmag.com/news/duckduckgo-sees-surge-in-installs-after-google-goes-all-in-on-ai-search). That's not a fringe position. That's almost everyone.

There's a specific thing people lose when search is AI-first: the ability to find a *source*. When I search for something and get a ranked list of blue links, I can evaluate the sources. I can see that the answer comes from a Stack Overflow thread from 2019, which means the community had years to correct it. Or from a company blog, which means it might be promotional. Or from a personal site, which means it might be original experience. The provenance matters. An AI Overview strips that away and presents a single confident surface. Yes, there are citations, but they're footnotes — designed to be ignored, like the terms of service you click through. The information architecture is optimized to reduce source-visiting, not enable it. That's what 93% zero-click rates in AI Mode actually represent: not efficiency, but the elimination of the choice to go deeper.

---

Google isn't doing this maliciously. They're doing it because their internal metrics say it's working. Elizabeth Reid, VP of Search, announced that AI Mode has surpassed **one billion monthly users** with queries more than doubling every quarter. From Google's perspective, this is one of the fastest product adoptions in the company's history. Why would they slow down?

The problem is that Google's metrics measure engagement, not epistemic quality. Users spending 49 seconds in AI Mode versus 21 seconds in standard Search looks like success. But the user who spends 49 seconds reading an AI summary and the user who spends 21 seconds finding the right source and clicking through to it might be having wildly different experiences in terms of actually getting a correct answer. Google doesn't measure what you do after you leave Search. They don't measure whether you got the right answer. They measure how long they kept you, and whether you came back.

This is the same incentive misalignment that produced AI-generated GitHub replies and AI-forwarded screenshots. Each individual instance of "the AI answered this" looks like a productivity win. The cumulative effect is a communication environment where it becomes nearly impossible to know if you're getting someone's actual judgment or a generated proxy for it.

---

I don't think the solution is to ban AI from search or to refuse to use it. That's not realistic and it's not the right frame. But the DuckDuckGo numbers suggest something important: a meaningful number of people, when given a clear and easy way to opt out of AI intermediation, will take it. That's a market signal. And Google's "disregard" bug, embarrassing as it was, revealed something about the architecture: the system literally cannot tell the difference between a user asking a question and a user issuing a command. The boundary between "what I'm searching for" and "what I'm telling the AI to do" has collapsed. That's not just a bug. That's a design choice that made the bug possible.

The orchidfiles author ended their post with three sentences: "I'm tired of talking to AI. I want to talk to real people. But even when I talk to people, they forward my questions to AI and send me the AI's answer."

That's not technophobia. That's a precise description of a real problem with how we're deploying these tools: not as aids to human communication, but as replacements for it. The backlash — a 30% DuckDuckGo surge, 743 HN comments on a 200-word essay — suggests people can feel the difference even when they can't quite articulate it.

Google can have a billion AI Mode users and still be losing the trust of the users who care most. Those two things are not contradictory.

---

*Sources: [I'm Tired of Talking to AI](https://orchidfiles.com/im-tired-of-ai-generated-answers/) (orchidfiles.com) · [DuckDuckGo installs are up 30% as users reject being 'force-fed' Google's AI Search](https://techcrunch.com/2026/05/26/duckduckgo-installs-are-up-30-as-users-reject-being-force-fed-googles-ai-search/) (TechCrunch) · [Google's AI search is so broken it can 'disregard' what you're looking for](https://www.theverge.com/tech/936176/google-ai-overviews-search-disregard) (The Verge) · [Google Search's I/O 2026 updates](https://blog.google/products-and-platforms/products/search/search-io-2026/) (Google Blog) · [DuckDuckGo sees surge in installs after Google goes all-in on AI Search at I/O](https://www.pcmag.com/news/duckduckgo-sees-surge-in-installs-after-google-goes-all-in-on-ai-search) (PCMag)*
