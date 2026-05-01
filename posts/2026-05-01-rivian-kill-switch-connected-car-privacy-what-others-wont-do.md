# Rivian Added a Kill Switch for Your Car's Internet. No Other Major Automaker Will.

**Date:** 2026-05-01

A Rivian support page is sitting at the top of Hacker News today, and the reason it's there tells you everything about where we are with connected car privacy. The page is titled ["Can I disable all data collection from my vehicle?"](https://rivian.com/support/article/can-i-disable-all-data-collection-from-my-vehicle) The fact that the answer is *yes* — actually yes, as in a toggle in the settings menu — is apparently so unusual that it's cracking 300 upvotes on a forum full of people who expect control over their software.

That says a lot. And none of it is about Rivian.

## What Rivian Actually Offers

The short version: Canadian Rivian owners get a toggle in Settings → Data and Privacy. US owners have to call Rivian Service and ask them to physically disable the eSIM. That's not frictionless, but it's something. The car's cellular modem goes silent. No data leaves the vehicle.

The trade-offs are real and Rivian is transparent about them: you lose navigation, lane keeping assistance, and over-the-air updates. That last one is the kicker. OTA updates are how modern EVs get safety patches, bug fixes, and new features. Turning them off means driving a car that's frozen in time — any security vulnerability discovered after your last update stays unpatched until you go to a service appointment and reconnect.

One commenter on the HN thread raised the exact right question: what happens when a safety recall comes through as a software update and your eSIM is dark? Rivian doesn't have a clean answer. Dealers presumably have a physical path to update the firmware, but you'd have to know to show up, which requires... receiving a notification you can't receive.

So it's not a perfect opt-out. But it's honest about what it is.

## What Everyone Else Offers

Let's run the table:

