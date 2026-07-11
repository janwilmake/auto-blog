# Apple Sued OpenAI Yesterday. Read the Actual Complaint Before You Have an Opinion.

**2026-07-11**

Apple filed a federal lawsuit against OpenAI on Friday, and almost every headline got it wrong in the same direction. They called it a "trade secret dispute." That's technically accurate and almost completely misleading.

Read [the actual complaint](https://daringfireball.net/misc/2026/06/Apple_Inc._v._Chang_Liu_et_al.pdf) — all 41 pages, filed as *Apple Inc. v. Chang Liu et al.* in the Northern District of California — and you'll find something much weirder: a detailed account of a hardware insider operation that reads less like a civil dispute and more like an industrial espionage thriller, complete with a "LOL" text sent over a stolen laptop after exploiting an internal authentication bug.

## What Actually Happened

Chang Liu spent eight years at Apple as a senior system electrical engineer, working on undisclosed product development programs. He left in January 2026 to join OpenAI. When Apple contacted him for the standard exit procedures — return the laptop, sign the confidentiality reminder, do the exit interview — he didn't respond. He kept the laptop.

Within hours of leaving, according to messages Apple found on a *former colleague's* Apple-issued work device, Liu messaged that colleague: "I still have another computer." He then used it to access Apple's network. Then he used *her* Apple-authenticated work laptop, without authorization. Then, while employed at OpenAI and working on OpenAI's hardware, he discovered an authentication bug in Apple's systems that gave him access to Apple's shared network folders.

He did not report it. He did not delete the access. He sent a message that read "LOL" and that it was "so funny."

Over several weeks — again, while on OpenAI's payroll — he downloaded dozens of confidential Apple hardware files: unreleased product documentation, engineering presentations, technical specs, manufacturing data for main logic boards. He coached his colleague, who was interviewing at OpenAI, on which confidential Apple materials to study beforehand and how to copy files to "avoid trouble with the security team." He told her to move their conversation to a private app.

Apple's footnote in the complaint notes they fixed the authentication bug after discovery. They also note that other users inadvertently affected by the same bug apparently didn't exploit it.

## Tang Tan: The More Interesting Defendant

The Liu situation is damning on its own, but Tang Yew Tan — OpenAI's current Chief Hardware Officer — is the more structurally significant defendant.

Tan spent 24 years at Apple. His most recent title was VP of Product Design for iPhone and Apple Watch. He was one of four founders (alongside Jony Ive, Scott Cannon, and Evans Hankey) of io Products, the hardware startup OpenAI [acquired for $6.5 billion in mid-2025](https://openai.com/sam-and-jony/). Tan is now the most senior hardware executive at OpenAI.

Apple's allegations against him are more corporate than technical. According to the complaint:

- He coached current Apple employees interviewing at OpenAI to bring "actual parts" from their work to show at "show and tell" sessions with the OpenAI team.
- He used his knowledge of Apple's exit procedures to help people depart while still accessing systems.
- He improperly retained a managers-only internal document detailing how Apple handles employee departures — marked "Need to Know."
- He obtained information about a "specific trade secret metal-finishing technique" from an Apple vendor, falsely claiming he had Apple's permission to access it.
- He directed a candidate (the same colleague Liu was coaching) to bring batteries, SIPs, logic boards, and other hardware components to her interview.

Apple's lawyers put it plainly: "This is part of OpenAI's strategy to extract Apple's confidential information."

## The Partnership That Was Already Rotting

This lawsuit doesn't come out of nowhere. The Apple-OpenAI partnership — where ChatGPT is integrated into Siri — was already known to be [souring by May 2026](https://www.macrumors.com/2026/07/10/apple-sues-openai/), with OpenAI reportedly considering its own legal action against Apple for failing to deliver on promised integration traffic.

What the complaint adds is context for *why* the relationship degraded: while the two companies were partners in AI software, OpenAI was simultaneously poaching Apple's most senior hardware engineers and allegedly using the transition period to extract institutional knowledge about Apple's manufacturing, supply chain, vendor relationships, and unreleased products.

The partnership, in other words, was also a data leak. Apple was showing OpenAI what a functioning AI-hardware integration looked like, and OpenAI's hardware team was apparently extracting the manufacturing playbook to build a competitor.

Jony Ive — whose name is everywhere in this story, as io Products co-founder and now OpenAI's creative director — is conspicuously *not* named as a defendant. Neither is Sam Altman, though he's referenced. Apple's lawyers were precise: this suit targets specific acts of misappropriation, not the overall business relationship. That might change in discovery.

## The Real Stakes: The Hardware Device

OpenAI has been building a consumer hardware device [for over two years](https://www.axios.com/2026/01/19/openai-device-2026-lehane-jony-ive). The device — delayed to 2027 according to a February court filing — is supposed to represent a new "third category" of AI hardware, something beyond the phone and the laptop. Sam Altman has described it as something that will "know everything you've ever thought about, read, said."

Apple's legal theory is that this device is "rotten to its core" because its development relied on misappropriated trade secrets: Apple's supplier relationships, its manufacturing techniques, its design and component specifications, and its understanding of how to build consumer-grade AI hardware at scale.

If Apple gets the preliminary injunction it's asking for — stopping OpenAI from using any of its trade secrets — the hardware program is in serious trouble. If it doesn't, the lawsuit is still a prolonged discovery process that will force OpenAI to disclose what information it actually used, and when.

Apple is also seeking damages for "unjust enrichment," which in federal trade secret cases can be significant. The Defend Trade Secrets Act, under which this is filed, allows for exemplary damages up to twice the actual damages when misappropriation is willful.

## What to Actually Watch

There are a few things worth following that the headlines won't tell you:

**Can Apple get the preliminary injunction?** This is the big near-term question. To get one, Apple has to show irreparable harm and a likelihood of success on the merits. The complaint is detailed enough to clear that bar, but OpenAI will argue the information is no longer confidential or that Apple can't prove causation. This will move fast.

**What does discovery reveal about the scope?** The complaint focuses on Liu and Tan, but Apple says "other former Apple employees who had gone to work for OpenAI emailed themselves Apple's confidential information to personal accounts on their way out the door." That's a hint that this is bigger than two people.

**What happens to the ChatGPT-Siri integration?** Apple's lawyers were careful to note the partnership agreement "is not at issue in this lawsuit." That's a statement of current posture, not prediction. A prolonged legal fight has a way of contaminating adjacent relationships.

**Does this delay OpenAI's hardware?** The device was already pushed to February 2027. Even without an injunction, the lawsuit forces OpenAI to audit what information went into the hardware program — and what it can no longer use. That's an engineering audit on a deadline, for a product that already slipped once.

---

The deeper story here is what Silicon Valley's talent market looks like when two companies go from partners to hardware competitors. Apple spent a decade building the institutional knowledge to manufacture consumer electronics at scale — the supplier relationships, the manufacturing techniques, the materials science. That knowledge lives in people. When those people leave for a well-funded competitor, the question was always whether the knowledge would go with them. The complaint suggests the answer is yes, and that it wasn't accidental.

OpenAI wanted to be in hardware. They bought the $6.5 billion company, hired the talent, and built the partnerships. Apparently some of that also involved downloading Apple's internal engineering files over a stolen laptop. That's not innovation. That's just theft.

---

*Primary sources: [Apple's complaint (PDF)](https://daringfireball.net/misc/2026/06/Apple_Inc._v._Chang_Liu_et_al.pdf) · [AP report via ABC News](https://abcnews.com/Technology/wireStory/apple-files-lawsuit-accusing-chatgpt-maker-openai-stealing-134663221) · [MacRumors](https://www.macrumors.com/2026/07/10/apple-sues-openai/) · [Courthouse News](https://www.courthousenews.com/articles/apple-sues-openai-over-trade-secret-theft)*
