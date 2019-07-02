---
title: "pkgsrccon 2019: Talk Announcement"
date: 2019-07-02T19:29:04+02:00
categories:
 - pkgsrc
---
In a few weeks, on the weekend of July 13 and 14, the annual [pkgsrc]
conference, [pkgsrcCon 2019], will take place in Cambridge, UK. Whether you are
a user or developer of pkgsrc, this is a really nice place to meet the
developers and spend some time hacking together and listening to talks.

[pkgsrc]: https://pkgsrc.org/
[pkgsrcCon 2019]: http://pkgsrc.org/pkgsrcCon/2019/

My talk this year was originally supposed to be about Go module support in
pkgsrc, but that work did not get done in time. So instead, I will talk about
something entirely different:

## A Tale of Two Spellcheckers

This talk is about the obscure and intricate world of spell checkers
and how they are packaged in pkgsrc, the NetBSD package collection.

There are many general-purpose spell checkers in existence. ispell and
aspell are the most famous ones, but hunspell, originally written for
Hungarian, has become the spell checker of choice. In addition, there
are a number of specialized checkers tailored to the idiosyncrasies of
a single language, e.g. voikko (Finnish) and zemberek (Turkish).

There is a separate library, called Enchant, that aims to abstract
away the spell checker implementation from the application code.
Enchant went through a botched transition of major versions, from V1
to V2. To this day, most apps only support V1. We'll talk about
general lessons from this case.
