---
title: "Pinebook Pro, First Impressions"
date: 2020-05-31T16:04:25Z
tags:
 - NetBSD
 - PinebookPro
---
*Note: This post was written on the Pinebook Pro :)*

After seeing it in action at FOSDEM (from afar, as the crowd was too
large), I decided to buy a Pinebook Pro for personal use. From the
beginning, the intention was to use it for pkgsrc development, with
NetBSD as the main OS. It was finally delivered on Thursday, one day
earlier than promised, so I thought I would write down my first
impressions.

If you have never heard of the Pinebook Pro: It is a cheap, open,
hackable laptop with an ARM processor, the successor of the original
Pinebook (which I thought was too low-end to be a useful daily driver)
with generally more premium components.

As I alluded to in the first paragraph, the enthusiasm of the Free
Software community is incredibly strong! Nothing showed this better than
the incredible resonance from a tweet with a quick snapshot after the
first boot:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">It&#39;s here, a day earlier than expected! <a href="https://twitter.com/thepine64?ref_src=twsrc%5Etfw">@thepine64</a> <a href="https://twitter.com/hashtag/PineBookPro?src=hash&amp;ref_src=twsrc%5Etfw">#PineBookPro</a> <a href="https://t.co/HIMdnFtxIG">pic.twitter.com/HIMdnFtxIG</a></p>&mdash; benz (@bentsukun) <a href="https://twitter.com/bentsukun/status/1266072937821614080?ref_src=twsrc%5Etfw">May 28, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


You'll note, in passing, that on the photo, I am downloading the
NetBSD install image :)

What makes this device attractive, apart from the price, is
the ARM architecture without the baggage of the PC world. What's more,
an open and hackable system in the age of Macbooks with soldered
everything, tablets that you cannot open, "secure" boot that severely
limits what you can run on it, is something of a counterculture device.
The Pine64 folks have built a great community that embodies the true
hacker spirit.

# The Hardware

Here is where I am going to be harsh: in some ways, using this device
feels like a regression.

I was previously using a Samsung Chromebook Pro, a Pixelbook and a Pixel
Slate as laptops. Compared to these, the Pinebook Pro has 

- no HiDPI screen,
- no touchscreen,
- a barrel connector power supply,

and peripherals are mostly using USB-A port. To be fair, there is USB-C,
and it can be used to charge the machine, so I haven't used the original
charger yet.

The display resolution is 1920x1080, equivalent to about 100 dpi. While
I regret the absence of HiDPI, it is well lit and well readable. The
viewing angles are fairly large, and the colors are crisp. The default
Manjaro Linux wallpaper is a great showcase for this.

The Pinebook Pro is vaguely shaped like a MacBook Air from a few years
ago, with the same curved bottom. At 14", it is surprisingly large --
the machines mentioned above are 12 to 13". Compared to the MacBook and
Pixelbook in their quest for ever thinner devices, the Pinebook Pro
feels strangely empty. I guess there is actually free space on the
inside that you can use for upgrades and such.

The most similar laptop I have used is the HP Chromebook 14. Against
this, the Pinebook Pro holds up really well though: it is lighter, has a
better keyboard and display and is actually cheaper!

# Keyboard and Trackpad

The keyboard is an absolute joy to use. Really, it's great. The keys
have a large amount of travel, comparable to older MacBook Pros, before
they introduced the terrible keyboard. The layout (I have ANSI, i.e. US)
is exactly what you would expect. This is definitely made for typing a
lot.

On the other hand, I am not friends with that trackpad. I am hoping I
can get used to it at some point. The way it tracks small finger
movement is ... weird and counter-intuitive, and I am having a hard time
hitting small click targets. It has two mouse buttons under the bottom
left and bottom right, so clicking in the middle usually has no effect.
For dragging, you need to keep one finger in the corner on the button
and move another finger, which sometimes triggers multi-touch gestures.

# Battery life

I have not done detailed measurements, but it seems pretty good at about
7 hours. Here is the `envstat` output while writing this:

```
                               Current  CritMax  WarnMax  WarnMin  CritMin  Unit
[cwfg0]
            battery voltage:     3.935                                         V
            battery percent:        79                                      none
  battery remaining minutes:       319        0        0        0        0  none
[rktsadc0]
                        CPU:    42.778   95.000   75.000                    degC
                        GPU:    43.333   95.000   75.000                    degC
```

# Performance

Compared to all those ARM SBCs I have used (Raspberry Pi, Orange Pi,
Pine-A64), the Pinebook Pro feels really fast. Storage (at this point I
am using a memory card) is decent speed-wise, and compilations are
reasonably fast -- though my five year old Intel NUC with an i7 still
beats it by far, of course. But my workload involves compiling lots of
stuff, so this seems like a good fit.

Graphics performance has not blown me away. Animations on Manjaro
stutter a bit, Midori on NetBSD (the first browser that I tried) is
really testing my patience.

# Hackability?

I noticed that opening the bottom of the housing is screwed on with
standard Philips head screws and easy to open. There are no rubber feet
glued on top of the screws, no special tools needed.

You can easily boot your custom OS from the micro-SD card reader. As
mentioned, I bought this machine for running NetBSD on it, which works
well.

There are upgrade kits available, for example an adapter to add an NVMe
disk instead of the eMMC. For the original Pinebook, there has been an
upgrade kit with a better processor even.

Because we are on ARM, there is no "Intel Inside", and consequently,
there are also no stickers, except for one on the underside that gives
the model number.

# Conclusion

This has become longer than I intended. Despite my criticism, I really
like this machine. I am hoping I can get some good development work done
with it and use NetBSD for my daily computing tasks.

Stay tuned for another post with some NetBSD tips!



