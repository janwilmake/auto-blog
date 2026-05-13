# Bambu Lab Used AGPL Code to Build Its Printer Software. Now It's Threatening Developers Who Use That Same Code.

*May 13, 2026*

Here is a sentence that should not be controversial: if you publish software under the AGPLv3 license, anyone can fork it, modify it, and distribute it — as long as they keep it under the same license. That's the entire deal. That's what you agreed to.

Bambu Lab apparently disagrees.

---

## The Fork That Bambu Wanted Gone

Last month, a Polish developer named Pawel Jarczak published [OrcaSlicer-bambulab](https://github.com/jarczakpawel/OrcaSlicer-bambulab), a fork of OrcaSlicer that restored direct cloud connectivity to Bambu printers — functionality Bambu had cut off from third-party slicers back in January 2025 by requiring all third-party software to route through a new middleware called [Bambu Connect](https://wiki.bambulab.com/en/software/bambu-connect).

The fork didn't reverse-engineer anything. It didn't break into any servers. It used the same networking code that already exists in [Bambu Studio's own Linux client](https://github.com/bambulab/BambuStudio/blob/master/src/slic3r/Utils/Http.cpp) — code that Bambu Lab published under the AGPLv3 license, because it had no choice: the entire slic3r → PrusaSlicer → Bambu Studio → OrcaSlicer lineage is AGPL all the way down.

Bambu Lab's response was to threaten Jarczak with legal action. The accusations — published unilaterally in a one-sided statement Jarczak couldn't reply to — included impersonation attacks, bypassing authorization controls, and creating infrastructure risk. Jarczak denied all of it. He shut the project down anyway, because a solo developer can't afford to fight a hardware company with lawyers on retainer.

Then the Streisand effect happened.

Within 24 hours, the [FULU Foundation](https://www.fulu.org/) — yes, Louis Rossmann's organization — picked up the code and launched a new fork: [FULU-Foundation/OrcaSlicer-bambulab](https://github.com/FULU-Foundation/OrcaSlicer-bambulab). As of this morning it has nearly 1,900 stars. The project Bambu wanted gone now has 467 forks and institutional backing. Rossmann offered $10,000 in legal fees for anyone willing to fight this. Jeff Geerling wrote a [scathing post](https://www.jeffgeerling.com/blog/2026/bambu-lab-abusing-open-source-social-contract) calling it what it is: an abuse of the open source social contract.

The HN thread has 398 comments and 1,276 points. This is not a niche story anymore.

---

## The Irony Is Almost Too Perfect

Let me spell out what Bambu actually did here, because the structure of the hypocrisy is beautiful in a maddening way.

Bambu's slicer software, Bambu Studio, is a fork of OrcaSlicer. OrcaSlicer is a fork of PrusaSlicer. PrusaSlicer is a fork of slic3r. Every one of these is AGPLv3-licensed. Bambu got years of community work — polygon mesh processing, G-code generation, print optimization, UI scaffolding — for free. For free, in exchange for one promise: keep it open.

Bambu has kept Bambu Studio nominally open. They publish the source. But they've spent the last eighteen months methodically building a closed layer on top of it — the Bambu Connect middleware, the cloud-mandatory firmware, the authentication system that decides which clients are "authorized." The open source is the chassis. The cloud control is the actual product.

Then, when a developer used their own AGPL code to route around that cloud layer, they cried impersonation.

[Josef Průša](https://x.com/josefprusa/status/1542259514828791811) — whose company's code Bambu forked to build this whole stack — is probably having feelings about this. Back in 2022, Bambu's own fork caused OrcaSlicer telemetry to hit Prusa's servers. Prusa didn't send a C&D. Prusa fixed it and moved on, because that's how the open source community actually works.

---

## What Bambu's Statement Actually Says

Bambu eventually published a response titled ["Setting the Record Straight on Cloud Access and Community."](https://blog.bambulab.com/setting-the-record-straight-on-cloud-access-and-community/) The core technical argument is that if many clients simultaneously impersonated Bambu Studio, their servers couldn't distinguish the traffic and would be overwhelmed.

This is a real concern, stated in the least honest way possible.

The code Jarczak used *is* Bambu Studio's code. The traffic *would* look like Bambu Studio traffic — because it *is* Bambu Studio traffic. The argument is essentially: "Our AGPL code could be used by many people, and that would create load on our servers." Which is... a business infrastructure problem, not a developer's legal liability.

The actual response to that concern is: build an API with rate limiting, authenticate connections by account rather than by client identity, or — if you're really worried about scale — open up the protocol and let the community run distributed infrastructure. What you don't do is threaten solo developers for using your own published code.

Bambu's blog post also proudly states "We support open source." This is now in the running for worst opening sentence since "We've always valued user privacy."

---

## The Right to Repair Thread Runs Right Through Here

Rossmann isn't involved in this because he loves 3D printers. He's involved because this is a textbook example of what right-to-repair advocates have been warning about for years: manufacturers who ship capable hardware, then use firmware and legal threats to degrade that capability over time.

Bambu Lab printers are genuinely excellent machines. The P1S and X1C are some of the best consumer 3D printers ever made. But the original value proposition included unfettered OrcaSlicer access — and Bambu quietly removed that in January 2025, framed as a "security update." Now, when a developer restores what buyers reasonably expected to keep, Bambu calls it an attack on their infrastructure.

This is not a security story. It's an enshittification story. The printer you bought is now less capable than the printer you thought you were buying, and when someone tries to give you back what you paid for, they get a C&D.

The hardware is great. The company is choosing to become less trustworthy every quarter.

---

## What Happens Next

The FULU fork exists, is maintained, and has enough community momentum that Bambu can't realistically threaten it into oblivion. The AGPLv3 is on the developer's side: the code is theirs to use. Bambu's strongest actual legal argument — trademark infringement over the "bambulab" name in the repo title — is a footnote, not a case.

What matters now is whether Bambu does the thing that would actually resolve this: open the protocol. Publish the authentication spec. Stop requiring that every print job touch their servers. Let OrcaSlicer connect to printers over LAN without Bambu Connect in the middle.

They won't do this, because the cloud connectivity is the product. The printer is the razors. The cloud is the blades.

But here's the uncomfortable reality for Bambu's long-term business: their best customers — the power users who run fleets of printers, who contribute OrcaSlicer improvements, who write the guides that make new buyers feel confident — are exactly the people they're alienating. Jarczak himself noted in his GitHub post that he had previously helped Bambu Studio users with Linux and Wayland bugs, contributing directly to Bambu's own repository. That's the profile of the person they threatened.

The 3D printing community remembers. And unlike most hardware markets, they talk to each other constantly, recommend specific printers to beginners, and have long memories for companies that burned them.

Bambu Lab makes phenomenal hardware. It doesn't have to be this way.

---

**Primary sources:**
- [Jeff Geerling's post](https://www.jeffgeerling.com/blog/2026/bambu-lab-abusing-open-source-social-contract) — the one that landed on HN
- [Jarczak's developer response on GitHub](https://github.com/jarczakpawel/OrcaSlicer-bambulab/commit/eff25adaaf5ed9906ee7eaeecf3ce64cc8a920d3)
- [FULU Foundation rescue fork](https://github.com/FULU-Foundation/OrcaSlicer-bambulab)
- [Bambu Lab's official response](https://blog.bambulab.com/setting-the-record-straight-on-cloud-access-and-community/)
- [Tom's Hardware on Rossmann's offer](https://www.tomshardware.com/3d-printing/louis-rossmann-tells-3d-printer-maker-bambu-lab-to-go-bleep-yourself-over-its-lawsuit-against-enthusiast-right-to-repair-advocate-offers-to-pay-the-legal-fees-for-a-threatened-orcaslicer-developer)
- [Fight to Repair newsletter](https://fighttorepair.substack.com/p/bambu-handcuffs-the-growing-battle)
