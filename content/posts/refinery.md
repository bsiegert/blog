---
title: "The Refinery, an Analogy for Distributed Systems"
date: 2021-03-04T14:06:35Z
categories:
 - cloud
 - personal
---
Back when I was in [Engineering school](https://ecpm.unistra.fr/), my
first-year internship happened in a refinery. In retrospect, this turned out
to be extremely relevant for my current job in tech.

The subject of my internship was the optimization of an existing process. The
unit had been planned on paper by an engineer on another continent, installed
according to specs, and it turned out to be ... not working so well.

# Don't believe Tech is unique

A refinery is a distributed system. There are specs and basically internal
contracts on each sub-unit regarding the quantity it should process per day,
what the requirements for inputs and the desired output characteristics are.
Instead of queries, the inputs and outputs are, you know, oil and gas.

There are continuous and batch processes. Just like in tech, the interface
between these is the subject of a lot of literature and ops knowledge.

In tech, services have availability and latency SLOs.
In a refinery, there are input and output SLOs (plus specs like purity, sulfur
content, water content, etc.).

In tech, there are error budgets. In a refinery, you have *emission budgets*
as a limiting factor. You may only send *x* amount of NOx or SOx or CO2 into
the air over the course of the day. You may only go over the target value for
*n* hours per month, otherwise the company pays a fine. The water that leaves
the grounds may only be so-and-so polluted and have at most *y* degrees of
temperature, otherwise there is another fine. And so on.

And just like in tech, contractors do the darndest things, although in tech,
you rarely get a truckload of methanol dumped into your waste water stream.

# Safety and Postmortem culture

In a tech company, we might say that prod is on fire when there is an
incident, but in a refinery, things might *literally* be on fire or explode.

I quickly learned to be distrustful of recently renovated spaces, because
that means it may have been blown up recently. My intern office was recently
renovated; at some point, they showed me the photos of the incident that had
all but destroyed the building the previous year.

The oil and gas industry has had a strong safety culture for years. The
postmortem culture that SRE is so proud of had been a thing in the industry
for years. Thus, when there was a large fire in a refinery in Texas City (the
refinery was run by a competitor!), we got hold of a copy of the postmortem
analysis, so that we could learn from it and avoid making the same mistakes.
There was a meeting where we went through it together, discussed and
acknowledged various lessons.

I am not sure how blameless these postmortems are. The problem is that in a
real incident, people can get hurt or killed. At some point, it involves
responsibility in a legal sense. The director of a unit may be responsible for
incidents resulting in damages to others, including in a criminal court.
Fortunately (?) for tech, incidents usually do not have this kind of
real-world impact.

# Dev/Ops Divide

Maybe even more than in tech, there is a divide between "dev" (as in, process
engineers and planners) and "ops" (actual operator crews who are literally
opening and closing giant valves with their two hands).

As a budding chemical engineer, I interned in process engineering. I quickly
found out that many process engineers don't actually bother finding out how
the units they planned perform day to day. On the other hand, the most helpful
thing I could do was to sit with the ops in the control room, drink a lot of
coffee, and listen to their mental models of the unit. They could perfectly
explain it in terms of "when I open this valve too much, the temperature over
here jumps up and I have to open this other valve to compensate". But they
cannot tell you why. The trick of the good engineer is to take what you know
about physics, thermodynamics, etc. and translate this into an understanding
of what actually happens.

Often, what actually happens is not what the planning says. In all cases,
there are variables that you did not take into account (outside temperature
anyone?).

In addition, some real-world boundary conditions are hard to express in
correlation matrices and linear programming models. For example: "If you open
this valve too much for too long, the bit in the middle will clog with
corrosive white slime deposits". What kind of coefficient is that?

# Your Monitoring is lying

Just like in tech, your monitoring is lying to you! You could have a
miscalibrated sensor, but what is more likely is that the sensor is placed in
a way that it does not show you the true picture.

In our case, there was a laser measurement in a gas conduit where the laser
sometimes passed through the stream of flowing gas and sometimes it didn't.
The wildly fluctuating output has no basis in the actual gas composition.
In another part of the unit, a sensor was placed in such a way that it was
disturbing the flow, so it was actually modifying the behavior of the system.

My two most important variables (the composition of the feed stock and the
composition of ammonia) were not even available as measurements. In a refinery
power plant, you burn whatever is left over at that moment. For gas, that may
be pure hydrogen, pure butane or anything in between. And don't get me started
on the disgusting sludge they call heavy gas-oil.

As for the concentration of ammonia in the ammonia feed (yes, really), I asked
for a manual measurement so I could have at least one data point. They told me
it was impossible. I asked why. The answer was "when we open the storage drum,
it catches fire. The contents are pyrophoric." Yay.

# Dependencies

The recommendation at the end of my internship was to switch off the unit and
to tear it down. My manager agreed but it turned out to be impossible because
other units had come to depend on this process to consume *their* input or
output feeds.

So for all I know, the piece of crap unit I tried to optimize in the early
2000s is still there, making the surrounding air just a tiny bit dirtier.

# Conclusion

Do not believe that tech is different from other, existing disciplines of
engineering. There are other industries that have worked on distributed
systems. Many software engineers I know are interested in the aerospace
industry for a similar reason, which usually means binging on plane crash
documentaries. But throughout many different industries, you can find
solutions to many of the same issues that you have with your distributed cloud
microservice mesh, or whatever.
