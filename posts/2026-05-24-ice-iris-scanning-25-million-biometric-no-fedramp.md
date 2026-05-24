# ICE Just Bought 1,570 Iris Scanners for $25 Million. No Competitive Bid. No FedRAMP. Database Includes Social Media Scrapes.

**2026-05-24**

On May 22, ICE finalized a $25.1 million no-bid contract with Bi2 Technologies, a small company in Plymouth, Massachusetts, for mobile iris-scanning hardware and database access. The devices are due at ICE field locations by late June. That's roughly five weeks.

This is the second contract ICE has signed with Bi2 in eight months. The first, in September 2025, was $4.6 million for 200 devices. This one is $25.1 million for 1,570 devices — eight times the hardware at five times the cost. By late June, ICE agents in the field will be able to point a smartphone at a person, scan their iris in under a second, and get back a match from a database of over five million records.

The records come from criminal booking data. They also come from social media profiles that Bi2 scraped.

## What the System Actually Does

Bi2 Technologies makes two products relevant here. IRIS (Inmate Recognition and Identification System) is their fixed-location system, originally sold to sheriff's offices for jail booking and release — enrolled inmates at intake, re-verified them at release to prevent accidental releases. The Plymouth County Sheriff's office has used it since at least 2011.

MORIS (Mobile Offender Recognition and Information System) is the field-deployable version. It runs on standard iOS or Android smartphones. It scans the iris from 10 to 15 inches away. It has built-in liveness detection. It returns a match in real time, querying Bi2's national iris repository and integrated DHS databases. The system captures over 265 unique points from the iris for its biometric template.

The critical phrase in the procurement documents is "field operations." This isn't a system for checking people you've already arrested. It's for verifying the identity of people you encounter — traffic stops, neighborhood operations, whatever situation puts an ICE agent face-to-face with a person whose identity they want to confirm. You don't need to go back to the office. You don't need a fingerprint scanner. You need a smartphone and the right app.

Biometric Update's reporting describes this as a "decentralized surveillance infrastructure" in which "agents can remotely and quietly identify individuals, verify immigration status, and trigger detainment decisions with minimal oversight or transparency." That framing is accurate.

## The Three Things That Make This Different From Other Government Tech Contracts

**1. No FedRAMP certification required.**

FedRAMP is the federal government's security review framework for cloud systems handling sensitive data. If you're building a SaaS app that federal employees will use to process government data, you need FedRAMP authorization. There are tiers based on sensitivity. The ICE IRIS/MORIS system, which will store biometric identifiers for millions of people and connect to DHS databases, required no FedRAMP clearance before deployment.

The procurement documents describe no independent security audit, no congressional notification, and no outside review process for how the system will be used. The $25 million contract award was posted to SAM.gov on May 22. That's how we know about it — not because anyone announced it.

**2. The database includes social media scrapes.**

The immigration policy tracking documents from the original August 2025 solicitation describe the database as including "over 5 million criminal booking records and [data] scrapped from social media profiles." Scraped, not subpoenaed. Not obtained through legal process. Scraped from social media.

Biometric data is already uniquely sensitive — you can change a password, you cannot change your iris. When the database that your iris is being checked against was built partly from social media scraping rather than legal process, and when the system will be used for street-level identity verification without probable cause, the governance question isn't academic.

**3. The timeline is 30 days to nationwide deployment.**

The September 2025 contract was a pilot: 200 devices. The May 2026 contract is 1,570 devices, due to ICE field locations within 30 days of the contract finalization. There is no stated evaluation period. No reporting on outcomes from the pilot. No published accuracy data for the specific populations and conditions in which agents will use it.

Iris recognition is genuinely one of the most accurate biometric modalities — better than facial recognition in controlled conditions. But "controlled conditions" is doing a lot of work. In a jail booking room with proper lighting and cooperative subjects, the false positive and false negative rates are excellent. At 15 inches, in outdoor light, with a person who may be moving, the error characteristics are different. The 2026 contract doesn't include requirements to measure or report error rates in field conditions.

## The "Law Enforcement Already Has Your Fingerprints" Argument Doesn't Cover This

People defending biometric expansion by law enforcement often argue: we've been fingerprinting people in the criminal justice system for a century. Iris scanning is just fingerprinting but faster. What's new?

What's new is the combination of three things that weren't previously combined:

*No-touch scanning at conversational distance.* Fingerprinting requires physical contact and cooperation. MORIS works at up to one meter. The legal distinction between a search that requires touching someone and one that requires only proximity matters enormously for Fourth Amendment analysis.

*A database built substantially outside the criminal justice system.* The IRIS/MORIS database isn't just booking records. It includes social-media scraped data. That means it may contain records of people who have never been arrested, never been fingerprinted, never interacted with the justice system. The database scope has expanded beyond the category of people who consented to biometric collection at booking.

*Nationwide, in-field, real-time access.* The old version of "law enforcement has your biometrics" meant: if you get arrested, we have your fingerprints on file. The new version means: if an ICE agent encounters you, anywhere in the country, they can silently verify your identity in real time against a database you may not know you're in.

## The Bi2 Origin Story Matters

Bi2 Technologies marketed MORIS and IRIS originally to sheriff's offices for jail operations. In 2023, they offered 31 county sheriff departments along the southern border *free* access to MORIS as part of an expansion push. The database grew through those partnerships. The business model for building a large biometric database is: give away early access, collect data, then sell the federal government access to what you've assembled.

This is a familiar pattern in the surveillance technology industry. Palantir did something similar. The data moat gets built through smaller agency partnerships until the database is big enough to be worth federal contracts. Once the federal contract exists, the data grows faster. The ICE September 2025 contract was $4.6 million. Eight months later it's $25 million. By next year, the comparison to previous contracts becomes irrelevant — the infrastructure exists, the switching costs are high, and the only direction is expansion.

## What Oversight Looks Like Right Now

According to Project Salt Box's reporting — which is the most detailed account of this contract available — the $25.1 million award was posted to SAM.gov. There is no congressional notification requirement described. No IG audit scheduled. No public reporting requirement on usage or error rates. The procurement document describes no independent review of how the system will be used, by whom, or with what constraints.

ICE's Enforcement and Removal Operations division has broad discretion over field operations. The technology specification says the system is for "quickly authenticating the identity of subjects during field operations." Who counts as a subject? Under what circumstances is a scan conducted? Are scans logged? Can a scan result be challenged? None of these questions are answered by the contract.

This is the governance vacuum that matters most. Not whether iris scanning works — it mostly does — but who decides when it gets used, what happens with the data, and how a false match gets corrected before a U.S. citizen or legal resident is detained because a scrape-enriched database returned the wrong result.

The devices are due at ICE field offices by late June. The governance questions don't have late-June answers.

---

*Primary sources: [Project Salt Box](https://www.projectsaltbox.com/p/ice-awards-25-million-iris-scanning), [SAM.gov contract record](https://www.usaspending.gov/award/CONT_AWD_70CTD025C00000001_7012_-NONE-_-NONE-), [Immigration Policy Tracking Project](https://immpolicytracking.org/policies/ice-notices-intent-to-sole-source-iris-scanning-app-and-database/), [Biometric Update](https://www.biometricupdate.com/202508/ices-biometric-surveillance-reach-expands-with-bi2-iris-scanning-tech), [The Boston Globe](https://www.bostonglobe.com/2026/01/28/metro/ice-surveillance-data/)*
