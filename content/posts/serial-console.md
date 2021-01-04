---
title: "Serial Console Basics"
date: 2021-01-01T21:33:49+01:00
draft: true
---
In the last few weeks, my son and I have done some basic experiments with
simple electronic circuits on GPIO ports. This led me to finally getting
the hang of those UARTs for serial consoles on ARM SBCs (single board
computers).

This post is meant to collect some very basic things that I have learned.
If you consider yourself a "maker", you probably think this is all trivial
:)

## Raspberry Pi GPIO

[**Here**](https://www.raspberrypi.org/documentation/usage/gpio/) is a
picture of the pinout of the 40-pin extension connector in the Raspberry
Pi. It has become somewhat of a standard, with many other SBCs adapting the
same pinout. (Unfortunately, the first RPi model used a different naming
scheme, which persists in some GPIO drivers, such as the one in the
[9front](https://9front.org/) operating system.)