---
title: Windows 10 April Update, unbootable system
date: 2018-05-09 13:49
draft: true
---

A few days ago, I installed the Windows 10 “April update”, and it broke my GRUB installation. What happened?

My primary disk has an MBR partition table. (Apparently, booting from GPT requires using UEFI, which exposes a whole new exciting set of firmware bugs.) GRUB was installed in a small ext2 partition (primary partition #3), while primary partitions #1 and #2 were used by Windows 10. Installing the April update created another primary partition and *moved my ext2 partition to slot #4* so grub can no longer find its files.

## Rescuing with grub

When this happens, you are greeted with a nice “rescue shell” mode.

```
grub rescue>
```

Now what?  Unfortunately, contrary to GRUB 1, GRUB 2 cannot do much without loading additional modules. You cannot even chainload Windows from a PBR because the `chainloader` command needs the `chainload` module to be loaded.

You can list partitions and directories with the `ls` command. The `set` command shows the variables that have been set; there is a variable named `prefix` that contains the path to the grub files. So after finding out that `/boot/grub` is now on `(hd0,msdos4)` instead of `(hd0,msdos3)` as before, you can re-set the prefix, load the `normal` module and execute the `normal` command to do the, well, normal boot:

```
set prefix=(hd0,msdos4)/boot/grub
insmod normal
normal
```

Unfortunately, there is no way (that I could find) to make the new prefix permanent, other than running `grub-install` from a running system. That is, you would be doing this dance on every boot from now on. Also unfortunately,

- the version of [grub2](http://pkgsrc.se/sysutils/grub2) in pkgsrc is kind of old and
- the grub2 package [has not built](https://bulktracker.appspot.com/pkgresults/sysutils/grub2), as far as I can tell, for anyone in the last two years.

As a result, I would need to boot a [grml.org](https://grml.org/) rescue system and reinstall grub from there.

# Working around

There is an easier way to make GRUB find its files again. We just need to make sure the partition table matches what it expects.

A partition table is like an array of pointers (start and length). So you can delete two partitions and recreate them with exactly the same (start, length) tuples but different partition numbers, and things will just work. So I did exactly that, using `fdisk -u ld0` on NetBSD. It helps to do this on X, where you can open two terminal windows side by side: one shows the previous partition table, one the interactive `fdisk` session, and you can copy values around with the mouse. `tmux` (in the NetBSD base system) on a text screen would also work.

So in my case, I swapped partitions #3 and #4, and GRUB worked again!

And one day later, Windows decided to apply the May update to the April update -.-

# Addendum: What new partition!?

Honestly, I am not sure what the new partition is actually doing. Even though I want a single Windows partition, there are now three of them:

1. an NTFS partition of about 100 MB, 
2. my actual C drive,
3. A partition of type 0x27 of about 450 MB. Googling shows that this might be MirOS BSD (which I doubt) or a hidden recovery partition of some sort.

I am not sure what is going on here. I would appreciate hints from readers as to why these partitions are there.

