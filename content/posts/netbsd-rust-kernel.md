---
title: "Rust in the Kernel, and other odd decisions"
date: 2026-01-30T12:19:28+01:00
draft: true
categories:
 - NetBSD
---
My email inbox is like the pile of documents on my desk. Things that I wanted to get back to ends up moving towards the bottom, into the never-ending pile of ... stuff. For the first time in a while, I have looked at the bottom -- and found an inquiry from someone who had seen my presentation at FOSDEM 2024.

They had a question for me, which I am going to paraphrase below. I am going to reproduce my answer here because it may be interesting for others.

> FreeBSD is apparently considering incorporating Rust code into the kernel, in order to appeal to a younger developer audience. Will NetBSD do similarly odd decisions in the future, or can I trust them to remain conservative in this regard?

First of all: I'm not so sure that Rust in the kernel would have been chosen to cater to a younger developer audience. It's probably more on merits such as memory-safety. Rust has a number of fans who are power users. I would be interested to learn why you think this is an odd decision.

NetBSD already has taken a much odder decision in the same space, by adding Lua into the kernel! This was also much discussed at the time. I have not used it, but apparently Lua is useful for rapid development and prototyping of kernel drivers.

Note that in general, NetBSD *is* more conservative in its technical decisions than FreeBSD or Linux. Rust in the core of NetBSD is probably a non-starter for multiple reasons:

- There are many architectures that NetBSD supports where Rust is not available. This is probably the most important argument against Rust.

- Today, even getting a Rust compiler running in the first place is hard.
Keeping Rust working (in pkgsrc) is quite a bit of work, resting on the shoulders of a few developers. 

- In general, the bootstrap relies on a binary package of the previous version. This is unacceptable for an otherwise source-only, self-contained distribution like the NetBSD sources. 

- For Rust to be used in base or in the kernel, the compiler would also have to be part of the base system. This means adding a lot of code and maintenance. It would not be impossible though -- we already have LLVM (a major dependency) in base. But again, the binary bootstrap kits are a significant hurdle.

- Finally, the release cycles of Rust are not compatible with the NetBSD ones. We support the last two major releases. Today, that's NetBSD 9 and 10. NetBSD 9.0 was released in 2020. Rust 1.41 was the current version back then. If Rust 1.41 had been part of NetBSD 9, it would be useless for anything except compiling NetBSD itself -- Rust 1.41 is so old that basically no modern code would compile with it. Not great.

