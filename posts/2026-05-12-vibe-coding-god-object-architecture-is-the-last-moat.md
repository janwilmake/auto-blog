# AI Writes Features, Not Architecture. One Developer Just Proved It Over 30 Weekends.

*May 12, 2026*

A developer named shvbsle spent seven months building a GPU-aware Kubernetes dashboard called k10s using nothing but vibe-coded sessions with Claude. 234 commits. ~30 weekends. Every feature prompt-to-code, never stopping to read the architecture.

Then the whole thing caved in on itself.

The [k10s postmortem](https://blog.k10s.dev/im-going-back-to-writing-code-by-hand/) hit the Hacker News front page this weekend with over 900 points and 560 comments — making it one of the most-discussed developer posts of the month. The story it tells is technically specific in a way that most AI-coding discourse isn't, which is exactly why it hit a nerve.

Here's what happened: the first few weeks were genuinely magic. Prompt Claude with "add a pods view with live updates" and it worked. Resource list views, namespace filtering, log streaming, describe panels — all vibe-coded in single sessions. Building at 10x normal speed felt incredible. The basic k9s clone took maybe three weekends.

Then he asked for the main selling point: a dedicated fleet view showing GPU allocation, utilization, temperature, power draw per node. Claude one-shotted it. Beautiful. It worked.

Then he typed `:rs pods` to switch back to the pods view. Nothing rendered. The table was empty. Live updates had stopped. Switching to nodes showed stale data from the fleet view's filter. The tab counts were wrong.

He calls what happened next the god object consuming itself.

---

## The God Object Problem Is the Architecture Problem

A god object is a classic anti-pattern: a single class or struct that knows everything about everything. It tends to emerge when code grows by accretion — when each new feature reaches directly into the center to get what it needs rather than communicating through clean interfaces. The code works fine when it's small. Then a change anywhere breaks something everywhere, and you can't find the thread.

The thing is, a god object is *exactly* what you'd expect to get from 30 weeks of "add a feature" prompts without architectural guidance.

Claude doesn't have a design philosophy. It has a context window. When you prompt it to add a fleet view to a TUI codebase, it looks at what exists and finds the path of least resistance to shipping working code. The path of least resistance in a growing codebase is almost always to wire things into whatever central object already has access to everything. That's the god object. Not because Claude doesn't know better — it absolutely does — but because you never told it to design an architecture, you told it to add a feature.

The developer figured this out only after seven months: *"AI writes features, not architecture. The longer you let it drive without constraints, the worse the wreckage gets. The velocity makes you think you're winning right up until the moment everything collapses simultaneously."*

He's now rewriting k10s in Rust, by hand, architecture first. Not because Rust is better than Go. Because, as he puts it, "it's the language I can steer. I've written enough of it to feel when something's wrong before I can articulate why. That instinct is the one thing vibe-coding can't replace."

---

## The Career Argument Nobody Wants to Make

Now combine this with a different post that's been generating comparable discussion: Sean Goedecke's ["Software engineering may no longer be a lifetime career."](https://www.seangoedecke.com/software-engineering-may-no-longer-be-a-lifetime-career/)

Goedecke's argument is blunt and uncomfortable. Using AI means you don't learn as much about performing the task. If that leads to skill atrophy — and it might — that still doesn't mean you *shouldn't* use AI, because the competitive pressure from engineers who do use it might leave you no choice. He compares it to professional athletes: fifteen good earning years, then the body gives out. We might be the first generation of software engineers who face the same cliff, just measured in cognitive terms rather than physical ones.

The pushback on HN was predictable: "Moving from assembly to C also made you worse at assembly." True. But that transition took decades and the skills transferred upward. This one is moving faster and the skills may transfer *sideways or not at all* — you get better at prompting and worse at reasoning about systems simultaneously.

Here's what I think both posts, read together, actually reveal: they're pointing at the same thing from different angles.

The k10s postmortem is a concrete, ground-level demonstration of *what skill atrophy looks like in production*. Seven months of building without designing produces a god object you can't refactor. The failure isn't "the AI wrote bad code." The code compiled. The features worked. The failure is that no architectural intuition was being exercised, so no architectural intuition was being built, and when the system hit a design constraint, there was no structural foundation to fall back on.

The Goedecke post is the career-level framing of the same dynamic. Each sprint you delegate to the AI is a sprint where you didn't develop the instinct for what a good design boundary looks like. Over years, that compounds.

---

## What's Actually the Moat Now

The obvious reaction here is: "So we should write code by hand again." But that's not quite right either, and the HN comments on the k10s post get at this directly.

The developer isn't swearing off AI. He's changing *where* the AI fits in his process. He's doing the architecture in a blank doc first — concrete interfaces, message types, ownership rules. Then he uses CLAUDE.md to communicate the decisions he's already made. Then he lets Claude implement within those constraints.

This is a materially different workflow. It's the difference between asking Claude to design your house and asking Claude to install the plumbing once you've drawn the floor plan. The value add is real in both cases; the risk profile is completely different.

So the moat isn't "writes code by hand." The moat is: *can you produce the floor plan?*

That means: do you have a mental model of what a clean service boundary looks like? Can you tell when a data structure is fighting its intended use? Do you know the difference between accidental complexity and essential complexity? Can you feel when something is wrong before you can articulate why?

These are skills that only come from building things and feeling them break. From debugging at 2am. From inheriting someone else's god object and having to unscramble it. From reading a diff and having your stomach drop before you can name the problem.

The developer who has spent the last two years vibe-coding every feature, never pausing to read the resulting architecture, is accumulating a speed advantage *right now* that may disappear the moment their system hits a constraint the AI can't see. The developer who is slower but still reads every diff, still thinks through ownership models, still argues about interface design — that developer is building the skill the other one is eroding.

---

## The Uncomfortable Conclusion

The k10s story isn't a cautionary tale about AI. The AI was just a tool. The cautionary tale is about mistaking *output velocity* for *engineering judgment*.

The models are good enough that you can ship features indefinitely without engaging your architectural intuition. That's new. Until recently, the act of writing code was also the act of exercising the muscle that knows when code is going in the wrong direction. Those two things have been decoupled. You can now generate enormous amounts of working code without ever engaging the part of your brain that would eventually recognize when the working code has become a trap.

The developers who will still be doing interesting, high-leverage work in five years are the ones who understand that gap — and who deliberately choose to stay in the loop not because it's faster, but because the loop is where the skill lives.

The others will have thirty weekends of velocity followed by a rewrite.

---

*Sources: [k10s postmortem](https://blog.k10s.dev/im-going-back-to-writing-code-by-hand/) — [HN thread, 900+ points](https://news.ycombinator.com/item?id=48090029) — [Sean Goedecke: Software engineering may no longer be a lifetime career](https://www.seangoedecke.com/software-engineering-may-no-longer-be-a-lifetime-career/) — [HN thread, 338+ points, 584 comments](https://news.ycombinator.com/item?id=42509408)*
