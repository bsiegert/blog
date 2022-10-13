---
title: "Using a Mi Band with Strava"
date: 2022-10-13T18:23:20Z
categories:
 - Basics
 - personal
---
I have used a Mi Band as a smartwatch / fitness tracking device for the last
couple years. Compared to, say, a Wear OS device, it offers a vastly better
deal:

 - It's cheap, at around 30-40 CHF.
 - The battery lasts several weeks.
 - It shows the time and tracks steps and movement.

The Wear OS device costs 5-10 times as much and needs daily charging. To be
fair, in addition to showing the time and tracking fitness data, it does many
other things that I don't need.

My old Mi Band would no longer hold a charge, so I upgraded to the Mi Band 6,
the latest iteration. I had read that it would be compatible with Strava,
which I like for tracking bike rides. Turns out it is, but only around several
corners!

The native app for the Mi Band is called *Zepp Life*, formerly known as Mi Fit.
There is a separate app called *Mi Fitness* that is completely different (but
also supports the Mi Band for some reason?). You can track workouts in Zepp Life
but they do not appear anywhere else by default, except (maybe) in Apple Health
on iOS. Zepp Life requires a Mi account.

## The trick is one more app

You need to connect your Mi account to your Strava account to make workouts sync
across the two. But Zepp Life does not support this. However, there is the
following amazing workaround:

1. Install the *Zepp* app (which apparently used to be called Amazfit).
2. Log in to Zepp with your Mi account, the same as in Zepp Life.
3. In the Zepp app, connect the Mi account to Strava by logging in to Strava
   from Zepp's "Integration" settings menu.

That's it! You can now deinstall Zepp, the connection has been established. Now,
you can start a bike ride from Zepp Life or from the band, and the following
information will appear in Strava:

* GPS trace, speed and elevation -- as usual
* Heart rate
* Cadence (I did not expect this!) 

Strava will show "Amazfit" as the data source. You can edit the workout after
the fact to add comments, description, photos etc.

## Thoughts

Fitness tracking seems like a nice thing. I like having the trace of where I
went. I like having the comparison to previous rides along the same stretch of
road that Strava provides.

>  **What I don't like is walled gardens. There are so many of them!**

Every manufacturer would like to store your fitness data in *their* cloud. Your
past data, the months or years worth of workouts, all this is a source of vendor
lock-in that tech companies are all too eager to embrace.  After all, why would
you buy a different brand (say, an Android phone when coming from iOS) if you
cannot transfer over this history?

There are all sorts of neat statistics available that only make sense if all
your fitness data is in the same walled garden and whatever device is always on
you. Look at how many kilometers you biked in the last month! You were active on
this many days! You used this many calories in your activities today! But if you
ever walk around without your phone, or without your fitness band, these
activities don't count for the stats.

This way of coupling accounts via some sort of Auth token so that data
automatically transfers over is good, but it should not be so difficult and
non-obvious to set it up. 