---
title: "Using CPU Subsets for Building Software"
date: 2020-07-04T14:53:01Z
categories:
 - NetBSD
 - PinebookPro
---
Like many ARM CPUs, the one in the Pinebook Pro has a "big.LITTLE"
architecture, where some cores are more powerful than others:

```
[     1.000000] cpu0 at cpus0: Arm Cortex-A53 r0p4 (v8-A), id 0x0
[     1.000000] cpu1 at cpus0: Arm Cortex-A53 r0p4 (v8-A), id 0x1
[     1.000000] cpu2 at cpus0: Arm Cortex-A53 r0p4 (v8-A), id 0x2
[     1.000000] cpu3 at cpus0: Arm Cortex-A53 r0p4 (v8-A), id 0x3
[     1.000000] cpu4 at cpus0: Arm Cortex-A72 r0p2 (v8-A), id 0x100
[     1.000000] cpu5 at cpus0: Arm Cortex-A72 r0p2 (v8-A), id 0x101
```

The A72 is a more powerful than the efficiency-oriented A53, it has
out-of-order execution, plus it reaches a higher maximum clock rate (1.4&nbsp;GHz
for the A53 and 2.0&nbsp;GHz for the A72 in the Pinebook Pro).

On NetBSD-current, the kernel scheduler prefers the big cores to the little
ones. However, when building software, you may want to force the build process
onto the big cores only. One advantage is that you still have the little cores
to deal with user input and such, yet your build has the highest performance.
Also, building with all cores at the highest clock rate will quickly lead to
overheating.

NetBSD has a somewhat obscure tool named `psrset` that allows creating "sets"
of cores and running tasks on one of those sets. Let's try it:

```
$ psrset
system processor set 0: processor(s) 0 1 2 3 4 5
```

Now let's create a set that comprises cpu4 and cpu5. You will have to do that
as root for obvious reasons:

```
# psrset -c 4 5
1
# psrset
system processor set 0: processor(s) 0 1 2 3
user processor set 1: processor(s) 4 5
```

The first invocation printed "1", which is the ID of our new processor set. Now
we can run something on this set. Everything run below only sees two cores,
cpu4 and cpu5. Note the "1" in the command below. This is the ID from before.

```
# psrset -e 1 make package-install MAKE_JOBS=2
```

If you run `htop` or similar while your package is building, you will see that
only cpu4 and cpu5 are busy. If you have installed `estd` to automatically
adjust CPU clocks, you will notice that cpu4 and cpu5 are at 2&nbsp;GHz while
the four little cores are running at a cool 400&nbsp;MHz.
