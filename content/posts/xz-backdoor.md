---
title: "The XZ Backdoor"
date: 2024-03-31T20:32:45+02:00
tags:
 - opensource
---
Over the Easter weekend 2024, there was a big kerfuffle around a compression
tool named `xz`. Honestly, the story is so amazing that it could be a gripping
novel. In fact, what happened is not dissimilar to the book [$ git commit
murder](https://mwl.io/fiction/crime) My Michael Warren Lucas.

A PostgreSQL developer, Andres Freund, was running some benchmarks and trying
to reduce the noise from other programs on the system. While doing so, he
noticed that `sshd` would take more CPU than expected during logins, for about
0.5 seconds at a time.

> *Side note:* The reason that this came up at all is that any system exposed to
the internet gets regular failed login attempts via ssh, from potential
attackers and security scanners of some sort.

So Andres starts looking into where the CPU time is spent and discovers that
most of it is in a part of `liblzma` (the compression format library) and not
covered by a symbol -- i.e. without an indication in which function it is.
This is very suspect. Digging further, it turns out that `sshd` is backdoored
through the installation of `xz` version 5.6.0 or 5.6.1!

At this point, I highly recommend heading over to
https://www.openwall.com/lists/oss-security/2024/03/29/4 
and reading Andres' original investigation posting. Below, I want to resume
some more salient points around this story.

## Why does OpenSSH even depend on xz?

Normally, it doesn't, however several Linux distributions patch `sshd` to
integrate systemd notifications. The systemd library to do that also happens
to link against `liblzma` from the xz project. So perhaps the folks
complaining about how systemd is so pervasive in modern Linux systems do have
a point.

## Who wrote the backdoor?

The malicious code has *not* been written by the original author and
maintainer of xz, Lasse Collin. In fact,
[he appears to be the victim here](https://tukaani.org/xz-backdoor/).

The backdoor was inserted by a second xz maintainer named Jia Tan, who joined
the project several years ago. The circumstances of him joining are a story
that happens all over open source projects:

At some point, the main development of xz was more or less done. As
[lcamtuf argued](https://lcamtuf.substack.com/p/technologist-vs-spy-the-xz-backdoor),
ongoing maintenance of a stable project "is as interesting as watching paint
dry". So I imagine Lasse struggling with motivation, then someone coming along
and offering to become the co-maintainer. They show up with some patches ready
to merge. At the same time, several people put pressure on Lasse, saying that
"patches sit forever in the queue", that they are doing a bad job and that
this person should just be admitted.

Note that this was *years* before this backdoor.

They then do the following:

* Jia Tan creates https://github.com/tukaani-project containing Lasse and
  themselves. The original xz code was hosted off GitHub on git.tukaani.org.
* The homepage moves from Lasse's server to a subdomain hosted on GitHub
  Pages, so that Jia Tan has full control over what's on the page.
* A workaround for a Valgrind failure is added to various distributions.
  Apparently, the Valgrind failure is caused by code added to later support
  the backdoor.
* Jia Tan adds plenty of test files (binary archives) with innocent-sounding
  names to the repo. Curiously, some of them are not actually used in tests.
* Jia Tan creates the official release tarballs on their machine.

The last two points are important for how the backdoor is hidden.

xz versions 5.6.0 and 5.6.1, both containing the backdoor, are released in
late March 2024, apparently while Lasse Collin is on vacation.

## How is the backdoor hidden?

The payload is effectively contained in some of the binary test archives added
to the repository before. But the way they are enabled is amazingly subtle.

In open source projects using Autotools, it is common to not check in
generated files (like the `configure` script) to the repository. After all,
they can be generated at any time, right? So what happens is that the
maintainer of the code (the perpetrator in this case) checks out the source at
a certain tag, *runs autoreconf and creates the release archive from the
result* (e.g. using `make dist`, which is part of automake). So whatever
changes they do to their local copies of autoconf and/or its macros is not
checked in to source control! If you look into the git repo, the code enabling
the backdoor is not there at all!

## What does the backdoor do?

On a Linux x86 system (and only on Linux x86), it hooks up into the signature
verification routines used when checking the credentials of someone trying to
log in via ssh. The exact way of working is still not fully understood as of
the time of this article. According to [preliminary analysis by Filippo
Valsorda](https://bsky.app/profile/did:plc:x2nsupeeo52oznrmplwapppl/post/3kowjkx2njy2b),
the backdoor becomes active when the login attempt contains a certain *client
key* (I was not even aware that a client key is sent in ssh logins) and runs
some code by calling `system`. So it appears to be Remote Code Execution, a
Pre-Auth Bypass, or both.

In this context, it is probably not a coincidence that [some folks saw a much
larger number of failed ssh login attempts](https://mastodon.sdf.org/@ParadeGrotesque/112184636692613817)
over the internet:

> xz 5.6.0 was released 2024-02-24, around one month ago, and it contained a backdoor.
>
> [...]
>
> There has been an incredible amount of SSH attacks on my server since early 2024. To give you an idea:
> 
> root@udon:~# grep -ic 2024 /etc/hosts.deny  
> 5406
> 
> root@udon:~# grep -ic 2023 /etc/hosts.deny  
> 763

## Who is Jia Tan?

In short, we don't know. It is probably a fake identity, just like the sock
puppet accounts that were used to pressure Lasse to admit Jia into the
project.

The back story is that they are Chinese and living in California. There is
some speculation that they were legitimate and a second person took over their
identity. But this is not confirmed, and I think it is unlikely.

Jia Tan's GitHub account is still active, though the xz-related repos have
been blocked. This does not make it easier to follow along the technical
write-ups, or to check out the source for ourselves.

Jia also made 9 commits into private repositories in the last month. I wonder
what those are?

In the meantime, Lasse found at least one other
[malicious commit](https://git.tukaani.org/?p=xz.git;a=commitdiff;h=f9cf4c05edd14dedfe63833f8ccbe41b55823b00)
in the xz source, disabling the use of Landlock sandboxing on Linux. Look at
that diff and think about whether you would have spotted this in code review!

Jia also made suspicious commits to other projects, such as
[libarchive](https://github.com/libarchive/libarchive/pull/1609). Again, some
of this suspicion may be overblown, but *we don't know*.

## Would SBOM have prevented this?

Haha lol no.

These were normal-looking releases, uploaded and signed by the regular release
manager. If anything, it is helpful to be aware that a larger product contains
a version of xz with the backdoor.

## What are the consequences?

**This could be a watershed moment for open source development.** Open source
development is currently a high-trust environment, and I fear that suspicion
about this case is going to color all sorts of future interactions.

The thing is, all the interactions that Jia Tan had look absolutely legit and
standard for open source. It is completely normal that someone who you never
met shows up and offers some help and patches. *Maybe* you meet them years
later at FOSDEM, or something. Maybe you never do.

For instance, in NetBSD, there are dozens of people who contribute under
pseudonyms at this very moment. To become a developer, we require that another
developer meet you in person and sign your PGP key. Maybe that's a good idea
after all.

But for instance, multiple times, I have shown up in some other open source
project with a fully formed patch, saying "Here is some code to support my OS
or my architecture. You probably cannot verify it as you don't have this
machine. Please apply it, I promise it's fine." Until now, I assumed that they
would do just that, and was annoyed when they didn't. But now? They don't know
if I am trying to insert a backdoor.

In the end, this is probably going to introduce more friction and more
suspicion of contributors' motivations into the distributed, open development
process. This is bad news for all users of Free software. I would hate it if
this destroys the trust-first (you might say naive) culture in open source
development.

But let me also offer two more hopeful takes.

* Diversity works. The backdoor targets the largest majority platform for
  servers: Linux, with systemd, on x86. Running NetBSD or FreeBSD, or perhaps
  another architecture such as ARM, prevents the attack from working. So by
  virtue of being on a less common platform, you can lower your risk. This is
  a basic economic argument: just like other closed source vendors (hah!),
  exploit writers primarily target the majority.
* Finally, the open source community did not actually do too bad in this!
  Sure, it was found by a stroke of pure luck. But the malicious code had been
  in the wild for only about a month. And it had not made it into long-term
  support releases of major distributions, where it would have made it into a
  ton of server systems. Compared to the lead time for this attach of 3-4
  years, this ended up not being a very successful operation by whichever
  organization is behind this. So in sum, we got away okay this time.
