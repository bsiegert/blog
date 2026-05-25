---
title: "MCP bad?"
date: 2026-05-25T11:05:19+02:00
categories:
 - work
---

A while ago, I made an off-hand remark that got some followup questions. I said
that in AI applications, Model Context Protocol (MCP) is no longer a favored
extension model for tools. The question was essentially -- Really? What to use
instead?

## What is the problem with MCP?

MCP is a chatty protocol. Connecting to any MCP endpoint will hand the AI model
a full description of *all* the possible calls, including some explanatory text
and parameters.

The problem with this is that it consumes a lot of tokens. An LLM has something
akin to a working memory, called the context window. Adding several MCPs,
together with the system prompt, might consume most of the context window
already. What tends to happen then is that the LLM forgets about earlier stuff,
e.g. what's in the system prompt. Suddenly, it might start talking to the user
in a foreign language, for instance.

## Are native tools better?

Agentic development frameworks such as Google ADK let you add tools natively by
writing code. This lets you get around some of the high token usage issues.

You can also scale up the number of usable tools by making more specialized
sub-agents. The main agent can decide which sub-agent should do the task and
hand it off. The A2A protocol (agent to agent) enables this. A2A is usually a
native feature of these agent frameworks.

Fundamentally, if you want your system to be able to do a lot of different
things, writing these specialized tools for every purpose does not scale well.
At work, we have found an approach that works suprisingly well for doing
"arbitrary" things though.

## "Meta" tools to the rescue

You can create an agent that can do a large variety of tasks with a very narrow
subset of tools:

1.  Command line interfaces for all the integrations that you would like. For
    example, a CLI to query the bug tracker, one to send emails, etc.
2.  Documentation for all the required CLI tools, ideally in Markdown.
3.  An agent tool to search the corpus of documentation.
4.  An agent tool to run shell commands.

With this combination of things, LLMs are pretty capable of formulating a
search term, reading the results and calling the tool. I think this is because
shell syntax is part of their training set.

If the LLM has the capability of creating a shell or Python script and running
it, it can do actual computation in this way, e.g. for grepping over a result
or calculating "2+2" by asking the Python interpreter.

The amazing thing about points 1 and 2 is that CLIs and docs are super useful
to humans too! So by making a system logical, easy to understand and well
documented, you are making it easier for the LLM *and* for the human. Win-win.
