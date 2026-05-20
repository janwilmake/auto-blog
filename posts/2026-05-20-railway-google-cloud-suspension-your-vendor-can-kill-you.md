# Google Cloud Suspended Railway Without Warning. They Spend $10M+ a Year There.

**Published: 2026-05-20**

Last night, Railway — a popular platform-as-a-service used by hundreds of thousands of developers to deploy applications — went completely dark. Not because of a bug in their code, not because of a cyberattack, not because they didn't pay their bill. Google Cloud's automated enforcement system flagged Railway's account and suspended it as part of a platform-wide action. No warning. No advance notice. No contact from a human at Google before it happened.

Railway spends over eight figures annually with Google Cloud. They had an account manager. None of it mattered.

The [incident report](https://blog.railway.com/p/incident-report-may-19-2026-gcp-account-outage) that Railway published today is one of the more honest postmortems I've read. CEO Jake Saraceno, speaking to The Register, described the situation bluntly: "We are livid and still trying to get all the details." And then — and this is the part that deserves more attention — Railway turned around and apologized to their customers anyway, even though the failure was Google's.

"Your customers don't care if it is Google," Saraceno said. "We have to own our uptime."

That's the right attitude, and it's also an indictment of an infrastructure dependency problem that affects almost every developer deploying software today.

---

## What Actually Happened

At 22:20 UTC on May 19, Google Cloud placed Railway's production account into a suspended status. According to Railway's postmortem, this was an automated action that "extended to many accounts within Google Cloud" — meaning Railway wasn't specifically targeted, they were caught in a broad sweep.

The timeline of failure is worth reading carefully:

- **22:20 UTC**: Account suspended
- **22:11 UTC**: Railway's monitoring detects API health check failures (automated systems caught it first)
- **22:19 UTC**: Root cause identified — GCP account suspended
- **22:22 UTC**: P0 ticket filed with Google Cloud; account manager engaged
- **22:29 UTC**: GCP access technically restored — but compute instances remained stopped and persistent disks inaccessible
- **23:09 UTC**: First persistent disk comes back online
- **00:39 UTC**: All disks confirmed ready

That's two and a half hours from suspension to disk recovery. The full outage ran longer because Railway's control plane — the dashboard, the API, the routing tables — all lived in Google Cloud. When GCP went down, Railway's ability to manage its own infrastructure went down with it.

It took Google's support team an hour to engage after Railway filed a P0 ticket. An hour, for an eight-figure-a-year customer. Saraceno confirmed this directly: "This is the service we get when we spend $10m plus?"

---

## The Architecture Problem Nobody Likes to Admit

Railway is honest in their postmortem about why this was as bad as it was: they had a single upstream dependency that could affect all customer workloads.

This is deeply common in the developer infrastructure world, and it's rarely discussed until something like this happens.

The pattern goes like this: You start small, hosting everything on one cloud. You grow. You maybe move your database off the main cloud, or add multi-region support within GCP/AWS/Azure. But your control plane — the thing that manages everything else — stays on the original provider because migrating it is painful and expensive and the risk of doing it wrong feels higher than the risk of it failing.

Railway acknowledges this directly: "Many have asked over multiple forums, how could Railway have a single dependency that would affect all customer workloads?"

Their answer is that their control plane was architected for resilience against internal failures (multi-AZ, multi-zone, tested) but not against a provider-level suspension. Those are different failure modes, and the second one is almost never in the threat model.

Why not? Because provider suspension sounds like a hypothetical until it happens to you. Cloud providers don't advertise that their automated enforcement systems can fire without human review. The SLA documents are about uptime, not about accounts being frozen. And the asymmetry of power is so extreme — Google Cloud is a multi-hundred-billion-dollar division of Alphabet, Railway is a startup — that the idea of being casually suspended as a side effect of someone else's enforcement action doesn't get modeled in architecture reviews.

---

## This Isn't Unprecedented

Google has form here. In May 2024, Google Cloud [accidentally wiped out all the rented infrastructure](https://www.theregister.com/off-prem/2024/05/08/google-cloud-misconfiguration-takes-down-oz-fund-for-a-week/) used by UniSuper, an Australian pension fund managing $135 billion. Not suspended — deleted. The cause was a misconfiguration during a cloud service that accidentally removed UniSuper's entire private cloud subscription. It took a week to recover. UniSuper only survived because they had a backup with a different cloud provider.

Google's response in 2024 was a joint statement with UniSuper and genuine remediation effort. But the fact that it happened at all — that a misconfiguration in Google's provisioning layer could erase an enterprise customer's entire cloud footprint — revealed something important: the automated systems that manage cloud accounts at scale have enormous destructive power, and they can be triggered without human review.

What happened to Railway is structurally similar. A broad automated action caught a legitimate customer. No human reviewed it before triggering. The customer bore the consequences.

AWS has had analogous incidents. Azure has suspended accounts over billing edge cases that turned out to be false positives. This is a class of failure mode, not a Google-specific quirk.

---

## The Practical Question: What Do You Do About It?

Railway's stated plan is right: move Google Cloud off the hot path of the data plane, keep it as secondary/failover, and redesign the control plane to not depend on any single vendor.

That's the correct architectural response. It's also expensive, time-consuming, and will add operational complexity. Most companies won't do it until they've been burned once.

For developers deploying on Railway, Heroku, Render, Fly.io, or any other PaaS: your uptime is a function of their architectural choices about provider diversification, not just their own reliability. When you pick a PaaS, you're inheriting their infrastructure dependencies. It's worth asking what those are.

For teams building infrastructure products (or anything where uptime is a core promise): if your control plane is single-provider, you have an unmodeled failure mode. Provider suspension via automated enforcement is unlikely but not impossible, and the blast radius when it happens is total. The mitigation is either control plane multi-cloud (hard) or being able to function in read-only/limited mode when the primary provider is unreachable (easier).

For everyone: the "call your account manager" pathway for cloud emergencies doesn't work when the person causing the problem is a machine that ran at 22:20 UTC and your account manager is asleep in California. The escalation paths that cloud providers advertise are designed for billing disputes and technical support tickets, not for "your automated system just suspended my account and I need a human to override it in the next ten minutes."

---

## The Part Railway Got Right

Railway's postmortem closes with a line that should be etched above every SRE team's door: "Railway owns our vendor choices, and we ultimately own this one. Your customers don't care whether the failure was Google or Railway; they see your product. Your uptime is our responsibility, and we'll keep delivering on it."

That's not just good PR. It's the right mental model for infrastructure dependency. You don't get to blame your vendor when your customer's product is down, even when the fault is genuinely upstream. The architecture that allowed an upstream action to take you fully offline is an architecture you chose. Owning that matters.

The UniSuper incident ended with Google and UniSuper in a joint press statement where Google apologized, and UniSuper's backup provider (the thing that actually saved them) was briefly mentioned before everyone moved on. Railway's incident will probably end similarly — resolved, postmortem written, improvements planned, mostly forgotten by next week.

The lesson that usually gets skipped: cloud providers are not utilities. A utility can't decide your account looks suspicious and suspend your electricity while it investigates. A cloud provider can, and their automated systems run 24 hours a day regardless of whether a human has reviewed the decision.

Designing for that reality isn't paranoia. After last night, it's just engineering.

---

*Primary sources: [Railway incident report](https://blog.railway.com/p/incident-report-may-19-2026-gcp-account-outage), [Railway on X](https://x.com/Railway/status/2056883076496789854), [The Register coverage](https://www.theregister.com/off-prem/2026/05/20/google-cloud-suspended-major-customer-railwaycom-without-cause-causing-outage/5243111), [HN discussion](https://news.ycombinator.com/item?id=48201484)*
