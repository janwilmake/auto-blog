# You Walked Around Scanning Things So Drones Could Navigate. Niantic's "Voluntary" Defence Misses the Point.

**Date:** 2026-06-13

Here's the pipeline, laid out clearly: Hundreds of millions of Pokémon Go players spent years holding up their phones to scan parks, street corners, and building facades to earn in-game rewards. Niantic accumulated roughly 30 billion of those environmental scans. Niantic Spatial — the spin-off that took ownership of that data when Niantic sold its gaming portfolio to Scopely — used those scans to train a camera-based navigation model that lets a machine locate itself using visual cues when GPS fails. In December 2025, Niantic Spatial announced a partnership with [Vantor](https://dronexl.co/2026/06/09/pokemon-go-scans-niantic-vantor-military-drone-navigation/) (formerly Maxar Intelligence), a US defence contractor, to fuse that ground-level positioning system with Vantor's aerial drone navigation software for GPS-denied military operations.

Most players had no idea.

This story broke in Dutch newspaper *Trouw*, got picked up by [The Guardian](https://www.theguardian.com/technology/2026/jun/12/pokemon-go-data-trained-ai-that-could-assist-military-drones-in-war-zones), and hit the top of Hacker News this morning. And the debate online is already fractured across the predictable fault lines: *It's in the ToS!* vs. *Nobody reads ToS!* vs. *This is technically a stretch!* I want to slow down on each of those, because they all contain something real — and they all miss something.

---

## Vantor's Non-Denial Denial Is the Tell

When *Trouw* asked Vantor directly whether its military navigation system uses Pokémon Go imagery, the company said it "would not use" the game's data. Then it declined to say whether the model it's deploying was *trained* on those scans in the past. Niantic Spatial, responding separately, confirmed the scans were used to train an "early version" of the navigation model — but said it had nothing new to share about the Vantor partnership specifically.

This is classic trained-on-but-not-deployed corporate hedging, and it matters technically. There's a real distinction between:

1. Using the Pokémon Go scans as a **live reference database** (drone aligns its camera feed against stored imagery of a specific street to determine its position)
2. Using the scans to **train a model** that learns generalizable visual navigation skills, then deploying that model somewhere else

Niantic Spatial is clearly claiming #2. And some engineers in the HN comments push back hard on whether civilian smartphone footage of European parks meaningfully transfers to anything resembling a combat theatre. GPS jamming is primarily an urban problem — but Pokémon Go scans are mostly suburban and tourist-heavy, not the kind of dense urban terrain where electronic warfare currently operates at scale.

That's a technically legitimate challenge to the headline. The problem is: the technical limitation doesn't resolve the ethical issue. It doesn't matter whether *these specific scans* helped a drone navigate *that specific city*. What matters is the structure of the arrangement.

---

## The Consent Problem Isn't That Nobody Read the ToS

The standard corporate response has already arrived. "AR Scans collected through Pokémon Go were submitted voluntarily by players who opted into the feature and were subject to the applicable Terms of Service and Privacy Policy at the time," Niantic Spatial told both *Trouw* and The Guardian.

This is technically true. The scanning feature was opt-in. Players who used it agreed to terms that permitted Niantic broad rights over the data.

But Iris Muis, a data ethics researcher at Utrecht University, put her finger on the actual problem: *a user cannot picture how their data might be used later*. A player in Rotterdam scanning a canal bridge in 2021 to get bonus PokéCoins was not making an informed choice about contributing to GPS-denied drone navigation training data. They were playing a game. The gap between "you agreed to our terms" and "you consented to this specific use" is enormous — and growing.

This isn't a new observation. We've had this argument about consumer data and unexpected downstream uses for twenty years. But the Niantic case makes the abstract concrete in a way that's genuinely striking:

- A consumer product, designed to be fun and approachable
- A population of players that skews young and who engaged with a feature framed as a bonus game mechanic
- Data with a 10+ year shelf life repurposed for a use most players would consider categorically different from anything they imagined
- Players from countries that are not the United States — including Dutch players who are now being told their scans potentially contributed to US military readiness

That last point is being somewhat underplayed. Pokémon Go had global reach. A significant portion of those 30 billion scans came from Europe, Asia, and elsewhere. Those players weren't consenting to support US defence contractors. They were playing a game.

---

## The "It Was Optional!" Defence Has Limits

Several commenters on HN have pointed out that the AR scanning feature was always opt-in and only ever used by a fraction of players. That's true. The feature was also removed entirely earlier this month as part of the transition to Scopely ownership. Niantic Spatial confirmed that "Pokémon Go data is not shared with Niantic Spatial" under the new structure.

But "it was optional" as a defence assumes the player understood what they were opting *into*. And "we've stopped now" doesn't undo a decade of collection.

There's a structural issue here that goes beyond any single game. [Meta's smart glasses continuously scan surroundings](https://www.theguardian.com/technology/2026/jun/12/pokemon-go-data-trained-ai-that-could-assist-military-drones-in-war-zones). Apple's Vision hardware builds 3D interior models. Waymo's fleet reconstructs street-level geometry at city scale. These systems are collecting spatial data about the physical world at a pace and precision that makes the Pokémon Go project look rudimentary. All of them have terms of service. All of those terms reserve broad rights.

TU Delft professor Jeroen van den Hoven put the Niantic case in simple terms: "Without the huge number of scans from all those gamers, the development of this system would never have progressed so quickly. The players have indirectly, perhaps minimally but still effectively, contributed to military applications."

The professor is being generous with "minimally." The *speed* argument is significant. Not that Pokémon Go data was irreplaceable — eventually someone would have collected enough street-level imagery to train a similar model. The issue is that gamification dramatically accelerated the collection process, at near-zero cost, from a population that had no idea what they were building.

---

## The Pattern Is the Problem

This is the same structural dynamic we've seen repeatedly with dual-use data:

- Street View faced backlash when its imagery was used in Israeli military planning tools
- Ring doorbell footage was shared with police without user notification  
- Fitness tracker routes mapped classified military bases
- reCAPTCHA trained OCR models and AI navigation under the guise of bot-blocking

Each time, the company points to terms of service. Each time, the gap between technical consent and informed consent is enormous. Each time, the data application is something most users would not have predicted or endorsed.

Niantic Spatial's case is notable not because it's unprecedented but because it's vivid. There's a direct, named pipeline from a beloved consumer game to a US defence contractor preparing drones for GPS-denied military operations. The company's spokesperson declined to answer the central question directly. A professor who reviewed the case said the players effectively contributed to military technology development. And the feature has now been quietly shut down.

---

## What Should Actually Change

The honest policy conclusion isn't "don't collect data." Spatial AI is going to get built, with or without gamified collection. The question is whether informed consent can be made real when the future use of data is genuinely unknowable.

Adrian Hon, British game designer and *Pokémon Go* expert, has advised players to stop making scans and to favour smaller games unlikely to resell data to defence contractors. That's practical individual advice, but it doesn't scale.

The more durable fix is binding purpose limitation: data collected for a specific purpose (improving AR game features) should not be reusable for categorically different purposes (military navigation training) without renewed, specific consent. The EU's GDPR gestures at this with its purpose limitation principle, but enforcement has been patchy, especially for companies headquartered outside the EU.

What's notably absent from the conversation is any regulatory action. No EU data protection authority has opened an investigation. No US congressional hearing is scheduled. The Dutch players who are most directly implicated have no clear avenue for redress.

And in the meantime, Vantor is a $70 million prime contractor to the National Geospatial-Intelligence Agency, the Raptor software is moving toward field deployment, and Niantic Spatial is looking for more indoor footage from other robotics deals — including delivery robots already rolling through US and European cities.

The scans are in the model. The model is going into drones. The players are still playing other games, scanning other things, agreeing to terms they haven't read.

---

*Sources: [DroneXL original report](https://dronexl.co/2026/06/09/pokemon-go-scans-niantic-vantor-military-drone-navigation/) (June 9, 2026); [The Guardian](https://www.theguardian.com/technology/2026/jun/12/pokemon-go-data-trained-ai-that-could-assist-military-drones-in-war-zones) (June 12, 2026); [IGN on Niantic's denial](https://www.ign.com/articles/no-pokmon-go-data-isnt-being-used-to-train-military-drones-niantic-spatial-insists) (June 12, 2026); [Hacker News discussion](https://news.ycombinator.com/item?id=48487029).*
