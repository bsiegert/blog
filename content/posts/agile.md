---
title: "Agile Development: Micromanage yourself"
date: 2022-04-10T17:13:05+02:00
categories:
 - work
---
Recently, I was thinking about one of my previous software development teams at
work. Our program manager was a former Scrum master, so he taught us the basics
of the Scrum method, which is one of the Agile development methodologies.

Now, the funny thing was that Scrum includes a number of rituals and techniques,
and the book essentially states that you must do all of them or it won't work;
despite that, we used a subset and were somewhat successful with it. We also
practiced continuous delivery by accident: because we didn't use experiments or
another way to gate new features, they became available to users as soon as they
got merged, with the next daily release.

We decided to do two-week "sprints" (iterations). At the beginning of each
iteration, there would be a sprint planning meeting where we decided on the
tasks that should be done. Each task (= ticket) was assigned a size in story
points, following the Fibonacci sequence, though without the use of cards or
other nonsense. Someone just guessed a number, basically, and the others stated
if they thought it was too low or too high.

At the end of the sprint, there was a **retrospective meeting** centered around
three basic questions:

1. What went well?
2. What did not go well?
3. What do we want to do differently in the future?

If nothing else, these retrospectives are probably the best thing, and I will do
them with any team no matter how they work. Some amount of introspection and
thinking about your own performance and that of your teammates is definitely
healthy.

## Why do any of this?

It took me some time to understand the fundamental reason for following this
model. That's because the literature presents the wrong dichotomy.

*In a project driven by software engineers, the natural state (if not following
Agile principles) is not the Waterfall. It's unstructured development.*

What I mean by that is that the Software Engineers will work on whatever comes
up, or seems important. Someone files a bug? It might just get fixed right away!
There is a spontaneous hallway conversation about feature X? Let's work on
feature X then.  This "development by Random Walk" seems convenient, but its
results are not reliable. Worse, engineers tend to lose motivation over time, in
my experience. 

In our project, we had stakeholders (well, users) that wanted to see us chip
away at the features they needed the most. In some cases, they were literally
paying for it by providing headcount to my team. So the solution is to put the
tasks with the highest priority firmly into the sprint. Then we assigned an
initial task to each person, often by them picking up the thing that they want
to work on most.

## Micromanagement?

"But wait", I hear you say, "isn't this just micromanagement?" Why yes, it is.
But crucially, you want to arrive at a point where **the individual software
engineer micromanages themselves**.

The goal is really this:

* to get everyone to agree on a set of tasks that need to be done in a unit of
  time;
* to get everyone to state what they will be working on;
* and for everyone to actually work on what they said they would be working on.

That last point is crucial.

## Non-Fungible Engineers

(Side note: I really wanted to use this heading.)

What did not work in our project was having extra, unassigned tasks for anyone
to pick up that has spare cycles. Scrum assumes that every team member can do
every task equally well; in practice, that's just not true.

Every team member has a corner of the code that they are most familiar with. For
instance, some folks may know JavaScript (and thus frontend development) better
than others. Some folks may understand the monitoring and alerting configs and
metrics better than others. Some folks are just plain uninterested in some
aspects. *Engineers are not fungible* in general, in my experience.

## Towards reliable delivery

Zooming out from the nitty-gritty of individual tasks: How do you motivate the
team to reach a quarterly or yearly goal?

A failure mode that I see often is that a team with *n* engineers has *n* goals,
one per person. At the end of the quarter, each goal is, say, 2/3 done. The team
has worked hard, but nothing has launched. The stakeholders and the team members
are frustrated.

What worked for us to get out of this hole was **swarming**: try to get as many
people as possible to work on one set of tasks related to the same goal, finish
it and launch. Celebrate the launch, congratulate folks, announce it far and
wide. Maybe even give folks some swag, a cash bonus or something. The positive
vibes increase everyone's motivation, and the next thing might go even better!

If you overpromised for whatever reason, at the end of the quarter, you might
have some things launched, one or two in flight, and some things not started at
all. That's totally fine. If stakeholders see progress *somewhere*, they are
likely not very upset that the team didn't get to the thing they wanted. It
might just come a bit later.

In our case, we decided on two small features to work on all together. Each took
about three weeks. They were the easiest things on our list, but the extra
motivation from launching these made the team work much better and taught them
how important it is to work together.