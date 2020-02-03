---
title: "How to do Pull-ups to pkgsrc-stable"
date: 2020-02-03T11:30:58+01:00
categories:
 - pkgsrc
---
I am part of the pkgsrc releng (release engineering) team. My main task there is
handling pull-ups into the stable branch.

pkgsrc creates a stable branch every three months and names it after the
respective quarter -- for example, the last branch was called 2019Q4. Pull-ups
are tickets to "pull up" one or more commit from the development branch into the
stable branch. Typical justifications for pull-ups are:

 - security updates
 - build fixes
 - important bug fixes (such as when the package crashes on startup)

In addition, sometimes we pull up updates to packages if they are "leaves" and
stop working without regular updates. Some web scrapers, for example, need
regular updates to keep up with changes in the sites they scrape.

Pull-up tickets can only be sent by pkgsrc developers, to a special mail alias.
Unfortunately, the mails come in all kinds of formats, which makes things harder
for me and the others on the team.

There is a Python script, in an internal repository, that we use for pull-ups to
pkgsrc.

The ideal input is just the commit messages, concatenated, up to and
including the blurb about diffs not being public domain. Links to
https://mail-index.netbsd.org/ are okay too, but I have to copy/paste the
corresponding messages manually. Patches typically mean more manual work
because they typically do not contain an appropriate commit message.

When there have been intermediate commits, most of the time they need
to be pulled up too. For example, if the stable branch is at version
1.0 and you want version 1.2 pulled up, you typically  need to add
the 1.1 update commit, for whatever patch or PLIST changes.

If the intermediate commit is a revbump touching a million packages,
it is probably better to leave that out. `PKGREVISION` merge conflicts are
almost trivial to fix.

Finally, if you are interested, there is a public web interface tracking the
status of pkgsrc pull-ups at http://releng.netbsd.org/cgi-bin/req-pkgsrc.cgi.
