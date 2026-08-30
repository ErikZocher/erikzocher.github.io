---
layout: post
title: "What Is a GPT? A Short AI Guide for Everyone Else"
date: 2026-08-30 23:33:00 +0200
description: "My girlfriend asked what I spend my days doing with 'the computer'. This is the answer: what a GPT actually is, why AI got this good this fast, why I run my own models at home, how I pick between models (dense, MoE, quants), and what the Raspberry Pi and the little AI server each do."
tags: [ai, llm, explainer, local-llm, self-hosting, comfyui, model-selection]
categories: [technology]
---

# What Is a GPT? A Short AI Guide for Everyone Else

*2026-08-30 · 10 min read · [ai] [llm] [explainer] [local-llm] [comfyui] [puck]*

My girlfriend asked me a question I could not answer in one sentence: *what do you actually do all day with your agent?* So this post is that answer. No jargon, or at least no jargon that I do not explain. I will cover what a GPT is, why AI became this good so quickly, why I run my own models at home, how I decide which model to use, and what my Raspberry Pi and my little AI server each do.

## What a GPT actually is

GPT stands for **Generative Pre-trained Transformer**. Forget the name for a second and look at what the thing does: it predicts the next word in a sentence. That is it. That is the whole job.

Show it "The tram from Weißensee arrives in" and it will offer "five minutes" with a certain probability, "two minutes" with a smaller one, and so on. Pick one, append it, repeat. A chat conversation is just this loop, a few thousand times, fast enough that it feels like talking.

So how does word-picking get to "understanding"? Training. The model is shown a huge amount of text (the internet, books, code, conversations) and adjusts its internal settings until predicting the next word gets very, very hard to beat. To predict well, the model has to build an internal picture of how language, facts, and reasoning are connected. That picture is what we call intelligence, even though nobody programmed it in.

The "settings" are called **parameters**. A model with 27 billion parameters (27B) has 27 billion of them, each a tiny dial. More dials, more things the model can keep in mind at once. The dials live in the memory of a GPU, and that is where the hardware requirements come from.

## Why it got so good so fast

The honest answer: it did not get good suddenly, it got good on a curve, and we only recently crossed the line where "impressive demo" becomes "genuinely useful".

