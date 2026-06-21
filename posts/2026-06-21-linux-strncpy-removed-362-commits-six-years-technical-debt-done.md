# Linux Just Deleted a Function. It Took 362 Commits and Six Years. That's How You Actually Fix Technical Debt.

*June 21, 2026*

Sometime last Friday, [a single merge commit](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1a3746ccbb0a97bed3c06ccde6b880013b1dddc1) landed in the Linux 7.2 tree and quietly deleted `strncpy()` from the kernel entirely. No fanfare. [Seven comments on Phoronix](https://www.phoronix.com/news/Linux-7.2-Drops-strncpy). 211 points on Hacker News.

The actual work was around 362 commits, spread across six years, touching drivers, filesystems, network code, architecture-specific string implementations, and dozens of other subsystems — all to eliminate a function that has caused bugs for fifty years.

This is one of the most useful stories in software engineering you'll read all year. Not because of the function itself, but because of what it says about how you actually fix the problems that are genuinely hard to fix.

## Why strncpy Was a Problem in the First Place

The short version: `strncpy()` does not do what most programmers think it does.

People reach for it when they want "a bounded strcpy" — copy a string, but stop after N bytes so you don't overflow the buffer. That's a reasonable thing to want. The problem is that `strncpy()` predates that use case. It was originally written to copy file names into fixed-width directory entries in early Unix — those 14-byte fields that weren't NUL-terminated, just zero-padded. It was built to zero-fill the entire destination. Copy a 3-character filename into a 14-byte field? You get 3 characters of name and 11 bytes of zeros. That was the design.

The trap is when you use it as a "safe strcpy." If your source string is exactly N bytes long — fitting the buffer with no room to spare — `strncpy()` will *not* NUL-terminate the destination. You now have a buffer that looks like a string but isn't. The next call that reads it as a string walks off the end. Security vulnerability. Data corruption. Mysterious crash.

An HN commenter [put it simply](https://news.ycombinator.com/item?id=48612943): "Whenever I've been asked to review C code, I always looked for strncpy and always found a bug with it."

Another commenter who was actually on the C89 standards committee quoted the rationale directly: *"strncpy was initially introduced into the C library to deal with fixed-length name fields in structures such as directory entries... strncpy is not by origin a 'bounded strcpy.'"*

So it was never a safe string function. It was misused as one, and it caused bugs for decades.

## The Replacement: strscpy

The Linux kernel's answer is `strscpy()`, introduced in 2017. It does what people actually wanted: copies up to N-1 bytes and always NUL-terminates. If the source is longer than the destination, it truncates — and importantly, it *tells you* it truncated by returning a negative value. You can check the return code. You know something was cut off.

That seems obvious in retrospect. Of course you want a string-copy function to always produce a valid string and tell you if it truncated. The tragedy of `strncpy()` is that the right answer wasn't obvious *until the wrong answer was already everywhere*.

The kernel also has `strscpy_pad()` for the original zero-padding use case, `strtomem_pad()` for non-NUL-terminated fixed-width fields, and plain `memcpy()` for everything else. There's now a function for each actual use case rather than one function that sort-of-works for all of them.

## Why It Took Six Years and 362 Commits

This is the part worth sitting with.

The function was deprecated. The better alternative existed. Everyone agreed `strncpy` was bad. And yet it took six years to eliminate it from a codebase of approximately 35 million lines.

Why?

Because technical debt removal in a large, active codebase is not a project you schedule and complete. It's a campaign you run in parallel with everything else. Every `strncpy` call is in a different driver, written by a different maintainer, serving a different purpose. Some were actually using the zero-padding behavior intentionally — those couldn't just be mechanically replaced with `strscpy()`. Some were in architecture-specific code that required careful review to confirm the semantics were equivalent. Some were in code that nobody actively maintains anymore, so the patch sat in a queue.

You also can't just do a global search-and-replace. The replacement function has a different return type. The calling code might need to handle the truncation case. You have to understand each call site.

Kees Cook, the kernel security developer who drove most of this work, noted in March 2026 when he did the final six instances: "We can remove strncpy() from the Linux kernel finally!" It took him — and dozens of other contributors — six years to get to that exclamation point.

## What This Should Tell You About Your Codebase

The uncomfortable truth for most software teams is that the Linux kernel handled this *better* than almost any commercial codebase would have.

A few things that made it work:

**Deprecation annotations.** The kernel marked `strncpy` as deprecated in kernel-doc long before the removal campaign began. New code triggering the deprecated call was flagged. The codebase didn't get worse while the old uses were being cleaned up.

**No "big bang" approach.** Nobody scheduled a two-week sprint to remove all 362 uses at once. Individual maintainers fixed the calls in their own subsystems on their own timeline, as part of other work. The progress was steady rather than heroic.

**A clear successor.** The replacement wasn't "write it yourself, case by case." It was "use `strscpy()`, and here's exactly why it's better." Developers could make the change confidently because the tradeoff was documented.

**Finishing.** This is underrated. A lot of deprecation campaigns get to 80% and stall. Someone does the math on the remaining cases and decides they're too obscure to bother with. The Linux kernel project finished. The API is actually gone now — you cannot accidentally use it in new code even if you wanted to.

In most commercial codebases, the equivalent story ends with "we have a policy against using `strncpy()` in new code, and there are 847 legacy uses we're planning to address eventually." That eventually never comes, the legacy code rots, and the bug you were trying to prevent continues to happen.

## The Broader Pattern

The strncpy removal sits in a larger Linux 7.2 cleanup story. The same release [removed the last optimized MD5 implementation](https://www.phoronix.com/news/Linux-7.2-Drops-strncpy) from the kernel. It deprecated AF_ALG, an insecure and useless crypto driver interface. It removed a DoS vector where timers could be set in the past. Multiple long-running cleanups all crossing the finish line in the same release cycle.

This is what a mature engineering culture looks like. Not the absence of technical debt — every large, long-lived codebase has it — but a systematic, patient process for reducing it without halting development.

The strncpy campaign cost approximately zero downtime and zero features. It made the kernel slightly safer and slightly cleaner. It will never appear in a changelog that anybody reads at a launch event. It just quietly happened, because enough people cared enough to finish it.

That's worth celebrating.

---

*Sources: [Phoronix: Linux Finally Eliminates The strncpy API](https://www.phoronix.com/news/Linux-7.2-Drops-strncpy), [kernel.org merge commit](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1a3746ccbb0a97bed3c06ccde6b880013b1dddc1), [Hacker News discussion](https://news.ycombinator.com/item?id=48612943), [LWN: strncpy history](https://lwn.net/Articles/507376/), [Kees Cook on Mastodon](https://hachyderm.io/@kees/116282745861595200)*
