# Google Turned Your Old API Keys Into Gemini Credentials — Without Telling You

**April 16, 2026**

A developer enabled Firebase AI Logic on an existing project. Within 13 hours, automated bots had run up €54,000 in Gemini API charges. Google denied the refund request. The charges stand.

This story is on the front page of Hacker News today, and it deserves more than a "wow, scary" reaction — because the underlying design decision that caused it is still in place, thousands of developers are still exposed, and the victims keep coming. A Japanese company owes $128k. A solo developer's startup nearly collapsed over a $15k bill. A Mexican dev team saw charges spike 455x in 48 hours. A computer science student got a $55,444 bill for accidentally leaking a key on GitHub.

The common thread isn't developer carelessness. It's a specific, documentable design failure that Google has acknowledged but not yet fully fixed.

---

## The Problem: One Key Format, Two Very Different Jobs

For over a decade, Google told developers that API keys (the ones starting with `AIza...`) are safe to put in public code. Not just "safe enough" — explicitly, officially safe.

Firebase's security checklist stated that API keys are *not secrets*. Google's Maps documentation literally instructed developers to paste keys directly into HTML. The rationale made sense: these keys were project identifiers for billing purposes, not authentication credentials. You needed them to attribute usage to your billing account, not to prove you were authorized to access private data.

Then Gemini arrived, and Google attached it to the exact same key format — with no retroactive warning.

