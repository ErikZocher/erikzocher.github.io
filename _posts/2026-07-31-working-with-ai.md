---
layout: post
title: "Working with AI: Five Ways, From Prompts to Agent Graphs"
date: 2026-07-31
description: "A beginner's guide to the five levels of working with AI: prompt engineering, context engineering, harness engineering, loop engineering, and graph engineering."
tags: [ai, prompt-engineering, agents, workflows, beginner]
categories: [technology]
---

# Working with AI: Five Ways, From Prompts to Agent Graphs

*2026-07-31 · 6 min read · [ai] [prompt-engineering] [agents] [workflows] [beginner]*

Most people think working with AI means typing a good prompt. That's level one. There are at least five levels of working with AI, and each one gives you more control than the last. Here they are, from the simplest to the most powerful.

Before we start: this is a moving target. The way we work with AI keeps evolving, and new approaches appear faster than anyone can write about them. Treat this list as a snapshot of where things stand now, not a complete map. The next level might already be on its way.

## Level 1: Prompt Engineering

**What it is:** the craft of asking well. The words you type into the box.

A prompt is more than a question. It carries context, constraints, examples, and a desired format. Compare these two:

> "Write a polite refusal."

> "Write a polite refusal to a client who just cancelled their contract. Three sentences. No apology for the delay, thank them for the years of collaboration."

Same intent, very different results. The second prompt tells the model who the audience is, how long the answer should be, and what to leave out.

**What it gets you:** better answers from a single request. That's it. No memory, no tools, no follow-up. One shot, one answer.

```mermaid
flowchart LR
    A[You] -->|prompt| B[LLM]
    B -->|answer| C[You]
```

## Level 2: Context Engineering

**What it is:** controlling everything the model sees before it answers. The system prompt, the instructions, the documents, the conversation history, the search results. All of these are context.

Prompt engineering is asking the right question. Context engineering is deciding what's in the room when you ask it.

There are many ways to get context in. RAG is the most famous, but it's only one:

- **RAG (retrieval-augmented generation):** the model looks up your notes, documentation, or database and answers from what it finds. Great for grounding answers in your own knowledge base.
- **IDE integration:** the model sees the actual code you're working on. Tools like Cursor, GitHub Copilot, or Claude Code run alongside your editor, so the context is the project itself, not a summary of it. You don't have to explain what your codebase looks like, the model already sees it.
- **Links and URLs:** give the model a documentation page, an API reference, or an article URL, and it reads the content before answering. Handy when the answer lives on the web and the model's training data is outdated.
- **System prompts and memory:** standing instructions plus what you've discussed before. The model remembers the rules and the history you've set up.

**What it gets you:** grounded, consistent answers that use the right source, whether that's your database, your codebase, or a webpage. This is how customer support bots stop hallucinating product details: they read the manual first.

```mermaid
flowchart LR
    A[You] -->|prompt| B[LLM]
    D[System prompt + memory] --> B
    E[Your docs via RAG] --> B
    F[IDE: your open code] --> B
    G[Linked documentation] --> B
    B -->|grounded answer| C[You]
```

## Level 3: Harness Engineering

**What it is:** giving the AI hands. Tools, APIs, and actions it can call. Instead of just answering, it can do things.

Think of the model as a brain. The harness is the body you build around it: which tools exist, what they can touch, and what the guardrails are. This is where reliability comes from. You decide the boundaries, not the model.

Examples of harness pieces:

- Web search
- Database queries
- Sending emails
- Running code
- Reading and writing files
- Calling other APIs

