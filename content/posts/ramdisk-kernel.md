---
title: "Building a NetBSD ramdisk kernel"
date: 2023-11-26T15:08:37+01:00
categories:
 - NetBSD
---

When I used OpenBSD, I was a big fan of `bsd.rd`: a kernel that includes a root
file system with an installer and a few tools. When I invariably did something
bad to my root file system, I could use that to repair things. `bsd.rd` is
also helpful for OS updates. And there is only a single file involved.

On NetBSD however, there is usually no `netbsd.rd` kernel installed, or even
available by default. The facility is there, it's just not standard. To be
fair, there are a number of architectures that use kernels with a ramdisk for
installation.

Recently, I have been toying with NetBSD on an Orange Pi 5. This is a 64-bit
ARM board, using the evbarm-aarch64 architecture. I am booting from an SD card
(details in a followup post) but once booted, the kernel does not see the card
any more, only the NVMe SSD. So my thoughts went back to `bsd.rd` and I
decided that I want one!

It turns out that there are two ways of getting a kernel with a ramdisk:

1.  Building the ramdisk image into the kernel itself, `bsd.rd`-style. This is
    called an "instkernel" in NetBSD terminology.
2.  A loadable kernel module, `miniroot.kmod`. The `modules.tar.xz` set
    contains an "empty" miniroot module, to which you can apparently add your
    own image.

I was unable to make #2 work, so I will only be talking about #1 here.

# How to create a NetBSD ramdisk kernel

For the ramdisk image, you can use a pre-built "daily" image, which is available at
http://nycdn.netbsd.org/pub/NetBSD-daily/HEAD/latest/evbarm-aarch64/installation/ramdisk/
as `ramdisk.fs`. Its size is 3072 KiB. Keep that number in mind for later.

For the next steps, you need a NetBSD source tree. The default location is
`/usr/src` but any other location works just as well.

The Orange Pi 5 uses the GENERIC64 kernel configuration but there is no
"install" variant. So you need to add a new configuration file,
`sys/arch/evbarm/conf/GENERIC64_INSTALL`, with these contents:

```
include "arch/evbarm/conf/GENERIC64"

no options MEMORY_DISK_DYNAMIC
options MEMORY_DISK_IS_ROOT
options MEMORY_DISK_RBFLAGS=RB_SINGLE
options MEMORY_DISK_ROOT_SIZE=6144
```

This disables the dynamic ramdisk size and use a fixed size of 6144 sectors x
512 bytes (= 3072 KiB) instead. It also sets the ramdisk as the root
filesystem and defaults to single-user mode.

Now let's build the kernel. I did this on a Mac but any OS would do:

```shell
$ cd /usr/src
$ ./build.sh -O /Volumes/obj/ -m evbarm64 -N1 -j 7 -U tools
$ ./build.sh -O /Volumes/obj/ -m evbarm64 -N1 -j 7 -U -u kernel=GENERIC64_INSTALL
```

Side note: the `obj` directory needs to be on a case-sensitive filesystem. On
a Mac, you can create a case-sensitive APFS dataset named `obj`, which is
mounted under `/Volumes/obj`.

Now the magic bit, adding the image into the kernel:

```
$ /Volumes/obj/tools/mdsetimage/mdsetimage -v /Volumes/obj/sys/arch/evbarm/compile/GENERIC64_INSTALL/netbsd ~/Downloads/ramdisk.fs
mapped /Volumes/obj/sys/arch/evbarm/compile/GENERIC64_INSTALL/netbsd
got symbols from /Volumes/obj/sys/arch/evbarm/compile/GENERIC64_INSTALL/netbsd
root @ 0xc6e328/3145728
copying image /Users/bsiegert/Downloads/ramdisk.fs into /Volumes/obj/sys/arch/evbarm/compile/GENERIC64_INSTALL/netbsd (3145728 bytes)
done copying image
exiting
```

Now you can copy the `sys/arch/evbarm/compile/GENERIC64_INSTALL/netbsd` file
to the SD card and boot from it. This boots into the NetBSD installer, which
also has a (limited) shell available.

In my case, I was able to install the system to the SSD. Success! 🎉