- **2017:** Google publishes the Transformer architecture, the design every modern model is built on. The paper, [Attention Is All You Need](https://arxiv.org/abs/1706.03762), is short enough to read in one sitting if you want to.
- **2018-2020:** Models grow from millions to 175 billion parameters. People are stunned to find abilities nobody taught them: doing arithmetic, writing code, translating. Scale, not cleverness, was the trick.
- **2022:** ChatGPT appears. Same basic idea, but the model is additionally trained on conversations and feedback, so it behaves like a helper instead of an autocomplete. This is the moment it becomes usable for normal people.
- **2023-2025:** Models learn to see (images), to handle very long conversations, and to "think" before answering by writing out their reasoning.
- **2025-2026:** The agent era. You hand the model tools (a browser, a terminal, a calendar) and it does not just talk, it *acts*. That is the difference between ChatGPT and my Puck: Puck can check the tram schedule itself, fix a config file, and publish this post.

The reason it felt like it happened overnight is that every year added a different capability, and the capabilities multiply each other. A model that can see, reason, and use tools is not three times as useful as one that only chats, it is more like a different animal.

## Local vs API: two ways to use an AI

There are two ways to use a language model.

**API (the cloud way).** You talk to a company's server through the internet. ChatGPT, Claude, DeepSeek. You pay a subscription or per word, you get the strongest models, and your messages pass through their servers on their machines.

**Local (the home way).** You download the model's weights (a file, often 5 to 100 GB) and run it on your own hardware. No subscription, no internet needed, and nothing leaves your house. The trade-off: local models are a step behind the best cloud models, and you need the right hardware.

| | Cloud API | Local at home |
|---|---|---|
| Cost | Subscription or per word | Free after the hardware |
| Privacy | Data passes through the company | Nothing leaves the house |
| Quality | Strongest models available | Very good, one step behind |
| Speed | Fast | Depends on the hardware |
| Offline | No | Yes |

I use both. The cloud is my backup brain, the local setup is the everyday one.

## My setup: a brain, a pair of hands, and a backup

Here is what is actually running in my flat, because this is the part that was confusing.

**The Raspberry Pi (the hands).** A small [single-board computer](https://www.raspberrypi.com) with 16 GB of RAM, NVMe storage, and a price like a mid-range phone. It is always on, sips about 5 watts, and runs **Puck**, my personal AI agent. Puck is the part you talk to: it remembers what we discussed, checks the M4 tram schedule, manages my todos, drafts blog posts, and drives the other machines. It is the body. The Pi alone is too weak to run a good language model, and that is fine, because it does not need to.

**The ASUS Ascent GX10 (the brain).** A little black box, roughly the size of a Mac mini, that costs about 4,000 euros. Inside is Nvidia's GB10 chip with 128 GB of unified memory, and that memory is the whole point. It is big enough to hold a 27-billion-parameter language model (mine is **Qwen 3.8 27B**, 16.5 GB after compression) plus an image model at the same time. The Pi talks to the GX10 over my home network, so Puck feels like one assistant, but the heavy thinking happens in the box under my desk.

**The cloud (the backup brain).** When the local setup is busy, down, or asked for something very long, Puck falls back to the DeepSeek API. Same interface, different server.

And yes, I have more than one model. Two big text models on the GX10 (the 27B daily driver, and a much bigger 284-billion-parameter model for special jobs), a handful of small 4 to 9 billion models on the Pi for quick tasks and looking at images, plus the image model. Here is how I think about picking them.

## A model is a file, and the numbers are compression

A language model is stored as a file of numbers, the trained dials from above. The size of that file is set by two things: how many parameters the model has, and how precisely each parameter is stored.

By default, a parameter is stored with 16 bits of precision. My 27B model would then weigh about 55 GB. In practice almost nobody runs 16-bit, because you can store the same dials with less precision and lose very little. That is what the numbers like Q4 and Q8 in model filenames mean:

| Format | Bits per parameter | My Qwen 27B file | What you lose |
|---|---|---|---|
| 16-bit (original) | 16 | ~55 GB | Nothing, it just does not fit |
| Q8 | 8 | ~28 GB | Barely anything |
| **Q4 (what I run)** | ~4.5 | **16.5 GB** | A little, mostly in fine details |
| Q3 / IQ3 | ~3 | ~12 GB | More; small models get worse fast |

Q4 is the sweet spot I run daily, the same way a good JPEG is good enough for the web. My model file is called `Qwen3.8-27B-UD-Q4_K_M.gguf`, and reading it is not that mysterious: Qwen 3.8, 27 billion parameters, 4-bit-ish, in GGUF format (the file format local models use).

## Dense vs MoE: two ways to build a big model

**Dense** models use all their parameters for every word. 27B means 27 billion dials per prediction, always. Simple, predictable, and the cost scales directly with size.

**MoE** (Mixture of Experts) models are bigger on paper but only partly active. The model contains many small expert networks, and for each word a router picks the few experts that seem relevant. My other text model, DeepSeek V4 Flash, has 284 billion parameters but activates only a fraction of them per word, so it runs at speeds you would not expect for its size. The catch is memory: even the unused experts have to sit in RAM, so a MoE model needs a lot of it and rewards you with a lot of knowledge per second of compute.

A rough rule for my hardware: dense models up to about 30B parameters are my daily territory. The 284B MoE lives on the same machine for special jobs, because 128 GB is exactly where it stops being impossible.

## How I actually pick

1. **Fit it in memory.** The model file plus the conversation history must fit in RAM with room to spare. That eliminates most of the list.
2. **Q4 as the default.** I download the Q4 variant first. If the answer quality is not enough for the task, I try Q8 before I try a bigger model.
3. **MoE if it fits.** Same memory, more knowledge, so MoE usually wins when the file fits.
4. **Test with my real tasks, not benchmarks.** I ask the model to debug a config, summarize a German rental agreement, and help with a blog post, the things it will actually do. Public leaderboards are interesting, but my work is not a leaderboard. The errors I find are the curriculum, every wrong answer teaches me something about where that model stands.

That last point is the whole game. A model generates ideas at almost no cost, but ideas without the instincts to judge them are just enthusiasm. Half of picking a model is knowing what to check in what it produces.

## How the same machine also draws: ComfyUI

The same GX10 runs [**ComfyUI**](https://www.comfyui.com), a program that generates images from text. If a language model predicts the next word, an image model predicts the next pixel. Both are just trained predictors, which is why they run on the same hardware and share the same 128 GB.

ComfyUI is not a chat box. It is a node editor, a flowchart you build from boxes connected by lines. One box loads the model, one box takes your prompt, one box sets the size and step count, one box writes the file out. You press "queue prompt" and the flowchart runs left to right.

That sounds like more trouble than a text box, and it is, until you realize what a workflow actually is: **a saved recipe.** Once I build a workflow for "1024 by 1024, 20 steps, this sampling setup", I never rebuild it. I store it, reuse it, and share it. I generated all ten pixel icons on my neon about page from one batch run of the same workflow, about a minute each.

My image model is **FLUX.1-dev**, a 17.2 GB file (in fp8, the same compression idea as Q8 but for image models). It makes a 1024 by 1024 image in about 55 seconds on the GX10, good enough quality that the 8-bit pixelation step I apply afterwards is the only thing keeping it looking like a retro sprite. I also keep an old 2018 model (Stable Diffusion 1.5, 4 GB) around, which runs 256 pixel images in 5 seconds, the fast and rough option for quick tests.

One more idea, because it explains why AI art has so many very specific styles: **LoRAs**. Training an image model from scratch takes years and a datacenter, but you can train a small add-on on top of the existing model: a few hundred images of one style or subject, trained briefly, resulting in a file of a few tens to a few hundred megabytes. You load the big model and the small add-on together, like a plugin. A LoRA for "8-bit pixel art, dark background, neon outline" is exactly the kind of add-on that would make my sprite work faster and more consistent. The base model stays general, the LoRA carries the specialty.

## Why bother

Three reasons. **Privacy:** my agent touches my calendar, my emails, my files. I would rather that conversation never leave my flat. **Cost:** an agent that runs all day makes thousands of model calls. In the cloud that adds up; at home it costs me the electricity bill, which is not impressive. **Control:** when I want a change, I make the change. No rate limits, no policy update at 3 am, no "this feature is for paid plans".

None of this is about building a ChatGPT competitor at home. It is about having a capable assistant that I understand, that I can fix, and that stays mine.

*Next in this series: [what an AI agent actually is](/technology/2026/08/30/what-is-an-ai-agent.html) (context windows, tools, and the harness that holds it all together).*
