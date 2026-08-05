---
layout: post
title: "The Case of the Slain Gateway"
date: 2026-08-02 14:00:00 +0200
description: "A true story of a headless Raspberry Pi that went silent, told as a Sherlock Holmes mystery: two systemd units, one PID file, a ghost process, and the root of all evil."
tags: [raspberry-pi, systemd, debugging, homelab, storytelling]
categories: [technology]
---

# The Case of the Slain Gateway

*2026-08-02 · 8 min read · [raspberry-pi] [systemd] [debugging] [homelab] [storytelling]*

Everything in this story actually happened. My headless Raspberry Pi went unreachable one Sunday evening, and debugging it turned out to be a genuine whodunit. The technical TL;DR is at the bottom if you prefer facts over fog. But first, the case.

> **AI disclosure:** All illustrations in this story are AI-generated images (FLUX.1-dev), selected and edited by me. They were added later, imagining what the case would have looked like if Sherlock Holmes had worked with systemd and a soldering iron.

---

## Chapter I: The House at 192.168.178.54

![The House at 192.168.178.54: a foggy Victorian street, the telegram boy at the door](/assets/images/slain-gateway/chapter1-house.png)

It was a Sunday evening in August, and the fog had crept over the digital quarter of Berlin like a thief. In a modest house on the corner of the street called `192.168.178.54`, a machine had fallen silent.

The telegram boy had knocked twice that afternoon and received no answer. The housemaid, who attended to the doorways of the house (we called her SSH), had found the front entrance sealed as if by a spell. The master of the house, a modest but industrious fellow by the name of Gateway, was nowhere to be seen. His clock, which had ticked faithfully for one week and two days, had stopped.

I found my friend Sherlock Holmes in his study, pipe in hand, staring at a sheet of paper upon which someone had scrawled the words: `BOOT_ORDER=0xf146`.

"The game," he said without turning, "is most certainly afoot."

---

## Chapter II: The Note on the Doorstep

![The Note on the Doorstep: Holmes examines two calling cards in the hearth ashes](/assets/images/slain-gateway/chapter2-note.png)

Holmes had been called to the house by a distraught servant. The tale she told was this: the master had been unwell for weeks. He would rise each morning, light his lamps, and begin his rounds. Then, without warning, he would collapse. The servants would find him gasping the same words each time, like a man possessed:

*"Gateway already running. Gateway already running. PID 1284. PID 1284."*

"Over and over," the maid wept. "Twenty times. Thirty-five times, I counted."

Holmes raised an eyebrow. "And this PID 1284. Was it a person? A rival?"

"That's the devil of it, sir," she said. "PID 1284 was the master himself."

"A man cannot be running and not running at once," I protested.

"Can he not, Watson?" Holmes smiled thinly. "Can he not?"

He knelt by the machine's hearth and examined the ashes. There, among the cinders, lay two calling cards. One was engraved with a single word: `system.slice`. The other, smaller and older, bore the mark: `user.slice`.

"Two suitors," Holmes murmured. "And both claim the hand of the same bride."

---

## Chapter III: The Rivalry

![The Rivalry: a brass magnifying glass over two calling cards](/assets/images/slain-gateway/chapter3-rivalry.png)

Holmes produced a magnifying glass and held it over the two cards.

"Observe, Watson. The first card, `system.slice`, is clean and new. It was pressed upon the household at exactly twenty minutes past nine this evening, by a footman who answers to the name of root. It carries a curious instruction, quite modern: `--replace`. Whoever bears this card may, by its power, step into the shoes of any predecessor."

"And the second card?"

"The second card is the intruder. It is dated the twentieth of July. It was slipped into the household ledger by a quieter servant, one who answers to the name of `systemd --user`. He does not announce himself at the front door. He waits in the pantry, in a file called `~/.config/systemd/user/hermes-gateway.service`, and at every dawn he sends forth his own claimant to the same position."

"So there are two masters claiming one chair?"

"Exactly, Watson. And when two men claim one throne, one of them must die. Or rather," he tapped the paper, "one of them must be told he is already dead."

I confess I did not follow. But Holmes was already striding toward the machine's great central chamber, the place the servants called the Process Table.

---

## Chapter IV: The Ghost in the Machine

![The Ghost in the Machine: two claimants in a vast steampunk machine hall](/assets/images/slain-gateway/chapter4-ghost.png)

We found them there, the two claimants, standing at opposite ends of the room.

The first was a hale and hearty fellow, PID 5371, born at twenty past nine, son of the system itself. He carried his `--replace` like a sabre. "I am the rightful master," he declared. "I answer to root, and I answer to no other."

The second was a gaunt and older figure, PID 5886, who had slipped in through the user's pantry at eleven minutes past nine. He carried no such sabre. He carried only the memory of having been there first.

"Stand aside," said the first.

"I was here before you," said the second. "The ledger says my name. PID 1284, it says. And then you came, and the ledger could not decide which of us was real."

Holmes studied them both, then turned to me.

"The crime, Watson, is not that one man killed another. The crime is that the ledger itself was corrupted. Each claimant looked into the book, saw the other's name, and concluded he was dead. Yet both breathed. Both claimed the bride. And the bride, poor creature, could serve neither."

"Then who," I asked, "is the victim?"

"The victim," said Holmes, "is the truth. And the murderer is the twentieth of July."

---

## Chapter V: The Hound of the Machine

![The Hound of the Machine: Holmes and the brass clockwork watchdog](/assets/images/slain-gateway/chapter5-hound.png)

