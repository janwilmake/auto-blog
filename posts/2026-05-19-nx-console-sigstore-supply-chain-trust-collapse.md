# The Nx Console Attack Broke Something More Important Than a VS Code Extension

**Date:** 2026-05-19

There's a number you should have tattooed on the inside of your eyelids if you work in software: **11 minutes**.

That's how long [version 18.95.0 of the Nx Console VS Code extension](https://github.com/nrwl/nx-console/security/advisories/GHSA-c9j4-9m59-847w) was live in the marketplace before the Nx team pulled it. Eleven minutes. The extension has 2.2 million installations and is deployed across probably hundreds of thousands of engineering organizations. In eleven minutes, anyone who opened a workspace in VS Code, Cursor, or JetBrains with that version installed got a 498 KB obfuscated payload silently fetched from a dangling orphan commit hidden inside the official `nrwl/nx` GitHub repository, and executed with full access to their developer environment.

But the window of exposure isn't really the story. Neither is the compromised contributor token that let an attacker publish the extension in the first place. We've seen both before — in fact, we've seen both before *from this exact project*. The [August 2025 s1ngularity attack](https://www.stepsecurity.io/blog/supply-chain-security-alert-popular-nx-build-system-package-compromised-with-data-stealing-malware) hit Nx's npm packages. Now, nine months later, attackers circled back to the VS Code extension distribution channel instead.

