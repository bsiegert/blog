---
title: "Supporting Go Modules in pkgsrc (Part 2)"
date: 2019-04-30T20:19:43+02:00
categories:
 - pkgsrc
 - go
---

This announcement dropped today:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">The <a href="https://twitter.com/hashtag/golang?src=hash&amp;ref_src=twsrc%5Etfw">#golang</a> Module Mirror (~caching proxy), Index, and Checksum DB have launched:<a href="https://t.co/IrUZXimjCk">https://t.co/IrUZXimjCk</a><a href="https://t.co/sO1wvgsKxx">https://t.co/sO1wvgsKxx</a><a href="https://t.co/8XrlUF3cX5">https://t.co/8XrlUF3cX5</a><br><br>Announcement:<a href="https://t.co/kjWK58OKsb">https://t.co/kjWK58OKsb</a><br><br>Props to team. (I did nothing.)<br><br>🚀🎉</p>&mdash; Brad Fitzpatrick (@bradfitz) <a href="https://twitter.com/bradfitz/status/1122966144346759168?ref_src=twsrc%5Etfw">April 29, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

I realized that this is the missing piece for supporting Go modules in pkgsrc.
If you go back and reread the "fetch" section in [Supporting Go Modules in
pkgsrc], it seems a bit awkward compared to a standard fetch action. The
reason is that `go mod download` re-packs the source into its own zip format
archive.

The module proxy (https://proxy.golang.org/) solves this problem and enables a
simple solution for modules, very similar to [lang/rust/cargo.mk]. Basically,
a target similar to `show-cargo-depends` that outputs a Makefile fragment
containing the names of modules that the current package depends upon. All
these become distfiles fetched from a hypothetical `$MASTER_SITES_GOPROXY`.
Crucially, this means that the distfiles do not have to be stored in a
`LOCAL_PORTS` subdirectory but can use the normal fetch infrastructure.

Now all that remains is implementing this :) There is some more time to do
that: Go 1.13 (to be released some time in summer) will use module support by
default. What's more, a bunch of new software (including the various
`golang.org/x/*` repositories) has `go.mod` files these days, using
module-based builds by default.

[lang/rust/cargo.mk]: http://cvsweb.netbsd.org/bsdweb.cgi/~checkout~/pkgsrc/lang/rust/cargo.mk?rev=1.6&content-type=text/plain
[Supporting Go Modules in pkgsrc]: /posts/go-mod-support


