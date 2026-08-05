---
layout: post
title: "How to Access Web Pages via MCPs: Chrome DevTools MCP, Browser Use, Hound MCP"
date: 2026-08-05 08:30:00 +0200
description: "Three MCP tools for web access, the honest truth about anti-bot systems, and a real case study: an AI agent connecting to Immobilienscout24 through CAPTCHAs, fingerprints, and one very patient login."
tags: [mcp, ai, agents, web-scraping, captcha, automation, raspberry-pi]
categories: technology
---

# How to Access Web Pages via MCPs: Chrome DevTools MCP, Browser Use, Hound MCP

When I turned my Raspberry Pi into an AI agent that does real things, the hardest problem was never the AI. It was the web. Every site I wanted my agent to use has a gatekeeper: a CAPTCHA, a bot detector, a fingerprint scanner. And every tool for letting an agent read the web has to pass through that gatekeeper first.

This post is the story of learning that the hard way. Three MCP tools for web access, what they are actually good at, and the real-world case that taught me the most: getting an agent into Immobilienscout24.

## What is an MCP anyway?

MCP (Model Context Protocol) is a standard way for AI agents to plug into tools. Instead of every agent inventing its own way to talk to a browser, an MCP server exposes a common interface. For web access, three flavors dominate:

| Tool | What it does | Best for |
|------|--------------|----------|
| **Chrome DevTools MCP** | Drives a Chrome browser via the DevTools protocol | Clicking, form filling, debugging, persistent sessions |
| **Browser Use** | Playwright-based automation with a clean Python API | Scripted browsing, scraping, multi-step flows |
| **Hound MCP** | Fetch + extract text with automatic anti-bot escalation | Reading content quickly, search, PDFs |

## The honest truth about anti-bot systems

Every serious website now runs bot detection: Imperva, Cloudflare, reCAPTCHA, PerimeterX. These systems check far more than "does this look like a browser?":

- Headless vs. headed rendering
- WebDriver flags and automation fingerprints
- IP reputation
- Cookie and session consistency
- Mouse movement and timing patterns

**An MCP cannot solve CAPTCHAs.** It can only choose which browser to present to the gatekeeper, and that choice determines whether you hit a wall. This is the sentence I wish I had read before spending an evening on it.

## Case study: Immobilienscout24 (the fun part)

I wanted my agent to contact apartment owners on Immobilienscout24. Sounds simple. It took three failed approaches and one working one.

### Attempt 1: Chrome DevTools MCP, headless

The MCP spins up a headless Chrome. Result: instant CAPTCHA. "Ich bin kein Roboter" on every page. The irony: the MCP drives exactly the kind of browser that bot detection is designed to flag.

### Attempt 2: Browser Use + browserforge fingerprint

Next I tried Playwright with a crafted Windows Chrome fingerprint, still headless. The idea: spoof enough browser traits to pass. Here is the actual fingerprint code:

```python
from browserforge.headers import HeaderGenerator
from browserforge.fingerprints import FingerprintGenerator

hg = HeaderGenerator(browser='chrome', os='windows')
headers = hg.generate()          # realistic Chrome/Windows headers

fg = FingerprintGenerator(browser='chrome', os='windows')
fp = fg.generate()               # realistic screen, UA, navigator traits
```

Result: CAPTCHA again. Fingerprint spoofing is a cat-and-mouse game, and headless rendering leaks through no matter what the headers claim.

### Attempt 3: Headed browser via Xvfb

Maybe the problem was headless rendering itself. So I ran a real headed Chromium on a virtual display (Xvfb) on the Pi. Result: still CAPTCHA. Headed-vs-headless is not the whole story. Automation traces and IP reputation matter too.

### What actually worked

A **real headed desktop Chromium** with a persistent profile, logged in manually once, then driven over CDP. No CAPTCHA. Why? Because it is indistinguishable from a human browser: real rendering, real session cookies, no automation flags from the browser's point of view.

### The solution is semi-automatic. And that is the point.

The honest architecture is a **human-in-the-loop boundary**, not full automation:

- **Manual once:** a human logs in, solves the initial CAPTCHA, and establishes the session. This is the one step that cannot be reliably automated, because that is exactly what bot protection is designed to stop.
- **Automatic forever after:** everything else (loading listings, parsing details, drafting the message, filling the form, clicking send) runs unattended over CDP.

This is not a failure of automation. It is the correct division of labor: **the human handles the irreducibly human step (proving you are human), the agent handles everything repeatable after it.** Every serious automation project hits this boundary eventually. The question is not "can I automate the whole thing?" but "where is the boundary, and how small can I make the manual side?"

For Immobilienscout, the boundary is one login, once per session. For your own projects: the manual part should be the *identity-establishing* step, and everything mechanical should live on the automated side.

## Practical recommendations

- For **reading content**: Hound MCP (or plain fetch). Fast, no browser overhead. It escalates from plain HTTP to a stealthy browser automatically when a site resists.
- For **interactive tasks** (forms, logins, clicking): Chrome DevTools MCP with a persistent profile, ideally headed.
- For **CAPTCHA-protected sites**: use a real headed browser with a real session, not a headless automation stack. Plan for a human-in-the-loop boundary from the start: one manual login, everything else automated.
- Respect **ToS and rate limits**. Contacting owners through a site's official contact form is fine; scraping everything is not.

## Closing thought

The pattern here is bigger than one apartment site. Any time you give an agent access to the real web, you are negotiating with a gatekeeper that exists to keep agents out. The winning move is not a cleverer fingerprint. It is finding the smallest honest human step that opens the gate, and automating everything on the other side of it.

My agent now checks Immobilienscout every ten minutes, reads new listings, and drafts contact messages. The only human input is the login that happens once per session. Everything else is automatic. And honestly, that boundary feels like the right shape for a lot of automation: a human at the door, an agent everywhere else.
