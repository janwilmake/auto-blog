# Flipper One Just Announced. They Called Their Own Project "Terrifying." That's Actually the Right Thing to Say.

*Published 2026-05-21*

Flipper Devices posted the Flipper One announcement this morning and it went straight to the top of Hacker News — not once, but twice. The [main blog post](https://blog.flipper.net/flipper-one-we-need-your-help/) got 376 points; the [raw tech specs doc](https://docs.flipper.net/one/general/tech-specs) hit 451. Together that's one of the bigger organic open-source hardware moments the front page has seen in a while.

The headline of the post is "We need your help." Not "We're shipping next quarter." Not "Pre-orders open now." The founder Pavel Zhovner leads with *"Honestly? We're genuinely terrified."* If you've been watching hardware crowdfunding disasters since the Pebble era, that kind of honesty is either a huge red flag or a refreshing change. I think, this time, it's the latter.

## What Flipper One Actually Is

First, the confusion to dispel: Flipper One is *not* Flipper Zero 2.0. People keep expecting a more powerful version of the Zero — better sub-GHz radio, improved NFC, same hacker-multitool form factor. That's not what this is.

Flipper One is a **Linux cyberdeck**. A pocket computer. The spiritual successor isn't the Zero; it's the idea that you should be able to carry a real, capable Linux machine the size of a thick wallet and plug anything into it.

The hardware is genuinely impressive on paper:

- **CPU**: Rockchip RK3576 — octa-core (4× Cortex-A72 + 4× Cortex-A53, up to 2.2 GHz), Mali G52 GPU, 6 TOPS NPU
- **MCU**: RP2350B (the same chip in the Raspberry Pi Pico 2) handling UI, buttons, display
- **RAM/Storage**: 8 GB LPDDR5, 64 GB UFS 2.2 internal
- **Connectivity**: 2× Gigabit Ethernet, Wi-Fi 6E, Bluetooth 5.2, full-size HDMI 2.1 (4K@120Hz), 2× USB-C (USB 3.1), USB-A
- **Expansion**: M.2 Key-B slot with PCIe 2.1, USB 3.1, SATA — meaning you can add an SDR, a 5G modem, a proper SSD, whatever
- **Battery**: 7,000 mAh (24 Wh)
- **Display**: 256×144 monochrome LCD (yes, intentionally simple — the MCU handles it while Linux does real work)

The co-processor split is clever. The RK3576 runs Linux; the RP2350 handles all the physical interface stuff — buttons, touchpad, screen, haptics. If Linux crashes, you can still reboot from the hardware layer without touching the main CPU. That's not a new concept, but it's rare to see it done right in a consumer-priced device.

## The Radical Part: No Radios By Default

Here's what will infuriate some people: the Flipper One ships with *no sub-GHz radio, no RFID, no NFC, no infrared* built in. None of the things that made the Zero famous — and infamous — are in the base device.

This is a deliberate strategic decision, and it's actually the smart call.

The Flipper Zero was [banned in Canada](https://www.cbc.ca/news/politics/canada-ban-flipper-zero-device-1.7117102), pulled from Amazon, and had units seized at border crossings — not because it could do anything a $30 SDR dongle couldn't, but because the marketing (and a bunch of TikTok videos) made politicians scared of it. A device that can "steal your Tesla" sounds like a threat even when the reality is it mostly let you read hotel key cards you already own.

By making radios modular M.2 add-ons, Flipper One can potentially be sold in markets that banned the Zero. The base device is just a small Linux computer with lots of ports. That's a category that doesn't scare airport security. You can still add every radio you want — you just have to buy the module.

Whether this actually works legally is TBD. But as a product strategy, moving from "hacking toy" to "open Linux platform that happens to support radio modules" is the right direction if they want to scale beyond hobbyist niches.

## The Part That Could Actually Matter: Mainline Linux Kernel Support

This is buried in the blog post but it's the most ambitious goal on their list:

> Build the most open and best-documented ARM computer in the world, with full mainline Linux kernel support.

That's a big claim. Most ARM SBCs ship with heavily forked kernels that are years behind upstream and never get merged into mainline. Rockchip devices in particular have a history of out-of-tree patches and binary blobs for GPU and VPU acceleration that make them frustrating to maintain long-term.

Flipper says they want to push changes upstream and pressure vendors to open their code. The RK3576 mainline support section in their developer docs shows they're actively tracking what's in upstream and what isn't.

If they actually pull this off — if Flipper One becomes a device you can run a stock Debian or Arch kernel on with full hardware support — that's genuinely valuable for the whole ecosystem, not just their product. That's the kind of work that benefits every other RK3576 device that comes after it.

Whether a scrappy hardware startup can actually move Rockchip's binary blob strategy is another question. But the stated goal matters.

## The "Developer Portal" Is the Real Bet

The other unusual thing about today's announcement: they're not asking for money. They're not opening a Kickstarter. They're asking for *contributors*.

Flipper's developer portal ([docs.flipper.net/one](https://docs.flipper.net/one)) is a public wiki with internal task trackers, architectural debates, half-finished documentation. Publicly editable. Deliberately messy. The goal is to develop in the open and pull in community talent at the design stage, not after launch.

This is how Flipper Zero's community actually worked in practice — the unofficial firmware ecosystems (Unleashed, Momentum, Xtreme) were better maintained than the official firmware for years. Flipper One is trying to formalize that dynamic from day one instead of fighting it later.

It's also a hedge against their stated fear: they genuinely might not be able to ship this. Zhovner is explicit about that. If the community is deeply involved in the architecture, the project has a better chance of continuing even if Flipper Devices hits financial trouble, gets banned again, or makes a bad hardware bet.

## The Vaporware Question

Let's be honest: Flipper One has been announced before. In softer terms, in internal Telegram posts, in leaked renders. The XDA deep-dive from March noted that prototypes existed but called the launch date uncertain. There were widespread "it might never ship" takes as recently as February.

What's different today is the specificity. The tech specs page has real numbers, real chip names, real connector pinouts. The GitHub repos have compilable firmware. Community members have already built the Linux image and booted it. This isn't a render with no silicon behind it.

The honest risk is price. Estimated range floating around is $300–$500 when it actually ships, and DRAM pricing has been rough. At $350 you're competing with a used ThinkPad that runs a real X86 Linux ecosystem and has no weird co-processor architecture to learn. The pitch has to be form factor + modular radio expansion + deep open-source commitment. That's a real pitch, but it's a niche one.

## Why This Matters Beyond the Device

The Flipper One announcement is fundamentally a statement about what kind of hardware you can build if you aren't trying to hide your supply chain and lock users into an ecosystem.

Most "hacker hardware" in 2026 is actually pretty locked down. Even devices that run Linux often have closed-source bootloaders, undocumented modem firmware, and GPU blobs that mean you're always running someone else's code below your kernel. The supply chain is deliberately opaque because openness means competitors can clone you.

Flipper is betting the opposite: that openness is the moat. That a community of developers who understand and trust the hardware will build more value than any secrecy could protect. The open developer portal, the mainline kernel push, the public task tracker — these aren't just PR. They're a theory of how to build a hardware business in an era when anyone can clone a circuit board.

Whether that theory survives contact with manufacturing reality is the story of the next 12 months. But the announcement today is the most interesting open hardware moment since the RISC-V tapeout community started proving people wrong about what open silicon could do.

I'm skeptical on timeline. I'm not skeptical about the ambition.

---

*Primary sources: [Flipper One announcement blog post](https://blog.flipper.net/flipper-one-we-need-your-help/), [tech specs](https://docs.flipper.net/one/general/tech-specs), [developer portal](https://docs.flipper.net/one), [XDA firmware deep-dive](https://www.xda-developers.com/dug-into-flipper-one-firmware-not-flipper-zero-sequel/)*
