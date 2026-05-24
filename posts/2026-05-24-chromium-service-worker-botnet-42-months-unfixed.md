# Google Sat on a "Turn Any Browser Into a Botnet" Exploit for 42 Months. Then Published It by Accident.

**2026-05-24**

Here's the timeline. In December 2022, a security researcher named Lyra Rebane reported a bug to Google's Chromium Issue Tracker. The bug: a malicious webpage can use the browser's Service Worker API to keep JavaScript running on your device indefinitely — even after you close the browser. One website visit, no clicks required, and the attacker now has persistent code execution on your machine. Rebane said explicitly in the report that this could be used to build a botnet from ordinary browser users, and that "people won't be aware that JavaScript can be remotely executed on their device."

Google marked the ticket valid. Google made the ticket private. Google did not fix the bug.

That was 42 months ago.

Last week, Rebane noticed that the exploit details had been published — publicly visible on the Chromium Issue Tracker. Google had accidentally made the ticket public while doing some housekeeping. The researcher's reaction, posted on Mastodon, was: "OH NO I JUST REALIZED THIS IS NOT ACTUALLY PROPERLY FIXED AND STILL WORKS."

Now the exploit code is loose, the bug is still present in Chrome, Edge, Brave, Opera, Vivaldi, Arc — every Chromium-based browser, which is most of the browser market — and Google is scrambling to issue an emergency patch before someone runs with it.

## What the Bug Actually Does

A Service Worker is a piece of JavaScript that browsers run outside the main page context. They're designed to handle background tasks — push notifications, offline caching, background sync. They're also the engine behind Progressive Web Apps. They're supposed to terminate when they're done, or when the browser closes.

This bug breaks that lifecycle. Via the browser's fetch API, a malicious page can register a Service Worker that never terminates. The connection keeps running. JavaScript keeps executing. The attacker maintains a live code execution channel on any device that visited the page, even after the tab is closed, even after the browser is closed.

On Chrome, at least there's a visual tell — a download dropdown that pops up when the exploit fires. On Microsoft Edge, Rebane found the dropdown no longer appears at all. The JavaScript runs completely silently. Close your browser, open your laptop tomorrow, and you're still a node in someone's botnet.

To be clear about the limits: this doesn't break the browser sandbox. The attacker's code runs inside the browser's process model, not the OS. They can't access your files or emails directly. But they can use your CPU and network: DDoS attacks, traffic proxying, redirecting your browser, phishing via the persistent service worker context. Rebane put it bluntly in 2022: tens of thousands of pageviews could build a practical botnet. That estimate hasn't changed.

## The Real Problem Is the 42-Month Clock

Everyone covers vulnerabilities as a "patch or don't patch" story. That's not the story here.

The story is that Google knew about this bug in 2022. They acknowledged it was valid. They marked it private (the standard procedure for security bugs in Chromium — you don't want to hand attackers a blueprint). And then... nothing. For three and a half years.

Chromium has had well over a hundred releases since December 2022. This issue has been sitting on someone's backlog, presumably marked "fix in progress" or "needs design review" or whatever label organizations use to let things rot. Meanwhile, the web has moved toward more aggressive Service Worker usage, not less. The attack surface has grown. The number of Chromium users has grown.

And yes, Google publishes exploit code accidentally sometimes. But when an accidentally-published ticket causes a researcher to post "OH NO I JUST REALIZED THIS IS NOT ACTUALLY PROPERLY FIXED," that's not a disclosure accident. That's a culture-of-indefinite-deferral accident.

## Why Chromium's Security Process Is Structurally Biased Toward Delay

Google is good at patching zero-days fast when those bugs are actively exploited. CVE-2026-3910 in V8 last March — patched in days. That's the visible, embarrassing, newspaper-headline kind of bug.

This bug was different. No exploitation in the wild (that we know of). No CVE assigned. No public pressure. Just a ticket in a tracker that said, essentially: "anyone could run this as an attack, but no one has yet." The security team's implicit cost-benefit calculation was: the marginal risk of exploitation is low, the fix requires touching Service Worker lifecycle code (notoriously complex), let's deprioritize.

This is a completely predictable result of how security teams work. They triage by urgency, not by potential severity. A potential botnet that hasn't been used yet is lower priority than an active zero-day being exploited by nation-states. Rationally, that makes sense in the moment. Multiply it across hundreds of bugs across dozens of teams over years, and you get: a 42-month-old unfixed exploit in the most widely deployed browser engine in the world.

The irony is that making the ticket private provided a false sense of security. Google knew. The bug existed. The only thing the private ticket prevented was outside researchers finding and fixing it independently, or the public putting pressure on Google to prioritize it.

## What You Should Actually Do

If you're a regular user: not much, frankly. Wait for the emergency patch. Enable automatic updates. This will be fixed within days now that the details are public — public embarrassment is the fastest bug triager in the industry. The risk in the next few days is real but limited; building a large-scale botnet operation takes more than a weekend.

If you run servers that use headless Chromium (and many do — PDF generation, web scraping, screenshot services, Puppeteer/Playwright test runners, CI pipelines): patch immediately, or disable your browser automation jobs until a fix lands. Headless Chrome doesn't necessarily have the user-interaction signals that might limit exploitation in practice. If you're rendering untrusted URLs with headless Chrome, you're at elevated risk right now.

If you're a Chromium contributor or work at Google: this bug should probably have been a `High` or `Critical` from the moment Rebane provided a working PoC. The "no in-the-wild exploitation yet" heuristic for deprioritization needs to be weighed against "no user interaction required and affects every Chromium browser on Earth." The denominator matters.

## The Lyra Rebane Problem

There's a researcher-ecosystem angle here that's worth naming. Rebane reported this in 2022. She did everything right — responsible disclosure, gave Google the ticket, stayed quiet, watched for the fix. She waited 42 months. The only reason this is public now is because Google accidentally published it, not because Google fixed it and credited her.

That's a bad outcome for everyone who wants to encourage responsible disclosure. The implicit promise of responsible disclosure is: tell us, give us time to fix it, and we'll handle it. What actually happened: you told us, we sat on it for three and a half years, and now it's public because we made a mistake.

Bug bounties and disclosure policies don't fix this. The fix is having internal processes that treat no-click, no-interaction, persistent-execution vulnerabilities as critical regardless of current exploitation status. Whether Google updates those processes after this incident, or treats it as an isolated disclosure-operations glitch, is the thing worth watching.

The exploit details are out. The patch is coming. But the 42 months is a fact.

---

*Primary sources: [BleepingComputer](https://www.bleepingcomputer.com/news/security/google-accidentally-exposed-details-of-unfixed-chromium-flaw/), [Ars Technica](https://arstechnica.com/security/2026/05/google-publishes-exploit-code-threatening-millions-of-chromium-users/), [Lyra Rebane on Mastodon](https://infosec.exchange/@rebane2001/116606836889483917)*
