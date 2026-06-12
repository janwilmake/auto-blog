# npm v12 Turns Off Install Scripts by Default. Yes, This Will Break Your Builds. Do It Anyway.

**Date:** 2026-06-12

npm v12 is coming in July, and it's shipping the most significant security-default change in the package manager's history: `npm install` will no longer execute `preinstall`, `install`, or `postinstall` scripts from your dependencies automatically. Git dependencies and remote URL dependencies are blocked too. All of it becomes opt-in.

GitHub published the [full changelog on June 9](https://github.blog/changelog/2026-06-09-upcoming-breaking-changes-for-npm-v12/). If you run Node.js projects and haven't read it yet, put this article down and go read it first.

The reaction in developer communities has been predictable: half the thread is "finally," and the other half is "this is going to break everything." Both are correct. But here's my take: the disruption is the point, and the people upset about it should be more upset about how long this took.

## What's Actually Changing

Three defaults flip at once in v12:

**`allowScripts` defaults to off.** When you run `npm install`, lifecycle hooks — `preinstall`, `install`, `postinstall` — from your dependencies and their transitive dependencies no longer run. This includes implicit `node-gyp` builds. If you have a native addon in your dependency tree (anything with a `binding.gyp`), it silently stops working until you explicitly approve it. The new workflow: run `npm approve-scripts --allow-scripts-pending` to see what's blocked, approve the packages you trust with `npm approve-scripts`, and commit the resulting allowlist to `package.json`.

**`--allow-git` defaults to `none`.** Any Git dependency in your `package.json` — or buried in your transitive tree — gets blocked. This closes a specific attack vector where a git dependency's `.npmrc` could override the git executable even when you ran with `--ignore-scripts`.

**`--allow-remote` defaults to `none`.** Dependencies pointing at remote URLs (HTTPS tarballs) are blocked. The related `--allow-file` and `--allow-directory` flags are unchanged.

None of this is a surprise if you've been paying attention. GitHub has been announcing these changes incrementally since February. npm 11.10.0 added `--allow-git`. npm 11.15.0 added `--allow-remote`. npm 11.16.0 (available now) surfaces warnings for everything that will break in v12. The tooling has been there to prepare. Most people haven't.

## Why Now, and Why This Specifically

The Shai-Hulud family of npm supply chain attacks — starting with the [original worm in early 2026](https://unit42.paloaltonetworks.com/monitoring-npm-supply-chain-attacks/), running through the [TanStack/Mini Shai-Hulud cache poisoning attack in May](posts/2026-05-15-tanstack-mini-shai-hulud-ci-pipeline-is-the-attack-surface.md), and culminating in the [Red Hat Miasma worm in June](posts/2026-06-05-red-hat-npm-miasma-open-source-attack-toolkit-franchise.md) — all shared one thing: the kill chain ran through automatic install script execution. The attacker gets a package installed, the postinstall hook fires, arbitrary code runs with the full permissions of the developer's machine or CI runner, and suddenly your GitHub secrets, AWS keys, and Kubernetes configs are in someone else's hands.

This attack class is *seven years old*. The [feature request to make install scripts opt-in was filed on GitHub in 2019](https://github.com/npm/rfcs/discussions/80). The community has been asking for this for as long as the attack surface has been documented. And in February 2026, after one particularly bad worm that propagated through `@bitwarden/cli`, someone in the npm maintainers' feed explicitly cited Shai-Hulud as the reason this had to finally ship.

So yes: npm v12 is reacting to attacks that were already happening at scale. That's not a great look for a seven-year delay. But it's happening, and it's the right call.

## What Will Actually Break

The honest answer: a lot of things, for a while.

**Native addons are the biggest category.** Anything built with `node-gyp` — C++ bindings for databases, image processing, compression libraries, cryptographic hardware — relies on that implicit `node-gyp rebuild` that npm used to run automatically. After v12, you'll need to explicitly approve the package. For well-known packages like `sqlite3`, `sharp`, `bcrypt`, or `canvas`, that's a one-time `npm approve-scripts` invocation. For enterprise monorepos with dozens of native dependencies, this is an afternoon of archaeology.

**Anything pulling from Git directly.** Some packages, some internal tooling, and a surprising amount of legacy code specifies dependencies as `git+https://` URLs. All of that breaks. Some of it deserves to break — "install from this arbitrary git URL" is not a supply chain practice you should be doing in 2026 anyway.

**CI pipelines that have never been audited.** This is the real liability. An enormous number of CI pipelines run `npm install` and assume it works. After v12, pipelines that've never been reviewed against a script allowlist will start failing silently (or loudly, depending on your error handling). The teams that will get hurt the worst are the ones who haven't committed a `package.json`-based allowlist and whose CI environment can't easily run `npm approve-scripts` interactively.

**Package maintainers who never documented their install scripts.** If you publish a package that uses an install script and you haven't told anyone about it, your users are about to find out the hard way. The right move is to add documentation now and either ship prebuilt binaries (using `prebuild`, `prebuildify`, or `node-pre-gyp`) or convert setup to an explicit post-install command that users run intentionally.

## How to Prepare Right Now

The migration path is well-designed. Here's what to actually do:

1. **Upgrade to npm 11.16.0 today.** `npm install -g npm@11` gets you there. At 11.16.0, you get advisory mode: warnings surface for everything that will break in v12, but nothing actually breaks yet.

2. **Run the audit.** `npm approve-scripts --allow-scripts-pending` lists every package in your dependency tree that has install scripts. Don't pass `--allow` yet — just look at the list. For most projects, you'll recognize everything. For some projects, you'll find three packages you've never heard of running code at install time, which is precisely the point.

3. **Make decisions package by package.** `npm approve-scripts <package>` adds a package to your allowlist, pinned to the current version you've reviewed. `npm deny-scripts <package>` blocks it explicitly. The result gets written to `package.json` and should be committed to source control so the whole team shares the same policy.

4. **Update your CI setup before July.** If your CI pipeline runs `npm install` and you don't update it before v12 ships, something will break on a Friday. The `approve-scripts` workflow runs in CI too — you can bake the allowlist into the repo and let it propagate automatically.

5. **If you maintain packages, read the maintainer guidance.** The [npm docs on `allow-scripts` config](https://docs.npmjs.com/cli/v11/using-npm/config) cover what maintainers need to do. Short version: document it, ship prebuilts where you can, and consider moving away from install scripts entirely for anything that doesn't absolutely require them.

## The Bigger Point

The framing I keep seeing in negative reactions — "this is going to break my workflow" — misunderstands what the change is actually doing. Your workflow was already broken. It just hadn't bitten you yet.

Running `npm install` as an implicit trust grant to every piece of code in your transitive dependency tree — letting it execute arbitrary scripts at full system privilege, with access to your environment variables and filesystem — was never a reasonable default. It was a convenience that the ecosystem normalized because the attacks it enabled were, for a while, infrequent and unsophisticated enough that you could get away with not thinking about it.

That era ended. The Shai-Hulud worm proved you can't rely on package maintainers to keep their release pipelines clean. The Miasma attack proved that once someone builds a weaponized toolkit for this attack class, anyone can run it. The [2019 RFC that first proposed opt-in install scripts](https://github.com/npm/rfcs/discussions/80) described exactly the threat model we've been living through. It just took seven years and a self-propagating worm that hit OpenAI, Bitwarden, and Red Hat to make it happen.

npm v12 doesn't solve supply chain security. You can still install malicious packages — the payload doesn't have to run at install time to cause harm. But it removes the easiest, most-automated attack path that's been responsible for the majority of high-impact incidents in the last 18 months.

The transition will be annoying. Update anyway.
