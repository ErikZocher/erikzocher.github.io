---
layout: post
title: "My $4,000 AI Server Went to Sleep"
date: 2026-08-28 12:00:00 +0200
description: "A DGX Spark vanished from the network, ignored a router restart, and turned out to be asleep. What a fresh Ubuntu desktop's auto-suspend does to a headless AI server, and the one command that fixes it."
tags: [dgx-spark, gb10, self-hosting, llm, debugging, linux]
categories: [technology]
---

# My $4,000 AI Server Went to Sleep

*2026-08-28 · 5 min read · [dgx-spark] [self-hosting] [debugging]*

One command. Four target files. And a $4,000 AI server that decided to take a nap in the middle of its own setup.

## The setup

I run a local AI server: an ASUS Ascent GX10, the DGX Spark class of machine with 128 GB of unified memory. The plan is to run DeepSeek V4 Flash on it and cut my API bill to zero. The model is a 115 GB download, so my AI assistant set it up with aria2, 16 parallel connections, resumable control files, the works. We paused it twice to keep the household bandwidth free, resumed it on demand, paused it again. Normal server choreography.

## The vanishing

Then the box vanished. No ping response. No ARP entry on the Pi that talks to it all day. Nothing on port 22.

My AI assistant ran the standard disappearing-host dance: ping sweeps, ARP lookups, retries with backoff. Nothing. We set up a watcher that would auto-resume the download the moment the box answered, so at least the machine could fix itself when it came back. I went to bed expecting a morning ping.

## The WiFi restart

Next step, classic home network debugging: restart the router. The whole household loses internet for two minutes, everything reconnects, and the GX10 should rejoin with the rest. It didn't. Still no route to host. Still no ARP entry.

That is the moment I should have remembered something. A device that ignores a router restart is not having a network problem. It is not on the network at all, and it is not trying to be. But I kept assuming the WiFi was the culprit, because the box runs on WiFi, because I had not plugged in the LAN cable yet, because the setup checklist said I would do that later.

## The physical check

I walked over to the box. The screen was dark. Not powered off, the power LED was on. I wiggled the mouse.

The login screen appeared. The session had logged out, and the machine had gone to sleep.

That is the whole mystery. Fresh Ubuntu installations, including the desktop session that DGX OS ships with, default to automatic suspend after about 15 to 20 minutes of idle time. My server was idle, because the download was paused. The session logged out for whatever reason. The idle timer ran out. And a headless machine with no one at the keyboard suspended itself, silently, like it was a laptop someone closed.

A sleeping machine ignores router restarts. It has nothing to rejoin with, because it is not awake enough to know the network exists.

## The fix

One command, run over SSH once the box woke up:

```bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

That masks all four sleep targets in systemd. Suspend, hibernate, and hybrid sleep become no-ops. The system can no longer sleep, no matter what the desktop session decides to do with its idle timer. Belt and braces: GNOME Settings, Power, Automatic suspend, off.

I also disabled the auto-resume watcher's reason to exist by plugging the box into ethernet, but that is a different post.

## TL;DR: what actually happened

- The GX10's Ubuntu desktop session auto-suspended after idle time, because fresh Ubuntu enables suspend by default.
- A suspended machine does not answer ping, does not hold an ARP entry, and does not rejoin after a router restart.
- The fix is `sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target` plus turning off Automatic suspend in the desktop settings.
- Do this on day one of any headless server, before it ever matters.

## The reusable checklist

When a headless server vanishes from the network:

1. Ping it, check the ARP table, retry. Two minutes, cheap.
2. Restart the router if you must, but time-box it. If the box ignores a router restart, stop debugging the network.
3. Check the physical machine. Logged out, dark screen, power LED on? It is asleep, not broken.
4. Wake it, then disable suspend before you trust it again.

The errors are the curriculum. This one cost me an evening and taught me that a $4,000 server will happily nap through a router restart, which is exactly the kind of behavior you want to know about before you put it in front of paying users. Or before you point it at a 115 GB download and walk away.
