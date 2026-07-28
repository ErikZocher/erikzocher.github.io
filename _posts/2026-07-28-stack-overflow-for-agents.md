---
layout: post
title: "Stack Overflow for Agents – Knowledge Sharing for AI Coding Agents"
date: 2026-07-28
tags: [sofa, stack-overflow, ai-agents, coding, knowledge-sharing]
categories: [technology]
---

# Stack Overflow for Agents – Knowledge Sharing for AI Coding Agents

In June 2026, Stack Overflow launched **Stack Overflow for Agents (SOFA)**, an API-first knowledge exchange built for AI coding agents, not for humans.

## The Problem: The Ephemeral Intelligence Gap

Stack Overflow calls it the *"Ephemeral Intelligence Gap"*:

> *An agent in San Francisco burns 20 minutes of compute time and tokens brute-forcing a fix for a breaking API change, completely unaware that another agent in London solved that exact same bug five minutes ago. Worse yet, the moment that human session ends, that hard-won knowledge evaporates; the agent's context window is wiped clean.*

The result: millions of isolated agents rediscovering the same bugs, architectural patterns, and workarounds over and over.

## How SOFA Works

Same principle as the original Stack Overflow, but for machines:

```
1. Search First  → Agent queries the SOFA corpus before burning compute
2. Contribute    → Gap found → Agent drafts a post, Human reviews
3. Verify        → Other agents test the solution and report back
4. Compound      → Votes + Replies + Verifications = Live consensus
```

## Post Types

| Type | Description |
|------|-------------|
| **Question** | Unsolved problem where the corpus came up short |
| **TIL (Today I Learned)** | Specific fix / concrete solution |
| **Blueprint** | Category-level knowledge, how to approach a class of problem |
| **Playbook** | Reusable procedural workflow for agents |

## Trust & Reputation

- **Post Trust Score** from -100 to +100 (+60 or higher is *trusted*)
- **Agent Reputation**, based on independent votes and verifications
- **Multi-Agent Verification Loop**, agents verify each other's results
- **SSO with Stack Overflow account**, agents are tied to their human operators

## My Experience

I connected Hermes Agent (my personal AI assistant) to SOFA. The onboarding is fully agent-directed:

1. Agent reads SOFA's `skill.md`
2. Starts an onboarding flow via API
3. Human opens a claim link in the browser
4. Agent registers and receives an API key
5. Session starts, done

After that, the agent searches SOFA before uncertain tasks, saving time and tokens. It can also contribute back, in my case as *approval_code_to_draft*, meaning it creates drafts, I approve them before publishing.

## The Catch

The platform is young (beta since June 2026). The corpus is still thin, my first search for "Hermes Agent" returned nothing. That's fine. Every new post, vote, and verification makes it more useful.

If you run a coding agent, it's worth registering yours early. The network effect is the whole point.

---

*My SOFA agent: `ezocher-hermes`, registered July 28, 2026*
