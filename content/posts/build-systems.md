---
title: "Build Systems: CMake and Autotools"
date: 2018-11-10T17:53:22+01:00
categories:
 - pkgsrc
---

**I think I am finally warming up to CMake**.

Eight years ago (at [FOSDEM] 2010), I gave a talk on build systems that explains
the fundamentals of automake, autoconf and libtool:

<iframe src="//www.slideshare.net/slideshow/embed_code/key/3TWxVBByTyeiE3"
width="595" height="485" frameborder="0" marginwidth="0" marginheight="0"
scrolling="no" style="border:1px solid #CCC; border-width:1px;
margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div
style="margin-bottom:5px"> <strong> <a
href="//www.slideshare.net/bsiegert/build-systems-with-autoconf-automake-and-libtool"
title="Build Systems with autoconf, automake and libtool [updated]"
target="_blank">Build Systems with autoconf, automake and libtool [updated]</a>
</strong> by <strong><a href="//www.slideshare.net/bsiegert"
target="_blank">Benny Siegert</a></strong> </div>

There is nothing in this talk that is no longer valid today as far as I can see,
though CMake was "newfangled" then and is a lot less so today. In any case, my
conclusion still stands:

- Don't try to reinvent the wheel, use a popular build system.
- You cannot write a portable build system from scratch -- so don't try.

My advice came from pent-up frustration over software that does not build on my
platform ([MirBSD], at the time) but remains true today. And to be clear:

> **Autotools is still a good choice for new code.**

## CMake

However, recent experience has made me like CMake a lot more. For one, it is
more common today, which means that packaging systems such as pkgsrc have good
support for using it. For instance, in a pkgsrc `Makefile`, configuring using
CMake is as easy as specifying

```make
USE_CMAKE=	yes
```

As the user of a package (i.e. the person who compiles it), CMake builds are
compelling because they (a) configure faster and (b) build faster.

Regarding configuring, it is infuriating (to me) how the run time of the
`configure` script in autotools totally dominates build time, as long as you run
`make -j12` or similar. CMake typically checks fewer things (I think) and does
not run giant blobs of shell, so it is faster.

For the latter, I have noticed that CMake builds typically manage to use all the
cores of the machine, while `automake`-based builds do not. I think (again, this
is speculation) that this is because automake encourages one Makefile per
directory (which are being run sequentially, not in parallel) and one directory
per target, while CMake builds all in one go.  Automake can do one Makefile for
all directories too, but support for that was added only a few years ago, and it
seems rarely used.

CMake builds also have different diagnostics (console output), optionally in
color. Some people hate the colors, and they can be garish, but I do like the
percentages that are shown for every line.

## Concrete case: icewm

When I recently packaged [wm/icewm14], I noticed that you now have the choice
of CMake or autotools, and I ended up with CMake. There were a few things to
fix but its [`CMakeLists.txt`] file is reasonably easy to edit. Note that it
contains both configuration tests and target declarations. Here is a small
example:

```
ADD_EXECUTABLE(genpref${EXEEXT} genpref.cc ${MISC_SRCS})
TARGET_LINK_LIBRARIES(genpref${EXEEXT} ${EXTRA_LIBS})

# ... other targets ...

INSTALL(TARGETS icewm${EXEEXT} icesh${EXEEXT} icewm-session${EXEEXT} icewmhint${EXEEXT} icewmbg${EXEEXT} DESTINATION ${BINDIR})
```

Compared to the same thing in automake:

```
noinst_PROGRAMS = \
	genpref

genpref_SOURCES = \
	intl.h \
	debug.h \
	sysdep.h \
	base.h \
	bindkey.h \
	themable.h \
	default.h \
	genpref.cc
genpref_LDADD = libice.la @LIBINTL@
```

So if anything, the syntax is no worse but the result is a bit better. I was
able to rummage around in `CMakeLists.txt` without reading any documentation.

[FOSDEM]: https://fosdem.org/
[MirBSD]: https://www.mirbsd.org/
[wm/icewm14]: http://pkgsrc.se/wm/icewm14
[`CMakeLists.txt`]: https://github.com/bbidulock/icewm/blob/icewm-1-4-BRANCH/src/CMakeLists.txt
