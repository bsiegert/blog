---
title: "pkgsrc and a Call for Action"
date: 2023-02-03T09:54:31Z
categories:
 - pkgsrc
 - NetBSD
---
I have been a pkgsrc developer for several years. For what it's worth, I think
pkgsrc is wonderful: a large selection of third-party software, packaged so
that it is easy to install with a single command -- either building everything
from source, or relying on binary packages. pkgsrc supports dozens of OSes --
not just NetBSD but also other BSDs, macOS, Linux, Illumos and more.

On the other hand, unfortunately, pkgsrc and NetBSD in general are suffering
from what I would call a *loss of mindshare*. When I joined the NetBSD
Foundation as a developer, I had the impression that NetBSD had more users and
community than OpenBSD or, say, Dragonfly. In recent years however, it seems
to me that people have more or less forgotten about NetBSD and pkgsrc.

## Here is where you may come in

Here is a simple idea that occurred to me a while ago and that I finally am
getting around to writing up, now that I am sitting in the train to
[FOSDEM 2023](https://fosdem.org/):

These days, a lot of upstream software has a manual with a section on
installation that does not give instructions for actually building it. (It
seems like building from source is so 1995, or something.) Instead, it goes
roughly like this:

> If you are using a Mac or a Linux/x86_64 machine, see our own binary
> packages here. Or use distro packages:
>
> For Debian, run `apt-get install somepackage`.
>
> For Fedora Linux, run some `yum` command.
>
> etc. etc.

There is typically a long list of install commands for a bunch of Linux
distributions and OSes. **pkgsrc is almost never mentioned.**

So if you are looking for a simple way of helping out pkgsrc and NetBSD, look
for the README of your favorite Open Source tools and add a section for
installing from pkgsrc. Typically, for a package from pkgsrc itself, the
simplest is to add instructions for using pkgin:

```
$ pkgin install somepackage
```

For packages in [pkgsrc-wip](https://wip.pkgsrc.org/), you could first ask on
the mailing list for an import so that there may be binary packages in the
future :) Then, give the typical instructions for installing from source:

```
$ cd /usr/pkgsrc/wip/somepackage
$ make package-install
```

I have created a few pull requests for such changes in the past, but I think
we need a lot more of that!
