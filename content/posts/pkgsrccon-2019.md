---
title: "A Tale of Two Spellcheckers"
date: 2019-07-15T20:44:00+02:00
categories:
  - pkgsrc
  - talks
---

This is a transcript of the talk I gave at [pkgsrcCon 2019] in Cambridge, UK. It
is about spellcheckers, but there are much more general software engineering
lessons that we can learn from this case study.

The reason I got into this subject at all was my paternal leave last year, when
I finally had some more time to spend working on pkgsrc. It was a tiny item in
the enormous `TODO` file at the top of the source tree ("update enchant to
version 2.2") that made me go into this rabbit hole.

# A short history of spellchecking

## spell

The oldest spellchecker, `spell`, appeared in version 6 AT&T Unix, but it was
actually written before that, in 1975. The great Doug McIlroy (who is also the
inventor of the concept of a "pipe", by the way) worked on `spell`, added it to
UNIX and wrote a 1982 paper. Today, NetBSD still contains a version of
[spell(1)] in the base system.

To say that `spell` is not user-friendly is an understatement. You give it a
text (or troff!) file to check, and it outputs a list of all the misspelled
words on stdout. It supports both kinds of languages, British *and* American.
In American mode, it flags all British spelling as incorrect, and vice versa.
This includes verbs ending in -ize needing to be written with an -ise ending,
which is highly questionable from a linguistic point of view too.

## ispell and aspell

Next came a program called `ispell`, which stands for "interactive spell". Its
main innovation was interactive operation: it would stop when it found a
misspelled word and present you with suggestions for what you meant. You choose
the correct spelling, and `ispell` replaces it in the text. It supports
different languages, and there is a comprehensive set of dictionaries.

`aspell` (advanced spell?) set out to replace `ispell` as the standard
spellchecker. Its main distinctive feature was that its suggestions are far
better than the ones that `ispell` provides ("even better than Word 97!", its
documentation claims). It also understands encodings, including UTF-8, which is
a big deal for most languages.

Both `ispell` and `aspell` are in active use today. Their dictionary formats are
different.

## A digression on agglutinating languages

Imagine that you would like to spellcheck a text that is written in Finnish. The
problem with writing a dictionary for Finnish though is the near-infinite number
of words that it would need to contain. It is an *agglutinating language*, which
means that you can stick words together without a space. As an example, consider
the word "kasvihuoneilmiö", which is composedof the individual words for
"plant", "room" and "phenomenon" and means "greenhouse effect". Such composites
do not obey vocal harmony rules (indeed, this is one way to see where the
separation is). In addition, there are about 15 different cases for the noun,
as a number of prepositions is replaced by a case (as in, a suffix). Some cases
use the strong stem, some the weak stem of the word. Et cetera.

So what do you do if you would like to keep the dictionary as small as possible?
Easy: you take a team of linguists and have them carefully model all the rules
of word construction as a library! Such a thing exists in fact for Finnish
(voikko), for Turkish (zemberek) and for Hungarian (hunspell).

Hunspell is particularly interesting: while it does contain special word
formation rules for Hungarian, it is also an excellent spellchecker for other
languages. It can use aspell dictionaries but is faster and gives even better
suggestions, apparently. So using hunspell is a fairly popular choice among
users, no matter what language.

# Needs more abstraction

The consequence of the previous section is:

* multiple spellcheckers are in active use;
* users would like to be able to choose which one to use;
* that choice may depend on the language of the document.

So what should you do as an application developer? You need an abstraction
library over all these different programs. Such a library exists, and it is
called [Enchant].

Enchant gives you a uniform interface over all spellcheckers (including the
system one on macOS, and a few more), handles the user choice of backend and the
user dictionaries.

## The messy Enchant 2 transition

Enchant 2.0.0 was released in August 2017. Its release notes contain this
(emphasis mine):

> The major version number has been incremented owing to API/ABI changes, but
> in practice upgrading from 1.6.x should be easy.

> *Previously-deprecated APIs have been removed.*

> *The little-used enchant_broker_get/set_param calls have been removed.*

> Some trivial API changes have been made to fix otherwise-unavoidable
> compilation warnings both in libenchant and in application code. *This is
> strictly an ABI change* (although the ABI may not actually have changed,
> depending on the platform).

So there is a new major release, and it is incompatible with the previous
release for ABI and API. In a surprising development, uptake of the new version
was extremely low. For someone to adopt the new version, they have to replace
the old version with it, at which point all programs that use Enchant stop
working until you fix them. Thus, the developers have created a chicken-and-egg
problem.

They spent the next couple releases re-adding bits that had been removed and
declared that the new release was now API-compatible to 1.x, "except for some
really deprecated calls". It just so happened that many Enchant-using programs
actually used these calls, since they were more convenient than their
non-deprecated replacements!

Then, in November 2017, Enchant 2.1.3 had this in its release notes (again,
emphasis mine):

> *This release adds support for parallel installation with other major
> versions of Enchant*, and fixes a crash in the Voikko provider when it has no
> supported languages.

2.2.0 fixed parallel installation fully. You can now install Enchant versions 1
and 2 in parallel, since they go into different subdirectories and have
different pkg-config files (`enchant.pc` vs. `enchant-2.pc`).

Adoption by other software is still really low: no one checks for the
`enchant-2` package, and for an application developer, there has never been a
compelling reason to use version 2 rather than 1.

## Enchant in pkgsrc

Back to pkgsrc. I tried to make Enchant 2 the only Enchant version in our tree.

And failed.

As stated above, almost no software has explicit support for checking
`enchant-2.pc`, so I resorted to a trick to not have to patch all those
`configure` scripts. `textproc/enchant2/buildlink3.mk` has this bit:

```
# Lots of older software looks for enchant.pc instead of enchant-2.pc.
${BUILDLINK_DIR}/lib/pkgconfig/enchant.pc:
        ${MKDIR} ${BUILDLINK_DIR}/lib/pkgconfig
        cd ${BUILDLINK_DIR}/lib/pkgconfig && ${LN} -sf enchant-2.pc enchant.pc

buildlink-enchant2-cookie: ${BUILDLINK_DIR}/lib/pkgconfig/enchant.pc
```

What this does is symlink enchant-2.pc to enchant.pc *within the buildlink tree*
that is created for a single package build. We can do that because no enchant1
files are present in that tree.

But what broke the whole thing was PHP. Of course.

php-enchant supports *only* enchant1. Worse, it translates the entire API,
including those deprecated bits, to PHP. So there is no way to make it use the
newer version: if you were to remove the APIs that are no longer provided,
software using php-enchant might break, at runtime. This is not acceptable for
web applications.

So this is where I am stuck.

# General advice for library authors

In lieu of a conclusion, I would like to offer some general advice if you are
the author or maintainer of a library.

The most important is this: **An incompatible V2 of a library is like a new
product.**

Importantly, this means that if you stop maintaining V1 the moment you release
V2, it is as if you had abandoned your library and created a new one.

**Think of other projects that depend on you as customers.** Think about
migration paths. Think about the cost-benefit ratio of an upgrade by your
customers.

Consider sending pull requests to your customers! If you look at pkgsrc, Debian,
etc., it is easy to see what other projects depend on your library. Many of them
are on github. All of them probably have a way of sending patches. Send them a
patch to upgrade the dependency. Do the work for them.

**Otherwise, you are developing for no one.**


[pkgsrcCon 2019]: https://pkgsrc.org/pkgsrcCon/2019/
[spell(1)]: https://man-k.org/spell.1
[Enchant]: https://github.com/AbiWord/enchant