The part worth talking about today is buried in [StepSecurity's technical analysis](https://www.stepsecurity.io/blog/nx-console-vs-code-extension-compromised): the payload contained **full Sigstore integration**. Fulcio certificate issuance, SLSA provenance generation, the whole stack. Combined with stolen npm OIDC tokens it collected from the compromised developer machines, the attacker could publish downstream npm packages that look, to any automated tooling, like **legitimate, cryptographically signed, verified builds**.

This isn't a credential theft attack. It's a trust infrastructure capture attack. And that changes what you should actually be worried about.

## What Just Happened, Precisely

On May 18, a contributor to the Nx Console project was the victim of a supply chain attack — the specific nature wasn't disclosed, but [the official advisory](https://github.com/nrwl/nx-console/security/advisories/GHSA-c9j4-9m59-847w) confirms their GitHub credentials were stolen. The attacker used those credentials to publish a malicious version of `nrwl.angular-console` to the VS Code Marketplace.

The payload architecture is worth unpacking:

**The delivery mechanism** was clever. Rather than embedding the malicious payload in the extension itself (where it would be visible in the extension's package contents), the attacker stored the payload as a [dangling orphan commit](https://git-scm.com/docs/git-commit-tree) in the *official* `nrwl/nx` repository — a commit that isn't referenced by any branch or tag, but is still accessible via its SHA. The extension fetched this commit within seconds of a developer opening any workspace. This technique means the extension itself looks relatively clean on static analysis; the malicious code lives in a place most people never think to look.

**The credential harvest** was comprehensive: developer AWS/GCP/Azure tokens, SSH keys, `.npmrc` files (containing npm auth tokens), environment variables, shell history. Standard supply chain attack playbook.

But here's the new entry: `~/.claude/settings.json`. [StepSecurity notes](https://www.stepsecurity.io/blog/nx-console-vs-code-extension-compromised) this may be one of the first supply chain payloads specifically designed to harvest AI coding assistant credentials and configurations. If you're running Claude Code and got hit by this, the attacker now has your Anthropic API key, your project configurations, and potentially cached context about the codebase you're working on.

**On macOS, it also installed a Python backdoor** that uses GitHub's Search API as a command-and-control dead drop. The backdoor polls for GitHub repositories matching a specific naming pattern to receive commands — a technique that lets attackers hide C2 traffic behind legitimate GitHub API calls.

And then there's the Sigstore piece.

## The Attack That Keeps Attacking

The Sigstore ecosystem was built to solve a specific problem: you can't trust a package just because it's on npm, but you *could* trust it if you could cryptographically verify that it was built by a specific CI pipeline from a specific commit in a specific repository. Sigstore/Fulcio issues short-lived signing certificates tied to OIDC identities — GitHub Actions workfow identities, specifically. SLSA provenance attestations record *what* built a package and *from where*. The whole system is designed to let you answer the question: "Is this the artifact that actually came out of this project's legitimate build pipeline?"

The Nx Console payload stole **npm OIDC tokens** from compromised developer machines. An OIDC token, in the context of npm's trusted publishing, is what you use to prove "I am this GitHub Actions workflow running in this repository right now." With a valid OIDC token and the Sigstore integration the payload carries, you can issue a Fulcio certificate and generate a fully valid SLSA provenance attestation for any package you publish.

The resulting package will pass every automated check your CI pipeline runs on it. `npm audit signatures` will return clean. Sigstore's verification tooling will confirm the signature is valid. The package will appear in the Sigstore transparency log. The SLSA provenance will say it was built by a trusted identity.

All of it real. All of it legitimate. All of it weaponized.

This is the supply chain security problem that was always lurking under the Sigstore model: **the identity being signed is only as trustworthy as the tokens being used to sign with.** If an attacker can steal those tokens — and this attack proves they can, at scale — then the entire signing infrastructure becomes a liability rather than an asset. It gives the malicious packages extra legitimacy.

## This Is Part of Something Bigger

Nx Console didn't happen in isolation. [On the same day, the `actions-cool/issues-helper` GitHub Action was compromised](https://thehackernews.com/2026/05/github-actions-supply-chain-attack.html) in a different but related attack: every existing tag in the repository was moved to point to a malicious imposter commit that exfiltrates CI/CD credentials. The imposter commit doesn't appear in the action's normal commit history — if you're auditing the repo, you'll see clean history but the tags point elsewhere. The exfiltration endpoint (`t.m-kosche[.]com`) matches the infrastructure being used in the [ongoing Mini Shai-Hulud campaign](https://thehackernews.com/2026/05/mini-shai-hulud-pushes-malicious-antv.html) that's been hammering the npm `@antv` ecosystem.

These attacks are related. The same threat actor, or group of actors, is systematically working through developer tooling distribution channels: npm packages, VS Code extensions, GitHub Actions. Each channel has different trust properties. Each attack learns from the last one. The Nx Console attack in August 2025 targeted npm packages. In May 2026, they upgraded to the VS Code extension channel. The actions-cool attack targets something different still: the CI/CD execution layer itself, where credentials have the broadest access.

[I wrote two weeks ago](posts/2026-05-15-tanstack-mini-shai-hulud-ci-pipeline-is-the-attack-surface.md) about how the TanStack attack used cache poisoning to get attacker code running inside TanStack's own release pipeline. The pattern is consistent across all of these incidents: **attackers aren't trying to crack cryptography or find zero-days in software. They're compromising the people who have trusted access, then using that trust to do things that look completely legitimate to every layer of automated verification.**

## What Actually Helps

I want to be careful not to make this sound hopeless, because it isn't. But some responses are more useful than others.

**Rotate everything reachable from affected machines.** If you had Nx Console installed and opened VS Code between whenever that update deployed and when it was pulled, treat every credential on that machine as compromised. AWS keys, GCP service account keys, npm tokens, SSH keys, GitHub PATs, Anthropic API keys. All of it. The payload specifically goes looking for these things.

**Pin GitHub Actions to commit SHAs, not tags.** The actions-cool attack worked by moving tags to point at a malicious commit. If your workflows reference `actions-cool/issues-helper@v1` (or any version tag), you're trusting that the tag still points to what it pointed at when you pinned it. With commit SHA pinning (`uses: actions-cool/issues-helper@COMMIT_SHA`), a tag redirect does nothing — you're fetching a specific immutable object, not whatever a mutable pointer happens to reference today.

```yaml
# Vulnerable
- uses: actions-cool/issues-helper@v3

# Safe
- uses: actions-cool/issues-helper@a520a44f96c8a793cda2b052b4e89d31b8a82b78
```

Tools like [Renovate](https://docs.renovatebot.com/) and [Dependabot](https://docs.github.com/en/code-security/dependabot) can keep these SHAs up to date automatically with proper review.

**Treat VS Code extension updates with the same skepticism you apply to npm dependencies.** The VS Code Marketplace does not have the same security tooling as npm. There are no equivalents to `npm audit`. Auto-update is on by default. Most organizations have no inventory of what extensions are installed on developer machines, let alone what versions updated when. This is a meaningful gap.

**Understand what Sigstore gives you and doesn't.** Valid signatures mean a package was built by someone who had a valid OIDC token for a given workflow/repo combination. They don't mean that workflow was running legitimate code. They don't mean the person who had that token wasn't compromised. Signatures verify *identity*, not *intent*. This distinction matters.

**Check for orphan commits in your own repos.** If your project has been targeted by supply chain attacks, or if any contributor's credentials have been compromised, run `git fsck --unreachable` to find objects in your repository that aren't reachable from any branch or tag. These are dangling commits that can be fetched if an attacker knows their SHA. The Nx Console attack used exactly this mechanism as a payload delivery system.

## The Uncomfortable Takeaway

We've spent several years building the Sigstore ecosystem precisely so that developers and organizations could have more confidence in the software supply chain. SLSA levels, provenance attestations, transparency logs — this is serious infrastructure built by serious people to solve a real problem.

What the Nx Console attack demonstrates is that these tools were designed to solve the problem of "how do you verify a legitimate package is legitimate?" They were not designed to solve the problem of "what happens when the entity doing the signing is compromised?" And that second problem is now the problem.

Every attack in this current wave — Nx Console, TanStack, actions-cool, Mini Shai-Hulud — traces back to a compromised human with legitimate access. Not a vulnerability in signing software. Not a flaw in the SLSA specification. A person whose machine or credentials got hit by a previous supply chain attack, whose stolen tokens were then used to attack their own project's users.

It's recursive. The supply chain attacks are now *creating* the compromised developer tokens that fuel the next supply chain attacks.

The verification layer was supposed to be the defense. It's now part of the attack surface. That's the thing worth sitting with today.

---

*Primary sources: [StepSecurity analysis of Nx Console compromise](https://www.stepsecurity.io/blog/nx-console-vs-code-extension-compromised) · [Official Nx Console security advisory GHSA-c9j4-9m59-847w](https://github.com/nrwl/nx-console/security/advisories/GHSA-c9j4-9m59-847w) · [THN: Compromised Nx Console 18.95.0](https://thehackernews.com/2026/05/compromised-nx-console-18950-targeted.html) · [THN: actions-cool/issues-helper compromise](https://thehackernews.com/2026/05/github-actions-supply-chain-attack.html) · [StepSecurity: actions-cool advisory](https://www.stepsecurity.io/blog/actions-cool-issues-helper-github-action-compromised-all-tags-point-to-imposter-commit-that-exfiltrates-ci-cd-credentials)*
