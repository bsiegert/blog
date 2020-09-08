---
title: "pkgsrc Developer Monotony"
date: 2020-09-08T18:19:24Z
categories:
 - pkgsrc
 - personal
---
Somehow, my contributions to NetBSD and pkgsrc have become monotonous.
Because I am busy with work, family and real life, the amount of time I can
spend on open source is fairly limited, and I have two commitments that I try
to fulfill:

 1. Member of pkgsrc-releng: I process most of the pull-ups to the stable
    quarterly branch.
 2. Maintainer of Go and its infrastructure.

Unfortunately, these things are always kinda the same.

For the pull-ups, each ticket requires a verification build to see if the
package in question actually works. That time tends to be dominated by Firefox
builds, of all things: we fortunately have people that maintain the very
regular updates to LTS versions of both [firefox] and [tor-browser], but that
means regular builds of those.

As for Go, there are regular updates to the two supported branches (1.14 and
1.15 as of now), some of which are security updates. This means: change
version, sync `PLIST`, commit, revbump all Go packages. Maybe file a pull-up.

This becomes somewhat uninteresting after a while.

## What To Do

Honestly, I am not sure. Give off some of the responsibility? There is only
one person in the pkgsrc-releng team that actually does pull-ups, and they are
busy as well.


[firefox]: http://pkgsrc.se/www/firefox
[tor-browser]: http://pkgsrc.se/security/tor-browser
