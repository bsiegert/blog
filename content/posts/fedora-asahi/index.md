---
title: "Fedora Asahi Remix"
date: 2024-02-03T10:21:03+01:00
categories:
 - Linux
---

I have been following Asahi Linux for a while. Linux for my MacBook Air M2 -- sure, why not? But I wasn't particularly interested in a distribution based on Arch Linux.

In late 2023, the Asahi folks presented a new distro that they called *Fedora Asahi Remix*. The promise is to combine the ground-breaking Kernel development of Asahi with the polish of Fedora Linux. I thought I would give it a go.

**tl;dr for the rest of this post: the installer feels very hackish but once installed, it's great!**

# Installation

The installer follows the infamous `curl | sudo bash` paradigm. I don't trust this sort of thing so I took a look at what is downloaded: it is mostly a launcher for the rest of the installation, which is also a collection of shell scripts. It turns out that Fedora has a graphical installer, which appears very late in the installation routine, for about two minutes, the rest happens in the terminal.

The first thing the installer does is shrink the macOS partition to make space. You choose how much to give it -- I chose a roughly 60:40 split. Aside: technically, it's not a partition but more of a pool, as APFS is similar to ZFS in its interface.

Step number two is really the special sauce of Asahi Linux: installing a UEFI environment, the U-Boot bootloader and making it bootable from the boot selector screen, including some firmware setting changes.

Unlike a PC, an ARM Mac has a specialized firmware which is made to boot macOS and nothing else. On other types of ARM machines such as the Pinebook Pro, U-Boot (the Universal Bootloader) takes care of running the OS. On the Mac, Asahi sets some firmware variables that allow this behavior as well. The Linux boot partition needs to be "blessed".

Here is where the most hackish part of the installation happens: the installer reboots into the macOS recovery system. Before it does that, it prints instructions asking you to run a series of commands inside the rescue system and accept the warnings that it prints. I don't think you can brick your machine if you do it wrong but I would not give this to someone who is not good at this computer thing.

{{< figure src="installer.jpg" title="Scary installer message" width=600 >}}

The next reboot is the first time that the machine is actually running a Linux kernel. The graphical Fedora installer appears and does a bit of configuration. That's it.

After installation, my system still boots into macOS by default. To boot Fedora, I hold the power button in the first boot phase, so that the firmware shows the boot selector screen. Then, the options are macOS, Fedora and the macOS rescue system.

# Using the system

This my first time using Fedora. The only RPM-based distro that I used in the past was SuSE, about 25 years ago. But from afar, Fedora always seemed to be fairly polished. So I was curious.

There are several flavors of desktops that can be selected in the installer: KDE, GNOME, or no desktop / do-it-yourself. The documentation says that KDE is the most polished option, so I went with KDE Plasma. Again, many years have passed since I last used KDE.

KDE still feels cluttered to me, as if its designers think what power users want is lots of buttons in lots of toolbars everywhere. But luckily, you can disable them and gain some space.

On the other hand, the UI in general (based on Wayland) looks gorgeous on the 2x Hi-DPI screen of the MacBook Air! I have struggled to configure Hi-DPI X11 desktops properly in the past (both on NetBSD and Debian), but Fedora has really nailed the setup out of the box. Kudos!

Another thing I noticed is the amount of updates. Fedora's equivalent to `apt` used to be called `yum` and is now `dnf`, though there is a symlink -- `yum update` still works. Contrary to `apt`, `dnf update` fetches package descriptions *and* updates packages.

New updates are added *all the time*. As I quipped on Mastodon the other day:

> I have not booted this Fedora system in a few days, so 750 MB of updates it is

This was literally the amount of updates I had at one point. I am not sure if this is good or bad. I guess it's not a big deal unless you are concerned about the amount of SSD writes, or the speed of your internet connection (FTTH FTW!).

## Installing more software

 * Bootstrapping [pkgsrc](/categories/pkgsrc) went fine, so that's another 35000 packages at my fingertips :)
 * I tried installing Steam but it seems like there are no Linux/aarch64 packages available. This works better on macOS with the Game Development kit doing some kind of ad-hoc x86 emulation, though I was able to make it work exactly once and it stopped working after a reboot.
 * I wanted Visual Studio Code (don't judge me!), and the graphical software catalog proposed a Flatpak version. As I quickly discovered, [the VS Code Flatpak is useless](/posts/vscode-flatpak), at least for my purposes. I suppose you can make it work with a generous sprinkling of Dev Containers, but *I don't want to.*

## Hardware support

**Almost everything works!** I cannot overstate what a big deal this is, and I expected the experience to be much less refined in that regard. The function keys do the right thing, you can control brightness and keyboard backlight, suspend/resume works perfectly, etc.

Using Fedora on this hardware feels super snappy. File system operations are shockingly fast. Compiling software seems much faster than on macOS, though I did not benchmark this. My hunch is that the process management is a bit slower in macOS, due to the Mach / XNU underpinnings. 3D graphics performance is good too.

I only found two minor caveats in my testing:

 * External displays over USB-C do not work, which means I cannot do my conference presentation from Linux. This is a known limitation.
 * The system uses more power while it is suspended. In macOS, you can close the lid and have almost no battery use during the time. On Linux, the battery is empty after a few days.
 * In general, battery life is a bit worse but still amazing. This may be due to me running more demanding workloads while on Linux. I did not do a scientific comparison.

# Conclusion

I really like it!

I might find myself using Linux as my main OS on the MacBook -- though in the last couple of days, I have been using macOS more, maybe half the time. Still, I had not expected this distro to be so good. And I had not expected that I would like it so much.

So is 2024 finally the year of Linux on the desktop? I guess for me it is.
