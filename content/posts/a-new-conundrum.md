---
title: "A New Conundrum"
date: 2026-11-02T14:41:13+01:00
categories:
 - work
 - AI
---
I came across an interesting ethical conundrum at work recently. The following
is like a *Bac de Philo*, the famous philosophy essay for the French school
degree. You have four hours, discuss.

---

Team A maintains a proprietary domain-specific language (DSL) that is widely
used inside the company. It is used both during builds and at runtime. Its
slowness and high memory usage shows up in profiling as a bottleneck.  

Team B is probably the biggest user of this DSL. A senior engineer on team B
has vibe-coded an alternative interpreter for this DSL, from scratch, within a
few days. They have not read the code. Because this interpreter's design is
specific to one task (evaluating this DSL), it is 10x faster than the existing
interpreter.  

The engineer on team B wants to replace the old interpreter to save a ton of
time and compute resources. They have run both interpreters on all the files
*that they know of* and verified that they produce identical output. On the
other hand, they don't want to maintain this interpreter forever – that's the
job of team A. They go to team A and propose that they should take on the new
codebase and replace their existing codebase with it.

1. As the tech lead of team A, would you accept the offer? Why or why not?  
2. As the tech lead of team B (not the implementer), what would you do? Would
   you participate in the negotiations? Would you put pressure on team A? Would
   you escalate?
3. As a senior lead (say Vice President), with both teams reporting to you
   indirectly, would you intervene? If so, how?  
4. If your answer to any of the above hinges on the fact that the code was
   written by AI: would your answers change if it was not? If the engineer had
   spent the weekend in a retreat and coded the whole thing by hand? What if it
   was in Haskell?
