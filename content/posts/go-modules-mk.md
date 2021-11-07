---
title: "go-modules.mk"
date: 2021-11-07T11:27:00+01:00
categories:
 - pkgsrc
 - personal
---
The BSD build system in general, and pkgsrc in particular, have a large number
of Makefiles ending in `.mk`.

Recently, I was looking at a commit message in Gmail and noticed that these
names are linkified. At the time, I was looking at a [Go module
package](/posts/go-mod-support-3), where there is a
[`go-modules.mk`](https://go-modules.mk/) file containing details about
dependencies. This got me thinking: Why is this file name turned into a link? 

It turns out that `.mk` is the ccTLD of the Republic of North Macedonia! So I
did what I had to do: I went to the website of a registrar in Skopje and
reserved the [go-modules.mk](https://go-modules.mk/) domain.

For the DNS, I created a zone in Google Cloud DNS, which was simple and
extremely cheap, on the order of a few cents per month. For the contents, I
added the domain as a redirect to my existing blog on the Firebase console.

So there you have it: next time, you see one of those emails, clicking the file
name will bring you directly to this blog.

I have not checked if other standard Makefile names are still free as domains.
So if you are interested in putting your pages at `bsd.prog.mk` or similar, here
is your chance! :D