# RFC 10008: HTTP Finally Has a Verb That Means "Read This Complex Query" — Five Years After Anyone Needed It

**June 19, 2026**

On June 15th, the IETF published [RFC 10008](https://www.rfc-editor.org/info/rfc10008/), which defines a new HTTP method called `QUERY`. It's been on Hacker News front page for two days, running at 447 points and 262 comments. The comments are exactly what you'd expect: one half of the thread is explaining why this was always needed, the other half is explaining why they've been doing it with POST for a decade and everything is fine.

Both camps are right. And that's the interesting part.

---

## The Problem QUERY Solves

Here's the scenario. You're building a search API. Users need to send complex filters: date ranges, nested conditions, arrays of tags, full-text expressions. The query is inherently stateless — it doesn't mutate anything, it's idempotent, you want it cached. It is, semantically, a read.

But GET requests aren't supposed to have a body. The HTTP spec forbids it (semantics, not wire protocol), and the practical consequence is that load balancers, CDN edge nodes, and reverse proxies will silently drop GET bodies. You cannot rely on them surviving transit.

So what do you do? You have three bad options:

**Option 1: Encode everything in the URL.** Works until your query has 200 parameters, a JSON filter expression, or a long full-text clause. URLs have no formal length limit, but the de facto ceiling is around 2000 characters in most environments. You also put your user's search terms in server logs, which is a privacy problem.

**Option 2: Use POST.** This is what everyone does. GraphQL is essentially this — a POST to `/graphql` with a JSON body. It works fine until your infrastructure tries to cache it, because POST is not safe or idempotent, so every CDN and reverse proxy treats it as "do not cache, pass through."

**Option 3: Use a WebDAV method like SEARCH or PROPFIND.** These exist. They're safe and idempotent with request bodies. They're also "we originate from the WebDAV activity, about which many have mixed feelings," as RFC 10008's appendix diplomatically puts it. No one is using `SEARCH` requests for their production search API and feeling good about it.

`QUERY` is supposed to be Option 4: a method that is explicitly **safe** (doesn't change server state), **idempotent** (same request produces same result), and **cacheable** — but also explicitly allows a request body.

---

## Why This Took Until 2026

The draft has been circulating since at least 2021 under the name "safe-method-w-body." Before that, there was a long-running debate inside the HTTP working group about whether the right answer was to add body semantics to GET (simpler, more intuitive) or create a new method.

The working group chose a new method, and the reasoning is in the RFC's appendix: GET-with-body was rejected due to "historical interoperability issues and strict compliance with the core architectural definitions of HTTP." Translation: there are too many proxies and intermediaries in the wild that would mangle a GET with a body in unpredictable ways. A new method name is a signal to the entire stack: this is a different thing, route it accordingly.

This is the right call, even though it's frustrating. The web's infrastructure is a distributed system with no upgrade mechanism. You can't send a memo to every nginx instance saying "hey, GET bodies are valid now." A new method name lets infrastructure maintainers add explicit support without breaking existing behavior.

The downside is that new method adoption takes years. Most HTTP client libraries will support QUERY quickly (it's a string). Framework and server support takes longer. CDN edge caching with QUERY semantics will take the longest — that's where the actual caching benefit lives, and CDN vendors have to ship it.

---

## What QUERY Actually Changes for API Developers

If you're building a new API today, here's the honest picture:

**The immediate win is intent clarity, not caching.** If you use QUERY for your search endpoints, you are explicitly communicating to every layer in the stack (and to every developer reading your code) that this operation is safe and idempotent. You can't accidentally hang a POST handler on it that writes to a database. A middleware that logs mutations won't flag your search queries. Your API documentation becomes clearer. This is worth something.

**The caching win is theoretical until CDNs ship it.** The whole point is that a QUERY response can be cached the same way a GET response can — by URL plus request body hash. This would let you cache complex search results at the edge without building your own query-to-URL hashing scheme. But this requires your CDN to understand QUERY semantics. Cloudflare, Fastly, and Akamai will eventually ship this; their engineers are already in the relevant IETF discussions. Until then, you're caching it yourself or using a query ID redirect pattern.

The redirect pattern is worth understanding. RFC 10008 explicitly supports a response to QUERY that includes a `Location` header pointing to a GET URL for the same results. The server can compute a stable hash of the query body, cache the results internally, and redirect the client to `/search?q=sha256:ABCDEF123`. The client caches that mapping. Subsequent identical queries become GET requests that the entire CDN stack can cache normally. This works today, before any CDN ships native QUERY caching.

**The "privacy is better than GET" argument is real but small.** RFC 10008 calls out that request bodies are "less likely to be logged than request URIs." This is true — your request content doesn't show up in nginx access logs, doesn't get indexed in error reporting, doesn't leak into browser history or bookmarks. For search queries containing personal information, this is a meaningful privacy improvement over encoding everything in the URL.

---

## The GraphQL Angle

The Hacker News thread has several comments pointing out that this is basically what GraphQL has needed for years. GraphQL's decision to use POST for all queries is a known limitation: you can't cache GraphQL queries at the CDN layer without a persisted query ID system (Apollo, Relay, etc. all have one). Every time someone says "GraphQL doesn't cache well," POST-for-reads is the root cause.

Whether the GraphQL specification will adopt QUERY is a separate governance question and probably not the right battle. Persisted queries work well enough for production deployments. But for anyone designing a new query API — REST, GraphQL-inspired, JSON:API, whatever — QUERY is now the semantically correct choice.

---

## Should You Use It Now?

The honest developer answer: **start using it in new services where you control both client and server.** The rollout path is:

1. Add QUERY handling to your server alongside your existing POST handler for the same endpoint. Return identical responses to both. This is additive and backwards compatible.
2. Update your API client to prefer QUERY when the server supports it (you can signal this with the `Accept-Query` response header, which RFC 10008 defines for exactly this purpose).
3. Add QUERY support to your edge caching when your CDN ships it.

The path from draft to "this just works everywhere" usually takes three to five years for an HTTP method. PROPFIND from WebDAV (RFC 4918) still confuses CDNs in 2026. QUERY has better messaging, a clearer use case, and active corporate sponsorship from companies that want better caching semantics. Expect meaningful infrastructure support by late 2027 to 2028.

In the meantime, you can use it today for the intent-clarity and logging-privacy benefits, while keeping your POST-based fallback for clients that haven't updated yet.

---

## The Larger Point

What I find genuinely interesting about this RFC is that it's a rare case of the standards process doing something right: identifying a real pattern that the entire industry is implementing wrong (POST-for-reads), and giving it a first-class name instead of blessing the hack. The GraphQL ecosystem built persisted queries. REST APIs built query DSLs crammed into URL parameters. Both approaches work and both have real costs.

A five-year standardization process to name a thing you've been doing informally for a decade feels slow. But the resulting standard is now unambiguous, has caching semantics defined by people who understand HTTP deeply, and ships as part of the Internet Standards Track. That matters.

Every time a developer adds `QUERY /search HTTP/2` to a codebase, a proxy maintainer updates their routing logic, and a CDN ships proper cache key semantics for request bodies, a long-running piece of web infrastructure gets cleaner. That's what standards are for.

---

*Sources: [RFC 10008 – The HTTP QUERY Method](https://www.rfc-editor.org/info/rfc10008/) (RFC Editor, June 15 2026); [HN thread](https://news.ycombinator.com/item?id=48568502) (135 points, growing); [api-platform/core QUERY discussion](https://github.com/api-platform/core/discussions/7618); [new-http-query-method-explained](https://kreya.app/blog/new-http-query-method-explained/) (Kreya, June 17 2026)*
