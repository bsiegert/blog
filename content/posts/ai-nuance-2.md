---
title: "Some nuanced thoughts on AI, part 2"
date: 2026-07-03T10:20:24+02:00
categories  :
  - personal
  - work
  - AI
---

It has been a bit over a year since I wrote [*Some nuanced thoughts on AI*](/posts/ai-nuance). Much of what I said then still stands.

On the other hand, the AI models have continued improving. Even more so, the "harness" running it has gone through multiple iterations. Back then, the main mode of interacting with an LLM during coding was through autocomplete. The LLM would guess what you wanted to write and propose some text. That UI paradigm is gone now.

The major game changer has been making it *agentic*. Yeah, I can hear you groan, dear reader. But the LLM can now run tools: it can build stuff. It can run tests. It can ingest test failures and use that for improvements. Most importantly, it can search documentation and can run CLI tools, e.g. for your framework.

The current UI paradigms that I see are:

- a sidebar in an IDE, typically based on VS Code. This is what Antigravity does. IMHO, this pattern is on the way out, as you need to make fewer edits to LLM generated code, and you can make them by prompting.
- a conversation view that can occasionally branch out to show some diffs, or a design doc. Choose your medium: TUI in the terminal, web app, or chatbot right in your Slack or similar.

The last one is what I am now working with (at my dayjob).

---

With the ability to run tools comes a big security problem: you don't want the LLM to take down production because it has decided that your deployment is fucked. You don't want it to be able to delete a database, or all those other things your account can do. What does *not* work is stopping every couple of seconds and asking the user to approve a command. You'll get tired of it and simply end up allowing everything. Or the LLM goes around your guardrails, e.g. by writing a Python script that does a dangerous thing.

At work, we have solved (solved-ish) the issue by giving everyone's LLM agent its own user ID, clearly marked as not-human. The harness runs in an ephemeral VM. The user and the VM are more limited in their access. Code produced by the LLM has to be reviewed by *two* humans, typically the owner of the bot and a second person. This is the modern version of the "four eyes" principle that apparently has something to do with compliance with the Sarbanes-Oxley act.

---

So how has my job changed? **If you had told me at the beginning of this year that I would be writing almost no code by hand at work, I would not have believed you.**

Anyway, I now write almost no code by hand. I still do quick edits, config changes and the like by hand -- but mostly in `vim` in the terminal. Do I mind? No. As a Staff Engineer, I have always been reviewing 10x more code than I'd been writing. So I don't mind reviewing code. Reviewing code is important.

I have been involved with packaging and patching software (in [pkgsrc](https://pkgsrc.org)) and with large scale changes at work, so only having superficial knowledge of a code base is also fine for me. It helps that my main language is Go, which (a) LLMs are very good at and (b) usually does exactly what the code says.

A positive change that I do see: "less important" coding tasks actually get done! I see quarterly goals that sat at "we won't have time to do this" get to completion in time, because someone can churn out that code "in the background", or on a calm Friday afternoon. I see tech debt actually decreasing because you can let the LLM tackle those tech debt reduction tasks.

I am still pretty uncomfortable with the AI maximalists at work that want to upend the entire development lifecycle: agents review code produced by other agents with no human involvement; agents writing tickets for other agents that autonomously design a feature. YOLOing your deployment because you "don't have time" to do it properly. That way still lies madness.

---

Do I use LLMs for coding in my spare time? Mostly no. I can ask Gemini to write me a complex SQL query, and it does a much better job than I do. I tried feeding it some hard-to-debug crash, and it came up with a plausible theory why it happens and a patch. But the patch did not solve the issue, which is a bummer.

Also, somehow, my motivation for computing in general has decreased. Should I spend the time learning Rust properly? Does it matter anymore?

I don't know if people are still genuinely enthusiastic about tech. It feels like tech is not sparking joy any more. 

And don't get me started about the prices. A Raspberry Pi 5 with 16G of RAM is 260 CHF. 260! You used to be able to buy more computer for that amount of money. The RAM price madness is ostensibly because of AI, but in reality it's more about the various people involved lining their pockets, at the expense of all of us.
