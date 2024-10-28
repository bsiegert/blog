---
title: "Emulating *BSD on ARM, Part 1: Introduction"
date: 2024-10-28T20:30:07+02:00
---

In my copious spare time, I maintain the Go CI system for certain platforms. These days, Go uses LUCI, the same CI pipeline that Chromium is using.

My current rabbit hole is making the "swarming bot" work on NetBSD/arm -- that's 32-bit ARM, not aarch64. When building Go code for 32-bit ARM, the `GOARM` environment variable can be set to the instruction set version, i.e. `GOARM=6` (ARMv6, the default) and `GOARM=7` (ARMv7). One of the things that ARMv7 has that v6 doesn't is atomic synchronisation instructions. At least on BSD systems, Go needs those for multi-core systems (which is more or less *all systems* these days). Thus, ARMv6 Go binaries exit on startup if run on a system with multiple cores.

The Chromium CI system used to only support ARMv6 -- they call it armv6l, for little-endian -- not ARMv7. This meant that the whole thing was pretty much broken on BSD on ARM.

So I added code for `netbsd-armv7l` in the LUCI swarming bot. Again, that's the LUCI platform name, Go calls this `netbsd-arm`. The feedback I got was basically "Great, now can you also do FreeBSD and OpenBSD?" 

Time to install some VMs to find out what they use as `uname`, or rather what Python's `platform` module returns for them. It turns out the three OSes are wildly inconsistent in this!

As a spoiler, here is what I found out:

| OS            | `platform.machine()` | `platform.processor()` |
|---------------|----------------------|------------------------|
| FreeBSD/armv7 | arm                  | armv7                  |
| OpenBSD/armv7 | armv7                | arm                    |
| NetBSD/evbarm | evbarm               | earmv7hf               |

In the next posts, I will detail how I got these platforms to run in qemu.
