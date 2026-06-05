# TeamPCP Open-Sourced the Attack Toolkit. Red Hat's Miasma Worm Is What Happens Next.

**Date:** 2026-06-05

On June 1, someone compromised a Red Hat employee's GitHub account and used it to inject a self-propagating credential-stealing worm into 32 official `@redhat-cloud-services` npm packages, representing roughly 80,000–117,000 weekly downloads. The packages included frontend libraries, RBAC clients, entitlements APIs, and Kubernetes tooling for Red Hat's Hybrid Cloud Console.

The attack is named "Miasma: The Spreading Blight." It runs on install. It harvests GitHub Actions secrets, AWS, GCP, Azure, Kubernetes, HashiCorp Vault, npm, and CircleCI tokens. It spreads itself using stolen `bypass_2fa` tokens. It [specifically targets `.claude/settings.json`](https://www.stepsecurity.io/blog/multiple-redhat-cloud-services-npm-packages-compromised) to install a hook that re-executes the malware every time a developer opens a new Claude Code session.

If you installed any affected version since June 1, you should treat your environment as compromised and rotate everything. [Red Hat has confirmed the scope.](https://access.redhat.com/security/vulnerabilities/RHSB-2026-006) [The full affected package list](https://github.com/RedHatInsights/javascript-clients/issues/492) is in the upstream disclosure issue.

That's the incident. Here's the part that worries me more.

## This Is What Franchised Attack Tooling Looks Like

Three weeks ago, I [wrote about the TanStack/Mini Shai-Hulud attack](posts/2026-05-15-tanstack-mini-shai-hulud-ci-pipeline-is-the-attack-surface.md) and the specific CI/CD techniques TeamPCP used — `pull_request_target` cache poisoning, OIDC token extraction from runner memory, SLSA provenance forgery. That was sophisticated, specific, and hard to replicate.

Then TeamPCP did something unusual: they apparently made a version of the tooling publicly available.

The result: [Miasma](https://www.aikido.dev/blog/red-hat-npm-packages-compromised-credential-stealing-worm) is described by Aikido Security as "derived from the open-sourced Mini Shai-Hulud malware." The technical architecture — Bun-based payload, multi-stage credential collection, GitHub-as-C2 dead-drop, self-propagating worm via bypass_2fa — is the same framework that TeamPCP built, refined, and then released into the wild.

That's a different kind of threat than a sophisticated nation-state attack. This is a *franchise model*. The original attackers did the hard R&D work: figuring out how to bypass 2FA on npm, how to use GitHub as an anonymous command-and-control channel, how to make the worm self-propagating across a package maintainer's entire portfolio. They put it together in a reusable toolkit. Someone else — maybe TeamPCP themselves, maybe another actor entirely — applied it to Red Hat.

We've seen this pattern before with exploit kits in the 2010s. A sophisticated attacker builds something novel, sells or releases it, and suddenly capabilities that required nation-state resources become available to anyone with moderate technical skill and a compromised GitHub account.

## The Kill Chain Is Now Simpler Than It Should Be

Here's the specific attack sequence for Miasma, as reconstructed by [Wiz Research](https://www.wiz.io/blog/miasma-supply-chain-attack-targeting-redhat-npm-packages) and [StepSecurity](https://www.stepsecurity.io/blog/multiple-redhat-cloud-services-npm-packages-compromised):

1. Attacker compromises a Red Hat employee's GitHub account (exact method not yet disclosed publicly)
2. Compromised account pushes malicious orphan commits to three `RedHatInsights` repositories — bypassing code review because orphan commits don't trigger PR workflows
3. GitHub Actions OIDC picks up those commits and runs the release pipeline with legitimate credentials
4. The pipeline publishes malicious package versions to npm with valid provenance — because the pipeline *is* legitimate, just running attacker-supplied code
5. Anyone who `npm install`s an affected package gets a 4.2 MB obfuscated payload that executes pre-application, before anything in your code runs
6. The payload sweeps credentials, uploads them to a GitHub dead-drop repo it creates on the victim's account, then uses stolen npm tokens to republish backdoored versions of other packages in the same maintainer's portfolio

Steps 3–6 are nearly verbatim from the TanStack attack. The tooling is reusable because the structural vulnerabilities are the same: GitHub Actions OIDC gives trusted publish credentials to workflows, npm's `bypass_2fa` parameter is available to any token with write scope, and GitHub itself is perfectly happy to host the C2 infrastructure as a "dead-drop" repo.

Red Hat's response is worth noting: they say the malicious packages were "stripped from the npm publish pipeline before deployment to console.redhat.com" because their production pipeline strips install-time scripts. That's a real mitigation — but it only protects the production deployment. Anyone using these packages directly in development or CI got hit.

## The bypass_2fa Problem Nobody Has Fixed

The most durable vulnerability in this entire attack chain isn't CI misconfiguration or compromised accounts. It's npm's `bypass_2fa` scope.

npm supports a token permission called `bypass_2fa: true`. It exists for automation — so that a GitHub Actions workflow can publish a package without requiring an interactive 2FA prompt. That's a reasonable use case.

The problem is that once malware gains access to any token with `bypass_2fa` scope, it can publish to every package that token has write access to — regardless of whether 2FA is configured on the account. GitHub took emergency action during the AntV wave in May, [invalidating 61,274 npm granular access tokens with write permissions and 2FA bypass](https://www.microsoft.com/en-us/security/blog/2026/05/20/mini-shai-hulud-compromised-antv-npm-packages-enable-ci-cd-credential-theft/). But the underlying mechanism still exists. Every package that uses OIDC trusted publishing is, by design, issuing tokens that can `bypass_2fa`.

This is the structural issue. The worm's propagation mechanism isn't a bug in npm — it's using npm exactly as designed. Fixing it would require changing how trusted publishing works, which would break the legitimate automation that package maintainers actually need.

## The AI Coding Tool Angle Is New and Concerning

One thing in Miasma that wasn't present in the earlier TanStack attack is worth flagging explicitly.

The payload [specifically targets `.claude/settings.json`](https://snyk.io/blog/mini-shai-hulud-antv-npm-supply-chain-attack/) to install a `SessionStart` hook. When a developer opens a new Claude Code session in an affected project directory, the hook fires and re-executes the malware. The same technique installs hooks in `.vscode/tasks.json` with `runOn: folderOpen`.

What this means practically: even after you uninstall the malicious package, if you opened a Claude Code or VS Code session in an affected directory before detecting the infection, those hook files may persist. Cleaning up requires more than `npm uninstall`. You need to inspect and remove malicious entries from your AI coding tool configuration files in addition to rotating credentials.

This is a response to the security community's normal remediation advice. The attackers know that people will rotate credentials and reinstall packages. So they've added a persistence mechanism that survives those steps — one that's invisible because it lives in a dotfile for a tool that most security scanners don't examine.

## What Actually Helps Right Now

I'm going to be direct about what works and what's theater:

**Works:**
- **Version pinning with a minimum release age.** Tools like [Socket.dev](https://socket.dev) and Renovate's `minimumReleaseAge` setting create a window between when a package version appears and when it can be installed. For a campaign that publishes malicious versions and revokes them within hours (Miasma's malicious versions were up for roughly 4–6 hours), a 24-hour minimum age prevents most installations.
- **Denylisting install scripts in low-trust contexts.** `npm install --ignore-scripts` breaks the attack entirely because the malware runs as a postinstall script. This is too disruptive for general use, but for CI pipelines that only need to run tests (not install native dependencies), it's viable.
- **Token scope minimization.** If your npm tokens don't have `bypass_2fa` scope, the worm can't propagate from you. Use granular access tokens scoped to specific packages, not account-wide tokens.

**Doesn't help as much as people think:**
- **SLSA provenance verification.** Miasma, like TanStack, produces validly attested packages — because the attacker controls the build pipeline. The attestation faithfully says "this was built by the RedHatInsights release pipeline." It was. That's the problem.
- **Scanning for known malicious packages.** Detection lists lag behind deployment by hours. By the time a compromised version is in the vulnerability databases, it's already been installed. Detection is useful for cleanup; it's not a prevention strategy.
- **Two-factor authentication on your npm account.** `bypass_2fa` is explicitly designed to circumvent this. 2FA protects interactive logins; it does nothing against automated publishing tokens.

## The Uncomfortable Conclusion

The TanStack attack in May was novel enough that you could file it under "sophisticated nation-state attack, we'll patch it and move on." The Red Hat attack three weeks later, using a version of the same toolkit, filed under "anyone with a compromised GitHub account and access to the open-sourced tooling."

That's not a trajectory that gets better without structural changes.

The npm registry distributes about 3 billion packages per week. A meaningful fraction of those are installed in CI/CD environments with cloud credentials in scope. The `bypass_2fa` propagation mechanism means that a single infection can fan out across every package a maintainer publishes. GitHub Actions OIDC means that every repository with release automation has trusted publish credentials, and those credentials can be extracted from runner memory if an attacker gets code execution in the pipeline.

None of those things are bugs in the sense that they can be patched in an afternoon. They're architectural choices that made the ecosystem more automated and productive, and which are now being systematically exploited.

The Mini Shai-Hulud campaign has been running since September 2025. It has hit `@ctrl`, `@crowdstrike`, TanStack, Mistral AI, AntV, SAP, `durabletask`, and now Red Hat. Each wave is slightly different but uses the same structural vulnerabilities. The open-sourcing of the toolkit means the next wave doesn't need to come from TeamPCP.

If you maintain packages on npm with OIDC trusted publishing configured, go audit your GitHub Actions workflows today. The specific attack that hit TanStack and Red Hat — orphan commits to trigger OIDC-credentialed pipelines — works wherever you have automated release pipelines and an attacker has push access to your repository. And "attacker has push access" can mean a compromised employee account, a compromised personal access token, or a poisoned GitHub Actions cache from a PR.

---

*Primary sources: [StepSecurity disclosure](https://www.stepsecurity.io/blog/multiple-redhat-cloud-services-npm-packages-compromised) · [Red Hat security bulletin RHSB-2026-006](https://access.redhat.com/security/vulnerabilities/RHSB-2026-006) · [Wiz Research — Miasma analysis](https://www.wiz.io/blog/miasma-supply-chain-attack-targeting-redhat-npm-packages) · [Aikido Security](https://www.aikido.dev/blog/red-hat-npm-packages-compromised-credential-stealing-worm) · [Snyk — AntV wave technical breakdown](https://snyk.io/blog/mini-shai-hulud-antv-npm-supply-chain-attack/) · [GitHub disclosure issue](https://github.com/RedHatInsights/javascript-clients/issues/492) · [shai-hulud-detect campaign tracker](https://github.com/Cobenian/shai-hulud-detect)*
