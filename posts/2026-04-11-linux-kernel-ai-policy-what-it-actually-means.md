# The Linux Kernel Just Settled the AI Contribution Debate — At Least for Itself

**April 11, 2026**

The Linux kernel now has an official policy on AI coding assistants, and it's worth reading carefully — because the way they've handled it is both more thoughtful and more pragmatic than almost anything else happening in the open-source AI debate right now.

The document, [added to the kernel's documentation tree in January](https://docs.kernel.org/process/coding-assistants.html) and hitting Hacker News today with nearly 350 points, boils down to three decisions:

1. **AI agents cannot sign off on their own work.** The `Signed-off-by` tag — the Developer Certificate of Origin (DCO) — is a legal certification that only a human can make.
2. **AI assistance must be disclosed.** If an AI tool helped write or shape a contribution, it gets an `Assisted-by` tag in the commit.
3. **The human submitter is fully responsible.** They review the code, certify the license compliance, and put their name on it.

That's it. No ban. No panic. No lengthy philosophical treatise about whether AI will destroy open source. Just: here's how we track it, here's who is legally accountable, and here's where to find the existing contribution guidelines that still apply.

If more of the open-source world worked like this, we'd have a lot fewer flamewars.

---

## The Landscape This Lands In

To understand why this matters, you need to see what the Linux kernel's policy is reacting against.

The last eighteen months have produced an increasingly chaotic patchwork of AI stances across major projects. NetBSD and Gentoo have outright banned "tainted" AI-generated code. QEMU's ban stems from the argument that AI can't satisfy the DCO (an argument the kernel policy directly refutes by requiring *human* DCO certification of *AI-assisted* work). Ghostty — the terminal emulator — reportedly bans contributors entirely if they're found using AI tools. cURL's Daniel Stenberg has talked openly about closing bounties because AI-generated submissions were 95% garbage.

On the other side, VS Code, Home Assistant, and Godot have no restrictions at all. EFF published a formal policy requiring human review of any AI-assisted contributions — which is essentially what the kernel is now doing too.

And then there's Linus Torvalds himself. Phoronix has documented emails where he's expressed strong personal skepticism about AI-generated contributions. The kernel's formal policy is notably more permissive than Torvalds's apparent personal instincts, which suggests some internal debate landed on the pragmatic side. In 2026, banning AI assistance entirely from kernel development would be like banning Google — you'd be creating an impossible-to-enforce rule that developers would simply ignore.

---

## The `Assisted-by` Tag Is the Clever Part

Most of the discussion around this policy has focused on what it *prohibits* (AI signing off) rather than what it *requires* (the `Assisted-by` tag). That's the wrong emphasis.

The tag format is:

```
Assisted-by: AGENT_NAME:MODEL_VERSION [TOOL1] [TOOL2]
```

For example:

```
Assisted-by: Claude:claude-3-opus coccinelle sparse
```

This is genuinely useful, and not just for compliance theater. Here's why:

**It creates a historical record.** In five years, you'll be able to grep the kernel's git history and see which parts of the codebase had AI assistance, from which tools, at which versions. When a bug turns up in code that was `Assisted-by: SomeModel:v1`, you'll have a data point. When the next paper comes out claiming AI-assisted code has higher (or lower) defect rates, kernel developers will have actual evidence from their own tree, not just controlled benchmarks.

**It makes accountability concrete.** The human reviewer's `Signed-off-by` tag sitting next to an `Assisted-by` tag is a visible, searchable signal that the human explicitly took responsibility for reviewing AI output. It's not just a policy checkbox — it's a paper trail.

**It's architecturally honest.** The kernel already has tags for static analysis tools (`coccinelle`, `sparse`, `smatch`). Slotting AI assistance into the same disclosure mechanism signals that the kernel maintainers see AI tools as a category of development tool, not some qualitatively different class of existential threat. That's the right frame.

---

## The DCO Problem Is Real, and the Kernel Solved It Correctly

The QEMU project's ban on AI contributions rests on the argument that AI-generated code can't satisfy the Developer Certificate of Origin because an AI can't legally certify that code is original, properly licensed, and not derived from proprietary sources.

That's true! An AI can't certify any of that. But the kernel's solution is obvious in retrospect: the *human* certifies it. The human reviews the AI-generated code, verifies its compliance (or fails to, at their legal peril), and adds their own `Signed-off-by`. The AI doesn't disappear from the record — the `Assisted-by` tag ensures it's visible — but the legal certification sits with the person who can actually make it.

This is how open-source license compliance has always worked. A human can copy-paste proprietary code and lie about its provenance in a `Signed-off-by` tag. AI doesn't introduce a new *category* of risk; it changes the *probability distribution* of risk and the *tooling* needed to evaluate it. The kernel's solution acknowledges this without catastrophizing.

The Hacker News thread has a comment that captures this well: "I could easily copy-paste proprietary code, sign my name that it's not and that it complies with the GPL and submit it. At the end of day, it just comes down to a lying human." AI assistance doesn't change that fundamental dynamic. What it might change — and this is a legitimate ongoing concern — is the *volume* and *detectability* of problematic code.

---

## What This Doesn't Solve

Let's be clear about the limits of this policy.

It doesn't solve the quality problem. The kernel's maintainers have been dealing with an increasing volume of low-effort, AI-generated patch submissions — the sort of thing that gets sent speculatively because it costs the sender nothing. The `Assisted-by` tag doesn't help here at all. If anything, by normalizing AI assistance, the policy might increase this noise, unless maintainers treat it as a signal for extra scrutiny (which some probably will, at least initially).

It doesn't solve the copyright problem. The legal questions around AI training data and derivative works remain genuinely unresolved. A `Signed-off-by` from a human doesn't retroactively resolve whether an AI model was trained on GPL code in ways that create license obligations. This is live litigation territory and no amount of kernel documentation can settle it.

And it doesn't stop the cultural war. The HN thread has comments predicting "Linux has fallen" and people calling for a "pre-clanker Linux" fork. These are the same people who've been predicting Linux's death via [insert concern here] for twenty years. The kernel has survived systemd, Rust, CoC controversies, and Linus's various sabbaticals. It'll survive this too.

---

## Why Other Projects Should Take Notes

The kernel's approach won't work for every project. A small project with limited maintainer bandwidth might reasonably decide that requiring human review of AI-assisted code creates more overhead than it's worth, and just ban it outright to avoid the burden. That's a legitimate call.

But for large, heavily-maintained projects — the kind where you have a structured review process already — the kernel's framework is a template worth stealing:

- **Disclose, don't ban.** Bans are unenforceable and create adversarial dynamics with contributors. Disclosure creates data and accountability.
- **Keep humans in the legal chain.** Whatever tools assist the work, a human has to sign off. This is both legally sound and practically necessary.
- **Use existing process infrastructure.** The `Assisted-by` tag fits cleanly into the commit message conventions that kernel developers already use. No new workflows, no new tools, no new bureaucracy.
- **Be boring about it.** The kernel policy reads like documentation, not a manifesto. That's intentional and it's right.

---

## The Bottom Line

The Linux kernel's AI contribution policy is not exciting. It's not going to satisfy the people who want a hard ban on AI-generated code, and it's not going to satisfy the people who want AI treated as just another contributor. It's a bureaucratic compromise that prioritizes legal accountability, traceability, and continuity with existing process.

That's exactly what good governance of a critical infrastructure project looks like.

The projects currently losing their minds about AI contributions could learn something from the boring confidence with which the kernel's maintainers said: here are the rules, here's the tag format, here's the existing documentation you were already supposed to read. Now go contribute.

---

*Sources: [AI Coding Assistants — Linux Kernel Documentation](https://docs.kernel.org/process/coding-assistants.html) · [Announcement thread on Hacker News](https://news.ycombinator.com/item?id=47721953) (349 points, 259 comments) · [Haiku OS forum roundup of open-source AI policies](https://discuss.haiku-os.org/t/heavily-moderated-what-are-your-thoughts-on-allowing-ai-on-the-forum/19041) · [PCMag: WireGuard & VeraCrypt signing saga](https://www.pcmag.com/news/microsoft-mysteriously-freezes-accounts-for-veracrypt-wireguard-windscribe)*
