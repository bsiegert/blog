---
title: "pkgsrc: Saving and Restoring State"
date: 2018-03-03T20:53:40+01:00
draft: true
categories:
 - pkgsrc
 - NetBSD
---

If you have not upgraded the packages in your pkgsrc installation in a while,
you might be so far behind on updates that most or all your packages are
outdated. Now what?

The easiest way to update you packages in order is to simply use
[`pkg_rolling-replace`]. Update your pkgsrc tree (either to the latest from
cvs, or to a supported quarterly release), then simply run

```shell
$ pkg_rolling-replace -uv
```

This will rebuild the required set of packages, in the right order. This takes
a while, as the rebuild is from source, and is somewhat likely to break in the
middle. When the compilation of a package fails, the tool just stops and leaves
you with an inconsistent (and in the worst case, non-working) set of packages.
Good luck fixing things. Making a backup of your `/usr/pkg` and `/var/db/pkg*`
directories *before* you start is a good idea.






[`pkg_rolling-replace`]: http://pkgsrc.se/pkgtools/pkg_rolling-replace
