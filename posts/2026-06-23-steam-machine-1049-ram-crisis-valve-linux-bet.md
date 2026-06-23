# The Steam Machine Launched Today at $1,049. That Price Is the Whole Story.

Valve's Steam Machine reservations went live this morning, and Hacker News immediately handed it 1,300 points and over a thousand comments. That level of engagement for a gaming peripheral announcement tells you something: people care about what this means beyond the box itself. The price — $1,049 for the 512GB base model, $1,349 for 2TB — is simultaneously the most criticized thing about it and the most important thing to understand.

Let me explain why I think the critics are half right, and why the people who dismiss it entirely are missing what Valve is actually doing.

## What Launched Today

The [Steam Machine](https://store.steampowered.com/hardware/steammachine) is a living-room desktop gaming PC. It runs SteamOS 3.8 — the same Linux-based OS on the Steam Deck, now expanded for desktop AMD GPU support. The hardware is:

- 6-core, 12-thread AMD Zen 4 CPU, boosting to 4.8 GHz
- Semi-custom RDNA 3 GPU, 28 compute units at ~2.45 GHz, **8 GB dedicated GDDR6 VRAM**
- 16 GB DDR5 system RAM (separate from the VRAM, unlike the Deck's shared pool)
- 512 GB or 2 TB NVMe SSD
- ~140W total TDP (30W CPU + 110W GPU)
- SteamOS, with the option to install Windows if you want

This is not a handheld. This is a small-form-factor PC designed to sit under your TV. Valve calls it "6x more powerful than the Steam Deck" — a marketing number, but not a dishonest one. The GPU alone has roughly 7× the compute power of the Deck's integrated RDNA 2 block, and the dedicated GDDR6 removes the shared-memory bottleneck that caps the Deck at any resolution above 800p.

It was announced in [November 2025](https://store.steampowered.com/news/group/45479024/view/578276333072679812) alongside the Steam Controller and Steam Frame. Then came delays — first "early 2026," then "first half of 2026," then just "this year" — because of the AI-fueled memory shortage that drove GDDR6 and DDR5 prices through the roof. The 16 GB of DDR5 RAM inside this machine would have cost you ~$250 to source on your own earlier this year. Valve absorbed that for as long as it could.

## The Price Is Real, and So Is the Context

$1,049 is expensive for a gaming device positioned next to a $449 PS5. The IGN review says it clearly: ["a bit too expensive to take on the PS5 or the Xbox Series X."](https://www.ign.com/articles/steam-machine-review) That's a fair read if you're comparing sticker prices to console sticker prices.

But there are three things that make the comparison more complicated.

**First, the RAM shortage is not Valve's fault and it's not over.** The ongoing datacenter buildout — every AI hyperscaler buying GDDR6 by the pallet — has created a memory market that looks nothing like 2021. Xbox and Sony built their consoles when GDDR6 cost a fraction of what it does today. They also benefit from massive scale (93 million PS5s shipped) that lets them negotiate component prices Valve simply cannot match at launch volume. When IGN writes that "the 16GB of RAM alone would cost nearly $250," that's not an excuse — it's a real supply chain number.

**Second, this is not subsidized hardware.** Sony and Microsoft sell consoles at or near cost because they make money on software royalties (30% of every game sold on the platform). Valve does not have that option with SteamOS — it's Linux, it's open, and Valve doesn't take the same kind of royalty cut from games distributed through third-party stores. The hardware has to carry more of the margin. That's a structural difference, not a pricing failure.

**Third, the specs are broadly comparable to a PS5 — not a PS5 Pro, not an Xbox Series X at its best.** [Hardware analysis from multiple outlets](https://twicethebits.com/2025/11/12/how-does-the-new-steam-machine-stack-up-against-the-xbox-series-x-and-ps5/) places the Steam Machine in PS5 territory at 1080p and 1440p, while the Xbox Series X (52 CUs of RDNA 2) beats it at pure 4K rasterization. The Zen 4 CPU is architecturally newer and faster clock-for-clock than the Zen 2 in both consoles — for CPU-heavy workloads and emulation, it may actually outperform. For pure GPU bandwidth, it's behind the best consoles but ahead of the worst.

## What This Is Actually About

Here's where I want to push back on the price-focused criticism, which treats this like a PS5 competitor and stops there.

The Steam Machine is not primarily competing with the PS5. It's competing with the question: **"Should I buy a living-room gaming PC, and if so, what kind?"**

That question has always had a messy answer. Your options before today were:

1. Build your own mini-ITX PC — more powerful, more expensive, requires you to care about PC building, no living-room optimized software layer
2. Buy a gaming laptop and connect it to your TV — awkward, thermals are bad horizontally, a 15-inch screen attached to a TV is silly
3. Buy a Steam Deck and dock it — Valve's recommended path for years, but you're gaming at 800p-1080p on a handheld-class chip
4. Wait

The Steam Machine answers with: SteamOS, optimized for your couch, configured at the factory, no assembly required, your existing Steam library works immediately, and it runs on the same GPU driver stack as the Steam Deck so the compatibility story is nearly identical to the Deck's.

That last point matters more than the price. The Steam Deck has a [Verified game compatibility library](https://www.protondb.com/) that now covers tens of thousands of titles on Linux through Proton. Every game that runs on the Deck runs on the Steam Machine. The Deck shipped in February 2022 and has spent four years proving that SteamOS + Proton is a real platform, not a science project. The Steam Machine inherits that.

## The Open Platform Bet

The part of the Steam Machine announcement that should get more attention is buried in the [FAQ](https://store.steampowered.com/hardware/steammachine):

> "Thanks to the openness of the PC platform, there are lots of options for devices that will allow you to run games natively or streamed to your TV. [...] We are continuing to work toward enabling SteamOS to be used on more hardware than just ours. In fact, with the newly-released SteamOS 3.8, you can run the same code and operating system as Steam Machine on your own living-room PC using whatever PC parts you want."

This is the move that Sony and Microsoft literally cannot make. Their consoles run proprietary operating systems that exist specifically to lock you into their storefronts. Valve is saying: here's our reference hardware at $1,049, but if you want to build your own SteamOS machine, the OS is free and we're actively improving driver support for AMD GPUs (Intel and NVIDIA are in progress).

That's not a consolation prize for people who won't pay $1,049. It's a fundamentally different theory of platform ownership. Valve is betting that if SteamOS becomes the best couch-gaming OS — for Valve hardware or otherwise — the value accrues to the Steam store regardless of who sells the machine. The platform is the distribution network, not the box.

Sony and Nintendo cannot replicate this. Every Nintendo Switch 2 buyer is a locked-in customer. Every Steam Machine buyer (and every person who builds a SteamOS box themselves) is a Steam customer who was probably already a Steam customer — but now they're buying from Steam in their living room instead of Windows.

## Should You Buy One?

If you're a console gamer who owns nothing in the Steam ecosystem, the honest answer is probably not right now. The price premium over a PS5 is real, the console-exclusive library gap is real (Spider-Man, God of War, Forza Motorsport — none of those are on Steam), and SteamOS 3.8 is still a first generation of this particular form factor.

If you already own 100+ games on Steam and you've ever looked at your PC library and thought "I wish I could play these on the couch without the hassle," the Steam Machine is a serious answer. The frictionless living-room experience it offers — Big Picture Mode on boot, the Steam Controller's gyro and trackpads, Proton running your backlog — justifies the cost better than the specs alone would suggest.

If you're the type of person who reads SteamDB and can build a mini-ITX PC, wait a few months: SteamOS 3.8 on your own hardware is free, and prices will come down as the RAM shortage eases. Valve has explicitly blessed this path.

## The Long Game

The 2015 Steam Machines failed because Valve outsourced them to third-party manufacturers who produced a dozen incompatible configurations at wildly varying price points, none of them running SteamOS properly, most of them just running Ubuntu with a Steam shortcut. The promise was incoherent and the execution was worse.

This is different. Valve is building the hardware itself, the same way it built the Steam Deck. It's the reference implementation of SteamOS on desktop. The delays were real but the reason was supply chains, not engineering. The launch today is a real product, shipping to real customers, with real reviews from people who tested it (the [Gamers Nexus review](https://www.reddit.com/r/pcgaming/comments/1ucqfz2/valve_steam_machine_review_gpu_cpu_benchmarks/) benchmarks are up).

Is $1,049 too expensive? Yes, probably, for the market size Valve would want. But Valve is not Sony. It doesn't need 50 million units to make the business work — it needs a critical mass of living-room Linux gamers to justify keeping SteamOS at parity with the gaming market. The Steam Deck proved that audience exists. The Steam Machine is the next step for that audience. The price is a problem; it's not a death sentence.

The real question this launch answers is whether Valve is serious about the living room. After today, the answer is yes.

---

*Primary sources: [Steam Machine launch announcement](https://store.steampowered.com/news/group/45479024/view/685257114654870245), [Steam Machine hardware page](https://store.steampowered.com/hardware/steammachine), [IGN review](https://www.ign.com/articles/steam-machine-review), [Gamers Nexus benchmarks thread](https://www.reddit.com/r/pcgaming/comments/1ucqfz2/valve_steam_machine_review_gpu_cpu_benchmarks/), [spec comparison at TwiceTheBits](https://twicethebits.com/2025/11/12/how-does-the-new-steam-machine-stack-up-against-the-xbox-series-x-and-ps5/), [November 2025 announcement](https://store.steampowered.com/news/group/45479024/view/578276333072679812).*
