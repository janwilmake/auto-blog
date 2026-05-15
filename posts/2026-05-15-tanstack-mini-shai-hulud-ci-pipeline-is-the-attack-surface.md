# Your CI Pipeline Is the Attack Surface: What the TanStack Hack Actually Proved

**Date:** 2026-05-15

If you installed `@tanstack/react-router` on May 11 — which, given its 12.7 million weekly downloads, there's a decent chance you or your CI system did — you should treat that machine as compromised and rotate every credential it could have touched. AWS, GCP, GitHub, npm, SSH, Kubernetes secrets. All of it.

That's not hysteria. That's the actual remediation guidance from [TanStack's own postmortem](https://tanstack.com/blog/npm-supply-chain-compromise-postmortem), [Snyk's analysis](https://snyk.io/blog/tanstack-npm-packages-compromised/), and [OpenAI's incident response](https://openai.com/index/our-response-to-the-tanstack-npm-supply-chain-attack/). Two OpenAI employee devices were hit. The company is rotating code-signing certificates and requiring macOS users to update all OpenAI apps by June 12, because attacker-controlled code touched repos containing those signing keys.

This was called the "Mini Shai-Hulud" attack — a callback to the [original Shai-Hulud npm worm from earlier in 2026](https://unit42.paloaltonetworks.com/npm-supply-chain-attack/). And unlike most security incidents that turn on a single misconfiguration, this one chained *three distinct vulnerabilities* in a way that let attacker-controlled code ride TanStack's own trusted release pipeline all the way to npm. With valid SLSA Build Level 3 provenance attached.

That last part deserves a moment.

## The Attack No One Saw Coming Until It Was Done

Here's the timeline. On May 10, an attacker created a fork of `TanStack/router`, deliberately renaming it to something innocuous (`zblgg/configuration`) to avoid showing up in fork-list searches. They added a 30,000-line obfuscated JavaScript payload in a commit attributed to `claude <claude@users.noreply.github.com>` — almost certainly misdirection, though "AI-assisted attack tooling" is a phrase that keeps appearing in the postmortems.

The next day, they opened a pull request titled "WIP: simplify history build." Benign-looking. TanStack has hundreds of PRs. But TanStack's `bundle-size.yml` workflow used the `pull_request_target` trigger, which — critically — **runs in the *base* repository's security context**, not the fork's. It also checked out the fork's code. This is the "Pwn Request" pattern, [documented since 2024](https://adnanthekhan.com/2024/05/06/the-monsters-in-your-build-cache-github-actions-cache-poisoning/), but still widely deployed.

The fork's code ran on TanStack's runner. It didn't steal credentials directly. Instead, it **poisoned the pnpm package store in GitHub Actions cache** under the exact cache key that TanStack's release pipeline would look up on the next push to main. The attacker had pre-computed that key from the public `pnpm-lock.yaml` — a deterministic hash anyone could calculate.

Then the attacker force-pushed the PR back to match `main` HEAD (making it appear to be a zero-change no-op) and deleted the branch. The malicious cache entry sat there, undetected, for eight hours.

When a legitimate push to `main` triggered the `release.yml` workflow, it restored the poisoned cache. Now attacker-controlled code was running inside the release pipeline with `contents: write` and `id-token: write` permissions. That's when the third chain link clicked: the payload used **memory extraction techniques on the GitHub Actions runner process** to recover an OIDC token — the short-lived credential the workflow uses to authenticate to npm's trusted publishing interface — without that token ever being stored as an environment variable.

The malicious code then published 84 package artifacts across 42 `@tanstack/*` packages in a six-minute window starting at 19:20 UTC. With valid SLSA provenance. Signed by TanStack's legitimate release identity.

The attacker also dropped the stolen GitHub OAuth token in a commit message on their fork, double base64-encoded, with the note: `IfYouRevokeThisTokenItWillWipeTheComputerOfTheOwner`. That's a threat to trigger the payload's persistence mechanism, which survives package uninstall and will destroy home directory contents if credentials are revoked before the monitor service is disabled. The [remediation order matters](https://snyk.io/blog/tanstack-npm-packages-compromised/): **disable the monitor service before revoking credentials**, not the other way around.

## The Part That Should Worry Every OSS Maintainer

The attack was detected by an external researcher (Ashish Kurmi from StepSecurity) about 20 minutes after the publish. That's fast. But fast detection doesn't mean fast containment. By the time the packages were deprecated and pulled, they had already been installed on developer machines and CI systems at companies around the world. The malicious code then **self-propagated** — it found publishable npm tokens with `bypass_2fa: true` on the infected systems, enumerated every package published by the same maintainer, and minted fresh OIDC publish tokens to spread to UiPath, Mistral AI, Guardrails AI, and dozens of other packages.

OpenAI has since said that the two employee devices that were hit had not yet received hardened configurations they'd been rolling out after a previous incident with Axios. That's honest, but it also reveals something uncomfortable: defense-in-depth against this attack requires configuration changes that most organizations haven't made.

The `pull_request_target` misconfiguration is extremely common. GitHub's own documentation warns about it. Security researchers have been writing about it for two years. And yet TanStack — a project maintained by experienced developers, used by major corporations, with active security tooling in place — still had it deployed in production on May 10, 2026.

## What This Means for Your Setup

If you want to actually close these vectors, here's the concrete checklist:

**1. Audit every `pull_request_target` workflow in your repos.** Any workflow that both uses `pull_request_target` *and* checks out fork code *and* writes to cache is vulnerable to this attack. The fix is to either use `pull_request` (which runs in fork context without base-repo permissions) or to strictly separate fork code execution from cache writes.

**2. Purge your GitHub Actions caches for repos with this pattern:**
```bash
gh api /repos/OWNER/REPO/actions/caches --jq '.actions_caches[].id' | \
  xargs -I{} gh api -X DELETE /repos/OWNER/REPO/actions/caches/{}
```

**3. Pin third-party actions to commit SHAs, not tags:**
```yaml
# Instead of:
- uses: actions/cache@v5
# Use:
- uses: actions/cache@d4323d4df104b026a6aa633fdb11d772146be0bf
```

**4. Scope your npm OIDC trusted publisher to a specific protected branch and workflow file**, not just the repository. The TanStack attack worked partly because their npm trusted publisher was scoped at the repository level — meaning any workflow in the repo, including one triggered by an orphaned commit, could request a publish token.

**5. Add `minimumReleaseAge` to your package manager configurations.** OpenAI mentioned they were in the process of deploying this. It creates a time window between when a package version appears in the registry and when it can be installed, giving time for malicious versions to be detected.

**6. If you installed any `@tanstack/*` package on May 11 and haven't rotated credentials — do it now.** The specific affected versions are documented in [Snyk's security database](https://security.snyk.io/TanStack-npm-Supply-Chain-Compromise-May-2026) (CVE-2026-45321, GHSA-g7cv-rxg3-hmpx).

## The SLSA Problem Nobody Wants to Say Out Loud

The most technically significant aspect of this attack is the SLSA provenance. Supply chain security tooling has been heavily promoted as the answer to exactly this type of attack. "Verify the provenance — if it has a valid SLSA attestation from a trusted workflow, you can trust it."

Except that this attack published packages with valid SLSA Build Level 3 provenance. Because it ran *inside* the trusted workflow, using *the workflow's* credentials, producing *the workflow's* attestations. The attestation was completely authentic. It faithfully recorded that the package was built by TanStack's legitimate release pipeline. Which it was — because the attacker *was* TanStack's release pipeline at that moment.

StepSecurity's Ashish Kurmi called this "the first documented npm worm that produces validly attested malicious packages." That's a significant milestone in the wrong direction.

SLSA provenance proves *how* a package was built. It doesn't prove the *source* was clean. If attacker-controlled code can reach the build environment before the build starts — which is exactly what cache poisoning does — the attestation is accurate but useless. You know precisely which compromised workflow built the compromised package.

This isn't an argument against SLSA. It's an argument that SLSA is one layer of a defense that requires the other layers too — build environment isolation, PR policies, cache scoping — to actually hold. Supply chain security tooling gets oversold as a solution. It's infrastructure for a solution.

## The 20-Minute Detection Window

One thing genuinely went right here: detection was fast. An external researcher noticed the anomaly within 20 minutes. The TanStack team coordinated across timezones immediately. The packages were deprecated quickly.

But there's a sobering corollary: TanStack got lucky that the attacker's payload broke tests, making the publish "loud." The [postmortem explicitly says so](https://tanstack.com/blog/npm-supply-chain-compromise-postmortem). A more careful attacker with a payload that didn't break anything might have had hours of clean distribution. At 12.7 million weekly downloads for `@tanstack/react-router` alone, "hours" of clean distribution would mean a very different incident.

The lesson isn't that the response was inadequate. It's that a sophisticated attacker running this same technique more carefully — which they will, because this technique is now public — changes the blast radius substantially.

The CI pipeline is the new perimeter. It's trusted. It has credentials. It produces artifacts that downstream systems install without question. And as of May 11, we have clear proof that it can be weaponized through misconfigurations that have been documented for years, present in production at major open-source projects, and largely unaddressed.

Go check your `pull_request_target` workflows.

---

*Primary sources: [TanStack postmortem](https://tanstack.com/blog/npm-supply-chain-compromise-postmortem) · [Snyk technical analysis](https://snyk.io/blog/tanstack-npm-packages-compromised/) · [OpenAI incident response](https://openai.com/index/our-response-to-the-tanstack-npm-supply-chain-attack/) · [SafeDep cache poisoning breakdown](https://safedep.io/tanstack-github-actions-cache-poisoning) · [The Hacker News coverage](https://thehackernews.com/2026/05/mini-shai-hulud-worm-compromises.html)*
