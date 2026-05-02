# Texas Instruments Just Made a $160 Device That Deliberately Can't Connect to the Internet. That's the Product.

**Published: 2026-05-02**

Texas Instruments [announced the TI-84 Evo](https://education.ti.com/en/products/calculators/graphing-calculators/ti-84-evo) on April 28th, and the Hacker News thread immediately cracked 200 comments. On a Saturday. About a calculator.

That tells you something.

The Evo is, by any hardware measure, a real upgrade: the old ez80 CPU is finally retired after 30-plus years, replaced by an ARM Cortex running at 156 MHz — three times the clock speed of the TI-84 Plus CE's 48 MHz chip. There's 50% more graphing area, USB-C charging, an icon-based home screen, and a redesigned keypad. It's approved for the ACT, SAT, AP, and IB exams. It costs $160.

It also has no Wi-Fi. No Bluetooth. No cellular. No notifications. No apps you didn't explicitly install for math. According to the [TI press release](https://www.ti.com/about-ti/newsroom/news-releases/2026/2026-04-28-texas-instruments-launches-the-ti-84-evo-graphing-calculator----the-most-advanced-ti-84-ever-built.html), it is "designed to do one thing exceptionally well — math."

In 2026, that's a positioning statement, not a spec sheet limitation.

## The Actual Interesting Move Here

People keep framing this as TI "updating a legacy product" or "keeping the calculator racket alive." But that misses what's happening.

We're in a moment where the dominant tech narrative is *add AI to everything*. Calculators? Your calculator should just... ask an AI. Watches? They should run LLMs locally. Refrigerators? Obviously. The pressure on every product category is to become a general-purpose compute surface connected to the cloud. Anything that resists this is "legacy."

TI just looked at that cultural moment and said: no, actually, single-purpose is a feature, not a bug. And they backed it with a genuine hardware upgrade to make the point land. If the Evo were just the same old calculator in new colors, nobody would care. But it's *faster*, *better*, *more capable* — and still deliberately offline. That combination is a statement.

It's the same logic behind [the Light Phone](https://www.thelightphone.com/), behind the boom in [sales of old-fashioned blue books](https://www.wsj.com/business/chatgpt-ai-cheating-college-blue-books-5e3014a6) as AI cheating tools proliferate, behind the weirdly successful Tin Can landline alternative. A growing market segment is actively choosing *not* to have AI assistance on hand for certain activities, and they're willing to pay for hardware that enforces that choice.

## The Education Angle Is Real

TI's press release mentions that [45 states have moved to restrict cell phones in classrooms](https://www.prnewswire.com/news-releases/texas-instruments-launches-the-ti-84-evo-graphing-calculator--the-most-advanced-ti-84-ever-built-302755962.html). That's not TI spin — it reflects an actual policy wave. The attention crisis in K-12 education is severe and well-documented. Teachers are dealing with a generation of students for whom every idle moment is a potential scroll session, and standardized test bodies are running out of ways to prevent calculator apps on smartphones from becoming AI tutoring sessions during exams.

The TI-84 Evo solves this cleanly. It's exam-approved by every major body, it can't run ChatGPT, and a teacher can look at it from across the room and immediately verify it's a calculator and not a phone. The form factor itself is the verification mechanism.

That's not a trivial thing. One of the hardest problems in ed tech right now is *trust* — how do you know what the student is actually doing on the device? The Evo sidesteps the problem entirely by being physically incapable of the bad behavior. You don't need a software policy. The hardware is the policy.

## But $160 Is Still Absurd

Let's be honest: TI has been a near-monopoly in graphing calculators for standardized testing for decades, and the pricing has never been competitive. The [HN thread](https://news.ycombinator.com/item?id=47979583) is full of complaints about this, and they're not wrong. A 156 MHz ARM chip costs pennies. The display, the flash memory, the keypad — none of this justifies $160 when a Raspberry Pi with more compute sells for $15.

TI's "value" here isn't the bill of materials. It's the regulatory moat: the device is approved for the SAT, ACT, and AP exams. That approval is worth more than any processor, because it means parents and students *must* buy something on the approved list. Without certification, a better, cheaper device from a competitor doesn't matter — you can't bring it to the test.

This is the calculator business. It always has been. The Evo is a legitimately better calculator, but TI's real product is the certification, not the hardware.

## The ARM Transition Matters More Than It Looks

Here's the technical detail that's getting underplayed: [according to Cemetech's analysis](https://www.cemetech.net/news/2026/4/1062/_/ti-84-evo-calculator-released-fast-graphing-new-ui-new-hardware), TI appears to have completely re-implemented the OS natively on the ARM Cortex rather than running an ez80 emulator for backward compatibility. That's a genuine break from over 30 years of the same underlying architecture.

The TI-83 launched in 1996 with a Zilog z80 CPU. The TI-84 Plus CE, released in 2015, used an ez80 — still in the z80 family, still running a lineage of code tracing back three decades. Every TI calculator since then has been, at some level, emulating or extending a 1990s computing paradigm.

The Evo breaks that chain. ARM is modern, efficient, and has a rich software ecosystem. In theory, this means future OS updates and new features become far easier to develop. It also means the community calculator scene — which has been hacking TI devices for games and custom programs for years — will have to completely rebuild its toolchain. That community will figure it out. But the transition is real.

## "Intentionally Dumb" Is Not an Insult Anymore

The Popular Science headline called the Evo ["intentionally dumb"](https://www.popsci.com/technology/new-texas-instruments-graphing-calculator-dumb/) and meant it as praise. That framing would have been strange ten years ago. In 2026, it's obvious.

We've watched what happens when you give students powerful general-purpose computers and tell them to only use them for math. They play games. They message each other. They ask LLMs to do their homework. The connected device has its own gravity, and that gravity pulls against focused work. Designing around that gravity — rather than trying to manage it with software policies that always get gamed — is increasingly the smart move.

TI understood this before most ed tech companies. They've been building the same deliberate limitation into their products for 30 years, not because they couldn't do better, but because that limitation *is* the product. The Evo is just the first time they've articulated it clearly and backed it up with hardware that's genuinely worth buying.

The question isn't whether TI's monopoly pricing is fair (it isn't). The question is whether a $160 device that forces you to do math, and only math, has real value in a world full of AI that will do the math for you if you let it.

For a lot of teachers, and a lot of students studying for the SAT, the answer is yes — and they'll pay for it.

---

*Sources: [TI-84 Evo product page](https://education.ti.com/en/products/calculators/graphing-calculators/ti-84-evo) · [TI press release](https://www.ti.com/about-ti/newsroom/news-releases/2026/2026-04-28-texas-instruments-launches-the-ti-84-evo-graphing-calculator----the-most-advanced-ti-84-ever-built.html) · [Cemetech technical breakdown](https://www.cemetech.net/news/2026/4/1062/_/ti-84-evo-calculator-released-fast-graphing-new-ui-new-hardware) · [Popular Science coverage](https://www.popsci.com/technology/new-texas-instruments-graphing-calculator-dumb/) · [Hacker News thread](https://news.ycombinator.com/item?id=47979583)*
