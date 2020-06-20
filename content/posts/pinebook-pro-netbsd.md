---
title: "Getting Started with NetBSD on the Pinebook Pro"
date: 2020-06-20T18:09:23+02:00
categories:
 - NetBSD
 - PinebookPro
---

If you buy a Pinebook Pro now, it comes with Manjaro Linux on the internal eMMC storage. Let's install NetBSD instead!

The easiest way to get started is to buy a decent micro-SD card (what sort of markings it should have is a science of its own, by the way) and install NetBSD on that. On a warm boot (i.e. when rebooting a running system), the micro-SD card has priority compared to the eMMC, so the system will boot from there.

As for which version to run, there is a conundrum:

 - There are binary packages but only for NetBSD-9. On -current, you have to compile everything yourself, which takes a long time.
 - Hardware support is better in NetBSD-current.
 
The solution is to run a userland from NetBSD-9 with a NetBSD-current kernel.

As the Pinebook Pro is a fully 64-bit capable machine, we are going to run the `evbarm-aarch64` NetBSD port on it. Head over to **https://armbsd.org/arm/** (thanks, Jared McNeill!) and grab a NetBSD 9 image for the Pinebook Pro. Then (assuming you are under Linux), extract it onto the memory card with the following command:

```sh
zcat netbsd-9.img.gz | dd of=/dev/mmcblk2 bs=1m status=progress
```

Be sure to check that `mmcblk2` is the correct device, e.g. by examining `dmesg` output! Once the command is done, you can reboot. Once booting is finished, you can log in as root with no password. The first thing you should do is to set one, using `passwd`.

# To the eMMC!

Would you like to replace the pre-installed Manjaro Linux on the eMMC?

It makes sense to have your main OS on the built-in storage, since it is quite a bit faster than the typical micro-SD card. In my tests, I get write speeds of about 70 MiB per second on the eMMC.

By the way, if you want more and even faster Storage, PINE64 will sell you an adapter board for adding an NVMe drive (a fast SSD).

Once you have booted NetBSD from the memory card, mount the Linux volume and copy over the image file from before, then unmount and extract it in exactly the same way as above. The only difference is that the target device is called `/dev/rld0d`. Shut down the system, remove the memory card, switch it back on and watch NetBSD come up :)

# Getting a -current Kernel

To have better driver support, I recommend installing a NetBSD-current kernel. To do that, you just need to replace the `/netbsd` file with the new kernel -- no changes to the bootloader are required.

You can find a pre-built kernel under https://nycdn.netbsd.org/pub/NetBSD-daily/HEAD/latest/evbarm-aarch64/binary/kernel. Download the file and install it:

```sh
cp /netbsd /netbsd.old
gunzip netbsd-GENERIC64.gz
install -o root -g wheel -m 555 netbsd-GENERIC64 /netbsd
```

You will find that there is now a driver for the built-in Broadcom Wi-Fi (as the `bwfm0` interface) but the firmware is missing. To fix this, download the `base.tgz` set from [the same location] and extract the firmware blobs only (as root):

```sh
cd /
tar xvpfz /path/to/base.tgz libdata/firmware
```

[the same location]: http://nycdn.netbsd.org/pub/NetBSD-daily/HEAD/latest/evbarm-aarch64/binary/sets/

In my experience however, the Broadcom Wi-Fi driver is extremely likely to make the system crash or hang. I tend to rely mostly on an old Apple Ethernet-USB adapter (an `axe` interface).

**NOTE:** I have since back-pedaled and returned to NetBSD 9. Other than the unstable Wi-Fi, I also had crashes when running `npm install` and other issues.
