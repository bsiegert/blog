---
title: "Fedora Update: btrfs self-destruct"
date: 2024-05-07T22:21:21+02:00
categories:
 - Linux
---

[A while ago](/posts/fedora-asahi), I installed Fedora Asahi Remix on my M2
MacBook Air, and I was very positive about it. So positive, in fact, that I
ended up making it the default partition in the bootloader. I haven't used
macOS in weeks. But then, a few days ago, something weird happened:

In the middle of some development work, running `cvs update` on the [pkgsrc]
repository, the screen suddenly filled with a bunch of "read-only file system"
errors. It turned out that both `/` and `/home` had remounted themselves
read-only, without my intervention.

Running `journalctl --system` showed some warning messages in an alarming
orange-red: btrfs, the file system that the system was running from, had found
inconsistencies and had chosen to remount itself read-only. Now what?

I tried running `btrfsck /dev/nvme0n1p6`. Now, by default, btrfsck will only
check, not repair. But even the check aborted with an error about "errors
found in FS roots".

From my BSD experience, I thought booting into single-user
mode and running fsck with a r/o root file system would do the trick. A quick
`init S` later ... it turns out that you can only get a shell in single-user
mode if you have a root password set. By default, logins as root are
forbidden, even in single-user mode. But good luck setting a password while
`/` is read-only!

After several reboots, I got the system to work r/w for a while, so I could
set a root password. So within single-user mode, I ran

```
# btrfsck --repair --force /dev/nvme0n1p6
```

Decent idea, but like the check before, the repair option *also* aborted with
an error, saying "parent transid verify failed". According to some random
search results, this means that the file system is likely damaged beyond
repair. Or, as [@ParadeGrotesque] aptly remarked on Mastodon:

> It's dead, Jim.
>
> Don't blame me, I don't make the rules.

So in the end, I managed to back up my home directory with Restic (good thing
I had restic installed before!), then wiped the entire installation and
re-installed. Bummer.

[pkgsrc]: https://pkgsrc.org/
[@ParadeGrotesque]: https://mastodon.sdf.org/@ParadeGrotesque/112378155926039166

## More on btrfs

Why is btrfs the default file system in the first place? The Fedora Asahi
Remix installer doesn't even let you choose the file system type! I wish it
did!

The way I understand btrfs is that the developers are aiming for a feature set
on par with ZFS, except fully under the GPL and more "Linux-native" -- ZFS was
originally developed for Solaris, after all, and its licensing situation is at
least a bit dodgy.

btrfs has long had a reputation of playing fast and loose with your data. In
the early days, I heard several stories of spectacularly losing data. People
have assured me that those days are over, and that btrfs is a reliable system
these days.

Honestly, this experience has shown me that btrfs, even today, is anything but
reliable. The corruption I witnessed is surely not due to broken hardware. The
hardware is absolutely fine.

I suspect that the root cause is probably several unclean shutdowns. Asahi
Linux does have an unfortunate bug where it will consume too much battery when
in standby, so I ran out of battery several times. And, to be fair, finding
the corruption and going read-only is sensible. But what I find absolutely
infuriating is the utter inability of its fsck to actually repair anything.
The fs roots are damaged, so what? There are incorrect transids, so what?
Telling me my FS is fucked and I need to recreate it from scratch is not
useful!

As it is, what happened with btrfs made me recall the previous low point of
Linux file systems: years ago, I ran a file server with ReiserFS in
production. It corrupted itself to such a degree that `reiserfsck` kept
segfaulting. We lost so much user data. Good times.
