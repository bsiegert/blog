---
title: "The VS Code Flatpak is useless"
date: 2024-01-14T18:47:30+01:00
categories:
 - Linux
---

I installed Fedora 39 the other day. (More on that in one of the next posts.) It
has a nifty software installer thing named "Discover". When I typed "Visual
Studio Code" into the search box, it dutifully installed VS Code. As a Flatpak.

This was the first time I interacted with Flatpak, and it did not go well.

# Aside: Why VS Code

I want to use VS Code for editing Go code, with `gopls`, since it provides a
really good integration. It turns out that a majority of Go developers use VS
Code, so the language server integration is well tested and complete. In short,
it's the well-lit path.

Specifically for myself, there is a second reason: At work, we use a bespoke
web-based IDE based on VS Code, with all sorts of integrations to make
developers more productive. I have gotten very familiar with this setup, so it
makes sense for me to write open source code in this way too and benefit from
the muscle memory, so to speak.

There is a philosophical argument that you should avoid VS Code because it is
controlled by Microsoft, and it gives Microsoft a certain leverage over the open
source Go ecosystem. For what it's worth, the same argument applies to using
GitHub: Such a large percentage of open source code is developed on GitHub these
days, and Microsoft could "enshittify" it at any moment if they wanted to. But
for both of these, I *personally* think that it would be easily doable to switch
away from them to something free -- for instance, move over to Neovim's LSP
integration. In the meantime, I remain pragmatic and use what works for me.

# Why not Flatpak

Back to Fedora. I installed Go and [`gopls`](https://pkgsrc.se/devel/gopls) from
[pkgsrc](https://pkgsrc.org/), as you do. However, the Go plugin tells me that
it cannot find `gopls`, or any of the toolchain. Why!?
Issue [golang/vscode-go#263](https://github.com/golang/vscode-go/issues/263) has
a bunch of people rather confused about this failure mode.

Ultimately, it comes down to this: **Flatpaks run isolated from the host OS**,
which is fundamentally incompatible with developing code in an IDE. Despite the
allowlist for access to OS paths, there is no way to allow access to `/usr`,
`/lib`, etc. This is fundamental to the Flatpak security model. The app runs in
a container that is cordoned off from the main OS.

But an IDE for developing native code *does* need host OS access! It needs to
run a build tool, toolchain, native tools, etc. Not only does the VS Code
Flatpak not allow doing this, it also does not provide an easy way to install a
toolchain *into* the container. Never mind that I do not want a second copy of
my tools. An IDE also needs to be able to run a shell in a terminal.

At this point, I am left wondering: who can actually use this Flatpak, if native
compilation is all but impossible? Is it made for frontend devs writing only JS,
with all the tooling in JS as well?

# Fixes

I am also wondering *why* the Flatpak is the default type of installation for VS
Code on Fedora, given that Upstream has a repository of perfectly fine RPM
packages. See
https://code.visualstudio.com/docs/setup/linux#_rhel-fedora-and-centos-based-distributions.

The other container-like option would be a Snap, if you are into additional
packaging systems on your distro. You can install the Snap in "classic" mode,
where it has file system access:

```
sudo snap install --classic code
```

My choice has been the native package, and now everything is running fine.