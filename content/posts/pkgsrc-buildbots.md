---
title: "Pkgsrc Buildbots"
date: 2019-02-04T17:15:42+01:00
categories:
 - pkgsrc
 - Cloud
 - NetBSD
---

After talking to Sijmen Mulder on IRC (thanks, TGV Wi-Fi!), I began thinking more about how you could automate the pkgsrc release engineers away.

The basic idea for a buildbot would be this:

 1. Download and unpack latest pkgsrc.tar.gz for the stable branch.
 2. Run the pullup script with the ticket number, then run whatever pullup script it outputs.
 3. Figure out the package that this concerns (perhaps from filenames).
 4. Go to the package in question, install its dependencies from binary packages.
 5. Build (`make package` is probably enough, or perhaps also install?).
 6. Upload build log to Cloud Storage.
 7. Post an email to the pullup thread with status and a link to the log.

For extra points, do this in a fresh, ephemeral VM, triggered by an incoming mail.

You would also need a buildbot supervisor that receives mails (to know that it should build something) and that launches the VM. I know that Google App Engine could do it, as it can receive emails. But maybe Cloud Functions would be the way to go?

In any case, this would be a cool project for someone, maybe myself :)

## Issues with Pull-up Ticket Tracking

This project is largely orthogonal to improvements in the pullup script. Right now, there are a number of issues with it that make it require manual intervention in many cases:

 - The tracker (`req`) doesn't do MIME, so sometimes mails are encoded with base64 or quoted-printale. This breaks parsing the commit mails.
 - Sometimes, submitters of tickets insert mail-index.netbsd.org URLs instead of copies of the message.
 - Some pullup tickets include a patch instead of, or in addition to, a list of commits. For instance, this may happen when backporting a fix to an older release instead of pulling up a bigger update.
 - Sometimes, commit messages are truncated, or there are merge conflicts. This mostly happens when there has been a revbump before the change that is to be committed -- in the majority of cases, the merge conflicts only concern `PKGREVISION` lines.

I am wondering how much we could gain, e.g. in terms of MIME support, from changing the request tracking software. admins@ uses RT, which has more features. Perhaps that could be brought to pullup tickets?