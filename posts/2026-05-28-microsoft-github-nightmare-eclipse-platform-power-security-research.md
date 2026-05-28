# Microsoft Owns the Platform Where Security Researchers Publish. The Nightmare-Eclipse Ban Just Proved Why That's a Problem.

**Published: 2026-05-28**

When Chaotic Eclipse (aka Nightmare-Eclipse) published BlueHammer on GitHub in April, I wrote about it as a disclosure story — a researcher's frustration with MSRC boiling over into six weeks of zero-day drops. That was the right framing for April. But the story has moved. What happened this week is something different, and it deserves its own analysis.

GitHub — owned by Microsoft — terminated Nightmare-Eclipse's account last week. Within hours of that, GitLab also blocked the account. And on May 27, Microsoft's MSRC team published [a blog post on coordinated vulnerability disclosure](https://www.microsoft.com/en-us/msrc/blog/2026/05/a-shared-responsibility-protecting-customers-through-coordinated-vulnerability-disclosure) — a public relations document dressed up as a policy statement — defending their handling of the whole affair.

The researcher responded on their personal blog: "You defame me in public with your CVE-2026-45585 advisory even though you literally deleted the Microsoft account I used to report bugs to you with and I got zero pennies from doing so and I still happily did like an idiot. Now you take the courtesy to flag my GitHub account and wipe it out of the public, just like that?"

There are two separate questions here that keep getting tangled together. Disentangling them is worth doing.

---

## Question 1: Was Nightmare-Eclipse Right to Drop Uncoordinated Zero-Days?

Short answer: no, not without significantly more cause than has been publicly established.

The standard critique of full disclosure — that you're handing weaponized code to attackers before defenders can patch — is real. BlueHammer, RedSun, and UnDefend were all observed being exploited in the wild within days of publication. That's not hypothetical harm. Those are real organizations getting hit with live attacks. Barracuda later noted the exploit traffic was geolocated to Russian infrastructure, which suggests the code was picked up immediately by state-adjacent actors.

The researcher's grievance — that Microsoft deleted the account they used to report bugs, credited other researchers for work they'd done, and left them "homeless with nothing" — may be entirely true. We don't have Microsoft's side of whatever private arrangement was allegedly violated. But even a legitimate grievance doesn't automatically justify releasing working BitLocker bypass code into the wild. YellowKey, which allows shell access to a fully encrypted drive via a USB attack — no recovery key required — is the kind of thing that turns stolen laptops into incidents at hospitals, law firms, and government agencies. "My feelings were hurt and I wasn't paid" is not proportionate justification for that.

So no, the full disclosure spree wasn't defensible on the merits. That should be clear.

But that's not the interesting question anymore.

---

## Question 2: Should GitHub Have Banned the Account?

This is where it gets genuinely complicated, because the answer depends entirely on whether you think Microsoft's ownership of GitHub is a neutral fact or a structural conflict of interest.

GitHub's terms of service prohibit content that "directly supports unlawful active attacks." That standard — whatever its merits in general — was applied here to ban a researcher who was publishing exploits for vulnerabilities in *Microsoft products*. The company being attacked is the same company that owns the platform being used to do the attacking.

Imagine if Oracle owned PyPI, and banned a researcher who published exploits against Oracle's Java runtime. Or if AWS owned npm, and removed packages that demonstrated vulnerabilities in AWS services. We would immediately recognize the conflict. We'd ask: was this a legitimate terms-of-service enforcement, or was this a vertically integrated company using platform control to suppress unflattering security research?

With GitHub, we accept that conflict as background noise because we've normalized Microsoft's 2018 acquisition. But it's the same structural problem.

The researcher's code moved to GitLab almost immediately — and GitLab banned it too. GitLab doesn't own Windows. That ban might be more defensible under a neutral "no active exploit code" policy, though the security research community has long debated whether repositories containing PoC code cross that line. The answer has generally been: it depends on context, and historical exploit code for documented vulnerabilities is typically preserved.

Microsoft's MSRC blog post on May 27 is careful not to mention GitHub, or bans, or platform control at all. It talks about "the industry standard" of coordinated disclosure, the hundreds of researchers they work with annually, and their commitment to compensating and acknowledging contributors. It presents Microsoft as the reasonable adult in the room whose customer safety concerns were ignored by a rogue actor. That framing may be partly accurate — but it's also convenient that the person who could rebut it most effectively just had their primary publishing platform removed.

---

## The Structural Issue That Nobody Wants to Name

Security research has a platform problem that predates Nightmare-Eclipse by years.

The dominant infrastructure for publishing exploit research — GitHub repos, GitHub Pages, GitHub Gists — is owned by one of the largest software vendors in the world. A vendor that also runs one of the largest bug bounty programs in the world. A vendor whose products are the most common targets of that research.

Most of the time, this doesn't matter. Microsoft doesn't remove repos disclosing vulnerabilities in Cisco gear, or Apple products, or open-source software. The incentive to abuse the position only appears when the target is Microsoft itself.

The Nightmare-Eclipse situation is a stress test. It exposes that the guardrails we thought existed — that GitHub enforces ToS neutrally regardless of who the affected party is — might not be as robust as assumed when the affected party is also the platform owner.

Six months from now, when a different researcher tries to disclose a Windows vulnerability in a way Microsoft considers premature, what does that researcher now know? They know that if they push too hard, the platform their code lives on can disappear. That's a chilling effect even if you think Nightmare-Eclipse went too far. You don't have to endorse someone's tactics to recognize that using platform control to silence them sets a bad precedent.

---

## What Should Have Happened, and What Happens Now

The original dispute — whatever private arrangement existed between the researcher and MSRC — should have been handled through CERT/CC, or a neutral third-party coordinator, before it became a public zero-day war. The security research community has those institutions precisely because bilateral researcher-vendor disputes go badly.

Microsoft's MSRC blog post ends with: "We always have and will continue to welcome vulnerability submissions from anyone through our public researcher portal, regardless of past interactions or reputation." That's a nice sentence. It would be more credible if the researcher making submissions still had an account on the platform Microsoft owns.

The researcher has promised a "July 14th" release of what they're calling "the documents" — apparently evidence of whatever went wrong in the private disclosure process. That date matters: July 14 is the next day after Patch Tuesday, so any vulnerabilities they release will be fresh against an unpatched population. Whether this plays out as promised, or whether the account deletions were enough to cool the campaign, we'll find out.

What we already know is this: the episode has clarified something the industry preferred to leave vague. When the world's most common software target owns the world's most common code-hosting platform, "neutral platform" is not a stable equilibrium. It's a policy choice that can be reversed whenever the costs get high enough.

---

**Primary sources:**
- [Microsoft MSRC blog: A shared responsibility — Coordinated Vulnerability Disclosure (May 27, 2026)](https://www.microsoft.com/en-us/msrc/blog/2026/05/a-shared-responsibility-protecting-customers-through-coordinated-vulnerability-disclosure)
- [Nightmare-Eclipse blog: July 14th (May 23, 2026)](https://deadeclipse666.blogspot.com/2026/05/july-14th.html)
- [Cybernews: GitHub bans vindictive security researcher (May 26, 2026)](https://cybernews.com/security/github-bans-researcher-releasing-windows-zero-days/)
- [The Hacker News: Microsoft Slams Public Zero-Day Disclosures Amid GitHub Researcher Account Removal (May 28, 2026)](https://thehackernews.com/2026/05/microsoft-slams-public-zero-day.html)
- [Barracuda: Nightmare-Eclipse — six zero-days, six weeks and one big grudge (May 19, 2026)](https://blog.barracuda.com/2026/05/19/nightmare-eclipse-zero-days-grudge)
- [CVE-2026-45585 (YellowKey) MSRC advisory](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2026-45585)
