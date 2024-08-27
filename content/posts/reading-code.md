---
title: "The code is not enough"
date: 2024-08-27T11:46:46+02:00
---

In my job, I read a *lot* of code. I read more code than I write. I suppose
that's true for many engineers at the senior level or above.

For instance, a new piece of code integrates with a library, or with another
code base, and I want to understand how the integration works. Or I am the
supplier of the infrastructure/library/framework and I need to debug someone's
(mis-)use.

* Why does this not compile?
* Why does it compile but then crashes on startup with a cryptic error
  message?
* Why does it seem to work but then all requests return the same error?


## Configuration

Code is nothing without the corresponding configuration, particularly when you
are looking at builds or deployments.

For instance, the code may contain a bunch of `#ifdef` preprocessor macros.
Which of the paths is taken and which one is not depends on the settings of
the build. So to understand what's happening, you need the actual build
settings. In the case of autotools, that's `config.status` -- which also
contains a ton of noise, but you can find the list of variables easily enough.
Or you look at `config.h`, which specifically has preprocessor defines.

But that's not the only type of configuration. When a program is running, its
behavior changes based on command-line flags, or the contents of a
configuration file. Or some settings (like secrets) are passed in from an
environment variable, or looked up in some sort of secret storage.

Sometimes, command-line flags contain a whole configuration message, e.g. a
bit of JSON as the flag value. I have seen this in libraries that control
authentication requirements -- i.e. whether a handler is public, or needs a
password, or needs some kind of strong user identity. If you are investigating
why no one seems to be able to call your HTTP endpoint, that's where you need
to start.

## What and why

The point I was trying to make though is this: when reading code, the two main
questions are **what** and **why**:

1. What is the code doing?
2. Why is it doing it?

I found that you need to understand both aspects to understand some code.  As
for #1, sure you can start looking at source files in `less` but you can do
better.

I personally find working **cross-references** super useful. As an example,
you see that some code calls `AddPeer`, and you want to know what it does, so
you jump to its definition. That particular function adds an entry to a table.
But what uses this table? So you highlight that identifier and look for
usages.

Getting cross-references within a code base is not hard. For C code, you can
use `ctags`, which is decades old. Both vim and emacs support these.

The modern approach is an **LSP server** interacting with your text editor.
Neovim for example has LSP support built in, as does VS Code. There are LSP
implementations for all sorts of programming languages. For instance, for Go
code, there is the excellent
[gopls](https://pkg.go.dev/golang.org/x/tools/gopls).

The second question, the why, is best answered from the *history* of the code.
It is vastly better to read the code with history available -- e.g. looking at
a VCS checkout rather than an extracted release tarball.

When reading a bit of code, wondering why it is doing a particular thing, you
can start from the "blame" or "annotate" layer. Some folks have called the
existence of this layer a mistake -- I disagree! Here is an example from the
NetBSD kernel, an excerpt of the result of running
`cvs annotate sys/dev/pci/pcireg.h`:

```
1.1          (mycroft  09-Aug-94): /*
1.60         (jakllsch 17-Aug-09):  * Size of each function's configuration space.
1.60         (jakllsch 17-Aug-09):  */
1.60         (jakllsch 17-Aug-09):
1.124        (msaitoh  28-Mar-17): #define	PCI_CONF_SIZE		0x100
1.124        (msaitoh  28-Mar-17): #define	PCI_EXTCONF_SIZE	0x1000
1.60         (jakllsch 17-Aug-09):
1.60         (jakllsch 17-Aug-09): /*
1.1          (mycroft  09-Aug-94):  * Device identification register; contains a vendor ID and a device ID.
1.1          (mycroft  09-Aug-94):  */
1.124        (msaitoh  28-Mar-17): #define	PCI_ID_REG		0x00
1.1          (mycroft  09-Aug-94):
1.3          (cgd      18-Jun-95): typedef u_int16_t pci_vendor_id_t;
1.3          (cgd      18-Jun-95): typedef u_int16_t pci_product_id_t;
```

It shows who last touched a line and when. You see that adjacent lines might
have very different dates!

From this, you can go back to the commit that made the change, look at its log
message and the other files that are part of the same commit. If your commit
log messages are good, they tell a story. They give the context needed to
understand the "why".

## Tooling

Combining cross-references and a history panel is a great way to read and
understand code. Many IDE developers understand this and offer a view just
like this.

An exemplary UI for this approach is Google Code Search. For example, look at
[mutex.go](https://cs.opensource.google/go/go/+/master:src/sync/mutex.go;bpv=1)
from the Go standard library. All identifiers are clickable and serve as
cross-references. The "History" panel at the bottom offers all the commits
where something was changed.

A similar web UI (but open source) is OpenGrok. Look at the
[`pcireg.h` example](https://nxr.netbsd.org/xref/src/sys/dev/pci/pcireg.h?a=true)
in NXR, the NetBSD OpenGrok instance. The file history is a separate page but
that works too. What's nice about OpenGrok is clicking on a revision in this
view shows the blame layer at that version, so you can see who edited the line
*before*.

These tools really provide a lot of added value when reading and understanding
code. Use them.
