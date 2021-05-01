---
title: "NetBSD VM on bhyve (on TrueNAS)"
date: 2021-04-25T16:50:20Z
categories:
 - NetBSD
 - TrueNAS
---
My new NAS at home is running [TrueNAS Core]. So far, it has been excellent,
however I struggled a bit setting up a NetBSD VM on it. Part of the problem is
that a lot of the docs and how-tos I found are stale, and the information in it
no longer applies.

[TrueNAS Core] allows running VMs using **bhyve**, which is FreeBSD's hypervisor.
NetBSD is not an officially supported OS, at least according to the guest OS
chooser in the [TrueNAS] web UI :) But since the release of NetBSD 9 a while ago,
things have become far simpler than they used to be -- with one caveat (see
below).

# UEFI Boot

NetBSD 9 and newer fully support booting through UEFI. This simplifies things
because (as far as I understand) bhyve does not really support BIOS boot, it
prefers loading the kernel directly.

It used to be the case that it was hard to get the installer working, so people
started with an image of an already installed system, plus GRUB for bhyve. This
is all very clunky and, to be clear, **it is no longer needed**.

# Starting the Installer

Begin by downloading a CD image for the installer -- the regular NetBSD
9_STABLE/amd64 installation CD image from
https://nycdn.netbsd.org/pub/NetBSD-daily/netbsd-9/latest/amd64/installation/cdrom/
-- and storing it on the ZFS volume.

In the web UI, under "Virtual Machines", create a new one with the following
settings:

 - Guest OS: FreeBSD
 - Boot method: UEFI
 - VNC: enabled
 - "wait with boot until VNC connects": enabled
 - Use VirtIO for disks and network
 - When asked for an install CD, select the CD image downloaded earlier

# Fallout

When you boot now, you will find that VNC disconnects after about five seconds.
Further investigation shows that it's actually the bhyve hypervisor that exits
with a segfault.

This turns out to be an issue with the USB 3 (`xhci`) driver in the NetBSD
kernel -- or rather, it is probably a bhyve bug but disabling the `xhci` driver
on the guest side works around it. Disabling `xhci` is not a big deal because
the VM does not need native USB 3 anyway.

To work around:

1. Start the VM, then connect to VNC.
2. Once the bootloader appears, press (3) to go to the prompt. Enter `boot -c`.
3. In the userconf shell, enter `disable xhci*`, then `quit`.
4. The installer should appear, letting you install normally.
5. After installation, shut down the VM and remove the CD from the list of
   devices.
6. Start it again and repeat steps 2 and 3 to boot into the installed system.

To make the workaround permanent, edit the file `/boot.cfg` so that the first
line reads

```
menu=Boot normally:rndseed /var/db/entropy-file;userconf disable xhci*;boot
```

At this point, you should also uncheck the "wait for boot until VNC connects"
checkbox in the settings so the VM can start unattended in the future.

# Success!

Here is the obligatory `dmesg` output for this system:

