# VS Code's Hidden Copilot Co-Author Line: It Was Never About Attribution

*May 4, 2026*

On April 16, 2026, a pull request landed quietly in the `microsoft/vscode` repository. The title: ["Enabling ai co author by default."](https://github.com/microsoft/vscode/pull/310226) A product manager authored it. A principal engineer merged it with no description. No announcement. No blog post.

By the time developers noticed — roughly two weeks later, when VS Code 1.118 shipped — it had already affected an estimated 4 million commits on GitHub. Every one of them now contains a line the developer didn't type and, in many cases, didn't consent to:

```
Co-authored-by: GitHub Copilot <copilot@github.com>
```

The fallout was severe. The PR accumulated 372 downvotes against 2 upvotes. The GitHub discussion thread was locked as spam. By May 3, the engineer responsible, Dmitriy Vasyura, [publicly acknowledged three bugs](https://the-decoder.com/co-pilot-becomes-a-co-author-in-vs-code-without-being-asked/): it fires even when AI features are disabled, it attributes AI co-authorship to commits with no AI involvement, and the default shouldn't have changed without better test coverage. A fix is coming in version 1.119.

So it's over, right? A bad default, a quick rollback, lesson learned?

No. Because the bugs are not the point. The bugs are cover for the actual design intent — and that intent is worth examining carefully.

## The metric they were counting

Let me tell you what Microsoft was measuring when this PR landed.

GitHub's business case for AI rests heavily on a single statistic: what percentage of code on GitHub is AI-co-authored. Microsoft CEO Satya Nadella cited a version of this number in multiple earnings calls. Google claimed 75% of their internal code is written with AI assistance. Every major tech company is racing to have the biggest AI-contribution number to show analysts.

The `Co-authored-by: Copilot` trailer is a machine-readable signal. GitHub's platform tracks it. Setting the default to `all` — not just for Copilot Chat completions, but for *any* commit made while Copilot is installed — is the fastest possible way to inflate that number.

One HN commenter put it bluntly: *"Finally the usage metrics look amazing, the masses have woken up."* Another: *"Someone saw Google's claim that 75% of their code is written with AI and said 'hold my beer.'"*

The tell is in the scope. A principled AI-attribution system would tag commits where Copilot's output made a material contribution. This system tagged commits where Copilot was *installed* — including, multiple developers confirmed, commits made with `"chat.disableAIFeatures": true` explicitly set. The feature was not about accurate attribution. It was about maximum count.

This matters because there's a word for this: fraud-adjacent. Not legally — Microsoft almost certainly has the technical latitude to inject metadata into commits made through their tooling. But "AI-assisted 4 million commits this week" is a very different claim from "VS Code silently tagged 4 million commits as Copilot co-authored regardless of AI involvement," and only one of them ends up in the earnings deck.

## The legal layer nobody at Microsoft addressed

Beyond the metrics manipulation, there's a legal dimension that got less attention than it deserves.

In March 2026, the U.S. Supreme Court denied certiorari in *Thaler v. Perlmutter*, cementing the lower-court precedent that non-human entities cannot hold copyright. The US Copyright Office's position is firm: copyright requires human authorship. An AI cannot be a rights-holder.

So what does it mean to list `GitHub Copilot <copilot@github.com>` as a co-author on a Git commit?

The answers are genuinely unsettled, and not in a good way:

**If you're working on GPL code**: The GPL's copyleft provisions rest on copyright. They require that contributors have standing to grant the license. A non-human co-author has no legal standing. Does listing Copilot as a co-author create an ambiguity in the chain of attribution that could be exploited? Almost certainly not in practice — but ask any lawyer working on an M&A deal to sign off on it.

**If your company has an AI policy**: Many enterprises have explicitly prohibited AI-generated code from appearing in proprietary codebases, citing IP contamination risk. Some of those developers now have four million commits with a Copilot attribution line, injected by their IDE, even though they wrote every line by hand. That's not a hypothetical compliance headache — it's a real one.

**Who owns AI co-authored code?** The Copyright Office's guidance suggests that the *human* elements of a mixed human/AI work are still copyrightable. But listing a non-human as co-author muddies provenance in ways that lawyers haven't sorted out yet. The DEV Community analysis went further, suggesting Microsoft might actually be building a liability shield: by consistently tagging Copilot as co-author, they create a factual basis to argue shared responsibility in future IP infringement cases. That's either clever lawyering or paranoid conspiracy theorizing, but the ambiguity itself is the problem.

Microsoft shipped a feature with these implications as a silent default. No legal review in the changelog. No guidance. No opt-in.

## The design failure is the default, not the bugs

Vasyura's mea culpa was honest — three real bugs, real rollback, real accountability. That's better than most corporate responses to this kind of backlash.

But the thing he didn't address: *even if those bugs were fixed*, the feature as designed was wrong.

The correct model for AI attribution is opt-in, not opt-out. Cursor does it this way. Claude Code does it this way. Sourcegraph Cody does it this way. In every case, the developer decides whether to signal AI involvement, and can review exactly what that signal says before it's committed to history.

Git commit metadata is not a place for your IDE's commercial interests. Git history is a record that outlasts any particular tool, any particular company policy, any particular lawyer's interpretation of the law. The `Co-authored-by` field is used by the Linux kernel for Developer Certificate of Origin sign-offs. It has real meaning in serious projects. Inserting a commercial AI product into that field by default — especially when the AI didn't contribute — is not a feature. It's vandalism with a business case.

VS Code's unique identity has always been that it *felt like yours*. An open-source core, a neutral surface, a thing you configured rather than a thing that configured you. This PR made that boundary porous in a way that matters.

## What to do if you're affected

First, check your damage: `git log --grep="Co-authored-by: Copilot"` will show you affected commits. If you committed to a project during the 1.117/1.118 window, you may have these.

Second, disable the feature if you haven't: set `"git.addAICoAuthor": "off"` in your VS Code settings. It's still present, just rolled back to off-by-default in 1.119.

Third, if you need to clean up commits: `git rebase -i` can let you edit commit messages. For public history you've already pushed, that's a bigger conversation with your collaborators or project maintainers.

Fourth: treat this as a prompt to actually think through your team's AI attribution policy before your tooling does it for you. Do you want to mark AI-assisted commits? If so, what threshold counts? Who decides? If you don't have an answer, you're one VS Code update away from someone else deciding for you.

The irony in all of this is that thoughtful AI provenance tracking is actually a good idea. Teams increasingly need to know which parts of their codebase had AI involvement — for security review, for compliance, for institutional knowledge about what was written with judgment versus what was generated. A commit trailer is not a crazy way to track that.

But attribution is only valuable when it's accurate. The moment you start counting commits where AI features were disabled as AI-co-authored, you've poisoned the signal. That's not a minor implementation bug. It's the design.

**Primary sources:**
- [VS Code PR #310226 — Enabling ai co author by default](https://github.com/microsoft/vscode/pull/310226)
- [The Decoder — Microsoft caught sneaking "Co-Authored-by Copilot" into VS Code commits](https://the-decoder.com/co-pilot-becomes-a-co-author-in-vs-code-without-being-asked/)
- [HN discussion — VS Code inserting 'Co-Authored-by Copilot' into commits regardless of usage](https://news.ycombinator.com/item?id=47989883)
- [ByteIota — VS Code Copilot Co-Author Default: Copyright Chaos Ensues](https://byteiota.com/vs-code-copilot-co-author-default-copyright-chaos-ensues/)
- [GitHub community discussion — Copilot silently inserts itself as co-author](https://github.com/orgs/community/discussions/194075)