**GM / OnStar**: In January 2026, the FTC [finalized a settlement](https://www.ftc.gov/news-events/news/press-releases/2026/01/ftc-finalizes-order-settling-allegations-gm-onstar-collected-sold-geolocation-data-without-consumers) requiring GM to stop selling precise geolocation and driving behavior data to consumer reporting agencies. The word "stop" is doing a lot of work in that sentence. GM had been selling data — location, speed, braking — collected as frequently as every few seconds. Insurers bought it. Some drivers saw their premiums spike without ever being told why. The FTC settlement bans this for five years and requires affirmative consent going forward. It does not include a monetary penalty, which tells you something about how regulators currently value your location data.

To actually stop data transmission on a GM vehicle, the old method was to physically pull the OnStar modem from under the dashboard. Some models were updated to disable the dash if you did that — a design choice that could charitably be described as a deterrent and uncharitably as coercion. The settlement now requires GM to give drivers a way to disable geolocation collection, but the implementation details are still being worked out.

**Tesla**: Tesla has a settings path (Software → Data Sharing) that lets you turn off "Autopilot Analytics" and "Road Segment Data Analytics." Tesla claims this data isn't connected to your VIN or account even when it is collected, which is a privacy-by-design argument that's hard to verify. A 2023 Reuters investigation found that Tesla employees had been sharing customer camera footage internally, which didn't exactly help the "we're not looking at your data" case. If you want to fully deactivate connectivity on a Tesla, you need to contact them — there's no in-car kill switch for the cellular connection itself. And Tesla is quite direct that opting out means losing over-the-air updates, remote services, in-car navigation, and internet radio. Their privacy policy describes "connectivity and performance" as "a core part of all Tesla vehicles."

**Stellantis (Jeep, Dodge, Ram, Chrysler)**: You have to call 800-800-2813 and say "Cancel for Privacy Reasons." There's no in-car option. The process requires creating a Connected Services account even if you've never wanted one. Fully disabling data transmission still requires physically disconnecting the telematics modem.

**Honda, Toyota, BMW, Ford**: Varying degrees of difficult. Most involve calling a number, clicking through account portals, or both. None of them offer a first-party, in-vehicle, single-tap option equivalent to what Rivian gives Canadian customers.

The pattern is consistent: making it inconvenient to opt out isn't accidental. These are revenue streams disguised as features.

## Why This Matters More Than It Did Last Year

The US government finalized its [connected vehicle rule](https://www.dlapiper.com/insights/publications/2026/04/connected-vehicles-rule-takes-effect) in early 2026, banning Chinese software in cloud-connected vehicle systems. Hardware bans follow in 2029. The national security frame is about preventing foreign adversaries from accessing the data your car generates — location traces, camera feeds, behavioral data. Automakers spent early 2026 [scrambling to audit their software supply chains](https://www.wsj.com/business/autos/the-car-industry-is-racing-to-replace-chinese-code-6b939e1f) to comply with the March deadline.

Here's the thing the national security framing obscures: if that data is sensitive enough that we ban Chinese companies from accessing it, it's sensitive enough that American companies shouldn't be selling it to data brokers either. The FTC's GM settlement acknowledged this, at least implicitly. But the settlement covers geolocation data shared with consumer reporting agencies. It doesn't cover driving behavior data sold to advertisers, or location data retained internally, or the full behavioral profile assembled by a company that knows everywhere you've driven for the past three years.

Your car has microphones, cameras, a GPS, accelerometers, and a persistent cellular connection. It knows when you leave the house and when you come home. It knows which restaurants you pass, which hospitals you've visited, and how fast you took that corner. Modern vehicles generate anywhere from 25 to 100 gigabytes of data per hour when all systems are active. Most of that stays on the vehicle, but the portions transmitted to the cloud paint a more complete behavioral picture than your phone does — and your phone is already pretty revealing.

## The Privacy-Safety Tradeoff Is Real, But It's Manufactured

The standard industry defense of mandatory connectivity is safety: automatic crash detection calls for help when you can't. Remote diagnostics can catch a brake failure before it becomes an accident. OTA updates can fix a steering bug in your sleep. These are real benefits, not invented ones.

But the "you must share everything to get any of it" framing is a choice, not a technical constraint. You could build a system where crash detection works locally and only dials out in an emergency. You could offer OTA updates without transmitting behavioral telemetry. You could separate safety features from data collection features at the architecture level.

Automakers haven't done this because selling driving data is a meaningful revenue stream — estimated at billions annually across the industry — and because the people who care about this tradeoff are, so far, a small minority of buyers. Most people buying an R1T aren't thinking about eSIM exfiltration. They're thinking about towing capacity and the frunk.

The HN crowd cares, though, and that's why this support page is blowing up. It's not that Rivian is a privacy-first company — they're not. They collect plenty. The page they wrote is actually a disclosure of everything they'll stop doing if you pull the plug, which is itself a form of transparency. What resonates is the existence of a legitimate off switch. The acknowledgment that the car belongs to you, and that you might have reasons to want it to go dark.

## What To Do If You Care About This

If you own a Rivian:
- **Canada**: Settings → Data and Privacy → toggle off. Simple.
- **US**: Call Rivian Service and request eSIM deactivation. Understand you're trading OTA updates and navigation for disconnection.

If you own a Tesla:
- Go to **Software → Data Sharing** and turn off Autopilot Analytics and Road Segment Data Analytics. This doesn't kill the cellular connection but reduces what's transmitted.

If you own a GM vehicle:
- The FTC settlement now requires GM to provide a geolocation opt-out. Check your current infotainment settings under Privacy, or call OnStar. The full modem removal route still exists for the determined.

For everyone:
- Factory-reset your car before selling it. This is the most often skipped step and the most consequential.
- Understand that your car's data has almost certainly already been sold. That's not paranoia — for GM owners, it's documented fact.

The Rivian kill switch isn't a revolution. But the fact that it's generating this much attention says something true: we've normalized cars as surveillance platforms, and any deviation from that norm — even a modest, friction-filled, features-degrading one — reads as a radical act.

It shouldn't have to.

---

*Sources: [Rivian support page](https://rivian.com/support/article/can-i-disable-all-data-collection-from-my-vehicle) | [HN discussion](https://news.ycombinator.com/item?id=47967786) | [FTC / GM settlement](https://www.ftc.gov/news-events/news/press-releases/2026/01/ftc-finalizes-order-settling-allegations-gm-onstar-collected-sold-geolocation-data-without-consumers) | [Connected vehicles rule – DLA Piper](https://www.dlapiper.com/insights/publications/2026/04/connected-vehicles-rule-takes-effect) | [WSJ: Car Industry Racing to Replace Chinese Code](https://www.wsj.com/business/autos/the-car-industry-is-racing-to-replace-chinese-code-6b939e1f) | [The Autopian: How to Stop Your Car Sharing Data](https://www.theautopian.com/heres-how-to-stop-your-car-from-sharing-your-data/)*
