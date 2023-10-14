---
title: "Talk about the Basics"
date: 2023-10-14T21:23:20+02:00
categories:
  - Basics
  - talks
  - NetBSD
---

Whenever I send around a Call for Papers for an open source conference, some
people reply something like

> Unfortunately, I don’t have anything to present right now. My work on XYZ is
> simply not far enough along.

Or similar. However, **this does not matter.**

I’ll let you in on a secret: When I was in academia, when someone was talking
about their results at a conference, I mostly did not give a shit about the
results themselves. (Unless I was working on something narrowly related, which
was rare.)

Instead, what I was really interested in was the Intro and the Conclusion. Why
did you do this research? What is the bigger picture? What have you learned?

# What to talk about in an (open source) conference

The idea for this post came to me after [EuroBSDCon 2023], where this type of
conversation happened more than once, with different NetBSD and other
developers. (I hung out mainly with the NetBSD crowd, as expected.)

Many of the successful presentations in the conference weren't all that much
about own work, the work of developing some driver, subsystem or whatever. 
They were about *cool stuff they do with the OS.* What cool stuff can *I*, the
person watching your talk, do with this OS, or with some other OS?
What are you using your operating system, or programming language, or whatever,
for? Surely you are using it for something? So you are running some web services
behind a firewall? You could talk about that! Explain how, say, [`npf`] in
NetBSD helps you with that.

You are fiddling with your OS on a tiny single-board computer? Bring it into the
talk, connect it to your laptop and boot it live!

## Example: OccamBSD

One of the more inspiring talks that I watched at EuroBSDCon 2023 was the
excellent Michael Dexter explaining [OccamBSD]. At its core, OccamBSD is a
minimal FreeBSD -- you build the world and disable all the knobs that can be
disabled, so you get a system that's minimal in size. Why?

- as a study object, to take apart and see what you can do without
- for jails, where you don't need a mailer daemon to run your webserver

And more. I thought the idea was neat, and I am now experimenting with doing
something similar on NetBSD.

So what, I hear you object, but this was presenting the author's own, finished
development work! Well yeah. But development work is never finished. Don't be
afraid of showing things which are not quite ready for prime time. You might
find people that like the idea and want to help you.

## Example: "NetBSD, not just for Toasters"

At FOSDEM 2020, I gave a somewhat generic talk introducing NetBSD and pkgsrc.
Its title was "[NetBSD: Not just for Toasters]". To be honest, when I started
writing the talk, I was a bit worried that it would be too generic and hence
boring. 

But to my great surprise, this was my most successful FOSDEM talk ever. The hall
was packed, and I had about an hour's worth of audience questions in the hallway
outside. It turns out that people who are not from the BSD community were coming
in to this talk to find out *what cool things your can do with this*, just as I
alluded to above.

And I did have some cool things in there, mentioning various emulation and cloud
support layers, plus some fun ARM hardware that you can run NetBSD on. And
clearly, even if you are never going to try this OS, maybe you got inspired to
buy a Pinebook Pro and use an open hardware laptop?

# Conclusion

It is hard to judge from your own perspective how interesting your topic will be
for the audience. You, as the author, tend to overindex on your own work and
take the context in which your work happens totally for granted. Yet, the
audience perspective is very different. 

- The audience is looking for ideas and inspiration.
- The audience might be looking for a cause that they can join and that sounds
  fun. It doesn't have to be finished.
- Even if they will not use the exact combination of things you present, they
  are looking for an *aspect* that translates to their own world.

I hope this gives you some ideas for when you are feeling writer's block when
the next call for papers arrives.

**PS:** The Twitter button is gone from this blog.

[EuroBSDCon 2023]: https://eurobsdcon.org/
[NetBSD: Not just for Toasters]: https://docs.google.com/presentation/d/e/2PACX-1vSzUQVxxC0dwCXA3OrLQIwoMiVCJ1ys4GRVkm1JtD_hOyux6WxBrCbrWDB1YFwOcgtUsgppMtt0jjY4/pub?start=false&loop=false&delayms=60000
[OccamBSD]: https://github.com/michaeldexter/occambsd
[`npf`]: https://man.netbsd.org/npf.7