This is the core finding from [Truffle Security's February research](https://trufflesecurity.com/blog/google-api-keys-werent-secrets-but-then-gemini-changed-the-rules): when you enable the Gemini API on a Google Cloud project, every existing API key in that project — including the one you put in your website's public JavaScript three years ago because Google told you to — silently gains access to Gemini. No warning. No confirmation dialog. No email.

Truffle Security scanned millions of websites and found **2,863 live Google API keys** in public JavaScript that could now authenticate to Gemini endpoints. Including, pointedly, a key from a Google-associated website. Keys that had been deployed as benign billing identifiers had become live AI credentials, sitting exposed on the public internet exactly as Google had instructed developers to deploy them.

---

## It Gets Worse: The Defaults Are Still Broken

The retroactive exposure of old keys is bad enough. But the default behavior for *new* keys compounds the problem.

When you create a new API key in Google Cloud, it defaults to "Unrestricted" — valid for every enabled API in your project, including Gemini, with no scope limitation at all. The UI shows a small warning about "unauthorized use," but the default is wide open.

This means a developer who follows the quick-start guide, creates a key, enables Firebase AI Logic, and puts the key somewhere accessible — which is exactly what the Firebase documentation workflow historically implied — has immediately created a key that unauthorized parties can use to rack up Gemini charges.

And here's the billing structure that turns an exposure into a disaster: Gemini API costs can scale to thousands of euros per hour for heavy workloads. Budget alerts exist but they're time-delayed — by hours. Spend caps exist but they're opt-in and default to off. By the time an alert fires, you can be looking at five figures in fraudulent charges. The €54k victim had a budget alert set at €80, which triggered — and they still ended up with €54,000 in charges because of the hours-long reporting lag.

As one HN commenter put it: "Spend caps exist for Gemini — they just default to OFF. For an API that can bill four figures per hour, opt-in safety by default isn't a UX choice, it's a billing strategy."

---

## Google's Position: Your Problem, Not Ours

What makes this story particularly grim is the aftermath for victims.

The €54k developer worked with Google Cloud support, provided logs and analysis showing the traffic was automated and anomalous, and had their refund request denied. The charges were classified as "valid usage" because they originated from their project.

The Japanese company facing potential bankruptcy got the same treatment. The solo developer who lost their startup to a $15k charge got the same treatment. Google's position is consistent: if the charges came from your project's key, you owe the money.

This is legally defensible. It's also deeply unreasonable given that Google:
1. Spent a decade telling developers those keys were safe to expose publicly
2. Changed the semantics of those keys without notifying existing users
3. Ships defaults that leave new users maximally exposed
4. Provides no real-time billing controls to stop runaway charges

Truffle Security shared their findings with Google before publishing. Google acknowledged the issue and said they'd "implemented proactive measures to detect and block leaked API keys." That's not nothing — HN commenters noted that most exposed keys they checked now return 403 errors with "Your API key was reported as leaked." But the detection is reactive, not architectural. The design is still broken, and victims are still appearing.

---

## Who Is Actually at Risk Right Now

This isn't a hypothetical future problem. You may be exposed today if:

**You have a Firebase project older than 2024** that you've recently added Gemini/Firebase AI Logic to. Your existing API keys — including any deployed in client-side code — now have Gemini access. Check your [Google Cloud Console](https://console.cloud.google.com/apis/credentials) and look at what APIs each key is scoped to.

**You followed Google's Maps JavaScript quickstart** and pasted an `AIza...` key into your HTML. If that project has Gemini enabled, that public key is a Gemini credential.

**You have any unrestricted API key in a project with Gemini enabled.** Even if you haven't published it anywhere, if it ended up in a git commit, a log file, a Slack message, or anywhere else, it's a live Gemini credential.

**You created a new Firebase AI Logic setup using the default quickstart flow** without explicitly restricting your key. The defaults leave you exposed.

---

## What To Actually Do

The fix is straightforward but requires you to actively do it — Google won't do it for you.

**1. Audit your API keys immediately.** Go to Google Cloud Console → APIs & Services → Credentials. For every API key, check the "API restrictions" column. "None (unrestricted)" means it can access every enabled API including Gemini. This is the wrong setting for any key that lives in client-side code.

**2. Restrict each key to only the APIs it needs.** A key used for Maps should be restricted to the Maps JavaScript API. It should not have access to Generative Language API. This is a two-minute fix per key.

**3. Set explicit spend caps on the Gemini API.** Go to Google Cloud → Billing → Budgets & alerts and set a hard budget. Then go to the [Gemini API billing settings](https://ai.google.dev/gemini-api/docs/billing) and set a monthly spend cap. Set it low. You can raise it. You cannot retroactively undo a €54,000 charge.

**4. Rotate any key that may have been publicly exposed.** If a key with Gemini access has ever been in a public GitHub repo, a deployed web app, or anywhere else a scraper could have found it, rotate it now even if you think the exposure was brief. Scrapers move fast.

**5. For new Firebase AI Logic projects:** Move Gemini API calls server-side. Client-side Gemini calls are fundamentally unsafe under the current key model. Server-side calls using service account credentials (not API keys) are the correct architecture. Yes, this is more work than the quickstart. That's the tradeoff.

---

## The Real Lesson

There's a tempting narrative here that blames developers for "not securing their keys." Don't accept that framing.

Google has an explicit, documented, long-standing policy that these keys are safe to expose. They changed the semantics of those keys when they launched Gemini. They did so without retroactively notifying users whose existing keys were now Gemini credentials. They shipped defaults that maximize exposure for new users. And when the predictable exploitation happened, they denied refunds on the grounds that the charges were technically valid.

This is a story about a platform that broke its contract with its developers — quietly, technically legally, with devastating financial consequences. The right response isn't just "secure your keys better." It's to recognize that Google Cloud's billing architecture for AI APIs is genuinely dangerous, that its defaults actively harm users, and that until Google fixes the fundamental design, the only safe option is to treat every `AIza...` key as a live credential regardless of how Google classified it when you created it.

The €54k developer is not the last victim. This will keep happening until either Google changes the defaults or enough developers know the risk.

Now you do.

---

*Primary sources: [Google developer forum post (€54k incident)](https://discuss.ai.google.dev/t/unexpected-54k-billing-spike-in-13-hours-firebase-browser-key-without-api-restrictions-used-for-gemini-requests/140262), [Truffle Security research](https://trufflesecurity.com/blog/google-api-keys-werent-secrets-but-then-gemini-changed-the-rules), [The Hacker News coverage](https://thehackernews.com/2026/02/thousands-of-public-google-cloud-api.html), [TechRadar coverage ($15k startup story)](https://www.techradar.com/pro/security/usd15k-bill-destroyed-a-solo-developers-startup-how-hackers-are-using-leaked-google-api-keys-to-go-wild-with-gemini-ai-for-free), [Reddit: Japanese company facing bankruptcy ($128k)](https://www.reddit.com/r/googlecloud/comments/1rv3xr9/we_are_facing_possible_bankruptcy_after/), [HN discussion](https://news.ycombinator.com/item?id=47791871).*