My own setup lives at this level. My agent ([Puck](https://erikzocher.github.io/technology/2026/07/28/meet-puck.html), yes I named it) has a terminal, a browser, file access, and messaging. It can check the BVG schedule, generate a dashboard image, push a blog post to GitHub, and send me the result on Telegram. That's a harness around a model.

**What it gets you:** AI that acts, not just talks.

```mermaid
flowchart LR
    A[You] -->|task| B[Agent]
    B -->|tool call| C[Web search]
    B -->|tool call| D[Database]
    B -->|tool call| E[Run code]
    B -->|tool call| F[Send email]
    C -->|result| B
    D -->|result| B
    E -->|result| B
    F -->|result| B
    B -->|result| G[You]
```

## Level 4: Loop Engineering

**What it is:** building feedback cycles. The AI acts, observes the result, adjusts, and repeats. Instead of a single shot, you get a loop.

A chef tastes the sauce and adjusts it. Single-shot prompting is cooking without tasting. Loop engineering is the tasting step.

The most common pattern is plan-act-observe: the AI makes a plan, takes a step, looks at what happened, and decides what to do next. It's also called ReAct (reason + act). Related patterns:

- Self-correction: the AI reviews its own output and fixes mistakes
- Retry with more info: when a tool call fails, feed the error back and try again
- Evaluation loops: a second AI checks the first one's work before it ships

**What it gets you:** much harder problems solved, because the system can course-correct instead of hoping the first try was right.

```mermaid
flowchart LR
    A[Plan] --> B[Act]
    B --> C[Observe result]
    C -->|needs adjustment| A
    C -->|goal reached| D[Done]
```

## Level 5: Graph Engineering

**What it is:** wiring many AI steps, or many agents, together like a flowchart. Different parts do different jobs and hand their results to each other.

An assembly line instead of a single worker. One step researches, the next drafts, another checks quality, and a final step publishes. Each node in the graph is a focused piece, and the connections define how work flows between them.

Examples in the wild:

- A research agent that finds sources and passes them to a writing agent
- A review agent that checks the writing agent's work against a style guide
- [n8n](https://n8n.io/)-style workflows with AI nodes in the middle
- Multi-agent systems built with tools like [LangGraph](https://langchain-ai.github.io/langgraph/)

**What it gets you:** complex, robust pipelines that split work across specialized pieces. If one step fails, you know exactly which one, and you can fix or replace it without touching the rest.

```mermaid
flowchart LR
    A[Research agent] --> B[Drafting agent]
    B --> C[Review agent]
    C -->|needs work| B
    C -->|passes| D[Publish agent]
```

## Beyond the Five Levels

Two more approaches exist outside this ladder, and they work on a different axis.

**Fine-tuning** changes the model itself instead of the interaction around it. You take a base model and train it further on your own data: your writing style, your company's support tickets, your code conventions. The result is a model that knows your stuff before you even prompt it. This is heavier than any level above. It needs data, compute, and maintenance, and it is usually the last resort when context and harness engineering are not enough.

**Evaluation engineering** is the quality control layer. Instead of asking "how do I make the AI better?", it asks "how do I know whether the AI is good?". You build a test set of known cases, run the model against it, and measure how often it gets things right. This matters more than people think. A system that is right 90% of the time and fails silently is worse than one that is right 70% of the time and flags its own uncertainty.

Both of these are their own crafts, and both keep evolving. They are worth knowing about, but they are not where a beginner should start.

## Which Level Should You Use?

| Your problem | Level |
|--------------|-------|
| Quick answer, one-off question | 1 |
| Answers must use your own data | 2 |
| The AI should take actions | 3 |
| Tasks where the first try often fails | 4 |
| A whole process with many moving parts | 5 |

Start at level 1 and add levels as the problem demands. There's no prize for using the most advanced level. The prize is solving the problem with the least complexity that works.

## A Personal Note

Everything on this blog now runs through levels 3 to 5. Puck has tools (harness), loops through steps until tasks are done (loop), and I run separate jobs for separate purposes: a weekly design job scan, a dashboard updater, a blog draft pipeline that opens pull requests for me to review (graph). I rarely type a raw prompt anymore. I engineer the context, the tools, and the loops around it.

## The Takeaway

Don't be impressed by people who "prompt well". The real craft is deciding what the AI sees, what it can touch, and how it learns from its own results. Prompts are the entrance. The levels above are where the actual work happens.

Start with prompts. Then work your way up one level at a time. Each level you add makes the AI more useful, and makes you think more like an engineer and less like a typist.
