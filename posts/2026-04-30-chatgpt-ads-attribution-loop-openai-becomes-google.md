# ChatGPT's Ad Machine Is Already More Invasive Than Google's. Nobody Noticed.

**Date:** 2026-04-30

OpenAI has been quietly running ads inside ChatGPT for weeks. That itself isn't news anymore — the leaked StackAdapt deck in late March confirmed the pilot, and observers have been watching live deployments in Australia, New Zealand, and Canada. What *is* news is that a researcher just [reverse-engineered the entire attribution stack](https://www.buchodi.com/how-chatgpt-serves-ads-heres-the-full-attribution-loop/) from observed traffic, and the picture that emerges is significantly more sophisticated — and more privacy-concerning — than anyone expected from a company that sold itself as the search-killer that wasn't poisoned by ads.

Let's talk about what's actually in there.

## How the Plumbing Works

When you send a message to ChatGPT, the backend fires an SSE (Server-Sent Events) stream back to your browser. Most of those events are model output. Some are now `single_advertiser_ad_unit` objects — structured JSON blobs that render as carousel cards with a favicon, title, body copy, and a click target. The ads appear *alongside* the model's response, injected into the same stream, at the same time the model is generating text.

The targeting is contextual and, right now, fairly coarse: your current conversation topic maps to an ad category. Ask about Beijing trip planning, get Grubhub. Ask about NBA playoffs, get Gametime. Ask about productivity software, get Canva. It's essentially keyword-adjacent intent matching — not that different from Google's original AdWords model circa 2004, except the "keyword" is your entire conversation context, not a three-word query.

Here's what's genuinely new: **OpenAI doesn't just know you clicked. It watches where you go after you click.**

When you tap an ad card, the link opens in an **in-app webview** rather than your external browser. The `target.open_externally` flag is set to `false`. That means OpenAI's container observes your entire post-click navigation — which pages you visit on the merchant's site, how long you spend there, what you look at — *in addition* to whatever the merchant's own pixel collects. Traditional ad networks see a click and then hand off. ChatGPT keeps watching.

The attribution chain uses four Fernet-encrypted tokens per ad:
- `ads_spam_integrity_payload` — server-side fraud check, never leaves the SSE stream
- `oppref` — the forward attribution token, lives in a **30-day first-party cookie** called `__oppref`, follows you across every merchant pixel event
- `olref` — impression-side logging on OpenAI's servers
- `ad_data_token` — a base64-wrapped secondary Fernet token, reconciled server-side at click time

On the merchant side, a new tracking SDK called **OAIQ** (version 0.1.3, already in the wild) runs in visitor browsers. It reads the `oppref` token from the click URL, sets the cookie, and posts conversion events back to `bzr.openai.com`. The domains to watch — or block — are `bzrcdn.openai.com` and `bzr.openai.com`.

## The Privacy Angle Nobody's Talking About

Every major conversation about ChatGPT ads has focused on two things: whether the ads will annoy users, and whether the targeting is good enough to justify the reported $3–5 CPC bids. Both are reasonable questions. Neither is the interesting one.

The interesting question is: **what does OpenAI now know about merchant customers that the merchants don't?**

When a user talks to ChatGPT about planning a vacation, then clicks a GetYourGuide ad, then browses tour listings for 20 minutes but doesn't buy — OpenAI gets that whole signal. They know what the user was thinking about before the click (the conversation), what they looked at during the click (the webview), and whether they converted (the OAIQ SDK). The merchant gets the pixel event. OpenAI gets the conversation that explains *why* the user was in the market in the first place.

That asymmetry of information is novel. Google knows you searched "great wall tours beijing." OpenAI knows you said "I'm going with my elderly parents who can't do a lot of walking, what's the easiest way to see the Great Wall without killing them, and I have about $200 per person." The intent model available from conversational AI is an order of magnitude richer than keyword search.

And because OpenAI hosts the creative (brand images load from `bzrcdn.openai.com`, not the merchant's CDN), they can run A/B tests on ad creative without the merchant ever knowing. They see which thumbnail drove a click, which headline got the best click-to-conversion ratio. The advertiser bids into a black box and gets a CPC back. The platform accumulates the learning.

This is not how Google or Meta run ads. Even Meta, with its notoriously opaque auction, gives advertisers access to conversion lift studies and audience insights. The OAIQ SDK is version 0.1.3 and has almost no advertiser-facing reporting surface yet. OpenAI is accruing the data and will decide later how much of it to share.

## The Free-Tier Subsidy Problem

Here's where the business model starts to get thorny. OpenAI's pitch to free users has always been implicitly: we'll subsidize your access because you help us train better models and eventually you'll upgrade. Ads change that implicit contract. Now free users are the product in the classic sense.

But there's a compounding problem: ChatGPT's paid subscribers ($20–200/month depending on tier) are a substantial portion of the user base that made ChatGPT valuable in the first place. The power users, the developers, the people who made ChatGPT look indispensable to their companies. If ads start bleeding into paid tiers — or if the free experience degrades enough that it nudges users toward competitors — OpenAI is playing a game that Google has years more practice at.

The current deployment is geographically limited to AU/NZ/Canada, which is the standard "far from US media scrutiny" test-bed move. Expect the rollout to go global within months.

## What This Means for the Web

The boring take is: ads are ads, this is how companies make money, move on.

The more interesting take is about **where Google's real moat was** and what happens when it disappears.

Google's ad dominance wasn't just about scale — it was about owning the **query graph**: the fact that before you buy almost anything, you articulate your need in a search box, and Google sees that articulation first. That's why search ads were worth hundreds of billions of dollars a year while display ads were worth a fraction as much. Search intent = high commercial value.

ChatGPT is now collecting a query graph that's richer than Google's in every dimension that matters for advertising: longer context, clearer intent, more personal. A user who types "recommend a CRM for a 10-person sales team with a tight budget and bad adoption of their current tool" is giving an advertiser more signal than a hundred searches for "CRM software."

The open question is whether users will tolerate ads in their AI assistant the way they tolerated ads in their search engine. The historical evidence isn't great. Users tolerate search ads because they understand the implicit deal: the results page has always had ads; it's part of the furniture. ChatGPT built its identity explicitly as the alternative that *wasn't* that. The first users who started seeing carousel cards in their conversations last month didn't sign up for that deal.

There's a version of this that works: if the ads are genuinely useful — if you're researching a product and the ad surfaces something you'd actually want — then contextual advertising inside an AI assistant might be more acceptable than a banner ad on a content site. That's the optimistic framing. 

The pessimistic framing is that we've just watched OpenAI build the most sophisticated conversational advertising infrastructure in history, test it in three countries with almost no press coverage, and deploy it before anyone had time to ask whether it was a good idea.

---

If you want to audit what's landing on your machine: check for `__oppref` and `__oaiq_domain_probe` cookies after clicking any ChatGPT recommendation. If you're running a network filter, add `bzrcdn.openai.com` and `bzr.openai.com` to your blocklist. The full technical breakdown is [here](https://www.buchodi.com/how-chatgpt-serves-ads-heres-the-full-attribution-loop/). The HN thread is [here](https://news.ycombinator.com/item?id=47942437) and worth reading — the discussion about AI as a propaganda distribution mechanism is uncomfortable but not obviously wrong.

OpenAI needed a revenue stream that wasn't compute-hungry model subscriptions. They found one. Whether it breaks ChatGPT's core value proposition is a question that will be answered over the next six months, one ad impression at a time.
