# WSL2 Finally Fixed the Last Big Cross-OS File Performance Bottleneck. Here's What They Actually Changed.

**Published: 2026-06-06**

If you've been using WSL2 for development with project files on your Windows drive — `C:\Users\yourname\projects` mapped to `/mnt/c/...` — you've been living with a known performance penalty for years. Microsoft's own documentation lists "performance across OS file systems" as a checkmark in the WSL1 column, not the WSL2 column. The workaround has always been "keep your files in the Linux filesystem, not Windows."

A change merged on May 27 removes one of the last major contention points on the virtiofs path. And if you understand *why* it was slow, the fix is actually elegant.

---

## The History You Need to Follow This

WSL2 is, at its core, a lightweight virtual machine running on Hyper-V. The Linux environment you type commands into is running inside a VM. When you access a file on your Windows drive from inside WSL2, that file access has to cross the VM boundary — from the Linux kernel inside the VM to the Windows filesystem on the host, and back.

Getting that cross-VM file access fast has been a multi-year project with three distinct generations:

**WSL1's DrvFs** was fast because there was no VM. WSL1 ran a Linux kernel compatibility layer directly on Windows, which meant `/mnt/c` was just Windows NTFS with a translation layer. Fast, but you didn't have a real Linux kernel, so system call compatibility was incomplete.

**WSL2's 9P (Plan 9 filesystem protocol)** was the first approach to cross-VM file access. A 9P server runs on the Windows host, a 9P client runs in the Linux VM, and file operations become network-protocol messages. This is still the default today. It works, but serialization overhead is significant for workloads that do many small file operations — which is exactly what `npm install`, `git` operations, and most build systems do.

**Virtiofs** arrived around 2021 as an opt-in alternative. It uses the VirtIO transport (a shared-memory fast path that's also used for virtio-net, virtio-blk, and other paravirtualized devices in VMs). Shared memory means you skip the serialization-deserialization round trip that 9P requires. It's substantially faster for I/O-heavy workloads.

To enable it, you add `virtiofs=true` to the `[wsl2]` section of your `.wslconfig`. You still have to opt in today — it's not the default yet.

---

## What Was Still Slow, and Why

Even with virtiofs enabled, there was a remaining bottleneck at the DMA layer.

Virtual machines on Hyper-V perform DMA (direct memory access) for I/O operations through *bounce buffers*: a reserved memory region below the 4 GB physical address boundary, which hardware can address directly. The Linux kernel calls this the SWIOTLB pool ("software I/O translation lookaside buffer").

The problem: until last month, all virtio devices in a WSL2 session shared a *single global SWIOTLB pool*. That means your virtiofs mount for `/mnt/c`, your virtiofs mount for `/mnt/d`, and your virtio network adapter were all queuing into the same shared buffer. Under heavy I/O — doing a `git clone` across `/mnt/c` while also doing network activity — these devices were competing for buffer space. You'd hit contention, and performance would degrade.

[PR #40654](https://github.com/microsoft/WSL/pull/40654), authored by Ben Hillis and merged May 27, 2026, gives each virtio device its own dedicated DMA pool.

The implementation is clean: the Linux kernel allocates a contiguous physical range below 4 GB at boot time, publishes its address through sysfs (`/sys/bus/vmbus/drivers/hv_pci/swiotlb_base` and `swiotlb_size`), and the WSL service reads those values and injects a per-device `swiotlb=` option into each virtio device at creation time. Your `/mnt/c` mount has its own pool. Your `/mnt/d` mount has its own pool. The network adapter has its own pool. No more contention at the DMA layer.

This requires kernel `Microsoft.WSL.Kernel 6.18.26.3-1`, shipping with WSL 2 DeviceHost `1.2.29-0`. If you're on an older kernel, WSL will tell you directly: *"The running kernel is missing a patch that significantly improves virtio device performance. Update to a more recent WSL kernel to enable this optimization."*

---

## The Bigger Improvement Arc

This fix is part of a multi-year series of incremental improvements on the virtiofs path:

- **PR #40298**: device reuse improvements (reducing overhead of opening and closing devices)
- **PR #40426**: shared mmap support without DAX (direct access), which allows memory-mapped file access through virtiofs without requiring a specific hardware feature
- **PR #40654** (the May 27 fix): per-device SWIOTLB pools, removing DMA contention

Each of these narrows the performance gap between WSL2 cross-OS file access and native Linux filesystem performance. The gap started at "nearly unusable for heavy builds" and is now at "meaningfully slower than native ext4 but acceptable for most workloads."

The context for how far this has come: when virtiofs was introduced, cross-OS file access in WSL2 was documented as notably slower than WSL1 for Windows-side files. For a few years, the practical advice was "if you need to edit files in Windows and build in Linux, WSL1 is better." Virtiofs closed most of that gap. This patch closes more of it.

---

## What You Should Actually Do

**If you're on the current WSL2 kernel** (check with `uname -r` inside WSL; you want `6.18.26.3` or later), you can enable virtiofs in `~/.wslconfig` (or `%UserProfile%/.wslconfig` from Windows) by adding:

```ini
[wsl2]
virtiofs=true
```

Then restart WSL with `wsl --shutdown` and relaunch. Virtiofs will be used for all Windows filesystem mounts.

**If you're not on the current kernel**, run `wsl --update` from a Windows command prompt to pull the latest kernel. Check for the update message; if WSL tells you about the missing performance patch, that's the signal to update.

**Should you move your files into the Linux filesystem anyway?** Honestly, yes, for maximum performance. If your workflow is primarily Linux tools (Node, Python, Rust, Go builds), keeping your project in the Linux ext4 filesystem and accessing it from Windows via `\\wsl.localhost\...` is still faster than virtiofs. But many developers have Windows-side constraints — their IDE needs the files in a Windows path, they share files between WSL distros, they use Windows-native design tools. For those workflows, virtiofs with per-device SWIOTLB is the right path, and this patch makes it meaningfully better.

---

## Why This Matters More Than It Used to

There's a larger reason this particular performance improvement is more significant in 2026 than it would have been three years ago: AI coding tools.

Claude Code, Cursor, and most other agentic coding assistants run their filesystem operations at machine speed, not human speed. When an agent is doing a codebase analysis — walking directory trees, reading source files, checking git history — it can issue thousands of filesystem operations in the time it takes a human to read a single file. The latency of each individual operation matters much less when a human is doing one `cat` per minute. It matters a lot when an agent is doing ten thousand of them in a session.

If you're running AI agents over a Windows-side codebase inside WSL2, the virtiofs improvement with per-device SWIOTLB pools compounds across those thousands of operations. The fix that seemed incremental for interactive development becomes more meaningful for programmatic, high-frequency access patterns.

The WSL team is doing the work in public at [github.com/microsoft/WSL](https://github.com/microsoft/WSL). The default is still 9P over a Hyper-V socket. Virtiofs is still opt-in. But the direction is clear: the gap keeps narrowing.

---

**Sources:** [Hayden Barnes: WSL 2 is getting faster Windows file system access](https://www.boxofcables.dev/wsl2-per-device-swiotlb-pools-for-virtiofs-and-virtioproxy/) · [WSL PR #40654](https://github.com/microsoft/WSL/pull/40654) · [Microsoft: Comparing WSL 1 and WSL 2](https://learn.microsoft.com/en-us/windows/wsl/compare-versions)
