# How to Access Web Pages via MCPs: Chrome DevTools MCP, Browser Use, Hound MCP

**Draft — status: in progress**
**Quest: "The MCP Manuscript" (+50 XP)**

## TL;DR

MCPs let AI agents drive browsers, fetch pages, and extract content. But there is a huge difference between "the page loads" and "the page loads for *you*". Anti-bot systems (CAPTCHA, fingerprinting) decide who gets served real content, and MCP tools have to be chosen with that in mind. This post covers three MCP approaches, what they are good at, and a real-world case study: connecting to Immobilienscout24 from an agent.

## What is an MCP anyway?

MCP (Model Context Protocol) is a standard way for AI agents to plug into tools. For web access there are three popular flavors:

| Tool | What it does | Best for |
|------|--------------|----------|
| **Chrome DevTools MCP** | Drives a Chrome browser via the DevTools protocol | Clicking, form filling, debugging, sessions |
| **Browser Use** | Playwright-based automation with a clean API | Scripted browsing, scraping, multi-step flows |
| **Hound MCP** | Fetch + extract text with anti-bot escalation | Reading content quickly, search, PDFs |

## The honest truth about anti-bot systems

Every serious website now uses bot detection: Imperva, Cloudflare, reCAPTCHA, PerimeterX. These systems check far more than "does this look like a browser?":

- Headless vs. headed rendering
- WebDriver flags and automation fingerprints
- IP reputation
- Cookie and session consistency
- Mouse movement and timing patterns

**An MCP cannot solve CAPTCHAs.** It can only choose which browser to present, and the choice determines whether you hit a wall.

## Case study: Immobilienscout24 (the fun part)

I wanted my agent to contact apartment owners on Immobilienscout24. Sounds simple. It took three failed approaches and one working one.

### Attempt 1: Chrome DevTools MCP, headless
The MCP spins up a headless Chrome. Result: instant CAPTCHA. "Ich bin kein Roboter" on every page. The irony: the MCP drives exactly the kind of browser that bot detection flags.

### Attempt 2: Browser Use + browserforge fingerprint
Playwright with a crafted Windows Chrome fingerprint, still headless. Result: CAPTCHA again. Fingerprint spoofing is a cat-and-mouse game, and headless rendering leaks through.

### Attempt 3: Headed browser via Xvfb
A real headed Chromium on a virtual display (Xvfb). Result: still CAPTCHA. Headed-vs-headless is not the whole story: automation traces and IP reputation matter too.

### What actually worked
A **real headed desktop Chromium** with a persistent profile, logged in manually once, then driven over CDP. No CAPTCHA. Why? Because it is indistinguishable from a human browser: real rendering, real session cookies, no automation flags from the browser's point of view.

### Lessons

1. **Headless = flagged.** If a site has serious bot protection, headless browsers lose, period.
2. **Fingerprinting tools are arms races.** They work until the site updates, then they don't.
3. **Session beats stealth.** A real logged-in session in a real browser is worth more than any fingerprint trick.
4. **MCP choice matters.** Chrome DevTools MCP is great for interactive work, Browser Use for scripts, Hound for reading. But none of them bypass anti-bot; they only choose the vehicle.

## Practical recommendations

- For **reading content**: Hound MCP (or plain fetch). Fast, no browser overhead.
- For **interactive tasks** (forms, logins, clicking): Chrome DevTools MCP with a persistent profile, ideally headed.
- For **CAPTCHA-protected sites**: use a real headed browser with a real session, not a headless automation stack.
- Respect **ToS and rate limits**. Contacting owners through a site's official contact form is fine; scraping everything is not.

## Where this goes next

- The full Immobilienscout workflow (session setup, form automation, message sending) is documented in the immoscout-contact-owner skill.
- Hound MCP details: smart_search, smart_fetch, smart_crawl with stealth escalation.

---

*Draft notes: needs intro expansion, code samples for each tool, and a closing section. The Immobilienscout case study section is the core value. No em-dashes used. English only.*
