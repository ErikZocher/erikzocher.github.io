---
layout: post
title: "What an AI Agent Actually Is: The Five Ideas Behind Puck"
date: 2026-08-30 23:55:00 +0200
description: "Part three of the non-technical AI guide: the five core ideas behind a working agent. Context windows, tool calling, prompting, MCP servers, and the harness (like Hermes) that glues it all together. Explained through one real conversation: checking the M4 tram."
tags: [ai, ai-agents, llm, mcp, prompting, hermes, puck]
categories: [technology]
---

# What an AI Agent Actually Is: The Five Ideas Behind Puck

*2026-08-30 · 7 min read · [ai-agents] [llm] [mcp] [prompting] [puck]*

So far: what a language model is, and how I pick one. But a model alone is just a file that finishes sentences. The thing you actually talk to, my Puck, is built from five more ideas on top of it. This post explains each one, in plain language, using a real conversation as the thread: I ask Puck "when is the next M4 tram at Buschallee?"

## 1. The context window: what the model can see right now

A language model has no memory of its own. Everything it knows about your conversation is text that is put in front of it, again, on every single answer. That bundle of text is called the **context**, and the maximum size it can hold is the **context window**, measured in words (technically tokens, and one token is roughly 3/4 of a word).

My model's window holds about 256,000 tokens. That sounds like a lot, but remember what has to fit inside: the model's own instructions, my standing preferences, the whole conversation so far, and the results of every tool call it made. A long working session eats tens of thousands of tokens, which is exactly why the window size is one of the first specs I check when picking a model.

The important consequence: **the model only ever sees what is in the window.** If something happened in a session from last week, Puck cannot remember it, unless that information was saved somewhere it can read again. That is why my setup has a memory store, a database of notes that Puck can search and load into the context when needed. Memory is not a magic feature, it is a file (or database) plus the habit of writing things down.

## 2. Tool calling: how a word machine does real things

A language model can only produce text. So how does Puck check the tram schedule, read a file, or open a browser? **Tool calling**: the model writes a request in a fixed format, and the software around it executes it.

When I ask about the M4, Puck does not guess. It writes something like: "call the tool `get_tram_departures` with the argument `stop: Buschallee, line: M4`." The software hands that request to the tool, the tool asks the BVG's public API for real data, and the answer comes back as text: "M4 towards Schönhauser Allee, 4 minutes." Puck reads that text and turns it into a normal sentence for me.

To the model, all of this is just more text in and more text out. The tools are how it touches the world. My Puck has well over a hundred of them: a terminal to run commands, a browser to open pages, a file reader, a search function, a calendar, email, and a memory database it can read and write. The list of available tools, with short descriptions of each, is part of the context window, which is one of the reasons context windows keep getting bigger: every tool costs a few hundred tokens of description, just to be known.

The model decides which tool to use. Nobody programs "if tram question then call tram tool." It reads my question, looks at the tool descriptions, and picks. That is also why the descriptions matter so much, and why a bad tool name or a vague description leads to an agent fumbling.

## 3. Prompting: the standing instructions

Every conversation with a model starts with a big text block of instructions: who the assistant is, what it may do, what style to use, what never to do. That block is called the **system prompt**, and the craft of writing it is **prompting**.

Mine tells the model its name is Puck, that I live in Berlin, that I prefer English, that design is a hobby not a profession, that it should use the tools to act instead of describing what it would do, and that it must never invent results. A system prompt is like the employee handbook you hand to a new colleague on day one. A good one is specific ("check the real schedule, never estimate"), a bad one is a wall of generalities, and the agent behaves accordingly.

Everything I have ever corrected Puck on ends up back in that prompt or in the memory store. When I told it once "the name is Zocher, with an r," that correction is now a permanent fact in its handbook, not something it has to relearn every session. Prompting is how you turn a general model into your assistant: the model supplies the raw ability, the prompt supplies the judgment about how to use it.

## 4. MCP servers: the USB-C of AI tools

Tool calling works, but until recently every program had to build its own tools, in its own way, for its own model. There was no common standard, like USB was for hardware.

**MCP (Model Context Protocol)** is that standard. An MCP server is a small program that speaks one universal language: "here is a tool called `search`, here is how you call it, here is the result." Any agent that supports MCP can plug in any MCP server, and that server immediately becomes available to every model it talks to. My Puck uses three of them: one that gives it a real browser, one that reads my Garmin watch data, and one for web search. Installing a new capability is starting one more server, not writing new code.

The pattern is worth noticing, because it repeats everywhere in this world: the model is the engine, and everything around it, the tools, the memory, the servers, the standards, is plumbing. Most of the progress you see in agent products is plumbing that finally fits together.

## 5. The harness: the cockpit around the engine

This is the idea that is easiest to miss, because it has no glamorous name. The model is only the engine. To drive it you need a cockpit, and that cockpit is what people mean by an **agent harness** (or agent framework). Mine is called [Hermes](https://hermes-agent.nousresearch.com), and here is what it does on the tram question:

1. **Builds the context.** Loads my system prompt, the tool list, and any memory notes that seem relevant, and sends them all to the model.
2. **Runs the loop.** The model answers with a tool request. The harness executes it, puts the result back into the context, and asks the model again. It keeps looping until the model gives a normal answer instead of another tool request. The tram question takes about two loops: one tool call, then the answer.
3. **Manages the window.** Watches how full the context is. When it gets close to the limit, the harness compresses the older parts of the conversation into a short summary, so the work can continue without losing the thread.
4. **Remembers.** Writes important outcomes to the memory store, so the next session starts smarter than the last one.
5. **Acts on my behalf.** The harness is what connects Puck to Telegram, to my email, to the blog. The model never sends a message by itself; the harness does, on the model's request.

Without the harness, the model is a very expensive autocomplete. With it, the model is an agent. And because the harness is ordinary, readable software, I can see and fix every step of what Puck does. When it got something wrong last month, I did not blame the model. I read the loop, found where the harness had compressed a detail away too early, and fixed that.

## Put together: the M4 tram, again

"I, when is the next M4 at Buschallee?"

The harness assembles my system prompt, the tool list, and a note that I ride the M4. The model reads all of it and decides: tool call, tram departures, Buschallee. The harness executes it against the real BVG API and returns "M4 towards Schönhauser Allee, 4 minutes." The model reads the result and answers me. The harness stores a one-line memory note about the exchange, and we move on.

Five ideas, none of them magic: a window the model can see, tools it can call, instructions that tell it how to behave, a standard for plugging in new tools, and a cockpit that runs the whole loop. That is what an AI agent is, and that is what I have been doing all day.

*This wraps the two-part series for non-technical readers. The next posts go back under the hood: the GX10's memory management, and what happens when the agent has to debug itself.*
