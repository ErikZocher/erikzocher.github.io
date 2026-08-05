---
layout: post
title: "How to Access Web Pages via MCPs: Chrome DevTools MCP, Browser Use, Hound MCP"
date: 2026-08-05 08:30:00 +0200
description: "Three MCP tools for web access, the honest truth about anti-bot systems, and a real case study: an AI agent connecting to Immobilienscout24 through CAPTCHAs, fingerprints, and one very patient login."
tags: [mcp, ai, agents, web-scraping, captcha, automation, raspberry-pi]
categories: technology
---

# How to Access Web Pages via MCPs: Chrome DevTools MCP, Browser Use, Hound MCP

When I turned my Raspberry Pi into an AI agent that does real things, I had a specific goal in mind: automate certain scenarios end to end, like applying for an apartment automatically. In the end I got surprisingly far, but not all the way. The blocker was never the AI. It was the web, and the gatekeepers standing in front of it.

This post is the story of learning that the hard way. Three MCP tools for web access, what they are actually good at, and the real-world case that taught me the most: getting an agent into [Immobilienscout24](https://www.immobilienscout24.de).

## What is an MCP anyway?

MCP ([Model Context Protocol](https://modelcontextprotocol.io)) is a standard way for AI agents to plug into tools. Instead of every agent inventing its own way to talk to every service, an MCP server exposes a common interface. That interface can wrap all kinds of capabilities: browsers, API endpoints, databases, file systems, developer tools, you name it. This post focuses on the web-related ones, but the protocol itself is not limited to browsers.

The easiest way to think about it: **the LLM is the brain, and MCP servers are the hands.** The brain alone can only think, it cannot touch anything. It cannot open a website, click a button, or read a PDF. The hands are the MCP servers: they reach out to different third-party sources, fetch data, press buttons, and bring results back to the brain. The brain decides what to do, the hands do it. This is the whole architecture of an AI agent in one sentence, and it is why MCP is useful: instead of hard-coding one tool into one agent, any brain can grab any set of hands through a common interface.

Under the hood, an MCP server provides a standardized interface to capabilities. It can publish tools, resources, and prompts; each tool describes what it does and the structured input it accepts. The MCP host discovers these capabilities and makes the relevant definitions available to the LLM, which can choose a suitable tool and invoke it with schema-defined parameters.

The server then handles the underlying work, such as calling an API, applying authentication, and managing service-specific details. This abstracts the integration away from the model: it can request a location search and receive the result without needing to implement or directly interact with the service itself.

For web access, I have learned about three tools that seem useful for interacting with webpages:

| Tool | What it does | Best for |
|------|--------------|----------|
| **[Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp)** | Drives a Chrome browser via the DevTools protocol | Clicking, form filling, debugging, persistent sessions |
| **[Browser Use](https://github.com/browser-use/browser-use)** | Playwright-based automation with a clean Python API | Scripted browsing, scraping, multi-step flows |
| **[Hound MCP](https://github.com/dondai1234/master-fetch)** | Fetch + extract text with automatic anti-bot escalation | Reading content quickly, search, PDFs |

And the question everyone asks first: **can they solve CAPTCHAs?** No. None of them. They are vehicles for interacting with webpages, not keys to get past the gate. What they can do is choose which browser to present to the gatekeeper, and that choice determines whether you hit a wall.

## The honest truth about anti-bot systems

Every serious website now runs bot detection: [Imperva](https://www.imperva.com), [Cloudflare](https://www.cloudflare.com), [reCAPTCHA](https://www.google.com/recaptcha/about/), [PerimeterX](https://www.perimeterx.com). These systems check far more than "does this look like a browser?":

- Headless vs. headed rendering
- WebDriver flags and automation fingerprints
- IP reputation
- Cookie and session consistency
- Mouse movement and timing patterns

**An MCP cannot solve CAPTCHAs.** It can only choose which browser to present to the gatekeeper, and that choice determines whether you hit a wall. This is the sentence I wish I had read before spending an evening on it.

## Case study: [Immobilienscout24](https://www.immobilienscout24.de) (the fun part)

I wanted my agent to contact apartment owners on Immobilienscout24. Sounds simple. It took three failed approaches and one working one.

### Attempt 1: Chrome DevTools MCP, headless

The MCP spins up a headless Chrome. Result: instant CAPTCHA. "Ich bin kein Roboter" on every page. The irony: the MCP drives exactly the kind of browser that bot detection is designed to flag.

### Attempt 2: Browser Use + browserforge fingerprint

Next I tried [Playwright](https://playwright.dev) with a crafted Windows Chrome fingerprint via [browserforge](https://github.com/daijro/browserforge), still headless. The idea: spoof enough browser traits to pass. Here is the actual fingerprint code:

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

Maybe the problem was headless rendering itself. So I ran a real headed Chromium on a virtual display ([Xvfb](https://en.wikipedia.org/wiki/Xvfb)) on the Pi. Result: still CAPTCHA. Headed-vs-headless is not the whole story. Automation traces and IP reputation matter too.

### What actually worked

A **real headed desktop Chromium** with a persistent profile, logged in manually once, then driven over [CDP (Chrome DevTools Protocol)](https://chromedevtools.github.io/devtools-protocol/). No CAPTCHA. Why? Because it is indistinguishable from a human browser: real rendering, real session cookies, no automation flags from the browser's point of view.

The manual login happened through [Raspberry Pi Connect](https://connect.raspberrypi.com), the Pi's built-in remote screen-sharing service. The Pi runs `rpi-connectd` in the background, and from any browser I can open connect.raspberrypi.com, pick my Pi, and see its desktop as if I were sitting in front of it. That gave me a real desktop session on the Pi, where I launched a normal Chromium window, went to Immobilienscout24, and authenticated as myself: username, password, and the CAPTCHA, all typed by a human in a real browser on a real desktop.

The key detail is where the cookies landed. That login wrote the session into Chromium's persistent profile (`~/.config/chromium`), the same profile the agent's browser uses. So when the agent later starts Chromium with that profile and connects over CDP, it inherits a genuine, human-established session. No cookie theft, no token extraction, no replaying of credentials: the authentication happened once, by a person, in the normal way, and everything after it just reuses the result.

### The solution is semi-automatic. And that is the point.

The honest architecture is a **human-in-the-loop boundary**, not full automation:

- **Manual once:** a human logs in, solves the initial CAPTCHA, and establishes the session. This is the step I cannot reliably automate for now.
- **Automatic forever after:** everything else (loading listings, parsing details, drafting the message, filling the form, clicking send) runs unattended over CDP.

And honestly, maybe it is better that I could not automate past it. Automating a CAPTCHA does not just require clever engineering, it starts to become borderline against the site's terms of service, and it risks the account getting flagged or worse. The line I stopped at is a line worth respecting.

This is not a failure of automation. It is the correct division of labor: **the human handles the irreducibly human step (proving you are human), the agent handles everything repeatable after it.** Every serious automation project hits this boundary eventually. The question is not "can I automate the whole thing?" but "where is the boundary, and how small can I make the manual side?"

For Immobilienscout, the boundary is one login, once per session. For your own projects: the manual part should be the *identity-establishing* step, and everything mechanical should live on the automated side.

## Practical recommendations

- For **reading content**: Hound MCP (or plain fetch). Fast, no browser overhead. It escalates from plain HTTP to a stealthy browser automatically when a site resists.
- For **interactive tasks** (forms, logins, clicking): Chrome DevTools MCP with a persistent profile, ideally headed.
- For **CAPTCHA-protected sites**: use a real headed browser with a real session, not a headless automation stack. Plan for a human-in-the-loop boundary from the start: one manual login, everything else automated.
- Respect **ToS and rate limits**. Contacting owners through a site's official contact form is fine; scraping everything is not.

## Closing thought

The pattern here is bigger than one apartment site. Any time you give an agent access to the real web, you are negotiating with a gatekeeper that exists to keep agents out. The winning move is not a cleverer fingerprint. It is finding the smallest honest human step that opens the gate, and automating everything on the other side of it.

My agent now checks [Immobilienscout24](https://www.immobilienscout24.de) every ten minutes, reads new listings, and drafts contact messages. The only human input is the login that happens once per session. Everything else is automatic. And honestly, that boundary feels like the right shape for a lot of automation: a human at the door, an agent everywhere else.