We descended into the basement, where a new servant had recently been hired. He was a large, loyal creature, and he answered to the name of Watchdog. His task was simple: every five minutes, he was to sniff at the master's chambers and report whether the master yet lived.

"Good fellow," said Holmes, "and what did you find?"

"I found," said the hound, "that the master was not dead, and yet not alive. I found two masters where there should be one. I barked. I sent letters by post to the far address, `zocher.erik+blog@gmail.com`, for when the telegram boy is dead, one must write letters instead. I barked until my throat was raw, and still the household would not listen."

"A faithful servant," Holmes observed, "and a clever one. He understood the first law of such households: when the telegram is silent, you must cry through other means."

"Indeed, sir," said the hound. "But I could not heal the rift. I could only name it."

"You named it well enough," said Holmes. "You said there were two. And where there are two, one may be dispensed with."

He turned to me, his eyes glittering.

"Come, Watson. We have found the ghost. Now we must find the root of all evil."

---

## Chapter VI: The Root of All Evil

![The Root of All Evil: Holmes holds the struck-through paper to the gas lamp](/assets/images/slain-gateway/chapter6-root.png)

The root of all evil was not buried deep. It lay, as such things often do, in the most ordinary of places: a single file, unremarkable, dated the twentieth of July.

Holmes held it up to the lamp. `~/.config/systemd/user/hermes-gateway.service`.

"Here, Watson, is the confession. On the twentieth of July, someone in this household installed a second doorkeeper. They meant no harm. They wished only for the master to rise each morning. But they did not know that another doorkeeper, a grander one, already stood at the front gate with the key of root and the sabre of `--replace`."

"So the second doorkeeper," I said slowly, "was not a murderer. Merely... redundant."

"Redundant is the kindest word for it. The crueler word is *conflict*. Two doorkeepers, each certain the other was an impostor. Each morning, the household would send both to the same post. Each would find the other's coat upon the peg and cry, 'Intruder!' And then the house would fall silent, for a house with two masters is a house with none."

"And the remedy?"

Holmes smiled. "The remedy is as old as Solomon. You do not divide the child. You dismiss one of the claimants."

He drew a pen and struck a single line through the servant's name.

`systemctl --user disable hermes-gateway`

"The user's doorkeeper is retired," he said. "He will not rise again. The system's doorkeeper remains, and he carries the modern key, `--replace`, which grants him the power to step over any ghost that lingers. And the hound, the faithful Watchdog, shall keep his vigil, and send letters when the telegram falls silent."

He closed his notebook and reached for his coat.

"The case is closed, Watson. The victim was not a man but a certainty. The murderer was not malice but duplication. And the ghost... the ghost was merely a master who had been told, once too often, that he was already dead."

---

## TL;DR: What Actually Happened

**The setup:** A Raspberry Pi 5 runs Hermes Agent as a systemd service (`hermes-gateway.service`), connecting to Telegram and running cron jobs. It had run stably for about a week and a half before becoming unreachable via both SSH and Telegram (a full system-level hang, likely a hard reset; no clean shutdown was recorded in the journal).

**The real culprit: dual systemd units.**

- **System unit:** `/etc/systemd/system/hermes-gateway.service` (managed by root, runs in `system.slice`, PPID 1)
- **User unit:** `~/.config/systemd/user/hermes-gateway.service` (created 20 July, managed by `systemd --user` PID 1219, runs in `user.slice`)

Both units were enabled, so every boot spawned two gateway instances competing for the same PID lock file (`~/.hermes/gateway.pid`).

**The failure cascade:**

1. The user instance started first at boot (PID 1284, `user.slice`).
2. Systemd's system instance then tried to start a second gateway, and Hermes' PID guard refused: *"Gateway already running (PID 1284)"* with exit code 1.
3. A `Restart=on-failure` (from a user-added override that contradicted the unit's `Restart=always`) caused rapid restart attempts, producing a crash-loop. The restart counter reached 35.
4. systemd eventually reported *"more than one ExecStart= setting... bad unit file setting"* because a drop-in `override.conf` had been written with a second `ExecStart=` line without resetting the first. That is a classic systemd drop-in merge footgun. The unit went `failed`.

**Fixes applied:**

- **`--replace` flag** added to the gateway's ExecStart (the official "useful for systemd" option). A fresh start now auto-replaces any stale instance instead of dying on the PID lock.
- **Override repaired:** `ExecStart=` (empty, resets the main unit's value) followed by the new `ExecStart=`. Required because systemd merges drop-ins, so a bare second `ExecStart=` would add, not replace.
- **User unit disabled** via `systemctl --user disable hermes-gateway`, removing the duplicate doorkeeper. Only the system unit starts at boot now.
- **Watchdog script** (`gateway_watchdog.sh`, cron every 5 minutes) checks that `ActiveState/SubState` is active/running and that exactly one gateway process exists. On failure it sends an email via himalaya, a fallback channel, since Telegram is dead when the gateway is. Alert spam is prevented with rate-limiting.

**Root cause in one line:** Two enabled systemd units (system + user) fought over one PID file; a broken drop-in override then locked the service in a crash-loop. Adding `--replace`, disabling the user unit, and installing a mail-capable watchdog made the whole thing self-healing.

---

*License note: the illustrations above were generated with Flux Dev, which is free for non-commercial use under the FLUX.1-dev Non-Commercial License, which this hobby blog clearly falls under. The images themselves may even be used commercially with a few caveats (attribution and AI-disclosure requirements apply). If I ever want to sell something made with it, I would need a license from Black Forest Labs or switch to the Apache-licensed FLUX.1-schnell. Your mileage may vary, so check the license before publishing anything commercial.*