```
Copyright (c) 1996, 1997, 1998, 1999, 2000, 2001, 2002, 2003, 2004, 2005,
    2006, 2007, 2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017,
    2018, 2019, 2020 The NetBSD Foundation, Inc.  All rights reserved.
Copyright (c) 1982, 1986, 1989, 1991, 1993
    The Regents of the University of California.  All rights reserved.

NetBSD 9.1_STABLE (GENERIC) #0: Thu Apr 22 10:08:46 UTC 2021
	mkrepro@mkrepro.NetBSD.org:/usr/src/sys/arch/amd64/compile/GENERIC
total memory = 8191 MB
avail memory = 7926 MB
cpu_rng: RDSEED
rnd: seeded with 256 bits
timecounter: Timecounters tick every 10.000 msec
Kernelized RAIDframe activated
running cgd selftest aes-xts-256 aes-xts-512 done
xhci* disabled
xhci* already disabled
timecounter: Timecounter "i8254" frequency 1193182 Hz quality 100
efi: systbl at pa bfb7cf18
  BHYVE (1.0)
mainbus0 (root)
ACPI: RSDP 0x00000000BFB88014 000024 (v02 BHYVE )
ACPI: XSDT 0x00000000BFB870E8 00004C (v01 BHYVE  BVFACP   00000001      01000013)
ACPI: FACP 0x00000000BFB86000 0000F4 (v04 BHYVE  BVFACP   00000001 BHYV 00000001)
ACPI: DSDT 0x00000000BEA98000 00191A (v02 BHYVE  BVDSDT   00000001 INTL 20200430)
ACPI: FACS 0x00000000BFB8C000 000040
ACPI: HPET 0x00000000BFB85000 000038 (v01 BHYVE  BVHPET   00000001 BHYV 00000001)
ACPI: APIC 0x00000000BFB84000 000062 (v01 BHYVE  BVMADT   00000001 BHYV 00000001)
ACPI: MCFG 0x00000000BFB83000 00003C (v01 BHYVE  BVMCFG   00000001 BHYV 00000001)
ACPI: SPCR 0x00000000BFB82000 000050 (v01 BHYVE  BVSPCR   00000001 BHYV 00000001)
ACPI: 1 ACPI AML tables successfully acquired and loaded
ioapic0 at mainbus0 apid 4: pa 0xfec00000, version 0x11, 32 pins
cpu0 at mainbus0 apid 0
cpu0: Intel(R) Pentium(R) Gold G5420 CPU @ 3.80GHz, id 0x906ea
cpu0: package 0, core 0, smt 0
cpu1 at mainbus0 apid 1
cpu1: Intel(R) Pentium(R) Gold G5420 CPU @ 3.80GHz, id 0x906ea
cpu1: package 0, core 0, smt 1
cpu2 at mainbus0 apid 2
cpu2: Intel(R) Pentium(R) Gold G5420 CPU @ 3.80GHz, id 0x906ea
cpu2: package 1, core 0, smt 0
cpu3 at mainbus0 apid 3
cpu3: Intel(R) Pentium(R) Gold G5420 CPU @ 3.80GHz, id 0x906ea
cpu3: package 1, core 0, smt 1
acpi0 at mainbus0: Intel ACPICA 20190405
acpi0: X/RSDT: OemId <BHYVE ,BVFACP  ,00000001>, AslId <    ,01000013>
acpi0: MCFG: segment 0, bus 0-255, address 0x00000000e0000000
acpi0: SCI interrupting at int 9
acpi0: fixed power button present
timecounter: Timecounter "ACPI-Safe" frequency 3579545 Hz quality 900
hpet0 at acpi0: high precision event timer (mem 0xfed00000-0xfed00400)
timecounter: Timecounter "hpet0" frequency 16777216 Hz quality 2000
pckbc1 at acpi0 (KBD, PNP0303) (kbd port): io 0x60,0x64 irq 1
pckbc2 at acpi0 (MOU, PNP0F03) (aux port): irq 12
SIO (PNP0C02) at acpi0 not configured
COM1 (PNP0501) at acpi0 not configured
COM2 (PNP0501) at acpi0 not configured
attimer1 at acpi0 (TIMR, PNP0100): io 0x40-0x43 irq 0
pckbd0 at pckbc1 (kbd slot)
pckbc1: using irq 1 for kbd slot
wskbd0 at pckbd0: console keyboard
pms0 at pckbc1 (aux slot)
pckbc1: using irq 12 for aux slot
wsmouse0 at pms0 mux 0
pci0 at mainbus0 bus 0: configuration mode 1
pci0: i/o space, memory space enabled, rd/line, rd/mult, wr/inv ok
pchb0 at pci0 dev 0 function 0: vendor 1275 product 1275 (rev. 0x00)
virtio0 at pci0 dev 3 function 0
virtio0: Virtio Block Device (rev. 0x00)
ld0 at virtio0: Features: 0x10000244<INDIRECT_DESC,FLUSH,BLK_SIZE,SEG_MAX>
virtio0: allocated 270336 byte for virtqueue 0 for I/O request, size 128
virtio0: using 262144 byte (16384 entries) indirect descriptors
virtio0: config interrupting at msix0 vec 0
virtio0: queues interrupting at msix0 vec 1
ld0: 10240 MB, 5201 cyl, 64 head, 63 sec, 512 bytes/sect x 20971520 sectors
virtio1 at pci0 dev 4 function 0
virtio1: Virtio Network Device (rev. 0x00)
vioif0 at virtio1: Features: 0x11010020<INDIRECT_DESC,NOTIFY_ON_EMPTY,STATUS,MAC>
vioif0: Ethernet address xx:xx:xx:xx:xx:xx
virtio1: allocated 32768 byte for virtqueue 0 for rx0, size 1024
virtio1: allocated 311296 byte for virtqueue 1 for tx0, size 1024
virtio1: using 278528 byte (17408 entries) indirect descriptors
virtio1: config interrupting at msix1 vec 0
virtio1: queues interrupting at msix1 vec 1
genfb0 at pci0 dev 29 function 0: vendor fb5d product 40fb (rev. 0x00)
genfb0: framebuffer at 0xc1000000, size 1024x768, depth 32, stride 4096
genfb0: shadow framebuffer enabled, size 3072 KB
wsdisplay0 at genfb0 kbdmux 1: console (default, vt100 emulation), using wskbd0
wsmux1: connecting to wsdisplay0
drm at genfb0 not configured
vendor 8086 product 1e31 (USB serial bus, xHCI) at pci0 dev 30 function 0 not configured
pcib0 at pci0 dev 31 function 0: vendor 8086 product 7000 (rev. 0x00)
isa0 at pcib0
com0 at isa0 port 0x3f8-0x3ff irq 4: ns16550a, working fifo
com1 at isa0 port 0x2f8-0x2ff irq 3: ns16550a, working fifo
timecounter: Timecounter "clockinterrupt" frequency 100 Hz quality 0
timecounter: Timecounter "TSC" frequency 3792882480 Hz quality 3000
ld0: GPT GUID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
dk0 at ld0: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", 262144 blocks at 64, type: msdos
dk1 at ld0: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", 16512960 blocks at 262208, type: ffs
dk2 at ld0: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", 4196319 blocks at 16775168, type: swap
IPsec: Initialized Security Association Processing.
boot device: ld0
root on dk1 dumps on dk2
root file system type: ffs
kern.module.path=/stand/amd64/9.1/modules
wsdisplay0: screen 1 added (default, vt100 emulation)
wsdisplay0: screen 2 added (default, vt100 emulation)
wsdisplay0: screen 3 added (default, vt100 emulation)
wsdisplay0: screen 4 added (default, vt100 emulation)
```

[TrueNAS Core]: https://truenas.com/truenas-core/
[TrueNAS]: https://truenas.com/
